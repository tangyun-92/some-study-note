在日常开发中，我们可能会遇到导出excel的情况，而后端此时给我们返回的是一个文件流，需要前端将文件流转为url地址进行下载。

可以将这个方法封装成一个工具类，方便其他地方调用，我这里放到了utils.js里面
```js
import axios from 'axios'
import Vue form 'vue'

/**
 * 根据后端返回的文件流转为excel导出
 * @param {Object} data
 */
export function exportExcelMethod(data) {
  axios({
    method: data.method,
    url: `${data.url}${data.params ? '?' + data.params : ''}`,
    responseType: 'blob',
    headers: {
      token: data.token
    },
    timeout: 8000,
    data: data.data
  })
    .then((res) => {
      const link = document.createElement('a')
      const blob = new Blob([res.data], {
        type: 'application/vnd.ms-excel'
      })
      link.style.display = 'none'
      link.href = URL.createObjectURL(blob)

      // link.download = res.headers['content-disposition'] //下载后文件名
      link.download = data.fileName //下载的文件名
      document.body.appendChild(link)
      link.click()
      document.body.removeChild(link)
      Vue.prototype.$message.success('导出成功！')
    })
    .catch((error) => {
      Vue.prototype.$message.error('参数错误！')
    })
}
```

```vue
<el-button type="primary" plain size="small" @click="exportLog">导出</el-button>
```

```js
import { exportExcelMethod } from '@/utils'

exportLog() {
    const url = this.baseUrl
    const data = this.data
    const myObj = {
      method: 'post',
      url: url,
      fileName: 'xxx日志',
      token: this.token,
      data: { data }
    }
    exportExcelMethod(myObj)
  }
```
