---
title: "AT Protocolで小さいサービスを作って1年経った"
emoji: "🔗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["atprotocol", "bluesky", "個人開発"]
published: false
---

この記事は[Bluesky / ATProtocol Advent Calendar 2025](https://adventar.org/calendars/12255)の13日目の記事です。

AT ProtocolでLinkatという小さいリンク集サービスを作って1年くらい経ちました。その中で感じたAT Protocol(以降、本文ではatproto)の良いところと、開発コミュニティの今の雰囲気、atprotoを使って遊ぶ方法を書いてみます。

このサービス自体は作っていくつか機能を足した後は放置しているので、サービスの機能とかはあまり書きません。

## AT Protocolとは

atprotoは大雑把に言えば公開API付きストレージを全アカウントが持っているネットワークです。[Bluesky](https://bsky.app/)というSNSなどで使われています。

APIが公開されているので、私が持っているデータを誰もが取得できます。

例えば私のBlueskyのプロフィールは以下で閲覧できます。このサービスは[PDSls](https://pdsls.dev)という、atprotoのアカウントが持っているデータを簡単に確認・編集できるサイトです。

https://pdsls.dev/at://did:plc:4gow62pk3vqpuwiwaslcwisa/app.bsky.actor.profile/self

誰でもこのデータを使ってサービスを作ることが出来ます。これがまず良いところです。

データがアカウント自身のサーバーに保存されるので、作るもの次第ではデータベース無しでサービスが作れます(このPDSlsもそうです)。個人開発でデータベースにかかる維持コストって割合として大きいんですよね。

## AT Protocolを使うとプロダクト同士を繋げられる

atprotoではアカウントが持っているデータが全て公開されるので、あるサービスが別のサービスで使われているデータを自由に使うことが出来ます。

例えばLinkatではプロフィールの表示にBlueskyで使われているプロフィールデータをそのまま表示しています。

↓でアカウントのアイコンが表示されていますが、Linkatで保存しているわけではありません。

![](/images/9ecfc0cedf1b70/profile.png)

逆にLinkatのデータが使われているサービスもあります。

Blueskyクライアントの[TOKIMEKI](https://tokimeki.blue/)、[Klearsky](https://klearsky.pages.dev/)や、Blueskyのユーザー紹介サービス[SkyBeMoreBlue](https://www.skybemoreblue.com/)では、プロフィールにLinkatへのリンクやLinkatで設定したリンクが表示されています。他にもいくつか利用例があります。

| TOKIMEKI                                 | Klearsky                                 | SkyBeMoreBlue                                 |
| ---------------------------------------- | ---------------------------------------- | --------------------------------------------- |
| ![](/images/9ecfc0cedf1b70/tokimeki.png) | ![](/images/9ecfc0cedf1b70/klearsky.png) | ![](/images/9ecfc0cedf1b70/skybemoreblue.png) |

このようにプロダクト同士が自由にデータを使うことが出来るので、相互に連携出来るサービスが生まれるとお互いを便利に使うことが出来ます。

もちろん同じアカウントが使えます。

### 代替サービスも作れる

異なるサービスが繋がるだけでなく、完全に同じ目的の代替サービスを作ることも出来ます。

まず、Linkatのデータは以下のような非常に簡単なJSONになっています。

```json
{
  "$type": "blue.linkat.board",
  "cards": [
    {
      "url": "",
      "text": "Linkatを開発しています"
    },
    {
      "url": "https://twitter.com/mkizka",
      "text": "Twitter"
    },
    {
      "url": "https://github.com/mkizka",
      "text": "GitHub"
    }
    // ...
  ]
}
```

これは以下のURLから取得できます。

```
https://enoki.us-east.host.bsky.network/xrpc/com.atproto.repo.getRecord?repo=did:plc:4gow62pk3vqpuwiwaslcwisa&collection=blue.linkat.board&rkey=self
```

URLを分解すると以下のようになっています。

- `https://enoki.us-east.host.bsky.network`
  - アカウントが所属しているサーバー。Personal Data Server(PDS)と呼ぶ
- `/xrpc/com.atproto.repo.getRecord`
  - データ1つを取得するAPI。このAPI群をXRPCとも言い、データはレコードと言う
- `?repo=did:plc:4gow62pk3vqpuwiwaslcwisa`
  - アカウントのリポジトリ。`did:`から始まる文字列が私のアカウントのID
- `&collection=blue.linkat.board`
- `&rkey=self`
  - レコードの種類とキー

これは認証無しでリクエスト出来るので、これを呼び出す実装を書くだけでもうLinkatと同じリンク集サービスが作れます。

もちろん編集したい場合は別途ログインと実装が必要です。編集したリンク集はPDSに保存されるので、おそらくデータベース無しでも作れるはずです。

(LinkatはSSRさせたかったのでデータベースを使っています。)

## AT Protocolの遊び方

## まとめ
