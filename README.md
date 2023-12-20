# git-hooks-gitleaks-old

リポジトリ作り直したのでこれはもう使わない。こっちが新しい方
https://github.com/SonicGarden/git-hooks-gitleaks


gitleaksをgithooksのpre-commitで実行するための仕組み

# gitleaks git-hooksのインストール

参考： https://github.com/gitleaks/gitleaks
## 1.  前提条件
git 2.9以上をインストールしておいてください

## 2. gitleaksインストール
```
brew install gitleaks
```
インストール確認
```
gitleaks version
```
バージョンが表示されればインストールOK
## 3. commit時にgitleaksでチェックするためのgithooksを設定する
```
cd ~
git clone https://github.com/sonicgarden/git-hooks-gitleaks git-hooks
cd git-hooks
git config --global core.hooksPath $(pwd)/hooks
```
※既にsecretlintをインストール済みなどで「$(pwd)/hooks」が存在している場合は事前にディレクトリを削除してください

~/.gitconfig に以下のような設定が追加されます
[core]
   hooksPath = /Users/xxx/src/github.com/SonicGarden/git-hooks-gitleaks/hooks

## 4. 動作確認

適当なプロジェクトでキーをコミットしてみる

```sh
echo "sg-gitleaks-test = AKIAIMAAXAAXAAXAXAAA" > secret_key_test
git add secret_key_test
git commit
```

以下のようなERRORが出ればOK
※このリポジトリの設定ファイルが参照されている場合は "RuleID: sg-gitleaks-test" が検知されるはず

```
  ▶ Check credentials by gitleaks

    ○
    │╲
    │ ○
    ○ ░
    ░    gitleaks

Finding:     sg-gitleaks-test = AKIAIMAAXAAXAAXAXAAAA
Secret:      AKIAIMAAXAAXAAXAXAAA
RuleID:      aws-access-token
Entropy:     1.670951
File:        secret_key_test
Line:        1
Fingerprint: secret_key_test:aws-access-token:1

Finding:     sg-gitleaks-test = AKIAIMAAXAAXAAXAX...
Secret:      sg-gitleaks-test
RuleID:      sg-gitleaks-test
Entropy:     3.030639
File:        secret_key_test
Line:        1
Fingerprint: secret_key_test:sg-gitleaks-test:1

3:10PM INF 1 commits scanned.
3:10PM INF scan completed in 17.1ms
3:10PM WRN leaks found: 2
```

# FAQ
- 誤検知をスキップしたい

```
git commit --no-verify
```
でコミットすれば、pre-commitフックが動かないので、チェックをスキップできます。

- 既存コードをすべて再チェックしたい

```
gitleaks detect --source . -v -c `git config --get core.hookspath`/gitleaks.toml
```
