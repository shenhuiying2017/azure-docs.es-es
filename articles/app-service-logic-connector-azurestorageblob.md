<properties 
   pageTitle="Conector de blobs de almacenamiento de Azure" 
   description="Introducción al conector de blobs de almacenamiento de Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
# Conector de blobs de almacenamiento de Azure

## Información general
El conector de blobs de almacenamiento de Azure permite cargar, descargar y eliminar blobs de un contenedor de blobs.

## Creación de un nuevo conector de blobs de almacenamiento de Azure
Para crear un nuevo conector de almacenamiento de Azure, siga los pasos que se mencionan a continuación.
- Inicie el Portal de Azure.
- Abra Azure Marketplace con +Nuevo (en la parte inferior de la página) -> Web+móvil--> Azure Marketplace.

![Launch Azure Marketplace][1]
- Haga clic en Aplicaciones de la API.
- Busque _Blob_ y seleccione el conector de blobs de almacenamiento de Azure.

![Select Azure Storage Blob Connector][2]
- Haga clic en Crear.
- En la hoja del conector de blobs de almacenamiento de Azure que se abre, proporcione los siguientes datos.

![Create Azure Storage Blob Connector][3]

- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
- **Suscripción**: elija una suscripción en la que desee crear este conector.
- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
- **Nivel de precios**: elija un nivel de precios para el conector.
- **Nombre**: asigne un nombre al conector de FTP.
- **Configuración del paquete** 
	- **URI de contenedor/SAS**: especifique el URI del contenedor de blobs. El URI puede incluir también el SAS token. Ejemplo http://storageaccountname.blob.core.windows.net/containername o http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Clave de acceso**: especifique una clave de acceso de la cuenta de almacenamiento principal o secundaria. Deje este campo vacío si se utiliza un token SAS para autenticación.
- Haga clic en Crear. Se creará un nuevo conector de blobs de almacenamiento de Azure.

## Uso del conector de blobs de almacenamiento de Azure en la aplicación lógica
Cuando se haya creado el conector de blobs de almacenamiento de Azure, se puede consumir desde el flujo.

Cree un nuevo flujo a través de +Nuevo -> Web+Móvil -> LogicApp. Proporcione los metadatos para el flujo incluyendo el grupo de recursos.

![Create Logic App][4]

Haga clic en  *triggers and actions*. Se abrirá el diseñador de flujos.

![Logic App empty flow designer][5]

El conector de blobs de almacenamiento de Azure puede usarse como desencadenador y acción. 

### Desencadenador
En el diseñador de flujo vacío, haga clic en el conector de blobs de almacenamiento de Azure en el panel derecho de la galería.

![Choose Blob Available Trigger][6]

El conector de blobs de almacenamiento de Azure tiene un desencadenador: _BlobAvailable_. Este desencadenador sondea los blobs presentes en un contenedor dado. También se admite el sondeo de nivel de directorio y el filtrado de blobs. Elimina los blobs del contenedor después de haberlos recogido.

Haga clic en el desencadenador _BlobAvailable_.

![Basic inputs Blob Available Trigger][7]

Las entradas le ayudarán a configurar una ruta de acceso a una carpeta concreta que se sondeará en una frecuencia programada. Las entradas básicas son las siguientes:
- Frecuencia: especifica la frecuencia del sondeo de FTP.
- Intervalo: especifica el intervalo de la frecuencia programada.
- Ruta de acceso de la carpeta: especifica una ruta de acceso de la carpeta virtual que se va a sondear. Utilice '.' en la carpeta del contenedor raíz.
- Tipo de archivo: especifica una máscara de archivo para que coincida con los nombres de archivo de blobs.  Solo los blobs con nombres de archivo que coincidan con esta máscara de archivo se incluirán en el sondeo. De manera predeterminada, se incluirán todos los blobs.

Al hacer clic en ..., se mostrarán las entradas avanzadas. 

![Advanced inputs Blob Available Trigger][8]

Las entradas avanzadas son las siguientes:

- Máscara de archivo: especifica una máscara de archivo para que coincida con los nombres de archivo de blobs.  Solo los blobs con nombres de archivo que coincidan con esta máscara de archivo se incluirán en el sondeo. De manera predeterminada, se incluirán todos los blobs.
- Excluir máscara de archivo: especifica una máscara de archivo para que coincida con los nombres de archivo de blobs.  Se excluirán los blobs que coincidan con la máscara de archivo. De manera predeterminada, no se excluye ningún blob.

Proporcione las entradas y haga clic en la marca de graduación para completar la configuración de la entrada.

![Configured Blob Available Trigger][9]

Tenga en cuenta que el desencadenador de _Blob Available_ configurado muestra ambos parámetros configurados, así como los parámetros de salida. 

Una vez creada la aplicación lógica, el desencadenador _Blob Available_ 


- Realiza un sondeo de la ruta de acceso de la carpeta para los archivos nuevos.
- Crea una instancia del flujo de lógica para cada archivo nuevo.
- Elimina el archivo de la ruta de acceso de la carpeta después de que se ha creado una instancia del flujo de lógica.

#### Uso de los resultados de las acciones anteriores como entrada para la acción de FTP
La salida del desencadenador _Blob Available_ puede utilizarse como entrada de otras acciones del flujo. 

Puede hacer clic en + en el cuadro de diálogo de entrada de la acción y seleccionar la salida de FTP directamente en el cuadro desplegable.

También puede escribir una expresión directamente en el cuadro de entrada de la acción. A continuación se indica la expresión de flujo para hacer referencia a la salida del desencadenador

	@triggers().outputs.body.Content

### Acciones
En el panel derecho, haga clic en el conector de blobs de almacenamiento de Azure. El conector enumera las acciones admitidas.

![List of Azure Storage Blob Actions][10]

El conector de blobs de almacenamiento de Azure admite las siguientes cuatro acciones:

- **Obtener blob**: obtenga un blob específico del contenedor.
- **Cargar blob**: cargue un blob nuevo o actualice uno existente.
- **Eliminar blob**: elimine un blob específico de un contenedor.
- **Enumerar blobs**: muestre todos los blobs de un directorio.
- **Blob de instantánea**: cree una instantánea de solo lectura de un blob específico.
- **Copiar blob**: cree un blob nuevo copiándolo desde otro blob.  El blob de origen puede estar en la misma cuenta o en otra cuenta.

Veamos un ejemplo: cargar un blob. Haga clic en Cargar blob

![Inputs of Upload Blob action][11]


- **Ruta de acceso del blob**: especifique la ruta de acceso del blob que se va a cargar.  La ruta de acceso se interpreta en relación con la ruta de acceso del contenedor configurado.
- **Contenido de escritura del blob**: especifique el contenido y las propiedades del blob que se van a cargar.
- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
- **Sobrescribir**: si se establece en true, el blob existente se sobrescribirá. De lo contrario, devolverá un error si ya existe un blob en la misma ruta de acceso.

Proporcione las entradas y haga clic en la marca de graduación para completar la configuración de la entrada.


Tenga en cuenta que la acción Cargar blob del blob de almacenamiento de Azure configurada muestra ambos parámetros de entrada, así como parámetros de salida.

#### Uso de los resultados de las acciones anteriores como entrada para la acción de FTP
Tenga en cuenta que en la captura de pantalla configurada, el valor de Contenido se establece en una expresión.

	@triggers().outputs.body.Content


Puede establecerlo en cualquier valor que desee. Esto es solo un ejemplo. La expresión toma el resultado del desencadenador de aplicación lógica y lo utiliza como el contenido del archivo que se va a cargar. Supongamos que desea utilizar la salida de una acción anterior, por ejemplo, transform (transformar). En ese caso, la expresión será:

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG

<!--HONumber=49-->