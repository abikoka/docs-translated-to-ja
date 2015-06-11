

Home > Documentation > The Cookbook > Cache > How to Use Varnish to Speed up my Website 

## How to Use Varnish to Speed up my Website

```
2.3 version
English
edit this page
```

Because Symfony's cache uses the standard HTTP cache headers, the Symfony Reverse Proxy can easily be replaced with any other reverse proxy. Varnish is a powerful, open-source, HTTP accelerator capable of serving cached content fast and including support for Edge Side Includes.  
Symfonyのcacheは標準的なHTTP cache headersを使うために、SymfonyのリバースProxyはあらゆる他のリバースproxyを置き換えやすい。Varnishは強力で、


### Make Symfony Trust the Reverse Proxy¶

Varnish automatically forwards the IP as X-Forwarded-For and leaves the X-Forwarded-Proto header in the request. If you do not configure Varnish as trusted proxy, Symfony will see all requests as coming through insecure HTTP connections from the Varnish host instead of the real client.

Remember to configure framework.trusted_proxies in the Symfony configuration so that Varnish is seen as a trusted proxy and the X-Forwarded headers are used.
Routing and X-FORWARDED Headers¶

If the X-Forwarded-Port header is not set correctly, Symfony will append the port where the PHP application is running when generating absolute URLs, e.g. http://example.com:8080/my/path. To ensure that the Symfony router generates URLs correctly with Varnish, add the correct port number in the X-Forwarded-Port header. This port depends on your setup.

Suppose that external connections come in on the default HTTP port 80. For HTTPS connections, there is another proxy (as Varnish does not do HTTPS itself) on the default HTTPS port 443 that handles the SSL termination and forwards the requests as HTTP requests to Varnish with a X-Forwarded-Proto header. In this case, add the following to your Varnish configuration:


```
sub vcl_recv {
    if (req.http.X-Forwarded-Proto == "https" ) {
        set req.http.X-Forwarded-Port = "443";
    } else {
        set req.http.X-Forwarded-Port = "80";
    }
}
```

Cookies and Caching¶

By default, a sane caching proxy does not cache anything when a request is sent with cookies or a basic authentication header. This is because the content of the page is supposed to depend on the cookie value or authentication header.

If you know for sure that the backend never uses sessions or basic authentication, have Varnish remove the corresponding header from requests to prevent clients from bypassing the cache. In practice, you will need sessions at least for some parts of the site, e.g. when using forms with CSRF Protection. In this situation, make sure to only start a session when actually needed and clear the session when it is no longer needed. Alternatively, you can look into Caching Pages that Contain CSRF Protected Forms.

Cookies created in JavaScript and used only in the frontend, e.g. when using Google Analytics, are nonetheless sent to the server. These cookies are not relevant for the backend and should not affect the caching decision. Configure your Varnish cache to clean the cookies header. You want to keep the session cookie, if there is one, and get rid of all other cookies so that pages are cached if there is no active session. Unless you changed the default configuration of PHP, your session cookie has the name PHPSESSID:

 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15

	

sub vcl_recv {
    // Remove all cookies except the session ID.
    if (req.http.Cookie) {
        set req.http.Cookie = ";" + req.http.Cookie;
        set req.http.Cookie = regsuball(req.http.Cookie, "; +", ";");
        set req.http.Cookie = regsuball(req.http.Cookie, ";(PHPSESSID)=", "; \1=");
        set req.http.Cookie = regsuball(req.http.Cookie, ";[^ ][^;]*", "");
        set req.http.Cookie = regsuball(req.http.Cookie, "^[; ]+|[; ]+$", "");

        if (req.http.Cookie == "") {
            // If there are no more cookies, remove the header to get page cached.
            remove req.http.Cookie;
        }
    }
}

If content is not different for every user, but depends on the roles of a user, a solution is to separate the cache per group. This pattern is implemented and explained by the FOSHttpCacheBundle under the name User Context.
Ensure Consistent Caching Behavior¶

Varnish uses the cache headers sent by your application to determine how to cache content. However, versions prior to Varnish 4 did not respect Cache-Control: no-cache, no-store and private. To ensure consistent behavior, use the following configuration if you are still using Varnish 3:

    Varnish 3

     1
     2
     3
     4
     5
     6
     7
     8
     9
    10
    11

    	

    sub vcl_fetch {
        /* By default, Varnish3 ignores Cache-Control: no-cache and private
           https://www.varnish-cache.org/docs/3.0/tutorial/increasing_your_hitrate.html#cache-control
         */
        if (beresp.http.Cache-Control ~ "private" ||
            beresp.http.Cache-Control ~ "no-cache" ||
            beresp.http.Cache-Control ~ "no-store"
        ) {
            return (hit_for_pass);
        }
    }

You can see the default behavior of Varnish in the form of a VCL file: default.vcl for Varnish 3, builtin.vcl for Varnish 4.
Enable Edge Side Includes (ESI)¶

As explained in the Edge Side Includes section, Symfony detects whether it talks to a reverse proxy that understands ESI or not. When you use the Symfony reverse proxy, you don't need to do anything. But to make Varnish instead of Symfony resolve the ESI tags, you need some configuration in Varnish. Symfony uses the Surrogate-Capability header from the Edge Architecture described by Akamai.

Varnish only supports the src attribute for ESI tags (onerror and alt attributes are ignored).

First, configure Varnish so that it advertises its ESI support by adding a Surrogate-Capability header to requests forwarded to the backend application:

1
2
3
4

	

sub vcl_recv {
    // Add a Surrogate-Capability header to announce ESI support.
    set req.http.Surrogate-Capability = "abc=ESI/1.0";
}

The abc part of the header isn't important unless you have multiple "surrogates" that need to advertise their capabilities. See Surrogate-Capability Header for details.

Then, optimize Varnish so that it only parses the response contents when there is at least one ESI tag by checking the Surrogate-Control header that Symfony adds automatically:

    Varnish 4

    1
    2
    3
    4
    5
    6
    7

    	

    sub vcl_backend_response {
        // Check for ESI acknowledgement and remove Surrogate-Control header
        if (beresp.http.Surrogate-Control ~ "ESI/1.0") {
            unset beresp.http.Surrogate-Control;
            set beresp.do_esi = true;
        }
    }

    Varnish 3

If you followed the advice about ensuring a consistent caching behavior, those vcl functions already exist. Just append the code to the end of the function, they won't interfere with each other.
Cache Invalidation¶

If you want to cache content that changes frequently and still serve the most recent version to users, you need to invalidate that content. While cache invalidation allows you to purge content from your proxy before it has expired, it adds complexity to your caching setup.

The open source FOSHttpCacheBundle takes the pain out of cache invalidation by helping you to organize your caching and invalidation setup.

The documentation of the FOSHttpCacheBundle explains how to configure Varnish and other reverse proxies for cache invalidation.

This work is licensed under a Creative Commons Attribution-Share Alike 3.0 Unported License .
