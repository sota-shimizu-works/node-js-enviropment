# NodeJS with Docker

Docker で nodeJS 環境を利用するためリポジトリです。
使用するためには Docker 及び Docker-desctop が必要です。

## Docker-desctop のインストール

[ダウンロードページ](https://www.docker.com/ja-jp/products/docker-desktop/"Docker-desctop")から Docker desctop をインストールしてください。

### WSL2 の有効化

インストーラー起動時、ホストマシンが Windows の場合は Configuration で Hyper-v の代わりに WSL2 を使用するかのオプションがあります。これにチェックを入れてインストールしてください。
インストールが完了すると再起動があり、最後に WSL2 の自動更新設定がありますが、任意で設定してください。

### Docker-desctop の起動

インストールなど完了し、アンケートなどが終わったあとアプリケーションが起動します。
アプリケーションが起動すると Docker Hub のログインが出てきますが、使用しないのでサインインせず続行で大丈夫です。

### 初回以降の起動

初回以降は Docker-desctop のアプリケーションを起動するだけで、Docker 環境が利用できます。何らかの理由でアプリケーションが終了した場合は、再起動する必要があります。

## リポジトリの使用

このリポジトリには NodeJS を Docker で使用するための Yml ファイル(設定 Json)があり、コマンドを叩くことで NodeJS 環境の Docker をセッティングできます。

### イメージのビルド

Docker-desctop には Docker compose が内包されています。この Docker compose を使用することで yml や Docker ファイルに記述した環境をすぐにビルドしたりできます。

```bash
$ cd node-js-docker
```

まずは NodeJS を利用できるようにイメージをビルドします。Docker-compose を利用する場合、基本的には yml ファイルと同じディレクトリでコマンドを実行するので`node-js-docerディレクトリ`へ移動します。

```bash
$ docker-compose build
```

yml ファイルに記述してある内容をビルドする場合のコマンドです。yml の内容および、yml にリレーションされた Docker ファイルからイメージをビルドします。
ここでエラーが出る場合の多くは、ホストマシンの CPU の種類が対応していないためイメージが利用できないなどです。

```
FROM node:20.11.1-alpine
```

このリポジトリは上記のイメージを利用しています。この node alpine はマルチプラットフォームに対応しているので基本的な環境では問題ないですが、必要に応じて変更に必要があります。

### イメージの再ビルド、コンテナの起動

```bash
$ docker-compose up -d
```

`up`コマンドはイメージの再ビルドとコンテナの起動を行います。`build`コマンドはプルしてきた際や、yml,Dockerfile 変更時に叩くコマンドであり、以降 Docker を利用したい場合はこの`up`コマンドを利用してコンテナを立ち上げます。

## コンテナの利用

Dokcer はコンテナと呼ばれる構成単位で仮想環境を作成して利用します。このリポジトリは nodeJS を動かすのみなので１つのコンテナしかないですが、拡張してデータベースなどが増えればその数だけコンテナが増えていきます。仮想環境内でコマンドを叩いたりしたい場合は都度コンテナへ入りコマンドを入力する必要があります。

```bash
$ docker exec -it node-js sh
```

コンテナに入る場合は上記`exec`コマンドで入ることができます。`npm`や`npx`コマンドを利用したい場合はコンテナ内で実行します。

## マウントディレクトリ

仮想環境(コンテナ)にあるファイルをローカルマシンで操作するためにマウントを行います。このマウントは基本的には yml ファイルに設定を入れ、ビルド時にマウント設定を同時に行います。
このリポジトリの場合は、nodeJS サーバーの app ディレクトリをリポジトリのルート直下の app ディレクトリにマウントしています。
なので、ルート直下でファイル操作を行うとそのままコンテナ内に反映される仕組みになっています。react などのファイルを作成する場合は app ディレクトリで作成してください。

## エラー

### Cannot connect to the Docker daemon at unix

```
Cannot connect to the Docker daemon at unix:///Xxxx/yyyy/.docker/run/docker.sock. Is the docker daemon running?
```

Docker デーモンにアクセスできない際に出るエラーです。Docker が起動していない場合が多いです。
