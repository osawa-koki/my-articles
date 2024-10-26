---
title: "GitHub Actions上でrefusing to merge unrelated historiesエラーが発生する場合の解決策。"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git", "github", "githubactions"]
published: true
---

# 概要

GitHub Actions 上で、マージを実行すると`refusing to merge unrelated histories`エラーが発生しました。  
このエラーは、ローカルでは再現せず、ローカル環境では問題なくマージできました。  
`orphan`ブランチも存在しないため、原因の特定に苦したため、解決策を記載しておきます。

## 解決策

シャロークローンを禁止します。 　
具体的には、`actions/checkout@v3`内で`fetch-depth: 0`を指定します。

```yml:*.yml
- name: Checkout repository
  uses: actions/checkout@v3
  with:
    fetch-depth: 0
```

デフォルトでは、`fetch-depth: 1`が指定されています。  

```yml
# Number of commits to fetch. 0 indicates all history for all branches and tags.
# Default: 1
fetch-depth: ''
```

※ [github.com/actions/checkout](https://github.com/actions/checkout)より引用。  

冗長になりますが、以下のコマンドで強制的にシャロークローンを解除できます。  

```shell
git fetch --unshallow
```

## エラーの原因

通常は`orphan`ブランチのような、異なる履歴を持つブランチをマージしようとすると発生します。  
ただ今回はシャロークローンで、正しくリモートリポジトリの履歴を取得できていなかったため、エラーが発生しました。  
