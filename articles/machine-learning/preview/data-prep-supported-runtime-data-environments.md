---
title: "Combinaciones admitidas de entornos de datos y de ejecución para Preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de las combinaciones admitidas de distintos tiempos de ejecución y orígenes de datos para Preparación de datos de Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="supported-matrix-for-this-release"></a>Matriz compatible para esta versión 
Cuando el código está cargando datos con orígenes de datos, o mediante Preparación de datos, obteniendo una trama de datos de Pandas o de Spark, se admiten las siguientes combinaciones de entornos de proceso experimentales y de ubicaciones de datos:

|     |Archivos locales  |Azure Blob  |Base de datos de SQL Server***  |
|---------|---------|---------|---------|---------|
|Python local    |     Compatible    |No compatible         | No compatible        |         |
|Docker (máquina virtual Linux) Python     |Compatible solo en los archivos de proyecto*         | No compatible        |        No compatible |         |
|Docker (máquina virtual Linux) PySpark     |Compatible solo en los archivos de proyecto*     |Compatible         | Compatible**        |         |
|Azure Data Science Virtual Machine Python     |Compatible solo en los archivos de proyecto*         |No compatible         |No compatible         |         |
|Azure Data Science Virtual Machine PySPark     | Compatible solo en los archivos de proyecto*        |No compatible         |No compatible         |         |
|Azure HDInsight PySpark     | No compatible        |Compatible         |Compatible**         |         |
|Azure HDInsight Python     | No compatible        | No compatible        | No compatible        |         |

Actualmente, Azure Data Lake Store no es compatible con ningún destino de proceso.

*Al usar rutas de acceso de archivos locales, los archivos del proyecto se copian en el entorno de proceso y luego se leen ahí. Los archivos que están fuera del proyecto no se copian y las rutas de acceso dejarán de resolverse en el entorno de proceso. Plantéese el uso de la sustitución de origen de datos para que el código pueda usar un archivo local cuando se ejecute localmente y, luego, cambie a un blob de Azure Storage para otra configuración de ejecución. También puede usar el soporte de muestreo de los orígenes de datos para administrar ejecuciones en datos de gran tamaño solo en determinadas configuraciones de ejecución.

**Usa el controlador Maven JDBC para SQL Server 6.2.1. Debe asegurarse de que este paquete (o uno compatible) esté incluido en el archivo spark_dependencies.yml del entorno de proceso.

***Admite Azure SQL Database, Azure SQL Data Warehouse o Microsoft SQL Server, siempre y cuando se pueda obtener acceso a la base de datos desde el entorno de proceso. 
