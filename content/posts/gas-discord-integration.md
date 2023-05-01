---
title: "スプレッドシートを更新すると自動でDiscordに通知が飛ぶ仕組み"
date: 2023-05-01T17:36:07+09:00
draft: false
---

Googleスプレッドシートで更新を行うとDiscordのチャンネルに通知が飛ぶ仕組みを紹介します。  

この例では、C列に「相談中」または「回答済」と入力することが通知を飛ばす条件です。  

条件の部分は適宜変更する必要があります。 

### 1. 更新通知を飛ばしたいDiscordのサーバでWebhookを作成
[こちら](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks)を参考に。

### 2. 更新を行うスプレッドシートのGASで以下のスクリプトを作成して保存
```
function onCellValueChange(e) {
  const url = 'https://docs.google.com/spreadsheets/YOUR-SPREADSHEETSURL';
  // 条件
  if (e.range.getColumn() === 3) {
    if (e.value === "相談中") {
      postMessageToDiscord(`🎾 相談を追加しました。\n ${url}`);
    } else if (e.value === "回答済") {
      postMessageToDiscord(`🏉 回答を入力しました。\n ${url}`);
    }
  }
}

function postMessageToDiscord(message) {

  // WebhookURLを設定する
  const discordUrl = 'https://discord.com/api/webhooks/YOUR-DISCORD-SERVER_WEBHOOK-URL';
  const payload = JSON.stringify({content: message});
  const params = {
    headers: {
      'Content-Type': 'application/json'
    },
    method: "POST",
    payload: payload,
    muteHttpExceptions: true
  };

  const response = UrlFetchApp.fetch(discordUrl, params);
  Logger.log(response.getContentText());

}

```

### 3. C列に「相談中」「回答済」と入力する
Discordに通知が飛びます。
