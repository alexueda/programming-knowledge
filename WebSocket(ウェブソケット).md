+++
# WebSocket
## WebSocketについて

HTTPはクライアント-サーバーアーキテクチャに基づいています。クライアントが常にリクエストを開始し、サーバーが応答します。これは、ハイパーリンクで接続されたグローバルなドキュメントライブラリを構築する場合には適していますが、多くの他のユースケースでは機能しません。通知アプリケーション、分散タスク処理、ピアツーピア通信、非同期イベントなどには、2つ以上の接続されたデバイスによって開始される通信が必要です。

長年、ウェブ開発者たちはクライアント/サーバーモデルの制限を克服するためのハックを作成してきました。その中には、クライアントが頻繁にサーバーにpingを送ってサーバーが何かを言いたいのか確認する方法や、クライアントがサーバー上でイベントが発生するのを待ちながら長時間接続を開いたままにする方法が含まれていました。当然ながら、これらの解決策はどれも優雅でも効率的でもありませんでした。

最終的に2011年、通信プロトコルであるWebSocketがこの問題を解決するために作られました。WebSocketの核心機能は、完全な双方向通信が可能である点です。つまり、クライアントが通常のHTTPを使用して最初の接続を確立し、その後サーバーがWebSocket接続にアップグレードすると、関係がピアツーピア接続に変わり、どちらの当事者もいつでも効率的にデータを送信できます。

## WebSocketアップグレード

WebSocket接続は依然として2者間のみです。そのため、ユーザーグループ間での会話を促進したい場合は、サーバーが仲介役として機能する必要があります。それぞれのピアが最初にサーバーに接続し、その後サーバーがピア間のメッセージを転送します。

## WebSocketピア

### WebSocket会話の作成

ブラウザ上で動作するJavaScriptは、ブラウザのWebSocket APIを使用してWebSocket接続を開始できます。最初に、通信したいポートを指定してWebSocketオブジェクトを作成します。

次に、`send`関数を使用してメッセージを送信し、`onmessage`関数を使用してコールバックを登録してメッセージを受信できます。

```javascript
const socket = new WebSocket('ws://localhost:9900');

socket.onmessage = (event) => {
  console.log('received: ', event.data);
};

socket.send('I am listening');
```

サーバーは`ws`パッケージを使用して、ブラウザが使用しているのと同じポートでリッスンしている`WebSocketServer`を作成します。WebSocketServerを作成する際にポートを指定することで、サーバーにそのポートでHTTP接続をリッスンし、リクエストに`connection: Upgrade`ヘッダーがある場合、自動的にWebSocket接続にアップグレードするよう指示します。

接続が検出されると、サーバーの`connection`コールバックが呼び出されます。サーバーはその後、`send`関数を使用してメッセージを送信し、`on message`関数を使用してコールバックを登録してメッセージを受信できます。

```javascript
const { WebSocketServer } = require('ws');

const wss = new WebSocketServer({ port: 9900 });

wss.on('connection', (ws) => {
  ws.on('message', (data) => {
    const msg = String.fromCharCode(...data);
    console.log('received: %s', msg);

    ws.send(`I heard you say "${msg}"`);
  });

  ws.send('Hello webSocket');
});
```

次回の指示では、この例を実行およびデバッグする方法を示します。
+++
