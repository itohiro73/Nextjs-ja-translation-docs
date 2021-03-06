# マルチゾーン

<details>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-zones">マルチゾーン</a></li>
  </ul>
</details>

ゾーンとは Next.js アプリケーションの単一のデプロイメントのことです。複数のゾーンを単一のアプリケーションに統合もできます。

例えば、以下のアプリケーションがあるとします:

- `/blog/**` を配信するためのアプリケーション
- 他のすべてのページを配信するための別のアプリケーション

マルチゾーンのサポートにより、これら両方のアプリケーションを 1 つに統合して単一の URL から閲覧できるようにしながらも、両方のアプリケーションを独立して開発・デプロイできます。

## ゾーンを定義する方法

ゾーンに関係した特別な API はありません。必要な作業は以下だけです:

- アプリケーションに必要なページのみを保持するようにしてください。これは、あるアプリケーションが別のアプリケーションのページを保持できないことを意味します。もしもアプリケーション `A` に `/blog` が存在している場合は、アプリケーション `B` はそれを持つべきではありません。
- 静的ファイルのコンフリクトを避けるために、[assetPrefix](/docs/api-reference/next.config.js/cdn-support-with-asset-prefix.md) を追加してください。

## ゾーンを統合する方法

任意の HTTP プロキシを使用してゾーンを統合できます。

[Vercel](https://vercel.com/now) の場合、 単一の `now.json` を両方のアプリケーションのデプロイに使用できます。複数のアプリケーションのルーティングは以下のように定義できます:

```json
{
  "version": 2,
  "builds": [
    { "src": "blog/package.json", "use": "@now/next" },
    { "src": "home/package.json", "use": "@now/next" }
  ],
  "routes": [
    { "src": "/blog/_next(.*)", "dest": "blog/_next$1" },
    { "src": "/blog(.*)", "dest": "blog/blog$1" },
    { "src": "(.*)", "dest": "home$1" }
  ]
}
```

また、上記のようなルートを使用してルーティングするように、プロキシサーバを設定してもよいでしょう。例えば、ブログアプリケーションを `https://blog.example.com` に、ホームアプリケーションを `https://home.example.com` にそれぞれデプロイし `https://example.com` に対して両方のアプリケーションのプロキシサーバを追加できます。
