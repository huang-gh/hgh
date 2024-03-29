<template>
  <div class="app-container">
    <el-collapse v-model="activeNames">
      <el-collapse-item title="js 复制" name="1">
        <pre v-highlight>
          <code class="js">
              export const copyToClipboard = (str) => {
                  const el = document.createElement('textarea')
                  el.value = str
                  el.setAttribute('readonly', '')
                  el.style.position = 'absolute'
                  el.style.left = '-9999px'
                  document.body.appendChild(el)

                  const selected =
                    document.getSelection().rangeCount > 0
                      ? document.getSelection().getRangeAt(0)
                      : false

                  el.select()
                  document.execCommand('copy')
                  document.body.removeChild(el)

                  if (selected) {
                    document.getSelection().removeAllRanges()
                    document.getSelection().addRange(selected)
                  }
                }
          </code>
        </pre>
      </el-collapse-item>
      <el-collapse-item title="解析身份证信息 - 出生日期 - 性别 - 年龄" name="2">
        <pre v-highlight>
          <code class="js">
              /**
                * @description 解析身份证信息
                * @param {String} idCard - 身份证号
                * @param {Number} analyseType - 解析类型（birthDate-出生日期 sex-性别 age-年龄）
                * @return {String}
                */
                export function getAnalysisIdCard(idCard = '', analyseType) {
                  const analyseObj = {
                    'birthDate': (idCard) => {
                      // 获取出生日期
                      const birth = `${idCard.substring(6, 10)}-${idCard.substring(10, 12)}-${idCard.substring(12, 14)}`
                      return birth
                    },
                    'sex': (idCard) => {
                      // 获取性别
                      const sex = parseInt(idCard.substr(16, 1)) % 2 === 1 ? '男' : '女'
                      return sex
                    },
                    'age': (idCard) => {
                      // 获取年龄(计算周岁，未过今年的生日则不加上一岁)
                      const myDate = new Date()
                      const month = myDate.getMonth() + 1
                      const day = myDate.getDate()
                      let age = myDate.getFullYear() - idCard.substring(6, 10) - 1
                      if (idCard.substring(10, 12) < month || +(idCard.substring(10, 12)) === month && idCard.substring(12, 14) <= day) {
                        age++
                      }
                      return age
                    }
                  }
                  if (!analyseObj[analyseType]) {
                    throw new Error('请传入正确的解析类型！')
                  }
                  return analyseObj[analyseType](idCard)
                }

                export const openIdFn = (url, name = 'openId') => {
                if (!url.includes('openId') && !url.includes('yltUrl')) return false
                // 获取url参数

                return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) || ['', ''])[1].replace(/+/g, '%20')) || null
              }

          </code>
        </pre>
      </el-collapse-item>
      <el-collapse-item title="获取url参数" name="3">
        <pre v-highlight>
      <code class="js">
        export const openIdFn = (url, name = 'openId') => {
          if (!url.includes('openId') && !url.includes('yltUrl')) return false
          // 获取url参数
          return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) || ['', ''])[1].replace(/\+/g, '%20')) || null
        }
      </code>
    </pre>
      </el-collapse-item>
      <el-collapse-item title="处理文件流" name="4">
        <pre v-highlight>
     <code class="js">
        // 处理文件流
        getObjectURL(file) {
          let url = null
          if (window.createObjectURL !== undefined) { // basic
            url = window.createObjectURL(file)
          } else if (window.webkitURL !== undefined) { // webkit or chrome
            try {
              url = window.webkitURL.createObjectURL(file)
            } catch (error) {
              console.log(error)
            }
          } else if (window.URL !== undefined) { // mozilla(firefox)
            try {
              url = window.URL.createObjectURL(file)
            } catch (error) {
              console.log(error)
            }
          }
          return url
        },
     </code>
   </pre>
      </el-collapse-item>
      <el-collapse-item title="获取图形验证码" name="5">
        <pre v-highlight>
          <code class="js">
              // 获取图形验证码
              async handleverifyCode() {
              try {
                  const data = await verifyCode()
                  this.isveryCoderule = false
                  const captchaimg = 'data:image/png;base64,' + btoa( // 解决base64 乱码问题
                  new Uint8Array(data).reduce((data, byte) => data + String.fromCharCode(byte), '')
                  )
                  this.formConfig[1].btnImg = captchaimg
              } catch (error) {
                  console.log(error)
              }
          </code>
      </pre>
      </el-collapse-item>
      <el-collapse-item title="防抖 - 节流" name="6">
        <pre v-highlight>
          <code class="js">
              // 防抖案例：窗口resize停止才执行最终的函数
              function debounce(fn, wait, ...args) {
                  var that = this
                  fn.tId && clearTimeout(fn.tId)
                  fn.tId = setTimeout(function() {
                      fn.apply(that, args)
                  }, wait)
              }

              function handle(e) {
                  console.log('resize end event')
                  console.log(e) // Event{}
              }
              // 缺点：handle不能写成匿名函数，因为把tId存储在handle函数对象上。所以间接导致传递参数e较为复杂
              window.onresize = function(e) { debounce(handle, 1000, e) }

              // 改进版
              // 思路： 用闭包把tId存储起来
              function debounce(fn, wait) {
                  var tId
                  return function() {
                      var that = this
                      var args = arguments
                      tId && clearTimeout(tId)
                      tId = setTimeout(function() {
                          fn.apply(that, args)
                      }, wait)
                  }
              }
              function handle(e) {
                  console.log('resize end event')
                  console.log(e) // Event{}
              }
              window.onresize = debounce(handle, 1000)
                

                // 节流案例： 不停scroll时，滚动条每隔100ms固定频率执行函数
                function throttle(fn, wait) {
                    var cur = new Date()
                    fn.last = fn.last || 0
                    if (cur - fn.last > wait) {
                        fn.call()
                        fn.last = cur
                    }
                }

                function handle() {
                    console.log('scroll event')
                }
                window.onscroll = function() { throttle(handle, 100) }

                // 或者
                function throttle(fn, wait) {
                    var canRun = true // 标记是否可继续执行
                    return function() {
                        var that = this
                        var args = arguments
                        if (!canRun) return
                        canRun = false
                        setTimeout(function() {
                            fn.apply(that, args)
                            canRun = true
                        }, wait)
                    }
                }

          </code>
      </pre>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  name: 'WhJs',
  data() {
    return {
      activeNames: ['1']
    }
  }
}
</script>
<style lang='scss' scoped>
</style>
