# 学習会 Firebase+Flutter 2021年11月13日

``PS>`` は PowerShell のプロンプトを表す。　Linux　や　Mac　はシェル上で同等のことができる。

``$`` は bash のプロンプトを表す。 Windows で WSL を利用しない場合は Git bash や PowerShell 上でも可。

## 開発環境

Windows 10

[Chrome](https://www.google.co.jp/chrome/)

https://docs.microsoft.com/ja-jp/windows/wsl/install

```
PS> wsl --list --online
PS> wsl --install -d Ubuntu
```

```
$ sudo apt update
$ sudo apt upgrade
$ git --version
git version 2.25.1

$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/michinobu/.ssh/id_rsa):
Created directory '/home/michinobu/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:

$ git config --global user.name "Michinobu Maeda"
$ git config --global user.email "michinobumaeda@gmail.com"
$ cat .ssh/id_rsa.pub
ssh-rsa AAA...Lpdc= michinobu@lemon
```

公開鍵 ``id_rsa.pub`` を GitHub に登録する。

注意： 秘密鍵 ``id_rsa`` はPCの外に持ち出したり他人に見せたりしてはダメ。

https://flutter.dev/docs/get-started/install/linux

```
$ wget https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_2.5.3-stable.tar.xz
$ tar xf ../flutter_linux_2.5.3-stable.tar.xz
$ ls flutter
```
``~/.bashrc`` に以下の行を追加する。

```
export PATH="/home/michinobu/flutter/bin:$PATH"
````

注意：Windows版の Flutter と競合する。


```
$ . .bashrc
$ flutter --version
Flutter 2.5.3

$ flutter doctor

$ flutter upgrade
$ sudo apt install openjdk-17-jdk
$ java --version
openjdk 17 2021-09-14
```

https://github.com/nvm-sh/nvm/blob/master/README.md

```
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
$ . .bashrc
$ nvm --version
$ nvm ls-remote --lts | grep Latest
         v4.9.1   (Latest LTS: Argon)
        v6.17.1   (Latest LTS: Boron)
        v8.17.0   (Latest LTS: Carbon)
       v10.24.1   (Latest LTS: Dubnium)
       v12.22.7   (Latest LTS: Erbium)
       v14.18.1   (Latest LTS: Fermium)
       v16.13.0   (Latest LTS: Gallium)

$ nvm install 14.18.1
$ nvm use 14
Now using node v14.18.1 (npm v6.14.15)

$ node --version
v14.18.1

$ npm --version
6.14.15

$ npm -g i yarn firebase-tools
$ yarn --version
1.22.17

$ firebase --version
9.22.0
```

https://code.visualstudio.com/

インストール後に PATH を反映するために再起動する。

## Flutter のプロジェクトの作成

```
$> flutter create --platforms=web ccuflutterpj20211113
$> code ccuflutterpj20211113
```

VS Code でターミナルを開く

```
$> flutter run -d chrome --web-port 5000
```

```
$ eval `ssh-agent`
$ ssh-add
```

## GitHub 初期登録

GitHub
<https://github.com/MichinobuMaeda>

- Repositories
    - New
        - Repository name: ccuflutterpj20211113

```
$ git init
$ git add .
$ git commit -m "first commit"
$ git branch -M main
$ git remote add origin git@github.com:MichinobuMaeda/ccuflutterpj20211113.git
$ git push -u origin main
```

```
PS> git clone git@github.com:MichinobuMaeda/ccuflutterpj20211113.git
PS> flutter pub get
PS> flutter run -d chrome --web-port 5000
```

## プラットフォーム Android の追加

```
$ flutter create --platforms=android .
$ flutter run -d emulator-5554
```

## Git pull request

```
$ git checkout -b changethemecolor
$ git branch 
* changethemecolor
  main
$ git status
$ git add .
$ git commit -m "テーマカラー変更"
$ git push
```

## Firebase のプロジェクトの作成

https://console.firebase.google.com/

- Add project
    - Project nane: ccuflutterpj20211113
    - Configure Google Analytics
        - Create a new account: ccuflutterpj20211113
        - Analytics location: Japan
- Project settings
    - Your apps
        - Select a platform to get started: </> (Web)
            - App nickname: Flutter test 20211031
    - Your project
        - Default GCP resource location: asia-northeast1 (Tokyo)
        - Public-facing name: ccuflutterpj20211113
        - Support email: my address
- Authentication
    - Sign-in providers
        - Email/Password: Enable
            - Email link (passwordless sign-in): Enable
    - Templates
        _ Template language: Japanese
- Firestore Database
    - Create Database: Start in production mode

```
$ firebase login

$ firebase init hosting
? What do you want to use as your public directory? build/web
? Configure as a single-page app (rewrite all urls to /index.html)? No
? Set up automatic builds and deploys with GitHub? No

$ firebase init hosting:github
? For which GitHub repository would you like to set up a GitHub workflow? (format: user/repository) MichinobuMaeda/ccuflutterpj20211113
? Set up the workflow to run a build script before every deploy? Yes
? What script should be run before every deploy? yarn install --frozen-lockfile && yarn build
? Set up automatic deployment to your site's live channel when a PR is merged? Yes
? What is the name of the GitHub branch associated with your site's live channel? main
i  Action required: Visit this URL to revoke authorization for the Firebase CLI GitHub OAuth App:
https://github.com/settings/connections/applications/89cf50f02ac6aaed3484
i  Action required: Push any new workflow file(s) to your repo
```

## サンプルアプリのデプロイ

GitHub Actions 上で firebase-tools を利用するために Node.js の設定を追加。　

```
$ npm init
$ yarn -D add firebase-tools
```

GitHub Actions のワークフロー

- .github
    - workflows
        - ``firebase-hosting-merge.yml``
        - ``firebase-hosting-pull-request.yml``

に、以下のタスクを追加する。

- Flutter
    - flutter コマンドのインストール
    - ``pubspec.yaml`` に指定されたパッケージの追加
    - ビルド ( Web )
- Node.js
    - node コマンド ( version: 14 ) のインストール
    - yarn コマンドのインストール
    - ``package.json`` に指定されたパッケージの追加

GitHub　に push する。　

```
$ git add .
$ git commit -m "修正内容等をここに書く。日本語可。"
$ git push
```

GitHub Actions の [ジョブ](https://github.com/MichinobuMaeda/ccuflutterpj20211113/runs/4186212125?check_suite_focus=true)
が自動で起動する。ここで使われるのは ``firebase-hosting-merge.yml`` の方。

Firebase Hosting の本番用の場所 <https://ccuflutterpj20211113.web.app/> にアプリがデプロイされる。

## Pull Request 

Pull Request 用のブランチを作成する。今回はテーマカラーの変更ということで ``changethemecolor`` .

```
$ git checkout -m changethemecolor
$ git branch
* changethemecolor
  main
```

``main.dart`` を編集してテーマカラーを変更する。

```
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
```

```
$ git add .
$ git commit -m "テーマカラー変更"
$ git push --set-upstream origin changethemecolor
```

GitHub に "Compaire and pull request" というボタンが表示されるので（設定によっては日本語かもしれない）、クリックして

GitHub Actions の [ジョブ](https://github.com/MichinobuMaeda/ccuflutterpj20211113/runs/4186400074?check_suite_focus=true)
が自動で起動する。ここで使われるのは ``firebase-hosting-pull-request.yml`` の方。

Firebase Hosting のプレビュー用の場所 <https://ccuflutterpj20211113--pr2-changethemecolor-tp6u6scu.web.app/> にアプリがデプロイされる。この URLは毎回変わる。 Actions のページにジョブからの出力として表示されている。メールでも通知される。

Pull Request は権限のある人が ``main`` にマージする。
マージは GitHub の Web 上でできる。コマンドでマージする場合は以下の通り。

```
$ git checkout main
$ git branch
  changethemecolor
* main

$ git merge changethemecolor
$ git push
```

GitHub Actions のジョブが自動で動いて本番用のURL https://ccuflutterpj20211113.web.app にデプロイされる。
PWA ( Progressive Web Apps ) としてローカルにキャッシュされている場合、
PC　のブラウザは [Ctrl]+[Shift]+[R] 等でページを更新する必要がある。
スマホやタブレットの場合は PWA の登録を消去するプログラムが必要。

## Firestore

サンプルのカウンターを Firebase の Firestore から取得するデータに差し替える。

修正用のブランチを作成する。

```
$ git checkout -b addfirebase
```

修正を push する。

```
$ git add .
$ git commit -m "サンプルのカウンターを Firebase の Firestore から取得するデータに差し替え"
$ git push --set-upstream origin addfirebase
```

GitHub 上で Pull Request する。

GitHub Actions が自動で起動するので、終了したらプレビュー用のデプロイ先
<https://ccuflutterpj20211113--pr3-addfirebase-xcksgjz3.web.app/>
を参照する。

バグっていたので修正。

```
$ git add .
$ git commit -m "bug fix: ボタンを押したときの処理を入れ忘れていた"
$ git push
```

該当ブランチで一度　Pull Request すると、その後は push するだけで自動で Actions　が起動する。

Firestore のアクセス件の設定を忘れていた。

```
$ firebase init firestore
? What file should be used for Firestore Rules? firestore.rules
? What file should be used for Firestore indexes? firestore.indexes.json
```

``firestore.rules`` にコレクション　``counters`` の読み書き権限を追加。まだ
Actions にこれを本番環境に反映する処理を入れていないので手動で実施する。　

```
$ firebase deploy --only firestore
```

変更を push する。

```
$ git add .
$ git commit -m "bug fix: ボタンを押したときの処理を入れ忘れていた"
$ git push
```

Firestore　を使う前と同じように動作する。

[Firebase のコンソール](https://console.firebase.google.com/project/)
で値を編集すると、即時に反映される。

Chrome と Firefox を並べて表示して、どちらか一方でボタンをクリックすると、両方の表示が更新される。
