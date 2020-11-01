# ベクトルタイルサーバの構築手順

本項では[OpenMapTiles](../generate_vector_tile/openmaptiles.md)を使って作成したベクトルタイルのバイナリ(.mbtile形式)を用いてベクトルタイルサーバを構築する手順を解説します。

ラスタタイルサーバの構築手順については別途解説をします。

## ターゲット

### ハードウェア

- Ubuntu 18.04
- 64bit CPU
- 4GBメモリ以上

### ソフトウェア

- Docker
  - `tileserver-gl`イメージ

インターネット上で公開するためにはreverse proxyが必須となります。

## ベクトルタイルのバイナリのコピー

ベクトルタイルのバイナリをコピーをします。

今回は[OpenMapTiles](../generate_vector_tile/openmaptiles.md)の項目にて作成した `data/tiles.mbtiles`を構築するサーバ上にコピーします。

```
scp data/tiles.mbtiles hogehoge@example.com:~/
```

今回は `example.com` の `hogehoge` ユーザで配信サーバを作成することとします。

## サービスの確認

まず、Dockerを使って ```tileserver-gl``` が起動するかを確認します。

サーバにログインして、mbtilesファイルがあるディレクトリで以下のようにして ```tileserver-gl ```のDocker イメージを起動します。

```bash
docker run -it -v $(pwd):/data -p 8080:80 klokantech/tileserver-gl --verbose
```

次に別のターミナルからサーバにログインして、curlコマンドなどで動作を確認します。

```bash
curl http://localhost:8080 | grep TileServer
```

grepにひっかかれば成功です。

## ```tileserver-gl``` の構成

tileserver-glでは以下のような構成となります。

```
.
├── config.json
├── fonts
├── sprites
├── styles
└── tiles.mbtiles
```

各ファイル、ディレクトリは以下のようになります。

- config.json: `tileserver-gl` の設定ファイル
- fonts: Protocol Buffer形式に変換されたフォントが格納したディレクトリ
- sprites: POIなどのアイコンファイルをCSS Spriteのような形式にした画像と参照用のjsonファイルを格納したディレクトリ
- styles: Mapbox GL Styleによるjsonファイルを格納したディレクトリ
- tiles.mbtiles: ベクトルタイルを格納したファイル

なお、ディレクトリの配置については後述するconfig.jsonで変更ができます。

### `tileserver-gl` のセットアップ

tileserver-glのdockerはcurrent directoryのみを参照します。
そのため、作業用のディレクトリを作成して、その中にセットアップをします。
[osm-bright-gl-style](https://github.com/openmaptiles/osm-bright-gl-style/tree/gh-pages)を用いてセットアップをします。

まずはディレクトリを作成し、`tiles.mbtiles`をコピーします。

```
mkdir tileserver
cd tileserver
cp ~/tiles.mbtiles .
```

次にフォントをダウンロードして展開します。

```
wget https://github.com/smellman/fonts/archive/gh-pages.zip
unzip gh-pages.zip
mv fonts-gh-pages fonts
rm gh-pages.zip
```

次に`sprite`画像を取得します。

```
mkdir sprites
cd sprites
wget https://raw.githubusercontent.com/openmaptiles/osm-bright-gl-style/gh-pages/sprite.json
wget https://raw.githubusercontent.com/openmaptiles/osm-bright-gl-style/gh-pages/sprite.png
wget https://raw.githubusercontent.com/openmaptiles/osm-bright-gl-style/gh-pages/sprite%402x.json
wget https://raw.githubusercontent.com/openmaptiles/osm-bright-gl-style/gh-pages/sprite%402x.png
cd ..
```

最後にスタイルを取得します。
    
```
mkdir styles
cd styles
wget https://raw.githubusercontent.com/openmaptiles/osm-bright-gl-style/gh-pages/style-local.json
mv style-local.json osm-bright.json
cd ..
```

### `tileserver-gl` の設定ファイル

config.json を編集することで、tileserver-glの設定や、配信されるスタイルとデータの組み合わせを表現することができます。

```
cd ~/tileserver
vim config.json
```

今回は以下のサンプルを作成します。

```
{
  "options": {
    "paths": {
      "root": "",
      "fonts": "fonts",
      "sprites": "sprites",
      "styles": "styles",
      "mbtiles": ""
    },
    "formatQuality": {
      "jpeg": 80,
      "webp": 90,
      "pngQuantization": false,
      "png": 90
    },
    "maxSize": 2048,
    "pbfAlias": "pbf",
    "serveAllFonts": true
  },
  "styles": {
    "osm-bright": {
      "style": "osm-bright.json"
    }
  },
  "data": {
    "v3": {
      "mbtiles": "tiles.mbtiles"
    }
  }
}
```

`options` はtileserver-glの基本的な構成を設定します。
`paths` ではディレクトリ構成を記述します。
`formatQuality` ではラスタタイル生成時のクオリティについて記述をします。
`maxSize` はラスタ画像の最大サイズを指定します。
なお、 `maxScaleFactor` というパラメータもあり、標準では `@3x suffix` まで対応しています。
基本的には通常のラスタタイルが 256x256 の画像で、 `@2x suffix` で 512x512 の画像になり、
`@3x suffix` で 768x768 の画像になります。

`styles` は実際に使うスタイルの識別子をキーにそのスタイルファイルのjsonファイル名などを与えます。

`data` はtileserver-glが配信または内部でラスタレンダリングに利用するベクトルタイルの情報を設定します。
こちらも `styles` 同様に識別子をキーにベクトルタイルを格納した `mbtiles` ファイル名を与えます。

設定ファイルの詳細は下記サイトを御覧ください。

https://tileserver.readthedocs.io/en/latest/config.html

## `tileserver-gl`を起動

`config.json`まで設定が終わったら`tileserver-gl`を起動します。

```
docker run -it -v $(pwd):/data -p 8080:80 klokantech/tileserver-gl
```

起動後にブラウザでタイルサーバにサクセスします。

[http://example.com:8080](http://example.com:8080)

表示されるのは`tileserver-gl`のフロントサイトです。ここからベクタタイル及びラスタタイルから参照することができいます。

最終的には `docker-compose` による自動起動やReverse Proxyを使って通常のhttps接続を行うことになると思います。
これらは後述するサーバ構築の項目を参照してください。