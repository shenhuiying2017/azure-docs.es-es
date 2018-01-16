---
title: "Exploración de las API REST en Fiddler o Postman (REST de Azure Search) | Microsoft Docs"
description: "Uso de Fiddler o Postman para generar solicitudes emisión HTTP y llamadas a la API de REST en Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/04/2018
ms.author: heidist
ms.openlocfilehash: a9a6bc035ba9f02057e3086a9682b54282da19f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Exploración de las API de REST de Azure Search mediante Fiddler o Postman

Una de las formas más sencillas de explorar la [API de REST de Azure Search](https://docs.microsoft.com/rest/api/searchservice) es usar Fiddler o Postman para formular solicitudes HTTP e inspeccionar las respuestas. Con las herramientas adecuadas y estas instrucciones, puede enviar solicitudes y ver las respuestas antes de escribir ningún código.

> [!div class="checklist"]
> * Descarga de una herramienta de prueba de API web
> * Obtención de la clave de api y el punto de conexión del servicio de búsqueda
> * Configuración de encabezados de solicitud
> * Creación de un índice
> * Carga de un índice
> * Búsqueda de un índice

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar y luego [regístrese en Azure Search](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Descarga e instalación de herramientas.

Las siguientes herramientas se utilizan profusamente en el desarrollo web, pero aunque use otra, debe seguir las instrucciones de este artículo.

+ [Postman (complemento de Google Chrome)](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Obtención de la clave de api y el punto de conexión

Las llamadas de REST requieren la dirección URL del servicio y una clave de acceso en cada solicitud. Con ambos se crea un servicio de búsqueda, por lo que si ha agregado Azure Search a su suscripción, siga estos pasos para obtener la información necesaria:

1. En Azure Portal, abra la página del servicio de búsqueda desde el panel o [busque el servicio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) en la lista.
2. Obtenga el punto de conexión en **Información general** > **Essentials** > **Dirección URL**. Un punto de conexión de ejemplo podría ser similar a `https://my-service-name.search.windows.net`.
3. Obtenga la clave de api en **Configuración** > **Claves**. Hay dos claves de administración para conseguir redundancia, por si desea sustituirlas. Las claves de administración conceden los permisos de escritura al servicio, lo que es necesario para crear y cargar índices. En las operaciones de escritura se pueden usar las claves primaria o secundaria.

## <a name="configure-request-headers"></a>Configuración de encabezados de solicitud

Cada herramienta conserva la información de encabezado de solicitud durante la sesión, lo que significa que el punto de conexión de la dirección URL, api-version, api-key y content-type solo hay que especificarlos una vez.

La dirección URL completa debe ser similar a la siguiente, pero la suya es la única que debería tener un reemplazo válido para el **`my-app`** nombre del marcador de posición: `https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01`

La composición de la dirección URL del servicio incluye los siguientes elementos:

+ Prefijo HTTPS.
+ Dirección URL del servicio, obtenida del portal.
+ Recurso, una operación que crea un objeto en el servicio. En este paso, es un índice denominado hotels.
+ api-version, una cadena en minúscula necesaria especificada como "? api-version = 2016-09-01" para la versión actual. Las [versiones de la API](search-api-versions.md) se actualizan periódicamente. La inclusión de api-version en todas las solicitudes proporciona un control total sobre la que se usa.  

La composición de los encabezado de solicitud incluye dos elementos, el tipo de contenido y la clave de api que se ha descrito en la sección anterior:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Formule una solicitud similar a la de la siguiente captura de pantalla. Elija **PUT** como verbo. Fiddler agrega `User-Agent=Fiddler`. Los dos encabezados de solicitud adicionales se pueden pegar en las líneas nuevas que hay debajo. Incluya el tipo de contenido y la clave de api del servicio, para lo que debe usar la clave de acceso de administrador del servicio.

![Encabezado de la solicitud de Fiddler][1]

> [!Tip]
> Puede desactivar el tráfico web para ocultar la actividad HTTP extraña no relacionada con las tareas que va a realizar. En Fiddler, vaya al menú **File** (Archivo) y desactive **Capture Traffic** (Capturar tráfico). 

### <a name="postman"></a>postman

Formule una solicitud similar a la de la siguiente captura de pantalla. Elija **PUT** como verbo. 

![Encabezado de solicitud de Postman][6]

## <a name="create-the-index"></a>creación del índice

El cuerpo de la solicitud contiene la definición del índice. La incorporación del cuerpo completa la solicitud que genera el índice.

Además del nombre del índice el componente más importante de la solicitud es la colección de campos. La colección de campos define el esquema de índice. En cada campo, especifique su tipo. Los campos de cadena se utilizan en la búsqueda de texto completo, por lo que puede convertir los datos numéricos en cadenas si necesita que se puedan realizar búsquedas en dicho contenido.

Los atributos del campo determinan la acción que se permite. Las API de REST permiten muchas acciones de forma predeterminada. Por ejemplo, de manera predeterminada es posible realizar búsquedas en todas las cadenas, se pueden recuperar, crear filtros y se pueden clasificar. A menudo, basta con establecer los atributos cuando haya que desactivar un comportamiento. Para más información acerca de los atributos, consulte [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) Creación de un índice (API de REST del servicio Azure Search).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }


Al enviar esta solicitud, debería aparece una respuesta HTTP 201, que indica que el índice se creó correctamente. Esta acción se puede comprobar esta acción en el portal, pero tenga en cuenta que la página del portal se actualiza periódicamente, por lo que puede tardar un minuto o dos en ponerse al día.

Si obtiene HTTP 504, compruebe que la URL especifique HTTPS. Si se muestra el error HTTP 400 o 404, compruebe el cuerpo de la solicitud para verificar que no haya errores al copiar/pegar. Un HTTP 403 indica normalmente que hay un problema con la clave de API (es una clave no válida o un problema de sintaxis sobre cómo se específica la clave de API).

### <a name="fiddler"></a>Fiddler

Copie la definición del índice en el cuerpo de la solicitud, tal como aparece en la siguiente captura de pantalla y , después, haga clic en **Execute** (Ejecutar) en la parte superior derecha para enviar la solicitud completada.

![Cuerpo de la solicitud de Fiddler][7]

### <a name="postman"></a>postman

Copie la definición del índice en el cuerpo de la solicitud, tal como aparece en la siguiente captura de pantalla, y haga clic en **Execute** (Ejecutar) en la parte superior derecha para enviar la solicitud completada.

![Cuerpo de solicitud de Postman][8]

## <a name="load-documents"></a>Carga de documentos

La creación del índice y su rellenado son pasos independientes. En Azure Search, el índice contiene todos los datos en que se pueden realizar búsquedas, y que puede proporcionar como documentos JSON. Para examinar la API de esta operación, consulte [Add, update o delete documentos (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) [Adición, actualización o eliminación de documentos (REST)].

+ Cambie el verbo a **POST** en este paso.
+ Cambie el punto de conexión para que incluya `/docs/index`. La dirección URL completa debe ser como la siguiente `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ Mantenga los encabezados de solicitud tal cual. 

El cuerpo de la solicitud contiene cuatro documentos que se van agregar al índice de hoteles.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

En unos pocos segundos debe ver una respuesta HTTP 200 en la lista de sesiones. Esto indica que los documentos se crearon correctamente. 

Si obtiene un 207, al menos un documento no pudo cargarse. Si aparece el error 404, significa que se ha producido un error de sintaxis en el encabezado o en el cuerpo de la solicitud: compruebe que ha cambiado el punto de conexión para que incluya `/docs/index`.

> [!Tip]
> Para los orígenes de datos seleccionados, puede elegir el enfoque de *indizador* alternativo, que simplifica y reduce la cantidad de código necesario para la indexación. Para más información, consulte [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)(Operaciones del indexador).

### <a name="fiddler"></a>Fiddler

Cambie el verbo a **POST**. Cambie la dirección URL para que incluya `/docs/index`. Copie los documentos en el cuerpo de la solicitud, tal como se muestra en la siguiente captura de pantalla y ejecute la solicitud.

![Carga de la solicitud de Fiddler][9]

### <a name="postman"></a>postman

Cambie el verbo a **POST**. Cambie la dirección URL para que incluya `/docs/index`. Copie los documentos en el cuerpo de la solicitud, tal como se muestra en la siguiente captura de pantalla y ejecute la solicitud.

![Carga de la solicitud de Postman][10]

## <a name="query-the-index"></a>Consultas al índice
Ahora que se han cargado el índice y los documentos, puede emitir consultas con ellos. Para más información acerca de esta API, consulte [Search Documents (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) [Búsqueda de documentos (REST)]  

+ Cambie el verbo a **GET** en este paso.
+ Cambiar el punto de conexión para incluir parámetros de consulta, incluidas las cadenas de búsqueda. Una dirección URL de consulta podría ser como esta: `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ Mantenga los encabezados de solicitud tal cual

Esta consulta busca el término "motel" y devuelve el número de documentos en los resultados de la búsqueda. La solicitud y respuesta deben ser similares a los de la siguiente captura de pantalla de Postman tras hacer clic en **Send** (Enviar). El código de estado debe ser 200.

 ![Respuesta a la consulta de Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Sugerencias para ejecutar consultas de ejemplo en Fiddler

La siguiente consulta de ejemplo proviene del artículo [Operación de índice de búsqueda (API de Azure Search)](http://msdn.microsoft.com/library/dn798927.aspx). Muchas de las consultas de ejemplo de este artículo incluyen espacios, algo que no está permitido en Fiddler. Reemplace cada espacio por un carácter + antes de pegar la cadena de la consulta e intentar la consulta en Fiddler:

**Los espacios anteriores se sustituyen (en lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**Los espacios posteriores se sustituyen por + (en lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>Propiedades del índice de consulta
También puede consultar la información del sistema para obtener recuentos de documentos y consumo de almacenamiento:`https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

En Postman, la solicitud debe ser similar al siguiente y la respuesta incluye el número de documentos y el espacio utilizado, en bytes.

 ![Consulta del sistema de Postman][12]

Observe que la sintaxis de api-version es distinta. En esta solicitud, utilice `?` para anexar api-version. El signo ? separa la ruta de acceso de la dirección URL de la cadena de consulta, mientras que & separa cada par " nombre = valor " par en la cadena de consulta. En esta consulta, api-version es el primer y único elemento de la cadena de consulta.

Para más información acerca de esta API, consulte [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) [Obtención de estadísticas de índice (REST)].


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Sugerencias para ver estadísticas de índices en Fiddler

En Fiddler, haga clic en la pestaña **Inspectors** (Inspectores), luego en **Headers** (Encabezados) y, finalmente, seleccione el formato JSON. Debe ver el recuento de documentos y el tamaño del almacenamiento (en KB).

## <a name="next-steps"></a>pasos siguientes

Los clientes de REST son muy valiosos para la exploración improvisada, pero ahora que sabe cómo funcionan las API de REST, puede avanzar en el código. Para conocer los pasos siguientes, consulte los siguientes vínculos:

+ [Create an Azure Search index using the REST API](search-create-index-rest-api.md) (Creación de un índice de Azure Search con la API de REST)
+ [Upload data to Azure Search using the REST API](search-import-data-rest-api.md) (Carga de datos en Azure Search con la API de REST)
+ [Query your Azure Search index using the REST API](search-query-rest-api.md) (Realización de una consulta al índice de Búsqueda de Azure con la API de REST)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png