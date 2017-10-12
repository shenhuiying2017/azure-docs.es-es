---
title: "Exploración de los datos de una máquina virtual de SQL Server en Azure | Microsoft Docs"
description: "Cómo explorar los datos que se almacenan en una máquina virtual de SQL Server en Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 4777a6acbf3b45ab207d64feb7cba52f61f38eeb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Exploración de los datos de una máquina virtual de SQL Server en Azure
Este documento explica cómo explorar los datos que se almacenan en una máquina virtual de SQL Server en Azure. Esto puede hacerse mediante la administración de datos usando SQL o mediante un lenguaje de programación como Python.

El siguiente **menú** vincula a temas que describen cómo usar herramientas para explorar los datos desde varios entornos de almacenamiento. Esta tarea es un paso en el proceso de análisis de Cortana (CAP).

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> En las instrucciones SQL de ejemplo de este documento se supone que los datos están en SQL Server. Si no lo están, consulte el mapa de proceso de ciencia de datos en la nube para obtener información sobre cómo mover los datos a SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Exploración de los datos de SQL con scripts de SQL
A continuación se muestran algunos scripts de SQL de ejemplo que se pueden usar para explorar los almacenes de datos en SQL Server.

1. Obtener el número de observaciones por día
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obtenga los niveles de una columna de categorías
   
    `select  distinct <column_name> from <databasename>`
3. Obtener el número de niveles de combinación de dos columnas de categorías 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obtención de la distribución para columnas numéricas 
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Para obtener un ejemplo práctico, puede usar el [conjunto de datos de los taxis de la Ciudad de Nueva York](http://www.andresmh.com/nyctaxitrips/) y consultar el IPNB llamado [Tratamiento de datos de la Ciudad de Nueva York mediante un Bloc de notas de IPython y SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb), que se trata de un tutorial completo.
> 
> 

## <a name="python"></a>Exploración de los datos de SQL con Python
Usar Python para generar explorar datos y generar características cuando los datos están en SQL Server es parecido a procesar los datos en Blob de Azure mediante Python, como se documenta en [Proceso de datos de Blob de Azure en su entorno de ciencia de datos](data-blob.md). Los datos deben cargarse desde la base de datos en una trama de datos de Pandas y, a continuación, se pueden procesar aún más. Se documenta el proceso de conexión a la base de datos y carga de los datos en la trama de datos de esta sección.

El formato de cadena de conexión siguiente puede usarse para conectarse a una base de datos de SQL Server desde Python mediante pyodbc (reemplace servername, dbname, username y password con sus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [biblioteca Pandas](http://pandas.pydata.org/) en Python ofrece un amplio conjunto de herramientas de análisis de datos y estructuras de datos para la manipulación de datos para la programación en Python. El código siguiente lee los resultados que se devuelven desde una base de datos de SQL Server en una trama de datos de Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Ya puede trabajar con la trama de datos de Pandas como se explica en el artículo [Proceso de datos del blob de Azure con análisis avanzado](data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Ejemplo de proceso de análisis de Cortana en acción
Para ver un tutorial de ejemplo completo del proceso de Cortana Analytics usando un conjunto de datos público, consulte [Proceso de ciencia de datos en equipos en acción: uso de SQL Server](sql-walkthrough.md).

