# GISコミュニティフォーラムセッション用コード
## <演習1>

``` Python
# ArcPy サイト パッケージをインポート
import arcpy

# ジオプロセシング ツールで利用する変数を定義
csv = r"C:\data\HandsOn.csv"
x = "X"
y = "Y"
layer = "PointLayer"
outpath = r"C:\data\ArcGISPro\arcpy.gdb\放置車両"

# CSV ファイルから XY イベント レイヤー（テンポラリのオブジェクト）を作成
arcpy.management.MakeXYEventLayer(csv, x, y, layer)

# XY イベント レイヤーをフィーチャ クラスとして出力
arcpy.conversion.ExportFeatures(layer, outpath)

# XY イベント レイヤーを削除
arcpy.management.Delete(layer)
```

## <演習2>
``` Python
# フィーチャクラスに LONG 型の total フィールドを追加
arcpy.management.AddField("放置車両","total","LONG")

# フィーチャクラスから取得するフィールド名のリストを作成
fields = ['total', 'bicycle', 'scooters', 'motorcycle']

# 繰り返し処理をしながら値を更新
with arcpy.da.UpdateCursor("放置車両", fields) as cursor:
    for row in cursor:
        row[0] = row[1] + row[2] + row[3] # bicycle と scooters と motorcycle の合計値
        cursor.updateRow(row) # 合計値の値を適用
```

## <演習3>
``` Python
# 現在開いているプロジェクト ファイルのオブジェクトを取得
aprx = arcpy.mp.ArcGISProject("CURRENT")

# マップのオブジェクト取得
maps = aprx.listMaps("マップ")[0]

# レイヤーのオブジェクト取得
lyrs =maps.listLayers()

# 放置車両レイヤーのシンボル更新
for lyr in lyrs:
    if lyr.name == "放置車両":
        sym = lyr.symbology
        sym.updateRenderer("GraduatedColorsRenderer") # 等級色
        sym.renderer.classificationField = "total"  #分類するフィールド名
        sym.renderer.classificationMethod = "Quantile" # 等比間隔
        sym.renderer.breakCount = 5 # クラス
        lyr.symbology = sym
```

## ＜演習４の前＞
``` Python
# レイヤーファイルを利用してシンボルを変更
arcpy.management.ApplySymbologyFromLayer("放置車両", r"C:\data\ArcGISPro\レイアウト用レイヤーファイル.lyrx")
```

## <演習4>
``` Python
# プロジェクト ファイルのオブジェクト取得
aprx = arcpy.mp.ArcGISProject("CURRENT")


# レイアウトを取得 「レイアウト」の方を取得
layout = aprx.listLayouts("レイアウト")[0]

#　ページ レイアウト設定内容を PDF へ出力
layout.exportToPDF(r"C:\data\output\Sample.pdf")
```

## <演習マップシリーズ>
``` Python
# プロジェクト ファイルのオブジェクト取得
aprx = arcpy.mp.ArcGISProject("CURRENT")
 
# レイアウトを取得
layout = aprx.listLayouts("マップシリーズ")[0]
 
# レイアウトからマップ シリーズを取得
ms = layout.mapSeries
 
# マップ シリーズのエクスポート
ms.exportToPDF(r"C:\data\output\mapseries.pdf")
```


## <オプション演習：アップロード>

``` Python
# プロジェクト ファイルのオブジェクト取得
aprx = arcpy.mp.ArcGISProject("CURRENT")

# マップのオブジェクト取得
maps = aprx.listMaps("マップ")[0]

# レイヤーを取得
lyrs = maps.listLayers("放置車両")[0]

# 公開に必要なパラメーターを設定
output = r"C:\data\output" # ファイル出力先
sddraft_output = output + r"\upload.sddraft" # サービス定義ドラフトファイル
sd_output = output + r"\upload.sd" # サービス定義ファイル

# サービス定義ドラフトファイルの作成
sharing_draft = maps.getWebLayerSharingDraft("HOSTING_SERVER","FEATURE","放置車両", lyrs)
sharing_draft.exportToSDDraft(sddraft_output)

# サービス定義ファイルの作成
arcpy.server.StageService(sddraft_output, sd_output)
arcpy.server.UploadServiceDefinition(sd_output, "HOSTING_SERVER")

```