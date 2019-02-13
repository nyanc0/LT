# Let's play with Integromat

---

## @css[h2](自己紹介)
- 福岡侑里奈
- TIS(4年目)
- Androidアプリ開発
- モンハンが好き(アイルーかわいい)
- アニメも好き

---

## @css[h1](Integromat)

---
## @css[h2](Integromatとは？)

- 2016年サービスイン
- 複数サービス間連携の機能を提供
- (ほぼ)ノンコーディングで利用可能

---

## @css[h2](例えば...)

![what_is_integromat](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/what_is_integromat.png?raw=true)

---

## @css[h2](利用し始めた背景)

Backlogの通知をSlackに流したいな・・・

- 自由に使えるサーバーがない |
- インフラ構築の知識もない |
- ツール開発の工数がそれほど取れない |

---

### @css[h2](環境構築不要<br>＆<br>開発工数がかからない)

---

## @css[h2](似たようなサービス)

![zaiper](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/zaiper.png?raw=true)
![ifttt](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/ifttt.png?raw=true)

---

## @css[h2](何が違うのか？)

- 無料枠が大きい(1000回/月)
- 複数サービスへの同時連携が可能
- カスタマイズがしやすい

---

## @css[h2](BacklogはWebhook連携だけ)  
つまらない

---

## @css[h2](アニメイベント情報を<br>即キャッチする)
重要

---

![gmail_7](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_7.png?raw=true)

---

## @css[h2](まずはScenarioの作成)

Scenario = 連携の設計図

![gmail_0](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_0.png?raw=true)

---

## @css[h2](Gmailを受け取って)
![gmail_1](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_1.png?raw=true)

---

## @css[h2](APIでPOST)

![gmail_2](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_2.png?raw=true)

---

## @css[h2](本文をごにょごにょして)

![gmail_3](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_3.png?raw=true)

---

## @css[h2](パース)

![gmail_4](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_4.png?raw=true)

---

## @css[h2](送信元別にSlackへ通知)

![gmail_5](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_5.png?raw=true)

---

## @css[h2](モジュールの作成)
モジュール = 連携するサービス

---

## @css[h2](たとえばSlackモジュールの場合)

---

## @css[h2](メニューから選択)

![webhook_2](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/webhook_2.png?raw=true)

---

## @css[h2](チャンネルとメッセージを設定)

<img src="https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/slack_1.png?raw=true" height="300">
<img src="https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/slack_2.png?raw=true" height="300">

---

## @css[h2](メッセージ設定完了)
![gmail_7](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_7.png?raw=true)

---

## @css[h2](キモはAPI連携)

- メールによって日本語が文字化けする...
- Slackに送信可能な文字数に制限
- リンクはメール本文から抜き出す必要あり

----

## @css[h2](キモはAPI連携)

1. Gmailの内容をJsonでPOST
2. エンコード
3. 特定のURLの抜き出し
4. Slack通知用に文字数をカット
5. 新たなJsonとして返す

---

## @css[h2](ここもサーバレスでやりたい)

---

## @css[h2](Azure Functions)
- サーバー構築不要
- 枠内であれば無料で利用可能
- JS,C#,F#で作成可能

---

## @css[h2](Azureで関数の作成)

![gmail_10](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_10.png?raw=true)

---

## @css[h2](IntegromatのHTTPモジュールに<br>APIのURLを設定)

<img src="https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_11.png?raw=true" height="300">

---

## @css[h2](Jsonの作成・パースは<br>Integromatにお任せ)

<img src="https://raw.githubusercontent.com/nyanc0/Android/other_knowledge/integromat/images/gmail_8.png" height="300">
<img src="https://raw.githubusercontent.com/nyanc0/Android/other_knowledge/integromat/images/gmail_9.png" height="300">

---
### @css[h2](完成！)
![gmail_0](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_0.png?raw=true)

---

## @css[h2](もう少し詳しい使い方)

https://github.com/nyanc0/Android/tree/other_knowledge/integromat

---

## @css[h2](メリット)
- 簡単な連携ならノンコーディングで利用できる
- 小規模なPJや趣味なら無料枠で対応可能
- カスタマイズ機能が多いのである程度複雑な要件にも耐えられる
- 連携できるサービスがどんどん増えている

---

## @css[h2](デメリット)
- 日本語の説明ないので英語との戦い
- 慣れるまでに時間がかかる
- 日本語処理がおかしいことがある(かも？)
---

Thank you.
