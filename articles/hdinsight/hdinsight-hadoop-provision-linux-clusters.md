<properties
   pageTitle="Aprovisionamiento de clústeres de Hadoop en Linux en HDInsight | Microsoft Azure"
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
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#Aprovisionamiento de clústeres de Hadoop Linux en HDInsight usando opciones personalizadas (vista previa)

En este artículo aprenderá las diferentes maneras de aprovisionar un clúster de Hadoop Linux de forma personalizada en HDInsight de Azure, mediante el uso del Portal de Azure, Azure PowerShell, CLI de Azure o el SDK de .NET de HDInsight.

## ¿Qué es un clúster de HDInsight?

¿Se ha preguntado alguna vez por qué mencionamos los clústeres cada vez que hablamos de Hadoop o Big Data? El motivo es porque Hadoop permite el procesamiento distribuido de datos grandes y el despliegue a través de diferentes nodos de un clúster. El clúster tiene una arquitectura de maestro/trabajo con un nodo maestro (también llamado nodo principal o nodo de nombre) y cualquier número de nodos de trabajo (también llamado nodo de datos). Para obtener más información, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Clúster de HDInsight][img-hdi-cluster]


Un clúster de HDInsight abstrae los detalles de implementación de Hadoop de manera que no tiene que preocuparse sobre cómo comunicarse con diferentes nodos de un clúster. Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. Para obtener más información, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md). Los datos que se van a renovar se almacenan en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md).


En este artículo se proporcionan instrucciones sobre diferentes formas de aprovisionar un clúster. Si busca un enfoque rápido para aprovisionar un clúster, consulte [Introducción a HDInsight de Azure en Linux](../hdinsight-hadoop-linux-get-started.md).

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Claves de Shell seguro (SSH)**. Si desea realizar una conexión remota a un clúster de Linux mediante SSH con una clave en lugar de una contraseña, el uso de una clave es el método recomendado, debido a que es más seguro. Para obtener instrucciones sobre cómo generar claves SSH, consulte los siguientes artículos:
	-  Desde un equipo con Linux: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Desde un equipo con Windows: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opciones de configuración

### Clústeres en Linux

HDInsight proporciona la opción de aprovisionar clústeres de Linux en Azure. Aprovisione un clúster de Linux si conoce Linux o Unix, si realiza una migración desde una solución de Hadoop basada en Linux existente o si desea una integración simple con los componentes del ecosistema de Hadoop creados para Linux. Para obtener más información sobre HDInsight de Azure en Linux, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md).


### Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar otros blobs que se asociarán también con el clúster.


Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md).


### Tienda de metadatos

La tienda de metadatos contiene información acerca de las tablas, las particiones, los esquemas y las columnas de Hive, entre otros elementos. Hive usa esta información para localizar dónde se almacenan los datos en Sistema de archivos distribuido de Hadoop (HDFS) o almacenamiento de blobs de Azure para HDInsight. De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información.

Cuando se aprovisiona un clúster de HDInsight, puede especificar una base de datos SQL que contendrá la tienda de metadatos para Hive. Esto permite conservar la información de metadatos cuando se elimina un clúster, porque se almacena externamente en la base de datos SQL.


> [AZURE.NOTE]Actualmente, la opción de usar tiendas de metadatos está disponible solo mientras se aprovisione HDInsight para Linux mediante el SDK de .NET. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight en Linux con SDK de .NET](#sdk).



## <a id="options"></a> Opciones para aprovisionar un clúster de HDInsight Linux

Puede aprovisionar un clúster de HDInsight Hadoop Linux desde un equipo con Linux, así como desde un equipo con Windows. La siguiente tabla proporciona información sobre las opciones de aprovisionamiento disponibles en los distintos sistemas operativos, además de vínculos a instrucciones para cada una de estas opciones.

Aprovisionamiento de clústeres de Linux desde un equipo que ejecuta este SO| Uso del portal de Azure | Uso de la interfaz de la línea de comandos | Uso del SDK de .NET | Uso de Azure PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Haga clic [aquí](#portal) | Haga clic [aquí](#cli)| No aplicable | No aplicable
Windows | Haga clic [aquí](#portal) | Haga clic [aquí](#cli) | Haga clic [aquí](#sdk) | Haga clic [aquí](#powershell)

### <a id="portal"></a> Uso del portal de Azure

Los clústeres de HDInsight usan un contenedor de almacenamiento de blobs de Azure como el sistema de archivos predeterminado. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de poder crear un clúster de HDInsight. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento](../storage-create-storage-account.md).


> [AZURE.NOTE]Actualmente, solo las regiones del **sudeste de Asia**, **norte de Europa**, **este de EE. UU.** y **centro y sur de EE. UU.** puede hospedar clústeres de HDInsight Linux.

**Para crear un clúster de HDInsight con la opción de creación personalizada**

1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **+ NUEVO** en la parte inferior de la página y después en **SERVICIOS DE DATOS**, **HDINSIGHT** y, a continuación, en **CREACIÓN PERSONALIZADA**.
3. En la página **Detalles del clúster**, escriba o seleccione los siguientes valores:

	![Proporcionar detalles del clúster de HDInsight de Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre del clúster</td>
			<td><p>Dé un nombre al clúster. </p>
				<ul>
				<li>El nombre del Sistema de nombres de dominio (DNS) debe comenzar y terminar con un carácter alfanumérico y puede contener guiones.</li>
				<li>El campo debe ser una cadena con una longitud comprendida entre 3 y 63 caracteres.</li>
				</ul></td></tr>
		<tr><td>Tipo de clúster</td>
			<td>Seleccione <strong>Hadoop</strong>.</td></tr>
		<tr><td>Sistema operativo</td>
			<td>Seleccione <b>Ubuntu 12.04 LTS Preview</b> para aprovisionar un clúster de HDInsight en Linux. Para aprovisionar un clúster de Windows, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Aprovisionamiento de clústeres de Hadoop en Windows en HDInsight</a>.</td></tr>
		<tr><td>Versión de HDInsight</td>
			<td>Seleccione la versión. Para HDInsight en Linux, el valor predeterminado es la versión 3.2 de HDInsight, que usa Hadoop 2.6.</td></tr>
		</table>

	Escriba o seleccione los valores mostrados en la tabla y después haga clic en la flecha derecha.

4. En la página **Configurar clúster**, escriba o seleccione los valores siguientes:

	![Proporcionar detalles del clúster de HDInsight de Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite de tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td><p>Elija la misma región que la cuenta de almacenamiento que almacenó anteriormente. Los clústeres de HDInsight requieren que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí.</p></td></tr>
	<tr><td>Tamaño de nodo principal</td><td><p>Seleccione un tamaño de máquina virtual (VM) para el nodo principal.</p></td></tr>
	<tr><td>Tamaño de nodo de datos</td><td><p>Seleccione un tamaño de máquina virtual para los nodos de datos.</p></td></tr>
	</table>

	>[AZURE.NOTE]En función de la elección de las máquinas virtuales, el costo puede variar. HDInsight usa todas las máquinas virtuales de nivel estándar para los nodos del clúster. Para obtener información sobre cómo afectan los tamaños de máquinas virtuales a los precios, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Precios de HDInsight</a>.


5. En la página **Configurar usuario de clúster** proporcione los siguientes valores:

    ![Proporcionar usuario del clúster de HDInsight de Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Contraseña de HTTP</td>
			<td>Especifique la contraseña para el usuario HTTP predeterminado, <strong>admin</strong>.</td></tr>
		<tr><td>Nombre de usuario de SSH</td>
			<td>Especifique el nombre de usuario de SSH. Usará este nombre de usuario para iniciar una sesión remota de SSH en los nodos del clúster de HDInsight.</td></tr>
		<tr><td>Tipo de autenticación de SSH</td>
			<td>Especifique si desea usar una contraseña o una clave SSH para autenticar un usuario de SSH.</td></tr>
		<tr><td>Contraseña de SSH</td>
			<td>Si eligió una contraseña como el tipo de autenticación, especifique la contraseña de SSH para autenticar un usuario de SSH. Esta contraseña se le solicitará cuando intente iniciar una sesión de SSH en el equipo remoto con Linux.</td></tr>
		<tr><td>Clave pública SSH</td>
			<td>Si eligió una clave como el tipo de autenticación, especifique la clave pública SSH que ya debe haber generado. Cuando inicie una sesión de SSH con un nodo en el clúster de Linux, utilizará la clave privada asociada con esta clave pública.<br>
			Para obtener instrucciones sobre cómo generar una clave SSH en un equipo con Linux, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">aquí</a>. Para obtener instrucciones sobre cómo generar una clave SSH en un equipo con Windows, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">aquí</a>.
		</td></tr>
		<tr><td>Especificación de la tienda de metadatos de Hive/Oozie</td>
			<td>Active esta casilla para especificar una base de datos SQL que se encuentra en el mismo centro de datos que el clúster, que se usará como tienda de metadatos de Hive/Oozie. Si activa esta casilla, debe especificar detalles acerca de la base de datos SQL de Azure en las páginas siguientes del asistente. Esto resulta de gran utilidad si desea conservar los metadatos sobre trabajos de Hive/Oozie después de haber eliminado un clúster.</td></tr>
		</td></tr>
		</table>


	> [AZURE.NOTE]Se recomienda usar autenticación con clave pública SSH con SSH, dado que es más segura que la autenticación con contraseña.

	Haga clic en la flecha derecha.

6. En la página **Configurar la tienda de metadatos de Hive/Oozie** proporcione los siguientes valores:

    ![Proporcionar usuario del clúster de HDInsight de Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Especifique una base de datos SQL de Azure que se usará como tienda de metadatos para Hive/Oozie. Puede especificar la misma base de datos para las tiendas de metadatos de Hive y de Oozie. Esta base de datos SQL debe estar en el mismo centro de datos que el clúster de HDInsight. El cuadro de lista solo incluye las bases de datos SQL del mismo centro de datos que ha especificado en la página <strong>Detalles del clúster</strong>. Especifique también el nombre de usuario y la contraseña para conectarse a la base de datos SQL de Azure seleccionada.

    >[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

    Haga clic en la flecha derecha.


6. En la página **Cuenta de almacenamiento**, proporcione los siguientes valores:


    ![Proporcionar la cuenta de almacenamiento del clúster de HDInsight de Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Cuenta de almacenamiento</td>
			<td>Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:
			<ul>
				<li><strong>Usar almacenamiento existente</strong></li>
				<li><strong>Crear nuevo almacenamiento</strong></li>
				<li><strong>Usar almacenamiento de otra suscripción</strong></li>
			</ul>
			</td></tr>
		<tr><td>Nombre de cuenta</td>
			<td><ul>
				<li>Si decidió utilizar almacenamiento existente, en <strong>Nombre de cuenta</strong>, seleccione una cuenta de almacenamiento existente. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
				<li>Si eligió la opción <strong>Crear nuevo almacenamiento</strong> o  <strong>Usar almacenamiento de otra suscripción</strong>, debe proporcionar el nombre de la cuenta de almacenamiento.</li>
			</ul></td></tr>
		<tr><td>Clave de cuenta</td>
			<td>Si eligió la opción <strong>Usar almacenamiento de otra suscripción</strong>, especifique la clave de cuenta para esa cuenta de almacenamiento.</td></tr>
		<tr><td>Contenedor predeterminado</td>
			<td><p>Especifique el contenedor predeterminado en la cuenta de almacenamiento que se utiliza como sistema de archivos predeterminado para el clúster de HDInsight. Si eligió <strong>Usar almacenamiento existente</strong> para el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
	        <p>Si eligió crear un almacenamiento nuevo o usar almacenamiento de otra suscripción de Azure, debe especificar el nombre del contenedor predeterminado.</p>
	    </td></tr>
		<tr><td>Cuentas de almacenamiento adicionales</td>
			<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el portal de Azure, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página <strong>Cuenta de almacenamiento</strong> adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>
	</table>

	Haga clic en la flecha derecha.

7. Si ha elegido configurar almacenamiento adicional para el clúster, escriba la información de la cuenta de almacenamiento adicional en la página **Cuenta de almacenamiento**:

	![Proporcionar detalles de almacenamiento adicionales del clúster de HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.


    > [AZURE.NOTE]Cuando haya elegido una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta ni cambiarla a un clúster distinto.


 	Una vez que especifique la cuenta de almacenamiento adicional, haga clic en la marca de verificación para comenzar a aprovisionar el clúster.

###<a id="cli"></a> Uso de la interfaz de la línea de comandos de Azure (CLI de Azure)

Otra opción para aprovisionar un clúster de HDInsight es la CLI de Azure. La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La CLI de Azure se puede instalar desde las siguientes ubicaciones:

- **SDK de Node.js**: <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **CLI de Azure para Mac, Linux y Windows**: <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Para obtener información general acerca de cómo usar la CLI de Azure, consulte [CLI de Azure para Mac, Linux y Windows](../xplat-cli.md).

Las instrucciones que aparecen a continuación le guían en la instalación de la CLI de Azure en Linux y Windows y, a continuación, sobre cómo usar la línea de comandos para aprovisionar un clúster.

- [Configurar la CLI de Azure para Linux](#clilin)
- [Configurar la CLI de Azure para Windows](#cliwin)
- [Aprovisionamiento de clústeres de HDInsight mediante la CLI de Azure](#cliprovision)

#### <a id="clilin"></a>Configurar la CLI de Azure para Linux

Realice los siguientes procedimientos para configurar un equipo con Linux para que use la interfaz de la línea de comandos de Azure (CLI de Azure):

- Instalar la CLI de Azure mediante el uso del Administrador de paquetes de Node.js (NPM)
- Conectarse a su suscripción de Azure

**Instalar la CLI de Azure mediante NPM**

1.	Abra una ventana del terminal en el equipo con Linux y ejecute el siguiente comando:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador **-h** en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectarse a su suscripción de Azure**

Antes de usar la CLI de Azure, debe configurar la conectividad entre la estación de trabajo y Azure. La CLI de Azure usa la información de su suscripción de Azure para conectarse a su cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la CLI de Azure usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.


> [AZURE.NOTE]El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use el Cifrado de unidad BitLocker.



1.	Abra una ventana del terminal.
2.	Ejecute el siguiente comando para iniciar sesión en su suscripción de Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella. Si la página web no se abre, haga clic en el vínculo que aparece en la ventana del terminal para abrir la página del explorador e iniciar sesión en el portal.

3.	Descargue el archivo de configuración de publicación en el equipo.
4.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configuración de la línea de comandos multiplataforma para Windows

Realice los siguientes procedimientos para configurar su equipo con Windows para que use la interfaz de la línea de comandos de Azure (CLI de Azure).

- Instalación de la interfaz de la línea de comandos con NPM o Windows Installer
- Descarga e importación de la configuración de publicación de la cuenta de Azure


La CLI de Azure se puede instalar mediante NPM o Windows Installer. Microsoft recomienda realizar la instalación usando solamente una de las dos opciones.

**Para instalar la CLI de Azure con NPM**

1.	Vaya a **www.nodejs.org**.
2.	Haga clic en **INSTALL** (INSTALAR) y siga las instrucciones, usando la configuración predeterminada.
3.	Abra **Command Prompt** (Símbolo del sistema) (o **Azure Command Prompt** (Símbolo del sistema de Azure) o **Developer Command Prompt for VS2012 (Símbolo del sistema para desarrolladores de VS2012)**) desde su estación de trabajo.
4.	Ejecute el comando siguiente en la ventana del símbolo del sistema:

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Si recibe un error que indica que no se encuentra el comando NPM, compruebe que las siguientes rutas de acceso se encuentren en la variable de entorno **PATH**: <i>C:\Program Files (x86)\nodejs;C:\Users[nombre de usuario]\AppData\Roaming\npm</i> o <i>C:\Program Files\nodejs;C:\Users[nombre de usuario]\AppData\Roaming\npm</i>


5.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador **-h** en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar la CLI de Azure con Windows Installer**

1.	Vaya a **http://azure.microsoft.com/downloads/**. 2.	Desplácese hasta la sección **Herramientas de línea de comandos** y, a continuación, haga clic en **Interfaz de la línea de comandos de Azure** y siga el asistente del instalador de plataforma web.

**Para descargar e importar la configuración de publicación**

Antes de usar la CLI de Azure, debe configurar la conectividad entre la estación de trabajo y Azure. La CLI de Azure usa la información de su suscripción de Azure para conectarse a su cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la CLI de Azure usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.


> [AZURE.NOTE]El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.



1.	Abra el símbolo del sistema.
2.	Ejecute el comando siguiente para descargar el archivo de configuración de publicación:

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella.


3.	En el símbolo del sistema para guardar el archivo, haga clic en **Guardar** y proporcione una ubicación en la que se deba guardar el archivo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]


#### <a id="cliprovision"></a>Aprovisionamiento de clústeres de HDInsight mediante la CLI de Azure

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con la CLI de Azure:

- Crear una cuenta de Almacenamiento de Azure
- Aprovisionamiento de un clúster


**Para crear una cuenta de almacenamiento de Azure**

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

- Desde la ventana del símbolo del sistema, ejecute el siguiente comando para crear una cuenta de almacenamiento de Azure:

		azure storage account create [options] <StorageAccountName>


	Cuando se le pida una ubicación, seleccione aquella en la que se puede aprovisionar el clúster de HDInsight Linux. El almacenamiento debe encontrarse en la misma ubicación que el clúster de HDInsight.


Para obtener información sobre la creación de una cuenta de almacenamiento de Azure a través del portal de Azure, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account.md).

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Para obtener información sobre cómo obtener la información mediante el Portal de Azure, consulte la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account.md).

Un clúster de HDInsight también requiere un contenedor dentro de una cuenta de almacenamiento. Si la cuenta de almacenamiento que proporciona todavía no tiene un contenedor, el comando **azure hdinsight cluster create** le pide un nombre de contenedor y también lo crea. Sin embargo, si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

- En la ventana del símbolo del sistema, ejecute el comando siguiente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]Los valores que especifica para **--userName** y **--password** son para el usuario de Hadoop. Siempre debe especificar el valor --userName como "admin" para el usuario de Hadoop.

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para aprovisionar un clúster de HDInsight con un archivo de configuración**

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos y, a continuación, se elimina el clúster para reducir los costes. La CLI de Azure le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

- En la ventana del símbolo del sistema, ejecute los comandos siguientes:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de Base de datos SQL, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para enumerar y mostrar los detalles del clúster**

- Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para eliminar un clúster**

- Use el comando siguiente para eliminar un clúster:

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a> Uso de Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con Azure PowerShell. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con Azure PowerShell:

- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

Puede usar la consola de Windows PowerShell o el entorno de scripting integrado (ISE) de Windows PowerShell para ejecutar los scripts.

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento para crear un clúster de HDInsight. Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight. Actualmente, solo las regiones del **sudeste de Asia**, **norte de Europa**, **este de EE. UU.** y **centro y sur de EE. UU.** puede hospedar clústeres de HDInsight Linux.

**Para conectarse a su cuenta de Azure**

		Add-AzureAccount

Se le pedirá que escriba las credenciales de la cuenta de Azure.

**Para crear una cuenta de almacenamiento de Azure**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los siguientes comandos de Windows PowerShell para recuperar dicha información:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Para crear un contenedor de almacenamiento de blobs de Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

Los dos parámetros más importantes que debe establecer para aprovisionar clústeres de Linux son donde especifica el tipo de sistema operativo y los detalles del usuario de SSH:

- Asegúrese de especificar el parámetro **-OSType** como **Linux**.
- Para usar SSH para las sesiones remotas en los clústeres, puede especificar la contraseña del usuario de SSH o la clave pública SSH. Si se especifica tanto la contraseña del usuario de SSH como la clave pública SSH, se omitirá la clave. Si desea usar la clave SSH en las sesiones remotas, debe especificar una contraseña SSH en blanco cuando se le solicite.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster


		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]Los valores que se especifican para **$clusterCredentials** se usan para crear la cuenta de usuario de Hadoop para el clúster. Esta cuenta se usará para conectarse al clúster. Los valores que se especifican para **$sshCredentials** se usan para crear la cuenta de usuario de SSH para el clúster. Esta cuenta se usa para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos. Si usa la opción de creación rápida del Portal de Azure para aprovisionar un clúster, el nombre de usuario de Hadoop predeterminado es "admin" y el nombre de usuario de SSH predeterminado es "hdiuser".

El aprovisionamiento del clúster puede durar varios minutos en completarse.

![HDI.CLI.Provision][image-hdi-ps-provision]

**Para aprovisionar un clúster de HDInsight usando opciones de configuración personalizada**

Durante el aprovisionamiento de un clúster, puede usar las otras opciones de configuración, como por ejemplo conectarse a más de un contenedor de almacenamiento de blobs de Azure o usar una base de datos SQL de Azure para tiendas de metadatos de Hive y Oozie. Esto le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

Los dos parámetros más importantes que debe establecer para aprovisionar clústeres de Linux son donde especifica el tipo de sistema operativo y los detalles del usuario de SSH:

- Asegúrese de especificar el parámetro **-OSType** como **Linux**.
- Para usar SSH para las sesiones remotas en los clústeres, puede especificar la contraseña del usuario de SSH o la clave pública SSH. Si se especifica tanto la contraseña del usuario de SSH como la clave pública SSH, se omitirá la clave. Si desea usar la clave SSH en las sesiones remotas, debe especificar una contraseña SSH en blanco cuando se le solicite.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster

		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore

		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

El aprovisionamiento del clúster puede durar varios minutos en completarse.

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a> Uso del SDK de .NET de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework.

Los siguientes procedimientos se deben realizar para aprovisionar un clúster de HDInsight en Linux con el SDK:

- Instalación del SDK de .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK de .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para crear una aplicación de consola de Visual Studio**

1. Abra Visual Studio 2013.

2. En el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

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

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en la **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar los paquetes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8. Agregue la siguiente instrucción en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. En la función **Main()**, copie y pegue el código siguiente:

	> [AZURE.NOTE]Asegúrese de que la ubicación que especifique sea una de las siguientes regiones que pueden hospedar clústeres de HDInsight Linux: **sudeste de Asia**, **norte de Europa**, **este de EE. UU.** y **centro y sur de EE. UU**. La cuenta de almacenamiento que proporcione también debe encontrarse en la misma región.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;


		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
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

10. Reemplace las variables al principio de la función **Main()**.

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.



##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido varias maneras de aprovisionar un clúster de HDInsight en Linux. Para obtener más información, consulte los artículos siguientes:

- [Trabajo con HDInsight en Linux](hdinsight-hadoop-linux-information.md): conozca los matices que implica trabajar con un clúster de HDInsight en Linux.
- [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a supervisar y administrar su clúster de Hadoop en HDInsight basado en Linux con Ambari Web o la API de REST de Ambari.
- [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md): aprenda sobre las distintas formas de ejecutar trabajos de MapReduce en un clúster.
- [Uso de Hive con HDInsight](hdinsight-use-hive.md): obtenga información acerca de las distintas maneras de ejecutar una consulta de Hive en un clúster.
- [Uso de Pig con HDInsight](hdinsight-use-pig.md): obtenga información acerca de las distintas maneras de ejecutar un trabajo de Pig en un clúster.
- [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md): sepa cómo HDInsight usa almacenamiento de blobs de Azure para almacenar datos para clústeres de HDInsight.
- [Carga de datos a HDInsight](hdinsight-upload-data.md): sepa cómo trabajar con datos almacenados en un almacenamiento de blobs de Azure para un clúster de HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Uso de Sqoop con HDInsight"
 

<!----HONumber=July15_HO4-->