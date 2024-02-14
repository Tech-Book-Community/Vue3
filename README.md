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

![mvvm](https://github.com/yhosutun2490/Vue3_Tech/assets/71853581/f56db56d-eea8-4830-8309-9cfa221254b9)
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

// ES module
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```
https://vuejs.org/guide/quick-start.html

* ### 理解createApp

https://book.vue.tw/CH1/1-2-instance.html

通常我們安裝好Vue後，會出現一個App.vue檔案，裡面是所謂的**SFC(Single File Component, SFC)** 檔案結構，通常開發時所有的頁面組件或是元件會匯入至App.vue這隻檔案,透過createApp()編譯模板，再利用mount()掛載至真正網頁某一段DOM元素容器中(通常是index.html中 id = app)。

```
import { createApp } from 'vue'

const app = createApp({
  /* root component options */
})

// 最後你會看到app.mount
app.mount('#app')

```
```
// SFC example
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

* ### 深入Vue渲染過程
https://vuejs.org/guide/extras/rendering-mechanism.html

前面章節提到我們可以用SFC檔案中撰寫類似HTML template，並在<script>標籤中撰寫資料變數或使用Vue響應式API等，然後透過Vue3 提供的 **createApp()和mount()** 方法，掛載至真正網頁上DOM達成畫面更新。  

不難想像Vue框架其中應該透過某些轉換機制，最後呼叫innerText、innerHTML等瀏覽器才有的操作DOM API去更新。

Vue SFC檔案(template、script和style標籤)通常需要經過 **編譯(compile)**，最後變成一包JS物件利用Vue 提供的**渲染函式(render function)** 產生**虛擬DOM (Virtual DOM)**，最後掛載入真正的網頁DOM中。

我們可以歸納整理出Vue初次選染畫面其中的幾個步驟:
* 編譯-compile
* 渲染函數-render
* 虛擬DOM (Virtual DOM)
* 掛載入真實網頁上 - mount to Real DOM

<img width="688" alt="render-pipeline sMZx_5WY" src="https://github.com/yhosutun2490/Vue3_Tech/assets/71853581/984a0057-2971-4636-9a07-78eef957b61c">

圖片來源: https://vuejs.org/guide/extras/rendering-mechanism.html

#### SFC compiler 編譯過程簡介
常常利用Vue SFC檔案開發，可以在template裡撰寫類似HTML的形式開發畫面HTML結構，應該常常想我是在寫真正的HTML嗎?，我們用下列程式碼來思考一下:
                            
在SFC檔案中我們可以用**雙括號綁定資料變數、@click Vue提供的事件語法糖等，或者用一些JS表達式來計算程式**都是可以的，不過原生HTML似乎沒有這種寫法。                          
```
<template>
 <button @click="count++">
  {{ count }}
  {{ conut ++ }}
 </button>
</template>
```
確實這些在SFC檔案確實有一段Vue插件`@vue/compiler-sfc`負責編譯過程，轉成一段帶有描述屬性的JS物件，稱之為**Vnode**  
https://vue-js.com/learn-vue/complie/#_3-%E6%95%B4%E4%BD%93%E6%B8%B2%E6%9F%93%E6%B5%81%E7%A8%8B
```
const vnode = {
  type: 'div',
  props: {
    id: 'hello'
  },
  children: [
    /* more vnodes */
  ]
}
```  
https://vuejs.org/guide/extras/rendering-mechanism.html#virtual-dom   
                            
讓我們來理解官方文件對Vnode和virtual DOM的關係解釋:

**同樣的所產生的Vnode仍然為一段JS物件，並且為virtual DOM組成的最小單位**。
                            
> Here, **vnode is a plain JavaScript object (a "virtual node")** representing a element. It contains all the information that we need to create the actual element. It also contains more children vnodes, **which makes it the root of a virtual DOM tree**.

**這裡也可以了解到Vue其實也有引入現代框架Virutal DOM概念，簡單來說模板編譯完成後會形成Virtual DOM Tree，框架會比較前後差異後，用最小幅度有效率地去操作網頁真實DOM的更新**。

![渲染過程](https://github.com/yhosutun2490/Vue3_Tech/assets/71853581/9f7093e7-610a-45b0-9eca-9bda627ae0b8)

圖片來源:　https://vue-js.com/learn-vue/complie/#_3-%E6%95%B4%E4%BD%93%E6%B8%B2%E6%9F%93%E6%B5%81%E7%A8%8B

在學習其他框架像React等一定也聽過**比較differ**或**協調reconciliation**這類專有名詞，是發生在當有資料或模板更新時新舊Virtual DOM比較過程。

### Vue3 生命週期 setup()
https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram

本章是初次理解Vue createApp()使用，不帶入太多生命週期函數使用細節，我們用宏觀方式理解新的**compositon API setup()** 到底在執行什麼事:
* 首先引入Vue創建實例後，我們在SFC檔案裡面可能會定義一些資料表定ref()、reactive()等，這些是包在Vue實例下的方法，我們必須等它創建完實例後才能使用，這是setup()生命週期函式基本用法。
* 之後SFC檔案中的tempalte、script標籤才進入編譯，告訴渲染函式我該形成怎樣的vnode大節點物件，描述什麼樣的特徵 ex: 資料綁定、遇到那些vue提供的方法等。

![lifecycle DLmSwRQE](https://github.com/yhosutun2490/Vue3_Tech/assets/71853581/b06de896-46b4-4c4c-86fb-77b5732be526)

圖片來源: https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram

小疑問:
上圖中Has pre-compiled template路徑是什麼?  
https://vuejs.org/guide/extras/render-function.html#vnodes-must-be-unique   
因為template模板需要經過編譯，而Vue也提供vnode產生函數，能夠直接創建vnode。
```
import { ref, h } from 'vue'

export default {
  props: {
    /* ... */
  },
  setup(props) {
    const count = ref(1)

    // return the render function
    return () => h('div', props.msg + count.value)
  }
}
```                     
              
### 補充-Vue3 新增的模板編譯優化更新:
https://cn.vuejs.org/guide/extras/rendering-mechanism#compiler-informed-virtual-dom

一般來說Virtual DOM都是忠實呈現前後樣板或資料流差異的比對去進行更新，而Vue 針對compiler過程進行了一些模板template標記，畢竟重新渲染時一律創建新舊Virtual DOM仍舊會對記憶體造成一些空間上耗損。
* 靜態提升- 對於雙括號綁定的資料變數視作可能更新的node
```
<div>
  <div>foo</div> <!-- 需提升 -->
  <div>bar</div> <!-- 需提升 -->
  <div>{{ dynamic }}</div>
</div>
```
* 編譯後vnode打平- DOM tree結構打平，比起槽狀結構對於比較搜尋會更有效率
```
<div> <!-- root block -->
  <div>...</div>         <!-- 不会追踪 -->
  <div :id="id"></div>   <!-- 要追踪 -->
  <div>                  <!-- 不会追踪 -->
    <div>{{ bar }}</div> <!-- 要追踪 -->
  </div>
</div>
div (block root)
- div 带有 :id 绑定
- div 带有 {{ bar }} 绑定
```


                            






