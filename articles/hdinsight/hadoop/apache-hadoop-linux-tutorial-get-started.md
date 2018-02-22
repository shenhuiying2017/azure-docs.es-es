---
title: "Introducción a Hadoop y Hive en Azure HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres de HDInsight y a consultar datos con Hive."
keywords: "introducción a hadoop, hadoop linux, inicio rápido de hadoop, introducción a hive, inicio rápido en hive"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: jgao
ms.openlocfilehash: a96612e09e51db8941a7828f8fe9cb7bbced837d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Tutorial de Hadoop: introducción al uso de Hadoop en HDInsight

Aprenda a crear clústeres de [Hadoop](http://hadoop.apache.org/) en HDInsight y a ejecutar trabajos de Hive en HDInsight. [Apache Hive](https://hive.apache.org/) es el componente más popular del ecosistema de Hadoop. Actualmente HDInsight tiene [siete tipos diferentes de clúster](apache-hadoop-introduction.md#overview). Cada uno de estos tipos de clúster es compatible con un conjunto de componentes diferente. Todos los tipos de clúster son compatibles con Hive. Para ver una lista de los componentes compatibles con HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Suscripción de Azure**: para crear una cuenta de evaluación gratuita durante un mes, vaya a [azure.microsoft.com/es-es/free/](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Crear clúster

La mayoría de los trabajos de Hadoop son trabajos por lotes. Se crea un clúster, se ejecutan algunos trabajos y luego se elimina el clúster. En esta sección, se crea un clúster de Hadoop en HDInsight mediante una [plantilla de Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). No es necesario tener experiencia en el uso de la plantilla de Resource Manager para seguir este tutorial. Para conocer otros métodos de creación de clústeres y las propiedades que se usan en este tutorial, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

La plantilla de Resource Manager utilizada en este tutorial se encuentra en [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba o seleccione los siguientes valores:
   
    ![Introducción a HDInsight Linux, plantilla del administrador de recursos en el portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Implementación de un clúster de Hadoop en HDInsight mediante Azure Portal y una plantilla de Resource Manager").
   
    * **Suscripción**: seleccione su suscripción de Azure.
    * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente.  Un grupo de recursos es un contenedor de componentes de Azure.  En este caso, el grupo de recursos contiene el clúster de HDInsight y la cuenta de Azure Storage dependiente. 
    * **Ubicación**: seleccione una ubicación de Azure donde desea crear el clúster.  Elija una ubicación más cercana a usted para mejorar el rendimiento. 
    * **Tipo de clúster**: seleccione **Hadoop** para este tutorial.
    * **Nombre del clúster**: escriba el nombre del clúster de Hadoop. Dado que todos los clústeres de HDInsight comparten el mismo espacio de nombres de DNS, este nombre debe ser único. El nombre puede constar de hasta 59 caracteres, letras, números y guiones incluidos. Tenga en cuenta que el primer y último carácter del nombre no pueden ser guiones.
    * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    * **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo. 
     
    Algunas propiedades se han codificado de forma rígida en la plantilla.  Puede configurar estos valores de la plantilla.
    
    Cada clúster depende de una [cuenta de Azure Storage](../hdinsight-hadoop-use-blob-storage.md) o de una [cuenta de Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Se conoce como cuenta de almacenamiento predeterminada. El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben estar en la misma región de Azure. La eliminación de los clústeres no elimina la cuenta de almacenamiento. 
        
    Para más información acerca de estas propiedades, consulte [Creación de clústeres de Hadoop en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y **Anclar al panel** y haga clic en **Comprar**. Verá un icono nuevo llamado **Implementación para la implementación de plantilla** en el panel del portal. Tarda aproximadamente 20 minutos en crear un clúster. 

4. Una vez creado el clúster, se cambia el título del icono por el nombre del grupo de recursos que ha especificado. Haga clic en el icono para ver el clúster y el almacenamiento predeterminado para este que se muestran.
   
    ![Introducción a HDInsight Linux: grupo de recursos](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Grupo de recursos de clúster de Azure HDInsight")

4. Haga clic en el nombre del clúster para abrir el clúster.

   ![Introducción a HDInsight Linux: configuración del clúster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Propiedades de clúster de HDInsight")


## <a name="run-hive-queries"></a>Ejecución de consultas de Hive

[Apache Hive](hdinsight-use-hive.md) es el componente más popular de los que se usan en HDInsight. Hay muchas maneras de ejecutar trabajos de Hive en HDInsight. En este tutorial se usa la vista de Hive de Ambari desde el portal. Para conocer otros métodos de enviar trabajos de Hive, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

1. Para abrir Ambari, en la captura de pantalla anterior, haga clic en **Panel de clúster** y en **Panel de clúster de HDInsight**.  También puede ir a **https://&lt;NombreDeClúster>.azurehdinsight.net**, donde &lt;NombreDeClúster> es el clúster que creó en la sección anterior.

2. Escriba el nombre de usuario de Hadoop y la contraseña que especificó al crear el clúster. El nombre de usuario predeterminado es **admin**.

3. Abra la **vista de Hive** como se muestra en la siguiente captura de pantalla:
   
    ![Selección de vistas de Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menú de visor de Hive de HDInsight")

4. En la pestaña **CONSULTA**, pegue las instrucciones HiveQL siguientes en la hoja de cálculo:
   
        SHOW TABLES;

    ![Vistas de Hive de HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor de consultas de vistas de Hive de HDInsight")
   
   > [!NOTE]
   > Hive requiere un punto y coma.       
   > 
   > 

5. Haga clic en **Ejecutar**. Aparecerá una pestaña **RESULTADOS** en la pestaña **CONSULTA** que mostrará información sobre el trabajo. 
   
    Cuando haya finalizado la consulta, la pestaña **CONSULTA** muestra los resultados de la operación. Verá una tabla denominada **hivesampletable**. Esta es una tabla de Hive de ejemplo que viene integrada en todos los clústeres de HDInsight.
   
    ![Vistas de Hive de HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor de consultas de vistas de Hive de HDInsight")

6. Repita los pasos 4 y 5 para ejecutar la consulta siguiente:
   
        SELECT * FROM hivesampletable;
   
7. También puede guardar los resultados de la consulta. Haga clic en el botón de menú de la derecha y especifique si desea descargar los resultados como archivo CSV o almacenarlos en la cuenta de almacenamiento asociada al clúster.

    ![Guardado de los resultados de la consulta de Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Save result of Hive query")



Después de completar un trabajo de Hive, puede [exportar los resultados a Azure SQL Database o Base de datos SQL Server](apache-hadoop-use-sqoop-mac-linux.md), y también puede [visualizar los resultados con Excel](apache-hadoop-connect-excel-power-query.md). Para más información sobre el uso de Hive en HDInsight, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Limpieza del tutorial
Después de completar el tutorial, puede eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. 

> [!NOTE]
> Con [Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md) puede crear clústeres de HDInsight a petición y configurar un valor de TimeToLive que elimine los clústeres automáticamente. 
> 
> 

**Para eliminar el clúster o la cuenta de almacenamiento predeterminada**

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el panel del portal, haga clic en el icono con el nombre del grupo de recursos que utilizó al crear el clúster.
3. Haga clic en **Eliminar grupo de recursos** para eliminar el grupo de recursos, que contiene el clúster y la cuenta de almacenamiento predeterminada; o bien, haga clic en el nombre del clúster en el icono **Recursos** y en **Eliminar**. Tenga en cuenta que, al eliminar el grupo de recursos, se elimina también la cuenta de almacenamiento. Si desea mantener la cuenta de almacenamiento, elija Eliminar solo el clúster.

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha aprendido cómo crear un clúster de HDInsight basado en Linux mediante una plantilla de Resource Manager y cómo realizar consultas básicas de Hive.

Para más información sobre el análisis de datos con HDInsight, consulte los siguientes artículos:

* Para más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, consulte [Uso de Hive con HDInsight](hdinsight-use-hive.md).
* Para más información sobre Pig, un lenguaje usado para transformar datos, consulte [Uso de Pig con HDInsight](hdinsight-use-pig.md).
* Para más información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, consulte [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md).
* Para más información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight para ejecutar una consulta de Hive](apache-hadoop-visual-studio-tools-get-started.md).

Si está listo para empezar a trabajar con sus propios datos y necesita más información acerca de la forma en que HDInsight o de cómo obtener datos en HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo HDInsight usa Azure Storage, consulte [Uso de Azure Storage con HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para más información sobre cómo cargar datos en HDInsight, consulte [Carga de datos en HDInsight](../hdinsight-upload-data.md).

Si desea más información acerca de cómo crear o administrar un clúster de HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para más información acerca de las opciones que se pueden seleccionar al crear un clúster de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Si está familiarizado con Linux y Hadoop, pero quiere conocer información específica sobre Hadoop en HDInsight, consulte [Información sobre el uso de HDInsight en Linux](../hdinsight-hadoop-linux-information.md). Este artículo proporciona información como:
  
  * Direcciones URL para los servicios hospedados en el clúster, por ejemplo, Ambari y WebHCat
  * La ubicación de los archivos y ejemplos de Hadoop en el sistema de archivos local
  * El uso de Azure Storage (WASB) en lugar de HDFS como el almacén de datos predeterminado

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


