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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Habilitación de canalizaciones para trabajar con datos locales

Para habilitar las canalizaciones en una factoría de datos de Azure para trabajar con un origen de datos local, debe agregar el origen de datos local como un servicio vinculado a la factoría de datos mediante el Portal de administración de Azure o Azure PowerShell.
 
Para poder agregar un origen de datos local como un servicio vinculado a una factoría de datos, primero debe descargar e instalar Microsoft Data Management Gateway en un equipo local y configurar el servicio vinculado para que el origen de datos local use la puerta de enlace.
 
> [WACOM.NOTE] Actualmente, solo se admite SQL Server como origen de datos local.

## <a href="DMG"></a> Data Management Gateway

**Data Management Gateway** es un software que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Con Data Management Gateway, puede realizar lo siguiente:

- **Conectar datos locales para el acceso a datos híbrido.** Puede conectar datos locales a servicios en la nube para aprovechar las ventajas que ofrecen los servicios en la nube mientras su negocio continúa funcionando con los datos locales.
- **Definir un proxy de datos seguro.** Puede definir qué orígenes de datos locales se exponen a través de Data Management Gateway para que esta puerta de enlace autentique la solicitud de datos de servicios en la nube y proteja los orígenes de datos locales.
- **Administrar la puerta de enlace para tener el control total.** Se proporcionan supervisión y registro completos de todas las actividades dentro de Data Management Gateway con fines de administración y control.
- **Mover datos de manera eficaz.** Los datos se transfieren en paralelo, con resistencia a problemas de red intermitentes mediante lógica automática de reintentos.


Data Management Gateway ofrece una gama completa de características de conexión de datos locales que incluye las siguientes:

- **No intrusivo para el firewall corporativo.** Data Management Gateway funciona después de la instalación, sin tener que abrir una conexión de servidor de seguridad ni realizar cambios intrusivos en la infraestructura de red corporativa.
- **Cifrar las credenciales con el certificado.** Las credenciales usadas para conectarse a orígenes de datos se cifran con un certificado que pertenece totalmente a un usuario. Sin el certificado, nadie puede descifrar las credenciales en texto sin formato, incluido Microsoft.

### Instalación de la puerta de enlace: procedimientos recomendados

1.	Si el equipo host hiberna, la puerta de enlace no puede responder a la solicitud de datos. Por tanto, configure un **plan de energía** adecuado en el equipo antes de instalar la puerta de enlace. 
2.	Data Management Gateway debe ser capaz de conectarse a los orígenes de datos locales que va a registrar en el servicio Factoría de datos de Azure. No necesita estar en el mismo equipo que el origen de datos, pero, **si está cerca del origen de datos**, reduce el tiempo de la puerta de enlace para conectarse al origen de datos.

### Consideraciones para utilizar Data Management Gateway
1.	Una única instancia de Data Management Gateway se puede usar para varios orígenes de datos locales, pero tenga en cuenta que una **puerta de enlace está asociada a una factoría de datos de Azure** y no puede compartirse con otra factoría de datos.
2.	So puede haber **una instancia de Data Management Gateway** instalada en el equipo. Supongamos que tiene dos factorías de datos que necesitan tener acceso a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales donde cada puerta de enlace esté asociada a una factoría de datos diferente.
3.	Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI**, instale una **puerta de enlace para la Factoría de datos de Azure** en otro equipo. 
 

## Tutorial

En este tutorial se crea una factoría de datos con una canalización que mueve los datos de una base de datos de SQL Server local a un blob de Azure. 

### Paso 1: Crear una factoría de datos de Azure
En este paso, utilice el Portal de administración de Azure para crear una instancia de factoría de datos de Azure denominada **ADFTutorialOnPremDF**. También puede crear una factoría de datos mediante cmdlets de la Factoría de datos de Azure. 

1.	Tras iniciar sesión en el [Portal de Azure en vista previa][
2.	azure-preview-portal], haga clic en **NUEVO** en la esquina inferior izquierda y elija **Factoría de datos** en la hoja **Nuevo**.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Si no aparece **Factoría de datos** en la hoja **Nuevo**, desplace el menú hacia abajo.  
6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialOnPremDF** en **Nombre**.
	2. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y seleccione **ADFTutorialResourceGroup** (si ha realizado el tutorial [Introducción a la Factoría de datos de Azure][adfgetstarted]. Puede seleccionar un grupo de recursos existente o crear uno nuevo. Para crear un nuevo grupo de recursos:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba un **nombre** para el grupo y haga clic en **Aceptar**.

7. Observe que está seleccionada la opción **Agregar al Panel de inicio** en la hoja **Nueva factoría de datos**.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. En la hoja **Nueva factoría de datos**, haga clic en **Crear**.
9. Busque las notificaciones del proceso de creación en el centro de **NOTIFICACIONES** situado a la izquierda.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. Una vez completada la creación, verá la hoja Factoría de datos como se muestra a continuación:

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. Debe aparecer también en el **Panel de inicio** como se muestra a continuación. Haga clic en él para abrir la hoja **Factoría de datos** si no está abierta aún.

	![Startboard][image-data-factory-startboard]

### Paso 2: Crear servicios vinculados 
En este paso, creará dos servicios vinculados: **MyBlobStore** y **OnPremSqlLinkedService**. El servicio **OnPremSqlLinkedService** vincula una base de datos de SQL Server local y el servicio vinculado **MyBlobStore** vincula un almacén de blobs de Azure a **ADFTutorialDataFactory**. Más adelante en este tutorial creará una canalización que copia datos de la base de datos de SQL Server local al almacén de blobs de Azure. 

### Adición de un servicio vinculado a una base de datos de SQL Server local
1.	En la hoja **Factoría de datos** de **ADFTutorialOnPremDF**, haga clic en **Servicios vinculados**. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	En la hoja **Servicios vinculados**, haga clic en **+ Puerta de enlace de datos**.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. En la hoja **Crear**, escriba **adftutorialgateway** para **Nombre** y haga clic en **Aceptar**. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto descarga el paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	Esta es la forma más sencilla (un clic) para descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver que la aplicación **Administrador de configuración de Microsoft Data Management Gateway** está instalada en el equipo. También puede buscar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\Archivos de programa\Microsoft Data Management Gateway\1.0\Shared**.

	Asimismo, puede descargar e instalar la puerta de enlace manualmente con los vínculos de esta hoja y registrarla utilizando la clave que se muestra en el cuadro de texto **REGISTRAR CON LA CLAVE**.
	
	Consulte la sección [Data Management Gateway](#DMG) para obtener información detallada de la puerta de enlace, incluidos procedimientos recomendados y algunas consideraciones importantes. 

4. Haga clic en **Aceptar** en la hoja **Nueva puerta de enlace de datos**.
5. Inicie la aplicación **Microsoft Data Management Gateway**   en el equipo.

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. Espere hasta que los valores se hayan establecido como se indica a continuación:
	1. Si el **estado** del servicio no es **Iniciado**, haga clic en **Iniciar servicio** para iniciarlo y espere un minuto para que se actualicen los demás campos.
	2. **Nombre de la puerta de enlace** está establecido en **adftutorialgateway**.
	3. **Nombre de instancia** está establecido en **adftutorialgateway**.
	4. **Estado de la clave de la puerta de enlace** está establecido en **Registrada**.
	5. La barra de estado de la parte inferior muestra **Conectado al servicio en la nube Data Management Gateway** junto con una **marca de verificación verde**.  

7. En la hoja **Servicios vinculados**, confirme que el **estado** de la puerta de enlace es **Bueno** y haga clic en **+ Almacén de datos**. Puede que necesite cerrar y volver a abrir la hoja para actualizarla. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. En la hoja **Nuevo almacén de datos**, escriba **OnPremSqlLinkedService** para el **nombre**.
9. Haga clic en **TIPO (requiere configuración)** y seleccione **SQL Server**. 
10. En la hoja **Nuevo almacén de datos**, haga clic en **PUERTA DE ENLACE DE DATOS(requiere configuración)**.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. Seleccione la puerta **adftutorialgateway** que creó antes. 
12.	En la hoja **Nuevo almacén de datos**, haga clic en **CREDENCIALES** para mostrar la hoja **Credenciales**.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	En la hoja **Credenciales**, haga clic en la opción **Haga clic aquí para establecer las credenciales de forma segura**. Inicia el siguiente cuadro de diálogo emergente.

	![One Click application install][image-data-factory-oneclick-install]

14. Haga clic en **Ejecutar** para instalar la aplicación **Administrador de credenciales** y mostrar el cuadro de diálogo Configuración de credenciales. 
15.	En el cuadro de diálogo **Configuración de credenciales**, escriba el **nombre de usuario** y la **contraseña** que el servicio puede usar para obtener acceso a la base de datos de SQL Server local, y haga clic en **Aceptar**. Este cuadro de diálogo solo admite **Autenticación de SQL**. Espere hasta que se cierre el cuadro de diálogo.
16.	Haga clic en **Aceptar** en la hoja **Credenciales** y elija **Aceptar** en la hoja **Nuevo almacén de datos**. Debe aparecer el servicio vinculado **OnPremSqlLinkedService** que agregó en la hoja Servicios vinculados.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Adición de un servicio vinculado para una cuenta de almacenamiento de Azure

1.	En la hoja **Factoría de datos**, haga clic en el cuadro **Servicios vinculados** para iniciar la hoja **Servicios vinculados**.
2. En la hoja **Servicios vinculados**, haga clic en **Agregar almacén de datos**.	
3. En la hoja **Nuevo almacén de datos**:
	1. Escriba un **nombre** para el almacén de datos. Para este tutorial, escriba **MyBlobStore** para el **nombre**.
	2. Escriba una **descripción (opcional)** para el almacén de datos.
	3. Haga clic en **Tipo**.
	4. Seleccione **Cuenta de almacenamiento de Azure** y haga clic en **Aceptar**.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. Ahora ha vuelto a la hoja **Nuevo almacén de datos**, que tiene el siguiente aspecto:

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Escriba el **Nombre de cuenta** y la **Clave de cuenta** para la cuenta de almacenamiento de Azure y haga clic en **Aceptar**.

  
6. Después de hacer clic en **Aceptar** en la hoja **Nuevo almacén de datos**, debe aparecer **MyBlobStore** en la lista **ALMACENES DE DATOS** en la hoja **Servicios vinculados**. Compruebe si hay algún mensaje en el centro de **NOTIFICACIONES** (a la izquierda).

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## Paso 3: Crear conjuntos de datos de entrada y salida
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => almacenamiento de blobs de Azure). Actualmente, el Portal de Azure en vista previa no admite la creación de conjuntos de datos ni canalizaciones, por lo que usará cmdlets de PowerShell de Azure para crear tablas y canalizaciones. Antes de crear conjuntos de datos o tablas (conjuntos de datos rectangulares), debe hacer lo siguiente (después de la lista, se detallan los pasos):

- Cree una tabla con el nombre **emp** en la base de datos de SQL Server que agregó como servicio vinculado a la factoría de datos e inserte un par de entradas de ejemplo en la tabla.
- - Si no ha realizado el tutorial del artículo [Introducción a la Factoría de datos de Azure][adfgetstarted], cree un contenedor de blobs denominado **adftutorial** en la cuenta de almacenamiento de blobs de Azure que agregó como servicio vinculado a la factoría de datos.

### Preparación de la instancia local de SQL Server para el tutorial

1. En la base de datos que especificó para el servicio vinculado SQL Server local (**OnPremSqlLinkedService**), use el siguiente script SQL para crear la tabla **emp** en la base de datos.


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

1.	Cree un archivo JSON para una tabla de factoría de datos que represente los datos de la tabla **emp** en la base de datos de SQL Server. Inicie el **Bloc de notas**, copie el siguiente script JSON y guárdelo como **EmpOnPremSQLTable.json** en la carpeta C:\ADFGetStarted\**OnPrem**. Cree la subcarpeta **OnPrem** en la carpeta **C:\ADFGetStarted** si no existe. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
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
	
	- El tipo (**type**) de ubicación está establecido en **OnPremisesSqlServerTableLocation**.
	- **tableName** está establecido en **emp**.
	- **linkedServiceName** está establecido en **OnPremSqlLinkedService** (creó este servicio vinculado en el paso 2).
	- Para una tabla de entrada no generada por otra canalización en la Factoría de datos de Azure, debe especificar la sección **waitOnExternal** en el código JSON. Indica que los datos de entrada se han producido fuera del servicio Factoría de datos de Azure.   

	Consulte la [documentación de referencia de scripting con JSON][json-script-reference] para obtener información detallada acerca de las propiedades JSON.

2. Los cmdlets de la Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**. Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager

	Descargue [Azure PowerShell][azure-powershell-install] si no lo tiene instalado en el equipo.
3. Utilice el cmdlet **New-AzureDataFactoryTable** para crear la tabla de entrada con el archivo **EmpOnPremSQLTable.json**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	Actualice el comando si está usando un grupo de recursos diferente.

### Creación de la tabla de salida

1.	Cree un archivo JSON para usar una tabla de la Factoría de datos como salida de la canalización que va a crear en el siguiente paso. Inicie el Bloc de notas, copie el siguiente script JSON y guárdelo como **OutputBlobTable.json** en la carpeta **C:\ADFGetStarted\OnPrem**.

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
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Tenga en cuenta lo siguiente: 
	
	- location **type** está establecido en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **MyBlobStore** (creó este servicio vinculado en el paso 2).
	- **folderPath** está establecido en **adftutorial/outfromonpremdf**, donde outfromonpremdf es la carpeta del contenedor adftutorial. Solo tiene que crear el contenedor **adftutorial**.
	- **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** en **1**).  El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** en la base de datos SQL de Azure. 

	Si no especifica un **nombre de archivo** para una **tabla de entrada**, todos los archivos o blobs de esta carpeta (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Puede ver algunos archivos de ejemplo en el [tutorial][adf-tutorial].
 
	Si no especifica un **nombre de archivo** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan en el siguiente formato: Data.<Guid>.txt (por ejemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

 

	Consulte la [documentación de referencia de scripting con JSON][json-script-reference] para obtener información detallada acerca de las propiedades JSON.

2.	En **Azure PowerShell**, ejecute el siguiente comando para crear otra tabla de factoría de datos para representar la tabla emp en la base de datos de SQL Azure.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## Paso 4: Creación y ejecución de una canalización
En este paso, va a crear una **canalización** con una **actividad de copia** que usa **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como salida.

1.	Cree un archivo JSON para la canalización. Inicie el Bloc de notas, copie el siguiente script JSON y guárdelo como **ADFTutorialPipelineOnPrem.json** en la carpeta **C:\ADFGetStarted\OnPrem**.
	 

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
	        	]
			}
		}

	Tenga en cuenta lo siguiente:
 
	- En la sección activities, hay solo una actividad cuyo **type** está establecido en **CopyActivity**.
	- **Input** está establecido en **EmpOnPremSQLTable** y **output** está establecido en **OutputBlobTable** para la actividad.
	- En la sección **transformation**, **SqlSource** está especificado como **tipo de origen** y **BlobSink ** está especificado como **tipo de receptor**.
	- La consulta SQL **select * from emp** está especificada para la propiedad **sqlReaderQuery** de **SqlSource**.

2. Use el cmdlet **New-AzureDataFactoryPipeline** para crear una canalización mediante el archivo **ADFTutorialPipeline.json** que ha creado.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. Una vez creadas las canalizaciones, puede especificar el tiempo en el que se producirá el procesamiento de datos. Al especificar el período activo para una canalización, define el tiempo durante el que se procesarán los segmentos de datos en función de las propiedades **Availability** que se definieron para cada tabla de la Factoría de datos de Azure.  Ejecute el siguiente comando de PowerShell para establecer el período activo en la canalización y escriba Y para confirmar. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] Reemplace el valor de **StartDateTime** por el día actual y el valor de **EndDateTime** por el día siguiente. Tanto StartDateTime como EndDateTime son horas UTC y deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. EndDateTime es opcional, pero se usará en este tutorial.
	> Si no especifica **EndDateTime**, se calcula como "**StartDateTime + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de **EndDateTime**.

	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
4. En el **Portal de Azure en vista previa**, haga clic en **Diagrama** en la página principal de la factoría de datos **ADFTutorialOnPremDF**. :

	![Diagram Link][image-data-factory-diagram-link]

5. Debería ver un diagrama similar al siguiente:

	![Diagram View][image-data-factory-diagram-view]

	**¡Enhorabuena!** Ha creado una factoría de datos de Azure, servicios vinculados, tablas y una  	canalización, y ha iniciado el flujo de trabajo.

## Paso 5: Supervisar la canalización y los conjuntos de datos
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener información detallada acerca del uso de cmdlets para supervisión, consulte [Supervisión y administración de Factoría de datos de Azure con PowerShell][monitor-manage-powershell].

1. Vaya al **Portal de Azure en vista previa** (si lo ha cerrado)
2. Si la hoja de **ADFTutorialOnPremDF** no está abierta, ábrala haciendo clic en **ADFTutorialOnPremDF** en el **Panel de inicio**.
3. Deben mostrarse el **recuento** y los **nombres** de las tablas y la canalización que creó en esta hoja.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. Ahora haga clic en **Conjuntos de datos**.
5. En la hoja **Conjuntos de datos**, haga clic en **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. Tenga en cuenta que ya se han producido los segmentos de datos hasta el momento actual y están **listos**. Esto ocurre porque ha insertado los datos en la base de datos de SQL Server y están ahí todo el tiempo. Confirme que no aparecen segmentos en la sección de **segmentos problemáticos** en la parte inferior.
7. Ahora en la hoja **Conjuntos de datos**, haga clic en **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Confirme que se han producido los segmentos hasta el momento actual y que están **listos**.
9. Haga clic en cada segmento de datos de la lista. Debe aparecer la hoja **SEGMENTO DE DATOS**.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. Haga clic en la **ejecución de la actividad** en la lista inferior para ver los **detalles de la ejecución de la actividad**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Haga clic en la **X** para cerrar todas las hojas hasta que vuelva a la hoja principal de **ADFTutorialOnPremDF**.
14. (Opcional) Haga clic en **Canalizaciones**, elija **ADFTutorialOnPremDF** y explore en profundidad las tablas de entrada (**Consumido**) o las tablas de salida (**Producido**).
15. Use herramientas como el **Explorador de almacenamiento de Azure** para comprobar la salida.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Creación y registro de una puerta de enlace con cmdlets de Azure PowerShell
En esta sección se explica cómo crear y registrar una puerta de enlace usando cmdlets de Azure PowerShell. 

1. Inicie **Azure PowerShell** en modo de administrador. 
2. Los cmdlets de la Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**. Ejecute el siguiente comando para cambiar al modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Use el cmdlet **New-AzureDataFactoryGateway** para crear una puerta de enlace lógica como se indica a continuación:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**Ejemplo de comando y salida**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. Use el cmdlet **New-AzureDataFactoryGatewayKey** para generar una clave de registro para la puerta de enlace recién creada y guarde la clave en una variable local **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**Ejemplo de comando y salida:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. En Azure PowerShell, cambie a la carpeta: **C:\Archivos de programa\Microsoft Data Management Gateway\1.0\PowerShellScript\** y ejecute el script **RegisterGateway.ps1** asociado con la variable local **$Key** como se muestra en el siguiente comando para registrar el agente cliente instalado en su equipo en la puerta de enlace lógica que creó antes.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Utilice el cmdlet **Get-AzureDataFactoryGateway** para obtener la lista de puertas de enlace de su factoría de datos. Cuando el **Estado** es **En línea**, significa que la puerta de enlace está lista para usarla.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

Puede quitar una puerta de enlace usando el cmdlet **Remove-AzureDataFactoryGateway** y actualizar la descripción de una puerta de enlace usando los cmdlets **Set-AzureDataFactoryGateway**. Para ver la sintaxis y otros detalles de estos cmdlets, consulte la documentación de referencia de los cmdlets de Factoría de datos.  




## Otras referencias

Artículo | Descripción
------ | ---------------
[Introducción a la Factoría de datos de Azure][adf-getstarted] | En este artículo se ofrece un tutorial completo que muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos de un blob de Azure a una base de datos de SQL Azure.
[Uso de Pig y Hive con la Factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo contiene un tutorial que muestra cómo utilizar la actividad HDInsight para ejecutar un script de hive y pig que procesa datos de entrada para generar datos de salida. 
[Tutorial: Movimiento y procesamiento de archivos de registro usando la Factoría de datos][adf-tutorial] | Este artículo ofrece un tutorial completo que muestra cómo implementar un escenario casi real usando la Factoría de datos de Azure para transformar datos de archivos de registro en perspectivas.
[Uso de actividades personalizadas en una factoría de datos][use-custom-activities] | En este artículo se ofrece un tutorial con instrucciones paso a paso para crear una actividad personalizada y usarla en una canalización. 
[Solución de problemas de Factoría de datos][troubleshoot] | En este artículo se explica cómo solucionar problemas de la Factoría de datos de Azure.  
[Documentación de referencia para desarrolladores de Factoría de datos de Azure][developer-reference] | La documentación de referencia para desarrolladores incluye el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/


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

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!--HONumber=46--> 

<!--HONumber=46--> 
