---
title: Pandas を使用して Azure BLOB ストレージ データの特徴を作成する | Microsoft Docs
description: Pandas Python パッケージを使用して Azure BLOB コンテナーに格納されているデータの特徴を作成する方法について説明します。
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 7d105e8e8cf67cd28c0be8a02b26913a25eed023
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Pandas を使用して Azure BLOB ストレージ データの特徴を作成する
このドキュメントでは、 [Pandas](http://pandas.pydata.org/) Python パッケージを使用して Azure BLOB コンテナーに格納されているデータの特徴を作成する方法について説明します。 まず Panda データ フレームにデータを読み込む方法を概説し、その後、Python スクリプトからインジケーター値を使用してカテゴリ特徴を生成し、特徴をビン分割する方法について説明します。

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

この **メニュー** は、多様な環境のデータの特徴を作成する方法が説明されたトピックにリンクされています。 このタスクは、 [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)の 1 ステップです。

## <a name="prerequisites"></a>前提条件
この記事は、Azure Blob Storage アカウントが作成済みで、そこにデータが格納されていることを前提とします。 アカウントの設定方法については、「[Azure Storage アカウントの作成](../../storage/common/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。

## <a name="load-the-data-into-a-pandas-data-frame"></a>Pandas データ フレームにデータを読み込む
データセットを探索および操作するには、BLOB ソースからローカル ファイルにダウンロードします。 次に、それを Pandas データ フレームに読み込みます。 この手順に必要な操作は次のとおりです。

1. BLOB サービスを使用する次の Python のサンプル コードによって、Azure BLOB からデータをダウンロードします。 次のコードの変数を、実際の値に置き換えます。
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. ダウンロードしたファイルから、Pandas データ フレームにデータを読み込みます。
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

これで、データを探索し、このデータセットでの特徴を生成する準備が整いました。

## <a name="blob-featuregen"></a>特徴の生成
次の 2 つのセクションでは、Python スクリプトを使用して、インジケーター値を持つカテゴリ特徴を生成し、特徴をビン分割する方法について説明します。

### <a name="blob-countfeature"></a>インジケーター値ベースの特徴の生成
カテゴリの特徴は、次のように作成できます。

1. カテゴリの列の分布を検査します。
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. 列の値ごとにインジケーター値を生成します。
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. インジケーター列と元のデータ フレームを結合します。
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. 元の変数自体を削除します。
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>ビン分割特徴の生成
ビン分割特徴を生成するには、次のように進めます。

1. 数値列をビン分割する列のシーケンスを追加します。
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. ビン分割を一連のブール型の変数に変換します。
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. 最後に、ダミー変数を元のデータ フレームと結合します。
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Azure BLOB にデータを書き戻して Azure Machine Learning で使用する
探索、サンプリング、および特徴化したデータを Azure Machine Learning でデータを使用するには、そのデータを Azure BLOB にアップロードします。 Azure Machine Learning Studio で追加の特徴を作成することもできます。 次の手順は、データをアップロードする方法を示しています。

1. ローカル ファイルへのデータ フレームの書き込み
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. 次のように、データを Azure BLOB にアップロードします。
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. これで、次のスクリーンショットに示すように、Azure Machine Learning の[データのインポート](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) モジュールを使用して BLOB からデータを読み取ることができます。

![リーダー BLOB](./media/data-blob/reader_blob.png)

