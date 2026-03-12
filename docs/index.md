# htmxで作られたWebページをPlaywrightでE2Eテストする方法(ただしnodeではなくbunの上で)

## 背景

わたしは静的HTMLだけでできた旧式なwebサイトの管理運用を職務上担当している。現代的なソフトウェア開発技法を用いてそのサイトを実装し直したいと企んでいる。

所与の条件がある

1.  TypeScript言語を使う

2.  Node + npm + npxではなくbunを使う

3.  ExpressではなくHonoを使う

4.  クライアント・サイドをできるだけ軽量にしたい。そのためwebページを実装するのにReactではなく、HonoのJSXを使ってサーバー・サイドでレンダリングする(SSR)をおもに使う

5.  ブラウザからお知らせ記事を投稿してサイトに追加するためのUIを実現したい。これをSSRだけで実現するのは無理だ。

会話的なWeb UIを実装するためにReactを使うしかないのかなと思っていた。そのノウハウを教えてくれる本も読んだ。しかしわたしはReactに気乗りがしなかった。わたしが目標とするwebサイトにReactはあまり適合的でなかった。もっと軽い方法はないものか？と迷っていた。そうこうするうち２０２６年３月、Web記事 [gihyo.jp 「HTMLを拡張し⁠⁠、JSなしで動的UIを作るhtmx」, 嶌田喬行（しまだたかゆき）](https://gihyo.jp/article/2026/02/htmx-introduction) を読んだ。この記事の冒頭にhtmxを使ったwebページのサンプルが紹介されていた。

    <button hx-get="/hello" hx-target="#result">
      読み込み
    </button>
    <div id="result">ここに結果が表示されます</div>

この通りのHTTPサーバアプリをbunとHono JSXで作ってみた。`` my-app`ディレクトリにcdして`bun run dev `` したら `http://localhost:3000` がするりと動いてしまった。とてもいい感じだ。

<figure>
<img src="https://gihyo.jp/assets/images/article/2026/02/htmx-introduction/hello.gif" alt="hello" />
</figure>

よおし、htmxをもっと学ぼうとわたしの気分は高まったが、すぐ課題が一つあることに気づいた。

**htmxで作ったHTTPサーバアプリケーションをPlaywrightを使ってE2Eテストしたい、ただしbunとHonoとJSXにhtmxを組み合わせるという条件のもとで。**

こういう課題の解決方法を教えてくれるWeb記事を探したけれど見当たらなかった。しょうがない、ネットから情報を集めて動くコード一式を自作しよう。

## 解決すべき問題

Playwrightはbunの上ではまともに動かない、PlaywrightはNodeを前提しているから、という記事があった。

- [browserstack, How to Use Bun for Playwright Tests](https://www.browserstack.com/guide/bun-playwright)

本当にそうなのか？と私はPlaywrightのサンプルコードを打ち込んでbunの上で試してみた。たしかに動かなかった。エラーメッセージが表示されるが、それを読んでも何がどう悪いのかよく分からなかった。手に負えない感じだった。困った。

## 解決方法

"bun playwright"をキーにググったら別のweb記事を見つけた。

- \[Running playwright on fly.io with bun, Stephen Haney\](<https://stephenhaney.com/2024/playwright-on-fly-io-with-bun/>)

この記事はテストを実行するのに\`npx playwright test\`コマンドを使わない。TypeScriptコード `src/main.ts` を書く。`main.ts` は `playwright-chromium` というJavaScriptライブラリをimportする。そして\`bun run src/main.ts\` というコマンドで実行する。

`` playwright-chromium`ライブラリはPlaywrightプロジェクトが開発したものだ。これを使えばJest流の単体テストがアダプタ層を介してChromiumブラウザを立ち上げ、URLをGETし、応答によってできたDOMをアクセスできるようにしてくれる。Playwrightの入門記事を読むと `npx playwright test `` コマンドを使えと書いてある。それが王道だ。だがさておくとしよう。Playwrightプロジェクトの成果物をimportして利用するという裏口入学もアリなのだ。この記事はその方法を示している。

Browserstackの記事が言うようにPlaywrightプロジェクトがNode依存なところがあるという説はたぶん正しい。しかし `playwright-chromium` ライブラリはNode依存ではなくて、bunの上でもちゃんと動作するんじゃなかろうか。試してみる価値があると思う。

## 説明

### わたしの環境

- macOS Tahoe 26.3.1 with GNU bash 5.3.3

- \[bun\](<https://bun.com/docs/installation>) 1.3.6

- \[Hono\](<https://hono.dev/docs/getting-started/basic>) ^4.12.5

### サンプルコードのGit Hubレポジトリ
