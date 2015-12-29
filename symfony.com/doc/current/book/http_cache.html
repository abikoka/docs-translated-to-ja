## Symfony Reverse Proxy

Symfony comes with a reverse proxy (also called a gateway cache) written in PHP. Enable it and cacheable responses from your application will start to be cached right away. Installing it is just as easy. Each new Symfony application comes with a pre-configured caching kernel (AppCache) that wraps the default one (AppKernel). The caching Kernel is the reverse proxy.

To enable caching, modify the code of a front controller to use the caching kernel:

```
// web/app.php
use Symfony\Component\HttpFoundation\Request;

// ...
$kernel = new AppKernel('prod', false);
$kernel->loadClassCache();
// wrap the default AppKernel with the AppCache one
$kernel = new AppCache($kernel);

$request = Request::createFromGlobals();

$response = $kernel->handle($request);
$response->send();

$kernel->terminate($request, $response);
```

The caching kernel will immediately act as a reverse proxy - caching responses from your application and returning them to the client.


> If you're using the framework.http_method_override option to read the HTTP method from a _method parameter, see the above link for a tweak you need to make.

> The cache kernel has a special getLog() method that returns a string representation of what happened in the cache layer. In the development environment, use it to debug and validate your cache strategy:
> ```
> error_log($kernel->getLog());
> ```

The AppCache object has a sensible default configuration, but it can be finely tuned via a set of options you can set by overriding the getOptions() method:

```
// app/AppCache.php
use Symfony\Bundle\FrameworkBundle\HttpCache\HttpCache;

class AppCache extends HttpCache
{
    protected function getOptions()
    {
        return array(
            'debug'                  => false,
            'default_ttl'            => 0,
            'private_headers'        => array('Authorization', 'Cookie'),
            'allow_reload'           => false,
            'allow_revalidate'       => false,
            'stale_while_revalidate' => 2,
            'stale_if_error'         => 60,
        );
    }
}
```

> Unless overridden in getOptions(), the debug option will be set to automatically be the debug value of the wrapped AppKernel.

Here is a list of the main options:

* default_ttl
    * The number of seconds that a cache entry should be considered fresh when no explicit freshness information is provided in a response. Explicit Cache-Control or Expires headers override this value (default: 0).
* private_headers
    * Set of request headers that trigger "private" Cache-Control behavior on responses that don't explicitly state whether the response is public or private via a Cache-Control directive (default: Authorization and Cookie).
* allow_reload
    * Specifies whether the client can force a cache reload by including a Cache-Control "no-cache" directive in the request. Set it to true for compliance with RFC 2616 (default: false).
* allow_revalidate
    * Specifies whether the client can force a cache revalidate by including a Cache-Control "max-age=0" directive in the request. Set it to true for compliance with RFC 2616 (default: false).
* stale_while_revalidate
    * Specifies the default number of seconds (the granularity is the second as the Response TTL precision is a second) during which the cache can immediately return a stale response while it revalidates it in the background (default: 2); this setting is overridden by the stale-while-revalidate HTTP Cache-Control extension (see RFC 5861).
* stale_if_error
    * Specifies the default number of seconds (the granularity is the second) during which the cache can serve a stale response when an error is encountered (default: 60). This setting is overridden by the stale-if-error HTTP Cache-Control extension (see RFC 5861).

If debug is true, Symfony automatically adds an X-Symfony-Cache header to the response containing useful information about cache hits and misses.

> ### Changing from one Reverse Proxy to another
>
> The Symfony reverse proxy is a great tool to use when developing your website or when you deploy your website to a shared host where you cannot install anything beyond PHP code. But being written in PHP, it cannot be as fast as a proxy written in C. That's why it is highly recommended you use Varnish or Squid on your production servers if possible. The good news is that the switch from one proxy server to another is easy and transparent as no code modification is needed in your application. Start easy with the Symfony reverse proxy and upgrade later to Varnish when your traffic increases.
> 
> For more information on using Varnish with Symfony, see the How to use Varnish cookbook chapter.

> The performance of the Symfony reverse proxy is independent of the complexity of the application. That's because the application kernel is only booted when the request needs to be forwarded to it.
