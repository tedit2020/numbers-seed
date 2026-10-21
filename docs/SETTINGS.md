# 設定ファイル（`.claude/settings.json`）の説明

設定ファイルには説明（コメント）を書けません。書くと、起動のときに設定のエラーになります（公式の settings の頁）。
そこで、1 行ずつの意味をこの頁に書きます。公式の頁は 2026-10-09 に確かめました。

## 1. 連携を止める 1 行

| 行 | 意味 |
|---|---|
| `"disableClaudeAiConnectors": true` | Claude のサイトでつないだ連携（Gmail・Google カレンダー・Google Drive・Slack など）を、このフォルダで起動した相棒から全部見えなくします。公式: どこか 1 か所に `true` があれば効き、このフォルダの設定に書いた `true` も効きます |

- 🔴 **このフォルダの外で起動すると、この行は効きません。** 相棒は、いつもこのフォルダで起動してください。

## 2. 許可（`allow`）

| 行 | 意味 |
|---|---|
| `Read` / `Glob` / `Grep` | ファイルを読む・探す |
| `Edit(/notebook/**)` | `notebook/` の中に書く（決めごと・やることの帳面） |
| `Edit(/onboarding/**)` | `onboarding/` の中に書く（設定の案） |
| `Edit(/work/**)` | `work/` の中に書く（下書き・まとめ） |

- 公式: 許可の行は、«このフォルダを信頼するか» の画面で信頼を選んだ後に効きます。
- 書き出しの `/` は «このフォルダ» からの場所、という意味です（公式の permissions の頁）。

## 3. 禁止（`deny`）

禁止の行は、信頼を選ぶ前からすぐ効きます。どの動き方（おまかせ / 毎回確認）でも効きます（公式）。

| まとまり | 行 | 意味 |
|---|---|---|
| 自分の設定 | `Edit(/.claude/**)` / `Edit(/CLAUDE.md)` | 相棒が自分の設定と構えを書き換えない |
| 鍵のファイル | `Read(**/.env*)` | 鍵を入れることが多いファイルを読まない |
| 消す | `Bash(rm *)` など / `PowerShell(Remove-Item *)` / `PowerShell(Clear-Content *)` | ファイルを消す・中身を空にする命令。PowerShell の別名（del / rm など）も、元の命令の名前に直して照らされます（公式） |
| 外に送る | `Bash(curl *)` / `Bash(wget *)` / `Bash(scp *)` / `Bash(ssh *)` / `Bash(osascript *)` / `PowerShell(Invoke-WebRequest *)` / `PowerShell(Invoke-RestMethod *)` / `PowerShell(Send-MailMessage *)` / `git push` | ネットへ送る・メールを送る・別の機械へ運ぶ命令 |
| 機械そのもの | `Bash(sudo *)` / `PowerShell(Format-Volume *)` / `PowerShell(Restart-Computer *)` / `PowerShell(Stop-Computer *)` / `PowerShell(Set-ExecutionPolicy *)` | 管理者の操作・ディスクの初期化・再起動・停止・実行の決まりの変更 |
| 入れる | `PowerShell(winget *)` / `PowerShell(Install-Module *)` / `Bash(npm install -g*)` / `PowerShell(Start-Process *)` | ほかのソフトを入れる・別のプログラムを起こす |
| 別の道 | `Bash(cmd *)` / `Bash(powershell *)` / `Bash(pwsh *)` / `PowerShell(cmd *)` | 禁止を避けて、別の窓の命令で同じことをする道 |
| 戻せない git | `git reset --hard` / `git clean` | 作業の跡を戻せない形で消す |
| まとめて入れる git | `Bash(git add -A*)` / `Bash(git add .*)` / `PowerShell(git add -A*)` / `PowerShell(git add .*)` | 全部を一度に履歴へ入れる命令。入れない物（`.gitignore` の一覧）が混ざるのを防ぐ。`git add ./` のような書き換えは止まりません（§4） |

## 4. この設定で止まらないもの（正直に）

- 禁止は «命令の名前» で照らします。名前を変えた別の道（たとえば、プログラムの中から消す）は、この一覧では止まりません。
- うちが Windows で試したときは、別の道は «おまかせ» なら裏の見張りが止め、«毎回確認» なら確認の画面が出ました。設定で止めたのではありません。
- 確実に機械で止めたいときは、«関所»（命令を実行の前に調べる小さなプログラム）を足します。この種はプログラムを入れない決まりなので、関所は有料の派遣で入れています。
- 頭に `!` を付けてあなたが打つ命令は、禁止も通りません。
- `git add -A` / `git add .` の禁止は «書き方» で照らします。`git add ./` や `git add -- .` のように書き換えると止まりません。送る物の一覧を見せる決まり（`CLAUDE.md` §2 の 5）で守る層です。

## 5. 「毎回確認」を選んだとき

はじめの聞き取りで «毎回確認» を選ぶと、相棒が `onboarding/settings.proposed.json` に、`permissions` の中へ次の 1 行を足した案を書きます。

```json
"defaultMode": "default"
```

あなたが 1 行の命令でそれを `.claude/settings.json` に置き換えると、次の起動からずっと «毎回確認» で始まります。
«おまかせ» はこのフォルダの設定に書いても効かない（公式）ので、«おまかせ» を選んだときは何も変えません。
