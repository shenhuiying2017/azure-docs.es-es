<properties
	pageTitle="Administración de clústeres de Hadoop en HDInsight mediante el portal de vista previa de Azure | Microsoft Azure"
	description="Vea cómo administrar el servicio HDInsight. Cree un clúster de HDInsight, abra la consola interactiva de JavaScript y la consola de comandos de Hadoop."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight mediante el portal de vista previa de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Con el [portal de vista previa de Azure][azure-portal] puede aprovisionar clústeres de Hadoop en HDInsight de Azure, cambiar la contraseña de usuario de Hadoop y habilitar el Protocolo de escritorio remoto (RDP) para que pueda tener acceso a la consola de comandos de Hadoop en el clúster.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]Los pasos descritos en este documento son específicos para trabajar con los clústeres de Hadoop basados en Windows. Para obtener información sobre cómo trabajar con clústeres basados en Linux, consulte [Administración de clústeres de Hadoop en HDInsight mediante el portal de vista previa de Azure](hdinsight-administer-use-portal-linux.md).



> [AZURE.NOTE]Hay otras herramientas disponibles para administrar HDInsight además del portal de vista previa de Azure.
>
> - [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
> - [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Cuenta de Almacenamiento de Azure**: un clúster de HDInsight usa contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Para obtener más información acerca de cómo el almacenamiento de blobs de Azure ofrece una experiencia perfecta con los clústeres de HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de Almacenamiento de Azure, consulte [Creación de una cuenta de Almacenamiento](../storage-create-storage-account.md).


##Aprovisionamiento de clústeres de HDInsight

Para obtener las instrucciones de aprovisionamiento mediante el portal de vista previa, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md#portal).

##Personalización del aprovisionamiento de clústeres de HDInsight

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md). Puede personalizar HDInsight mediante una de las opciones siguientes:

- Use Generar script de acción para ejecutar scripts que pueden personalizar un clúster para cambiar la configuración del clúster o para instalar los componentes personalizados, como Giraph o Solr. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante Generar acción de script](hdinsight-hadoop-customize-cluster.md).
- Use los parámetros de personalización de clústeres en .NET SDK de HDInsight o PowerShell de Azure durante el aprovisionamiento de clústeres. Estos cambios de configuración se conservan durante toda la vida del clúster y no se ven afectados por el restablecimiento de imágenes de nodos del clúster que la plataforma Azure realiza periódicamente para su mantenimiento. Para obtener más información acerca del uso de los parámetros de personalización del clúster, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).
- Algunos de los componentes nativos de Java, como Mahout y Cascading, se pueden ejecutar en el clúster como archivos JAR. Estos archivos JAR se pueden distribuir al almacenamiento de blobs de Azure y enviarse a los clústeres de HDInsight usando los mecanismos de envío de trabajo de Hadoop. Para obtener más información, consulte [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]Si tiene problemas con la implementación de los archivos JAR en los clústeres de HDInsight o con la llamada de los archivos JAR en los clústeres de HDInsight, póngase en contacto con [Soporte técnico de Microsoft](http://azure.microsoft.com/support/options/).

	> La presentación en cascada no se admite en HDInsight, y no puede optar a recibir soporte técnico de Microsoft. Para ver las listas de los componentes compatibles, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](hdinsight-component-versioning.md).


La instalación de software personalizado en el clúster mediante la Conexión a Escritorio remoto no es compatible. Debe evitar el almacenamiento de cualquier archivo en las unidades del nodo principal, debido a que se perderán si necesita volver a crear los clústeres. Recomendamos almacenar los archivos en el almacenamiento de blobs de Azure. El almacenamiento de blobs es persistente.

## Familiarizarse con la interfaz del portal del clúster.

**Para tener acceso al clúster**

1. Inicie sesión en el [portal de vista previa][azure-portal].
2. Haga clic en **Examinar todo** en el menú izquierdo para abrir la hoja **Examinar**.
3. Haga clic en **Clústeres de HDInsight** para abrir la hoja **Clústeres de HDInsight**.
4. Haga clic en uno de los clústeres de la lista para abrir la hoja del clúster:

	![Portal de HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. Haga clic en **Configuración** para ver los detalles de configuración y modificar los del clúster:

	![Configuración de clúster del portal de HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|Configuración|Descripción|
	|-----|-----|
	|Propiedades| Mostrar las propiedades de clúster.|
	|Claves de almacenamiento de Azure| Mostrar la información de la cuenta de almacenamiento de Azure predeterminada. |
	|Inicio de sesión de clúster| Otorgar o revocar el acceso a los servicios web y configurar la información de inicio de sesión de clúster. |
	|Tiendas de metadatos externas| Mostrar la información de la tienda de metadatos de Oozie o Hive.|
	|Escalar clúster| Aumentar o disminuir el número de nodos de trabajo del clúster.|
	|Escritorio remoto| Habilitar o deshabilitar la conectividad de Escritorio remoto, conectarse al clúster mediante Escritorio remoto.|


##Concesión/Revocación del acceso a los servicios de HTTP

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso desde el Portal de Azure.

>[AZURE.NOTE]Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del clúster.

**Para conceder/revocar el acceso a los servicios web de HTTP**

1. Inicie sesión en el [portal de vista previa][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Inicio de sesión de clúster**.
5. Haga clic en **Activado** o **Desactivado** en **Acceso de inicio de sesión de clúster**.  
6. Como **Nombre de usuario del clúster** y **Contraseña del clúster**, escriba el nombre de usuario y la contraseña nuevos (respectivamente) del clúster.
7. Haga clic en **GUARDAR**.

	![hdinsight grand eliminar acceso al servicio web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


Esto también se puede hacer con los cmdlets de PowerShell de Azure:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md).

##Cambio de nombre de usuario y contraseña del clúster de HDInsight
Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight se crea durante el proceso de aprovisionamiento. También puede crear una cuenta de usuario de RDP para tener acceso al clúster mediante RDP. Consulte [Habilitación de escritorio remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Para cambiar el nombre de usuario y contraseña del clúster de HDInsight**

1. Inicie sesión en el [portal de vista previa][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Inicio de sesión de clúster**.
4. Cambie el **Nombre de inicio de sesión del clúster** o la **Contraseña de inicio de sesión del clúster** y haga clic en **Guardar**.

	![hdinsight cambiar nombre de usuario contraseña de clúster http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##Escalado de clústeres
La característica de escalado de clústeres permite cambiar la cantidad de nodos de trabajo que usa un clúster que se ejecuta en HDInsight de Azure sin necesidad de volver a crear el clúster.

>[AZURE.NOTE]Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades. Consulte [Familiarizarse con la interfaz del portal del clúster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

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

	![reequilibrio de escalado de storm de hdinsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Para escalar clústeres**

1. Inicie sesión en el [portal de vista previa][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escalar clúster**.
4. Escriba el **Número de nodos de trabajo**. El límite del número de nodos del clúster varía según las suscripciones de Azure. Puede ponerse en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite. La información de costo reflejará los cambios realizados en el número de nodos.


	![hdinsight hadoop hbase storm spark escalar](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##Conexión a los clústeres de HDInsight con RDP

Las credenciales para el clúster que proporcionó en su creación dan acceso a los servicios en el clúster, pero no al clúster mismo a través del Escritorio remoto. Puede activar el acceso de Escritorio remoto cuando aprovisiona un clúster o después de su aprovisionamiento. Para obtener instrucciones sobre cómo habilitar Escritorio remoto en el aprovisionamiento, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).

**Para habilitar el Escritorio remoto**

1. Inicie sesión en el [portal de vista previa][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escritorio remoto**.
4. Especifique los valores de **Expira en**, **Nombre de usuario de Escritorio remoto** y **Contraseña de Escritorio remoto** y haga clic en **Habilitar**.

	![hdinsight habilitar deshabilitar configurar escritorio remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	El valor predeterminado de Expira en es de una semana.
> [AZURE.NOTE]También puede usar .NET SDK de HDInsight para habilitar el Escritorio remoto en un clúster. Use el método **EnableRdp** en el objeto de cliente de HDInsight de la siguiente manera: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. De forma similar, para deshabilitar el Escritorio remoto en el clúster, puede usar **client.DisableRdp(clustername, location)**. Para obtener más información sobre estos métodos, consulte [Referencia de .NET SDK de HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Esto solo se aplica a los clústeres de HDInsight que se ejecutan en Windows.

**Para conectarse a un clúster con RDP**

1. Inicie sesión en el [portal de vista previa][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escritorio remoto**.
4. Haga clic en **Conectar** y siga las instrucciones. Si Conectar está deshabilitado, debe habilitarlo primero. Asegúrese de usar el nombre de usuario y la contraseña de Escritorio remoto. No puede usar las credenciales de usuario del clúster.

##Apertura de una línea de comandos de Hadoop

Para conectarse al clúster mediante el Escritorio remoto y usar la línea de comandos de Hadoop, primero debe tener habilitado el acceso del Escritorio remoto al clúster como se describe en la sección anterior.

**Para abrir una línea de comandos de Hadoop**

1. Conéctese al clúster a través del Escritorio remoto.
8. En el escritorio, haga doble clic en **Línea de comandos de Hadoop**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obtener más información acerca de los comandos de Hadoop, consulte [Referencia de comandos de Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

En la captura de pantalla anterior, el nombre de la carpeta tiene el número de versión de Hadoop incrustado. El número de versión se puede cambiar según la versión de los componentes de Hadoop instalados en el clúster. Puede usar las variables de entorno de Hadoop para referirse a esas carpetas. Por ejemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%


##Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el portal de vista previa y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](../hdinsight-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Página de aterrizaje del clúster"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Línea de comandos de Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=Oct15_HO3-->