# 1 メモ

## 1.1 Terminal Command

## 1.1.1 React create

- normal setup
npx create-react-app file-name
cd [file-name]
code .
npx start

- firebase環境へのdeploy
  
1. npm run build
2. firebase deploy

- vite setup
npm install --save-dev vite @vitejs/plugin-react

npm run build

npm run preview  

## 1.1.2 Library install

### 1.1.2.1 Router download

npm i react-router-dom

## 1.1.2.3 Bootstrap

npm install react-bootstrap bootstrap

## 1.1.1.2.3 BoxIcons

npm install boxicons --save

## 1.1.2.4 framer-motion

npm i framer-motion

<!-- ドキュメント -->
<!-- https://reffect.co.jp/react/framer-motion -->
<!-- https://www.framer.com/motion/ -->

## 1.1.2.5 Lenis

慣性スクロールを実現する  

npm i framer-motion @studio-freight/lenis

## 1.1.2.6 Typescript

npm install @types/react @types/react-dom

## 1.1.2.7 Material UI

npm install @mui/material @emotion/react @emotion/styled

<!-- ドキュメント -->
[https://mui.com/material-ui/getting-started/installation/]

## 1.1.2.8 Material Symbols

Googleが提供するフォントパッケージ

npm install material-symbols@latest

### 1.1.3 Firebase

初期化設定  

firebase init

- What do you want to use as your public directory?
  →build(※実際にfirebase上で公開するフォルダがbuildフォルダのため)
- Configure as single-page app
  → Yes

---

## 1.2 JS

let mainImage = document.querySelector(".carousel-item");
let subImage = [...document.querySelectorAll(".image")];

//ある特定の位置を超えると作動する関数
let setItemActive = (entries) => {
  entries.map((entry) => {
    if (entry.isIntersecting) {
      entry.target.classList.add("active-1");
    }
  });
};

//具体的にいつ発動させるのかを決めるオプション
let options = {
  rootMargin: "0px", //デフォルトで０.marginとほぼ同じ。
  threshold: 0.5, //閾値は0.2。これが１になると完全に画面におさまってから発動する
};

//監視
let observer = new IntersectionObserver(setItemActive, options);
observer.observe(mainImage);

//複数の.imageが読み込まれるたびに.active-1を付与する
subImage.map((image) => {
    observer.observe(image);
});

---

## 1.3 CSS

### 1.3.1 css フォルダ構成のベストプラクティス  

[https://kinsta.com/jp/blog/react-best-practices/]

alan2207/bulletproof-react/docs/project-structure.md
Lines 1 to 85 in master

#### 1.3.1.1  🗄️ Project Structure

Most of the code lives in the `src` folder and looks like this:

```sh
src
|
+-- assets            # assets folder can contain all the static files such as images, fonts, etc.
|
+-- components        # shared components used across the entire application
|
+-- config            # all the global configuration, env variables etc. get exported from here and used in the app
|
+-- features          # feature based modules
|
+-- hooks             # shared hooks used across the entire application
|
+-- lib               # re-exporting different libraries preconfigured for the application
|
+-- providers         # all of the application providers
|
+-- routes            # routes configuration
|
+-- stores            # global state stores
|
+-- test              # test utilities and mock server
|
+-- types             # base types used across the application
|
+-- utils             # shared utility functions
```

In order to scale the application in the easiest and most maintainable way, keep most of the code inside the `features` folder, which should contain different feature-based things. Every `feature` folder should contain domain specific code for a given feature. This will allow you to keep functionalities scoped to a feature and not mix its declarations with shared things. This is much easier to maintain than a flat folder structure with many files.

A feature could have the following structure:

```sh
src/features/awesome-feature
|
+-- api         # exported API request declarations and api hooks related to a specific feature
|
+-- assets      # assets folder can contain all the static files for a specific feature
|
+-- components  # components scoped to a specific feature
|
+-- hooks       # hooks scoped to a specific feature
|
+-- routes      # route components for a specific feature pages
|
+-- stores      # state stores for a specific feature
|
+-- types       # typescript types for TS specific feature domain
|
+-- utils       # utility functions for a specific feature
|
+-- index.ts    # entry point for the feature, it should serve as the public API of the given feature and exports everything that should be used outside the feature
```

Everything from a feature should be exported from the `index.ts` file which behaves as the public API of the feature.

You should import stuff from other features only by using:

`import {AwesomeComponent} from "@/features/awesome-feature"`

and not

`import {AwesomeComponent} from "@/features/awesome-feature/components/AwesomeComponent`

This can also be configured in the ESLint configuration to disallow the later import by the following rule:

```js
{
    rules: {
        'no-restricted-imports': [
            'error',
            {
                patterns: ['@/features/*/*'],
            },
        ],

    // ...rest of the configuration
}
```

This was inspired by how [NX](https://nx.dev/) handles libraries that are isolated but available to be used by the other modules. Think of a feature as a library or a module that is self-contained but can expose different parts to other features via its entry point.

### 1.3.2 コンポーネントライブラリ

- Material UI
- Ant-design
- React Bootstrap
- Semantic UI React

### 1.3.3 記載コツ

```css
.item1 {
  width: 200px;

  .item2: {
    width: 100px;

    .item3 {
      width: 20px;
    }
  }
}
```

出力結果

```css
.item1 {
  width: 200px;
}

.item1 .item2 {
  width: 100px;
}

.item1 .item2 .item3 {
  width: 20px
}

```

---

## 1.4 React

### 1.4.1 Reactの再レンダリングされるタイミング  

1. state が変更した場合  
2. propsが変化した場合  
3. 親コンポーネントが再レンダリングされた場合

### 1.4.2 サイトをより高速化する方法

1. useCallback と useMemo を組み合わせて使用する  
詳しい内容は調べる必要あり  

### 1.4.3 Redux のメモ

1. Action は型などどういう状態を維持するかなどを定義している  
2. Reducer はstateを変更する役割をしている(dispatch を使用してActionの型からstateの状態を読み取り更新)
3. operation はstateを変更する際のロジック的な役割を担っている

### 1.4.4 JSX

階層構造で記述を行う必要がある
階層構造を作れない(不要なdivタグなどを使用したくない)場合は<React.Fragment>や<>を使用する必要がある

### 1.4.5 フォルダ説明

src
・開発用ファイルが格納される
・ReactコンポーネントのJSXファイルなどはここに置く

public
・静的ファイルが格納される
・htmlファイルや画像ファイルなど

build
・本番用ファイルが格納される
・npm run buildコマンドで作成される

vite
いろんなフレームワークが使えるビルドツール

### 1.4.6 分割代入

[https://qiita.com/cha_ayu418/items/7fe607bec2b4acf8b8d3]

### 1.4.7 ReactHookの注意点

フックはトップレベルでしか呼び出せないので気を付ける

[https://qiita.com/tatsumin0206/items/4e1076e2deedf20a9485]

---

## 1.5 TypeScript

## 1.5.1 Eventの型まとめ  

[https://zenn.dev/kenta0313/articles/a39fb1d8edc3a4]

## 1.5.2 オプションパラメータ

[https://zenn.dev/oreo2990/articles/3d780560c5e552]

## 1.5.3 論理積、論理和

[https://zenn.dev/nrikiji/articles/914e13da25caed]

---

## 77 Headタグ

./public/index.htmlディレクトリで設定されている

---

## 88 メモ

### 88.1

- module.cssを使用したハイフンクラス名の指定の仕方  
className={styles['progress-bar']}

### 88.2 ulタグ

×  

```html
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
<ul>
```

〇

```html
ul>li * 5
```

### 88.3　SPA

React + Firebase + Stripe で実現する

---

## 99 参考URL
