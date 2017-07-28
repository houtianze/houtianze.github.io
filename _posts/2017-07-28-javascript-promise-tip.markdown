---
layout: post
title:  "Javascript Promise tips"
date:   2017-07-28 00:00:00
categories: javascrip promise async
---

After using [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) for a while, I realized the followings might be good to know:
- The `executor` function of a promise is **executed immediately** after creation. That means if you create a Promise object using `new`, the function you passed in (the `executor`) will run right after the `new` statement. [^1]<br>
For example, in the following codes:
~~~ javascript
var prom = new Promise((resolve, reject) => {
  console.log('Immediately')
  setTimeout(() => {
    resolve('whatever')
  }, 3000)
})
~~~

It will output "Immediately" immediately after the code is executed (the `executor` runs synchronously)

[^1]: https://stackoverflow.com/a/29963279/404271
