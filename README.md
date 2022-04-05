# Git

## 目次
<!-- vscode-markdown-toc -->
1. [QuickStart](#QuickStart)  
2. [Gitの説明](#Git)
3. [セットアップと公式リンク](#setup)
4. [Git(バージョン管理ツール)をなぜ使うのか](#whyareweusegit)
5. [Git の基本的な使い方](#basicusageofgit)
6. [branch(Git の重要な機能)](#branchGit)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->


##  1. <a name='QuickStart'></a>QuickStart

Gitを使う上での基本的な流れは以下の3つ  

(以下のコマンドはVScodeなどを使うと全てGUIで実行できたりするのでそちらのほうがやりやすいかも: [参考リンク](https://qiita.com/y-tsutsu/items/2ba96b16b220fb5913be))

- 開発を始めるときはdevelopブランチからfeatureブランチを作る(featureブランチは1機能くらいの小さい単位で分けたほうが良い 例:feature/#xx_indmo_number_sort)

  コマンド例

  ```sh
      cd program
      # developブランチに移動
      git checkout develop
      # Featureブランチを作成(今回は#1_edit_main.f90という名前)
      git branch feature/#1_edit_main.f90
      # git checkoutコマンドを使ってfeature branchに移動
      git checkout feature/#1_edit_main.f90
  ```

- Featureブランチ上でコーディング作業を行う

  - このときこまめにcommitするとよい(セーブポイントをこまめにつけることでもとに戻りやすくなる)  
    (例)

    ```sh
      cd program
      git add main.f90
      git commit -m '[Fix] Bugfix main.f90. Memory allocation error'
    ```

- featureブランチで付けた名前の機能分程度の実装ができ、バグなく実行できることが確認できたらmainブランチやdevelopブランチにマージする
  - githubやgitlabなどのリモートリポジトリをつかっていたらpushする([参考リンク1](https://www.freecodecamp.org/news/git-push-to-remote-branch-how-to-push-a-local-branch-to-origin/),[参考リンク2](https://qiita.com/shimotaroo/items/ed08d76447144d566637))

    ```sh
      cd program
      # feature/#1_edit_main.f90ブランチをgithubにpush(アップロード)
      git push origin feature/#1_edit_main.f90
    ```

    - (リモートリポジトリを使っている場合)pushできたらPull requestを発行してdevelopブランチ、mainブランチにマージ
      - このとき[squash機能](https://docs.github.com/ja/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/configuring-commit-squashing-for-pull-requests)を使ってcommitの数を1つにするとmainブランチやdevelopブランチの履歴がすっきりするが履歴が1つに圧縮されるという欠点があるのでどちらの方法を選ぶかを決めておく
      - (個人的にはmainブランチはsquashして、developブランチをsquashしないのが良いと思っている)
      - [squashの参考記事](https://www.granfairs.com/blog/cto/git-merge-squash)
  - 使っていなかったらdevelopブランチにマージする

    ```sh
      cd program
      # Developブランチに移動
      git checkout develop
      # Squashしてマージする
      git merge --squashed feature/#1_edit_main.f90
      # 差分を追記してcommit
      git add .
      git commit -m 'feature/#1_edit_main merged.'
    ```

##  2. <a name='Git'></a>Gitの説明
バージョン管理ツールの 1 つ  
似たようなツールに<a href="https://www.mercurial-scm.org/" class="urlextern" title="https://www.mercurial-scm.org/">Mercurial</a>や<a href="https://subversion.apache.org/" class="urlextern" title="https://subversion.apache.org/">Subversion</a>がある  
プログラムのバージョンを管理するのに

```code
  # バージョンが多すぎてどれが最終バージョンなのかがわからない!!
  ---- program_ver1
    |_ program_ver2
    |_ program_ver3
    |_ program_ver3_fix
    |_ program_ver3_new
```

のようなディレクトリの状態になっていたり

```code
  program_ver3_fix---- main.f90
                    |_ main.fix.2021.11.1.f90
                    |_ main.fix.2021.11.2.f90
                    |_ main.fix.new.f90
```

のようなファイル構造になっていたり  
編集するたびに単純にコードを上書きしていて  
間違えたことに気がついたときにもとに戻せる状態でないならぜひ使うべきツール。

##  3. <a name='setup'></a>セットアップと公式リンク

大抵の Linux 環境には git が入っているのでインストールをせずとも

```sh
git --version
```

で git のバージョンが表示されるはず  
もし表示されなかったり、新しいバージョンを新規インストールしたい場合は<a href="https://git-scm.com/" class="urlextern" title="https://git-scm.com/">Git 公式</a>を参照

- <a href="https://training.github.com/downloads/ja/github-git-cheat-sheet/" class="urlextern" title="https://training.github.com/downloads/ja/github-git-cheat-sheet/">公式の GitHub チートシート</a>が用意されているのでコマンドを忘れたらこのリンクを確認すると良い。  
  (GitHub を使わない場合「変更の同期」の部分は無視して良い)

- <a href="https://git-scm.com/docs/git#_git_commands" class="urlextern" title="https://git-scm.com/docs/git#_git_commands">公式のコマンド一覧</a>のページも存在する

最初に

```sh
git config --global user.name "任意の名前"
git config --global user.email "任意のメールアドレス"
```

を実行することで git の設定は完了

##  4. <a name='whyareweusegit'></a>Git(バージョン管理ツール)をなぜ使うのか

- 編集者が**任意に作成したセーブポイントにいつでも戻ることができる**から

(例)ファイル(main.f90)を編集したものの、間違いに気づいたので  
今のファイルを破棄して最後に作ったセーブポイントに戻りたくなったとき

ファイル構造は

```code
# ファイルがmain.f90だけ。シンプル。
program ---- main.f90
```

の 1 つのファイルしかないが

```sh
# programディレクトリに移動
cd program
# gitのコマンドを使って今のファイルを破棄して最後に作ったセーブポイントに戻る
git reset --hard HEAD
```

と実行するだけで main.f90 を**最後に作ったセーブポイントの状態に復元できる!!**

##  5. <a name='basicusageofgit'></a>Git の基本的な使い方

基本的な使い方は<a href="https://www.google.com/search?q=git+%E5%85%A5%E9%96%80" class="urlextern" title="https://www.google.com/search?q=git+%E5%85%A5%E9%96%80">Google で「git 入門」と検索</a>するだけで山のようにチュートリアルサイトがヒットする  
ここでは例として

```code
program ---- main.f90
          |_ sub.f90
          |_ time.f90
          |_ util.f90
```

のような構造のプログラム群を git で管理するときの流れを紹介する

```sh
# programディレクトリに移動
cd program
# programディレクトリ以下をgitの管理化に置くという宣言
git init
# program/以下のファイルをすべてgitのステージングエリアという場所に追加する
git add -A
# program/以下のファイルをすべてgitのローカルリポジトリ(セーブポイント置き場のようなもの)に追加する
# Initial commit というコメントを追加してセーブポイントを作っている
# commit(ローカルリポジトリへのファイルの追加)をするには必ずコメントが必要
git commit -m "Initial commit"
```

この後例えば main.f90 のみを編集した場合、次の commit(セーブポイント)を作成するには以下のようにする

```sh
# programディレクトリに移動
cd program
# program/main.f90をgitのステージングエリアに追加する
git add main.f90
# program/main.f90をローカルリポジトリに追加する
# [Change] Edited main.f90 というコメント(コミットメッセージ)を追加してセーブポイントを作っている
git commit -m "[Change] Edited main.f90"
```

セーブポイントを確認するには

```sh
git log
```

と入力すればよく、次のような出力が得られるはずである

```sh
# commitの番号やAuthorの値やDateの値は違っていても問題ない
 
commit cc3fb05fd32adcb95cffafdcff926ad6b5d2461f
Author: 任意の名前 <任意のメールアドレス>
Date:   Tue Nov 2 17:24:28 2021 +0900
 
    [Change] Edited main.f90
 
commit ef9aacd4c2380974e118e56e5130caa84500508e
Author: 任意の名前 <任意のメールアドレス>
Date:   Tue Nov 2 17:22:56 2021 +0900
 
    initial commit
```

このような手順で順次 commit していくことでセーブポイントが作られていくため、間違えたときや一時的に過去のバージョンに戻りたいときに簡単にバージョンを変更することができるようになる

##  6. <a name='branchGit'></a>branch(Git の重要な機能)

実はGitの基本的な使い方で行っていた編集は1つのbranchというものを使って行っていた。

しかし通常 Git を使う際は Initial
Commit をした branch に新しい commit をどんどん書き込むということはせず  
**複数の branch**を用意して**違う branch に commit**をする(とても小規模なコードの場合など例外はある)

branch のイメージは以下の Git 公式の図(c1,c2,…は commit、main,develop,topic が branch を表す)

> ![branchのイメージ図](https://git-scm.com/book/en/v2/images/lr-branches-2.png)

出典:<a href="https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C" class="urlextern" title="https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C">3.4 Git のブランチ機能 - ブランチでの作業の流れ</a>
Git  
URL:<a href="https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C" class="urlextern" title="https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C">[https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C)</a>

branch とは枝という意味で、上の図のように branch が枝分かれしていく様子から名付けられているらしい

branch をどのような構造で作るかについては各種議論があるが、  
このページの編集者が個人的に推しているのは<a href="https://www.atlassian.com/ja/git/tutorials/comparing-workflows/gitflow-workflow" class="urlextern" title="https://www.atlassian.com/ja/git/tutorials/comparing-workflows/gitflow-workflow">Git-flow</a>の構造  
従って以降の説明はこの構造に則って行う。

**各ブランチの役割**

- **master (or main)**:
  メインブランチ。version0.0.1 などと version をつけて管理する。**基本的に完全に動作する commit のみの**ブランチ。

- **release**: master (or
  main)に変更を渡すためのブランチ。なくてもよい。

<!-- -->

- **develop**: releaseに変更を渡すためのブランチ。基本的に新しい feature ブランチは develop ブランチの最新の commit から作る。

<!-- -->

- **feature**:
  開発用のブランチ。**気軽に最新の develop ブランチや他の feature ブランチから作成したり、消したりてもよい。**feature ブランチは複数作られるので(例)feature/\#1_edit_main.f90
  のように**feature/\#番号\_内容**の形式でブランチを作る。

<!-- -->

- **hotfix**: master (or main)ブランチから作られるブランチ。master (or
  main)ブランチにバグが生じたときに作る緊急メンテナンス用。

**Git-flow形式の Git 管理の例**

- 要約

1.  master(or main)branch を作る

2.  develop, feature branch を作る

3.  feature branch で開発する

4.  develop branch に feature branch の内容を merge(結合)する

5.  master(or main)branch に develop branch の内容を merge(結合)する

6.  もし master(or main)branch にバグが発生したら master(or
    main)branch から hotfix branch を作る

7.  hotfix branch で開発、修正を行う

8.  master(or main)branch に hotfix branch の内容を merge(結合)する

(例)

```code
program ---- main.f90
          |_ sub.f90
          |_ time.f90
          |_ util.f90
```

- master(or main)branch を作る

```sh
cd program
git init
git add -A
git commit -m "Initial commit"
```

- develop, feature branch を作って feature branch に移動する

```sh
cd program
# git branchコマンドを使ってdevelop, feature branchを作成
git branch develop
git branch feature/#1_edit_main.f90
# git checkoutコマンドを使ってfeature branchに移動
git checkout feature/#1_edit_main.f90
```

- feature branch でファイルを編集、変更を commit する

```sh
# 何らかのエディタ(この例ではvim)でファイルを開き編集
vim main.f90
# main.f90の変更をadd,commitする
git add main.f90
git commit -m "[Change] Edited main.f90"
 
# 更に編集してみる
vim main.f90
git add main.f90
git commit -m "[Change] Edited main.f90 again"
```

- develop branch に feature branch の変更を適用する

```sh
# develop branchに移動
git checkout develop
# git mergeコマンドでfeature branchの変更をdevelop branchに適用
git merge feature/#1_edit_main.f90
```

- master(or main) branch に develop branch の変更を適用する

```sh
# master(or main) branchに移動
git checkout main
# git merge --squash コマンドでdevelop branchの変更をmaster(or main) branchに適用
# (--squash を用いるとdevelop branchの2つの追加commit(Edited main.f90とEdited main.f90 again)を
# 1つのコミットにすることができる
git merge --squash develop
# git merge --squash の後はcommitしないと変更がローカルリポジトリに保存されない
git commit -m "v0.0.1:Edited main.f90"
```

以上の手順を行うと main branch の環境をできるだけシンプルに保ったままバージョン管理ができる(嬉しい)!
