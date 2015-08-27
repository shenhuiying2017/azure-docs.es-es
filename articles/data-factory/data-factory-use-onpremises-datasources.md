<properties 
	pageTitle="Habilitación de canalizaciones para trabajar con datos locales | Microsoft Azure" 
	description="Obtenga información acerca de cómo registrar un origen de datos local con una factoría de datos de Azure y copiar datos en el origen de datos o desde él." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="spelluru"/>

# Habilitación de canalizaciones para trabajar con datos locales

Para habilitar las canalizaciones en una factoría de datos de Azure para trabajar con un origen de datos local, debe agregar el origen de datos local como un servicio vinculado a la factoría de datos mediante el Portal de administración de Azure o Azure PowerShell.
 
Para poder agregar un origen de datos local como un servicio vinculado a una factoría de datos, primero debe descargar e instalar Microsoft Data Management Gateway en un equipo local y configurar el servicio vinculado para que el origen de datos local use la puerta de enlace.


## <a href="DMG"></a> Data Management Gateway

**Data Management Gateway** es un software que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Con Data Management Gateway, puede realizar lo siguiente:

- **Conectar datos locales para el acceso a datos híbrido**: puede conectar datos locales a servicios en la nube para aprovechar las ventajas que ofrecen los servicios en la nube mientras su negocio continúa funcionando con los datos locales.
- **Definir un proxy de datos seguro**: puede definir qué orígenes de datos locales se exponen a través de Data Management Gateway para que esta puerta de enlace autentique la solicitud de datos de servicios en la nube y proteja los orígenes de datos locales.
- **Administrar la puerta de enlace para tener el control total**: se le proporciona supervisión y registro completos de todas las actividades dentro de Data Management Gateway con fines de administración y gobernanza.
- **Mover datos de manera eficaz**: los datos se transfieren en paralelo, con resistencia a problemas de red intermitentes mediante lógica automática de reintentos.


Data Management Gateway ofrece una gama completa de características de conexión de datos locales que incluye las siguientes:

- **No intrusivo para el firewall corporativo**: Data Management Gateway funciona después de la instalación, sin tener que abrir una conexión de servidor de seguridad ni realizar cambios intrusivos en la infraestructura de red corporativa. 
- **Cifrar las credenciales con el certificado**: las credenciales usadas para conectarse a orígenes de datos se cifran con un certificado que pertenece totalmente a un usuario. Sin el certificado, nadie puede descifrar las credenciales en texto sin formato, incluido Microsoft.

### Consideraciones para utilizar Data Management Gateway
1.	Una sola instancia de Data Management Gateway se puede usar para varios orígenes de datos locales, pero tenga en cuenta que **una puerta de enlace está asociada a una factoría de datos de Azure** y no puede compartirse con otra factoría de datos.
2.	**Solo puede haber una instancia de Data Management Gateway** instalada en el equipo. Supongamos que tiene dos factorías de datos que necesitan tener acceso a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales donde cada puerta de enlace esté asociada a una factoría de datos diferente.
3.	La **puerta de enlace no necesita estar en el mismo equipo que el origen de datos** pero, si está cerca del origen de datos, reduce el tiempo de conexión de la puerta de enlace al origen de datos. Le recomendamos que instale la puerta de enlace en un equipo diferente del que hospeda el origen de datos local para que la puerta de enlace no compita por los recursos con el origen de datos.
4.	Puede tener **varias puertas de enlace en diferentes equipos conectados al mismo origen de datos local**. Por ejemplo, puede que tenga dos puertas de enlace que atienden a dos factorías de datos pero el mismo origen de datos local está registrado con las dos factorías de datos. 
5.	Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI** , instale una **puerta de enlace independiente para Factoría de datos de Azure** en otro equipo.

### Consideraciones de puertos y seguridad 
- El programa de instalación de Data Management Gateway abre los puertos **8050** y **8051** en el equipo de la puerta de enlace. Estos puertos los usa el **Administrador de credenciales** (aplicación ClickOnce), que le permite establecer credenciales para un servicio vinculado local y probar la conexión al origen de datos. Estos puertos no se pueden alcanzar desde Internet y no es necesario que estén abiertos en el firewall corporativo.
- Al copiar datos desde y hacia una base de datos de SQL Server local hacia o desde una base de datos SQL a Azure, asegúrese de lo siguiente:
 
	- El firewall del equipo de la puerta de enlace permite la salida de la comunicación TCP de en el puerto **TCP** **1433**.
	- Establezca la [configuración de firewall de SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) para agregar la **dirección IP del equipo de la puerta de enlace** a las **direcciones IP permitidas**.

- Si copia datos desde y hacia SQL Server local a cualquier destino y los equipos de la puerta de enlace y de SQL Server son diferentes, haga lo siguiente: [configure Firewall de Windows](https://msdn.microsoft.com/library/ms175043.aspx) en el equipo de SQL Server para que la puerta de enlace pueda tener acceso a la base de datos a través de puertos en los que escucha la instancia de SQL Server. En la instancia predeterminada es el puerto 1433.

- Debe iniciar la aplicación **Administrador de credenciales** en un equipo que pueda conectarse a Data Management Gateway para poder establecer credenciales para el origen de datos y probar la conexión al origen de datos.

### Instalación de la puerta de enlace: requisitos previos 

1.	Las versiones de **sistema operativo** compatibles son Windows 7, Windows 8/8.1, Windows Server 2008 R2 y Windows Server 2012.
2.	La **configuración** recomendada del equipo de la puerta de enlace es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y disco de 80 GB.
3.	Si el equipo host está en hibernación, la puerta de enlace no podrá responder a las solicitudes de datos. Por tanto, configure un **plan de energía** adecuado en el equipo antes de instalar la puerta de enlace. La instalación de la puerta de enlace emite un mensaje si el equipo está configurado para la hibernación.  

Data Management Gateway serializa y deserializa los datos de origen y receptor en el equipo donde se hospeda. También realiza conversiones de tipos en los datos mientras se está copiando. Durante la operación de copia, la puerta de enlace lee los datos de origen en un búfer de memoria y al mismo tiempo, otro subproceso de escritura escribe el búfer en el receptor. Dado que pueden ejecutarse varios trabajos de actividad de copia al mismo tiempo en el equipo host de puerta de enlace durante las horas punta, el consumo de memoria y los recursos de la CPU son mucho mayores que en tiempos de inactividad. Por lo tanto, el equipo host que ejecuta una Data Management Gateway puede necesitar más recursos de los descritos en la configuración mínima de equipos sugerida anteriormente, o menos recursos durante tiempos de inactividad.


## Tutorial

En este tutorial se crea una factoría de datos con una canalización que mueve los datos de una base de datos de SQL Server local a un blob de Azure.

## Paso 1: Creación de una factoría de datos de Azure
En este paso, use el Portal de administración de Azure para crear una instancia de Factoría de datos de Azure denominada **ADFTutorialOnPremDF**. También puede crear una factoría de datos mediante cmdlets de la Factoría de datos de Azure.

1.	Tras iniciar sesión en el [Portal de vista previa de Azure][azure-preview-portal], haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **Análisis de datos** en la hoja **Crear** y haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

	![New->DataFactory][image-data-factory-new-datafactory-menu]
  
6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialOnPremDF** en el **nombre**.
	2. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y seleccione **ADFTutorialResourceGroup** (si ha seguido el tutorial [Introducción a la Factoría de datos de Azureadfgetstarted][adfgetstarted]. Puede seleccionar un grupo de recursos existente o crear uno nuevo. Para crear un nuevo grupo de recursos:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba un **nombre** para el grupo de recursos y haga clic en **Aceptar**.

7. Observe que está seleccionada la opción **Agregar al Panel de inicio** en la hoja **Nueva factoría de datos**.

	![Agregar al Panel de inicio][image-data-factory-add-to-startboard]

8. En la hoja **Nueva factoría de datos**, haga clic en **Crear**.

	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "ADFTutorialOnPremDF" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearla de nuevo. Use este nombre en lugar de ADFTutorialFactory al realizar los restantes pasos de este tutorial.

9. Busque las notificaciones del proceso de creación en el centro de **NOTIFICACIONES** situado a la izquierda. Haga clic en **X** para cerrar la hoja **NOTIFICACIONES** si está abierta.

	![Centro de NOTIFICACIONES][image-data-factory-notifications-hub]

11. Una vez completada la creación, verá la hoja **Factoría de datos** como se muestra a continuación:

	![Página principal de Factoría de datos][image-data-factory-datafactory-homepage]

## Paso 2: Creación de una puerta de enlace de administración de datos
5.	En la hoja **Factoría de datos** de **ADFTutorialOnPremDF**, haga clic en **Servicios vinculados**. 

	![Página principal de Factoría de datos][image-data-factory-home-age]

2.	En la hoja **Servicios vinculados**, haga clic en **+ Puerta de enlace de datos**.

	![Servicios vinculados: botón Agregar una puerta de enlace][image-data-factory-linkedservices-add-gateway-button]

2. En la hoja **Crear**, escriba **adftutorialgateway** para el **nombre** y haga clic en **Aceptar**.

	![Hoja Crear puerta de enlace][image-data-factory-create-gateway-blade]

3. En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto descarga el paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.

	> [AZURE.NOTE] Utilice Internet Explorer o un explorador web compatible con Microsoft ClickOnce.

	![Puerta de enlace: hoja Configurar][image-data-factory-gateway-configure-blade]

	Esta es la forma más sencilla (un clic) para descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver que la aplicación **Administrador de configuración de Microsoft Data Management Gateway** está instalada en el equipo. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	También puede descargar e instalar la puerta de enlace manualmente con los vínculos de esta hoja y registrarla usando la clave que se muestra en el cuadro de texto **REGISTRAR CON LA CLAVE**.
	
	Consulte la sección [Data Management Gateway](#DMG) para obtener información detallada de la puerta de enlace, incluidos procedimientos recomendados y algunas consideraciones importantes.

	>[AZURE.NOTE] Debe ser administrador del equipo local para instalar y configurar correctamente Data Management Gateway. Puede agregar usuarios adicionales al grupo local de Windows Usuarios de usuarios de Data Management Gateway. Los miembros de este grupo podrán usar la herramienta Administrador de configuración de Data Management Gateway para configurar la puerta de enlace.

4. Haga clic en el centro de **NOTIFICACIONES** en la parte izquierda. Espere hasta ver el mensaje **La configuración rápida de ''adftutorialgateway'' se realizó correctamente** en la hoja **Notificaciones**.

	![La configuración rápida se realizó correctamente][express-setup-succeeded]
5. Haga clic en **Aceptar** en la hoja **Crear** y, a continuación, en la hoja **Nueva puerta de enlace de datos**.
6. Cierre la hoja **Servicios vinculados** (presionando el botón **X** situado en la esquina superior derecha) y vuelva a abrir la hoja **Servicios vinculados** para ver el estado más reciente de la puerta de enlace. 
7. Confirme que el **estado** de la puerta de enlace es **En línea**. 

	![Estado de la puerta de enlace][gateway-status]
5. Inicie la aplicación **Administración de configuración de Microsoft Data Management Gateway** en el equipo.

	![Administrador de configuración de puertas de enlace][image-data-factory-gateway-configuration-manager]

6. Espere hasta que los valores se hayan establecido como se indica a continuación:
	1. Si el **estado** del servicio no es **Iniciado**, haga clic en **Iniciar servicio** para iniciarlo y espere un minuto para que se actualicen los demás campos.
	2. **Nombre de la puerta de enlace** está establecido en **adftutorialgateway**.
	3. **Nombre de instancia** está establecido en **adftutorialgateway**.
	4. **Estado de la clave de la puerta de enlace** está establecido en **Registrada**.
	5. La barra de estado de la parte inferior muestra **Conectado al servicio en la nube Data Management Gateway** junto con una **marca de verificación verde**.
	
7. En la hoja **Servicios vinculados**, confirme que el **estado** de la puerta de enlace es **Bueno**.
8. Cierre todas las hojas hasta que llegue a la página principal de **Factoría de datos**. 

## Paso 2: Creación de servicios vinculados 
En este paso, creará dos servicios vinculados: **StorageLinkedService** y **SqlServerLinkedService**. El servicio **SqlServerLinkedService** vincula una base de datos de SQL Server local y el servicio vinculado **StorageLinkedService** vincula un almacén de blobs de Azure a **ADFTutorialDataFactory**. Más adelante en este tutorial creará una canalización que copia datos de la base de datos de SQL Server local al almacén de blobs de Azure.

### Adición de un servicio vinculado a una base de datos de SQL Server local
1.	En la hoja **FACTORÍA DE DATOS**, haga clic en el icono **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar][image-author-deploy-tile]

	Consulte el tema [Editor de la Factoría de datos][data-factory-editor] para obtener información general del Editor de la Factoría de datos.

2.	En el **Editor de la factoría de datos**, haga clic en el botón **Nuevo almacén de datos** de la barra de herramientas y seleccione **Base de datos de SQL local** en el menú desplegable.

	![Botón Nuevo almacén de datos del Editor][image-editor-newdatastore-onpremsql-button]
    
3.	Debería ver la plantilla JSON para crear un servicio vinculado de SQL Server local en el panel derecho. 
	![Servicio vinculado de SQL local: configuración][image-editor-newdatastore-onpremsql-settings]

4.	En el panel JSON, haga lo siguiente:
	1.	En la propiedad **gatewayName**, escriba **adftutorialgateway** para reemplazar todo el texto entre comillas dobles.  
	2.	Si está usando **Autenticación de SQL**: 
		1.	En la propiedad **connectionString**, reemplace **<servername>**, **<databasename>**, **<username>** y **<password>** por los nombres de su servidor SQL de Azure local, base de datos, cuenta de usuario y contraseña. Para especificar un nombre de instancia, use el carácter de escape:. Por ejemplo: **server\\instancename**. 	
		2.	Quite las dos últimas propiedades (**username** y **password**) del archivo JSON y quite el carácter de **coma (,)** al final de la última línea del script JSON restante.
		
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
				      "gatewayName": "adftutorialgateway"
				    }
				  }
				}
	3.	Si está usando **Autenticación Windows**:
		1. En la propiedad **connectionString**, reemplace **<servername>** y **<databasename>** por los nombres de su servidor SQL de Azure local y base de datos. Establezca **Seguridad integrada** en **True**. Quite **ID** y **Contraseña** de la cadena de conexión.
			
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
				      "gatewayName": "adftutorialgateway",
				      "username": "<Specify user name if you are using Windows Authentication>",
				      "password": "<Specify password for the user account>"
				    }
				  }
				}		
		
6. Haga clic en **Implementar** en la barra de herramientas para implementar SqlServerLinkedService. Confirme que aparece el mensaje **SERVICIO VINCULADO CREADO CORRECTAMENTE** en la barra de título. También debe ver **SqlServerLinkedService** en la vista de árbol de la izquierda.
		   
	![Implementación de SqlServerLinkedService correcta][image-editor-sql-linked-service-successful]
	
  
También puede crear un servicio vinculado de SQL Server haciendo clic en el botón **Nuevo almacén de datos** de la barra de herramientas en la hoja **Servicios vinculados**. Si elige esa ruta, establezca las credenciales del origen de datos con la aplicación ClickOnce el Administrador de credenciales se ejecuta en el equipo que tiene acceso al portal. Si tiene acceso al portal desde un equipo diferente del equipo de la puerta de enlace, debe asegurarse de que la aplicación del Administrador de credenciales puede conectarse al equipo de la puerta de enlace. Si la aplicación no puede ponerse en contacto con el equipo de la puerta de enlace, no podrá establecer las credenciales del origen de datos ni probar la conexión al origen de datos.

#### Adición de un servicio vinculado para una cuenta de almacenamiento de Azure
 
1. En el **Editor**, haga clic en el botón **Nuevo almacén de datos** en la barra de herramientas y seleccione **Almacenamiento de Azure** en el menú desplegable. Debería ver la plantilla JSON para crear un servicio vinculado de almacenamiento de Azure en el panel derecho. 

	![Botón Nuevo almacén de datos del Editor][image-editor-newdatastore-button]
    
6. Reemplace **<accountname>** y **<accountkey>** por los valores de clave de cuenta y nombre de cuenta para la cuenta de almacenamiento de Azure.

	![JSON de almacenamiento de blobs del Editor][image-editor-blob-storage-json]
	
	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Haga clic en **Implementar** en la barra de herramientas para implementar StorageLinkedService. Confirme que aparece el mensaje **SERVICIO VINCULADO CREADO CORRECTAMENTE** en la barra de título.

	![Implementación de almacenamiento de blobs del Editor][image-editor-blob-storage-deploy]

 
## Paso 3: Creación de conjuntos de datos de entrada y salida
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => almacenamiento de blobs de Azure). Antes de crear conjuntos de datos o tablas (conjuntos de datos rectangulares), debe hacer lo siguiente (después de la lista, se detallan los pasos):

- Cree una tabla con el nombre **emp** en la base de datos de SQL Server que agregó como servicio vinculado a la factoría de datos e inserte un par de entradas de ejemplo en la tabla.
- - Si no ha realizado el tutorial del artículo [Introducción a la Factoría de datos de Azurea][adfgetstarted], cree un contenedor de blobs denominado **adftutorial** en la cuenta de almacenamiento de blobs de Azure que agregó como servicio vinculado a la factoría de datos.

### Preparación de la instancia local de SQL Server para el tutorial

1. En la base de datos que especificó para el servicio vinculado de SQL Server local (**SqlServerLinkedService**), use el siguiente script de SQL para crear la tabla **emp** en la base de datos.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Inserte algún ejemplo en la tabla:


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Creación de la tabla de entrada

1.	En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **SQL local**. 
2.	Reemplace el script JSON del panel derecho por el texto siguiente:    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Tenga en cuenta lo siguiente:
	
	- **type** está establecido en **SqlServerTable**.
	- **tableName** está establecido en **emp**.
	- **linkedServiceName** está establecido en **SqlServerLinkedService** (creó este servicio vinculado en el paso 2).
	- Para una tabla de entrada no generada por otra canalización en la Factoría de datos de Azure, debe especificar la propiedad **external** en **true**. De manera opcional, puede especificar las directivas en la sección **externalData**.   

	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON][json-script-reference].

2. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA TABLA SE IMPLEMENTÓ CORRECTAMENTE**.


### Creación de la tabla de salida

1.	En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.
2.	Reemplace el script JSON del panel derecho por el texto siguiente: 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Tenga en cuenta lo siguiente:
	
	- **type** está establecido en **AzureBlob**.
	- **linkedServiceName** está establecido en **StorageLinkedService** (creó este servicio vinculado en el paso 2).
	- **folderPath** está establecido en **adftutorial/outfromonpremdf**, donde outfromonpremdf es la carpeta del contenedor adftutorial. Solo tiene que crear el contenedor **adftutorial**.
	- El elemento **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval**, en **1**). El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure. 

	Si no especifica **fileName** para una **tabla de entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Consulte los archivos de muestra del [tutorial][adf-tutorial] para ver algunos ejemplos.
 
	Si no especifica un valor **fileName** para una tabla de salida, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad partitionedBy. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON][json-script-reference].

2.	Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA TABLA SE IMPLEMENTÓ CORRECTAMENTE**.
  

## Paso 4: Creación y ejecución de una canalización
En este paso, va a crear una **canalización** con una **actividad de copia** que usa **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como salida.

1.	Haga clic en **Nueva canalización** en la barra de comandos. Si no ve el botón, haga clic en **... (puntos suspensivos)** para expandir la barra de comandos.
2.	Reemplace el script JSON del panel derecho por el texto siguiente:   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}
	Tenga en cuenta lo siguiente:
 
	- En la sección de actividades, solo hay una actividad cuyo **type** está establecido en **Copy**.
	- La **entrada** de la actividad está establecida en **EmpOnPremSQLTable** y la **salida** de la actividad está establecida en **OutputBlobTable**.
	- En la sección **transformation** , **SqlSource** está especificado como el **tipo de origen** y **BlobSink ** está especificado como el **tipo de receptor**.
	- La consulta SQL **select * from emp** está especificada para la propiedad **sqlReaderQuery** de **SqlSource**.

	Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://es.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **end** es opcional, pero se utilizará en este tutorial.
	
	Si no especifica el valor para la propiedad **end**, se calcula como "**inicio + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de la propiedad **end**.
	
	Está definiendo la duración del procesamiento de los segmentos de datos según las propiedades de **disponibilidad** que se definieron para cada tabla de la Factoría de datos de Azure.
	
	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
2. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA CANALIZACIÓN SE IMPLEMENTÓ CORRECTAMENTE**.
5. Ahora, cierre la hoja **Editor** haciendo clic en **X**. Vuelva a hacer clic en **X** para cerrar la hoja ADFTutorialDataFactory con la barra de herramientas y la vista de árbol. Si ve el mensaje que indica que **se descartarán las modificaciones no guardadas**, haga clic en **Aceptar**.
6. Debe volver a la hoja **FACTORÍA DE DATOS** para **ADFTutorialDataFactory**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

### Visualización de la factoría de datos en una vista de diagrama 
1. En el **Portal de vista previa de Azure**, haga clic en el icono **Diagrama** en la página principal de Factoría de datos **ADFTutorialOnPremDF**.

	![Vínculo de diagrama][image-data-factory-diagram-link]

2. Debería ver un diagrama similar al siguiente:

	![Vista de diagrama][image-data-factory-diagram-view]

	Puede acercar, alejar, acercar al 100%, ampliar para ajustar, colocar automáticamente canalizaciones y tablas, y mostrar información de linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados). Puede hacer doble clic en un objeto (tabla o canalización de entrada o salida) para ver sus propiedades.

## Paso 5: Supervisión de las canalizaciones y los conjuntos de datos
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información sobre el uso de cmdlets para la supervisión, vea [Supervisión y administración de la Factoría de datos mediante PowerShell][monitor-manage-powershell].

1. Vaya al **Portal de vista previa de Azure** (si lo ha cerrado).
2. Si la hoja de **ADFTutorialOnPremDF** no está abierta, ábrala haciendo clic en **ADFTutorialOnPremDF** en el **Panel de inicio**.
3. Deben mostrarse el **recuento** y los **nombres** de las tablas y la canalización que creó en esta hoja.

	![Página principal de Factoría de datos][image-data-factory-homepage-2]
4. Ahora haga clic en el icono **Conjuntos de datos**.
5. En la hoja **Conjuntos de datos**, haga clic en **EmpOnPremSQLTable**.

	![Segmentos EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Tenga en cuenta que ya se han producido los segmentos de datos hasta el momento actual y están **listos**. Esto ocurre porque ha insertado los datos en la base de datos de SQL Server y están ahí todo el tiempo. Confirme que no aparecen segmentos en la sección de **segmentos problemáticos** de la parte inferior.


	Las listas **Segmentos actualizados recientemente** y **Segmentos erróneos recientes** se ordenan por la **HORA DE LA ÚLTIMA ACTUALIZACIÓN**. En las situaciones siguientes, se cambia la hora de actualización de un segmento.
    

	-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
	-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos. Haga clic en **Filtro** en la barra de herramientas para filtrar los segmentos.
	
	Para ver los intervalos de datos ordenados por las horas de inicio y finalización, haga clic en el icono **Segmentos de datos (por hora del segmento)**.

7. A continuación, en la hoja **Conjuntos de datos**, haga clic en **OutputBlobTable**.

	![Segmentos OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Confirme que se han producido los segmentos hasta el momento actual y que están **listos**. Espere hasta que los estados de los segmentos hasta el momento actual estén establecidos en **Listo**.
9. Haga clic en cualquier segmento de datos de la lista. Debe aparecer la hoja **SEGMENTO DE DATOS**.

	![Hoja Segmento de datos][image-data-factory-dataslice-blade]

	Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.

10. Haga clic en la **ejecución de la actividad** de la lista en la parte inferior para ver los **detalles de la ejecución de la actividad**.

	![Hoja Detalles de ejecución de actividad][image-data-factory-activity-run-details]

11. Haga clic en **X** para cerrar todas las hojas hasta volver a la hoja principal de **ADFTutorialOnPremDF**.
14. (Opcional) Haga clic en **Canalizaciones**, elija **ADFTutorialOnPremDF** y obtenga detalles de las tablas de entrada (**Consumido**) o las tablas de salida (**Producido**).
15. Use herramientas como el **Explorador de almacenamiento de Azure** para comprobar la salida.

	![Explorador de almacenamiento de Azure][image-data-factory-stroage-explorer]


## Creación y registro de una puerta de enlace con Azure PowerShell 
En esta sección se explica cómo crear y registrar una puerta de enlace usando cmdlets de Azure PowerShell.

1. Inicie **Azure PowerShell** en modo de administrador. 
2. Los cmdlets de Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**. Ejecute el siguiente comando para cambiar al modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Use el cmdlet **New-AzureDataFactoryGateway** para crear una puerta de enlace lógica como se indica a continuación:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Ejemplo de comando y salida**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Use el cmdlet **New-AzureDataFactoryGatewayKey** para generar una clave de registro para la puerta de enlace recién creada y almacene la clave en una variable local **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Ejemplo de comando y salida:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. En Azure PowerSell, cambie a la carpeta **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** y ejecute el script **RegisterGateway.ps1** asociado con la variable local **$Key** como se muestra en el siguiente comando para registrar el agente cliente instalado en su equipo en la puerta de enlace lógica que creó antes.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Use el cmdlet **Get-AzureDataFactoryGateway** para obtener la lista de puertas de enlace de su factoría de datos. Cuando el **Estado** es **En línea**, significa que la puerta de enlace está lista para usarla.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Puede quitar una puerta de enlace con el cmdlet **Remove-AzureDataFactoryGateway** y actualizar la descripción de una puerta de enlace con los cmdlets **Set-AzureDataFactoryGateway**. Para ver la sintaxis y otros detalles de estos cmdlets, consulte la documentación de referencia de los cmdlets de Factoría de datos.



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!------HONumber=August15_HO7-->