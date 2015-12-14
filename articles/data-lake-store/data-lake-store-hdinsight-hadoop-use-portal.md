<properties 
   pageTitle="Aprovisionamiento de clústeres de Hadoop en HDInsight con el Almacén de Azure Data Lake mediante el Portal | Azure" 
   description="Use el Portal de Azure para configurar y usar clústeres de Hadoop en HDInsight con el Almacén de Azure Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar el Portal de Azure para configurar un clúster de HDInsight (Hadoop, HBase o Storm) para trabajar con un Almacén de Azure Data Lake. Algunas consideraciones importantes sobre esta versión:

* **En clústeres de Hadoop y Storm (Windows y Linux)**, el almacén de Data Lake solo puede usarse como cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán Blobs de almacenamiento de Azure (WASB).

* **En clústeres HBase (Windows y Linux)**, el almacén de Data Lake puede usarse como almacenamiento predeterminado o almacenamiento adicional.


En este artículo, aprovisionamos un clúster de Hadoop con el Almacén de Data Lake como almacenamiento adicional. La configuración de HDInsight para trabajar con el Almacén de Data Lake mediante el Portal de Azure consta de los pasos siguientes:

* Creación de un clúster de HDInsight con autenticación ante una entidad de servicio de Azure Active Directory
* Configuración del acceso al Almacén de Data Lake con la misma entidad de servicio
* Ejecución de un trabajo de prueba en el clúster

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).


## Creación de un clúster de HDInsight con autenticación ante una entidad de servicio de Azure Active Directory

En esta sección, se crea un clúster de Hadoop en HDInsight que usa el Almacén de Data Lake como almacenamiento adicional. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Siga los pasos descritos en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para iniciar el aprovisionamiento de un clúster de HDInsight.
 
3. En la hoja **Configuración opcional**, haga clic en **Origen de datos**. En la hoja **Origen de datos**, especifique los detalles de la cuenta de almacenamiento y el contenedor de almacenamiento, especifique **Ubicación** como **Este de EE. UU. 2** y después haga clic en **Identidad de AAD de clúster**.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Agregar entidad de servicio al clúster de HDInsight")

4. En la hoja **Identidad de AAD de clúster**, puede seleccionar una entidad de servicio existente o crear una nueva.
	
	* **Cree una nueva entidad de servicio**. En la hoja **Identidad de AAD de clúster**, haga clic en **Crear nuevo**. Haga clic en **Entidad de servicio** y después, en la hoja **Crear una entidad de servicio**, proporcione valores para crear una nueva entidad de servicio. En este paso, también se crean un certificado y una aplicación de Azure Active Directory. Haga clic en **Crear**.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Agregar entidad de servicio al clúster de HDInsight")

		En la hoja **Identidad de AAD de clúster**, haga clic en **Seleccionar** para continuar con la entidad de servicio que se va a crear.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Agregar entidad de servicio al clúster de HDInsight")


	* **Elija una entidad de servicio existente**. En la hoja **Identidad de AAD de clúster**, haga clic en **Usar existente**. Haga clic en **Entidad de servicio** y después, en la hoja **Seleccionar una entidad de servicio**, busque una entidad de servicio existente. Haga clic en un nombre de entidad de servicio y después en **Seleccionar**.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Agregar entidad de servicio al clúster de HDInsight")

		En la hoja **Identidad de AAD de clúster**, cargue el certificado (.pfx) que creó antes y proporcione la contraseña que usó para crear el certificado. Haga clic en **Seleccionar**. Así se completa la configuración de Azure Active Directory para el clúster de HDInsight.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Agregar entidad de servicio al clúster de HDInsight")

6. Haga clic en **Seleccionar** en la hoja **Origen de datos** y continúe con el aprovisionamiento del clúster tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Una vez aprovisionado el clúster, puede comprobar que la entidad de servicio esté asociada con el clúster de HDInsight. Para hacerlo, en la hoja del clúster, haga clic en **Configuración** y en **Identidad de AAD de clúster**; debería ver la entidad de servicio asociada.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Agregar entidad de servicio al clúster de HDInsight")

## <a name="acl"></a>Configuración de la entidad de servicio para acceder al sistema de archivos del Almacén de Data Lake

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Si no tiene una cuenta de Almacén de Data Lake, créela. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md).

	Si ya tiene una cuenta de Almacén de Data Lake, en el panel izquierdo, haga clic en **Examinar**, en **Almacén de Data Lake** y después en el nombre de cuenta a la que desea conceder acceso.

	Realice las tareas siguientes en su cuenta de Almacén de Data Lake.

	* [Cree una carpeta en el Almacén de Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Cargue un archivo en su Almacén de Data Lake](data-lake-store-get-started-portal.md#uploaddata). Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData).

	Usará los archivos cargados más adelante al probar la cuenta de Almacén de Data Lake con el clúster de HDInsight.

3. En la hoja del Almacén de Data Lake, haga clic en **Explorador de datos**.

	![Explorador de datos](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "Explorador de datos")

4. En la hoja **Explorador de datos**, haga clic en la raíz de su cuenta y después, en la hoja de la cuenta, haga clic en el icono **Acceso**.

	![Establecer las ACL en el sistema de archivos de Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "Establecer las ACL en el sistema de archivos de Data Lake")

5. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado e incluir la entidad de servicio que creó antes.

	![Mostrar acceso estándar y personalizado](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "Mostrar acceso estándar y personalizado")

6. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y después, en la hoja **Seleccionar usuario o grupo**, busque la entidad de servicio que creó antes en Azure Active Directory. El nombre de la entidad de servicio creada antes es **HDIADL**. Haga clic en el nombre de la entidad de servicio y después en **Seleccionar**.

	![Agregar un grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "Agregar un grupo")

7. Haga clic en **Seleccionar permisos**, seleccione los permisos que desea asignar a la entidad de servicio y después haga clic en **Aceptar**.

	![Asignar permisos al grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "Asignar permisos al grupo")

8. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, se mostrará ahora en la hoja **Acceso**.

	![Asignar permisos al grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "Asignar permisos al grupo")

7. Si es necesario, también puede modificar los permisos de acceso después de agregar la entidad de servicio. Active o desactive la casilla para cada tipo de permiso (lectura, escritura, ejecución) en función de si desea quitarlo o asignarlo. Haga clic en **Guardar** para guardar los cambios o en **Descartar** para deshacerlos.



## Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Azure Data Lake

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight puede acceder a datos en el Almacén de Azure Data Lake. Para hacerlo, vamos a ejecutar algunas consultas de Hive que tienen como destino el Almacén de Data Lake.

1. Abra la hoja del clúster que acaba de aprovisionar y después haga clic en **Panel**.

	![Inicie el panel del clúster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Inicie el panel del clúster")

	Cuando se le solicite, escriba las credenciales de administrador para el clúster.

2. Se abre la consola de consulta de HDInsight de Microsoft Azure. Haga clic en **Editor de Hive**.

	![Abrir el Editor de Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Abrir el Editor de Hive")

3. En el Editor de Hive, escriba la siguiente consulta y después haga clic en **Enviar**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	En esta consulta de Hive, creamos una tabla con los datos almacenados en el almacén de Data Lake en `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Esta ubicación contiene un archivo de datos de ejemplo que debería haber cargado antes.

	La tabla **Sesión de trabajo** en la parte inferior muestra el estado del trabajo, que cambia de **Inicializando** a **Ejecutando** y después a **Completado**. También puede hacer clic en **Ver detalles** para obtener más información sobre el trabajo completado.

	![Crear tabla](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Crear tabla")

4. Ejecute la siguiente consulta para comprobar que se creó la tabla.

		SHOW TABLES;

	Haga clic en Ver detalles para esta consulta; se debería mostrar lo siguiente:

		hivesampletable
		vehicles

	**vehicles** es la tabla que creó antes. La tabla de ejemplo **hivesampletable** está disponible en todos los clústeres de HDInsight de forma predeterminada.

5. Ahora también puede ejecutar una consulta para recuperar datos de **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

## Acceso al Almacén de Data Lake mediante comandos de HDFS

Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar**, en **Clústeres de HDInsight** y después en el clúster de HDInsight que creó.

3. En la hoja del clúster, haga clic en **Escritorio remoto** y después, en la hoja **Escritorio remoto**, haga clic en **Conectar**.

	![Conexión remota con un clúster de HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Crear un grupo de recursos de Azure")

	Cuando se le solicite, escriba las credenciales que proporcionó para el usuario de Escritorio remoto.

4. En la sesión remota, inicie Windows PowerShell y use los comandos del sistema de archivos de HDFS para enumerar los archivos en el Almacén de Azure Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## Consideraciones para aprovisionar un clúster de HBase que use el Almacén de Data Lake como almacenamiento predeterminado

Para los clústeres de HBase, puede usar cuentas de Almacén de Data Lake como almacenamiento predeterminado. Si decide hacerlo, para aprovisionar un clúster correctamente, la entidad de servicio asociada con el clúster **debe** tener acceso a la cuenta del almacén de Data Lake. Puede asegurarse de esto de dos maneras:

* **Si usa una entidad de servicio existente**, debe comprobar que la entidad de servicio se agregue a la ACL en el nivel raíz del sistema de archivos del almacén de Data Lake antes de iniciar el aprovisionamiento del clúster.
* **Si opta por crear una nueva entidad de servicio** como parte del aprovisionamiento del clúster, en cuanto se inicie dicho proceso, debe agregar la entidad de servicio recién creada al nivel raíz del sistema de archivos del almacén de Data Lake. Si no lo hace, el clúster se aprovisionará, pero los servicios de HBase no se podrán iniciar. Para solucionar este problema, deberá agregar la entidad de servicio a la ACL de la cuenta de Almacén de Data Lake y después reiniciar los servicios de HBase mediante la interfaz de usuario de Web de Ambari.

Para obtener instrucciones sobre cómo agregar una entidad de servicio a un sistema de archivos del almacén de Data Lake, consulte [Configuración de la entidad de servicio para acceder al sistema de archivos del almacén de Data Lake](#acl).



## Otras referencias

* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/es-ES/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/es-ES/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_1203_2015-->