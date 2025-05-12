## オプション : ArcGIS Online にフィーチャ レイヤーを公開

ハンズオンでは実施しませんが、オプションとして作成したフィーチャ レイヤーを ArcGIS Online に公開する方法をご紹介します。お時間のある方はチャレンジしてみてください。

1. ArcGIS Pro が公開先の ArcGIS Online アカウントでサイン インされていることを確認します。コンテンツを作成し公開する権限があるアカウントである必要があります。

2. 下記のコードを Python ウィンドウに入力し、Enter キーを 2 回押下して実行します。

```Python
# プロジェクト ファイルのオブジェクト取得
aprx = arcpy.mp.ArcGISProject("CURRENT")

# マップのオブジェクト取得
maps = aprx.listMaps()[0]

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

上記のコードは、放置車両レイヤーを ArcGIS Online に公開しています。
ArcGIS Online 上に公開するには、中間ファイルとなる、サービス定義ドラフトファイルとサービス定義ファイルを作成した後にアップロードすることができます。詳細は [Web レイヤーとサービスの共有の自動化](https://pro.arcgis.com/ja/pro-app/latest/tool-reference/server/an-overview-of-the-publishing-toolset.htm#ESRI_SECTION1_3A66E57F2C604C0C85F648C8746B1E69) をご参照ください。


まず、[Map クラス](https://pro.arcgis.com/ja/pro-app/latest/arcpy/mapping/map-class.htm) の getWebLayerSharingDraft メソッドと [FeatureSharingDraft クラス](https://pro.arcgis.com/ja/pro-app/latest/arcpy/sharing/featuresharingdraft-class.htm) の exportToSDDraft メソッドを組み合わせてサービス定義ドラフトファイルを作成し、その後 [サービスのステージング](https://pro.arcgis.com/ja/pro-app/latest/tool-reference/server/stage-service.htm) ツールと [サービス定義のアップロード](https://pro.arcgis.com/ja/pro-app/latest/tool-reference/server/upload-service-definition.htm) ツールを使用して ArcGIS Online へアップロードしています。


![alt text](image07.png)