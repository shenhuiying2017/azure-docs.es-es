---
title: "Creación de clústeres de Hadoop con un explorador web - Azure HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres Hadoop, HBase, Storm o Spark en Linux para HDInsight con un explorador web y el Portal de vista previa de Azure."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: c200b405d8ae2548e16548b88683cf6de4aef2b3
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Crear clústeres basados en Linux en HDInsight con el Portal de Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

El Portal de Azure es una herramienta de administración basada en web para servicios y recursos hospedados en la nube de Microsoft Azure. En este artículo aprenderá a crear clústeres de HDInsight basados en Linux mediante el portal.

## <a name="prerequisites"></a>requisitos previos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un explorador web moderno**. Azure Portal usa HTML5 y Javascript, y puede que no funcione correctamente en exploradores web más antiguos.

## <a name="create-clusters"></a>Creación de clústeres
El Portal de Azure expone la mayor parte de las propiedades del clúster. Mediante la plantilla de Azure Resource Manager puede ocultar una gran cantidad de detalles. Para más información, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **+**, en **Intelligence + analytics** y después en **HDInsight**.
   
    ![Creación de un nuevo clúster en el portal de Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "Creación de un nuevo clúster en el portal de Azure")

3. En la hoja **HDInsight**, haga clic en **Custom (size, settings, apps)** [Personalizar (tamaño, configuración y aplicaciones)], haga clic en **Básico** y después escriba la siguiente información.

    ![Creación de un nuevo clúster en el portal de Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "Creación de un nuevo clúster en el portal de Azure")

    * Especifique el **nombre del clúster**: dicho nombre debe ser único globalmente.

    * En la lista desplegable **Suscripción**, seleccione la suscripción de Azure que se usa para el clúster.

    * Haga clic en **Tipo de clúster** y, a continuación, seleccione el tipo de clúster (Hadoop, Spark, etc.) que quiere crear. En **Sistema operativo**, haga clic en **Linux** y, a continuación, seleccione una versión. Use la versión predeterminada si no sabe qué opción elegir. Para obtener más información, consulte [Versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

        Para los tipos de clúster de Hadoop, Spark e Interactive Query, puede elegir instalar el **Paquete de seguridad de la empresa**. El paquete de seguridad de la empresa habilita características de seguridad como la integración de Azure Active Directory y Apache Ranger para los clústeres. Para obtener más información, consulte el [paquete de seguridad de la empresa en Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

        ![Habilitar el paquete de seguridad de la empresa](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-enable-enterprise-security-package.png "Habilitar el paquete de seguridad de empresa")
     
        > [!IMPORTANT]
        > Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método admitido para crear un solo clúster que combine varios tipos, como Storm y HBase. 
        > 
        > 
        
    * Como **Nombre de usuario de inicio de sesión del clúster** y **Contraseña de inicio de sesión de clúster**, escriba el nombre de usuario y la contraseña para el usuario administrador.

    * Escriba un **Nombre de usuario de SSH** y, si desea que la contraseña SSH sea igual que la contraseña de administrador que especificó anteriormente, seleccione la casilla **Utilizar la misma contraseña que en el inicio de sesión del clúster**. De no ser así, especifique una **CONTRASEÑA** o **CLAVE PÚBLICA**, que se usarán para autenticar al usuario de SSH. Es recomendable usar una clave pública. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.
   
    Para más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Para **Grupo de recursos**, especifique si desea crear un grupo de recursos o utilizar uno existente.

    * Especifique la **ubicación** del centro de datos donde se creó el clúster.

    * Haga clic en **Next**.

4. En **Almacenamiento**, especifique si desea que Azure Storage (WASB) o Data Lake Store sea el servicio de almacenamiento predeterminado. Para más información, consulte la tabla siguiente.

    ![Creación de un nuevo clúster en el portal de Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "Creación de un nuevo clúster en el portal de Azure")

    | Storage                                      | DESCRIPCIÓN |
    |----------------------------------------------|-------------|
    | **Blobs de Azure Storage como almacenamiento predeterminado**   | <ul><li>En **Tipo de almacenamiento principal**, seleccione **Azure Storage**. Después de eso, en **Método de selección**, puede elegir **Mis suscripciones** si desea especificar una cuenta de almacenamiento que forma parte de su suscripción de Azure y, a continuación, seleccione la cuenta de almacenamiento. En caso contrario, haga clic en **Clave de acceso** y proporcione la información de la cuenta de almacenamiento que desea elegir fuera de la suscripción de Azure.</li><li>En **Contenedor predeterminado**, puede optar por el nombre de contenedor predeterminado que se sugiere en el portal o especificar uno propio.</li><li>Si usa WASB como almacenamiento predeterminado, puede (opcionalmente) hacer clic en **Cuentas de almacenamiento adicionales** para especificar cuentas de almacenamiento adicionales que se van a asociar con el clúster. En **Claves de Azure Storage**, haga clic en **Agregar una clave de almacenamiento** y, a continuación, proporcione una cuenta de almacenamiento de las suscripciones de Azure o de otras suscripciones (para ello puede proporcionar la clave de acceso de la cuenta de almacenamiento).</li><li>Si usa WASB como almacenamiento predeterminado, puede (opcionalmente) hacer clic en **Acceso a Data Lake Store** para especificar Azure Data Lake Store como almacenamiento adicional. Para más información, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Azure Data Lake Store como almacenamiento predeterminado** | En **Tipo de almacenamiento principal**, seleccione **Data Lake Store** y, a continuación, consulte el artículo [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) para obtener instrucciones. |
    | **Tiendas de metadatos externas**                      | De manera opcional, puede especificar una base de datos SQL para guardar los metadatos de Hive y Oozie asociados con el clúster. En **Seleccionar una base de datos SQL para Hive**, seleccione una base de datos SQL y luego escriba el nombre de usuario y la contraseña de la base de datos. Repita estos pasos para los metadatos de Oozie.<br><br>Algunas consideraciones al usar Azure SQL Database para las tiendas de metadatos. <ul><li>La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.</li><li>Al crear una tienda de metadatos, no debe usar un nombre de base de datos que contenga guiones, ya que esto puede producir un error en el proceso de creación del clúster.</li></ul> |

    Haga clic en **Next**. 

    > [!WARNING]
    > No se admite el uso de una cuenta de almacenamiento adicional en una ubicación diferente a la del clúster de HDInsight.

5. De manera opcional, haga clic en **Aplicaciones** para instalar aplicaciones que funcionen con clústeres de HDInsight. Estas aplicaciones puede desarrollarlas Microsoft, fabricantes de software independientes (ISV) o el propio usuario. Para más información, consulte [Instalación de aplicaciones de HDInsight](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Haga clic en **Tamaño del clúster** para mostrar información sobre los nodos que se usarán para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. También se muestra el costo estimado de la ejecución del clúster.
   
    ![Planes de tarifa de nodos](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "Especificación del número de nodos del clúster")
   
   > [!IMPORTANT]
   > Si planea crear más de 32 nodos de trabajo, al crear el clúster o mediante el escalado del clúster después de su creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
   > 
   > Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Haga clic en **Siguiente** para guardar la configuración de precios del nodo.

7. Haga clic en **Configuración avanzada** para configurar otros valores de configuración opcionales, como el uso de **Acciones de script** para personalizar un clúster para instalar componentes personalizados o unirse a una **red virtual**. Para más información, consulte la tabla siguiente.

    ![Planes de tarifa de nodos](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "Especificación del número de nodos del clúster")

    | Opción | DESCRIPCIÓN |
    |--------|-------------|
    | **Acciones de script** | Use esta opción si quiere usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información sobre acciones de script, vea [Personalización de clústeres de HDInsight con una acción de script](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Virtual Network** | Seleccione una red virtual de Azure y la subred si quiere colocar el clúster en una red virtual. Para más información sobre el uso de HDInsight con una red virtual como, por ejemplo, los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). |

    Haga clic en **Next**.

8. En **Resumen**, compruebe la información introducida anteriormente y luego haga clic en **Crear**.

    ![Planes de tarifa de nodos](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "Especificación del número de nodos del clúster")
    
    > [!NOTE]
    > El clúster tarda algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** en la parte izquierda de la página para comprobar el proceso de aprovisionamiento.
    > 
    > 
12. Cuando termine el proceso de creación, haga clic en el icono del clúster desde el panel de inicio. La ventana del clúster ofrece la siguiente información.
    
    ![Interfaz del clúster](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "Propiedades del clúster")
    
    Use las siguientes opciones para comprender los iconos de la parte superior.
    
    * **Información general**: pestaña que proporciona toda la información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.
    * **Panel**: lo remite al portal de Ambari asociado con el clúster.
    * **Shell seguro**: información necesaria para acceder al clúster mediante SSH.
    * **Escalar clúster**: permite aumentar el número de nodos de trabajo asociados con el clúster.
    * **Eliminar**: elimina el clúster de HDInsight.
    

## <a name="customize-clusters"></a>Personalización de los clústeres
* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminación del clúster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>pasos siguientes
Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster:

### <a name="hadoop-clusters"></a>Clústeres Hadoop
* [Uso de Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hadoop/hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clústeres HBase
* [Introducción a HBase en HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres Storm
* [Desarrollo de topologías de Java para Storm en HDInsight](storm/apache-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clústeres de Spark
* [Crear una aplicación independiente con Scala](spark/apache-spark-create-standalone-application.md)
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](spark/apache-spark-livy-rest-interface.md)
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](spark/apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

