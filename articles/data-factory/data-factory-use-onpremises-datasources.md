<properties 
	pageTitle="Habilitación de canalizaciones para trabajar con datos locales | Factoría de datos de Azure" 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Habilitación de canalizaciones para trabajar con datos locales

Para habilitar las canalizaciones en una factoría de datos de Azure para trabajar con un origen de datos local, debe agregar el origen de datos local como un servicio vinculado a la factoría de datos mediante el Portal de administración de Azure o Azure PowerShell.
 
Para poder agregar un origen de datos local como un servicio vinculado a una factoría de datos, primero debe descargar e instalar Microsoft Data Management Gateway en un equipo local y configurar el servicio vinculado para que el origen de datos local use la puerta de enlace.


## <a href="DMG"></a> Data Management Gateway

**Data Management Gateway** es un software que se conecta a orígenes de datos locales para servicios en la nube de manera segura y administrada. Con Data Management Gateway, puede realizar lo siguiente:

- **Conectar a datos locales para el acceso de datos híbrida** : datos locales puede conectarse a servicios de nube para beneficiarse de los servicios en la nube mientras mantiene el negocio en funcionamiento con datos locales.
- **Definir un proxy de proteger los datos** : puede definir qué orígenes de datos locales se exponen a través de puerta de enlace de datos de administración para que dicha puerta de enlace autentica la solicitud de datos de servicios de nube y protege los orígenes de datos locales.
- **Administrar su puerta de enlace para el gobierno completa** – se proporcionan con la supervisión y el registro de todas las actividades dentro de la puerta de enlace de administración de datos para la administración y control total.
- **Mover datos de forma eficaz** : los datos se transfieren en paralelo, problemas de red resistente a intermitentes con auto lógica de reintento.


Data Management Gateway ofrece una gama completa de características de conexión de datos locales que incluye las siguientes:

- **No intrusivo para el firewall corporativo** – Data Management Gateway funciona después de la instalación, sin tener que abrir un servidor de seguridad de conexión o requerir intrusivo cambia a la infraestructura de red corporativa. 
- **Cifrar las credenciales con el certificado** – credenciales usadas para conectarse a orígenes de datos se cifran con un certificado totalmente pertenecen a un usuario. Sin el certificado, nadie puede descifrar las credenciales en texto sin formato, incluido Microsoft.

### Consideraciones para utilizar Data Management Gateway
1.	Una única instancia de puerta de enlace de datos de administración se pueden usar para varios orígenes de datos locales, pero tenga en cuenta que un **puerta de enlace está asociado a un generador de datos de Azure** y no puede compartirse con otro generador de datos.
2.	Puede tener **solo una instancia de puerta de enlace de datos de administración** instalado en su equipo. Supongamos que tiene dos factorías de datos que necesitan tener acceso a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales donde cada puerta de enlace esté asociada a una factoría de datos diferente.
3.	El **puerta de enlace no deben estar en el mismo equipo que el origen de datos**, pero mantiene más cercano al origen de datos reduce el tiempo de la puerta de enlace para conectarse al origen de datos. Se recomienda que instale la puerta de enlace en un equipo que es diferente de la que se hospeda el origen de datos local para que la puerta de enlace no compiten por los recursos con origen de datos.
4.	Puede tener **varias puertas de enlace en diferentes equipos que se conectan al mismo origen de datos local**. Por ejemplo, es posible que tenga dos puertas de enlace que actúa de dos generadores de datos, pero el mismo origen de datos local se registra con tanto los generadores de datos. 
5.	Si ya tiene una puerta de enlace instalado en la porción del equipo un **Power BI** escenario, instale un **puerta de enlace independiente para el generador de datos de Azure** en otro equipo.

### Consideraciones de seguridad y puertos 
- Se abre el programa de instalación de Data Management Gateway **8050** y **8051** puertos en el equipo de puerta de enlace. Estos puertos son utilizados por la **Administrador de credenciales** (aplicación ClickOnce), que le permite establecer credenciales para un servicio local vinculado y probar la conexión al origen de datos. Estos puertos no se puede alcanzar desde internet y no necesita tener estos abierto en el firewall corporativo.
- Cuando se copian datos desde y hacia una base de datos de SQL Server local desde una base de datos de SQL Azure, asegúrese de lo siguiente:
 
	- Firewall en el equipo de puerta de enlace permite la comunicación TCP de salida en **TCP** puerto **1433**
	- Configurar [configuración de firewall de SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) para agregar la **dirección IP del equipo de puerta de enlace** a la **direcciones IP permitidas**.

- Al copiar datos en SQL Server local a cualquier destino y la puerta de enlace y los equipos de SQL Server son diferentes, realice lo siguiente: [configurar Firewall de Windows](https://msdn.microsoft.com/library/ms175043.aspx) en SQL Server del equipo para que la puerta de enlace puede tener acceso a la base de datos a través de puertos que escucha la instancia de SQL Server. Para la instancia predeterminada, es el puerto 1433.

- Debe iniciar la **Administrador de credenciales** a la aplicación en un equipo que pueda conectarse a la puerta de enlace de datos de administración para poder establecer credenciales para el origen de datos y probar la conexión al origen de datos.

### Instalación de puerta de enlace: requisitos previos 

1.	Los valores de **sistema operativo** versiones son Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012.
2.	La opción **configuración** la puerta de enlace máquina es al menos 2 GHz, 4 núcleos, 8 GB de RAM y 80 GB de disco.
3.	Si el equipo host hiberna, la puerta de enlace no podrá responder a las solicitudes de datos. Por lo tanto, configurar adecuada **plan de energía** en el equipo antes de instalar la puerta de enlace. La instalación de puerta de enlace solicita un mensaje si el equipo está configurado para la hibernación.  


 

## Tutorial

En este tutorial se crea una factoría de datos con una canalización que mueve los datos de una base de datos de SQL Server local a un blob de Azure.

## Paso 1: Crear un generador de datos de Azure
En este paso, se utilizará el Portal de administración de Azure para crear una instancia de generador de datos de Azure denominada **ADFTutorialOnPremDF**. También puede crear una factoría de datos mediante cmdlets de la Factoría de datos de Azure.

1.	Tras iniciar sesión en la [Portal de vista previa de Azure][azure-preview-portal], haga clic en **nuevo** en la esquina inferior izquierda, seleccione **análisis de datos** en el **crear** blade y haga clic en **factoría de datos** en el **análisis de datos** blade.

	![Nuevo -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. En el **Nuevo generador de datos** módulo:
	1. Escriba **ADFTutorialOnPremDF** para el **nombre**.
	2. Haga clic en **nombre del grupo de recursos** y seleccione **ADFTutorialResourceGroup** (si hubiera hecho el tutorial de [empezar a trabajar con el generador de datos de Azure][adfgetstarted]. Puede seleccionar un grupo de recursos existente o crear uno nuevo. Para crear un nuevo grupo de recursos:
		1. Haga clic en **crear un nuevo grupo de recursos**.
		2. En el **Crear cuadro de grupo de recursos**, especifique una **nombre** para el grupo de recursos y haga clic en **Aceptar**.

7. Tenga en cuenta que **Agregar al panel de inicio** se comprueba en el **Nuevo generador de datos** blade.

	![Agregar al Panel de inicio][image-data-factory-add-to-startboard]

8. En el **Nuevo generador de datos** blade, haga clic en **crear**.

	> [AZURE.NOTE]**Nombre del generador de datos "ADFTutorialOnPremDF" no está disponible**  
9. Busque las notificaciones desde el proceso de creación de la **notificaciones** concentrador a la izquierda. Haga clic en **X** para cerrar la **notificaciones** blade si está abierto.

	![Centro de notificaciones][image-data-factory-notifications-hub]

11. Una vez completada la creación, verá el **factoría de datos** blade tal como se muestra a continuación:

	![Página de inicio del generador de datos][image-data-factory-datafactory-homepage]

## Paso 2: Crear una puerta de enlace de administración de datos
5.	En el **factoría de datos** módulo para **ADFTutorialOnPremDF**, haga clic en **servicios vinculados**. 

	![Página de inicio del generador de datos][image-data-factory-home-age]

2.	En el **servicios vinculados** blade, haga clic en **+ puerta de enlace de datos**.

	![Servicios vinculados - agregar un botón de la puerta de enlace][image-data-factory-linkedservices-add-gateway-button]

2. En el **crear** blade, escriba **adftutorialgateway** para el **nombre**, y haga clic en **Aceptar**.

	![Crear módulos de puerta de enlace][image-data-factory-create-gateway-blade]

3. En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto descarga el paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.

	> [AzURE.NOTE]Utilice Internet Explorer o un explorador web compatible de Microsoft ClickOnce.

	![Puerta de enlace - configurar blade][image-data-factory-gateway-configure-blade]

	Esta es la forma más sencilla (un clic) para descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver el **Microsoft Data Management Gateway Configuration Manager** aplicación está instalada en el equipo. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\Program Files\Microsoft datos administración Gateway\1.0\Shared**.

	También puede descargar e instalar puerta de enlace de manualmente mediante los vínculos en este módulo y registrarlo mediante la clave que se muestra en el **registrar con la clave** cuadro de texto.
	
	Consulte [Data Management Gateway](#DMG) sección para obtener más información acerca de la puerta de enlace incluye mejores prácticas y consideraciones importantes.

	>[AZURE.NOTE]Debe ser un administrador en el equipo local para instalar y configurar correctamente la puerta de enlace de datos de administración. Puede agregar usuarios adicionales al grupo local de usuarios de puerta de enlace de administración de datos de Windows. Los miembros de este grupo podrán utilizar la herramienta Administrador de configuración de puerta de enlace de administración de datos para configurar la puerta de enlace.

4. Haga clic en el **notificaciones** concentrador a la izquierda. Espere hasta que vea **Express el programa de instalación de 'adftutorialgateway' se realizó correctamente** el mensaje en el **notificaciones** blade.

	![Se realizó correctamente la configuración rápida][express-setup-succeeded]
5. Haga clic en **Aceptar** en el **crear** blade y, a continuación, en la **nueva puerta de enlace de datos** blade.
6. Cerrar la **servicios vinculados** blade (presionando **X** situado en la esquina superior derecha) y vuelva a abrir el **servicios vinculados** blade para ver el estado más reciente de la puerta de enlace. 
7. Confirme que el **estado** de la puerta de enlace es **en línea**. 

	![Estado de la puerta de enlace][gateway-status]
5. Iniciar **Microsoft Data Management Gateway Configuration Manager** a la aplicación en el equipo.

	![Administrador de configuración de puerta de enlace][image-data-factory-gateway-configuration-manager]

6. Espere hasta que los valores se hayan establecido como se indica a continuación:
	1. Si el servicio **estado** no está establecido en **Started**, haga clic en **iniciar el servicio** para iniciar el servicio y espera un minuto para los demás campos actualizar.
	2. **Nombre de puerta de enlace** está establecido en **adftutorialgateway**.
	3. **Nombre de instancia** está establecido en **adftutorialgateway**.
	4. **Estado clave de puerta de enlace** está establecido en **registrado**.
	5. El muestra de la parte inferior de la barra de estado **conectado al servicio de nube de Data Management Gateway** junto con un **marca de verificación verde**.
	
7. En el **servicios vinculados** blade, confirme que la **estado** de la puerta de enlace es **buena**.
8. Cierre todos los módulos hasta que llegue a la **factoría de datos** página principal. 

## Paso 2: Crear servicios vinculados 
En este paso, creará dos servicios vinculados: **StorageLinkedService** y **SqlServerLinkedService**. El **SqlServerLinkedService** vincula una base de datos de SQL Server local y la **StorageLinkedService** servicios vinculados vincula un almacén de blobs de Azure para la **ADFTutorialDataFactory**. Más adelante en este tutorial creará una canalización que copia datos de la base de datos de SQL Server local al almacén de blobs de Azure.

### Adición de un servicio vinculado a una base de datos de SQL Server local
1.	En el **factoría de datos** blade, haga clic en **autor e implementar** mosaico para iniciar la **Editor** para el generador de datos.

	![Mosaico Crear e implementar][image-author-deploy-tile]

	> [AZURE.NOTE][Editor de generador de datos][data-factory-editor]
2.	En el **Editor**, haga clic en **nuevo almacén de datos** botón en la barra de herramientas y seleccione **base de datos local SQL server** desde el menú desplegable. 

	![Botón de almacén de datos nuevo editor][image-editor-newdatastore-onpremsql-button]
    
3.	Debería ver la plantilla JSON para crear un servicio de SQL Server vinculado de forma local en el panel derecho. ![Local SQL vinculado servicio: configuración][image-editor-newdatastore-onpremsql-settings]

4.	Haga lo siguiente en el panel JSON:
	1.	Para el **gatewayName** propiedad, escriba **adftutorialgateway** para reemplazar todo el texto entre comillas dobles.  
	2.	Si está utilizando **autenticación de SQL**: 
		1.	Para el **connectionString** propiedad, reemplace **< nombreServidor >**, **< databasename >**, **< username >**, y **< contraseña >** con los nombres de su SQL server en local, la base de datos, la cuenta de usuario y la contraseña.	
		2.	Quitar las últimas dos propiedades (** username ** y **contraseña**) desde el JSON de archivos y quitar los **coma (,)** caracteres al final de la última línea del script JSON restantes.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Si está utilizando **la autenticación de Windows**:
		1. Para el **connectionString** propiedad, reemplace **< nombreServidor >** y **< databasename >** con los nombres de la base de datos y SQL server en local. Establecer **la seguridad integrada de** a **True**. Quitar **ID de** y **contraseña** de la cadena de conexión.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. Haga clic en **implementar** en la barra de herramientas para implementar el SqlServerLinkedService. Confirme que aparece el mensaje **vinculado el servicio creado correctamente** en la barra de título. También debe ver el **SqlServerLinkedService** en la vista de árbol de la izquierda.
		   
	![Implementación de SqlServerLinkedService correcta][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]También puede crear un servicio de SQL Server vinculado, haga clic en **nuevo almacén de datos** botón de barra de herramientas en el **servicios vinculados** blade. Si optamos por este camino, establezca las credenciales para el origen de datos mediante el uso de la aplicación de ClickOnce de administrador de credenciales que se ejecuta en el equipo de acceso al portal. Si tiene acceso al portal desde un equipo diferente desde el equipo de puerta de enlace, debe asegurarse de que la aplicación Administrador de credenciales puede conectar con el equipo de puerta de enlace. Si la aplicación no puede ponerse en contacto con el equipo de puerta de enlace, no le permitirá establecer credenciales para el origen de datos y probar la conexión al origen de datos.

#### Adición de un servicio vinculado para una cuenta de almacenamiento de Azure
 
1. En el **Editor**, haga clic en **nuevo almacén de datos** botón en la barra de herramientas y seleccione **almacenamiento de Azure** desde el menú desplegable. Debería ver la plantilla JSON para crear un servicio de almacenamiento de Azure vinculado en el panel derecho. 

	![Botón de almacén de datos nuevo editor][image-editor-newdatastore-button]
    
6. Reemplace **< nombreCuenta >** y **< accountkey >** con el nombre de cuenta y valores de clave de cuenta para la cuenta de almacenamiento de Azure.

	![Almacenamiento de blobs de editor JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Consulte [referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obtener más información acerca de las propiedades JSON.

6. Haga clic en **implementar** en la barra de herramientas para implementar el StorageLinkedService. Confirme que aparece el mensaje **vinculado el servicio creado correctamente** en la barra de título.

	![Implementar el almacenamiento de blobs de editor][image-editor-blob-storage-deploy]

 
## Paso 3: Crear la entrada y salida de los conjuntos de datos
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => almacenamiento de blobs de Azure). Antes de crear conjuntos de datos o tablas (conjuntos de datos rectangulares), debe hacer lo siguiente (después de la lista, se detallan los pasos):

- Crear una tabla denominada **emp** en la base de datos de SQL Server se agrega como un servicio vinculado a los datos par de fábrica y de inserción de entradas de ejemplo en la tabla.
- - Si no ha experimentado el tutorial de [empezar a trabajar con el generador de datos de Azure][adfgetstarted] del artículo, crear un contenedor de blob denominado **adftutorial** de Azure agrega como un servicio vinculado al generador de datos de cuenta de almacenamiento de blobs.

### Preparación de la instancia local de SQL Server para el tutorial

1. En la base de datos especificada para las instalaciones de SQL Server vinculado service (** SqlServerLinkedService **), use el siguiente script SQL para crear el **emp** tabla en la base de datos.


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

1.	En el **Editor del generador de datos**, haga clic en **nuevo conjunto de datos** en la barra de comandos y haga clic en **local SQL**. 
2.	Reemplace el JSON en el panel derecho con el siguiente texto:    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	Tenga en cuenta lo siguiente:
	
	- ubicación **tipo** está establecido en **OnPremisesSqlServerTableLocation**.
	- **tableName** está establecido en **emp**.
	- **linkedServiceName** está establecido en **SqlServerLinkedService** (este servicio vinculado hubiera creado en el paso 2).
	- Para una tabla de entrada que no se genera por otra canalización en la factoría de datos de Azure, debe especificar **waitOnExternal** sección en JSON. Indica que los datos de entrada se han producido fuera del servicio Factoría de datos de Azure.   

	Consulte [referencia de secuencias de comandos de JSON][json-script-reference] para obtener más información acerca de las propiedades JSON.

2. Haga clic en **implementar** en la barra de comandos para implementar el conjunto de datos (tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título dice **tabla implementada correctamente**.


### Creación de la tabla de salida

1.	En el **Editor del generador de datos**, haga clic en **nuevo conjunto de datos** en la barra de comandos y haga clic en **almacenamiento Azure Blob**.
2.	Reemplace el JSON en el panel derecho con el siguiente texto: 

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Tenga en cuenta lo siguiente:
	
	- ubicación **tipo** está establecido en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **StorageLinkedService** (este servicio vinculado hubiera creado en el paso 2).
	- **folderPath** está establecido en **adftutorial/outfromonpremdf** donde outfromonpremdf es la carpeta en el contenedor adftutorial. Necesitará crear el **adftutorial** contenedor.
	- El **disponibilidad** está establecido en **cada hora** (** frecuencia ** establecido en **hora** y **intervalo** establecido en **1**). El servicio de generador de datos generará un segmento de datos de salida cada hora en el **emp** tabla en la base de datos de SQL Azure. 

	Si no especifica un **nombre de archivo** para un **tabla de entrada**, todos los archivos o blobs desde la carpeta de entrada (** folderPath **) se consideran como entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Ver los archivos de ejemplo en el [tutorial][adf-tutorial] para obtener ejemplos.
 
	Si no especifica un valor **fileName** para una tabla de salida, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para establecer **folderPath** y **nombre de archivo** dinámicamente en función de la **SliceStart** tiempo, utilice la propiedad partitionedBy. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Consulte [referencia de secuencias de comandos de JSON][json-script-reference] para obtener más información acerca de las propiedades JSON.

2.	Haga clic en **implementar** en la barra de comandos para implementar el conjunto de datos (tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título dice **tabla implementada correctamente**.
  

## Paso 4: Crear y ejecutar una canalización
En este paso, creará un **canalización** con una **copia actividad** que utiliza **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como salida.

1.	Haga clic en **nueva canalización** en la barra de comandos. Si no ve el botón, haga clic en **... (puntos suspensivos)** para expandir la barra de comandos.
2.	Reemplace el JSON en el panel derecho con el siguiente texto:   


        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
							}	
						},
						"Policy":
						{
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
 
	- En la sección actividades, hay sólo actividad cuya **tipo** está establecido en **CopyActivity**.
	- **Entrada** de la actividad se establece en **EmpOnPremSQLTable** y **salida** de la actividad se establece en **OutputBlobTable**.
	- En el **transformación** sección, **SqlSource** se especifica como el **tipo de origen de** y **BlobSink **se especifica como el **receptor tipo**. - Consulta SQL **Seleccionar * desde emp** se especifica para la **sqlReaderQuery** propiedad de **SqlSource**.

	> [AZURE.NOTE]Sustituya el valor de la **iniciar** propiedad con la fecha actual y **end** valor con el día siguiente. Ambos iniciar y fechas y horas de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. El **end** tiempo es opcional, pero se utilizará en este tutorial. Si no especifica el valor para el **end** propiedad, se calcula como "** inicio + 48 horas **". Para ejecutar la canalización de forma indefinida, especifique **9/9/9999** como el valor de la **end** propiedad. Define la duración de tiempo en el que se procesarán las rebanadas de datos basándose en los **disponibilidad** las propiedades que se definen para cada tabla de la factoría de datos de Azure. En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
2. Haga clic en **implementar** en la barra de comandos para implementar el conjunto de datos (tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título dice **canalización implementada correctamente**.
5. Ahora, cierre el **Editor** blade haciendo clic en **X**. Haga clic en **X** para cerrar el cuadro de ADFTutorialDataFactory con la vista de árbol y de la barra de herramientas. Si ve **se descartarán los cambios no guardados** de mensajes, haga clic en **Aceptar**.
6. Debe volver a la **factoría de datos** módulo para la **ADFTutorialOnPremDF**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

### Vista del generador de datos en una vista de diagrama 
1. En el **Portal de vista previa de Azure**, haga clic en **diagrama** en mosaico en la página principal de la **ADFTutorialOnPremDF** factoría de datos.:

	![Vínculo de diagrama][image-data-factory-diagram-link]

2. Debería ver un diagrama similar al siguiente:

	![Vista de diagrama][image-data-factory-diagram-view]

	Puede acercar, alejar, zoom al 100%, zoom ajustar automáticamente colocar las canalizaciones y tablas y mostrar información de linaje (resalta elementos ascendente y descendentes de los elementos seleccionados). Puede blick de doble en un objeto (tabla de entrada y salida o canalización) para ver las propiedades para ella.

## Paso 5: Supervisar las canalizaciones y los conjuntos de datos
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información acerca del uso de cmdlets para la supervisión, consulte [Monitor y administrar factoría de datos Azure con PowerShell][monitor-manage-powershell].

1. Vaya a **Portal de vista previa de Azure** (si se haya cerrado)
2. Si el módulo para **ADFTutorialOnPremDF** no está abierto, ábralo haciendo clic en **ADFTutorialOnPremDF** en el **panel de inicio**.
3. Debería ver el **recuento** y **nombres** de tablas y canalización creada en este cuadro.

	![Página de inicio del generador de datos][image-data-factory-homepage-2]
4. Ahora, haga clic en **conjuntos de datos** de mosaico.
5. En el **conjuntos de datos** blade, haga clic en el **EmpOnPremSQLTable**.

	![Sectores EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Observe que ya se han producido las rebanadas de datos hasta la hora actual y son **Listo**. Esto ocurre porque ha insertado los datos en la base de datos de SQL Server y están ahí todo el tiempo. Confirme que los sectores no aparecen en la **sectores problema** sección en la parte inferior.


	Ambos **actualizado recientemente sectores** y **error recientemente sectores** listas se ordenan por el **hora de última actualización**. En las situaciones siguientes, se cambia la hora de un intervalo de actualización.
    

	-  Actualiza el estado del sector manualmente, por ejemplo, utilizando la **conjunto AzureDataFactorySliceStatus** (o), haga clic en **ejecutar** en el **sector** módulo para el sector.
	-  El segmento cambia estado debido a una ejecución (por ejemplo, una ejecución iniciado, una ejecución finalizado y no se pudo, una ejecución finaliza y se realizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** Para ver la lista más amplia de sectores. Haga clic en **filtro** en la barra de herramientas para filtrar los sectores.
	
	Para ver los intervalos de datos ordenados por los tiempos de inicio y fin del sector en su lugar, haga clic en **rebanadas de datos (por hora de sector)** en mosaico.

7. Ahora, en la **conjuntos de datos** blade, haga clic en **OutputBlobTable**.

	![Sectores OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Confirme que se producen sectores hasta la hora actual y **Listo**. Espere hasta que se establecen los Estados de sectores hasta la hora actual en **Listo**.
9. Haga clic en cualquier segmento de datos de la lista y debería ver el **segmento de datos** blade.

	![Módulo de segmento de datos][image-data-factory-dataslice-blade]

	Si no se encuentra en el sector del **Listo** estado, puede ver los intervalos de nivel superior que no están listos y están bloqueando el intervalo actual de la ejecución en el **intervalos de nivel superior que no están listos** lista.

10. Haga clic en el **actividad se ejecute** de la lista en la parte inferior para ver **Detalles de ejecución de la actividad**.

	![Cuadro de detalles de actividad][image-data-factory-activity-run-details]

11. Haga clic en **X** para cerrar todos los módulos hasta que regrese al blade principal para la **ADFTutorialOnPremDF**.
14. (opcional) Haga clic en **canalizaciones**, haga clic en **ADFTutorialOnPremDF**, y obtener detalles de las tablas de entrada (** consumidos **) o tablas de salida (** producido **).
15. Usar herramientas como **Azure Storage Explorer** para comprobar el resultado.

	![Explorador de almacenamiento de Azure][image-data-factory-stroage-explorer]


## Crear y registrar una puerta de enlace con Azure PowerShell 
En esta sección se explica cómo crear y registrar una puerta de enlace usando cmdlets de Azure PowerShell.

1. Iniciar **Azure PowerShell** en modo de administrador. 
2. Los cmdlets de generador de datos de Azure están disponibles en la **AzureResourceManager** modo. Ejecute el siguiente comando para cambiar a la **AzureResourceManager** modo.     

        switch-azuremode AzureResourceManager


2. Utilice la **AzureDataFactoryGateway nuevo** cmdlet para crear una puerta de enlace lógico como sigue:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Comando de ejemplo y salida**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. Utilice la **AzureDataFactoryGatewayKey nuevo** cmdlet para generar una clave de registro para la puerta de enlace recién creado y almacenar la clave en una variable local **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Resultado del comando de ejemplo:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. En Azure PowerShell, cambie a la carpeta: **C:\Program Files\Microsoft datos administración Gateway\1.0\PowerShellScript** y ejecute **RegisterGateway.ps1** secuencia de comandos asociado a la variable local **$Key** como se muestra en el siguiente comando para registrar el agente de cliente instalado en el equipo con la puerta de enlace lógico crear anteriormente.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Puede utilizar el **AzureDataFactoryGateway Get** para obtener la lista de puertas de enlace en el generador de datos. Cuando el **estado** muestra **en línea**, esto significa que la puerta de enlace está listo para su uso.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Puede quitar una puerta de enlace con el **AzureDataFactoryGateway quitar** descripción del cmdlet y actualización de una puerta de enlace mediante el **AzureDataFactoryGateway Set** cmdlets. Para ver la sintaxis y otros detalles de estos cmdlets, consulte la documentación de referencia de los cmdlets de Factoría de datos.



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

<!---HONumber=GIT-SubDir--> 