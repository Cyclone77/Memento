

# window.postMessage

> **window.postMessage()** 方法可以安全地实现跨源通信。通常，对于两个不同页面的脚本，只有当执行它们的页面位于具有相同的协议（通常为 https），端口号（443 为 https 的默认值），这两个脚本才能相互通信。—— [MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)

### 实际使用

案例：A 窗口通过 `window.open(...popup details...)` 打开的 B 窗口，这2个窗口之间的消息发送。

```javascript
/*
 * A 窗口的域名是<http://example.com:8080>，以下是 A 窗口的 script 标签下的代码：
 */

var popup = window.open(...popup details...);

// 假设当前页面没有改变 location，这条语句会成功添加 message 到发送队列中去（targetOrigin 设置对了）
popup.postMessage("hello there!", "http://example.org");

// 这里因为window.open()打开的窗口并不知道什么时候加载完成，处理方法是：A窗口也监听 message 事件，B窗口加载完成后，通过 postMessage 发送消息给 A窗口表示B窗口加载完成，A收到消息后，再进行后续的消息通信。

// 以上的消息发送是无效的

function receiveMessage(event)
{
  // 我们能相信信息的发送者吗？(也许这个发送者和我们最初打开的不是同一个页面).
  if (event.origin !== "http://example.org")
    return;

  // event.source 是我们通过 window.open 打开的弹出页面 popup
  // event.data 是 popup 发送给当前页面的消息 "page completed"，表示B窗口加载完成。
  if (event.data === 'page completed') {
      // 发送消息给B窗口正常收到
      popup.postMessage("The user is 'bob' and the password is 'secret'.", "http://example.org");
  }
}
window.addEventListener("message", receiveMessage, false);
```

B 窗口通过 `window.opener` 父窗口的引用给A 窗口发送消息表示 B 窗口加载完成。 

``` javascript
/*
 * 弹出页 popup 域名是<http://example.org>，以下是 script 标签中的代码：
 */

//当 A 页面 postMessage 被调用后，这个 function 被 addEventListener 调用
const receiveMessage = event => {
    // 我们能信任信息来源吗？
    if (event.origin !== 'http://example.com:8080') return;

    // event.source 就当前弹出页的来源页面
    // event.data 是 "The user is 'bob' and the password is 'secret'."

    // 假设你已经验证了所受到信息的 origin (任何时候你都应该这样做), 一个很方便的方式就是把 event.source
    // 作为回信的对象，并且把 event.origin 作为 targetOrigin
};

window.addEventListener('message', receiveMessage, false);

// B窗口加载完成后发送消息给 A窗口表示可以进行消息通信了。
window.opener.postMessage('page completed', 'http://example.com:8080');
```



