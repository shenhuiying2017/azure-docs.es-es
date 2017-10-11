---
title: "Uso de Hive con Hadoop para el análisis de registros de sitios web: Azure HDInsight | Microsoft Docs"
description: "Vea cómo usar Hive con HDInsight para analizar registros de sitios web. Usará un archivo de registro como entrada en una tabla de HDInsight y HiveQL para consultar los datos."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: e1cdb786bb6049980aafc0213abf53013e342618
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Uso de Hive con HDInsight basado en Windows para analizar registros de sitios web
Vea cómo usar HiveQL con HDInsight para analizar registros de un sitio web. El análisis de registros de sitios web se puede usar para segmentar su público en función de actividades parecidas, clasificar los visitantes a los sitios por datos demográficos, descubrir el contenido que ven, los sitios web de los que proceden, etc.

> [!IMPORTANT]
> Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. HDInsight solo está disponible en Windows en versiones inferiores a la 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

En este ejemplo, usará un clúster de HDInsight para analizar archivos de registro de sitios web a fin de conocer la frecuencia de las visitas al sitio web en un día desde sitios web externos en un día. También generará un resumen de errores de sitios web que experimentan los usuarios. Aprenderá a:

* Conectarse a un almacenamiento de blobs de Azure, que contiene archivos de registro de sitios web.
* Crear tablas de HIVE para consultar esos registros.
* Crear consultas de HIVE para analizar los datos.
* Usar Microsoft Excel para conectarse a HDInsight (usando conectividad abierta de base de datos, ODBC) para recuperar los datos analizados.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

## <a name="prerequisites"></a>Requisitos previos
* Debe aprovisionar un clúster de Hadoop en HDInsight de Azure. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].
* Debe tener instalado Microsoft Excel 2013 o Excel 2010.
* Debe tener [Microsoft Hive ODBC Driver para](http://www.microsoft.com/download/details.aspx?id=40886) importar datos de Hive en Excel.

## <a name="to-run-the-sample"></a>Para ejecutar el ejemplo
1. En el [Portal de Azure](https://portal.azure.com/), en el panel de inicio (si ancló el clúster allí), haga clic en el icono de clúster en el que quiera ejecutar el ejemplo.
2. En la hoja del clúster, en **Vínculos rápidos**, haga clic en el **panel del clúster** y luego, en la hoja **Panel de clúster**, haga clic en el **panel del clúster de HDInsight**. También puede abrir directamente el panel con la siguiente dirección URL:

         https://<clustername>.azurehdinsight.net

    Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó el clúster.
3. En la página web que se abre, haga clic en la pestaña **Galería de introducción** y, a continuación, en la categoría **Soluciones con datos de ejemplo**, haga clic en el ejemplo **Análisis de registro del sitio web**.
4. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.

## <a name="next-steps"></a>Pasos siguientes
Pruebe el siguiente ejemplo: [Análisis de datos de sensor usando Hive con HDInsight](hdinsight-hive-analyze-sensor-data.md).

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
