<properties 
	pageTitle="Movimiento de datos: Data Management Gateway | Microsoft Azure"
	description="Configuración de una puerta de enlace para mover datos entre una infraestructura local y la nube. Uso de Data Management Gateway en Data Factory de Azure para mover los datos." 
    keywords="puerta de enlace de datos, integración de datos, mover datos, credenciales de puerta de enlace"
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
	ms.date="07/08/2016" 
	ms.author="spelluru"/>

# Movimiento de datos entre orígenes locales y la nube con Data Management Gateway
Este artículo proporciona información general sobre la integración de los almacenes de datos locales y los almacenes de datos en la nube mediante Data Factory. Este artículo se basa en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) y otros artículos de conceptos básicos de Data Factory: [conjuntos de datos](data-factory-create-datasets.md) y [canalizaciones](data-factory-create-pipelines.md).

## Data Management Gateway
Debe instalar Data Management Gateway en su equipo local para habilitar el movimiento de datos a o desde un almacén de datos local. La puerta de enlace puede instalarse en el mismo equipo que el almacén de datos o en un equipo diferente, siempre que la puerta de enlace pueda conectarse al almacén de datos. Vea el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para obtener todos los detalles sobre Data Management Gateway.

El siguiente tutorial se muestra cómo crear una factoría de datos con una canalización que mueve los datos de una Base de datos de SQL Server local a un blob de Azure. Como parte del tutorial, instalará y configurará Data Management Gateway en su equipo.

## Tutorial: copiar datos locales a la nube
  
## Creación de Data Factory
En este paso, use el Portal de Azure para crear una instancia de Data Factory de Azure denominada **ADFTutorialOnPremDF**. También puede crear una factoría de datos mediante cmdlets de la Factoría de datos de Azure.

1.	Tras iniciar sesión en el [Portal de Azure](https://portal.azure.com), haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **Análisis de datos** en la hoja **Crear** y haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

	![New->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialOnPremDF** en el **nombre**.
	2. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y seleccione **ADFTutorialResourceGroup**. Puede seleccionar un grupo de recursos existente o crear uno nuevo. Para crear un nuevo grupo de recursos:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba un **nombre** para el grupo de recursos y haga clic en **Aceptar**.

7. Observe que está seleccionada la opción **Agregar al Panel de inicio** en la hoja **Nueva factoría de datos**.

	![Agregar al Panel de inicio](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. En la hoja **Nueva factoría de datos**, haga clic en **Crear**.

	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "ADFTutorialOnPremDF" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearla de nuevo. Use este nombre en lugar de ADFTutorialFactory al realizar los restantes pasos de este tutorial.

9. Para buscar las notificaciones desde el proceso de creación, haga clic en el botón **Notificaciones** que se encuentra en la barra de título, como se muestra en la siguiente imagen. Haga clic en él de nuevo para cerrar la ventana de notificaciones.

	![Centro de NOTIFICACIONES](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Una vez completada la creación, verá la hoja **Factoría de datos** como se muestra a continuación:

	![Página principal de Factoría de datos](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## Crear puerta de enlace
5. En la hoja **FACTORÍA DE DATOS**, haga clic en la ventana **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
6.	En el Editor de la Factoría de datos, haga clic en **... (puntos suspensivos)** en la barra de herramientas y luego haga clic en **Nueva puerta de enlace de datos**.

	![Nueva puerta de enlace de datos en la barra de herramientas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. En la hoja **Crear**, escriba **adftutorialgateway** para el **nombre** y haga clic en **Aceptar**.

	![Hoja Crear puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto descarga el paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.

	> [AZURE.NOTE] 
	Utilice Internet Explorer o un explorador web compatible con Microsoft ClickOnce.
	> 
	> Si usa Chrome, vaya a [Chrome Web Store](https://chrome.google.com/webstore/), busque la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instálela.
	>  
	> Debe hacer lo mismo para Firefox (complemento de instalación). Haga clic en el botón **Abrir menú** de la barra de herramientas (**tres líneas horizontales** en la esquina superior derecha), haga clic en **Complementos**, busque la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instálela.

	![Puerta de enlace: hoja Configurar](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Esta es la forma más sencilla (un clic) para descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver que la aplicación **Administrador de configuración de Microsoft Data Management Gateway** está instalada en el equipo. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	También puede descargar e instalar la puerta de enlace manualmente con los vínculos de esta hoja y registrarla usando la clave que se muestra en el cuadro de texto **NUEVA CLAVE**.
	
	Vea el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para obtener todos los detalles sobre la puerta de enlace.

	>[AZURE.NOTE] Debe ser administrador del equipo local para instalar y configurar correctamente Data Management Gateway. Puede agregar usuarios adicionales al grupo local de Windows Usuarios de usuarios de Data Management Gateway. Los miembros de este grupo podrán usar la herramienta Administrador de configuración de Data Management Gateway para configurar la puerta de enlace.

5. Espere un par de minutos e inicie la aplicación **Administración de configuración de Data Management Gateway** en el equipo. En la ventana **Búsqueda**, escriba **Data Management Gateway** para tener acceso a esta utilidad. También puede encontrar el archivo ejecutable **ConfigManager.exe** en la carpeta: **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	![Administrador de configuración de puertas de enlace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Espere hasta que los valores se hayan establecido como se indica a continuación:
	2. **Nombre de la puerta de enlace** está establecido en **adftutorialgateway**.
	4. La barra de estado de la parte inferior muestra **Conectado al servicio en la nube** junto con una **marca de verificación verde**.

	En la pestaña **Inicio**, también puede hacer lo siguiente: **Registrar** una puerta de enlace con una clave desde el Portal de Azure mediante el botón Registrar. **Detener** la ejecución del servicio host de Data Management Gateway en el equipo de la puerta de enlace. **Programar actualizaciones** que se deben instalar a una hora determinada del día. Ver cuándo se ha **actualizado por última vez** la puerta de enlace.

8. Cambie a la pestaña **Configuración**. El certificado que se ha especificado en la sección Certificado se usa para cifrar o descifrar las credenciales para el almacén de datos local que especifique en el Portal (opcional para este tutorial). Haga clic en **Cambiar** para usar su propio certificado en su lugar. De forma predeterminada, la puerta de enlace usa el certificado generado automáticamente por el servicio Factoría de datos.

	![Configuración del certificado de puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

	También puede realizar las siguientes funciones en la pestaña Configuración: - Ver o exportar el certificado que está usando la puerta de enlace. - Cambiar el punto de conexión HTTPS que usa la puerta de enlace.
9. (Opcional) Cambie a la pestaña **Diagnósticos**, active la opción **Habilitar el registro detallado** si quiere habilitar el registro detallado que puede usar para solucionar los problemas de la puerta de enlace. La información de registro se encuentra en el **Visor de sucesos**, en el nodo **Registros de aplicaciones y servicios** -> **Data Management Gateway**.

	![Ficha Diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	También puede hacer lo siguiente en la pestaña **Diagnósticos**:
	
	- Utilice la sección **Probar conexión** para probar la conexión con un origen de datos local mediante la puerta de enlace.
	- Haga clic en **Ver registros** para ver el registro de la puerta de enlace de administración de datos en una ventana del Visor de eventos.
	- Haga clic en **Enviar registros** para cargar un archivo zip con los registros de los últimos siete días en Microsoft y facilitar así la solución de sus problemas.
10. En el Portal de Azure, haga clic en **Aceptar** en la hoja **Configurar** y, después, en la hoja **Nueva puerta de enlace de datos**.
6. Debería ver **adftutorialgateway** en **Puertas de enlace de datos** en la vista de árbol de la izquierda. Si hace clic en ella, debería ver el JSON asociado.
	

## Crear servicios vinculados 
En este paso, creará dos servicios vinculados: **AzureStorageLinkedService** y **SqlServerLinkedService**. **SqlServerLinkedService** vincula una base de datos de SQL Server local, mientras que **AzureStorageLinkedService** vincula un almacén de blobs de Azure a la instancia de Data Factory. Más adelante en este tutorial creará una canalización que copia datos de la base de datos de SQL Server local al almacén de blobs de Azure.

#### Adición de un servicio vinculado a una base de datos de SQL Server local
1.	En el **Editor de la Factoría de datos**, haga clic en **Nuevo almacén de datos** en la barra de herramientas y seleccione **SQL Server**.

	![Nuevo servicio vinculado de SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
3.	En el **Editor de JSON**, haga lo siguiente:
	1. Para el **gatewayName**, especifique **adftutorialgateway**.
	2. Si está usando la autenticación de Windows:
		1. En **connectionString**:
			1. Establezca **Seguridad integrada** en **true**.
			2. Especifique el **nombre de servidor** de la base de datos y el **nombre de la base de datos**.
			2. Quite **Id. de usuario** y **Contraseña**.
		3. Especifique el nombre de usuario y la contraseña en las propiedades **userName** y **password**.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}

	4. Si está usando la autenticación de SQL:
		1. Especifique el **nombre del servidor** de la base de datos, el **nombre de la base de datos**, el **Id. de usuario** y la **Contraseña** en **connectionString**.
		2. Quite las últimas dos propiedades JSON (**userName** y **password**) de JSON.
		3. Quite la **, (coma)** al final de la línea que especifica el valor de la propiedad **gatewayName**.

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		Las credenciales se **cifrarán** mediante un certificado que es propiedad del servicio Data Factory. Si prefiere usar el certificado asociado a la puerta de enlace de administración de datos, consulte [Configuración de credenciales y seguridad](#set-credentials-and-security).
    
2.	Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado de SQL Server.

#### Adición de un servicio vinculado para una cuenta de almacenamiento de Azure
 
1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo almacén de datos** en la barra de comandos y haga clic en **Almacenamiento de Azure**.
2. Escriba el nombre de la cuenta de almacenamiento de Azure en **Nombre de cuenta**.
3. Escriba la clave de su cuenta de almacenamiento de Azure en **Clave de cuenta**.
4. Haga clic en **Implementar** para implementar **AzureStorageLinkedService**.
   
 
## Creación de conjuntos de datos
En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => almacenamiento de blobs de Azure). Antes de crear conjuntos de datos o tablas (conjuntos de datos rectangulares), debe hacer lo siguiente (después de la lista, se detallan los pasos):

- Cree una tabla con el nombre **emp** en la base de datos de SQL Server que agregó como servicio vinculado a la factoría de datos e inserte un par de entradas de ejemplo en la tabla.
- Cree un contenedor de blobs llamado **adftutorial** en la cuenta de almacenamiento de blobs de Azure que agregó como un servicio vinculado a la factoría de datos.

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

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Tabla de SQL Server**.
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
	
	- **type** está establecido como **SqlServerTable**.
	- **tableName** está establecido en **emp**.
	- **linkedServiceName** está establecido en **SqlServerLinkedService** (creó este servicio vinculado en el paso 2).
	- Para una tabla de entrada no generada por otra canalización en Factoría de datos de Azure, tiene que especificar la propiedad **external** en **true**. Indica que los datos de entrada se han producido fuera del servicio Factoría de datos de Azure. Opcionalmente, puede especificar las directivas de datos externos mediante el elemento **externalData** en la sección **Policy**.

	Consulte la [documentación de referencia de scripting con JSON][json-script-reference] para obtener información detallada acerca de las propiedades JSON.

2. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA TABLA SE IMPLEMENTÓ CORRECTAMENTE**.


### Creación de la tabla de salida

1.	En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.
2.	Reemplace el script JSON del panel derecho por el texto siguiente:

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
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
	- **linkedServiceName** está establecido en **AzureStorageLinkedService** (este servicio vinculado se creó en el paso 2).
	- **folderPath** está establecido en **adftutorial/outfromonpremdf**, donde outfromonpremdf es la carpeta del contenedor adftutorial. Solo tiene que crear el contenedor **adftutorial**.
	- El elemento **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** en **1**). El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.

	Si no especifica **fileName** para una **tabla de entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Puede ver algunos archivos de ejemplo en [tutorial][adf-tutorial].
 
	Si no especifica un valor **fileName** para una **tabla de salida**, el nombre de los archivos generados en **folderPath** tendrán siguiente formato: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2.	Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA TABLA SE IMPLEMENTÓ CORRECTAMENTE**.
  

## Creación de una canalización
En este paso, va a crear una **canalización** con una **actividad de copia** que usa **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como salida.

1.	En la hoja **FACTORÍA DE DATOS**, haga clic en la ventana **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2.	Haga clic en **Nueva canalización** en la barra de comandos. Si no ve el botón, haga clic en **... (puntos suspensivos)** para expandir la barra de comandos.
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
		    "start": "2016-07-05T00:00:00Z",
		    "end": "2016-07-06T00:00:00Z",
		    "isPaused": false
		  }
		}

	Tenga en cuenta lo siguiente:
 
	- En la sección de actividades, solo hay una actividad cuyo **type** está establecido en **Copy**.
	- La **entrada** de la actividad está establecida en **EmpOnPremSQLTable** y la **salida** de la actividad está establecida en **OutputBlobTable**.
	- En la sección de **transformación**, **SqlSource** está especificado como **tipo de origen** y **BlobSink** está especificado como** tipo de receptor**.
	- La consulta SQL **select * from emp** está especificada para la propiedad **sqlReaderQuery** de **SqlSource**.

	Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **end** es opcional, pero se utilizará en este tutorial.
	
	Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de la propiedad **end**.
	
	Está definiendo la duración del procesamiento de los segmentos de datos según las propiedades de **disponibilidad** que se definieron para cada tabla de la Factoría de datos de Azure.
	
	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
2. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos (la tabla es un conjunto de datos rectangular). Confirme que aparece un mensaje en la barra de título que indica **LA CANALIZACIÓN SE IMPLEMENTÓ CORRECTAMENTE**.
5. Ahora, cierre la hoja **Editor** haciendo clic en **X**. Vuelva a hacer clic en **X** para cerrar la hoja ADFTutorialDataFactory con la barra de herramientas y la vista de árbol. Si ve el mensaje que indica que **se descartarán las modificaciones no guardadas**, haga clic en **Aceptar**.
6. Debe volver a la hoja **FACTORÍA DE DATOS** para **ADFTutorialDataFactory**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

#### Visualización de la factoría de datos en una vista de diagrama 
1. En el **Portal de Azure**, haga clic en el icono **Diagrama** en la página principal de Factoría de datos **ADFTutorialOnPremDF**.

	![Vínculo de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Debería ver un diagrama similar al siguiente:

	![Vista de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	Puede acercar, alejar, acercar al 100%, ampliar para ajustar, colocar automáticamente canalizaciones y tablas, y mostrar información de linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados). Puede hacer doble clic en un objeto (tabla o canalización de entrada o salida) para ver sus propiedades.

## Supervisión de la canalización
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información sobre la supervisión, consulte [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md).

1. Vaya al **Portal de Azure** (si lo ha cerrado).
2. Si la hoja de **ADFTutorialOnPremDF** no está abierta, ábrala haciendo clic en **ADFTutorialOnPremDF** en el **Panel de inicio**.
3. Deben mostrarse el **recuento** y los **nombres** de las tablas y la canalización que creó en esta hoja.

	![Página principal de Factoría de datos](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Ahora haga clic en el icono **Conjuntos de datos**.
5. En la hoja **Conjuntos de datos**, haga clic en **EmpOnPremSQLTable**.

	![Segmentos EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Tenga en cuenta que ya se han producido los segmentos de datos hasta el momento actual y están **listos**. Esto ocurre porque ha insertado los datos en la base de datos de SQL Server y están ahí todo el tiempo. Confirme que no aparecen segmentos en la sección de **segmentos problemáticos** de la parte inferior.


	Las listas **Segmentos actualizados recientemente** y **Segmentos erróneos recientes** se ordenan por la **HORA DE LA ÚLTIMA ACTUALIZACIÓN**. En las situaciones siguientes, se cambia la hora de actualización de un segmento.
    

	-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureRmDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
	-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos. Haga clic en **Filtro** en la barra de herramientas para filtrar los segmentos.
	
	Para ver los intervalos de datos ordenados por las horas de inicio y finalización, haga clic en el icono **Segmentos de datos (por hora del segmento)**.

7. A continuación, en la hoja **Conjuntos de datos**, haga clic en **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Confirme que se han producido los segmentos hasta el momento actual y que están **listos**. Espere hasta que los estados de los segmentos hasta el momento actual estén establecidos en **Listo**.
9. Haga clic en cualquier segmento de datos de la lista. Debe aparecer la hoja **SEGMENTO DE DATOS**.

	![Hoja Segmento de datos](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.

10. Haga clic en la **ejecución de la actividad** de la lista en la parte inferior para ver los **detalles de la ejecución de la actividad**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Haga clic en **X** para cerrar todas las hojas hasta que
12. regrese a la hoja principal de **ADFTutorialOnPremDF**.
14. (Opcional) Haga clic en **Canalizaciones**, elija **ADFTutorialOnPremDF** y obtenga detalles de las tablas de entrada (**Consumido**) o las tablas de salida (**Producido**).
15. Use herramientas como el **Explorador de almacenamiento de Azure** para comprobar la salida.

	![Explorador de almacenamiento de Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## Pasos siguientes

- Consulte en el artículo [Data Management Gateway](data-factory-data-management-gateway.md) (Puerta de enlace de administración de datos) para ver todos los detalles.
- Consulte [Copia de datos de Almacenamiento de blobs en Base de datos SQL mediante Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) si necesita más información sobre el uso general de la actividad de copia para mover datos desde un almacén de datos de origen a un almacén de datos receptor.

<!---HONumber=AcomDC_0720_2016-->