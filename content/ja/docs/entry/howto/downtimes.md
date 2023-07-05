---
Title: ダウンタイムを設定する
Date: 2019-07-16T13:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/howto/downtimes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613422755482
---

Mackerelでは、時間帯を指定して監視を停止することができます。
定期的なバッチ処理や、メンテナンス期間など、あらかじめ負荷が上がることが分かっている期間に、アラートが発報されないように設定することができます。

監視を停止させる期間や監視の対象スコープを含む設定のことを、ダウンタイムと呼びます。

[:contents]

## ダウンタイムを設定する
### コンソール画面で設定する
ダウンタイムは https://mackerel.io/my/downtimes より設定することができます。

最も基本的なダウンタイムは、監視を停止する期間のみが指定されたダウンタイムです。
開始日時と監視を停止させる期間を設定します。
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190905/20190905172415.png)

監視停止を一定間隔で繰り返す場合は、繰り返し設定を行ってください。
隔週の土日といった設定も行うことができます。
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190905/20190905172423.png)

最後に、ダウンタイムの対象スコープを設定します。
サービスやロール、監視ルールによる絞り込みや除外設定に対応しています。
サービスやロールの指定と監視ルールの指定は、AND条件となります。
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190905/20190905172431.png)

### APIで設定する
APIを利用してダウンタイムを設定することもできます。
以下のコマンドは、`TestService`を対象に現在時刻から一時間のダウンタイムを設定するコマンドです。

```shell
curl -X POST https://api.mackerelio.com/api/v0/downtimes \
    -H "X-Api-Key: $MACKEREL_APIKEY" \
    -H "Content-Type: application/json" \
    -d '{"name": "Test downtime", "start": '$(date +%s)', "duration": 60, "serviceScopes": ["TestService"] }'
```

詳しくは、 [APIドキュメント](https://mackerel.io/ja/api-docs/entry/downtimes)を参照してください。

### 注意事項

Mackerel の仕様上、ダウンタイムの開始が日曜日かつ期間が 24 時間を超える場合、24 時間以降はダウンタイムが無視されますのでご注意ください。  
たとえばダウンタイム開始日時を日曜日の 00:00、ダウンタイム期間を 24 時間 30 分に設定した場合、月曜日の 00:00 〜 00:30 は監視が停止しません。繰り返し設定の場合も同様です。  
日曜日に開始するダウンタイムについては期間が 24 時間を超えないように設定してください。
