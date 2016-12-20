---
title: "Creación de clústeres de Hadoop en HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres para HDInsight de Azure con el Portal de Azure."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6680fb1e33b221a74d6c5db4cf5012d9109eac6


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Creación de clústeres de Hadoop basados en Windows en HDInsight con el Portal de Azure
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear un clúster de Hadoop en HDInsight con el Portal de Azure. El [Portal de Microsoft Azure](../azure-portal-overview.md) es una ubicación central desde donde se pueden aprovisionar y administrar los recursos de Azure. El Portal de Azure es una de las herramientas que puede utilizar para crear el clúster de Hadoop basado en Linux o en Windows en HDInsight. Para consultar otras herramientas y características de creación de clústeres, haga clic en la selección de pestaña de la parte superior de esta página o consulte los [métodos de creación de clústeres](hdinsight-provision-clusters.md#cluster-creation-methods).

## <a name="prerequisites"></a>Requisitos previos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creación de clústeres
**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Datos y análisis** y, luego, en **HDInsight**.
   
    ![Creación de un clúster en el Portal de Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")
3. Escriba o seleccione los valores siguientes:
   
   * **Nombre del clúster**: escriba un nombre para el clúster. Si el nombre está disponible, aparecerá una marca de verificación verde junto al nombre del clúster.
   * **Tipo de clúster**: seleccione **Hadoop**. Otra opciones incluyen **HBase**, **Storm** y **Spark**.
     
     > [!IMPORTANT]
     > Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método compatible para crear un clúster que combina varios tipos, como Storm y HBase en un clúster.
     > 
     > 
   * **Sistema operativo del clústeres**: seleccione **Windows**. Para crear un clúster basado en Linux, seleccione **Linux**.
   * **Versión**: consulte [Versiones de HDInsight](hdinsight-component-versioning.md).
   * **Suscripción**: seleccione la suscripción de Azure que se usará para crear este clúster.
   * **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo. Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
   * **Credenciales**: configure el nombre de usuario y la contraseña del usuario de Hadoop (usuario HTTP). Si habilita Escritorio remoto para el clúster, deberá configurar el nombre de usuario y la contraseña del Escritorio remoto y una fecha de caducidad de la cuenta. Haga clic en **Seleccionar** en la parte inferior para guardar los cambios.
     
          ![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")
   * **Origen de datos**: cree una cuenta de Almacenamiento de Azure o seleccione una ya existente para usar como sistema de archivos predeterminado para el clúster.
     
          ![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")
     
         * **Método de selección**: establezca esta opción en **De todas las suscripciones** para permitir la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.
         * **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
         * **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
         * **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento. Esta ubicación determinará la ubicación del clúster.  El clúster y su cuenta de almacenamiento predeterminada debe colocarse en el mismo centro de datos Azure.
   * **Niveles de precios de nodo**: establezca el número de nodos de trabajo que requiere para el clúster. El costo estimado del clúster se mostrará en la hoja.

        ![Hoja Niveles de precios de nodo](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


    * **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales, como unir una **red virtual**, configurar una instancia de **Metastore externa** para almacenar datos de Hive y Oozie, use las acciones de script para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

    * **Versión de HDInsight**: seleccione la versión que desee usar para el clúster. Para obtener más información, consulte [Versiones de clústeres de HDInsight](hdinsight-component-versioning.md).
    * **Red virtual**: seleccione una red virtual de Azure y la subred si quiere colocar el clúster en una red virtual.  

        ![Hoja de red virtual](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

        Para más información sobre el uso de HDInsight con una red virtual, como por ejemplo, los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).



    * Instancias de **Metastore externas**: especifique una base de datos SQL de Azure para guardar los metadatos de Hive y Oozie asociados con el clúster.

        > [AZURE.NOTE] La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

    ![Hoja de tiendas de metadatos personalizados](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

    En **Usar una Base de datos SQL existente para metadatos de Hive**, haga clic en **Sí**, seleccione una base de datos SQL y luego escriba el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una Base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.

    >[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

            &nbsp;

            > [AZURE.IMPORTANT] Al crear una tienda de metadatos, no debe usar un nombre de base de datos que contenga guiones, ya que esto puede producir un error en el proceso de creación del clúster.

          * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.


            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


        * **Claves de Azure Storage**: especifique cuentas de almacenamiento adicionales para asociar con el clúster. En la hoja **Claves de Azure Storage**, haga clic en **Agregar una clave de almacenamiento** y, luego, seleccione una cuenta de almacenamiento existente o cree una nueva.


            ![Hoja de almacenamiento adicional](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


1. Haga clic en **Crear**. Si selecciona **Anclar a Panel de inicio** , se agregará un icono del clúster en el Panel de inicio de su Portal. El icono indicará que el clúster se está creando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.
   
    El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.
2. Cuando termine la creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.

    ![Hoja de clúster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Cluster properties")


    Use las siguientes opciones para comprender los iconos de la parte superior de esta hoja y, en la sección **Conceptos básicos** :


    * **Configuración** y **Toda la configuración**: Muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
    * **Panel**, **Panel de clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.
    * **Escritorio remoto**: permite habilitar o deshabilitar Escritorio remoto en los nodos de clúster.
    * **Escalar clúster**: Permite cambiar el número de nodos de trabajo para este clúster.
    * **Eliminar**: elimina el clúster de HDInsight.
    * **Inicio rápido** (![cloud and thunderbolt icon = quickstart](./media/hdinsight-provision-clusters/quickstart.png)): muestra información que lo ayudará a empezar a usar HDInsight.
    * **Usuarios** (![icono de usuarios](./media/hdinsight-provision-clusters/users.png)): permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de la suscripción de Azure.


        > [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

    * **Etiquetas** (![icono de etiquetas](./media/hdinsight-provision-clusters/tags.png)): las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__y luego usar un valor común para todos los servicios asociados a un proyecto específico.

## <a name="customize-clusters"></a>Personalización de los clústeres
* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md) : aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md) : aprenda a enviar trabajos a HDInsight mediante programación
* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md)




<!--HONumber=Nov16_HO3-->


