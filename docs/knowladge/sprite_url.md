# Sprite URL について

MapLibre GL StyleではPOI用のアイコンの画像を使う際には `Sprite URL` を設定しなくてはいけません。

Spriteとは CSS Sprite のような画像を指し、 MapLibre Style ではSpriteの位置をPOIなどと関連付けるため、
`sprite.json` というファイルを用意する必要があります。

実際に必要な構成は以下のようになります。

```bash
.
├── sprite.json
├── sprite.png
├── sprite@2x.json
└── sprite@2x.png
```

このうち、 `sprite.json` と `sprite.png` がペアに、
`sprite@2x.json` と `sprite@2x.png` が高解像度のペアとなります。

アイコンは全てSVGファイルで作成します。
そして、`spritezero`及びそのコマンドラインインターフェイスの`spritezero-cli`を使って`sprite.json`及び`sprite.png`のペアを作成します。

[spritezero](https://github.com/mapbox/spritezero/)

[spritezero-cli](https://github.com/mapbox/spritezero-cli)

なお、 `spritezero-cli` にいたってはmapboxが全く動いてくれず動かない状態で放置されているので、 [Pull Request](https://github.com/mapbox/spritezero-cli/pulls) から使えるものを利用する羽目になると思います。

今回の画像は作成は以下のようにして作成しています(注: 以下のコードは現在は動かないので注意)。

```bash
npm -g install @mapbox/spritezero-cli
git clone https://github.com/smellman/osm-bright-gl-style.git
cd osm-bright-gl-style
spritezero /tmp/sprite icons # sprite.jsonとsprite.pngのペア作成
spritezero --ratio=2 /tmp/sprite@2x icons # sprite@2x.jsonとsprite@2x.pngのペア作成
```

## SDFアイコンについて

SDFアイコンとは `icon-color` などで色が変更可能なアイコンを指します。
ただし、詳細な情報は明確にドキュメント化されていません。

[Support for SDF icons #97](https://github.com/mapbox/mapbox-gl-style-spec/issues/97)

まともな情報源は上記issueのtmcwの発言ぐらいで、

- SDFはSigned Distance Fieldの意味である
- 単色のラスタファイルのみ利用可能

ぐらいの情報しかありません。

ただし、基本的に単色のSVGファイルで構築されたアイコンに対して `spritezero` を実行したあとに、出力されたjsonファイルに `"sdf": true` という値を入れればよいことが分かっています。

`spritezero` では以下のようなJSONのハッシュとなります。

```json
"marker_11": {
  "height": 17,
  "pixelRatio": 1,
  "width": 17,
  "x": 289,
  "y": 115
},
```

ここにテキストエディタで `"sdf": true` を追加します。

```json
"marker_11": {
  "height": 17,
  "pixelRatio": 1,
  "width": 17,
  "x": 289,
  "y": 115,
  "sdf": true
},
```

これで `icon-color` が利用可能になります。

なお、 `spritezero-cli` は現状 SDFアイコンとして出力をする機能を持たないようなので注意してください。

また、 Mapbox自体は SDF アイコンを削除しようと考えていましたが、コミュニティからの反発があり実現されませんでした。

[Remove support for SDF icons #4118](https://github.com/mapbox/mapbox-gl-js/issues/4118)
