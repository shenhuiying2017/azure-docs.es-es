<properties 
	pageTitle="Introducción a Búsqueda de Azure" 
	description="Introducción a Búsqueda de Azure" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Introducción a Búsqueda de Azure

[AZURE.INCLUDE [En este artículo se usa el Portal de vista previa de Azure](../includes/preview-portal-note.md)]

Búsqueda de Microsoft Azure es un nuevo servicio que le permite incrustar funcionalidad de búsqueda en aplicaciones personalizadas. Proporciona un motor de búsqueda y almacenamiento para sus datos, que administra, y a los cuales tiene acceso, mediante un SDK de .NET o una API de REST. Para obtener más información sobre las razones de usar Búsqueda de Azure, consulte [Supuestos y funciones de Búsqueda de Azure](http://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/).  

Como administrador, puede agregar un servicio de búsqueda a una suscripción existente sin coste alguno al seleccionar el servicio compartido o a un precio reducido cuando se opta por los recursos dedicados. Este artículo contiene las siguientes secciones:

<a id="sub-1"></a>
## Comienzo con el servicio gratuito

Los suscriptores obtienen acceso gratuito automáticamente a un servicio de búsqueda compartido multiempresa que puede usar para fines de aprendizaje, pruebas de prueba de concepto o pequeños proyectos de búsqueda de desarrollo. Regístrese para usar la versión gratuita con los siguientes pasos.

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com) con su suscripción actual. Tenga en cuenta que esta URL lo lleva al portal de vista previa. Es indispensable el uso del portal de vista previa. 

2. Haga clic en **New** en la parte inferior de la página.
 
  	![][6]

3. Haga clic en **Todo** en la parte superior de la página. 

  	![][7]

4. En la galería, haga clic en **Datos + análisis**.
 
  	![][8]

5. En los servicios de datos, haga clic en **Búsqueda**.
 
  	![][10]

7. En la parte inferior de la página, haga clic en **CREAR**.

8. Escriba un nombre de servicio en minúscula para usarlo en la URL de servicio, evite espacios y respete el límite de cadena de 15 caracteres.
 
  	![][11]

9. Haga clic en la flecha **Nivel de precios** para seleccionar una opción de precios. Seleccione **GRATIS** y, luego, haga clic en **SELECCIONAR** en la parte inferior de la página. La versión gratuita ofrece capacidad suficiente para probar tutoriales y crear un código de prueba de concepto, pero no está destinada a las aplicaciones de producción. 

  	![][12]

10. Haga clic en la flecha en **Grupo de recursos** para elegir un grupo existente o crear uno nuevo. Los grupos de recursos son contenedores para servicios y recursos que se usan para un objetivo en común. Por ejemplo, si va a compilar una aplicación de búsqueda personalizada basada en Búsqueda de Azure, Sitios web Azure, almacenamiento de blobs de Azure, puede crear un grupo de recursos que mantenga estos servicios juntos en las páginas de administración del portal.

11. Haga clic en la flecha en **Suscripción** si tiene varias suscripciones y quiere usar una suscripción diferente para este servicio de búsqueda.

12. Haga clic en la flecha en **Ubicación** para seleccionar una región del centro de datos. En esta vista previa, puede seleccionar entre Oeste de EE. UU., Este de EE. UU., Europa del Norte y Sudeste asiático. Posteriormente, cuando otras regiones estén conectadas, seleccione una región para el servicio que va a crear. La distribución de recursos en varios centros de datos no será una configuración compatible para una vista previa pública.

13. Haga clic en **CREAR** para realizar el aprovisionamiento del servicio. Tenga en cuenta que **CREAR** se habilita solo después de llenar todos los valores requeridos. 

El servicio se crea en unos pocos minutos. Puede volver a los ajustes de configuración para obtener la URL o las claves de API. Las conexiones con el servicio de búsqueda requieren que tenga la URL y una clave de API para autenticar la llamada. A continuación se presenta como buscar estos valores rápidamente:

14. Vaya a **Inicio** para abrir el panel. Haga clic en el servicio de búsqueda para abrir el panel del servicio. 

  	![][13]

15.	En el panel de servicio, verá iconos para **PROPIEDADES** y **CLAVES**, e información de uso que muestra el uso de recursos de un vistazo. 

  	![][23]

   **PROPIEDADES** contiene la URL del servicio. 

   **CLAVES** contiene las claves de API que se usan para autenticación.
 
   **USO** muestra el recuento de documentos, los recursos disponibles y los límites de almacenamiento.

Ir a [Prueba de operaciones del servicio](#sub-3) para obtener instrucciones sobre cómo conectarse al servicio con estos valores.

<a id="sub-2"></a>
## Actualización a una búsqueda estándar

La búsqueda estándar obtiene recursos dedicados en un centro de datos de Azure que solo usted puede usar. Las cargas de trabajo de búsqueda requieren réplicas de almacenamiento y servicio. Al registrarse para una búsqueda estándar, puede optimizar la configuración del servicio para usar más de cualquier recurso que sea el más importante para su escenario.

Tener recursos dedicados le proporcionará una mayor escalación y un mejor rendimiento, pero no características adicionales. La búsqueda compartida y estándar ofrecen las mismas características.

Para usar la búsqueda estándar, cree un nuevo servicio de búsqueda seleccionando el nivel de precios estándar. Tenga en cuenta que la actualización no es una actualización local de la versión gratuita. Cambiar al modo estándar, con su potencial de escalación, requiere un servicio nuevo. Tendrá que volver a cargar los índices y documentos usados por su aplicación de búsqueda.

La configuración de recursos dedicados puede tardar unos minutos (15 minutos o más). 

**Paso 1: Creación de un servicio nuevo con un nivel de precios estándar**

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com) con su suscripción actual. 

2. Haga clic en **New** en la parte inferior de la página.

3. Haga clic en **Todo** en la parte superior de la página.

4. En la galería, haga clic en **Datos + análisis**.

6. En los servicios de datos, haga clic en **Búsqueda**.

7. En la parte inferior de la página, haga clic en **CREAR**.

8. Escriba un nombre de servicio en minúscula para usarlo en la URL de servicio, evite usar guiones y espacios, y respete el límite de cadena de 15 caracteres.

9. Haga clic en la flecha **Nivel de precios** para seleccionar una opción de precios. Seleccione **ESTÁNDAR** y, luego, haga clic en **SELECCIONAR** en la parte inferior de la página.

 ![][14]

**Paso 2: Ajuste de las unidades de búsqueda basadas en requisitos de escalación**

La búsqueda estándar comienza con una réplica y una partición, pero se puede volver a escalar fácilmente a niveles de recursos más altos.

1.	Después de que se haya creado el servicio, vuelva al panel del servicio, haga clic en el icono de **escalación**.

2.	Use los controles deslizantes para agregar réplicas, particiones o ambos. 

Las réplicas y las particiones adicionales se cobran en unidades de búsqueda. El total de unidades de búsqueda necesario para admitir una configuración de recursos en particular se muestra en la página, a medida que agrega recursos. 

Puede consultar [Detalles de precios](http://go.microsoft.com/fwlink/p/?LinkID=509792) para obtener la información de facturación por unidad. Consulte [Límites y restricciones](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obtener ayuda para decidir cómo configurar las combinaciones de partición y de réplica.

 ![][15]

<a id="sub-3"></a>
## Prueba de operaciones del servicio

La confirmación de que su servicio está operativo y que puede obtenerse acceso a él desde una aplicación cliente es el paso final en la configuración de la búsqueda. Este procedimiento usa Fiddler, disponible como una [descarga gratuita en Telerik](http://www.telerik.com/fiddler), para emitir solicitudes HTTP y ver respuestas. Al usar Fiddler, puede probar la API inmediatamente, sin tener que crear ningún código. 

El siguiente procedimiento funciona para la búsqueda compartida y estándar. En los siguientes pasos, podrá crear un índice, cargar documentos, consultar el índice y, a continuación, consultar el sistema en busca de información de servicio.

### Creación de un índice

1. Inicie Fiddler. En el menú Archivo, desactive **Capturar tráfico** para ocultar actividad HTTP irrelevante que no está relacionada con la tarea actual. En la pestaña Compositor, formulará una solicitud que se ve similar a lo siguiente: 

  	![][16]

2. Seleccione **PUT**.

3. Escriba una URL que especifica la URL de servicio (que puede encontrar en la página Propiedades), solicite los atributos y la versión de API. Algunos indicadores que tener en cuenta:
   + Use HTTPS como el prefijo
   + El atributo de solicitud es "/índices/hoteles". Esto le indica a la búsqueda que cree un índice llamado  'hotels'.
   + La versión de API es en minúsculas y se específica como "?api-version=2014-07-31-preview". Las versiones de API son importantes porque Búsqueda de Azure implementa actualizaciones regularmente. En raras ocasiones, una actualización de servicio puede presentar un cambio innovador en la API. Al usar las versiones de API, puede seguir usando la versión actual y actualizar a una más nueva cuando sea conveniente.

    La URL completa debe ser similar al siguiente ejemplo:

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

4.	Especifique el encabezado de la solicitud al reemplazar el host y la clave de API por valores que son válidos para su servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	En el cuerpo de la solicitud, pegue los campos que componen la definición del índice.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ] 
        }

6.	Haga clic en **Ejecutar**.

En algunos segundos, debe ver una respuesta HTTP 201 en la lista de sesiones, que indica que el índice se creó correctamente. 

Si obtiene HTTP 504, compruebe que la URL especifique HTTPS. Si se muestra el error HTTP 400 o 404, compruebe el cuerpo de la solicitud para verificar que no haya errores al copiar/pegar. Un HTTP 403 indica normalmente que hay un problema con la clave de API (es una clave no válida o un problema de sintaxis sobre cómo se específica la clave de API).

### Carga de documentos

En la pestaña Compositor, se verá su solicitud para enviar documentos como a continuación. El cuerpo de la solicitud contiene los datos de búsqueda de cuatro hoteles.

   ![][17]

1. Seleccione **POST**.

2.	Escriba una URL que comience con HTTPS, seguida de su URL de servicio, seguida de "/índices/<'indexname'>/docs/index?api-version=2014-07-31-preview". La URL completa debe ser similar al siguiente ejemplo:

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.	El encabezado de la solicitud debe ser igual que antes. Recuerde que reemplazó el host y la clave de API con valores que son válidos para el servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	El cuerpo de la solicitud contiene cuatro documentos que se van agregar al índice de hoteles.

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

8.	Haga clic en **Ejecutar**.

En unos pocos segundos debe ver una respuesta HTTP 200 en la lista de sesiones. Esto indica que los documentos se crearon correctamente. Si obtiene un 207, al menos un documento no pudo cargarse. Si obtiene un error 404, se ha producido un error de sintaxis en el encabezado o en el cuerpo de la solicitud.

### Consultas al índice

Ahora que se han cargado el índice y los documentos, puede emitir consultas con ellos.  En la pestaña Compositor, el comando GET que consulta su servicio tendrá la siguiente apariencia:

   ![][18]

1.	Seleccione **GET**.

2.	Escriba una URL que comience por HTTPS, seguida de su URL de servicio, "/índices/<'indexname'>/docs?", y después los parámetros de la consulta. A modo de ejemplo, use la siguiente URL, que reemplaza el nombre del host de ejemplo por uno que es válido para su servicio.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    Esta consulta busca el término "motel" y recupera las categorías de faceta para las calificaciones.

3.	El encabezado de la solicitud debe ser igual que antes. Recuerde que reemplazó el host y la clave de API con valores que son válidos para el servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

El código de respuesta debe ser 200 y el resultado de la respuesta debe ser similar a la siguiente ilustración.
 
   ![][19]

La siguiente consulta de ejemplo proviene de la [operación de índice de búsqueda (API de Búsqueda de Azure)](http://msdn.microsoft.com/library/dn798927.aspx) en MSDN. Muchas de las consultas de ejemplo en este tema incluyen espacios, que no están permitidos en Fiddler. Reemplace cada espacio por un carácter + antes de pegar la cadena de la consulta e intentar la consulta en Fiddler: 

**Los espacios anteriores se reemplazan:*

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**Los espacios posteriores se reemplazan por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### Consultas al sistema

También puede consultar al sistema para obtener recuentos de documentos y consumo de almacenamiento. En la pestaña Compositor, su solicitud tendrá la siguiente apariencia y la respuesta devolverá un recuento de la cantidad de documentos y el espacio usado.

   ![][20]

1.	Seleccione **GET**.

2.	Escriba una URL que incluya la URL de su servicio, seguida de "/índices/hoteles/stats?api-version=2014-07-31-Preview":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.	Especifique el encabezado de la solicitud al reemplazar el host y la clave de API por valores que son válidos para su servicio.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Deje vacío el cuerpo de la solicitud.

5.	Haga clic en **Ejecutar**. Debe ver un código de estado HTTP 200 en la lista de sesiones. Seleccione la entrada enviada para su comando.

6.	Haga clic en la pestaña Inspectores | Encabezados, y seleccione el formato JSON. Debe ver el recuento de documentos y el tamaño del almacenamiento (en KB).

 	![][21]

<a id="sub-4"></a>
## Exploración del panel del servicio de búsqueda

Si necesita un actualizador sobre dónde buscar las páginas de configuración, siga los siguientes pasos para ubicar el panel de servicio.

1.	Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com) con su suscripción actual. 
2.	Haga clic en **Inicio** y, a continuación, haga clic en el icono del servicio de búsqueda.

 	![][22]

4.	Se abrirá el panel de servicio. Tenga en cuenta que los comandos de **inicio**, **detención** y **eliminación** están en la parte superior. El panel de servicio incluye iconos para ver propiedades, claves y un inicio rápido con vínculos a la información y las instrucciones. Desplácese hacia abajo para ver el uso.

5.	Haga clic en **PROPIEDADES**. Tenga en cuenta que la página de propiedades se abre a la derecha. La URL de servicio se encuentra en la parte superior de la página. Necesitará esta dirección URL para conectarse a su servicio de búsqueda de Azure.

 	![][23]
	
7.	Haga clic en **CLAVES** para ver las claves de la API. Necesitará una clave de administración para autenticar el servicio. También puede utilizar el servidor principal o secundario. Si lo desea, puede crear claves de consulta para obtener acceso de solo lectura al servicio.


<!--Next steps and links -->
<a id="next-steps"></a>
## Prueba

¿Está listo para el siguiente paso? Los siguientes vínculos lo llevan a material adicional que muestra cómo compilar y administrar aplicaciones de búsqueda que usan Búsqueda de Azure.

- [Creación de la primera solución de búsqueda de Azure](search-create-first-solution.md) 

- [Creación de un ejemplo de búsqueda geográfica con Búsqueda de Azure](search-create-geospatial.md)

- [Administración de la solución de búsqueda en Microsoft Azure](search-manage.md) 

- [Información técnica sobre Búsqueda de Azure](http://msdn.microsoft.com/library/dn798933.aspx)

- [API de REST de Búsqueda de Azure](http://msdn.microsoft.com/library/dn798935.aspx)

- [Vídeo de Canal 9: Introducción a Búsqueda de Azure](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [Vídeo de Canal 9: Búsqueda de Azure y datos geoespaciales](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [Descripción de la nube, episodio 152: Generación de un índice en Búsqueda de Azure](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Comienzo con el servicio gratuito]: #sub-1
[Actualización a una búsqueda estándar]: #sub-2
[Prueba de operaciones del servicio]: #sub-3
[Exploración del panel del servicio de búsqueda]: #sub-4
[Prueba]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Administración de la solución de búsqueda en Microsoft Azure]: search-manage.md
[Flujo de trabajo de desarrollo de Búsqueda de Azure]: search-workflow.md
[Creación de la primera solución de búsqueda de Azure]: search-create-first-solution.md
[Crear una aplicación de búsqueda geoespacial usando Búsqueda de Azure]: search-create-geospatial.md

<!--HONumber=49--> 

<!--HONumber=49-->