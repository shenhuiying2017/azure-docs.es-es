---
title: Uso de Grafana en Azure HDInsight | Microsoft Docs
description: Obtenga información sobre cómo acceder a Grafana en Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: jgao
ms.openlocfilehash: c452cb1264dceff8cb791588fa7c58f73631d422
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305682"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Acceso a Grafana en Azure HDInsight


Grafana es un software popular y de código abierto para crear gráficos y paneles. Grafana está lleno de características; no solo permite a los usuarios crear paneles que pueden personalizar y compartir, sino que también ofrece paneles con plantillas o scripts, integración de LDAP, varios orígenes de datos y mucho más.

Actualmente, Grafana solo es compatible con el tipo de clúster de Consulta interactiva en Azure HDInsight.


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="create-a-hadoop-cluster"></a>Creación de un clúster de Hadoop

En esta sección, se crea un clúster de Consulta interactiva en HDInsight mediante una plantilla de Azure Resource Manager. No es necesario tener experiencia en el uso de la plantilla de Resource Manager para seguir este artículo. 

1. Haga clic en el botón **Deploy to Azure** (Implementar en Azure) siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba o seleccione los valores como se sugiere en la siguiente captura de pantalla:

    > [!NOTE]
    > Los valores que proporcione deben ser exclusivos y deben seguir las directrices de nomenclatura. La plantilla no realiza comprobaciones de validación. Si los valores proporcionados ya están en uso o no sigue las directrices, obtendrá un error después de haber enviado la plantilla.       
    > 
    >
    
    ![Introducción a HDInsight Linux, plantilla de Resource Manager en el portal](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Implementación de un clúster de Hadoop en HDInsight mediante Azure Portal y una plantilla de Resource Manager")

    Escriba o seleccione los siguientes valores:
    
    |Propiedad  |DESCRIPCIÓN  |
    |---------|---------|
    |**Suscripción**     |  Seleccione su suscripción a Azure. |
    |**Grupos de recursos**     | Cree un grupo de recursos o seleccione uno existente.  Un grupo de recursos es un contenedor de componentes de Azure.  En este caso, el grupo de recursos contiene el clúster de HDInsight y la cuenta de Azure Storage dependiente. |
    |**Ubicación**     | Seleccione una ubicación de Azure en la que quiera crear el clúster.  Elija una ubicación más cercana a usted para mejorar el rendimiento. |
    |**Tipo de clúster**     | Seleccione **hadoop**. |
    |**Nombre del clúster**     | Escriba el nombre del clúster de Hadoop. Dado que todos los clústeres de HDInsight comparten el mismo espacio de nombres de DNS, este nombre debe ser único. El nombre puede tener un máximo de 59 caracteres, letras, números y guiones incluidos. El primer y el último carácter del nombre no pueden ser guiones. |
    |**Nombre de inicio de sesión y contraseña del clúster**     | El nombre de inicio de sesión predeterminado es **admin**. La contraseña debe tener un mínimo de 10 caracteres y contener al menos un dígito, una letra mayúscula y una letra minúscula, y un carácter no alfanumérico (excepto los caracteres ' " y `\). Asegúrese de **no proporcionar** contraseñas comunes, como "Pass@word1".|
    |**Nombre de usuario y contraseña de SSH**     | El nombre de usuario predeterminado es **sshuser**.  El nombre de usuario de SSH se puede cambiar.  La contraseña de usuario de SSH tiene los mismos requisitos que la contraseña de inicio de sesión del clúster.|
       
    Algunas propiedades se han codificado de forma rígida en la plantilla.  Puede configurar estos valores de la plantilla. Para más información acerca de estas propiedades, consulte [Creación de clústeres de Hadoop en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y **Anclar al panel** y seleccione **Comprar**. Verá un icono nuevo llamado **Enviando implementación** en el panel del portal. Se tarda aproximadamente 20 minutos en crear un clúster.

    ![Progreso de Template Deployment](./media/hdinsight-grafana/deployment-progress-tile.png "Progreso de Azure Template Deployment")

4. Una vez creado el clúster, se cambia el título del icono por el nombre del grupo de recursos que ha especificado. El icono también muestra el clúster de HDInsight que se crea en el grupo de recursos. 
   
    ![Introducción a HDInsight Linux: grupo de recursos](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Grupo de recursos de clúster de Azure HDInsight")
    
5. El icono también muestra el almacenamiento predeterminado asociado con el clúster. Cada clúster depende de una [cuenta de Azure Storage](../hdinsight-hadoop-use-blob-storage.md) o de una [cuenta de Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Se conoce como cuenta de almacenamiento predeterminada. El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben estar en la misma región de Azure. La eliminación de los clústeres no elimina la cuenta de almacenamiento.
    

> [!NOTE]
> Para conocer otros métodos de creación de clústeres y las propiedades que se usan en este tutorial, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       
> 
>

## <a name="access-the-grafana-dashboard"></a>Acceso al panel de Grafana

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Clústeres de HDInsight** y, a continuación, seleccione el nombre del clúster que creó en la última sección.
3. En **Vínculos rápidos**, haga clic en **Panel de clúster**.

    ![Portal del panel de clúster de HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Panel de clúster de HDInsight en el portal")

4. En el panel, haga clic en el icono **Grafana**.
5. Escriba las credenciales de usuario del clúster de Hadoop.
6. El panel de Grafana tiene este aspecto:

    ![Panel de Grafana en HDInsight](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Panel de Grafana en HDInsight")

## <a name="clean-up-resources"></a>Limpieza de recursos
Después de completar el artículo, puede eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. 

> [!NOTE]
> Si avanza de *inmediato* al siguiente tutorial para aprender a ejecutar operaciones de ETL con Hadoop en HDInsight, es posible que quiera mantener el clúster en ejecución. Esto es porque en el tutorial tendrá que crear un clúster de Hadoop de nuevo. Sin embargo, si no va a continuar con el próximo tutorial de inmediato, debe eliminar el clúster ahora.
> 
> 

**Para eliminar el clúster o la cuenta de almacenamiento predeterminada**

1. Vuelva a la pestaña de explorador en la que tenga Azure Portal. Estará en la página de información general del clúster. Si solo quiere eliminar el clúster, pero desea seguir conservando la cuenta de almacenamiento predeterminada, seleccione **Eliminar**.

    ![HDInsight, eliminar clúster](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Eliminar clúster de HDInsight")

2. Si quiere eliminar el clúster y la cuenta de almacenamiento predeterminada, seleccione el nombre del grupo de recursos (resaltado en la captura de pantalla anterior) para abrir la página del grupo de recursos.

3. Seleccione **Eliminar grupo de recursos** para eliminar el grupo de recursos, que contiene el clúster y la cuenta de almacenamiento predeterminada. Tenga en cuenta que, al eliminar el grupo de recursos, se elimina también la cuenta de almacenamiento. Si desea mantener la cuenta de almacenamiento, elija Eliminar solo el clúster.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido cómo crear un clúster de HDInsight basado en Linux mediante una plantilla de Resource Manager y cómo realizar consultas básicas de Hive. En el siguiente artículo, aprenderá cómo realizar una operación ETL (extraer, transformar y cargar) mediante Hadoop en HDInsight.

> [!div class="nextstepaction"]
>[Extraer, transformar y cargar datos mediante Apache Hive en HDInsight ](../hdinsight-analyze-flight-delay-data-linux.md)

Si está listo para empezar a trabajar con sus propios datos y necesita más información acerca de la forma en que HDInsight o de cómo obtener datos en HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo HDInsight usa Azure Storage, consulte [Uso de Azure Storage con HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para más información sobre cómo cargar datos en HDInsight, consulte [Carga de datos en HDInsight](../hdinsight-upload-data.md).

Para más información sobre el análisis de datos con HDInsight, consulte los siguientes artículos:

* Para más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, consulte [Uso de Hive con HDInsight](../hdinsight-use-hive.md).
* Para más información sobre Pig, un lenguaje usado para transformar datos, consulte [Uso de Pig con HDInsight](../hdinsight-use-pig.md).
* Para más información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, consulte [Uso de MapReduce con HDInsight](../hdinsight-use-mapreduce.md).
* Para más información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight para ejecutar una consulta de Hive](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Si desea más información acerca de cómo crear o administrar un clúster de HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para más información acerca de las opciones que se pueden seleccionar al crear un clúster de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


