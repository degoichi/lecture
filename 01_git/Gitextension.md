# GitExtensions 補足

以下をするためのGit Extensionの使い方の補足。
といってもほとんど補足する事がないので自分のメモ。

<https://narazaka.hatenablog.jp/entry/2021/02/13/VRChat%E3%82%A2%E3%83%90%E3%82%BF%E3%83%BCGit%E7%AE%A1%E7%90%86%E3%81%A7%E3%82%A2%E3%83%90%E3%82%BF%E3%83%BC%E5%A4%9A%E9%A0%AD%E9%A3%BC%E3%81%84%E3%81%AE%E3%82%B9%E3%82%B9%E3%83%A1>

## 予備知識

こっちを参照

<https://github.com/degoichi/lecture/blob/main/01_git/init.md>

## 補足

### Git Extensinonsの設定

初期設定を求められたら以下のように入れればよさそう。
（ただし、ほとんど使うことないかも？）

![Git_Extension_setting image](/01_git/02_gitextension.png)

.gitcofnigが以下みたくなるはず。

```bash
[diff]
	guitool = vscode
[difftool "vscode"]
	cmd = 'code' --wait --diff \"$LOCAL\" \"$REMOTE\"
[merge]
	tool = vscode
	guitool = vscode
[mergetool]
	prompt = false
	keepBackup = false
[mergetool "vscode"]
	cmd = 'code' \"$MERGED\"
	trustExitCode = false
```

### マージ時の流れ

PULL実施してコンフリクト起こした後、以下のようにしてマージを行う。

1. Git Extensionsでマージを実行する。コンフリクト起こす。
1. Git Extensionsで「競合の解決」のウィンドウが開いたらvscodeを起動する。
  ここで「マージツールを開始」をするとコンフリクトしたファイル一個ずつやる必要があるためしない。
1. 「ファイル」→「フォルダをワークスペースに追加」からGit管理しているフォルダを追加
1. 下記画像のように「ソース管理」→「変更のマージ」からマージする。ある程度まとめてやっても可。マージの方法は先の説明の通り。
![vscode_merge image](/01_git/03_vscode_merge.png)
1. 以下の「+」を推してステージに追加（git addと同義）
![vscode_merge image](/01_git/04_vscode_stage.png)
1. Git Extensionsに戻ってマージの競合の「再スキャン」を実施
  「マージツールの設定が正しくありません」と出るがOKを押す。
  すると、「コミットしますか？」と聞かれるのでOKを押す。
1. コミットを押す。
