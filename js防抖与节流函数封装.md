
### 函数防抖
短时间内多次触发同一事件，只执行最后一次，或者只执行最开始的一次，中间的不执行。
```js
/**
 * @param {Function} fn 目标函数
 * @param {Number} time 延迟执行毫秒数
 * @param {Boolean} immediate true - 立即执行 false - 延迟执行
 * @description 防抖函数
 */
export function debounce(fn, time, immediate = true) {
  let timer
  return function() {
    const that = this
    const args = arguments

    if (timer) clearTimeout(timer)
    if (immediate) {
      const callNow = !timer
      timer = setTimeout(() => {
        timer = null
      }, time)
      if (callNow) {
        fn.apply(that, args)
      }
    } else {
      timer = setTimeout(() => {
        fn.apply
      }, time)
    }
  }
}
```

### 函数节流
连续触发事件但是在 n 秒中只执行一次函数。即 2n 秒内执行 2 次，节流如字面意思，会稀释函数的执行频率。
```js
/**
 * @param {Function} fn 目标函数
 * @param {Number} time 延迟执行毫秒数
 * @param {Boolean} type 1-立即执行，2-不立即执行
 * @description 节流函数
 */
function throttle(fn, time, type) {
  let previous = 0
  let timeout
  return function() {
    let that = this
    let args = arguments
    if (type === 1) {
        let now = Date.now()
 
        if (now - previous > time) {
          fn.apply(that, args)
          previous = now
        }
    } else if (type === 2) {
      if (!timeout) {
        timeout = setTimeout(() => {
          timeout = null
          fn.apply(that, args)
        }, time)
      }
    }
  }
}
```
