---
title: "Exploración de datos en Azure Blob Storage con Pandas | Microsoft Docs"
description: "Cómo explorar los datos almacenados en el contenedor de blobs de Azure mediante Pandas."
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: a46735dde28740087d201d7490f135349aad76f6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Exploración de datos en el almacenamiento de blobs de Azure con Pandas
Este documento explica cómo explorar los datos almacenados en el contenedor de blobs de Azure mediante el paquete de Python [Pandas](http://pandas.pydata.org/) .

El siguiente **menú** vincula a temas que describen cómo usar herramientas para explorar los datos desde varios entornos de almacenamiento. Esta tarea constituye un paso del [proceso de ciencia de datos en equipos (TDSP)]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Almacenó los datos en una cuenta de almacenamiento de blobs de Azure. Si necesita instrucciones, consulte [mover con Azure Storage como origen y destino](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Carga de los datos en una trama de datos de Pandas
Para explorar y manipular un conjunto de datos, se debe descargar desde el origen de blob en un archivo local que se pueda cargar en una trama de datos de Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descargue los datos del blob de Azure con el siguiente código de ejemplo Python mediante el servicio BLOB. Reemplace la variable en el código siguiente por sus valores específicos: 
   
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

## <a name="blob-dataexploration"></a>Ejemplos de exploración de datos con Pandas
A continuación, se muestran algunos ejemplos de formas de explorar datos mediante Pandas:

1. Inspeccionar el **número de filas y columnas** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Inspeccionar** las primeras o las últimas **filas** del conjunto de datos, como se indica a continuación:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Comprobar el **tipo de datos** como el que se importó cada columna mediante el siguiente código de ejemplo:
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Comprobar las **estadísticas básicas** de las columnas del conjunto de datos de la siguiente forma:
   
        dataframe_blobdata.describe()
5. Observar el número de entradas de cada valor de columna, como se indica a continuación
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Contar los valores que faltan** frente al número real de entradas de cada columna, mediante el siguiente código de ejemplo:
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Si hay **valores que faltan** para una columna determinada en los datos, puede quitarlos como se indica:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   Otra forma de reemplazar los valores que faltan es a través de la función de modo:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Crear un gráfico de **histograma** con un número variable de discretizaciones para trazar la distribución de una variable.    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Examinar las **correlaciones** entre las variables mediante un gráfico de dispersión o con la función de correlación integrada.
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

