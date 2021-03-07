# tileserver-glのローカル設定

Maputnikで参照されるURL参照元が

ダウンロードしたJSONファイルをtileserver-glに反映させる場合は、ダウンロードしたJSONファイルのコピーを作成してから以下の項目を書き換えます。

```diff
   "sources": {
     "openmaptiles": {
       "type": "vector",
-      "url": "https://tile2.openstreetmap.jp/data/japan.json"
+      "url": "mbtiles://{japan}"
     }
   },
-  "sprite": "https://tile2.openstreetmap.jp/styles/osm-bright/sprite",
-  "glyphs": "https://tile2.openstreetmap.jp/fonts/{fontstack}/{range}.pbf",
+  "sprite": "{style},
+  "glyphs": "{fontstack}/{range}.pbf",
   "layers": [
     {
       "id": "background",
```

書き換える箇所は

- url
- sprite
- glyphs

の三ヶ所となります。
これらは全てサーバ上に存在しており、またローカルのファイルを参照するようになるため、
ラスタレンダリングの高速化には必須となります。

一般的にはMaputnikの編集用のJSONをメンテナンスして、tileserver-glにデプロイする時のみコピーを作成して上記の書き換えを行います。

