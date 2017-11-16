---
title: "Exploración de los datos de las tablas de Hive con consultas de Hive | Microsoft Docs"
description: "Exploración de los datos de las tablas de Hive con consultas de Hive."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 9cf205abcf9782ceac4d9ac5a920e136b69c57b6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Exploración de los datos de las tablas de Hive con consultas de Hive
Este documento proporciona scripts de Hive de ejemplo que se usan para explorar los datos de las tablas de Hive en un clúster de Hadoop para HDInsight.

El siguiente **menú** vincula a temas que describen cómo usar herramientas para explorar los datos desde varios entornos de almacenamiento.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight. Si necesita instrucciones, consulte [Personalización de clústeres de Hadoop de HDInsight de Azure para análisis avanzado](customize-hadoop-cluster.md).
* Se han cargado los datos en tablas de subárbol en clústeres de Hadoop de HDInsight de Azure. Si no es así, siga las instrucciones proporcionadas en [Crear y cargar datos en tablas de Hive](move-hive-tables.md) para cargar los datos en tablas de Hive primero.
* Habilitado el acceso remoto al clúster. Si necesita instrucciones, consulte [Acceso al nodo principal del clúster de Hadoop](customize-hadoop-cluster.md).
* Si necesita instrucciones sobre cómo enviar consultas de Hive, consulte [Envío de consultas de Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Ejemplo de scripts de consulta de Hive para la exploración de datos
1. Obtención del número de observaciones por partición `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obtención del número de observaciones por día `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obtención de los niveles de una columna de categorías   
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obtención del número de niveles de combinación de dos columnas de categorías `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obtención de la distribución para columnas numéricas   
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extraer registros de la combinación de dos tablas
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionales para escenarios de datos de trayectos en taxi
También se ofrecen ejemplos de consultas que son específicos de escenarios de [NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/) en el [repositorio de GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas ya tienen el esquema de datos especificado y están listas para enviarse para su ejecución.

