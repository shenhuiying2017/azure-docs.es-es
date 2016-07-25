<properties
   pageTitle="Creación de clústeres de Hadoop en HDInsight | Microsoft Azure"
   	description="Aprenda a crear clústeres para HDInsight de Azure con el Portal de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/10/2016"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop basados en Windows en HDInsight con el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear un clúster de Hadoop en HDInsight con el Portal de Azure. El [Portal de Microsoft Azure](../azure-portal-overview.md) es una ubicación central desde donde se pueden aprovisionar y administrar los recursos de Azure. El Portal de Azure es una de las herramientas que puede utilizar para crear el clúster de Hadoop basado en Linux o en Windows en HDInsight. Para consultar otras herramientas y características de creación de clústeres, haga clic en la selección de pestaña de la parte superior de esta página o consulte los [métodos de creación de clústeres](hdinsight-provision-clusters.md#cluster-creation-methods).

##Requisitos previos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Creación de clústeres


**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.

    ![Creación de un clúster nuevo en el Portal de Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creación de un clúster nuevo en el Portal de Azure")

3. Escriba o seleccione los valores siguientes:

  * **Nombre del clúster**: escriba un nombre para el clúster. Si el nombre está disponible, aparecerá una marca de verificación verde junto al nombre del clúster.
  * **Tipo de clúster**: seleccione **Hadoop**. Otra opciones incluyen **HBase**, **Storm** y **Spark**.
  * **Sistema operativo del clústeres**: seleccione **Windows**. Para crear un clúster basado en Linux, seleccione **Linux**.
  * **Versión**: consulte [Versiones de HDInsight](hdinsight-component-versioning.md).
  * **Suscripción**: seleccione la suscripción de Azure que se usará para crear este clúster.
  * **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo. Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
  * **Credenciales**: configure el nombre de usuario y la contraseña del usuario de Hadoop (usuario HTTP). Si habilita Escritorio remoto para el clúster, deberá configurar el nombre de usuario y la contraseña del Escritorio remoto y una fecha de caducidad de la cuenta. Haga clic en **Seleccionar** en la parte inferior para guardar los cambios.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Origen de datos**: cree una cuenta de Almacenamiento de Azure o seleccione una ya existente para usar como sistema de archivos predeterminado para el clúster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Niveles de precios de nodo**: establezca el número de nodos de trabajo que requiere para el clúster. El costo estimado del clúster se mostrará en la hoja.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales como, por ejemplo, unirse a una **Red virtual**, configurar una **Tienda de metadatos externa** para almacenar datos de Hive y Oozie, usar Acciones de script a fin de personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.

            &nbsp;

            > [AZURE.IMPORTANT] When creating a metastore, do not use a database name that contains dashes or hyphens, as this can cause the cluster creation process to fail.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Haga clic en **Crear**. Si selecciona **Anclar a Panel de inicio**, se agregará un icono del clúster en el Panel de inicio de su Portal. El icono indicará que el clúster se está creando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.
	
    El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.
	

5. Cuando termine la creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.


	![Hoja de clúster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propiedades de clúster")


	Use las siguientes opciones para comprender los iconos de la parte superior de esta hoja y, en la sección **Conceptos básicos**:


	* **Configuración** y **Toda la configuración**: Muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
	* **Panel**, **Panel de clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.
	* **Escritorio remoto**: permite habilitar o deshabilitar Escritorio remoto en los nodos de clúster.
	* **Escalar clúster**: Permite cambiar el número de nodos de trabajo para este clúster.
	* **Eliminar**: elimina el clúster de HDInsight.
	* **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-provision-clusters/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.
	* **Usuarios** (![icono de usuarios](./media/hdinsight-provision-clusters/users.png)): permite establecer permisos de _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.
	

		> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.
		
	* **Etiquetas** (![icono de etiqueta](./media/hdinsight-provision-clusters/tags.png)): las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.

##Personalización de los clústeres

- Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md).

##Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md): aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md): aprenda a enviar trabajos a HDInsight mediante programación
* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0713_2016-->