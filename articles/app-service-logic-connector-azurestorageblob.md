<properties 
   pageTitle="Conector de blobs de almacenamiento de Azure" 
   description="Introducción al conector de blobs de almacenamiento de Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
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
   
#Conector de blobs de almacenamiento de Azure

##Información general
El conector de blobs de almacenamiento de Azure permite cargar, descargar y eliminar blobs de un contenedor de blobs.

##Creación de un nuevo conector de blobs de almacenamiento de Azure
Para crear un nuevo conector de almacenamiento de Azure, siga los pasos que se mencionan a continuación.
<ul>
<li>Iniciar el portal de Azure <li> Abra Azure Marketplace con +Nuevo (en la parte inferior de la página) -> Web+móvil--> Azure Marketplace.
</ul>

![Inicio de Azure Marketplace][1]<br>
<ul>
<li>Haga clic en Aplicaciones de API
<li>Busque <i>Blob</i> y seleccione el conector de blobs de almacenamiento de Azure
</ul>

![Selección del conector del blobs de almacenamiento de Azure][2]
<br>
<ul>
<li>Haga clic en Crear <li>En la hoja del conector de blobs de almacenamiento de Azure que se abre, proporcione los siguientes datos.
</ul>

![Creación del conector del blobs de almacenamiento de Azure][3]

- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
- **Suscripción**: elija una suscripción en la que desee crear este conector.
- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
- **Nivel de precios**: elija un nivel de precios para el conector.
- **Nombre**: asigne un nombre al conector de almacenamiento de blobs.
- **Configuración del paquete** 
	- **URI de contenedor/SAS**: especifique el URI del contenedor de blobs. El URI también puede incluir también el token SAS. Por ejemplo http://storageaccountname.blob.core.windows.net/containername, o http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah.
	- **Clave de acceso**: especifique una clave de acceso de la cuenta de almacenamiento principal o secundaria. Deje este campo vacío si se utiliza un token SAS para autenticación.
- Haga clic en Crear. Se creará un nuevo conector de blobs de almacenamiento de Azure.

##Uso del conector de blobs de almacenamiento de Azure en la aplicación lógica
Cuando se haya creado el conector de blobs de almacenamiento de Azure, se puede consumir desde el flujo.

Cree un nuevo flujo a través de +Nuevo -> Web+Móvil -> LogicApp. Proporcione los metadatos para el flujo incluyendo el grupo de recursos.

![Creación de la aplicación lógica][4]

Haga clic en *Desencadenadores y acciones*. Se abrirá el diseñador de flujos.

![Diseñador de flujo vacío de la aplicación lógica][5]

El conector de blobs de almacenamiento de Azure puede usarse como acción.

###Acciones
En el panel derecho, haga clic en el conector de blobs de almacenamiento de Azure. El conector enumera las acciones admitidas.

![Lista de acciones de blobs de almacenamiento de Azure][10]

El conector de blobs de almacenamiento de Azure admite las siguientes siguientes:

- **Obtener blob**: obtenga un blob específico del contenedor.
- **Cargar blob**: cargue un blob nuevo o actualice uno existente.
- **Eliminar blob**: elimine un blob específico de un contenedor.
- **Enumerar blobs**: muestre todos los blobs de un directorio.
- **Blob de instantánea**: cree una instantánea de solo lectura de un blob específico.
- **Copiar blob**: cree un blob nuevo copiándolo desde otro blob. El blob de origen puede estar en la misma cuenta o en otra cuenta.

Veamos un ejemplo: cargar un blob. Haga clic en Cargar blob

![Entradas de acción Cargar blob][11]


- **Ruta de acceso del blob**: especifique la ruta de acceso del blob que se va a cargar. La ruta de acceso se interpreta en relación con la ruta de acceso del contenedor configurado.
- **Contenido de escritura del blob**: especifique el contenido y las propiedades del blob que se van a cargar.
- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
- **Sobrescribir**: si se establece en true, el blob existente se sobrescribirá. De lo contrario, devolverá un error si ya existe un blob en la misma ruta de acceso.

Proporcione las entradas y haga clic en la marca de graduación para completar la configuración de la entrada.


Tenga en cuenta que la acción Cargar blob del blob de almacenamiento de Azure configurada muestra ambos parámetros de entrada, así como parámetros de salida.

####Uso de las salidas de las acciones anteriores como entrada para las acciones de blobs de almacenamiento de Azure
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

<!---HONumber=54-->