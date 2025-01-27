# Glyphs URLについて

`Glyphs URL`はフォントを扱うためのURLとなります。

`https://tile.openstreetmap.jp/fonts/{fontstack}/{range}.pbf`

上記URLのうち、`{fontstack}`がフォント名に、`{range}`がフォントの実体を参照するような仕組みになります。
また、フォントは `{range}`ごとに `Protocol Buffer` 形式にされたものが参照されます。

また、 MapLibre Style のフォントサーバでは１つのPOIに複数のフォントを使うことができます。
ただし、この場合複数のフォントを一度に参照するURLを参照します。

例: `https://tile.openstreetmap.jp/fonts/Klokantech%20Noto%20Sans%20Regular,Klokantech%20Noto%20Sans%20CJK%20Regular/36096-36351.pbf`

この場合は tileserver-gl がフォントのURLを受け取ると、 `Klokantech Noto Sans Regular`(欧米フォント) と
`Klokantech Noto Sans CJK Regular`(CJKフォント) の２つを参照して、
前者を優先的にしたフォントを返すことで英語などの文字には欧米フォントを、
日本語などの文字にはCJKフォントを、という風にフォントをあわせて使うことが可能になっています。

なお、 tileserver-gl 以外でフォントを配布する場合は、
上記のようなURLが生成されるため、フォントを２つ以上使わないようにする必要があります。
また、ラスタレンダリングのためのフォントの取得を外部サーバを参照する必要があるため、
あまりオススメはできません。

新しいフォントファイルを作成する場合には以下のように行います(注: 古いNode.jsが必要になります)。

```sh
npm -g install genfontgl
genfontgl (対象のフォント) (出力先ディレクトリ)
```

出来上がったフォントをディレクトリごと `tileserver-gl` の `fonts` ディレクトリにコピーして、
`tileserver-gl`を再起動してください。
