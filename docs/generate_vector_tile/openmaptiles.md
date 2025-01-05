# OpenMapTiles

本項ではopenmaptilesを利用して簡単にベクトルタイルのバイナリ(.mbtile形式)を作成するところまでを解説します。

## ターゲット

- OpenMapTiles v3.15

## ハードウェア

- 64bit CPU
- 4GBメモリ以上
- 30GB以上のディスクスペース

## ソフトウェア

共通して必要なもの

- Docker
- Docker Compose V2プラグイン
- git
- make

`quickstart.sh`で利用されるもの

- bc
- md5sum

## セットアップ

openmaptiles の `quickstart.sh` の実行に必要なコマンドをインストールします。

### Ubuntu or Debian

```bash
sudo apt install git make bc
```

### Mac

```bash
sudo xcode-select -s /Applications/Xcode.app
xcode-select --install
brew install coreutils
```

なお、 `xcode-select` はCommand Line Toolsをインストールするもので、直接Xcodeを起動していた場合は必要ない可能性があります。また、アップデートはSoftware Updateで自動的に行われます。

また、 `md5sum` は必須ではないが `quickstart.sh` を実行後に停止してしまうのでインストールを推奨。

### openmaptiles

最初にopenmaptilesをgithubから `git clone` して、ターゲットとなるバージョンを指定します。

```bash
git clone https://github.com/openmaptiles/openmaptiles.git
cd ./openmaptiles
git checkout -b v3.15 refs/tags/v3.15
```

次に必要なdocker imageを取得します。

```bash
docker compose pull
```

あとは動作チェックのために、`quickstart.sh`を実行します。

```bash
./quickstart.sh
```

これでセットアップは完了です。

## タイルの作成

openmaptilesでは`quickstart.sh`を通して以下の作業をします。

- インポート先となる PostgreSQL のインスタンスを起動
- OpenStreetMapのデータを[geofabrikのミラー](http://download.geofabrik.de)からダウンロード
- layersの定義に沿って[imposm3](https://github.com/omniscale/imposm3)を使ってOSMデータをインポート
- PostgreSQLでプロセッシング
- `Mapbox Vector Tile` を生成して `data/tiles.mbtiles` へ格納

また、デフォルトではズームレベル 7 のタイルまでしか作成しないため、事前に `.env` にある`QUICKSTART_MAX_ZOOM`の値を変更しておく必要があります。

```bash
vi .env
```

ズームレベル14まで生成する場合は、差分は以下のようになります。

```diff
diff --git a/.env b/.env
index 0ef9a89..6fafc98 100644
--- a/.env
+++ b/.env
@@ -20,7 +20,7 @@ BBOX=-180.0,-85.0511,180.0,85.0511

 # Which zooms to generate with   make generate-tiles-pg
 MIN_ZOOM=0
-MAX_ZOOM=7
+MAX_ZOOM=14

 # `MID_ZOOM` setting only works with `make generate-tiles-pg` command.  Make sure MID_ZOOM < MAX_ZOOM.
 # See https://github.com/openmaptiles/openmaptiles-tools/pull/383
```

あとは `quickstart.sh` を使ってタイルを作成します。

```bash
./quickstart.sh {region}/{country_name}
```

日本のデータを作成する場合は以下のように行います。

```bash
./quickstart.sh asia/japan
```

作成可能な国名の一覧(geofabrickのミラーの一覧)は以下のコマンドで取得できます。

```bash
make list-geofabrik
```

また、 `quickstart.sh` 実行後には `data/tiles.mbtiles` というファイルが作成されます。

### タイルの再作成

最新のデータを使ってタイルを再度作成する場合は一度作成したタイルや設定ファイルを削除する必要があります。

```bash
cd openmaptiles
git clean -dfx
./quickstart.sh japan
```
