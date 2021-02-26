![1556518852165](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\1556518852165.png)

```javascript
(/iphone|ipod|ipad/i.test(navigator.appVersion)) && document.addEventListener('blur', (e) => {
  ['input', 'textarea'].includes(e.target.localName) && document.body.scrollIntoView(false)
}, true)
```

