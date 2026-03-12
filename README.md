# htmx getting started さわってみよう htmx

## 背景となるモチベーション

わたしは静的HTMLだけでできた旧式なwebサイトの管理運用を職務上担当している。現代的なソフトウェア開発技法を用いてそのサイトを実装し直したいと企んでいる。

所与の条件がある

1. TypeScript言語を使う
2. Node + npm + npxではなくbunを使う
3. ExpressではなくHonoを使う
4. クライアント・サイドをできるだけ軽量にしたい。そのためwebページを実装するのにReactではなく、HonoのJSXでサーバー・サイドでレンダリングする(SSR)を使う
5. SSRを主としつつも、それをはみ出すユーザー要求がある。ブラウザ上で会話的操作をするちょっとしたWeb UIを実装したい。お知らせ記事を投稿してサイトに追加するUIを追加したい。

会話的なWeb UIを実装するためにReactを使うしかないのかなと思っていた。それを教えてくれる本も読んだ。しかしわたしはReactに気乗りがしない。実行時の動作が重くて、これでOKとは思えない。Web UIを実現するのにもっと軽快なテクノロジーはないものか？と迷っていた。Reactは習うべきことが多くてしんどいし。

そうこうするうち２０２６年３月、Web記事 [gihyo.jp 「HTMLを拡張し⁠⁠、JSなしで動的UIを作るhtmx」, 嶌田喬行（しまだたかゆき）](https://gihyo.jp/article/2026/02/htmx-introduction) を読んだ。この記事の冒頭にhtmxを使ったwebページのサンプルが紹介されていた。

```
<button hx-get="/hello" hx-target="#result">
  読み込み
</button>
<div id="result">ここに結果が表示されます</div>
```

この通りのHTTPサーバアプリをbunとHono JSXで作ってみた。`my-app`ディレクトリにcdして`bun run dev` したら `http://localhost:3000` がするりと動いてしまった。とてもいい感じだ。

![img](https://gihyo.jp/assets/images/article/2026/02/htmx-introduction/hello.gif)

よおし、htmxをもっと学ぼうとわたしの気分は高まった。そこですぐ思った。

*htmxで作ったHTTPサーバアプリケーションをPlaywrightを使ってE2Eテストしたい、ただしbunとHonoとJSXにhtmxを組み合わせるという条件のもとで。*

こういう課題の解決方法を教えてくれるWeb記事を探したけれど見当たらなかった。しょうがない、ネットから情報を集めて動くコード一式を自作しよう。

## 解決すべき問題

Playwrightはbunの上ではまともに動かない、PlaywrightはNodeを前提しているから、という記事があった。

- [browserstack, How to Use Bun for Playwright Tests, ](https://www.browserstack.com/guide/bun-playwright)

本当にそうなのか？と私はPlaywrightのサンプルコードを打ち込んでbunの上で試してみた。たしかに動かなかった。エラーメッセージが表示されるが、それを読んでも何がどう悪いのかよく分からなかった。手に負えない感じだった。困った。

## 解決方法

"bun playwright"をキーにググったら別のweb記事を見つけた。

- [Running playwright on fly.io with bun, Stephen Haney](https://stephenhaney.com/2024/playwright-on-fly-io-with-bun/)

この記事は`npx playwright test`コマンドを使わない。
TypeScriptコード `src/main.ts` を書き、`bun run src/main.ts` とコマンドで実行する。`main.ts` は `playwright-chromium` というJavaScriptライブラリをimportする。

`playwright-chromium`ライブラリはPlaywrightプロジェクトが開発したものだ。これを使えばJest流の単体テストがアダプタ層を介してChromiumブラウザを立ち上げ、URLをGETし、応答によってできたDOMをアクセスできるようにしてくれる。`npx playwright test` コマンドが王道だが、それをさておき裏口から入ってPlaywrightプロジェクトの成果物をimportして利用するという道もアリなのだ。このようにわたしは理解した。

Browserstackの記事が言うようにPlaywrightプロジェクトがNode依存なところがあるという説はたぶん正しい。しかし `playwright-chromium` ライブラリはNodeへ依存していなくて、bunの上でもちゃんと動作するんじゃなかろうか。そういう希望を持った。試してみる価値があると思う。

## 説明

TODO

