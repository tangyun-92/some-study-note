#### 1、初始化
首先新建一个 tailor-img 文件夹，接着执行 npm init -y 初始化一个package.json

#### 2、安装相关插件
- archiver  压缩文件
- canvas  裁剪图片
- glob  批量获取路径
npm i  archiver canvas glob --save

#### 3、app.js
```js
const fs = require('fs')
const { basename } = require('path')
// 压缩文件
const archiver = require('archiver')
// canvas库，用于裁剪图片
const { createCanvas, loadImage } = require('canvas')
// 批量获取路径
const glob = require('glob')
const config = require('./config')

// 根据宽高获取配置
function getOptions(options, config) {
  const [sourceWidth, sourceHeight] = options
  const { width, height, isWidth, isHeight, scale } = config
  const haveWidth = [width, (sourceHeight * width * scale) / sourceWidth]
  const haveHeight = [(sourceWidth * height * scale) / sourceHeight, height]
  if (width === 0 || height === 0) {
    return [0, 0]
  }
  if (width && height) {
    if (isWidth) {
      return haveWidth
    }
    if (isHeight) {
      return haveHeight
    }
    return [width / scale, height / scale]
  }
  if (width && !height) {
    return haveWidth
  }
  if (height && !width) {
    return haveHeight
  }
  return options.map((item) => item / scale)
}

!(async () => {
  const paths = glob.sync('./images/*')
  // 压缩成zip
  const archive = archiver('zip', {
    zlib: {
      level: 9,
    },
  })
  // 输出到当前文件夹下的 image-resize.zip
  const output = fs.createWriteStream(__dirname + '/image-resize.zip')
  archive.pipe(output)
  for (let i = 0; i < paths.length; i++) {
    const path = paths[i]
    const image = await loadImage(path)
    const { width, height } = image

    // 由于使用了扩展运算符展开对象，这里需要为对象定义迭代器
    const obj = { width, height }
    obj[Symbol.iterator] = function () {
      return {
        next: function () {
          let objArr = Reflect.ownKeys(obj)
          if (this.index < objArr.length - 1) {
            let key = objArr[this.index]
            this.index++
            return { value: obj[key] }
          } else {
            return { done: true }
          }
        },
        index: 0,
      }
    }
    // 默认缩放2倍
    // const options = [width, height].map((item) => item / 2)
    const options = getOptions(obj, config)
    const canvas = createCanvas(...options)
    const ctx = canvas.getContext('2d')
    ctx.drawImage(image, 0, 0, ...options)
    archive.append(canvas.toBuffer(), { name: `${basename(path)}` })
  }
})()
```

#### 4、config.js用于修改宽高等配置
```js
module.exports = {
  width: 300,
  height: '',
  // 根据宽度等比缩放，优先级更高
  isWidth: true,
  // 根据高度等比缩放
  isHeight: false,
  // 宽高整体缩放
  scale: 1,
}
```
