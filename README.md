# Vue3 讀書會

---
## 1- Vue的核心實體 Creating a Vue Application/template syntax

* ### 宣告式、指令式和Vue.js MVVM模式
**指令式渲染(Imperative)**   
以前熱門的jQuery都是直接操作Dom來操控畫面，也稱為指令式的渲染，優點是直觀、操作簡單，但是當需求越來越多，程式也會越來越多行，造成難以維護的窘境。

像是以前我們針對以下html進行畫面更新時，一個指令一個動作加入事件監聽器達成(event listener)。

```
<h1 id='message'></h1>
<input name='meassage' type='text'/>

<script>
document.querySelctor('[name='message']').addEventListener('input',(event)=>{
    document.querySelctor('[name='message']').innerText = event.target.value
})
<script/>
```
**宣告式渲染(Declarative)**   

注重封裝處理後的結果，將命令式邏輯封裝成API暴露給開發者，使使用者更專注在資料更新和綁定上。

如下圖所示Vue是一個以**MVVM(model、viewModel、view)模型**，與一般我們認識的MVC架構中的Model對應DB資料庫有所不同，這裡Model指的是前端我們定義的資料，viewModel則包含我們引用創建的Vue實例，裡面包含著一些資料更新邏輯，view則對應到真正網頁上畫面的DOM。

![mvvm](https://hackmd.io/_uploads/BJdjZ3Ysa.png)
圖片來源: https://012.vuejs.org/guide/

* ### Vue的實例 (Vue Instance)
在Vue2 版本中我們會使用new Vue()來創建一個Vue實例，在Vue3版本中則改用createApp()來創建Vue的實例，可以使用CDN script方式引用，或透過npm安裝Vue套件。
```
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<script>
  const { createApp, ref } = Vue

  createApp({
    setup() {
      const message = ref('Hello vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```
https://vuejs.org/guide/quick-start.html
