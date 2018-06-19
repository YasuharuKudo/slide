# Let's play with the GAS!

<br>

### 〜 ルーチンワーク撲滅計画 〜
###### Maverick inc. @edochan

---

## Who are you?

<br>

@fa[arrows gp-tip](Press F to go Fullscreen)

@fa[microphone gp-tip](Press S for Speaker Notes)

---

## What are you doing?

- GASの紹介 |

---

## What is GAS?

- Google Apps Script |
- Googleが提供しているJavaScript動作環境 |
- GmailとかSpreadSheetとかその他外部サービスと連携できる |
- Googleのアカウントがあれば好きに使える |
- ただ！ |

---

## Motivation

- 不幸なことに何らかの発表ネタが必要となった |
- 便利だけれどみんな触ってない？ |
- LT資料作ったことなかったので練習 |

---

## Let's try it!

---

## Ingredients

- Google Account |
- Google Drive |
- Google Apps Script |
- Slack |

---

## How to use GAS?

- Google Account作る |
- Google Driveを開く |
- Google Apps Scriptのアプリの追加 |
- Google Apps Scriptのファイルを作成 |

---

## How to install GAS

---?image=beige/img/gas_install1.png

---?image=beige/img/gas_install2.png

---

## Try to write

- いったん書いてみる |

---

```javascript
function takeVacation() {
  var date = new Date()
  var today = date.getMonth() + "/" + date.getDate()
  var to = "edo1km@gmail.com"
  var subject = "【勤怠】 " + today + " 休暇"
  var body = "各位\n\nお疲れ様です、工藤です。\n体調不良でお休みします。\nよろしくお願いいたします。\n\n以上"
  MailApp.sendEmail(to, subject, body)
}
```

@[3,5](日付はちゃんと入れる)
@[6](文章を適度に変えるのは今後の課題)

---

## Operation check

---?image=beige/img/mail_test.png

---

## よさそう

---

## Next step

- こいつを自動化する |
- Slackのステータスを活用してみよう |
- Slackのステータスがアクティブじゃなかったら出す |
- ステータスって取れるのか？多分取れるよね |

---?image=beige/img/slackapi_users.getPreference.png

---

## あった

https://api.slack.com/methods/users.getPresence

---

### ok
```javascript
{
    "ok": true,
    "presence": "active"
}
```

### ng

```javascript
{
    "ok": false,
    "error": "invalid_auth"
}
```

---

## Slack Web APIを叩く

- とりあえずfetch叩けばよい？ |
- 認証どうするんだろう？ |

---

## ライブラリ発掘

https://qiita.com/soundTricker/items/43267609a870fc9c7453

- Slackのトークンが必要 |
- GASはライブラリキーというものを入れるとimportできるらしい |
- このライブラリは活用させてもらう |

---

## Slack Legacy Tokens発行

https://api.slack.com/custom-integrations/legacy-tokens

- レガシーらしいがよく調べていない |
- 使おうとしたら承認されてなかった |

---?image=beige/img/approved_my_request.png&size=auto 90%

---

### SlackAppを試してみる

- トークンは取れたので使ってみる |
- 適当なコードを拾ってくる |

---

### サンプルコード

```javascript
var slackAccessToken = 'ここにアクセストークンを記載';
function test() {
  var slackApp = SlackApp.create(slackAccessToken)
  var channelId = "#unk"
  var message = "I'm bot"
  var options = {
    username: "edochan"
  }
  slackApp.postMessage(channelId, message, options);
}
```

@[4](サンプル文だと#generalだったのだが肥溜めに捨てる)

---?image=beige/img/bot_test.png&size=auto 90%

---

## ステータスを取る

- 動くことはわかったのでステータス取得の方法を見る |
- Activeかどうかはpresenceに入ってる |
- SlackAppのAPIリファレンスを見てみる |
https://script.google.com/macros/library/d/M3W5Ut3Q39AaIwLquryEPMwV62A3znfOO/22

---?image=beige/img/presence_set_active.png

---?image=beige/img/presence_set_away.png

---

## SlackApp...

- presenceを返してくれそうなものがない |
- 別れを告げて自分で叩く |
- ユーザIDが必要なので調べる |
https://slack.com/api/users.list?token=[TOKEN]

---

users.listのレスポンスを一部抜粋

```json
{
  "id":"U03S4HQC1","team_id":"T02G57K6E","name":"edochan","deleted":false,"color":"4bbe2e",
  "real_name":"yasuharu kudo","tz":"Asia\/Tokyo","tz_label":"Japan Standard Time",
  "tz_offset":32400,
  "profile":{"title":"","phone":"090-2879-3871","skype":"edonaruto","real_name":"yasuharu kudo","real_name_normalized":"yasuharu kudo","display_name":"edochan","display_name_normalized":"edochan","status_text":"\u4eca\u65e5\u306f\u3055\u3048\u3066\u308b","status_emoji":":fire:","status_expiration":0,"avatar_hash":"90e3543f25ff","image_original":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_original.png","email":"edo1km@gmail.com","first_name":"yasuharu","last_name":"kudo","image_24":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_24.png","image_32":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_32.png","image_48":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_48.png","image_72":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_72.png","image_192":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_192.png","image_512":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_512.png","image_1024":"https:\/\/avatars.slack-edge.com\/2018-03-08\/325911563025_90e3543f25ffbb39a0b2_1024.png","status_text_canonical":"","team":"T02G57K6E","is_custom_image":true},
  "is_admin":false,"is_owner":false,"is_primary_owner":false,"is_restricted":false,"is_ultra_restricted":false,
  "is_bot":false,"updated":1529293236,"is_app_user":false,"has_2fa":false
},
```

@[2](ID発見)

---

### users.getPresenceを試す

consoleで叩いてみる

```javascript
var url = "https://api.slack.com/methods/users.getPresence?"
var params = new URLSearchParams()
params.set("token", "ここにトークンを入れる")
params.set("user", "自分のIDを入れる")
fetch(url + params.toString()
).then((e) => e.json()
).then((json) => console.log(json))
```

---

Slackを立ち上げている時のusers.getPresenceのレスポンス

```json
{
    "ok": true,
    "presence": "active",
    "online": true,
    "auto_away": false,
    "manual_away": false,
    "connection_count": 1,
    "last_activity": 1529329712
}
```

@[3](presenceは"active"になっている)

---

Slackを落としている時のusers.getPresenceのレスポンス

```json
{
    "ok": true,
    "presence": "away",
    "online": false,
    "auto_away": false,
    "manual_away": false,
    "connection_count": 0
}
```

@[3]("away"になっているので判別に使えそう)

---

<p><span class="slide-title">gasでのまとめ</span></p>

```javascript
var MY_SLACK_ID = "自分のID"
var SLACK_ACCESS_TOKEN = 'Slackのアクセストークン';

function attendanceCheck() {
  if (!isActive(MY_SLACK_ID)) {
    takeVacation()
  }
}

function takeVacation() {
  var date = new Date()
  var today = date.getMonth() + 1 + "/" + date.getDate()
  var to = "talent@mvrck.co.jp"
  var subject = "【勤怠】 " + today + " 休暇"
  var body = "各位\n\nお疲れ様です、工藤です。\n体調不良でお休みします。\nよろしくお願いいたします。\n\n以上"
  MailApp.sendEmail(to, subject, body)
}

function isActive(userId) {
  var params = "token=" + SLACK_ACCESS_TOKEN + "&user=" + userId
  var response = JSON.parse(UrlFetchApp.fetch("https://slack.com/api/users.getPresence?" + params))
  return response.presence == "active"
}
```

@[10-17](おやすみメール発射装置)
@[19-23](Slackのログイン状況を返すやつ)
@[4-8](Slack立ち上がっていなかったらtakeVacationを叩く)

---

## みんなもGASで楽しよう！

---

## Template Features

- Code Presenting |
- Repo Source, Static Blocks, GIST |
- Custom CSS Styling |
- Slideshow Background Image |
- Slide-specific Background Images |
- Custom Logo, TOC, and Footnotes |

---?code=sample/go/server.go&lang=golang&title=Golang File

@[1,3-6](Present code found within any repo source file.)
@[8-18](Without ever leaving your slideshow.)
@[19-28](Using GitPitch code-presenting with (optional) annotations.)

---

@title[JavaScript Block]

<p><span class="slide-title">JavaScript Block</span></p>

```javascript
// Include http module.
var http = require("http");

// Create the server. Function passed as parameter
// is called on every request made.
http.createServer(function (request, response) {
  // Attach listener on end event.  This event is
  // called when client sent, awaiting response.
  request.on("end", function () {
    // Write headers to the response.
    // HTTP 200 status, Content-Type text/plain.
    response.writeHead(200, {
      'Content-Type': 'text/plain'
    });
    // Send data and end response.
    response.end('Hello HTTP!');
  });

// Listen on the 8080 port.
}).listen(8080);
```

@[1,2](You can present code inlined within your slide markdown too.)
@[9-17](Displayed using code-syntax highlighting just like your IDE.)
@[19-20](Again, all of this without ever leaving your slideshow.)

---?gist=onetapbeyond/494e0fecaf0d6a2aa2acadfb8eb9d6e8&lang=scala&title=Scala GIST

@[23](You can even present code found within any GitHub GIST.)
@[41-53](GIST source code is beautifully rendered on any slide.)
@[57-62](And code-presenting works seamlessly for GIST too, both online and offline.)

---

## Template Help

- [Code Presenting](https://github.com/gitpitch/gitpitch/wiki/Code-Presenting)
  + [Repo Source](https://github.com/gitpitch/gitpitch/wiki/Code-Delimiter-Slides), [Static Blocks](https://github.com/gitpitch/gitpitch/wiki/Code-Slides), [GIST](https://github.com/gitpitch/gitpitch/wiki/GIST-Slides)
- [Custom CSS Styling](https://github.com/gitpitch/gitpitch/wiki/Slideshow-Custom-CSS)
- [Slideshow Background Image](https://github.com/gitpitch/gitpitch/wiki/Background-Setting)
- [Slide-specific Background Images](https://github.com/gitpitch/gitpitch/wiki/Image-Slides#background)
- [Custom Logo](https://github.com/gitpitch/gitpitch/wiki/Logo-Setting), [TOC](https://github.com/gitpitch/gitpitch/wiki/Table-of-Contents), and [Footnotes](https://github.com/gitpitch/gitpitch/wiki/Footnote-Setting)

---

## Go GitPitch Pro!

<br>
<div class="left">
    <i class="fa fa-user-secret fa-5x" aria-hidden="true"> </i><br>
    <a href="https://gitpitch.com/pro-features" class="pro-link">
    More details here.</a>
</div>
<div class="right">
    <ul>
        <li>Private Repos</li>
        <li>Private URLs</li>
        <li>Password-Protection</li>
        <li>Image Opacity</li>
        <li>SVG Image Support</li>
    </ul>
</div>

---

### Questions?

<br>

@fa[twitter gp-contact](@gitpitch)

@fa[github gp-contact](gitpitch)

@fa[medium gp-contact](@gitpitch)

---?image=assets/image/gitpitch-audience.jpg

@title[Fork this Template!]

### <span class="white">Get your presentation started!</span>
### [Download this template @fa[external-link gp-download]](https://gitpitch.com/template/download/beige)

