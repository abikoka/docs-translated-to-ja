Contents ≫ APM ≫ New Relic APM ≫ Apdex

## Apdex: Measuring user satisfaction (Apdex: ユーザ満足度計測)

Apdex is an industry standard to measure users' satisfaction with the response time of an application or service. It's a simplified Service Level Agreement (SLA) solution that gives application owners better insight into how satisfied users are, in contrast to traditional metrics like average response time, which can be skewed by a few very long responses.   
Apdexは、アプリケーションやサーバのレスポンス時間を介したユーザの満足度に対する業界標準です。満足されたユーザーがいるかどうか、アプリケーションオーナーに見抜けるようにさせることを簡単化したサービス品質保証(SLA)のソリューションであり、少ない長時間の応答時間によって歪みとなりえる平均応答時間のような従来のメトリクスとは対照となる。

## Contents

* Apdex measurements 
* Apdex levels
* Apdex score
* Error pages
* Dissatisfaction percentage
* For more help

## Apdex measurements

Apdex is a measure of response time based against a set threshold. It measures the ratio of satisfactory response times to unsatisfactory response times. The response time is measured from an asset request to completed delivery back to the requestor.  
Apdexは、決められた敷居値を超えることにもとづく応答速度の計量法です。不満足な応答速度に対する満足な応答時間の比率を計測します。応答時間はアセットの要求から要求者へ転送完了するまでを計測されます。

The application owner defines a response time threshold T. All responses handled in T or less time satisfy the user.  
アプリケーションのオーナーは応答時間の敷居値「T」を定義します。すべての応答は「T」で処理させるかユーザを満足させる時間以内とする。

For example, if T is 1.2 seconds and a response completes in 0.5 seconds, then the user is satisfied. All responses greater than 1.2 seconds dissatisfy the user.  
例えば、もし「T」が1.2秒であるとして、応答時間が0.5秒で完了するならば、ユーザは満足している。1.2秒より長いすべての応答時間は、ユーザに満足されない。

You can define Apdex T values for each of your New Relic APM applications, with separate values for app server and end-user browser performance (page load timing). You can also define individual Apdex T thresholds for key transactions.  
あなたがNew Relic APM アプリケーションの各々に対して、Aappサーバとend-userブラウザパフォーマンス（ページ読み込み時間）を分けて持たせたpdex T値を定義できる。keyトランザクションに対して独立のApdex T閾値を定義することもできる。


For a summary of Apdex, watch this video (approximately 6.5 minutes).  
（Apdexの集計に対して、概要説明のビデオが公開されている）


## Apdex levels

Apdex tracks three response counts:  
Apdexは3つの応答回数をトラックする：

* Satisfied: The response time is less than or equal to T. (満足された： 応答時間が「T」より小さいか同じであるとき）
* Tolerating: The response time is greater than T and less than or equal to 4T. In this example, 4 x 1.2 = 4.8 seconds as the maximum tolerable response time. (我慢できる：応答時間が「T」より大きいが「T」の4倍より小さい。この例では 最大の我慢できる時間として、4 x 1.2 = 4.8秒 となる)
    Frustrated: The response time is greater than 4T. (失望させた：応答時間が「T」の4倍おり長い。)

The Time calculation will change based on your own app's T setting. In the following example, T = 1.2 seconds.  
時間計測はあなた自身のappのT設定に基づいて変化します。以下のサンプルでは T = 1.2秒です。

| Level |  Multiplier | Time (T Example = 1.2) |
| Satisfied |  T or less |  <= 1.2 seconds |
| Tolerated |  >T, <= 4T |  Between 1.2 and 4.8 seconds |
| Frustrated | > 4T | Greater than 4.8 seconds |

```
Note: Your configuration file's apdex_f value is four times your app server's Apdex T value. This threshold is useful, for example, with transaction traces. For more information, see the configuration file documentation for your New Relic agent.
注意： あなたの設定ファイルのapdex_f値がappサーバのApdex T値を4倍します。この閾値は便利です。たとえば、トランザクションのトレースに持たせるなど。より詳しい情報について、あなたのNew Relic エージェントに対する設定ファイルのドキュメントを見なさい。
```

## Apdex score

The Apdex score is a ratio value of the number of satisfied and tolerating requests to the total requests made. Each satisfied request counts as one request, while each tolerating request counts as half a satisfied request.  
Apdexスコアは、生成されるすべてのリクエストに対する満たされたときと我慢できるリクエストの数の比率です。各満足されたリクエストは1リクエストで数え、各我慢できるリクエスト数は満足されたリクエスト数の半分として数える。

chart-apdex-noexp.png
The Apdex score is based on the ratio of satisfied and tolerating requests to the total requests made.
(Request time RとTの関係図）

### Example:

* During a 2 minute period a server handles 200 requests. (2分間にサーバは200リクエスト処理する。)
* The threshold T = 0.5 seconds (500ms). This value is arbitrary and is selected by the user. (閾値T=0.5秒(500ms)。この値は任意にあり、ユーザによって選択されている）
* 170 of the requests were handled within 500ms, so they are classified as Satisfied. (リクエストのうち170は500ms以内で処理されていた。そのためそれらは満足されたものとして分類される。
* 20 of the requests were handled between 500ms and 2 seconds (2000 ms), so they are classified as Tolerating. (リクエストのうち20は500msから2秒(2000ms)の間で処理された。そのため、我慢できるものとして分類された）
* The remaining 10 were not handled properly or took longer than 2 seconds, so they are classified as Frustrated. (残る10は適切に処理されなかったか2秒より長くかかっていた。そのため、失望されたものと分類された)
* The resulting Apdex score is 0.9: (170 + (20/2))/200 = 0.9. (結果としてApdexスコアは0.9である(170 + (20/2)/200 = 0.9))

Tip: You can download this New Relic Apdex example [PDF | 81KB].

## Error pages

Errors frustrate your users. Any request that raises a server-side error is considered a frustrating response, no matter how fast it returns to the user. Nobody likes to see a "500: Application Error" page. This makes it possible to have an average response time that is well below the Apdex T but still have a poor Apdex score.
エラーはあなたのユーザ達を失望させる。サーバ側のエラーが起こるあらゆるリクエストは失望するレスポンスとみなされる。たとえどんなにユーザへ早く返ったとしても。誰もが"500: アプリケーションエラー"ページを見たくない。これは、平均応答時間では良いとなることに対して、Apdex Tでは粗悪なApdex Tスコアとなることを示せます。

## Dissatisfaction percentage

The dissatisfaction percentage is the percentage of the total dissatisfaction experienced by the app's users that is contributed by this transaction.
"dissatisfaction percentage"は、このトランザクションによって与えられるappのユーザによる経験されたすべての不満足の度合いです。

```
Transaction % = (Frustrations(Transaction) + Tolerations(Transaction)/2)

App % = (Frustrations(App) + Tolerations(App)/2)
```

If a transaction is always frustratingly slow but rarely visited, it will not contribute much to the app's total dissatisfaction. Conversely, if a transaction normally is fast (for example, Apdex 0.95) but has high throughput, this may contribute a large proportion of the app's dissatisfaction simply because it contributes a large proportion of your app's traffic.
もし、トランザクションが常に失望的に低いがまれに訪問されるならば、appの総不満度合を与えられないだろう。反対に、トランザクションが普通に早い（例えば、Apdex 0.95)が速いスループットならば、これはappの不満度が単純に大きく均整がとれている。なぜなら、あなたのappのトラフィックのかなり均整を与えるため。


## For more help

Additional documentation resources include:
追加のドキュメントソースを含む：

* [Changing your Apdex settings](https://docs.newrelic.com/docs/site/changing-your-apdex-settings) (where to edit your Apdex settings)
* [Viewing your Apdex score](https://docs.newrelic.com/docs/site/viewing-your-apdex-score) (where the Apdex score appears in the interface)
* [Applications Overview](https://docs.newrelic.com/docs/applications-menu/applications-overview) and [Browser Overview](https://docs.newrelic.com/docs/new-relic-browser/browser-overview) (using the main New Relic dashboards)
* [Apdex Alliance](http://apdex.org/overview.html) (website with detailed information about Apdex)

Join the discussion about New Relic APM in the New Relic Community Forum ! The Community Forum is a public platform to discuss and troubleshoot your New Relic toolset.

If you need additional help, get support at support.newrelic.com .

