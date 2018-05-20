---
title: 'Guía de inicio rápido: Introducción a Hadoop y Hive en Azure HDInsight con la plantilla de Resource Manager | Microsoft Docs'
description: Aprenda a crear clústeres de HDInsight y a consultar datos con Hive.
keywords: introducción a hadoop, hadoop linux, inicio rápido de hadoop, introducción a hive, inicio rápido en hive
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 4cf20dacf66ee334dcd455ce1770609c175d3b88
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-get-started-with-hadoop-and-hive-in-azure-hdinsight-using-resource-manager-template"></a>Guía de inicio rápido: Introducción a Hadoop y Hive en Azure HDInsight con la plantilla de Resource Manager

En este artículo, aprenderá a crear clústeres de [Hadoop](http://hadoop.apache.org/) en HDInsight con una plantilla de Resource Manager y, a continuación, ejecutar trabajos de Hive en HDInsight. La mayoría de los trabajos de Hadoop son trabajos por lotes. Se crea un clúster, se ejecutan algunos trabajos y luego se elimina el clúster. En este artículo, realizará las tres tareas.

En esta guía de inicio rápido, usará una plantilla de Resource Manager para crear un clúster de HDInsight Hadoop. También puede crear un clúster mediante [Azure Portal](apache-hadoop-linux-create-cluster-get-started-portal.md).

Actualmente HDInsight tiene [siete tipos diferentes de clúster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Cada uno de estos tipos de clúster es compatible con un conjunto de componentes diferente. Todos los tipos de clúster son compatibles con Hive. Para ver una lista de los componentes compatibles con HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](../hdinsight-component-versioning.md)  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Creación de un clúster de Hadoop

En esta sección, se crea un clúster de Hadoop en HDInsight mediante una plantilla de Azure Resource Manager. No es necesario tener experiencia en el uso de la plantilla de Resource Manager para seguir este artículo. 

1. Haga clic en el botón **Deploy to Azure** (Implementar en Azure) siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba o seleccione los valores como se sugiere en la siguiente captura de pantalla:

    > [!NOTE]
    > Los valores que proporcione deben ser exclusivos y deben seguir las directrices de nomenclatura. La plantilla no realiza comprobaciones de validación. Si los valores proporcionados ya están en uso o no sigue las directrices, obtendrá un error después de haber enviado la plantilla.       
    > 
    >
    
    ![Introducción a HDInsight Linux, plantilla de Resource Manager en el portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Implementación de un clúster de Hadoop en HDInsight mediante Azure Portal y una plantilla de Resource Manager")

    Escriba o seleccione los siguientes valores:
    
    |Propiedad  |DESCRIPCIÓN  |
    |---------|---------|
    |**Suscripción**     |  Seleccione su suscripción a Azure. |
    |**Grupos de recursos**     | Cree un grupo de recursos o seleccione uno existente.  Un grupo de recursos es un contenedor de componentes de Azure.  En este caso, el grupo de recursos contiene el clúster de HDInsight y la cuenta de Azure Storage dependiente. |
    |**Ubicación**     | Seleccione una ubicación de Azure donde desea crear el clúster.  Elija una ubicación más cercana a usted para mejorar el rendimiento. |
    |**Tipo de clúster**     | Seleccione **hadoop**. |
    |**Nombre del clúster**     | Escriba el nombre del clúster de Hadoop. Dado que todos los clústeres de HDInsight comparten el mismo espacio de nombres de DNS, este nombre debe ser único. El nombre puede tener un máximo de 59 caracteres, letras, números y guiones incluidos. El primer y el último caracteres del nombre no pueden ser guiones. |
    |**Nombre de inicio de sesión y contraseña del clúster**     | El nombre de inicio de sesión predeterminado es **admin**. La contraseña debe tener un mínimo de 10 caracteres y contener al menos un dígito, una letra mayúscula y una letra minúscula, y un carácter no alfanumérico (excepto los caracteres ' " y `\). Asegúrese de **no proporcionar** contraseñas comunes, como "Pass@word1".|
    |**Nombre de usuario y contraseña de SSH**     | El nombre de usuario predeterminado es **sshuser**.  El nombre de usuario de SSH se puede cambiar.  La contraseña de usuario de SSH tiene los mismos requisitos que la contraseña de inicio de sesión del clúster.|
       
    Algunas propiedades se han codificado de forma rígida en la plantilla.  Puede configurar estos valores de la plantilla. Para más información acerca de estas propiedades, consulte [Creación de clústeres de Hadoop en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y **Anclar al panel** y seleccione **Comprar**. Verá un icono nuevo llamado **Enviando implementación** en el panel del portal. Se tarda aproximadamente 20 minutos en crear un clúster.

    ![Progreso de Template Deployment](./media/apache-hadoop-linux-tutorial-get-started/deployment-progress-tile.png "Progreso de Azure Template Deployment")

4. Una vez creado el clúster, se cambia el título del icono por el nombre del grupo de recursos que ha especificado. El icono también muestra el clúster de HDInsight que se crea en el grupo de recursos. 
   
    ![Introducción a HDInsight Linux: grupo de recursos](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Grupo de recursos de clúster de Azure HDInsight")
    
5. El icono también muestra el almacenamiento predeterminado asociado con el clúster. Cada clúster depende de una [cuenta de Azure Storage](../hdinsight-hadoop-use-blob-storage.md) o de una [cuenta de Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Se conoce como cuenta de almacenamiento predeterminada. El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben estar en la misma región de Azure. La eliminación de los clústeres no elimina la cuenta de almacenamiento.
    

> [!NOTE]
> Para conocer otros métodos de creación de clústeres y las propiedades que se usan en este tutorial, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       
> 
>

## <a name="run-hive-queries"></a>Ejecución de consultas de Hive

[Apache Hive](hdinsight-use-hive.md) es el componente más popular de los que se usan en HDInsight. Hay muchas maneras de ejecutar trabajos de Hive en HDInsight. En este tutorial se usa la vista de Hive de Ambari desde el portal. Para conocer otros métodos de enviar trabajos de Hive, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

1. Para abrir Ambari, desde la captura de pantalla anterior, seleccione **Panel de clúster**.  También puede ir a **https://&lt;NombreDeClúster>.azurehdinsight.net**, donde &lt;NombreDeClúster> es el clúster que creó en la sección anterior.

    ![HDInsight Linux, introducción al panel de clúster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux, introducción al panel de clúster")

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

5. Seleccione **Execute**(Ejecutar). Aparecerá una pestaña **RESULTADOS** en la pestaña **CONSULTA** que mostrará información sobre el trabajo. 
   
    Cuando haya finalizado la consulta, la pestaña **CONSULTA** muestra los resultados de la operación. Verá una tabla denominada **hivesampletable**. Esta es una tabla de Hive de ejemplo que viene integrada en todos los clústeres de HDInsight.
   
    ![Vistas de Hive de HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor de consultas de vistas de Hive de HDInsight")

6. Repita los pasos 4 y 5 para ejecutar la consulta siguiente:
   
        SELECT * FROM hivesampletable;
   
7. También puede guardar los resultados de la consulta. Seleccione el botón de menú de la derecha y especifique si desea descargar los resultados como archivo CSV o almacenarlos en la cuenta de almacenamiento asociada al clúster.

    ![Guardado de los resultados de la consulta de Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Save result of Hive query")

Después de completar un trabajo de Hive, puede [exportar los resultados a Azure SQL Database o Base de datos SQL Server](apache-hadoop-use-sqoop-mac-linux.md), y también puede [visualizar los resultados con Excel](apache-hadoop-connect-excel-power-query.md). Para más información sobre el uso de Hive en HDInsight, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="clean-up-resources"></a>Limpieza de recursos
Después de completar el artículo, puede eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. 

> [!NOTE]
> Si avanza de *inmediato* al siguiente tutorial para aprender a ejecutar operaciones de ETL con Hadoop en HDInsight, puede que desee mantener el clúster en ejecución. Esto es porque en el tutorial tendrá que crear un clúster de Hadoop de nuevo. Sin embargo, si no va a seguir con próximo tutorial de inmediato, debe eliminar el clúster ahora.
> 
> 

**Para eliminar el clúster o la cuenta de almacenamiento predeterminada**

1. Vuelva a la pestaña de explorador en la que tenga Azure Portal. Estará en la página de información general del clúster. Si desea eliminar el clúster, pero conservar la cuenta de almacenamiento predeterminada, seleccione **Eliminar**.

    ![HDInsight, eliminar clúster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminar clúster de HDInsight")

2. Si desea eliminar el clúster, así como la cuenta de almacenamiento predeterminada, seleccione el nombre del grupo de recursos (resaltado en la captura de pantalla anterior) para abrir la página del grupo de recursos.

3. Seleccione **Eliminar grupo de recursos** para eliminar el grupo de recursos, que contiene el clúster y la cuenta de almacenamiento predeterminada. Tenga en cuenta que, al eliminar el grupo de recursos, se elimina también la cuenta de almacenamiento. Si desea mantener la cuenta de almacenamiento, elija Eliminar solo el clúster.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido cómo crear un clúster de HDInsight basado en Linux mediante una plantilla de Resource Manager y cómo realizar consultas básicas de Hive. En el siguiente artículo, aprenderá cómo realizar una operación ETL (extraer, transformar y cargar) mediante Hadoop en HDInsight.

> [!div class="nextstepaction"]
>[Extraer, transformar y cargar datos mediante Apache Hive en HDInsight ](../hdinsight-analyze-flight-delay-data-linux.md)

Si está listo para empezar a trabajar con sus propios datos y necesita más información acerca de la forma en que HDInsight o de cómo obtener datos en HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo HDInsight usa Azure Storage, consulte [Uso de Azure Storage con HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para más información sobre cómo cargar datos en HDInsight, consulte [Carga de datos en HDInsight](../hdinsight-upload-data.md).

Para más información sobre el análisis de datos con HDInsight, consulte los siguientes artículos:

* Para más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, consulte [Uso de Hive con HDInsight](hdinsight-use-hive.md).
* Para más información sobre Pig, un lenguaje usado para transformar datos, consulte [Uso de Pig con HDInsight](hdinsight-use-pig.md).
* Para más información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, consulte [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md).
* Para más información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight para ejecutar una consulta de Hive](apache-hadoop-visual-studio-tools-get-started.md).



Si desea más información acerca de cómo crear o administrar un clúster de HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para más información acerca de las opciones que se pueden seleccionar al crear un clúster de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


