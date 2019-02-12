# Let's play with Integromat

---

## 自己紹介
- TIS(4年目)
- Androidアプリ開発
- モンハンが好き(アイルーかわいい)
- アニメも好き

---

## Integromat

---
## Integromatとは？

- 2016年サービスイン
- 様々なWebサービスの連携機能を提供
- (ほぼ)ノンコーディングでサービス連携が可能

---

## 例えば...

![what_is_integromat](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/what_is_integromat.png?raw=true)

---

## 利用し始めた背景

Backlogの通知をSlackに流したいな・・・

- 自由に使えるサーバーがない
- インフラ構築の知識もない
- ツール開発の工数がそれほど取れない  

#### @color[orange](環境構築不要＆開発工数がかからない)

---

### 似たようなサービス

![zaiper](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/zaiper.png?raw=true)  
![ifttt](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/ifttt.png?raw=true)

---

### 何が違うのか？

- 無料枠が大きい(1000回/月)
- 複数サービスへの同時連携が可能
- カスタマイズがしやすい

---

BacklogはWebhook連携だけ  
(つまらない)

---

アニメイベント情報を即キャッチする  
(重要)

---

![gmail_7](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_7.png?raw=true)

---

### まずはScenarioの作成

Scenario = 連携の設計図

![gmail_0](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_0.png?raw=true)

---

### Gmailを受け取って
![gmail_1](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_1.png?raw=true)

---

### APIでPOST

![gmail_2](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_2.png?raw=true)

---

### 本文をごにょごにょして

![gmail_3](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_3.png?raw=true)

---

### パース

![gmail_4](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_4.png?raw=true)

---

### 送信元別にSlackへ通知

![gmail_5](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_5.png?raw=true)

---

### モジュールの作成
モジュール = 連携するサービス

---

### たとえばSlackモジュールの場合

---

### メニューから選択

![webhook_2](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/webhook_2.png?raw=true)

---

### チャンネルとメッセージを設定

<img src="https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/slack_1.png?raw=true" height="300">

<img src="https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/slack_2.png?raw=true" height="300">

---

### キモはAPI連携

1. Gmailの内容をJsonでPOST
2. エンコード
3. 特定のURLの抜き出し
4. Slack通知用に文字数をカット
5. 新たなJsonとして返す

---

### ここもサーバレスでやりたい

---

### Azure Functions
- サーバー構築不要
- 枠内であれば無料で利用可能
- JS,C#,F#で作成可能

---

### Azureで関数(Functions)の作成

![gmail_10](https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_10.png?raw=true)

---

### IntegromatのHTTPモジュールに<br>APIのURLを設定

<img src="https://github.com/nyanc0/Android/blob/other_knowledge/integromat/images/gmail_11.png?raw=true" height="600">

---

### Jsonの作成・パースはIntegromatにお任せ

<img src="https://raw.githubusercontent.com/nyanc0/Android/other_knowledge/integromat/images/gmail_8.png" height="300">

<img src="https://raw.githubusercontent.com/nyanc0/Android/other_knowledge/integromat/images/gmail_9.png" height="300">

---

### もう少し詳しい使い方
https://github.com/nyanc0/Android/tree/other_knowledge/integromat

---

### メリット
- 簡単な連携ならノンコーディングで利用できる
- 小規模なPJや趣味なら無料枠で対応可能
- カスタマイズ機能が多いのである程度複雑な要件にも耐えられる
- 連携できるサービスがどんどん増えている

---

### デメリット
- 日本語の説明ないので英語との戦い
- 慣れるまでに時間がかかる
- 日本語処理がおかしいことがある(かも？)
---

Thank you.
