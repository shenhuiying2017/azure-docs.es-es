<properties 
   pageTitle="Aprovisionamiento de clústeres de Hadoop en Linux en HDInsight | Azure" 
   description="Aprenda a aprovisionar clústeres de Hadoop en Linux para HDInsight usando el portal de administración, la línea de comandos y el SDK de .NET." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>


# Aprovisionamiento de clústeres de Hadoop Linux en HDInsight usando opciones personalizadas (vista previa)

En este artículo aprenderá las diferentes maneras de aprovisionar un clúster de Hadoop Linux de forma personalizada en HDInsight de Azure usando el Portal de administración de Azure, PowerShell, herramientas de la línea de comandos o el SDK de .NET de HDInsight.

## ¿Qué es un clúster de HDInsight?

¿Se ha preguntado alguna vez por qué mencionamos clústeres cada vez que hablamos sobre Hadoop o BigData? El motivo es porque Hadoop permite el procesamiento distribuido de datos grandes y el despliegue a través de diferentes nodos de un clúster. El clúster tiene una arquitectura maestro/esclavo con un maestro (también llamado nodo principal o nodo de nombres) y cualquier número de esclavos (también llamado nodo de datos). Para obtener más información, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight Cluster][img-hdi-cluster]

Un clúster de HDInsight abstrae los detalles de implementación de Hadoop de manera que no tiene que preocuparse sobre cómo comunicarse con diferentes nodos de un clúster. Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. Para obtener más información, consulte [Introducción a Hadoop en HDInsight](../hdinsight-hadoop-introduction/). Los datos que se van a renovar se guardan en el almacenamiento de blobs de Azure, también denominado *Azure Storage - Blob* (o WASB) en el contexto de HDInsight. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage/).

En este artículo se proporcionan instrucciones sobre diferentes formas de aprovisionar un clúster. Si busca un enfoque rápido para aprovisionar un clúster, consulte [Introducción a HDInsight de Azure en Linux](../hdinsight-hadoop-linux-get-started).

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de HDInsight PowerShell realizan las tareas con su suscripción. Para obtener más información sobre cómo obtener una suscripción, consulte <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">Opciones de compra</a>, <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">Ofertas para miembros</a>o <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">Versión de prueba gratuita</a>.
- Claves SSH. Si desea tener acceso remoto a un clúster de Linux mediante SSH Para obtener instrucciones sobre cómo generar claves SSH, consulte los siguientes artículos:
	-  Desde un equipo con Linux, [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Linux, Unix u OS X](../hdinsight-hadoop-linux-use-ssh-unix).
	-  Desde un equipo con Windows, [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](../hdinsight-hadoop-linux-use-ssh-windows).

## En este artículo

* [Opciones de configuración](#configuration)
* [Opciones para el aprovisionamiento de clústeres de HDInsight Linux](#options)
* [Pasos siguientes](#nextsteps)

## <a id="configuration"></a>Opciones de configuración

### Clústeres en Linux

HDInsight proporciona la opción de aprovisionar clústeres de Linux en Azure. Aprovisione un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información sobre HDInsight de Azure en Linux, consulte [Introducción a Hadoop en HDInsight](../hdinsight-hadoop-introduction). 

### Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar otros blobs que se asociarán también con el clúster.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](http://azure.microsoft.com/ documentation/articles/hdinsight-use-blob-storage/).

### Tienda de metadatos

La tienda de metadatos contiene información sobre las tablas, particiones, esquemas, columnas, etc. de Hive, que usa esta información para saber dónde están almacenados los datos en HDFS (o WASB para HDInsight). De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información.

Cuando se aprovisiona un clúster de HDInsight, puede especificar una base de datos SQL que contendrá la tienda de metadatos para Hive. Esto permite conservar la información de metadatos cuando se elimina un clúster, porque se almacena externamente en Base de datos SQL.

> [WACOM.NOTE] Actualmente, la opción de usar los almacenes de metadatos solo está disponible mientras se aprovisiona HDInsight para Linux con SDK de .NET. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight en Linux con SDK de .NET](#sdk).


## <a id="options"></a> Opciones para aprovisionar un clúster de HDInsight Linux

Puede aprovisionar un clúster de HDInsight Hadoop Linux desde un equipo con Linux, así como desde un equipo con Windows. La siguiente tabla proporciona información sobre las opciones de aprovisionamiento disponibles en los distintos sistemas operativos, además de vínculos a instrucciones para cada una de estas opciones.

Aprovisionamiento de clústeres de Linux desde un equipo que ejecuta este SO| Uso del Portal de administración de Azure | Uso de la CLI multiplataforma | Uso de SDK de .NET | Uso de PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Haga clic [aquí](#portal) | Haga clic [aquí](#cli)| No disponible | Pronto disponible
Windows | Haga clic [aquí](#portal) | Haga clic [aquí](#cli) | Haga clic [aquí](#sdk) | Pronto disponible

### <a id="portal"></a> Uso del Portal de administración de Azure

Los clústeres de HDInsight usan contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]. Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].


> [WACOM.NOTE] Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.** y **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.

**Para crear un clúster de HDInsight con la opción de creación personalizada**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **+ NUEVO** en la parte inferior de la página y después en **SERVICIOS DE DATOS**, **HDINSIGHT** y **CREACIÓN PERSONALIZADA**.
3. En la página **Detalles del clúster** escriba o elija los valores siguientes:

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre del clúster</td>
			<td><p>Dé un nombre al clúster. </p>
				<ul>
				<li>El nombre DNS debe empezar y terminar por caracteres alfanuméricos y puede contener guiones.</li>
				<li>El campo debe ser una cadena con una longitud entre 3 y 63 caracteres.</li>
				</ul></td></tr>
		<tr><td>Tipo de clúster</td>
			<td>Para el tipo de clúster, seleccione <strong>Hadoop</strong>.</td></tr>
		<tr><td>Sistema operativo</td>
			<td>Seleccione <b>Ubuntu 12.04 LTS Preview</b> para aprovisionar un clúster de HDInsight en Linux. Para aprovisionar un clúster de Windows, consulte <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/" target="_blank">Aprovisionamiento de clústeres de Hadoop en Windows en HDInsight</a>.</td></tr>
		<tr><td>Versión de HDInsight</td>
			<td>Seleccione la versión. Para HDInsight en Linux, el valor predeterminado es la versión 3.2 de HDInsight, que usa Hadoop 2.5.</td></tr>
		</table>

	Escriba o seleccione los valores mostrados en la tabla y después haga clic en la flecha derecha.

4. En la página **Configurar clúster** escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite de tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td><p>Seleccione la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí.</p><p>Las regiones disponibles son las siguientes: <strong>Asia oriental</strong>, <strong>Sudeste asiático</strong>, <strong>Europa del Norte</strong>, <strong>Europa occidental</strong>, <strong>Este de EE. UU.</strong>, <strong>Oeste de EE. UU.</strong>, <strong>Centro-Norte de EE. UU</strong>, <strong>Centro-Sur de EE. UU</strong><br/></p></td></tr>
	</table>



5. En la página **Configurar usuario de clúster** proporcione los siguientes valores:

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Contraseña de HTTP</td>
			<td>Especifique la contraseña para el usuario de HTTP predeterminado, <i>admin</i>.</td></tr>
		<tr><td>Nombre de usuario de SSH</td>
			<td>Especifique el nombre de usuario de SSH. Usará este nombre de usuario para iniciar una sesión SSH remota en los nodos del clúster de HDInsight.</td></tr>
		<tr><td>Tipo de autenticación de SSH</td>
			<td>Especifique si desea usar una contraseña o una clave SSH para autenticar un usuario de SSH.</td></tr>
		<tr><td>Contraseña de SSH</td>
			<td>Si eligió una contraseña como el tipo de autenticación, especifique la contraseña de SSH para autenticar un usuario de SSH. Esta contraseña se le solicitará cuando intente iniciar una sesión de SSH en el equipo remoto con Linux.</td></tr>
		<tr><td>Clave pública SSH</td>
			<td>Si eligió una clave como el tipo de autenticación, especifique la clave pública SSH que ya debe haber generado. Cuando inicie una sesión SSH con una nota en el clúster de Linux, usará la clave privada asociada con esta clave pública.<br>
			Para obtener instrucciones sobre cómo generar una clave SSH en un equipo con Linux, consulte <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">aquí</a>. Para obtener instrucciones sobre cómo generar una clave SSH en un equipo con Windows, consulte <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">aquí</a>.
		</td></tr>
		</table>

	> [WACOM.NOTE] Se recomienda usar autenticación con clave pública SSH con SSH, dado que es más segura que la autenticación con contraseña.

	Haga clic en la flecha derecha.


6. Proporcione el siguiente valor en la página **Cuenta de almacenamiento**:

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Cuenta de almacenamiento</td>
			<td>Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:
			<ul>
				<li>Usar almacenamiento existente</li>
				<li>Crear nuevo almacenamiento</li>
				<li>Utilizar almacenamiento de otra suscripción</li>
			</ul>
			</td></tr>
		<tr><td>Nombre de cuenta</td>
			<td><ul>
				<li>Si ha elegido usar el almacenamiento existente, en <strong>Nombre de cuenta</strong>, seleccione una cuenta de almacenamiento existente. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
				<li>Si ha elegido la opción <strong>Crear nuevo almacenamiento</strong> o <strong>Utilizar almacenamiento de otra suscripción</strong> , debe proporcionar el nombre de la cuenta de almacenamiento.</li>
			</ul></td></tr>
		<tr><td>Clave de cuenta</td>
			<td>Si ha elegido la opción <strong>Utilizar almacenamiento de otra suscripción</strong> , especifique la clave de cuenta de esa cuenta de almacenamiento.</td></tr>
		<tr><td>Contenedor predeterminado</td>
			<td><p>Especifica el contenedor predeterminado de la cuenta de almacenamiento que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Si ha elegido la opción <strong>Usar almacenamiento existente</strong> en el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
            <p>Si eligió crear un nuevo almacenamiento o usar un almacenamiento de otra suscripción de Azure, debe especificar el nombre predeterminado del contenedor.</p>
        </td></tr>
		<tr><td>Cuentas de almacenamiento adicionales</td>
			<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el Portal de administración, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página Cuenta de almacenamiento adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>
	</table>

	Haga clic en la flecha derecha.

7. Si ha elegido configurar almacenamiento adicional para el clúster, escriba la información de la cuenta de almacenamiento adicional en la página **Cuenta de almacenamiento**:

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.

    > [WACOM.NOTE] Una vez que haya seleccionado una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta, ni cambiarla por otra diferente.

 	Una vez que especifique la cuenta de almacenamiento adicional, haga clic en la marca de verificación para comenzar a aprovisionar el clúster. 

### <a id="cli"></a> Uso de la línea de comandos entre plataformas

> [WACOM.NOTE] Desde el 29/8/2014, no se puede usar la interfaz de línea de comandos multiplataforma para asociar un clúster con una red virtual de Azure.

Otra opción para aprovisionar un clúster de HDInsight es la interfaz de línea de comandos entre plataformas. La herramienta de línea de comandos se implementa en Node.js. y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La herramienta de línea de comandos es de código abierto.  El código fuente se administra en GitHub en <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>. Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [Uso de las herramientas de línea de comandos de Azure para Mac y Linux][azure-command-line-tools]. Para obtener una documentación de referencia completa, consulte [Herramienta de línea de comandos de Azure para Mac y Linux][azure-command-line-tool].

- [Configuración de la línea de comandos multiplataforma de Azure para Linux](#clilin)
- [Configuración de la línea de comandos multiplataforma de Azure para Windows](#cliwin)
- [Aprovisionamiento de clústeres de HDInsight con la línea de comandos multiplataforma de Azure](#cliprovision)

#### <a id="clilin"></a>Configuración de la línea de comandos multiplataforma para Linux

Realice los siguientes procedimientos para configurar su equipo con Linux para que use las herramientas de la línea de comandos de Azure.

- Instalación de la línea de comandos multiplataforma con NPM
- Conexión a su suscripción de Azure

**Para instalar la interfaz de línea de comandos con NPM**

1.	Abra una ventana del terminal en el equipo con Linux y ejecute el siguiente comando:

		sudo npm install -g azure-cli

2.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectarse a su suscripción de Azure**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [WACOM.NOTE] El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker. 


1.	Abra una ventana del terminal.
2.	Ejecute el siguiente comando para iniciar sesión en su suscripción de Azure.

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella. Si la página web no se abre, haga clic en el vínculo que aparece en la ventana del terminal para abrir la página del explorador e iniciar sesión en el portal. 

3.	Descargue el archivo de configuración de publicación en el equipo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Configuración de la línea de comandos multiplataforma para Windows

Realice los siguientes procedimientos para configurar su equipo con Windows para que use las herramientas de la línea de comandos de Azure.

- Instale la línea de comandos multiplataforma (con NPM o Windows Installer).
- Descarga e importación de la configuración de publicación de la cuenta de Azure


La interfaz de línea de comandos se puede instalar mediante *Node.js Package Manager (NPM)* o Windows Installer. Microsoft recomienda realizar la instalación usando solamente una de las dos opciones.

**Para instalar la interfaz de línea de comandos con NPM**

1.	Vaya a **www.nodejs.org**.
2.	Haga clic en **INSTALL** y siga las instrucciones usando la configuración predeterminada.
3.	Abra el **símbolo del sistema** (o *Azure Command Prompt* o  *Developer Command Prompt for VS2012*) desde la estación de trabajo.
4.	Ejecute el comando siguiente en la ventana del símbolo del sistema.

		npm install -g azure-cli

	> [WACOM.NOTE] Si aparece un error que indica que no se ha podido encontrar el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno de PATH: <i>C:\Archivos de programa (x86)\nodejs;C:\Users\[nombre_usuario]\AppData\Roaming\npm</i> o <i>C:\Archivos de programa\nodejs;C:\Users\[nombre_usuario]\AppData\Roaming\npm</i>

5.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos con Windows Installer**

1.	Navegue a **http://azure.microsoft.com/ downloads/**.
2.	Desplácese hasta la sección **Herramientas de línea de comandos** y, a continuación, haga clic en **Interfaz de la línea de comandos multiplataforma** y siga el asistente del instalador de plataforma web.

**Para descargar e importar la configuración de publicación**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [WACOM.NOTE] El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.


1.	Abra un **símbolo del sistema**.
2.	Ejecute el comando siguiente para descargar el archivo de configuración de publicación.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella.

3.	En el símbolo del sistema para guardar el archivo, haga clic en **Guardar** y proporcione una ubicación en la que se deba guardar el archivo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Aprovisionamiento de clústeres de HDInsight con la línea de comandos multiplataforma de Azure

Los procedimientos siguientes son necesarios para aprovisionar un clúster de HDInsight con la línea de comandos entre varias plataformas:

- Creación de una cuenta de almacenamiento de Azure
- Aprovisionamiento de un clúster


**Para crear una cuenta de almacenamiento de Azure**

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

- Desde la ventana del símbolo del sistema, ejecute el siguiente comando para crear una cuenta de almacenamiento de Azure:

		azure storage account create [options] <StorageAccountName>

	Cuando se le pida una ubicación, seleccione aquella en la que se puede aprovisionar el clúster de HDInsight. El almacenamiento debe encontrarse en la misma ubicación que el clúster de HDInsight. Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.** y **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.  

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure con el Portal de administración de Azure, consulte [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storageaccount].

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	-- lists storage accounts
	azure storage account list

	-- Muestra información de una cuenta de almacenamiento
	azure storage account show <NombreCuentaAlmacenamiento>

	-- Muestra las claves de una cuenta de almacenamiento
	azure storage account keys list <NombreCuentaAlmacenamiento>

Para obtener información acerca de cómo conseguir los datos con el portal de administración, consulte la sección *How to: View, copy and regenerate storage access keys* de [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storageaccount].

Un clúster de HDInsight también requiere un contenedor dentro de una cuenta de almacenamiento. Si la cuenta de almacenamiento que proporciona todavía no tiene un contenedor, el comando *azure hdinsight cluster create* le pide un nombre de contenedor y también lo crea. Sin embargo, si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

- En la ventana del símbolo del sistema, ejecute el comando siguiente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword> --OSType Linux --SshUserName <SSH username> --SshPassword <SSH user password>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para aprovisionar un clúster de HDInsight con un archivo de configuración**

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos y, a continuación, se elimina el clúster para reducir los costes. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

- En la ventana del símbolo del sistema, ejecute los comandos siguientes:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>" --OSType Linux --SshUserName "<SSH username>" --SshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Microsoft Azure**, haga clic en **Sí** y en **Guardar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para enumerar y mostrar los detalles del clúster**

- Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para eliminar un clúster**

- Use el comando siguiente para eliminar un clúster:

		azure hdinsight cluster delete <ClusterName>



### <a id="sdk"></a> Uso del SDK .NET de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET.

Los siguientes procedimientos se deben realizar para aprovisionar un clúster de HDInsight en Linux con el SDK:

- Instalación del SDK .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para crear una aplicación de consola de Visual Studio**

1. Abra Visual Studio 2013.

2. En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3. En Nuevo proyecto, escriba o seleccione los siguientes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoría</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CrearClústerHDI</td></tr>
	</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**.

6. Ejecute los siguientes comandos en la consola para instalar los paquetes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8. Agregue la siguiente instrucción en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. En la función Main(), copie y pegue el siguiente código:

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));
        

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Reemplace las variables al principio de la función main().

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.



## <a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido varias maneras de aprovisionar un clúster de HDInsight en Linux. Para obtener más información, consulte los artículos siguientes:

- [Trabajo con HDInsight en Linux](../hdinsight-hadoop-linux-information). Conozca los matices que implica trabajar con un clúster de HDInsight en Linux.
- [Administración de clústeres de HDInsight con Ambari](../hdinsight-hadoop-manage-ambari) Aprenda a supervisar y administrar el clúster de Hadoop en HDInsight basado en Linux con Ambari Web o la API de REST de Ambari. 
- [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce]. Aprenda sobre las distintas formas de ejecutar trabajos de MapReduce en un clúster.
- [Use Hive con HDInsight][hdinsight-use-hive]. Aprenda sobre las distintas formas de ejecutar una consulta de Hive en un clúster.
- [Uso de Pig con HDInsight][hdinsight-use-pig]. Aprenda sobre las distintas formas de ejecutar un trabajo de Pig en un clúster.
- [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage). Sepa cómo HDInsight usa almacenamiento de blobs de Azure para almacenar datos para clústeres de HDInsight.
- [Carga de datos en HDInsight][hdinsight-upload-data]. Sepa cómo trabajar con datos almacenados en un almacenamiento de blobs de Azure para un clúster de HDInsight.

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /es-es/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=45--> 
