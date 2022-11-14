---
title: 202 Accepted
slug: Web/HTTP/Status/202
---

{{HTTPSidebar}}

HTTP (HyperText Transfer Protocol) の **`202 Accepted`** レスポンスステータスコードは、リクエストを受け取ったが、処理が完了していないことを表します。実際には、処理はまだ始まっていない可能性もあります。そのリクエストは、実際に処理が行われたときに拒否される可能性があるため、最終的に処理されるかどうかはわかりません。処理が実際に行われたときに許可されないかもしれないからです。

202 はコミットするものではありません。つまり、 HTTP にはリクエストの処理結果を示す非同期レスポンスを後で送信する方法がありません。別のプロセスやサーバーがリクエストを処理する場合や、バッチ処理のためのものです。

## ステータス

```
202 Accepted
```

## 仕様書

| 仕様書                                                   | 題名                                                         |
| -------------------------------------------------------- | ------------------------------------------------------------- |
| {{RFC("7231", "202 Accepted" , "6.3.3")}} | Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content |

## 関連情報

- {{HTTPHeader("Accept")}}