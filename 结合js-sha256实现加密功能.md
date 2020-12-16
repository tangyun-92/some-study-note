在日常开发中，我们登录注册时，可能会对登录密码进行加密，一般我们会使用md5加密

但是近些年md5太容易被破解，这里结合js-sha256插件，写了个工具函数，对密码进行加密，下面上代码

### 1、安装 js-sha256
```js
yarn add js-sha256
```

### 2、封装encrypt-utils.js工具函数
```js
export function stringToByte(str) {
  var bytes = []
  var len, c
  len = str.length
  for (var i = 0; i < len; i++) {
    c = str.charCodeAt(i)
    if (c >= 0x010000 && c <= 0x10ffff) {
      bytes.push(((c >> 18) & 0x07) | 0xf0)
      bytes.push(((c >> 12) & 0x3f) | 0x80)
      bytes.push(((c >> 6) & 0x3f) | 0x80)
      bytes.push((c & 0x3f) | 0x80)
    } else if (c >= 0x000800 && c <= 0x00ffff) {
      bytes.push(((c >> 12) & 0x0f) | 0xe0)
      bytes.push(((c >> 6) & 0x3f) | 0x80)
      bytes.push((c & 0x3f) | 0x80)
    } else if (c >= 0x000080 && c <= 0x0007ff) {
      bytes.push(((c >> 6) & 0x1f) | 0xc0)
      bytes.push((c & 0x3f) | 0x80)
    } else {
      bytes.push(c & 0xff)
    }
  }
  return bytes
}

export function hexToBytes(hex) {
  for (var bytes = [], c = 0; c < hex.length; c += 2) {
    bytes.push(parseInt(hex.substr(c, 2), 16))
  }
  return bytes
}

export function bytesToBase64(bytes) {
  return base64ArrayBuffer(bytes)
}

export function base64ArrayBuffer(arrayBuffer) {
  var base64 = ''
  var encodings =
    'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'

  var bytes = new Uint8Array(arrayBuffer)
  var byteLength = bytes.byteLength
  var byteRemainder = byteLength % 3
  var mainLength = byteLength - byteRemainder

  var a, b, c, d
  var chunk

  // Main loop deals with bytes in chunks of 3
  for (var i = 0; i < mainLength; i = i + 3) {
    // Combine the three bytes into a single integer
    chunk = (bytes[i] << 16) | (bytes[i + 1] << 8) | bytes[i + 2]

    // Use bitmasks to extract 6-bit segments from the triplet
    a = (chunk & 16515072) >> 18 // 16515072 = (2^6 - 1) << 18
    b = (chunk & 258048) >> 12 // 258048   = (2^6 - 1) << 12
    c = (chunk & 4032) >> 6 // 4032     = (2^6 - 1) << 6
    d = chunk & 63 // 63       = 2^6 - 1

    // Convert the raw binary segments to the appropriate ASCII encoding
    base64 += encodings[a] + encodings[b] + encodings[c] + encodings[d]
  }

  // Deal with the remaining bytes and padding
  if (byteRemainder === 1) {
    chunk = bytes[mainLength]

    a = (chunk & 252) >> 2 // 252 = (2^6 - 1) << 2

    // Set the 4 least significant bits to zero
    b = (chunk & 3) << 4 // 3   = 2^2 - 1

    base64 += encodings[a] + encodings[b] + '=='
  } else if (byteRemainder === 2) {
    chunk = (bytes[mainLength] << 8) | bytes[mainLength + 1]

    a = (chunk & 64512) >> 10 // 64512 = (2^6 - 1) << 10
    b = (chunk & 1008) >> 4 // 1008  = (2^6 - 1) << 4

    // Set the 2 least significant bits to zero
    c = (chunk & 15) << 2 // 15    = 2^4 - 1

    base64 += encodings[a] + encodings[b] + encodings[c] + '='
  }

  return base64
}
```

### 3、在页面中使用
```js
import { bytesToBase64 } from '@/utils/encrypt-utils'
import { sha256 } from 'js-sha256'

const hash = sha256.create()
hash.update('SHA-256')
hash.update(this.loginForm.password) // 当前传入后端的密码

this.$refs.ruleForm.validate(valid => {
	if (!valid) {
    	return false
    } else {
    	login({
        	...this.loginForm,
       		password: bytesToBase64(hash.array()) // 调用方法
        }).then(res => {
        	...
        })
    }
})
```
