<properties
	pageTitle="Adición de la acción HTTP + Swagger a las aplicaciones lógicas | Microsoft Azure"
	description="Información general de las operaciones y la acción HTTP + Swagger"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introducción a la acción HTTP + Swagger

Con la acción HTTP + Swagger puede crear un conector de primera categoría para cualquier punto de conexión REST a través de un [documento Swagger](https://swagger.io).

- Amplíe una aplicación lógica para llamar a cualquier punto de conexión REST con una experiencia de diseñador de primer nivel.

Para empezar a usar la acción HTTP + Swagger en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Uso de HTTP + Swagger como desencadenador o acción

Las funciones de HTTP + Swagger como desencadenador y acción son las mismas que en la [acción HTTP](connectors-native-http.md), solo que proporcionan una experiencia de diseño más eficaz, ya que muestra forma de la API y las salidas en el diseñador desde los [metadatos de Swagger](https://swagger.io). Además, puede utilizar HTTP + Swagger como desencadenador, y si quiere implementar un desencadenador de sondeo, debe seguir el patrón de sondeo que se [describe en este documento](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).
	
Obtenga [más información sobre las acciones y los desencadenadores de aplicaciones lógicas.](connectors-overview.md)

A continuación se muestra un ejemplo de cómo utilizar la operación HTTP + Swagger como una acción en un flujo de trabajo.

1. Haga clic en el botón **Nuevo paso**.
1. Elija **Agregar una acción**.
1. En el cuadro de búsqueda de acciones, escriba swagger para mostrar la acción HTTP + Swagger.

	![Acción de selección de HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)

1. Escriba la dirección URL de un documento Swagger.
	- La URL debe ser un punto de conexión HTTPS y debe tener habilitado CORS para poder utilizarse en el diseñador. Si el documento Swagger no cumple este requisito, puede usar el [Almacenamiento de Azure con CORS habilitado](#hosting-swagger-from-storage) para almacenar el documento.
1. Haga clic en Siguiente para realizar operaciones de lectura y procesamiento en el documento Swagger.
1. Agregue cualquier parámetro necesario para la llamada HTTP.

	![Completar acción HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Haga clic en Guardar en la esquina superior izquierda de la barra de herramientas; la aplicación lógica se guardará y se publicará (activará).

### Hospedaje de Swagger desde Almacenamiento de Azure

Es posible que quiera hacer referencia a un documento Swagger que no está hospedado o que no cumple los requisitos de seguridad y origen cruzado necesarios para utilizarlo en el diseñador. Como alternativa, puede almacenar el documento Swagger en el Almacenamiento de Azure y habilitar CORS hacer referencia al documento. Estos son los pasos necesarios para crear, configurar y almacenar Swagger en Almacenamiento de Azure:

1. [Cree una cuenta de almacenamiento de Azure con Almacenamiento de blobs](../storage/storage-create-storage-account.md): establezca los permisos en Acceso público.
1. Habilite CORS en el Blob de Azure.
	- Puede usar [este script de PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para configurar automáticamente esta opción.
1. Cargue el archivo de Swagger en el Blob de Azure.
	- Puede hacerlo desde el [Portal de Azure](https://portal.azure.com) o con una herramienta como el [Explorador de almacenamiento de Azure](http://storageexplorer.com/).
1. Haga referencia a un vínculo HTTPS en el documento del Blob de Azure (con el formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`).

---

## Detalles técnicos

A continuación se muestran los detalles de los desencadenadores y las acciones que admite este conector.

## Desencadenadores HTTP + Swagger

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores.](connectors-overview.md) El conector HTTP + Swagger tiene 1 desencadenador.

|Desencadenador|Descripción|
|---|---|
|HTTP + Swagger|Realizar una llamada HTTP y devolver el contenido de la respuesta.|

## Acciones HTTP + Swagger

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md) El conector HTTP + Swagger tiene 1 acción posible.

|Acción|Descripción|
|---|---|
|HTTP + Swagger|Realizar una llamada HTTP y devolver el contenido de la respuesta.|

### Detalles de la acción

El conector HTTP + Swagger incluye 1 acción posible. A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

#### HTTP + Swagger

Realice una solicitud HTTP saliente con ayuda de los metadatos de Swagger. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|Método*|estático|Verbo HTTP que se usará|
|URI*|uri|Identificador URI de la solicitud HTTP|
|Encabezados|encabezados|Objeto JSON de los encabezados HTTP que incluir|
|Cuerpo|body|Cuerpo de la solicitud HTTP|
|Autenticación|authentication|Autenticación que se utilizará para la solicitud ([consulte HTTP para obtener más información)](./connectors-native-http.md#authentication)|
<br>

**Detalles de salida**

Respuesta HTTP

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de respuesta|
|Cuerpo|objeto|Objeto de respuesta|
|Código de estado|int|Código de estado HTTP|

### Respuestas HTTP

Al realizar llamadas a diversas acciones, es posible que obtenga determinadas respuestas. A continuación se incluye una tabla que describe las respuestas y descripciones correspondientes.

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|

---

## Pasos siguientes

A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## Creación de una aplicación lógica

Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Puede explorar los demás conectores disponibles en aplicaciones consultando nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->