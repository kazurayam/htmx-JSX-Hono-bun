# htmx getting started さわってみよう htmx

## 背景となるモチベーション

[青森県眼科医会ホームページ](https://www.aomori-gankaikai.jp/)を現代的なソフトウェア開発技法を用いて実装し直したいと kazurayam は企んでいます。

所与の条件がある

1. TypeScript言語を使う
2. Nodeではなくbunを使う
3. npmではなくHonoを使う
4. クライアント・サイドをできるだけ軽量にしたい。そのためwebページを実装するのにReactではなくHonoのJSXでサーバー・サイドでレンダリングするのを主にしたい。
5. ブラウザ上で会話的操作をするちょっとしたWeb UIを実装したい。お知らせ記事を投稿してサイトに追加するUIを追加したい。

会話的なUIを実装するためにReactを使うしかないのかなと思っていた。しかしわたしはReactに気乗りがしない。習うべきことが多くてしんどい。実行時の動作が重くてうんざりする。Web UIを実現するのにもっと軽快なテクノロジーはないものか？とモヤモヤしていた。

２０２６年３月、[htmx](https://gihyo.jp/article/2026/02/htmx-introduction) を知った。なんだか良さそうだ。学習しようと思う。

Web記事 [gihyo.jp 「HTMLを拡張し⁠⁠、JSなしで動的UIを作るhtmx」, 嶌田喬行（しまだたかゆき）](https://gihyo.jp/article/2026/02/htmx-introduction) の冒頭にhtmxを使ったwebページのサンプルが紹介されていた。

```
<button hx-get="/hello" hx-target="#result">
  読み込み
</button>
<div id="result">ここに結果が表示されます</div>
```

この通りのHTTPサーバアプリをbunとHono JSXで作ってみた。`my-app`ディレクトリにcdして`bun run dev` したら `http://localhost:3000` が立ち上がっスルッと動いてしまった。とてもいい感じだ。

![img](https://gihyo.jp/assets/images/article/2026/02/htmx-introduction/hello.gif)

よおし、htmxをもっと学ぼうと高まった。そこで私は思った。htmxで作ったHTTPサーバアプリケーションをPlaywrightを使ってE2Eテストしたい、ただしbunとHonoとJSXにhtmxを組み合わせるという条件のもとで。

こういう課題の解決方法を丸っと教えてくれるWeb記事は見当たらなかった。しょうがない、コード一式を自作しよう。ネットから情報を集めればなんとかなるだろう。やりましょう。

## 解決すべき問題

bunの上でPlaywrightはNodeを前提に開発された経緯があり、bunの上でPlaywrightはまともに動かない、という記事があった。

- [browserstack, How to Use Bun for Playwright Tests, ](https://www.browserstack.com/guide/bun-playwright)

本当にそうなのか？と半信半疑で私はPlaywrightのサンプルコードを打ち込んでbunの上で試してみたが、動かなかった。エラーメッセージが表示されるが、それを読んでも何がどう悪いのかよく分からなかった。手に負えない感じだった。困った。

## 解決方法

"bun playwright"をキーにググったら別のweb記事を見つけた。

- [Running playwright on fly.io with bun, Stephen Haney](https://stephenhaney.com/2024/playwright-on-fly-io-with-bun/)

この記事は`npx playwright test`コマンドを使わない。 `playwright-chromium`というJavaScriptライブラリをimportするTypeScriptコード `src/main.ts` を書き、`bun run src/main.ts`とやって実行する。`playwright-chromium`ライブラリはPlaywrightプロジェクトが開発したものだ。これを使えばJest流の単体テストがアダプタ層を介してChromiumブラウザを立ち上げ、URLをGETし、応答によってできたDOMにアクセスすることができる。`npx playwright test`コマンドを使わすに、別の入口からPlaywrightの成果物を利用することができる、というふうにわたしは理解した。

Browserstackの記事が言うようにPlaywrightプロジェクトがNode依存なところがあるという説はたぶん正しい。しかし `playwright-chromium` ライブラリはNodeへ依存していなくて、bunの上でもちゃんと動作するんじゃなかろうか。そういう希望を持った。試してみる価値があると思う。

## 説明

TODO

