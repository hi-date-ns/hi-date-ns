# ♻️Vue.js 3講座 受講メモ（１）

https://nssol.udemy.com/course/vue-js-complete-guide/learn/lecture/42563942#overview

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
  
```
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

```
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
   - 計算に使用するときも`price.value =+= 1`って感じにするよ
   - ただ、テンプレート側では.valueはつけなくてもＯＫ…内部的に内科医チェックされているため！なのでむしろつけちゃダメ
- オブジェクト自体を引数に入れることも可能、こんな感じで

 ```
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

### reactive()を使ってもリアクティビティに！

- ref()を使った場合、技術的な問題で必ずオブジェクトになっていたが…オブジェクトはおのままリアクティブにできないのか？
- 出来るのだ、`import { reactive } from 'vue'`をインポートするべし、valueを使う必要がないよ


 ```
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

```
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

-------------------------

## templateについてお詳しく

### テンプレート構文

- htmlが記載できるが、ビルド後結局jsに最適化されるので…その時分かりやすくなるようなヒントとしてhtml記述が使われている！
  - ゆえにフレームワークなのだなあ
- てなわけで厳密にhtmlではないので、テンプレート構文といったりする
- `{{}}`の中には式もかける、三項演算子参考絵が描けるのが便利（単一の指揮しか書けないからね）
- トップレベル（グローバルな感じ）で定義されてる変数しかつけないのも注意！

### ディレクティブ

- htmlとはちょっと離れて、テンプレート構文特有の記載方法
- `v-`から始まる属性がそれ！
- たとえばcount変数があるとき、`<div v-text="count"></div>`ってやるのと`{{count}}`ってやるのは同じ表示なんだけど、二重波括弧があるから普通に使わないらしい
- 記載ルール的には二重波括弧と同じ

#### v-html

- スクリプトにhtmlタグを入れた変数があるとする、テンプレートで使おうにも二重波括弧だと文字で表示されてしまう
- これをちゃんとタグしたいときに、、使える！！
- ただ、悪意のある使い方ができるので、危険な技（XSS攻撃）
  - 必ず、ユーザーからもらったデータ、ではない信頼データのみを指定するようにしよう 

#### v-bind

- 次に、スクリプトにURLを入れた変数があるとする、その場合はhref属性内に二重波括弧だとうまく遷移できない！
- hrefが{{url}}ってURLだとそのまま認識するからね仕方ないよね
- そういう時は`<a v-bind:href="url">Vue.js</a>`で解決です
- hrefに限らず、属性に対いては何でも使えるよ、`<a v-bind:id="id " v-bind:href="url">Vue.js</a>`といった感じで…
- なので！基本的には**文字列内では二重波括弧**、属性値に対しては**v-bind**を使う
- が、非常によく使われるので省略の書き方のがもはや推奨されている！`<a :id="id"  :href="url">Vue.js</a>`みたいに
  - さらに、属性名と変数名が同じなら省略して書いたって良い、`<a :id  :href="url">Vue.js</a>`
- 動的だったり、とりあえず文字列じゃない意味を持たせたい時はコレ使おう

##### booleanに対してのv-bind

- null、undefinedをv-bindに設定すると、属性が消えてしまう！
- ところでhtmlにはboolean属性というものがある、値をとらなくてもいい属性のことだね
  - `<button disabled>`みたいな書いてあるだけで効果発揮する属性のこと
  - こいつらに対してv-bindでfalseを指定すると、その属性は消えてしまう！`<button :disabled="false">`みたいに

##### v-bindで複数の属性も付けられる

- その場合はコロンではなく、イコールキーバリューで書いてみるのだ
- `<a v-bind="{id: id, href: url}">Vue.js</a>`といった感じで。ウーン、すっきり！

#### v-on

- イベント時に処理したいときに使うのが`v-on`、コロンの後にハンドラーを設定する！
- こんな感じで、 `<button v-on:click="count++">button</button>`クリックしたらカウントプラス！
- これももちろん人気なので省略記法あり、「@」を使うのだ！
  - `<button @click="count++">button</button>` となる、これも公式で推奨されてるよ
  - こんな感じで直接処理を書くのはインラインハンドラーといいます
  - 関数自体も設定できるぜ、その場合はメソッドハンドラーというよ
  - イベントはいろいろあるからドキュメントを見てみてね

#### イベントオブジェクト

- イベントの詳細を取りたい時があるかもしれない
  - どゆこと？って感じだが…イベント発生すると、内部的にイベントの情報が詰まったイベントオブジェクトが生成されているのです
  - vue.jsはそれを取得できるのさ
  - 例えば、クリックした位置（y軸、x軸）などが取得できるのだ、なんか便利そうだった！
- メソッドハンドラですと`function CountUp(event)`って感じで第一引数入れとけば勝手に入ってくるよ
  - さっきの例だと`event.clientX`みたいな感じで取得できるぜ
- 上記の例はすべてメソッドハンドラーですが…インラインハンドラーではどう書くのか？？
  - ダラー変数、「$」を使えば可能だ！`<button @click="count = $event.clientX">button</button>`  で同じ動きとなる

##### ハンドラーに渡すか、引数…

- メソッドハンドラを使っていれば、関数に引数を渡したい時もあるだろう
- そういう時は、逆に？！メソッドハンドラーとして関数を呼び出せばいいのである。
  - メソッドの引数にeventがある場合は、渡す側でもしっかり指定してあげるべし
  - `<button @click="countUp($event, 1)">button</button>`てな感じで
  
##### イベント修飾子

- 「preventDefauli」「stopProgation」っていうよく使うイベントがあるよ
  - `event.preventDefauli`がくっつくと 、デフォルトの挙動がなくなってしまうのだ！リンク飛ばなくなるとかね
    - バリデーションチェック用に使われたりするよ
  - `event.stopProgation` はイベントの伝播を止めてくれるよ
   - 例えば、親の<click>に<button @click>が挟まれていたとして、buttonが動くとclickも動くのが普通だが…それを伝えず、clickを動かさないのがコイツだ
- これらはよく使われるので、もっと簡単に実行できる仕組み…イベント修飾子がある！
 - 「preventDefauli」であれば、`<a href="htps://~" @click.prevent="">`でOK
 - 「stopProgation」であれば、`<a href="htps://~" @click.stop="">`でOK
 - イベント修飾子は「 .」でどんどん繋げちゃってもいいよ

##### キー修飾子

- キー操作のイベントだけで使える修飾子
- 例えば`<iinput type="text" @keyup="count++">`みたいにやるとキー入力したときにハンドラーが実行されるが…
  - 特定のキーでしか動かしたくない時もあるだろう、そこで使うのが！キー修飾子
  - 同じくイベント名の後ろにドットを付けるのだ
  - `<iinput type="text" @keyup.space.delete="count++">`でスペースキー＆デリートキーだけ反応

### ディレクティブの構造

- v-とかイベントとか修飾子とか色々出てきたけど…整理してみよう
- 一番長くて「v-on:click.prevent="changeDate"」これはそれぞれ名前がついている！
  - `v-on`は名前
  - `click`は引数  
  - `prevent`は修飾子
  - `changeDate`は値
  
#### ディレクティブバインディング

- 引数にscriptの変数を使いたい！時があるかもしれない、
- その時は各括弧を扱う、「[変数]」で動的に引数を指定できるよ

```
     <script>
     	    const eventName = 'keyup'
    </script>

    <template>
		<input type="text" @[eventName].space.delete="count++" />
    </template> 
```

- 例はv-onなので@が付いてるが、v-bind等なんでも同じように使えるよ

#### v-model

- 逆に？インプットで入力された内容をスクリプトで使うこともできる
- 実はめっちゃ簡単、`v-model`で値を指定すると、同盟の変数と常に内容がリンクする！

```
     <script>
     	    const userInput = ref('')
    </script>

    <template>
		<input v-model="userInuput" type="text" />
    </template> 
```

### 一つにまとめろcomputed！

- リアクティブシステムを保ったまま処理を一つにまとめる方法
- なんのこっちゃですので…例えば以下のコードは、クリック数が3超えたらGoodそれ以外はBadを表示するもの

```
     <script>
     	    const score = ref(0)
    </script>

    <template>
		<p>{{ score > 3 ? 'Good' : 'Bad' }}</p>
		<p>{{ score }}</p>
		<button @click="score++">+1</button>
    </template> 
```

- ちょと長い、一つにまとめられたらいいのだが…まるごと定数にするとかどう？
 - しかし`const evaluation = score.value > 3 ? 'Good' : 'Bad'`とかやっても、評価された結果が入るだけなので意味はない
 - リアクティブなまま式すっきりさせる方法はもうないのか、、？？いや！ある、それが`computed`だ！
 - インポートして使おう、関数を引数に取るので、こんな感じになるぞ

```
     <script>
     	    const evaluation =　computed((value) => {
     	    　return  score.value > 3 ? 'Good' : 'Bad'`
     	    })
    </script>

    <template>
		<p>{{ evaluation }}</p>
		<button @click="score++">+1</button>
    </template> 
```

- 内部的には、computedは常に内部のデータを監視し変更があればその都度関数を再実行してくれているということだ
- ちなみに引数valueには、前回実行時の戻り値が入っているぞ！ 
  - 使わなければ引数取らなくてもＯＫ 
  
#### computedの注意点

- computed内でデータのセットはできない！読み取り専用であり、処理をまとめるものなのだ
- サイドエフェクト（副作用）も避けたほうがいい、外側の状態が変更されるような処理さ
  - もちろんタイマー系とか非同期の処理もだめよ

### watchEffect

- 監視って言葉が出たところで…何かが変更されたら何かの処理をする機能って便利だよね
- その機能に特化したのが「watch」「watchEffect」、それぞれインポートして使おう
- watchEffectも関数を引数に持つのだが、、computed同じく、内部のデータを監視している
- 内部のデータっていうのは`conxolw.log(count)`のレベルで対象になるよ
  - computedとほとんど同じ動きの理解でOK
  - ただ、こっちは非同期処理も入れてもよいぞ

### watch
 
- こちらは引数を二つ取るぞ！
  - 第一引数は、監視したいリアクティブなデータを入れる
    - もうrefオブジェクトになってるやつにしてね
  - 第二引数は、監視しているデータが更新s慣れた時に実行したい処理を関数で書く！
  - これだと、countが更新されたらコンソールログが出る感じ

```
     <script>
     	    watch(count, () => {
     		    console.log('watch')
     	    })
    </script>
```

- watchEffectとの違いは、明示的に監視したいデータを指定するかどうか
  - さらに、watchは最初にすぐ実行されるが、watchの関数はデータが更新されたときに動くとか
  - が、 第三実行に`immediate:true`をセットするとwatchEffectと同じになるらしい 
- 複数の関数を監視したい場合は配列を使うといいぞ
- ちなみにwatchの関数のほうだが、第一引数に監視しているデータの最新の値、第二引数には変更前の値が入る

### class属性

- 例えば、以下のように書いたらHelloの背景は青、文字は赤が一気に適用されるわけだ
- v-bindで書いたとしても同じこと、、扱いづらいよな！
- そこで！！class属性に対しては属性値に文字列以外を指定する方法があるのだ！

```
     <template>
         <div :class="'red bg-blue'">Hello</div>
    </template>

     <style>
     	    .red{
     	    	color: red;
     	    )
     	    .bg-blue{
     	    	background-color: blue;
     	    )
    </style>
```

- オブジェクトにして、キーにはクラス、値にはtrueかfallseを書けばいいのです、こりゃいいね！
- ハイフンが含まれる場合は括弧で囲むべし

```
     <template>
         <div :class="{ red: true, 'bg-blue': true}">Hello</div>
    </template>
```

- 値をリアクティブなデータにしたってOK

```
     <script>
         const isRed = ref(true)
         const isBgBlue = ref(true)

         function toggleClass(){
         	isRed.value
         }
    </script>
    
     <template>
         <div :class="{ red: isRed, 'bg-blue': isBgBlue}">Hello</div>
         <button @click="toggleClass">toggle</button>
    </template>
```

- こうすると、ボタンを押すたびにスタイルが切り替わるぞ！
- 配列を使うとキー自体も変えられたりする、` <div :class="[className, 'bg-blue']">Hello</div>`みたいに。すごい改造だあ
- しかも混ぜることもできる、` <div :class="[className, { 'bg-blue'：true}]">Hello</div>`

### style属性

- も同じようにオブジェクトで指定できるよ
- `<div :style="{ color: 'red', 'background-color: 'blue'' }">`みたいにね
  - ちなみにケバブケースではなく、キャメルケースにしてもオッケー！backgroundColorみたいに
  - 推奨されているのはキャメルケース🐫

-------------------------

## 条件付きレンダリングとリストレンダリング

### v-if

- 条件付きでレンダリングさせたりさせなかったりする
- テンプレート内で`<p v-if="true">OK</p>`で、カンタン
- elseの動きが欲しければ、続けて`<p v-else-if="true">maybe OK</p>`やら`<p v-else>not OK</p>`を書けばよい
  - 必ずくっつけて記載しよう！（要素は異なってもOK、divとかね）
    
#### テンプレートごと操作せよ

- まとめて全部消したい時、一個ずつ要素にv-if付けたら出来はするが…めんどくさい
  - 考えられる解決案に`<div v-if="ok">`使う、、こともできるが
  - 複数要素まとめて条件付きレンダリングしたいだけなので、無駄なdivタグになってしまう
  - そこで！！divタグの代わりにテンプレート構文タグを使うのだ
    - v-if系と一緒にテンプレートタグを使った場合、vue.jsが存在しないタグとして消してくれるのよ

```
     <template>
	     <template v-if='false'>
	         <div :class="{ red: isRed, 'bg-blue': isBgBlue}">Hello</div>
	     </template>
    </template>
```

### v-show

- v-ifと似ているのが、`<p v-show="true">OK</p>`
- 何が違うのかというと、、要素の消し方に違いがある！
  - v-ifはfalseの時、レンダリング後のソースから消える 
  - v-showがfalseの時、レンダリング後のソースから消えない！表面上だけ消してくれる
  - どっちを使えばいいのかというと、値の条件（true/false）が高い頻度で変わる場合は`v-show`のほうが良い
    - いちいち要素を消さないため、結果的に早いのだ
    - 逆に`v-if`の時に初期値がfalseであれば必要な時に初めて表示されるため、高頻度でなければ、それはそれで効率も良い
  - あと、 v-showではテンプレートタグ使えないので注意！（やるならdiv）

### v-for

- `v-for = "変数 in 配列名"`で配列の各要素が簡単に描画できるぞ！

```
     <script>
         const fruits = ref({'Apple', 'Banana', 'Grape'})
    </script>
    
     <template>
         <li v-for="fruit in fruits"> {{ fruit }}</li>
    </template>
```

- しかし、このままだとキー属性を使え！と怒られてしまう…なんのことか？
  - vue.js特有で、キー属性というものがあるのだ
  - 内部的にレンダリングする際のヒント用に存在する
  - もっというと、 リストの一個一個を識別するためだ（デフォルトだと勝手に効率のいい動きにされる）
  -  `<li v-for="fruit in fruits" :key="fruit" > {{ fruit }}</li>`
    - キーには変数が使用可能
      - 配列の中に重複があるとかぶってしまうのでバグるぞ、必ずユニークになるように！ 
      - が、配列をオブジェクトにしてid持たせれば安全、`<li v-for="fruit in fruits" :key="fruit.id" > {{ fruit.name }}</li>`てきな
    - 静的なデータだと全部同じ値になってしまうためv-bind(:) を使っているよ
- ちなみにinの代わりにofも使えるよ、というプチ情報
  
#### v-forのインデックスを取得せよ

- ループが今何回目か知りたい時があるかもしれない
- 変数を丸括弧で囲えばおっけーよ
- `<li v-for="(fruit, index) in fruits" :key="fruit.id" > {{ fruit.name }} {{ index}}</li>`てきな

#### v-forで分割代入せよ

- オブジェクトが変数に入る場合、分割代入ができるのだ
- 毎回オブジェクトを入れずとも `<li v-for="{id, name } in fruits" :key="id" > {{ name }}</li>`でOK

#### テンプレートタグにv-forを使用せよ

- 例えば、複数の要素をまとめてループさせたいが、divタグを作りたくない時に効果的

```
    <template>
	    <template v-for="({id, name}, index) in fruits" :key="id">
	        <p>id: {{ id }} /p>
	        <input type="text" />{{ id }}{{ index}} 
	    </template>
    </template>
```

- v-ifと同時に使った場合、v-ifが先に作られるため、forの中身は使用不可
  - 基本的に、一緒に使うのはやめましょう

#### 配列以外にもv-forは適用できる

- オブジェクトの場合、も同じく`v-for = "変数 in オブジェクト名"`でOK
  - `<p v-for="(value, key, index) in user" :key="value" > {{ key }}: {{value}} ( {{index}} )</p>`みたいに色々取得できる
- 数字を入れることもできるよ
 - `<p v-for="n in 10" :key="n" > {{ n }}</p>`で何週目かを取得できる、1スタートなので注意

## コンポーネントはこう使う！

- ここまでは、コンポーネント自体の書き方をやってきたが…
- vue.jsのアプリケーションはコンポーネントがツリー上になって構成されている！…とは？？

### コンポーネントツリー

- まず、コンポーネントの繋がりは木構造（コンポーネントツリー）
- 一番上をルートコンポーネント
  - 最初のほうでやった、`const app = createApp(App)`に入れる「App」の部分だね
- とあるコンポーネントの一つ上は親、下は子と呼ぶ

### コンポーネントのつなげ方

- 木構造なので「App.vue」 の下にコンポーネントを作ってみよう、例えば「CountUp.vue」
- つなげる第一歩として、App.vueの中でCountUp.vueをインポートして、テンプレート内でHTMLタグとして書くのだ

```
     <script setup>
         import CountUp from './CountUp.vue'
     </script>
    
     <template>
         <CountUp />
    </template>
```

- これでCountUpコンポーネントがApp内に埋め込まれ、まるまるタグが「Count.vue」のUIと置き換わるのだ！
  - コンポーネントは複数書いても良い、その分埋め込まれるよ＝使いまわしやすいということ
- ちなみにしれっと出てきたsetupだが、ビジネスロジックはすべて<script setup>に書く理解で一回OKとする…

### コンポーネントの名づけ方

- vue.jsのドキュメントではパスカルケースを推奨している！（大文字はじまり＋キャメルケース）
  - 普通のHTMLのタグと異なることがぱっとみ分かりやすいぞ 
- また、名前は２単語以上にすべき！HTMLタグは基本1単語なので被る可能性もある

### コンポーネントのグローバル登録

- グローバル登録することで、 インポートしなくても使えるようになるのだ
- たとえば「BaseIcon.vue」があるとする、これをあらゆるコンポーネントで使いたい！
  - 毎回インポートするのは若干面倒なので、、「main.js」 で以下のように書けばOK
  
```
     const app CreateApp(App)
     app.component('BaseIcon', BaseIcon)
     app.mount('#app')
```

- この例だと`<BaseIcon />`で全コンポーネントで使えるようになるぞ！
- 便利だけど、単体でvue.jsみると急になんだ！？ってなるので若干わかりづらい
  - 保守的にいうと微妙なので、基本は一個一個インポートを推奨！ 
  
### コンポーネントのまとめ方

- 一般的には、「components」フォルダに「App.vue」以外をすべて入れることが多い
- ちなみにviteの設定フォルダに書いておけば`@/components/CountUp.vue`で、@が「src」フォルダを意味してくれる

### コンポーネントに属性を渡したら

- 子コンポーネントのテンプレート直下で、外側のタグが一つだけ（<div>~</div>とか）であれば、、
  - そのタグに、親呼び出し時にくっつけられた属性が継承される！
  - 被った場合は親コンポーネント優先だが、classとstyleだけは両方の値が合体される
  - 子コンポーネントがボタンの時、親で v-onくっつけても子の動きに継承される
- 一つのタグじゃない場合は、一切適用されないよ
  - が、`v-bind="$attrs"`ってやれば明示的に継承できたりもする
  
### styleタグの動きについても詳しくみよう

- CSSはほかのコンポーネントから使うことができる！
  - ていうのは、親コンポーネントで書かれているCSSは子コンポーネントで使用できるということだ
  - もっというと、一個のコンポーネントで書かれているCSSはどこでも自由に使えてしまう
  - …では、コンポーネント内でしか使えないCSSを書く方法はないのか！？
  - そこで使えるのがスコープ属性
- `<style scoped>`とやれば、外部のコンポーネントに適用されず、内部だけで使用できるようになるのだ
  - 基本、scopedはつけましょう
  
#### Viteの機能でグローバルなstyle

- styleにscopedつけないでもいいけど、viteの機能でもできるよ
  - まずグローバルに適用させたいCSSファイルを「assets」フォルダとかに用意する
  - それをmain.jsでfrom書かずにインポートする、`import './assets/main.css'`的な感じでやればOK！
  
### Vue Devtools

- っていうのがあるよ、画面とエディタを行き来できるから使ってみよう
- 開発画面下のアイコンから遷移可能！

-------------------------

## 親子間のコンポーネントでデータを受け渡そう

### props

- 例えば、親で定義したデータを子コンポーネントで使いたい…すなわち親→子でデータを渡したい場合
  - 子コンポーネントで**関数defineProps**を使い、それ用の属性名を設定しよう
    - 例えば `<script setup>const props = defineProps{['foo', 'bar']}</script>`
    - そしたら子のテンプレート内でfooを表示させる場所を用意する
    - 後は親コンポーネントでfooを子コンポーネントに、v-bindすればOK
      - `<ShowCount v-bind="{foo: 0, bar: ''hello}">`みたいに 
- propsはリードオンリーの為、子コンポーネントから代入はできない、親から受け取るだけのもの（一方通行！）
  - ゆえに読みやすく、保守性も高くて安全！
- propsのの分割代入も可能、 `<script setup>const { foo } = defineProps{['foo']}</script>`
  
#### バリデーション

- propsに対してバリデーションを設定しよう
- そのために、definePropsに設定するのは配列ではなく、オブジェクトにする！
  - 例えば 型が設定できるので、不正な値を防ぐことができる！

```
     <script setup>
         defineProps({
         foo:{
        	 type: Number, // 型
        	 required: false, //必須項目
        	 default: 100 //必須ではない場合のデフォルト値（設定がない場合undefinedになる、bolleanだけはfalse）
         },
         
         bar:{
        	 type:[Number, String, Boolean、Array, Object, 自作クラス…] ,  // 配列で複数指定可能
        	 default: function(){
        	// 関数が即座に実行され、返り値がデフォルトの値になる！
        	// デフォルトの値がオブジェクトor配列の時はこれを使わないといけない
        	 },
        	 validator(value){ // 独自のチェックを設定できる
        		return value === 0 || value === 1 
        	 }
         }
      })
     </script>
```

#### propsの命名規則

- 名前が複数単語になっている場合は、子側でキャメルケース使用するのが一般的
- が！親コンポーネントで属性として指定するときは、キャメルケースもいいけどケバブケースにしても大丈夫
  - そして、vue.jsをしてもケバブケースを推奨している（普通のhtmlの書き方と同じくね）
  
### emit

- 子から親に通信することができるよ
  - あくまでデータの流れは親→子の一方通行なので、propsほど自由なものではないのだが…
  - それでも通信できる手段があれば便利だよね、ってレベルのもの
- 例えば子のリセットボタンがクリックされたことを親に伝えられたら、親側でカウント0にできたり便利かもしれない
  - 子側でイベントのシステムを使うのだ！
  - コンポーネント内では関数「$emit()」が使用できるので、引数でイベント名を設定する
  - そうすることで、クリック時にイベントを発生させられるのだ！ 
    - たとえば`<button @click="$emit('reset')">Reset</button>`とすると、resetイベントが発生
    - そんでもって、親側で`<ResetButton @reset="count 0">`てやれば関連したイベントハンドラが作れる！
    - 仕組み的には、子側でカスタム独自イベントを発生させ、親でそのハンドラを起こすことで、結果子→親に伝えている
  - `$emit`はイベントと同時に、データも渡すことができる！
    - 親は`$event`で受け取れる、どう使うかは明示的に書くべし

#### difineEmit

- 関数difineEmitで、コンポーネントないでemitとする可能性のある文字列をまとめておける
  - なくてもいいのだが、明示的に書くことで発生するイベントが分かりやすくなる！
  - ので書いておこう
- スクリプトからイベントを笑みっとしたい場合は返り値を使うっていうのもあるけど、今はそこまでわからない！

#### emitするイベントの命名規則

- propsと同じく、キャメルケースでもケバブケースでもOK

-------------------------

## Pinia

- アプリケーションの規模が大きく、PJ全体で使うデータがある場合に便利！

### なぜ必要？

- コンポーネントツリーがあって、一番左側にあるデータを一番右側に持っていきたい時があるとする！
  - 今までのやり方で考えると、emit→emit→emit→props→props→propsみたいにデータを渡すしかない…面倒！
  - 解決策として、データをコンポーネントツリーとは別のところで管理して、そこを経由して渡す方法がある
  - シンプルな解決策だと、リアクティブオブジェクトを一つ外部のフォルダに置くとか
    - しかし！コードの流れを追いやすくするためにやっていたemit,propsルールに反してしまう
    - なので、もしチームで使うのならば皆が同じ方法で操作できるようにすべきだし、規約もしっかり決めねばならない
    - そこで使うのが、Pinia！

### Piniaを使ってデータを管理する

- グローバルなデータを管理する方法をStoreと呼ぶ
- Storeを複数持つことができる！
- そのうえで、Piniaでは一つのファイルに一つのStoreを定義する、という規約があるよ
  - 例えば、「stores」フォルダを用意して、カウンター管理用、人管理用、とそれぞれファイルを用意してみる
  - importでdifineStoreを呼び出して、一意になるストアの名称と、データ定義用の関数をセットしよう
  - 使いたい関数はreturnする
  - それをこんな感じでエクスポートする。カウンター管理用なら、「useカウンターStore」みたいな命名！
  
```
    import { difinrStore } from 'pinia'
    import { ref } from 'vue'
    
    export const useCounterStore = definesStore('counter', () =>{
    	couns count = ref(0)
    	const doubleCount = computed(() => count.value + 2) //computedも使える
    	return{
    		count: count
    	}
    })
```

### Piniaをコンポーネントで使ってみる

- まず、useCounterStoreをインポートしよう、エクスポートされているuseCounterStoreは関数になってるのでそのまま使用できる！
- 使う時は返り値を使おう、`const counterStore = useCounterStore()`という感じで
  - returnした値がそのまま入っていますよ、counterStore.countで使おう
  - 読み取り専用じゃないので中身変えてOK
  - ほかのコンポーネントでも同じデータが共用されるよ！
  - つまり、一つのデータをいろんなとこで使えるということさ、慎重に使おう

### store

- Storeの中で定義されたrefオブジェクトのことをstateと呼ぶ！
- Storeの中で定義されたcomputedオブジェクトのことはGettersと呼び、関数のことはActionsと呼ぶ
- なんであっても、必ずretunに返すオブジェクトに含めよう！

### コンポーネント以外の場所でも使えるよ
Dh!202412

-------------------------

## Vue.jsの内部構造

### 仮想DOMとは

- を使って レンダリングしている！！
- DOM自体はブラウザに伴っている機能のこと、HTMLを受け取ったら扱いやすい形に変換する動きのことをいう
- javaScriptはDOMを操作することで画面を更新しているのだ
- DOM自体は