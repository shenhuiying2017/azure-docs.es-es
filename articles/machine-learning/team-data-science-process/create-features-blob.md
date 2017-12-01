---
title: "Creación de características para los datos de Azure Blob Storage mediante Panda | Microsoft Docs"
description: "Cómo crear características para los datos almacenados en un contenedor de blobs de Azure mediante el paquete Panda de Python."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev;garye
ms.openlocfilehash: 7a2e64927f4afca87642fb4829166c5ec60dbc09
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Creación de características para los datos de almacenamiento de blobs de Azure mediante Panda
Este documento muestra cómo crear características para datos que se almacenan en el contenedor de blobs de Azure mediante el paquete de Python [Pandas](http://pandas.pydata.org/) . Después de esquematizar cómo cargar los datos en una trama de datos de Panda, se muestra cómo generar características de categorías mediante scripts de Python con valores de indicador y características de discretización, mediante scripts de Python.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Este **menú** vincula a temas en los que se describe cómo crear características para datos en diversos entornos. Esta tarea constituye un paso del [proceso de ciencia de datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ja creado una cuenta de almacenamiento de blobs de Azure y que ha almacenado los datos ahí. Si necesita instrucciones para configurar una cuenta, consulte [Creación de una cuenta de Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Carga de los datos en una trama de datos Pandas
Para explorar y manipular un conjunto de datos, puede descargarlo desde el origen de blob a un archivo local. Después, cárguelo en una trama de datos de Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descargue los datos del blob de Azure con el siguiente código de Python de ejemplo mediante el servicio BLOB. Reemplace la variable en el código siguiente por sus valores específicos:
   
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
2. Lea los datos en una trama de datos de Pandas desde el archivo descargado.
   
        #LOCALFILE is the file path
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Ya puede explorar los datos y generar características en este conjunto de datos.

## <a name="blob-featuregen"></a>Generación de características
Las dos secciones siguientes muestran cómo generar características de categorías con valores de indicador y características de discretización mediante scripts de Python.

### <a name="blob-countfeature"></a>Generación de características basada en el valor de indicador
Las características de categorías se pueden crear como sigue:

1. Inspeccione la distribución de la columna de categorías:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Genere valores de indicador para cada uno de los valores de columna
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Combine la columna de indicador con la trama de datos original
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Quite la propia variable original:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Generación de características de discretización
Para generar características discretizadas, se procede de la siguiente manera:

1. Agregue una secuencia de columnas para discretizar una columna numérica
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Convierta la discretización en una secuencia de variables booleanas
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Por último, combine las variables de prueba con la trama de datos original
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Reescritura de datos en un blob de Azure y consumo en Azure Machine Learning
Para consumir los datos que ha explorado, muestreado o caracterizado en Azure Machine Learning, cargue los datos en un blob de Azure. También se pueden crear características adicionales en Azure Machine Learning Studio. Los pasos siguientes muestran cómo cargar los datos:

1. Escriba la trama de datos en el archivo local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Cargue los datos en el blob de Azure como se indica a continuación:
   
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
3. Ahora se pueden leer los datos del blob mediante el módulo [Importar datos](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) de Azure Machine Learning, como se muestra en la pantalla siguiente:

![lector de blobs](./media/data-blob/reader_blob.png)

