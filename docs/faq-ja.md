# よくある質問

## 早く助けを求めるには？

1. ChatGPT / Bing / Baidu / Google などに尋ねてください。
2. オンラインの友達に聞く。背景情報と問題の詳細な説明を提供してください。質の高い質問ほど、有益な回答を得られる可能性が高くなります。

# デプロイメントに関する質問

## なぜ Docker のデプロイバージョンは常に更新を要求するのか

Docker のバージョンは安定版と同等であり、最新の Docker は常に最新のリリースバージョンと一致しています。現在、私たちのリリース頻度は1～2日に1回なので、Dockerのバージョンは常に最新のコミットから1～2日遅れており、これは予想されることです。

## Vercel での展開方法

1. GitHub アカウントを登録し、このプロジェクトをフォークする。
2. Vercel を登録し（携帯電話認証が必要、中国の番号でも可）、GitHub アカウントを接続する。
3. Vercel で新規プロジェクトを作成し、GitHub でフォークしたプロジェクトを選択し、必要な環境変数を入力し、デプロイを開始する。デプロイ後、Vercel が提供するドメインからプロジェクトにアクセスできます。(中国本土ではプロキシが必要)

- 中国で直接アクセスする必要がある場合: DNS プロバイダーで、cname.vercel-dns.com を指すドメイン名の CNAME レコードを追加します。その後、Vercel でドメインアクセスを設定してください。

## Vercel 環境変数の変更方法

- Vercel のコンソールページに入ります;
- askai-web プロジェクトを選択してください;
- ページ上部の設定オプションをクリックしてください;
- サイドバーで環境変数オプションを見つけます;
- 必要に応じて対応する値を変更してください。

## 環境変数 CODE とは何ですか？設定する必要がありますか？

カスタムアクセスパスワードです:

1. 設定しないで、環境変数を削除する。この時、誰でもあなたのプロジェクトにアクセスすることができます。
2. プロジェクトをデプロイするときに、環境変数 CODE を設定する（カンマ区切りで複数のパスワードをサポート）。アクセスパスワードを設定した後、ユーザーはそれを使用するために設定ページでアクセスパスワードを入力する必要があります。[関連手順](https://github.com/sinhong2011/askai-web#access-password)

## なぜ私がデプロイしたバージョンにはストリーミングレスポンスがないのでしょうか？

> 関連する議論: [#386](https://github.com/sinhong2011/askai-web/issues/386)

nginx のリバースプロキシを使っている場合、設定ファイルに以下のコードを追加する必要があります:

```
# キャッシュなし、ストリーミング出力をサポート
proxy_cache off;  # キャッシュをオフにする
proxy_buffering off;  # プロキシバッファリングをオフにする
chunked_transfer_encoding on;  # チャンク転送エンコーディングをオンにする
tcp_nopush on;  # TCP NOPUSH オプションをオンにし、Nagleアルゴリズムを無効にする
tcp_nodelay on;  # TCP NODELAY オプションをオンにし、遅延ACKアルゴリズムを無効にする
keepalive_timeout 300;  # keep-alive のタイムアウトを 65 秒に設定する
```

netlify でデプロイしている場合、この問題はまだ解決待ちです。

## デプロイしましたが、アクセスできません。

以下の問題を確認し、トラブルシューティングを行ってください:

- サービスは開始されていますか？
- ポートは正しくマッピングされていますか？
- ファイアウォールのポートは開いていますか？
- サーバーへのルートは問題ありませんか？
- ドメイン名は正しく解決されていますか？

## "Error: Loading CSS chunk xxx failed..." と表示されることがあります。

Next.js では、最初のホワイトスクリーンの時間を短縮するために、デフォルトでチャンキングを有効にしています。技術的な詳細はこちらをご覧ください:

- https://nextjs.org/docs/app/building-your-application/optimizing/lazy-loading
- https://stackoverflow.com/questions/55993890/how-can-i-disable-chunkcode-splitting-with-webpack4
- https://github.com/vercel/next.js/issues/38507
- https://stackoverflow.com/questions/55993890/how-can-i-disable-chunkcode-splitting-with-webpack4

ただし、Next.js は古いブラウザとの互換性に制限があるため、このエラーが発生することがあります。

ビルド時にチャンキングを無効にすることができます。

Vercel プラットフォームの場合は、環境変数に `DISABLE_CHUNK=1` を追加して再デプロイします。
セルフデプロイのプロジェクトでは、ビルド時に `DISABLE_CHUNK=1 pnpm build` を使用することができます。
Docker ユーザーの場合、ビルドはパッケージング時にすでに完了しているため、この機能を無効にすることは現在サポートされていません。

この機能を無効にすると、ユーザーの最初の訪問時にすべてのリソースがロードされることに注意してください。その結果、ユーザーのネットワーク接続が悪い場合、ホワイト・スクリーンの時間が長くなり、ユーザーエクスペリエンスに影響を与える可能性があります。この点を考慮の上、ご判断ください。

# 使用法に関する質問

## なぜいつも "An error occurred, please try again later" と表示されるのですか？

様々な原因が考えられますので、以下の項目を順番にチェックしてみてください:

- まず、コードのバージョンが最新版かどうかを確認し、最新版にアップデートしてから再試行してください;
- api キーが正しく設定されているか確認してください。環境変数名は大文字とアンダースコアでなければなりません;
- api キーが使用可能かどうか確認する;
- 上記のステップを踏んでも問題が解決しない場合は、issue エリアに新しい issue を投稿し、vercel のランタイムログまたは docker のランタイムログを添付してください。

## ChatGPT の返信が文字化けするのはなぜですか？

設定画面-機種設定の中に `temperature` という項目があります。この値が 1 より大きい場合、返信が文字化けすることがあります。1 以内に調整してください。

## 設定ページでアクセスパスワードを入力してください」と表示される。

プロジェクトでは環境変数 CODE でアクセスパスワードを設定しています。初めて使うときは、設定ページでアクセスコードを入力する必要があります。

## 使用すると、"You exceeded your current quota, ..." と表示される。

API KEY に問題があります。残高不足です。

## プロキシとは何ですか？

OpenAI の IP 制限により、中国をはじめとする一部の国や地域では、OpenAI API に直接接続することができず、プロキシを経由する必要があります。プロキシサーバ（フォワードプロキシ）を利用するか、事前に設定された OpenAI API リバースプロキシを利用します。

- フォワードプロキシの例: VPN ラダー。docker デプロイの場合は、環境変数 HTTP_PROXY にプロキシアドレス (http://address:port) を設定します。
- リバースプロキシの例: 他人のプロキシアドレスを使うか、Cloudflare を通じて無料で設定できる。プロジェクトの環境変数 BASE_URL にプロキシアドレスを設定してください。

## 中国のサーバーにデプロイできますか？

可能ですが、対処すべき問題があります:

- GitHub や OpenAI などのウェブサイトに接続するにはプロキシが必要です;
- GitHub や OpenAI のようなウェブサイトに接続するにはプロキシが必要です;
- 中国の政策により、海外のウェブサイト/ChatGPT 関連アプリケーションへのプロキシアクセスが制限されており、ブロックされる可能性があります。

# ネットワークサービス関連の質問

## クラウドフレアとは何ですか？

Cloudflare（CF）は、CDN、ドメイン管理、静的ページホスティング、エッジコンピューティング機能展開などを提供するネットワークサービスプロバイダーです。一般的な使用例: メインの購入やホスティング（解決、ダイナミックドメインなど）、サーバーへの CDN の適用（ブロックされないように IP を隠すことができる）、ウェブサイト（CF Pages）の展開。CF はほとんどのサービスを無料で提供しています。

## Vercel とは？

Vercel はグローバルなクラウドプラットフォームで、開発者がモダンなウェブアプリケーションをより迅速に構築、デプロイできるように設計されています。このプロジェクトや多くのウェブアプリケーションは、ワンクリックで Vercel 上に無料でデプロイできます。コードを理解する必要も、Linux を理解する必要も、サーバーを持つ必要も、お金を払う必要も、OpenAI API プロキシを設定する必要もありません。欠点は、中国の制限なしにアクセスするためにドメイン名をバインドする必要があることだ。

## ドメイン名の取得方法

1. Namesilo（アリペイ対応）や Cloudflare（海外プロバイダー）、Wanwang（中国国内プロバイダー）などのドメインプロバイダーに登録する。
2. 無料ドメインプロバイダー: eu.org（セカンドレベルドメイン）など。
3. 無料セカンドレベルドメインを友人に頼む。

## サーバーの取得方法

- 海外サーバープロバイダーの例 Amazon Web Services、Google Cloud、Vultr、Bandwagon、Hostdare など。
  海外サーバーの注意点 サーバー回線は中国でのアクセス速度に影響するため、CN2 GIA、CN2 回線を推奨。もしサーバーが中国でアクセスしにくい場合（深刻なパケットロスなど）、CDN（Cloudflare のようなプロバイダーのもの）を使ってみるとよいでしょう。
- 国内のサーバープロバイダー アリババクラウド、テンセントなど
  国内サーバーの注意点 ドメイン名の解決にはファイリングが必要。国内サーバーの帯域幅は比較的高い。海外のウェブサイト（GitHub、OpenAI など）へのアクセスにはプロキシが必要。

# OpenAI 関連の質問

## OpenAI のアカウントを登録するには？

chat.openai.com にアクセスして登録してください。以下のものが必要です:

- 優れた VPN (OpenAI はサポートされている地域のネイティブ IP アドレスしか許可しません)
- サポートされているメール (例: Gmail や会社/学校のメール。Outlook や QQ のメールは不可)
- SMS 認証を受ける方法（SMS-activate ウェブサイトなど）

## OpenAI API を有効にするには？API 残高の確認方法は？

公式ウェブサイト（VPN が必要）: https://platform.openai.com/account/usage
VPN なしで残高を確認するためにプロキシを設定しているユーザーもいます。API キーの漏洩を避けるため、信頼できる情報源であることを確認してください。

## OpenAI の新規アカウントに API 残高がないのはなぜですか？

(4月6日更新) 新規登録アカウントは通常 24 時間以内に API 残高が表示されます。現在、新規アカウントには 5 ドルの残高が与えられています。

## OpenAI API へのチャージ方法を教えてください。

OpenAI では、指定された地域のクレジットカードのみご利用いただけます（中国のクレジットカードはご利用いただけません）。お住まいの地域のクレジットカードに対応していない場合は、以下の方法があります:

1. Depay バーチャルクレジットカード
2. 海外のクレジットカードを申し込む
3. オンラインでトップアップしてくれる人を探す

## GPT-4 API にアクセスするには？

(4月6日更新) GPT-4 API へのアクセスには別途申請が必要です。以下のアドレスにアクセスし、ウェイティングリストに参加するための情報を入力してください（OpenAI の組織 ID をご用意ください）: https://openai.com/waitlist/gpt-4-api
その後、メールの更新をお待ちください。

## Azure OpenAI インターフェースの使い方

次を参照: [#371](https://github.com/sinhong2011/askai-web/issues/371)

## トークンの消費が速いのはなぜですか？

> 関連する議論: [#518](https://github.com/sinhong2011/askai-web/issues/518)

- GPT-4 にアクセスし、GPT-4 の API を定期的に使用している場合、GPT-4 の価格は GPT-3.5 の約 15 倍であるため、請求額が急激に増加します;
- GPT-3.5 を使用しており、頻繁に使用していないにもかかわらず、請求額が急速に増加している場合は、以下の手順で直ちにトラブルシューティングを行ってください:
  - OpenAI のウェブサイトで API キーの消費記録を確認してください。トークンが 1 時間ごとに消費され、毎回数万トークンが消費される場合は、キーが流出している可能性があります。すぐに削除して再生成してください。**適当なサイトで残高を確認しないでください。**
  - パスワードが 5 文字以下など短い場合、ブルートフォースによるコストは非常に低くなります。誰かが大量のパスワードの組み合わせを試したかどうかを確認するために、docker のログを検索することを推奨する。キーワード:アクセスコードの取得
- これら 2 つの方法を実行することで、トークンが急速に消費された原因を突き止めることができます:
  - OpenAI の消費記録に異常があるが、Docker ログに問題がない場合、API キーが流出したことを意味します;
  - Docker ログにアクセスコード取得のブルートフォース試行回数が多い場合は、パスワードがクラックされています。
