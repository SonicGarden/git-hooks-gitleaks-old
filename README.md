# git-hooks-gitleaks

gitleaksをgithooksのpre-commitで実行するための仕組み

# gitleaks git-hooksのインストール

参考： https://github.com/gitleaks/gitleaks
## 1.  前提条件
git 2.9以上をインストールしておいてください

※caulkingなどを使って gitleaksをインストールしているのであれば下記の2,3はできているはずなので4の動作確認ができていればOKです。

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
・適当なプロジェクトでキーをコミットしてみる
```
echo 'AKIAIMAAXAAXAAXAXAAA' > secret_key_test
git add secret_key_test
git commit
```
・以下のようなERRORが出ればOK
    
▶ Check credentials by gitleaks
    
    ○
    │╲
    │ ○
    ○ ░
    ░    gitleaks    
    Finding:     AKIAIMAAXAAXAAXAXAAAA
    Secret:      AKIAIMAAXAAXAAXAXAAA
    RuleID:      aws-access-token
    Entropy:     1.670951
    File:        secret_key_test
    Line:        1
    Fingerprint: secret_key_test:aws-access-token:1



# FAQ
- 誤検知をスキップしたい

git commit --no-verify

でコミットすれば、pre-commitフックが動かないので、チェックをスキップできます。

- 既存コードをすべて再チェックしたい

gitleaks detect --source . -v -c `git config --get core.hookspath`/gitleaks.toml

