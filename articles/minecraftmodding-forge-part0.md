---
title: "Minecraft Forge 1.18.2 Part0 EpitapのMOD開発忘備録について"
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Java"
  ,"minecraft"
  ,"IntelliJ"
  ,"初心者"
  ,"minecraftmodding"]
published: true
---
こんにちは！DegeneracyCraft開発者のEpitapです！
マインクラフトのMOD…
・いざ作ろうとしても、そもそもどこを調べたらいいのかわからない。
・YouTubeでもmodding(MOD製作の意)解説はあるが、大体英語なのでよくわからない。
・やりたいことを思いついてもその実現方法についてよくわからない。
↑その結果折れてしまう…

正直めちゃくちゃ大変だと思います。
なので、ここでは私Epitap(初心者)が
**超絶ゴリ押し(コードが端麗ではなく煩雑という意味)**
ながらもなんとか実装に漕ぎつけたものを一度振り返ってみようというものです。
また、MOD開発を志す人にほんの少し助力にでもなれれば幸いであるというものです。

ここに書き連ねることについての注意点は
・McJty氏、Kaupenjoe氏、henkelmax氏、桜鬼神氏、柊ラッセルつばさ氏らのコード・解説を参考にして、**ゴリ押しで**実装しているものがほとんどであること
・参考にしてもらっても構いませんが、以下の開発環境で開発しているということが前提である

:::message
開発環境 IntelliJ Community Edition 2022.2.4
SDK temurin-17 
Java version 17.0.4
Minecraft Version Forge ~~1.18.2-40.1.51~~ 1.18.2-40.2.0 mdk
:::
:::message alert
なぜMinecraftVersionが最新ではないか…
理由はおそらくわかると思います
:::
:::message 
↑Minecraft Version Forge 1.18.2-40.2.0 mdk
に移行成功しました！
:::

・再度申し上げますが、コードに自信はありません…
・更新は不定期であること


そんな感じです。

