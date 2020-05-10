# chat app

enter this page

![username](https://github.com/obukoh/chat-app/blob/master/others/images/username.PNG)



chat screen used by Bob

![Bob](https://github.com/obukoh/chat-app/blob/master/others/images/Bob.PNG)



chat screen used by John

![John](https://github.com/obukoh/chat-app/blob/master/others/images/John.PNG)



chat screen used by Mike

![Mike](https://github.com/obukoh/chat-app/blob/master/others/images/Mike.PNG)



# install node.js npm

```bash
# install
$ sudo apt install nodejs npm
```

```bash
# make package
# -y : skip all question
$ npm init -y
```

I used [this page](https://qiita.com/righteous/items/e5448cb2e7e11ab7d477) as reference

```bash
# -s : package.json の dependencies欄 にパッケージ名が記録
$ npm install -s express socket.io
```



# chat.html

## 大枠

```html
<!-- dir="" : 文書全体の書字方向(ltr, rtl) -->
<html lang="en" dir="ltr>"
  <head>
<!-- <style> : css -->
    <style>
      ...
    </style>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="/socket.io/socket.io.js"></script>
  </head>
  <body>
      ...
  </body>
  <script>
      var socket = io();
      var app = new Vue()
      socket.on();
      socket.on();
  </script>
</html>

```

 

## ul タグ

```html
<main id="chat">
      <ul v-for="message in messages">
        <li>{{message}}</li>
      </ul>
      <div class="form">
        <input v-model="chatMessage">
        <button v-on:click="postMessage">送信</button>
      </div>
    </main>

<script>
    var socket = io();
    var app = new Vue({
      el: '#chat',
      data: {
        chatMessage: '',
        messages: []
      },
      methods: {
          postMessage: function() {
        }
        }
      },
    })
</script>
```

> ULタグはUnordered Listの略で、順序のないリストを表示する際に使用します。 リストの各項目はLIタグを用いて記述します。 また、順序のあるリスト表示にはOLタグを使用します。



### Vue.js の v-forディレクティブ

**リストレンダリング**

配列に基づいて、アイテムのリストを描画するために、`v-for` ディレクティブを使用することができます。`v-for` ディレクティブは `item in items` の形式で特別な構文を要求し、`items` はソースデータの配列で、`item` は配列要素がその上で反復されている**エイリアス**です



### Vue.js の v-model ディレクティブ

**フォーム入力バインディング**

form の input 要素 や textarea 要素、 select 要素に双方向 (two-way) データバインディングを作成するには、`v-model` ディレクティブを使用することができます。それは、自動的に入力要素のタイプに基づいて要素を更新するための正しい方法を選択します。



### Vue.js の v-on ディレクティブ

**イベントハンドリング**

多くのイベントハンドラのロジックはより複雑になっていくので、`v-on` 属性の値に JavaScript 式を記述し続けるのは現実的ではありません。そのため、`v-on` は呼び出したいメソッドの名前も受け付けます。



## Vue.js の基本構文

Vue を用いて Hello world! を表示する例を示します。#app-1 の要素に対して Vue を適用します。Vue が適用された要素内では、{{ message }} などの表記が、JavaScript 側で保持する変数の値に置換されます。

```html
<body>

<div id="app-1">{{ message }}</div>  <!-- {{ message }} が Vueデータに置換される -->

<script>
var app1 = new Vue({
  el: '#app-1',                        /* #app-1 要素に対して Vue を適用する */
  data: { message: 'Hello world!' }    /* message という名前のデータを定義する */
})
</script>

</body>
```

　I used [this page](http://www.tohoho-web.com/ex/vuejs.html) as reference



## socket.io

![workflow](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F122992%2Fc1b7e33d-a6f9-ed93-1cf5-54bdd173e7e0.png?ixlib=rb-1.2.2&auto=format&gif-q=60&q=75&w=1400&fit=max&s=5e1104b68bfa50cd0d11bca6a42d47a8)

**「io()」を任意の変数へ格納することでSocket.ioが提供するさまざまなAPIを利用することができる**

```javascript
var socket = io();
```



### socket.emit(送信event, 送信data)

イベントを発生させる

**emit()の第1引数**の「c2s-msg」という文字列で、これは何でも良いのですが、サーバー側の処理も同じ文字列を利用する必要があります。

```javascript
socket.emit('c2s-msg', this.chatMessage);
          this.chatMessage = '';
          return false;
```



### socket.on(受信event, function(受信data))

受信dataを受け取り、処理をおこなう

**第1引数**には今回利用している「chat」という文字列を使い、**第2引数に実行したい関数処理**を記述します。

関数の引数「msg」には、サーバーから送られたメッセージ（文字列）が格納されています。

```javascript
socket.on('s2c-msg', function(msg) {
      console.log(msg);
      app.messages.push(msg);
      window.scrollTo(0, document.body.scrollHeight);
    });
```



# index.js

## 大枠

```javascript
var app = require('express')();
var http = require('http').Server(app);
var io = require('socket.io')(http);
var port = process.env.PORT || 3000;
app.get('/', function(req, res){
  res.sendFile(__dirname + '/chat.html');
});
var messages = [];
var users = [];

io.on('connection', function(socket){
  // サーバー側のメッセージリストをクライアント側に送る（emit）
  // クライアント側から送られたメッセージを受け取り、全クライアントに送る（emit）
});

// httpサーバーを立てる
http.listen(port, function(){
  console.log('listening on *:' + port);
});
```

最初にhttp,express, socket.io のパッケージ読み込み、expressを用いて、リクエストのハンドリング、httpを用いて、サーバーを立ててます。



## socket.io

### io.on('connection', function(socket))

フロント側と接続されたときの処理

**第1引数**に**「connection」**を設定することで、接続されているかどうかを確認できます。

**接続されている場合は、第2引数の関数を実行**することができます。

この関数の引数「socket」に、ユーザー情報も含めて送信されたメッセージが格納されています。

```javascript
io.on('connection', function(socket){

  // サーバー側のメッセージリストをクライアント側に送る（emit）
  socket.emit('init-chat', messages);
  // クライアント側から送られたメッセージを受け取り、全クライアントに送る（emit）
  socket.on('c2s-msg', function(msg) {
      var newMessage = msg;
      messages.push(newMessage);
      io.emit('s2c-msg', newMessage);
  });

});
```



### io.emit(送信event, 送信data)

イベントのブロードキャスト

全ユーザにメッセージを配信

```javascript
io.emit('s2c-msg', newMessage);
```



# user name

```javascript
# chat.html
socket.on('connect', function(){
    socket.emit('setUserName', prompt('ユーザー名を入力してください'));
    });
```

```javascript
# index.js
socket.on('setUserName', function (userName) {
    if(!userName) userName = '匿名';
    socket.userName = userName;
  });

socket.on('c2s-msg', function(msg) {
      var newMessage = msg;
      messages.push(socket.userName + ' : ' + newMessage);
      io.emit('s2c-msg', socket.userName + ' : ' + newMessage);
  });
```



# 実行方法

```bash
$ node index.js
```



# 参考

I used [this page](https://kumatetsublog.com/shoot/blog/chat-app-1) as reference.

Thank you very much!!

