# MessageChannel()

## 实现窗口之间的传值

``` javascript
var channel = new MessageChannel();
var para = document.querySelector('p');

var ifr = document.querySelector('iframe');
var otherWindow = ifr.contentWindow;

ifr.addEventListener("load", iframeLoaded, false);

function iframeLoaded() {
  otherWindow.postMessage('Hello from the main page!', '*', [channel.port2]);
}

channel.port1.onmessage = handleMessage;
function handleMessage(e) {
  para.innerHTML = e.data;
}
```



## 实现对象深拷贝

``` javascript
const obj = { name: '张三', age: 20 };
const { port1, port2 } = new MessageChannel();
port1.postMessage(obj);
port2.onmessage = event => {
    console.log(event.data); // {name: '张三', age: 20}
    console.log(obj === event.data); // false
    console.log(JSON.stringify(obj) === JSON.stringify(event.data)); // true
};
```

