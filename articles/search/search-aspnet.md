<properties
	pageTitle="Conexión de Búsqueda de Azure con aplicaciones web de ASP.NET | Microsoft Azure"
	description="Conexión de una aplicación web de ASP.NET con Búsqueda de Azure. Aprenda a conectar, consultar y representar los resultados mediante la biblioteca .Net o la API de REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="08/26/2015"
	ms.author="heidist"/>

#Integración de Búsqueda de Azure con aplicaciones web de ASP.NET

ASP.NET es el marco de aplicaciones web predominante en soluciones personalizadas que se integran con Búsqueda de Azure. En este artículo, aprenderá a conectar la aplicación web de ASP.NET con Búsqueda de Azure, a iniciarse en los patrones de diseño para operaciones comunes y a revisar algunas prácticas de codificación que pueden ayudarle a facilitar su experiencia de desarrollo.

##Organización del código

La división de las cargas de trabajo en proyectos independientes en la misma solución de Visual Studio ofrece una mayor flexibilidad para diseñar, mantener y ejecutar cada programa. Lo recomendable son tres:

- Código de creación de índice
- Código de ingesta de datos
- Código de interacción del usuario

En Búsqueda de Azure, las operaciones de indexación y las operaciones con documentos, como agregar o actualizar documentos o ejecutar consultas, son completamente independientes entre sí. Esto significa que puede desacoplar la administración de índices del código de interacción del usuario de ASP.NET que formula solicitudes de búsqueda y representa los resultados.

En la mayoría de los códigos de ejemplo, el índice se crea y se carga en un proyecto (conocido como DataIndexer, CatalogIndexer o DataCatalog en diversos ejemplos), mientras que el código que controla las solicitudes y respuestas de búsqueda se coloca en un proyecto de aplicación de ASP.NET MVC. En los códigos de ejemplo, resulta práctico agrupar la creación de índices y la carga de documentos en un proyecto, pero el código de producción probablemente aislará estas operaciones. Una vez que se crea un índice, rara vez se cambia (y si se cambia, debe volver a generarse), mientras que los documentos se actualizarán probablemente de forma periódica.

La separación de las cargas de trabajo proporciona otras ventajas en forma de diferentes niveles de permisos para Búsqueda de Azure (derechos de administración completos frente a derechos de solo consulta), uso de diferentes lenguajes de programación, dependencias más específicas por programa, además de la posibilidad de revisar los programas de forma independiente o de crear varias aplicaciones front-end que funcionan todas en el índice compilado y mantenido por una aplicación de indexación central.

##Ejemplos y demostraciones con ASP.NET y Búsqueda de Azure

Ya existen varios códigos de ejemplo que muestran cómo se integra el servicio Búsqueda con ASP.NET. Puede pasar directamente al código o a una aplicación de demostración visitando cualquiera de estos vínculos:

- [Sitio de demostración de trabajos de Nueva York (NYC)](http://aka.ms/azjobsdemo)
- [Prueba del Servicio de aplicaciones + Búsqueda de Azure](search-tryappservice.md)
- [Lista completa de vídeos, tutoriales, demostraciones y ejemplos de código](earch-video-demo-tutorial-list.md)

##Conexión al servicio

Para establecer una conexión al servicio y emitir solicitudes, la aplicación web solo necesita tres cosas:

- Una dirección URL al servicio Búsqueda de Azure que ha aprovisionado, con el formato https://<service-name>. search.windows.net.
- Una clave de API (GUID) que autentica la conexión a Búsqueda de Azure.
- Un HTTPClient o SearchServiceClient para formular la solicitud de conexión.

####Direcciones URL y claves de API

Puede encontrar la clave de API y la dirección URL en el [portal](search-create-service-portal.md) o recuperarlas mediante programación a través de la [API de REST de administración](https://msdn.microsoft.com/library/dn832684.aspx).

Normalmente, la dirección URL y la clave se ubican en el archivo web.config de su programa de interacción del usuario:

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

El nombre del servicio Búsqueda puede ser el nombre corto que especificó durante el aprovisionamiento mientras anexe el dominio (search.windows.net) en la conexión, o puede especificar el nombre completo (<service-name>. search.windows.net) en el archivo web.config, sin el prefijo HTTPS.

La clave de API es un token de autenticación generado durante el aprovisionamiento del servicio (solo claves de administración) o generado manualmente si va a crear claves de consulta en el portal. El tipo de clave determina qué operaciones de búsqueda están disponibles para la aplicación:

- claves de administración (permisos de lectura y escritura, 2 por servicio)
- claves de la consulta (solo lectura, hasta 50 por servicio)

Todas las claves de API son GUID. Visualmente, no hay distinción entre las claves de administración y de consulta. Deberá visitar el portal o usar la API de REST de administración para determinar el tipo de clave.

> [AZURE.TIP]Una clave de consulta proporciona una experiencia de solo lectura al cliente. Vea [TryAppService + Búsqueda Azure](search-tryappservice.md) para probar las operaciones de Búsqueda de Azure que están disponibles en un servicio de solo lectura. Tenga en cuenta que en TryAppService, el código de aplicación web es totalmente modificable; puede cambiar cualquier parte del código de C# en el proyecto de ASP.NET para modificar el diseño de páginas web, la construcción de consultas de búsqueda o los resultados de búsqueda. Solo las operaciones de índice de servicio y de carga de documentos del servicio Búsqueda de Azure son de solo lectura, por la inclusión de una clave de API de consulta en la conexión del servicio.

####Conexión del cliente

Los dos fragmentos de código siguientes establecen una conexión al servicio Búsqueda con la clave de API y la dirección URL. Recuerde que el nombre del servicio y las claves de API se especifican en el archivo web.config. Para llamadas de REST, se deben pasar claves de administrador en el encabezado de solicitud, mientras que las claves de consulta se pueden pasar en el encabezado o directamente en la dirección URL.

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) con llamadas a API de REST**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) con .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##Patrones de diseño

Una aplicación web que se integra con Búsqueda de Azure deberá formular consultas y representar los resultados. En esta sección se proporcionan instrucciones sobre cómo estructurar el código para las tareas que se ejecutan en un programa que contiene código de interacción de usuario. La definición de esquema, la generación de índice y la ingesta de datos se excluyen deliberadamente. Para obtener información sobre cómo codificar esas operaciones, vea los tutoriales y ejemplos que se enumeran en [Vídeos, ejemplos y tutoriales en Búsqueda de Azure](search-video-demo-tutorial-list.md).

###Formulación de consultas

Una búsqueda de texto completo en el índice se ejecuta en los campos marcados como **isSearchable** en el esquema que define el índice. Dada la entrada de un término de búsqueda (representada a continuación por la cadena "q"), el motor de búsqueda busca una coincidencia en todos los campos de búsqueda y devuelve los resultados desde los campos marcados como **isRetrievable**.

> [AZURE.NOTE]Aunque es probable que la mayoría de los campos permitan realizar búsquedas, un índice podría contener campos usados solo en las expresiones de filtro, en cuyo caso los marcaría como "no admiten búsqueda" para excluirlos de la búsqueda de texto completo, y como "no recuperables" para excluirlos de los resultados de búsqueda.

Una consulta de búsqueda contiene el término de entrada proporcionado por el usuario en una solicitud de búsqueda que especifica el índice de destino, además de los parámetros usados para filtrar o restringir la solicitud. Los operadores insertados en la cadena de búsqueda, como +, - o |, se controlan automáticamente, lo que significa que no hay ningún requisito de codificación para el análisis de un término de búsqueda. Todo análisis se realiza mediante el motor de búsqueda, como una operación interna. Puede suponer que la cadena que se pasa la analizará el motor.

Una consulta de búsqueda adopta dos versiones: **búsqueda** o **sugerencias**. Definirá métodos independientes para cada tipo de consulta. **Búsqueda** es la búsqueda de texto completo sobre los campos del índice. **Sugerencias** es la característica de consulta de tipo anticipada o autocompletar del servicio Búsqueda de Azure que genera una lista de posibles términos de búsqueda en función de los tres primeros caracteres de la entrada del usuario. En la mayoría de los casos, restringirá las **sugerencias** a solo los campos que contienen valores relativamente únicos o distintos (como un nombre de producto o de publicación), en lugar de los campos que contienen datos no diferenciados.

El siguiente fragmento de código captura una entrada del término de búsqueda en un programa que usa la API de REST. El término de entrada se representa por string q y los parámetros restantes se usan para pasar valores de filtro desde una estructura de navegación por facetas en la misma página de búsqueda. Tanto el término de entrada como los parámetros de filtro se usan en el método de búsqueda.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
El método de **búsqueda** que acepta esta consulta se define de la manera siguiente. Observe que se definen los parámetros en la cadena de consulta, además de la estructura de navegación por facetas (que se admite gracias a los filtros que hacen el trabajo pesado en la acotación de los resultados de búsqueda) y los criterios de ordenación.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

Un método .NET que construye una cadena de búsqueda podría colocarse en una vista de MVC o en un controlador. Esta función pasa la cadena al controlador de inicio. También define una estructura de datos para los resultados.

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

Un método .NET para invocar **Búsqueda** podría tener este aspecto, contenido en el programa principal de C# que proporciona la operación de búsqueda y conexión:

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###Tratamiento de los resultados de búsqueda

Los resultados de búsqueda se devuelven como un conjunto de filas compuestas por campos que están marcados en el esquema de índice como isRetrievable. Una de las maneras más sencillas de representar un conjunto de resultados es mediante el objeto del sistema ViewBag en MVC. El siguiente fragmento de código es de Index.cshtml del [proyecto AdventureWorksDemo en CodePlex](https://azuresearchadventureworksdemo.codeplex.com/).

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###Navegación por facetas

En el mismo archivo Index.cshmtl, puede encontrar el código HTML usado para generar una estructura de navegación por facetas que proporciona clasificaciones para el filtrado autodirigido, así los resultados de búsqueda se acotan progresivamente por color, precio o categoría.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###Resaltado de referencias

La aplicación de un estilo a la instancia del término de búsqueda en un resultado de búsqueda se denomina elemento destacado. En Búsqueda de Azure, los elementos destacados se especifican en la consulta, mediante el parámetro de búsqueda de resaltado, que proporciona una lista delimitada por comas de campos para buscar términos coincidentes. El estilo real que aplique es decisión suya. Los tres fragmentos de código siguiente son del tutorial [TryAppService + Búsqueda de Azure](search-tryappservice.md).

En primer lugar, especifique los elementos destacados como un parámetro de búsqueda y muestre los campos para buscar términos coincidentes. Especifique el estilo HTML para usar en elementos destacados.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

A continuación, recorra en iteración los resultados de búsqueda hasta encontrar la cadena que debe aparecer resaltada. private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

Por último, proporcione el diseño de los resultados de búsqueda; para ello, especifique el conjunto de resultados que se evaluó en el fragmento anterior.

	<div class="col-xs-12 col-sm-6 col-md-10">
	  <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
	
	  <ul class="list-unstyled">
	    <!-- Cycle through the search results -->
	   @foreach (var item in response.Results)
	    {
	     <li>
	       <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
	       <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
	       <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
	       <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
	       <br />
	     </li>
	    }
	  </ul>
	</div>


##Prácticas de codificación comunes

¿Nuevas API de REST, programación de .NET o MVC? Estas secciones ofrecen algunas prácticas de codificación para ayudarle a ponerse al día.

###Plantilla de MVC

En la tabla siguiente se resume cómo se usan los componentes de plantillas de MVC en aplicaciones que incluyen Búsqueda de Azure. Si usa MVC 4 o MVC 5, generalmente se agregará código que integra Búsqueda de Azure a estos módulos

Archivo|Descripción
----|-----------
Web.config|Proporcione la dirección URL y la clave de API del servicio. Agregue una referencia a System.Configuration en el módulo de programa principal para leer los valores.
Program.cs|En el programa principal, configure un HttpClient o SearchServiceClient para establecer una conexión al servicio. Agregue el método de búsqueda a este programa.
DataModel|No se usa. Suponiendo que las operaciones de creación de índices y de carga de documentos se encuentren en diferentes programas, no se requiere ningún modelo de datos para Búsqueda de Azure en la aplicación web.
Vistas|Una vista contiene el código HTML de la página web de la aplicación, desde la entrada en el cuadro de búsqueda hasta el HTML dinámico para el control de los resultados de búsqueda.
Controladores|La construcción de consultas y el control de errores se encuentran normalmente en HomeContoller.cs. Como mínimo, el controlador debe incluir un método de búsqueda que devuelva resultados de Búsqueda de Azure y que reenvíe el conjunto de resultados a la vista. 

Opcionalmente, si va a usar sugerencias para consultas de autocompletar, debería incluir un método que devuelva las consultas sugeridas, en función de si el índice contiene un valor que coincida con la entrada del término de búsqueda proporcionado por el usuario.

###Cuándo usar la biblioteca de cliente .NET frente a la API de REST

Para aplicaciones ASP.NET, la biblioteca de cliente .NET se considera una mejor opción porque configura la conexión HTTP y controla la serialización y deserialización de JSON, lo que simplifica el código JSON.

En algunos casos, la elección de la API puede venir determinada por la paridad de características entre los dos enfoques. Por lo general, la [biblioteca cliente .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) y la [API de REST de servicio](https://msdn.microsoft.com/library/azure/dn798935.aspx) son intercambiables siempre y cuando se implementan las operaciones que necesite en ambos. Sin embargo, a veces aparecen nuevas características primero en la API de REST como parte de una versión preliminar, y solo se agregan a la biblioteca .NET meses más tarde. Por ejemplo, los indexadores, que se usan para automatizar las operaciones de carga de datos de tipos de orígenes de datos específicos, aparecieron primero en la API de REST preliminar antes de mostrarse en la biblioteca cliente unos meses más tarde. Las restricciones sobre la implementación de características se indican en la documentación de la característica.

###Inclusión de AzureSearchHelper.cs para la serialización y deserialización de JSON en API de REST

A diferencia de la biblioteca .NET que hace este paso automáticamente, las API de REST de servicios deben serializar y deserializar los documentos JSON en el intercambio de solicitud y respuesta con el servicio. JSON es el formato de carga para la transmisión de datos al cargar o actualizar documentos del índice.

Se puede encontrar código para la serialización de JSON en varios ejemplos, en un archivo denominado **AzureSearchHelper.cs**:

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}


##Pasos siguientes

Para fomentar el conocimiento de Búsqueda de Azure y la integración con ASP.NET, consulte los siguientes vínculos:

- [Cómo usar Búsqueda de Azure desde una aplicación .NET](search-howto-dotnet-sdk.md) 
- [Caso práctico para desarrolladores de Búsqueda de Azure](search-dev-case-study-whattopedia.md)
- [Flujo de trabajo de desarrollo típico para Búsqueda de Azure](search-workflow.md) 

<!---HONumber=September15_HO1-->