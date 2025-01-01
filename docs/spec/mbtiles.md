# MBTiles 仕様

MBTilesとは、Mapboxが作成した[タイル](./tile.md)を格納するためのストレージ仕様です。

[MBTiles Specification](https://github.com/mapbox/mbtiles-spec)

[SQLite](https://sqlite.org/index.html)を用いており、1つのファイルで多くのタイルを格納することができ、また転送時のコストもタイルに比べて少なくなります[^1]。

## 構成

ここでは[OpenMapTiles](../generate_vector_tile/openmaptiles.md)が出力するmbtilesを例に解説をします。

まず、SQLiteの構成から確認をします。これは`sqlite3`コマンドを用います。

```bash
sqlite3 data/tiles.mbtiles
```

`.tables`コマンドで中身を確認します。

```sql
sqlite> .tables
geocoder_data  grid_key       grids          keymap         metadata
grid_data      grid_utfgrid   images         map            tiles
```

このうち、実際に利用されるのは`tiles`と`metadata`となります。他にも`grids`などがありますが、これは[UTFGrid Specitication](https://github.com/mapbox/utfgrid-spec)に利用されているもので、実際のところ使うことはないです[^2]。

### tiles テーブル/ビュー

OpenMapTilesでは`tiles`はViewとして実装されています。

```sql
sqlite> .schema tiles
CREATE VIEW tiles AS
    SELECT
        map.zoom_level AS zoom_level,
        map.tile_column AS tile_column,
        map.tile_row AS tile_row,
        images.tile_data AS tile_data
    FROM map
    JOIN images ON images.tile_id = map.tile_id
/* tiles(zoom_level,tile_column,tile_row,tile_data) */;
```

アプリケーションから呼び出す側では特にテーブルかビューかを気にする必要はありません。

[^1]: 大量のタイルを転送するケースではOS側のファイルの処理に多くの負担がかかることがある。例えば100万タイルぐらいあるだけでファイルコピーだけでも恐ろしく時間がかかるなど。
[^2]: きっぱりと書いていますが、本当に使ったことないのでしょうがない。
