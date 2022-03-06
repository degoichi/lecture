# 雑にわかるはじめてのGit

初めてGit使う人向けの布教用資料。
Unity爆発などの不慮の事故報告が多い昨今、救済したく作成した。

Git用のGUIツールもあるが、エラー出るとわからなくて詰むことが多いため、`Git Bash`を推奨する。
そのため、ここでは`Git Bash`の説明を主とする。(とかいいつつ`gitk`を一部使う)

感覚で書いている部分が多いので真に受けないこと。

## はじめに

### 想定読者

* Gitの使い方がわからない。
* Gitの実用的な所だけ知りたい。
* Git Bashつかったことない。
* Windowsユーザー

### やること

1. 環境構築
1. コミット
1. チェックアウト
1. プッシュ
1. クーロン
1. コンフリクト解消

### Gitの（個人的に）いいところ

* バージョン管理ができる。
  * フォルダの状態をタイムマシンのように戻せるということ。
* 分散管理ができる。
  * Cドライブで作業しているフォルダの内容をDドライブにバックアップ（C：ローカルリポジトリ、D：リモートリポジトリとした場合）とかメインのPCとサブPCで同じようにファイルを管理できる。
* （ぼっち運用だとほぼないけど）ファイルがリグレッションしない。
  * 妖怪不祥事案件でいうところの俺が修正した内容を他の人のファイルの上書きで消えちゃってるよねを引き起こす妖怪を撃退できる。
* 数コマンド覚えれば上記ができる。

### 語弊がある用語集

個人の見解で書いているので真に受けないこと。

| 単語        | 意味                                                             |
| --------- | -------------------------------------------------------------- |
| Git       | `the stupid content tracker`(バカ構成管理システム)とマニュアルに書いてある...<br>参考→ <https://medium.com/gitopia/git-a-stupid-content-tracker-d0ef5b86865f>                                     |
| commit    | セーブするコマンド                                                      |
| push      | 外部の場所にアップロードするコマンド。                                            |
| clone     | pushした内容をロードするコマンド。                                            |
| pull      | 最新バージョンをおとす的なコマンド（ぼっち運用だとあまり使わない）                            |
| branch  | ある時点での分岐点。IFの世界線みたいなもの。主な用途として平行での開発をするために切られる。 |
| マージ  | 修正した内容を最新のファイルに反映すること。だいたい自動でしてくれる。自動でできない場合はCONFLICTになる。 |
| CONFLICT  | pull又はbranchのマージをしたときにおきるエラー。初心者の壁。妖怪不祥事案件でいうところの誰かと更新箇所がかぶっちゃうことってあるよねを引き起こす妖怪。 |
| checkout  | タイムマシンしたいときにファイルを切り替えるのに使うコマンド。                                |
| ノンベアリポジトリ | 作業用のフォルダ（`git init`したフォルダのこと)                                  |
| ベアリポジトリ   | push先のフォルダ(`git init --bare`したフォルダのこと) 。            |
| リモートリポジトリ | pullとかpushとかするリポジトリ。ベアリポジトリとほぼ同義(ノンベアリポジトリにもpushできるけど非推奨のため) |

## 初期設定

### Gitのインストール

Windowsであれば以下のパスの`Standalone Installer`をダウンロードする。
<https://git-scm.com/download/win>

実行して次へを押すだけでいい。
設定はあとから変えることが可能なため。
各画面の詳しい説明は検索すればたくさん出てくる。

### ユーザー名、メールの設定

Gitを使うためには「誰がcommitとかしたか」を追跡するための情報を設定する必要がある。
以下のコマンドでユーザー名とメールを設定する。
内容は後からも変えられるため、適当なもので問題ない。理由は以下である。

* GitはGitHubのアカウントがなくてもつかえる。
* この設定によってネットワークにつなげて何かするものではない。
* 後から同じようなコマンドで変えられる。

```bash
git config --global user.name "hoge"
git config --global user.email "hoge@example.com"
```

### コンフィグの変更

初期のコンフィグの設定。
以下を打ち込む。

```bash
git config --global core.autocrlf false
git config --global init.defaultBranch main
```

| 設定値                     | 理由                               |
| ----------------------- | -------------------------------- |
| core.autocrlf false     | 改行コードの自動変換をしないようにするため。           |
| init.defaultBranch main | デフォルトのブランチ名をmainとする。昔はmasterだった。 |

以下を入力して設定された文言があることを確認する。

```bash
git config --list
```

| コマンド              | 意味       |
| ----------------- | -------- |
| `git config --list` | Gitの設定確認 |

## 始め方

### 簡単なコマンドライン

Windowsの場合、インストールすると`Git Bash`みたいなのができるのでダブルクリックすると黒い画面が出てくる。
これでキーボードだけで操作（通称、コマンドライン）を行うが、簡単に操作方法を説明する。

基本的にコマンドは __一行ずつコピーして実行__ する。貼り付けは`Ctrl+V`を使えないので、右クリックしてPastをするか、`Shift+insert`をする。

#### 自分の位置の確認

自分がどのディレクトリにいるかは以下を入れる。

```bash
pwd
```

以下のコマンドを入れるとホームディレクトリ（/C/Users/ユーザー名）がでるはず。

#### 移動

以下でCドライブ直下に移動する。

```bash
cd /C/
pwd
```

スペース等あるときは以下のようにする。

```bash
cd /C/'Program Files'/
pwd
```

上の階層にいくときは以下のようにする。

```bash
cd ..
pwd
```

cdだけ打つとユーザーフォルダの直下に移動する。

```bash
cd
pwd
```

#### フォルダの内容の確認

フォルダにある内容を確認したいときは以下のコマンドを入れる。

```bash
ls
```

そこにあるフォルダ、ファイルが表示される。

#### フォルダの作成、削除

```bash
mkdir hoge
rmdir hoge
```

`rmdir`は空フォルダのみ消せる。

| コマンド  | 意味              |
| ----- | --------------- |
| `cd`    | フォルダ移動          |
| `pwd`   | 現在いるところのパスを表示   |
| `ls`    | フォルダ、ファイルの一覧を表示 |
| `mkdir` | フォルダの作成         |
| `rmdir` | フォルダの削除         |

### リポジトリの作成(`git init`)

手動コピーでいうところのバックアップフォルダを作成する。

```bash
mkdir hoge
cd hoge
git init
git status
```

`nothing to commit`と出力される。

なお、作成したhogeフォルダを開くと`.git`という名前のフォルダが作成されている。
これがGit版のバックアップフォルダである。

| コマンド       | 意味                        |
| ---------- | ------------------------- |
| `git init`   | リポジトリを作成                  |
| `git status` | 変更されたファイルやGitに登録するものを確認する。 |

### ファイルのコミット(`git commit`)

hoge.txtを追加してバージョン管理する。

echoを使って"hoge"とかかれたテキストファイルを作成する。

```bash
echo "hoge" > hoge.txt
cat hoge.txt
git status
```

`cat`はhoge.txtのファイルの中身を表示するコマンド。
テキストエディタでhoge.txtを開いて同じことを確認する。

`nothing added to commit but untracked files present`と出る。
追跡できてないファイルがあると言われている。
hoge.txtをバージョン管理できるように追加する。

```bash
git add -A
git status
```

コミット予定変更点にhoge.txtが追加されている。

まだコミット予定状態なのでコミットして確定させる。

```bash
git commit -m "first commit"
git status
```

コミットしたので、`git status`をしても`nothing to commit`と出力される。
コミットされたことを確認するには以下のコマンドを入れる。

```bash
gitk
```

左上のほうに○とコメント(first commit)の記載がある。
これがコミットを重ねるたびに伸びていく。

| コマンド                         | 意味                                      |
| ---------------------------- | --------------------------------------- |
| `echo "hoge" > hoge.txt` | "hoge"というメッセージをテキストに出力する。 |
| `cat hoge.txt` | テキストの中身を表示する。 |
| `git commit -m "first commit"` | コミットする。"first commit"のところはコメントなので何でもいい。 |
| `git add -A`                    | 変更のあるファイルをすべてバージョン管理対象にする。             |
| `gitk`                         | Gitのコミット履歴をGUIで表示するやつ。                  |

gitkを閉じるとbashがまた操作可能になる。

### ファイルを戻す(`git checkout`)

```bash
echo "hoge2" >> hoge.txt
cat hoge.txt
git add -A
git commit -m "hoge2"
git log --oneline -10
```

上記で表示された履歴のうち、"first commit"したときの頭の7桁の文字列を使って以下のコマンドを実行する。

```bash
git checkout 55e22db
cat hoge.txt
```

hoge2を追加する前に戻したので"hoge2"の文字がない。
以下のコマンドでもとに戻す。

```bash
git checkout main
cat hoge.txt
```

hoge2が追加され、戻ったことを確認できる。

| コマンド                  | 意味                     |
| --------------------- | ---------------------- |
| `git log --oneline -10` | Gitのコミット履歴をCUIで表示する。`-10`で10行だけ表示する。 |
| `git checkout 55e22db`  | 指定した履歴にタイムスリップ         |
| `git checkout main`     | mainブランチの最新のやつにタイムスリップ |

### ベアリポジトリの作成

NASとか別ドライブとかに作成するとバックアップにもなるのでおすすめである。
ベアリポジトリのフォルダ名は最後に`.git`とする慣習がある。

```bash
cd
mkdir hoge.git
cd hoge.git
git init --bare
```

| コマンド            | 意味         |
| --------------- | ---------- |
| `git init --bare` | ベアリポジトリを作成 |

### 別のとこにアップロード(`git push`)

ベアリポジトリを登録してからプッシュする。

```bash
cd 
cd hoge
git remote add origin ../hoge.git
git remote -v
git push origin main
```

`git remote`のオプションは例えると"国会議事堂"(origin)という名前で"東京都〇区１－１"(../hoge.Git)という住所を登録するという意味である。

`git push`はそのoriginにmainの内容をアップロードする。
mainはデフォルトでのブランチの名前で、今回はほかにブランチを切っていないため今までのコミットの内容がアップロードされる。

| コマンド                           | 意味                                         |
| ------------------------------ | ------------------------------------------ |
| `git remote add origin hoge.git` | リモートリポジトリのパスをoriginという名前で登録する。             |
| `git remote -v`                  | リモートリポジトリの設定がされていることを確認する。                 |
| `git push origin main`           | リモートリポジトリ(origin)へローカルのブランチ(main)の更新をプッシュする。 |

### アップロードした内容のクローン(`git clone`)

ボブというイマジナリーフレンドがいる想定でクローンを実施する。

```bash
cd 
git clone hoge.git hoge.bob
cd hoge.bob
cat hoge.txt
```

hoge.txtの中身があること。
これは主に作業用ディレクトリのドライブが死んだとき、新しい環境で復元する際に使用する。

| コマンド                        | 意味                                             |
| --------------------------- | ---------------------------------------------- |
| `git clone hoge.git hoge.bob` | リモートリポジトリ(hoge.Git)をクローンしてhoge.bobフォルダを作成して展開する。 |

### ファイルの競合の解消

ファイルの競合を起こす前にマージツールの設定をする。

#### マージツールの設定

Gitで競合が起きた場合、競合を解決するためのマージツールが必要である。マージツールは何でもいいが、VScodeを推奨する。

VScodeは軽量で無料なコードエディタである。
開発者や小説家、ナウなヤングに至るまでばかうけしている。
これもインストーラー版をダウンロードしてインストールすればいい。

<https://azure.microsoft.com/ja-jp/products/visual-studio-code/>

インストール完了後、GitのマージツールにVSCodeを設定する。

ホームディレクトリ（/C/Users/ユーザー名）直下に".gitconfig"というのがある。
これをテキストエディタで開いて、以下の内容を最終行に追加する。

```bash
[merge]
        tool = vscode
[mergetool]
        prompt = false
        keepBackup = false
[mergetool "vscode"]
        cmd = 'code' \"$MERGED\"
        trustExitCode = false
```

#### 競合をおこす

hogeフォルダとhoge.bobのフォルダ両方で同じファイルの編集を行い競合を起こさせる。
hogeフォルダで以下のコマンドを入する。

```bash
cd 
cd hoge
echo "hogehoge" >> hoge.txt
cat hoge.txt
git add -A
git commit -m "change hogehoge"
git pull origin main
git push origin main
```

pullをしても`Already up to date.`と出る。
これは更新がないため。

次に「俺はボブ」と言ってから以下を入力する。

```bash
cd ../hoge.bob
echo "bob change" >> hoge.txt
cat hoge.txt
git add -A
git commit -m "bob change"
git pull origin main
```

下から2行目あたりにCONFLICTという恐怖の文言がでる。
これはhogeフォルダでコミットした箇所とボブの修正した箇所がかぶってることで起きている。

```bash
cat hoge.txt
```

ファイルの中身を見ると`<<<<<<< HEAD`とか`=======`とか追加されている。
この部分をメモ帳とかで消してもいいが、VSCodeで編集すると楽である。

マージするため、以下のコマンドでマージツール（VSCode）を起動する。

```bash
git mergetool
```

すると、VSCodeが立ち上がり、以下のような画面になる。

![conflict image](/01_git/01_conflect.png)

赤枠の文言で好きなものをクリックする。なお、押した後`Ctrl+Z`で戻せる。

`Compare Changes`は左右にファイルを表示して差分をわかりやすく別タブで表示してくれる。確認したらそのタブを閉じれば問題ない。

| アクション                  | 意味                           |
| ---------------------- | ---------------------------- |
| Accept Current Change  | 自分で直した修正（今回はボブの修正）を反映        |
| Accept Incoming Change | リモートリポジトリの修正(hogehogeの修正)を反映 |
| Accept Both Change     | どっちの修正も採用                    |

保存してファイルを閉じる。

`Was the merge successful [y/n]?`(マージ成功した？　)と聞かれるので`Y`を押す。

マージ完了したらマージしたファイルをコミットする。

```bash
git add -A
git commit -m "merge"
git push origin main
gitk
```

分岐した線が繋がっていること。
hogeフォルダの方でもpullで反映されることを確認する。

```bash
cd 
cd hoge
git pull origin main
cat hoge.txt
```

以上で本資料は終わりなので今回作成したhoge,hoge.bobは削除してよい。

| コマンド          | 意味           |
| ------------- | ------------ |
| `git pull origin main` | originのmainブランチから最新の情報を取得する。 |
| `git mergetool` | マージツールを起動する。 |

## QandA

### だいたい理解したけどまず何すればいい？

自作ポエム、自作絵、自作プロジェクト等、消えたら死ぬってところをGitで管理する。
この際、NASや別ストレージにベアリポジトリを作り分散管理すること。

Cドライブに作業用フォルダ、Dドライブにバックアップとした場合、以下のようになる。

```bash
cd /D/
mkdir 自作ポエム.git
cd 自作ポエム.git
git init --bare

cd /C/xxx/xxx/自作ポエム
git init
%% 必要あれば後述する.gitignoreをここで追加する。
git add -A
git commit -m "first commit"
git remote add origin /D/自作ポエム.git
git push origin main
```

その後は、ファイル更新するたびにとりあえず以下のコマンドを実行する癖をつけるだけでいい。

```bash
git add -A
git commit -m "comment"
git push origin main
```

### コミットしたくないファイルがある

UnityやExcel等があるフォルダの場合、一時ファイルやビルドのたびに再生成されてしまうファイルもあり、それをコミット対象外にできる。
やり方としては`.gitignore`というブラックリストを記載したファイルを`git init`したフォルダの直下に格納する。
ファイルの中身のテンプレートはGitHubにあるものをコピー&ペーストする。

Unityであれば以下が上げられる。

<https://github.com/github/gitignore/blob/main/Unity.gitignore>

### ファイル数が多すぎて`git add -A`から帰ってこない

`git add -A`を実行するとGit用バックアップフォルダにファイルの圧縮及びコピーをしているためファイル数が多いと時間がかかる。ご飯を作るなどして気長に待つこと。
なお、HDDの場合はSSDに変更するのもおすすめ。

### Git Bashで日本語が文字化けする

以下を参する。

<https://ygkb.jp/3494>

### もっと探求したい

今回は触れていない部分はたくさんある。
特に以下は調べてみるといい。

* branch及びmerge
* tag
* fetch
* Git log等各コマンドのオプション(-mとか-10とか)

また、以下の点も調べるといい。

* 他のGitのGUIツール
  * GitExtensionsとか？
* .gitignore
  * UnityやC言語などやる場合は特におすすめ
* Github、GitLabなど

### デフォルトブランチ名をmasterからmainに変更したい

以下でブランチを変更してpushする。

```bash
git branch -m master main
git push -u origin main
```

ベアリポジトリのデフォルトブランチをmainにする。

```bash
git symbolic-ref HEAD refs/heads/main
```

masterブランチを削除する。
また、ローカルに残っているリモート追跡ブランチの削除も行う。

```bash
git push --delete origin master
git branch -a
git fetch --prune
git branch -a
```

### わかりにくいとこがある

issueかプルリクください。

### `git pull origin main`と`git push origin main`

引数が送信元を指していたり、送信先を指していたりするので混乱する。

* `git pull` (取得先) (取得先)
* `git push` (送信先) (取得元※)

※送信先のブランチ名を兼用しているとも言える。

#### `git pull origin main`とは

以下の2つの処理を行っている。

1. リモートリポジトリ(origin)のブランチ(main)の内容を取得する。(`git fetch origin main`)
1. 現在いるローカルブランチに対し、ブランチ(main)の内容を反映する。(`git merge main`)

例）featureのbranchにいるとき同コマンドを実行した場合、(origin/main)の内容がfeatureに反映される。

#### `git push origin main`とは

リモートリポジトリ(origin)に対して、ローカルリポジトリのブランチ(main)の内容をアップロードする。
なお、pushの反対はfetch。
リモートリポジトリに同名のブランチがない場合は追加される。
fetchに合わせて書く場合、「リモートリポジトリ(origin)のブランチ(main)の内容を更新する。ローカルブランチ(main)で」と言える。
