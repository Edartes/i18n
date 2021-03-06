# ipcMain

> メインプロセスからレンダラープロセスへ非同期で通信を行います。

プロセス: [Main](../glossary.md#main-process)

`ipcMain` オブジェクトは [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter) クラスのインスタンスの一つです。 メインプロセスで使用される場合、レンダラープロセス (ウェブページ) から送られる非同期及び同期メッセージを処理します。 レンダラーから送信されたメッセージは、このモジュールに送られます。

## メッセージ送信

また、メインプロセスからレンダラープロセスにメッセージを送ることもできます。より詳しくは [webContents.send](web-contents.md#contentssendchannel-arg1-arg2-) を参照して下さい。

* メッセージを送信しているとき、イベント名は `channel` です。
* 同期メッセージに返信をするには、`event.returnValue` を設定する必要があります。
* To send an asynchronous message back to the sender, you can use `event.reply(...)`. This helper method will automatically handle messages coming from frames that aren't the main frame (e.g. iframes) whereas `event.sender.send(...)` will always send to the main frame.

レンダラー/メインプロセス間のメッセージの送信と処理の例:

```javascript
// メインプロセス
const { ipcMain } = require('electron')
ipcMain.on('asynchronous-message', (event, arg) => {
  console.log(arg)  // "ping"を表示
  event.reply('asynchronous-reply', 'pong')
})

ipcMain.on('synchronous-message', (event, arg) => {
  console.log(arg)  // "ping"を表示
  event.returnValue = 'pong'
})
```

```javascript
// レンダラープロセス（ウェブページ）
const { ipcRenderer } = require('electron')
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')) // "pong"を表示

ipcRenderer.on('asynchronous-reply', (event, arg) => {
  console.log(arg) // "pong"を表示
})
ipcRenderer.send('asynchronous-message', 'ping')
```

## メソッド

`ipcMain` オブジェクトは、イベントを受け取るために以下のメソッドがあります。

### `ipcMain.on(channel, listener)`

* `channel` String
* `listener` Function

`channel` に新しいメッセージが来たときに `listener` が `listener(event, args...)` として呼ばれます、

### `ipcMain.once(channel, listener)`

* `channel` String
* `listener` Function

一回だけの `listener` イベント関数を追加します。この `listener` は次に `channel` にメッセージが送信された後にのみ呼び出され、その後削除されます。

### `ipcMain.removeListener(channel, listener)`

* `channel` String
* `listener` Function

指定した `channel` の listener 配列から、指定した `listener` を削除します。

### `ipcMain.removeAllListeners([channel])`

* `channel` String

指定した `channel` の listener を全て削除します。

## イベントオブジェクト

`callback` に渡される `event` オブジェクトには以下のメソッドがあります。

### `event.frameId`

An `Integer` representing the ID of the renderer frame that sent this message.

### `event.returnValue`

同期メッセージでの戻り値をこれにセットします。

### `event.sender`

メッセージを送った `webContents` を返します。`event.sender.send` を呼ぶことで、非同期メッセージに返信できます。より詳しくは [webContents.send](web-contents.md#contentssendchannel-arg1-arg2-) を参照して下さい。

### `event.reply`

A function that will send an IPC message to the renderer frane that sent the original message that you are currently handling. You should use this method to "reply" to the sent message in order to guaruntee the reply will go to the correct process and frame.