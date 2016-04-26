<properties
	pageTitle="Administración de clústeres de Hadoop basados en Linux en HDInsight usando el portal de Azure | Microsoft Azure"
	description="Aprenda a crear y administrar clústeres de HDInsight basados en Linux mediante el portal de Azure."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="jgao"/>

#Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Con el [Portal de Azure][azure-portal], puede administrar clústeres basados en Linux en HDInsight de Azure. Use el selector de pestañas para obtener información sobre cómo crear clústeres de Hadoop en HDInsight con otras herramientas.

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Abrir el portal

1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Después de abrir el portal, puede:

	- Hacer clic en **Nuevo** en el menú de la izquierda para crear un nuevo clúster:
	
		![botón nuevo clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
	- Hacer clic en **Clústeres de HDInsight** en el menú izquierdo para mostrar los clústeres existentes
	
		![Portal de Azure botón clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

        Si **HDInsight** no aparece en el menú de la izquierda, haga clic en **Examinar**.

        ![Portal de Azure botón Examinar clúster](./media/hdinsight-administer-use-portal-linux/azure-portal-browse-button.png)

##Creación de clústeres

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md). Para obtener información general sobre la creación de clústeres, consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Enumeración y visualización de clústeres

1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú izquierdo para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clústeres es extensa, puede usar el filtro de la parte superior de la página.
4. Haga doble clic en un clúster de la lista para mostrar los detalles.

	**Menú y operaciones básicas**:

	![Portal de Azure clúster de hdinsight operaciones básicas](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
	
	- **Configuración** y **Toda la configuración**: Muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
	- **Panel**, **Panel de clúster** y **Dirección URL: todas son formas de acceder al panel del clúster, que es la web de Ambari para los clústeres basados en Linux.
	- **Secure Shell**: muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH).
	- **Escalar clúster**: permite cambiar el número de nodos de trabajo de este clúster.
	- **Eliminar**: elimina el clúster.
	- **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.
	- **Usuarios (![icono de usuarios](./media/hdinsight-administer-use-portal-linux/users.png))**: permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.
	
		> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos de este clúster en el Portal de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.
	- **Etiquetas (![icono de etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png))**: las etiquetas permiten establecer pares de clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.
	- **Vistas de Ambari**: vínculos a la web de Ambari.
	
	> [AZURE.IMPORTANT] Para administrar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

	**Uso**
	
	![Botón de clúster HDInsight en el Portal de Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-cluster-usage.png)
	
5. Haga clic en **Configuración**.

	![Botón de clúster HDInsight en el Portal de Azure](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.cluster.settings.png)

	- **Registros de auditoría**:
    - **Inicio rápido**: muestra información que le ayudará a empezar a usar HDInsight.
	- **Inicio de sesión de clúster**: cambie el nombre de usuario y la contraseña HTTP del clúster.
	- **Escalar clúster**: aumente o disminuya el número de nodos de trabajo del clúster.
    - **Secure Shell**: muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH).
    - **Asociado de HDInsight**: agrega o quita el asociado actual de HDInsight.
	- **Tiendas de metadatos externas**: consulte las tiendas de metadatos de Hive y Oozie. Las tiendas de metadatos solo pueden configurarse durante el proceso de creación del clúster.
    - **Acciones de script**: ejecuta scripts de Bash en el clúster.
    - **Propiedades**: muestra las propiedades del clúster.
	- **Claves de almacenamiento de Azure**: consulte la cuenta de almacenamiento predeterminada y su clave. La cuenta de almacenamiento se configura durante el proceso de creación del clúster.
	- **Identidad AAD de clúster**: 
	- **Usuarios**: permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción a Azure.
	- **Etiquetas**: las etiquetas permiten establecer pares de clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.
    
    > [AZURE.NOTE] Se trata de una lista genérica de opciones de configuración disponibles; no todas ellas estarán presentes en todos los tipos de clúster.

6. Haga clic en **Propiedades**.

	Las propiedades son:
	
	- **Nombre del clúster**: nombre del clúster.
	- **Dirección URL del clúster**.
	- **Estado**: puede ser Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
	- **Región**: ubicación de Azure. Para una lista de ubicaciones de Azure admitidas, vea el cuadro de lista desplegable **Región** en [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
	- **Datos creados**.
	- **Sistema operativo**: **Windows** o **Linux**.
	- **Tipo**: Hadoop, HBase, Storm, Spark. 
	- **Versión**. Consulte [Versiones de HDInsight](hdinsight-component-versioning.md).
	- **Subscripción**: nombre de la suscripción.
	- **Id. de suscripción**.
    - **Origen de datos predeterminado**: el sistema de archivos predeterminado del clúster.
	- **Plan de tarifa de los nodos de trabajo**.
	- **Plan de tarifa de los nodos de Head**.

##Eliminación de clústeres

Eliminar un clúster no eliminará la cuenta de almacenamiento predeterminada ni otras cuentas de almacenamiento vinculadas. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y las mismas tiendas de metadatos. Se recomienda usar un nuevo contenedor de blobs predeterminado cuando vuelva a crear el clúster.

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. Haga clic en **Eliminar** en el menú superior y luego siga las instrucciones.

Vea también [Pausa o apagado de clústeres](#pauseshut-down-clusters).

##Escalado de clústeres
La característica de escalado de clústeres permite cambiar la cantidad de nodos de trabajo que usa un clúster que se ejecuta en HDInsight de Azure sin necesidad de volver a crear el clúster.

>[AZURE.NOTE] Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades. Vea [Enumeración y visualización de clústeres](hdinsight-adminster-use-management-portal/#list-and-show-clusters).

A continuación se muestra el efecto que tiene cambiar la cantidad de nodos de datos de cada tipo de clúster compatible con HDInsight:

- Hadoop

	Puede aumentar sin ningún problema la cantidad de nodos de trabajo en un clúster de Hadoop que se encuentre en ejecución, sin que afecte a ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.

	Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Esto provoca que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.

- HBase

	Puede agregar nodos sin problemas al clúster de HBase mientras se encuentra en ejecución, así como eliminarlos. Los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Sin embargo, puede equilibrar manualmente los servidores regionales iniciando sesión en el nodo principal del clúster y ejecutando los comandos siguientes desde una ventana del símbolo del sistema:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Para obtener más información sobre el uso del shell HBase, consulte
- Storm

	Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de finalizar correctamente la operación de escalado, deberá volver a equilibrar la topología.

	Esto se puede realizar de dos formas:

	* La interfaz de usuario web de Storm
	* La herramienta de la interfaz de línea de comandos (CLI)

	Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

	La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:

	![reequilibrio de escalado de storm de hdinsight](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)

	El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Para escalar clústeres**

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escalar clúster**.
4. Escriba el **Número de nodos de trabajo**. El límite del número de nodos del clúster varía según las suscripciones de Azure. Puede ponerse en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite. La información de costo reflejará los cambios realizados en el número de nodos.

	![hdinsight hadoop hbase storm spark escalar](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

##Pausa o apagado de clústeres

La mayoría de los trabajos de Hadoop son trabajos por lotes que se ejecutan sol ocasionalmente. En la mayoría de los clústeres de Hadoop, hay grandes períodos de tiempo en los que el clúster no se usa para el procesamiento. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso.

Hay muchas maneras de programar el proceso:

- Usar Factoría de datos de Azure. Consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight a petición con Data Factory de Azure](hdinsight-hadoop-create-linux-clusters-adf.md) para crear servicios vinculados a HDInsight a petición.
- Usar Azure PowerShell. Vea [Análisis de datos de retrasos de vuelos](hdinsight-analyze-flight-delay-data.md).
- Uso de CLI de Azure. Consulte [Administrar clústeres de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md).
- Usar .NET SDK de HDInsight. Vea [Envío de trabajos de Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Para información sobre precios, vea [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el portal, vea [Eliminar clústeres](#delete-clusters).

##Cambio del nombre de usuario del clúster

Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight (también conocida como cuenta de usuario HTTP) y la cuenta de usuario SSH se crean durante el proceso de creación. Puede hacer que la IU web de Ambari cambie el nombre de usuario y la contraseña de la cuenta de usuario del clúster:

**Para cambiar la contraseña del usuario del clúster de HDInsight**

1. Inicie sesión en la IU web de Ambari con las credenciales de usuario del clúster de HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://<HDInsight Cluster Name>azurehdinsight.net**.
2. Haga clic en **Administrador** en el menú superior y después haga clic en "Administrar Ambari". 
3. En el menú izquierdo, haga clic en **Usuarios**.
4. Haga clic en **Administrador**.
5. Haga clic en **Cambiar contraseña**.

##Concesión o revocación del acceso

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar o conceder el acceso mediante [CLI de Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) y [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

##Buscar el identificador de la suscripción

**Para buscar los identificadores de la suscripción de Azure**

1. Inicie sesión en el [Portal][azure-portal].
2. Haga clic en **Examinar todo** desde el menú izquierdo y después haga clic en **Suscripciones**. Cada suscripción tiene un nombre y un identificador.

Cada clúster está asociado a una suscripción de Azure. El identificador de la suscripción se muestra en el icono **Esencial** del clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

##Búsqueda del grupo de recursos 

En el modo ARM, cada clúster de HDInsight se crea con un grupo de recursos de Azure. El grupo de recursos de Azure al que pertenece un clúster aparece en:

- La lista de clústeres tiene una columna **Grupo de recursos**.
- Icono **Esencial** del clúster.  

Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).


##Búsqueda de la cuenta de almacenamiento predeterminada

Cada clúster de HDInsight tiene una cuenta de almacenamiento predeterminada. La cuenta de almacenamiento predeterminada y sus claves para un clúster aparecen en **Configuración**/**Propiedades**/**Claves de almacenamiento de Azure**. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).


##Ejecución de consultas de Hive

No puede ejecutar un trabajo de Hive directamente desde el Portal de Azure, pero puede usar la vista de Hive en la IU web de Ambari.

**Para ejecutar las consultas de Hive mediante la vista de Hive de Ambari**

1. Inicie sesión en la IU web de Ambari con las credenciales de usuario del clúster de HDInsight. El nombre de usuario predeterminado es **administrador**. La dirección URL es **https://<HDInsight Cluster Name>azurehdinsight.net**.
2. Abra la vista de Hive como se muestra en la siguiente captura de pantalla:  

	![vista de hive de hdinsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. Haga clic en **Consulta** en el menú superior.
4. Escriba una consulta de Hive en el **Editor de consultas** y después haga clic en **Ejecutar**.

##Supervisión de trabajos

Consulte [Administración de clústeres de HDInsight con la IU web de Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

##Examinar archivos

Con el Portal de Azure, puede examinar el contenido del contenedor predeterminado.

1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú izquierdo para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clústeres es extensa, puede usar el filtro de la parte superior de la página.
4. Haga clic en **Configuración**.
5. En la hoja **Configuración**, haga clic en **Claves de almacenamiento de Azure**.
6. Haga clic en el nombre de la cuenta de almacenamiento predeterminada.
7. Haga clic en el icono **Blobs**.
8. Haga clic en el nombre del contenedor predeterminado.


##Supervisión del uso de los clústeres

La sección __Uso__ de la hoja del clúster de HDInsight muestra información sobre el número de núcleos disponibles con la suscripción para su uso con HDInsight, así como el número de núcleos asignados al clúster y cómo se asignan a los nodos de este clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

> [AZURE.IMPORTANT] Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para más información sobre el uso de Ambari, vea [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

##Conectarse a un clúster

Consulte [Uso de Hive con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).
	
##Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Uso de Hive en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig en HDInsight](hdinsight-use-pig.md)
* [Uso de Sqoop en HDInsight](hdinsight-use-sqoop.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"

<!---HONumber=AcomDC_0413_2016-->