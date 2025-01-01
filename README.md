# はじめに

本ドキュメントは`Mapbox Vector Tile`について以下の項目などを扱う。

- 作成技術(OpenMapTiles, 国連ベクトルタイルツールキットなど)
- ベクタタイル配信技術
- ラスタタイル配信技術
- スタイリング
- データフォーマット

本ドキュメントの目的は`Mapbox Vector Tile`に対する知見の共有を行うことである。

## 著者について

現時点の著者は以下の通り。

- Taro Matsuzawa: OSGeo.JP/OSMFJ/jus/Georepublic [twitter](https://twitter.com/smellman)

## 本レポジトリについて

本レポジトリは [HonKit](https://github.com/honkit/honkit/)を用いてドキュメントをビルドしています。

ローカルでの閲覧及びビルドは以下の手順で行えます。

```sh
git clone https://github.com/smellman/vector_tile_book.git
cd vector_tile_book
npm install
npm run serve
npm run build
```

なお、Pull Requestを送る前には必ずmarkdownlintを実行してください。

```sh
npm run lint
```
