# ♻️Vue.js 3講座 受講メモ（２）

<https://nssol.udemy.com/course/vue-js-complete-guide/learn/lecture/42563942#overview>

## Vue.jsを使ってみる
  
### createApp()から始めよう

- indec.htmlのscriptタグの中でmain.jsが実行されているよ、main.jsにvueを書いてみよう
- まず、名前付きインポート文を使ってvueから提供される「createApp」をゲットしよう
  - 関数にもなっているので、`createApp()`を呼び出すことが、アプリケーション作りの第一歩である
  - ただ、vueはコンポーネントをいろいろ配置してこそアプリケーションになるため、、こいつにも引数としてコンポーネントを渡してあげる必要アリ
  - コンポーネント、それはすなわちビューファイル
  - たとえばApp.jsを作ったとして、`import App from './App.vue'`としてデフォルトインポート関数として扱えるようになる
  - `createApp(App)` としたとして、引数に指定されたコンポーネント元にメイン機能！UIを作るのだ
  - vue.jsの心臓部分なんだねえ、絶対呼び出そう！！
  - ただ、インターフェイスを作るまでで、作ったものを表示する処理は特にしていない…
    - 表示の処理には戻り値を使う、`const app = createApp(App)`てな感じで
    - `app.mount()`で晴れてインターフェイスの表示ができる！
    - どこの表示するか？はさらに引数でCSSを設定しよう
    - ちょうど index.htmlでdivタグにid=appがあるので、ここに挿入する形で使ってみるか
    - `app.mount(''#app)`となるよ、元の記述は上書きされちゃうよ

### App.vueを整えてみよう

- その中身は「script」「template」「stale」の３ブロック！
  - 「script」の中にはjavascriptが書けるし、
  - 「template」の中にはHTMLが書けるぜ
  - 「stale」の中にはCSSが書ける！
- template以外は任意、必ずしもフルセット必要ではない
  - ただ、templateとscriptをうまく組み合わせるのがキモなのさ

#### 組み合わせて表示させてみる
  
```ts
    <script>
     const title = 'Vue.js Course'
     let price = 9.99
    </script>
    
    <template>
     <h1>Title: {{ title }}</h1>
     <h2>Price: ${{ price - 1 }}</h2>
    </template> 
```

- 画面に「Title: Vue.js Course」「Price: $8.99」と表示される！

#### クリック処理してみる
  
```ts
    <script>
    let price = 9.99
    
    function increment(){
      price += 1
      console.log(price)
     }
    </script>
    
    <template>
      <h1>Title: {{ title }}</h1>
      <h2>Price: ${{ price - 1 }}</h2>    
      <button @click="increment">button</button>
    </template> 
```

- 画面のボタン押下時に、「increment()」が実行される！
- ただ、コンソール内でpriceが足しこまれても、再代入されていないので画面に表示されるPriceは更新されないよ
- javaScriptの動きとしては、そりゃそうなんだけど…イマイチ直感的ではない
- 紐づく変数もリアクティビティに更新させる機能が、vue.jsにはあるんです

### ref()を使ってリアクティビティに

- まずは`import { ref } from 'vue'`をインポートするべし
- 次に、`let price = ref(9.99)`として、返り値をこれからは使いましょう
- これで、データ更新後に自動で再レンダリングがされるぞ！
- が、javascriptには本来存在しない動きのため、priceはオブジェクトとなる
  - いや普通に数値で欲しいんだけど！という時は`price.value`として数値で取得もできる、便利ね～
  - 計算に使用するときも`price.value =+ 1`って感じにするよ
  - ただ、テンプレート側では.valueはつけなくてもＯＫ…内部的にチェックされているのでむしろつけちゃダメ
- オブジェクト自体を引数に入れることも可能、こんな感じで

```ts
    <script>
          const info = ref({
           students: 1000,
           rating: 4
          })
          
          console.log(info.value.students)
    </script>

    <template>
      <h2>Students: {{info.students }}</h2>    
    </template> 
```

- ただ、オブジェクトを再度オブジェクトにするような動きで二度手間チック
- そこでもう一つ方法があるのです

### reactive()を使ってもリアクティビティに

- ref()を使った場合、技術的な問題で必ずオブジェクトになっていたが…オブジェクトはおのままリアクティブにできないのか？
- 出来るのだ、`import { reactive } from 'vue'`をインポートするべし、valueを使う必要がないよ

 ```ts
    <script>
       function increment(){
        instructor.bio = 'hi'
        instructor.sns.twitter = 'hello'
       }     
       
          const instructor = reactive({
           name: 'Yoshipi',
           age: 25,
           sns: {
            twiiter: '@__Yoshipi__',
            youtube: '@Yoshipi',
           }
          })
          
       instructor.bio = 'hello'
       console.log(instructor.age)
    </script>

    <template>
      <h2>Instructor age: {{instructor.age }}</h2>    
      <h2>Instructor bio : {{instructor.bio }}</h2>    
      <h2>Instructor sns: {{instructor.sns.twiiter }}</h2>    
     <button @click="increment">button</button>
    </template> 
```

- ボタン押下で中身も変わる！
- ちなみに、reactive()の中でref()も使える。その場合は、valueは不要！
- 例外として、配列の場合はvalueを書く必要アリ
- ただ…**公式でもref()関数が推奨されているので、そっち使ったほうがいいかもしれない！**

### 普通のオブジェクトの中に、ref()を使うパターンもある

- こんな感じで出来るが、今度はtemplate内でちゃんとvalueつけないと怒られるので注意
- vueが内部で見てくれているのは先頭だけだから、いい感じにならないのだ
  - いままではrefであればtamplateで判断してvalueつけてたんだけど、今回は普通のオブジェクトだからね

```ts
    <script>
          const instructor = reactive({
           name: ref('Yoshipi'),
           age: ref(25),
          })
          console.log(instructor.age.value)          
    </script>

    <template>
      <h2>Instructor age: {{instructor.age.value }}</h2>    
    </template> 
```
