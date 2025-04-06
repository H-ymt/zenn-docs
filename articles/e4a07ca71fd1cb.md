---
title: "cdコマンドを強化する zoxide でターミナル操作を快適にする"
emoji: "📂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [zoxide]
published: true
---

## zoxide とは

Linux コマンド`cd`の改良版。z コマンドで移動したディレクトリを履歴としてデータベースに保存し、頻繁に使用するディレクトリに高速に移動ができるようになる。

例えば、今まで`cd`で次のように移動していたものが、

```sh:~
cd path/to/develop/project-name
```

zoxide インストール後に移動した場合は、データベースに履歴が保存され、次のコマンドだけで移動できる（`z pro` とかだけでも移動できる）

```sh:~
z project-name
```

## zoxide のインストール

homebrew でインストール

```sh
brew install zoxide
```

※別の OS の場合は下記を参照
https://crates.io/crates/zoxide<br/><br/>

zi コマンドで履歴からディレクトリをインタラクティブに選択するために [fzf](https://junegunn.github.io/fzf/) も使用するので、インストールしておく

```sh
brew install fzf
```

https://junegunn.github.io/fzf/installation/
<br/>
zoxide コマンドを Zsh シェルで使えるように、`~/.zshrc`に記述

```sh:~/.zshrc
# zoxide
eval "$(zoxide init zsh)"
```

bash や powershell などを使用している場合は下記を参考
https://crates.io/crates/zoxide

## 使い方

基本的に`cd`の代わりに`z`を使用するだけ

```sh
z developer/nextjs/nextjs-my-app
```

上記コマンドで一度移動すると、データベースに履歴が保存され、次回移行は次のコマンドだけで移動できるようになる

```sh
z my-app
```

### zi コマンド

`zi` コマンドでは fzf を使って、記憶されたディレクトリの一覧からインタラクティブに選択して移動できる

```sh
zi
```

![](/images/e4a07ca71fd1cb/zi-command.png)

`zi next`で next を含むディレクトリを検索することもできる
![](/images/e4a07ca71fd1cb/zi-command-next.png)

### z - コマンド

直前にいたディレクトリに移動できる

```sh
z -
```

## z や zi コマンドのプレフィックスを変更

`--cmd`をフラッグを使用し`~/.zshrc`を編集することで、j/ji・cd/cdi などで同じ操作が可能となる

```sh:~/.zshrc
# zoxide
eval "$(zoxide init zsh --cmd j)"
```

もしくは、

```sh:~/.zshrc
# zoxide
eval "$(zoxide init zsh --cmd cd)"
```
