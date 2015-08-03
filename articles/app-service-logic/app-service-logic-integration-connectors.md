<properties 
	pageTitle="Integración en BizTalk de Aplicaciones de API en Servicios de aplicaciones de Microsoft Azure | Azure" 
	description="Aprenda a crear y configurar aplicaciones de API de integración de BizTalk; arquitectura de microservicios" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>


# Aplicaciones de API de integración de BizTalk en el Servicio de aplicaciones de Microsoft Azure

> [AZURE.NOTE]Este tema se ha retirado. Consulte la [lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md) para ver todas las aplicaciones API y conectores integrados que se encuentran disponibles.


El Servicio de aplicaciones de Microsoft Azure (o Servicio de aplicaciones para abreviar) incluye muchas aplicaciones de API de BizTalk que son esenciales para los entornos de integración. Estas aplicaciones de API se basan en conceptos y herramientas que se usan en BizTalk Server, pero que ahora están disponibles como parte de Servicios de aplicaciones de Azure.

Una categoría de estas aplicaciones de API son las aplicaciones de API de integración de BizTalk. Con estas aplicaciones de API de BizTalk, puede agregar reglas de negocio fácilmente, transformar y validar mensajes XML, codificar archivos sin formato y datos JSON, etc.; exactamente igual que haría localmente con BizTalk Server.

Estas aplicaciones de API de integración ofrecen funcionalidades de tipo "Acción". Una acción es el resultado, como después de recibir un mensaje XML, validarlo en un esquema XML.


## ¿Qué son las aplicaciones de API de integración?
Las aplicaciones de API de integración de BizTalk son aplicaciones de API existentes previamente creadas que pueden realizar procesamiento de datos y producir una salida. Por ejemplo, algunas de estas aplicaciones de API permiten que diferentes formatos de archivo trabajen conjuntamente y algunas aplican lógica de negocios o lógica de aplicación. Las aplicaciones de API de integración incluyen:

Aplicaciones de API | Descripción
--- | ---
<ul><li>Aplicación de API X12</li><li>Conector de AS2</li><li>Aplicación de API EDIFACT</li><li>Aplicación de API de administración de socios comerciales</li> | Estas aplicaciones de API proporcionan capacidades negocio a negocio. En [Conectores negocio a negocio](app-service-logic-b2b-connectors.md) encontrará más detalles sobre estas aplicaciones de API.
Codificador de archivos sin formato de BizTalk | Una aplicación de API **Acción**. Hace que los datos de archivo sin formato (como Excel y csv) y los datos XML trabajen juntos (interoperabilidad). Puede convertir un archivo sin formato a XML y viceversa.
Codificador JSON de BizTalk | Una aplicación de API **Acción**. Hace que los datos JSON y XML trabajen conjuntamente (interoperabilidad). Puede convertir una instancia JSON en XML y viceversa.
Reglas de BizTalk | Una aplicación de API **Acción**. Puede implementar y aplicar lógica de negocios "reglas" para controlar una operación empresarial. Las reglas son dinámicas y pueden cambiar en cualquier momento. Reglas de BizTalk permite a los usuarios habilitar la lógica de negocios sin escribir ningún código y se pueden actualizar fácilmente sin afectar a las aplicaciones.
Transformación de BizTalk | Una aplicación de API **Acción**. Convierte datos de un formato a otro. Puede usar diferentes funciones integradas para manipular cadenas, completar expresiones aritméticas, formatear la fecha y hora, etc. 
Validador XML de BizTalk | Una aplicación de API **Acción**. Valida los datos XML en esquemas XML predefinidos. Puede usar esquemas basándose en instancias de un archivo sin formato, instancias JSON o conectores existentes. 
Extractor XPath de BizTalk | Una aplicación de API **Acción**. Busque y extraiga datos de contenido XML basándose en una expresión XPath específica.
Esperar | Retrasar la ejecución durante un tiempo que especifique o hasta un momento concreto. Cuando se agrega a una aplicación lógica, se puede usar para retrasar la ejecución de toda la aplicación.


	> [AZURE.NOTE] If the input xml has a simple node with an attribute (like "<authorid= ”1”>abc</author>"), then the JSON output of the library is { “author”: { “@id” : “1”, “#text”: “abc”}}. To handle the “Id” attribute, a new “#text” key is added for the text content of the node. To handle this kind of node, add a constant key. This is by design in the Newtonsoft.Json library. When you insert this into SQL, use “JSONOutput.Author.#text”; do not use “JsonOutput.Author”.

Mediante estas aplicaciones de API, puede completar tareas de datos o de mensajería diferentes. Por ejemplo, mediante la aplicación de API Reglas de BizTalk, puede recibir un pedido y aplicar un descuento cuando se pide una cantidad específica. O bien, puede cargar un índice de impuestos específico según el código postal.

Puede crear tantas aplicaciones de API como desee y crearlas de forma fácil. Las aplicaciones de API también se pueden reutilizar dentro varios escenarios o flujos de trabajo.

Por ejemplo, supongamos que tiene una directiva empresarial que aplica un 10 % de descuento cuando un cliente pide 100 artículos de su producto. En la aplicación, puede agregar la aplicación de API Reglas de BizTalk que comprueba la cantidad pedida y, si es superior a 100, aplicar el 10 % de descuento.

También puede expandir esa directiva empresarial. Supongamos que tiene el objetivo de aumentar las ventas en Carolina del Norte. Además el 10 % de descuento en los pedidos con cantidades iguales o superiores a 100, también ofrece envío gratuito si el pedido procede de Carolina del Norte. Puede agregar fácilmente esta condición de Carolina del Norte a la regla de BizTalk existente.

Puede hacerlo mediante estas aplicaciones de API y sin escribir ningún código. Comencemos.


## Creación de aplicaciones de API
Las aplicaciones de API de integración pueden crearse mediante el Portal de Azure o mediante API de REST.


### Creación de aplicaciones de API mediante API de REST
Consulte [API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


### Creación de aplicaciones de API de integración en el Portal de Azure
En el Portal de Azure, puede crear aplicaciones de API de integración de BizTalk al crear aplicaciones lógicas, aplicaciones web o aplicaciones móviles. O bien, puede crear uno con su propia hoja. Ambas formas son sencillas, por lo que depende de sus necesidades o preferencias. Algunos usuarios prefieren crear primero todas las aplicaciones de API con sus propiedades específicas. A continuación, cree las aplicaciones lógicas, web o móviles y agregue las aplicaciones de API de integración creadas.

Mediante los pasos siguientes se crean aplicaciones de API de integración de BizTalk con la hoja Aplicaciones de API:

1. En el Panel de inicio del Portal de Azure (la página principal), seleccione **Marketplace**. En **Aplicaciones de API** se muestran todas las aplicaciones de API y conectores existentes. También puede **buscar** una aplicación de API de BizTalk específica.
2. Seleccione la aplicación de API. En la nueva hoja, seleccione **Crear**. 
3. Especifique las propiedades: 

Propiedad | Descripción
--- | ---
Nombre | Escriba un nombre para la aplicación de API. Por ejemplo, puede asignarle el nombre *RulesDiscountTaxCode* o *APIAppValidateXML*.
Plan de servicio de aplicación | Muestra el plan de pagos. Puede cambiarlo si necesita más o menos recursos.
Nivel de precios | Propiedad de solo lectura que muestra la categoría de precio de su suscripción de Azure. 
El grupos de recursos | Cree uno nuevo o utilice un grupo ya existente. Todas las aplicaciones de API y conectores de las aplicaciones lógicas, aplicaciones web y aplicaciones móviles deben estar en el mismo grupo de recursos. En <br/><br/>[Uso de grupos de recursos](../resource-group-overview.md) se explica esta propiedad. 
La suscripción | Propiedad de solo lectura que muestra su suscripción actual.
Ubicación | La ubicación geográfica que hospeda el servicio de Azure. 
Agregar al Panel de inicio | Seleccione esto para agregar las aplicaciones de API de integración a su panel de inicio (la página principal)


## Configuración de las aplicaciones de API de BizTalk
En el Portal de administración de Azure, abra su aplicación de API de BizTalk. En la sección **Componentes**, puede agregar los componentes adicionales necesarios para completar la aplicación de API:

API App | Tasks
--- | ---
Codificador de archivos sin formato de BizTalk | Especifique un archivo sin formato, como Excel o un archivo csv que desee convertir a XML. O bien, especifique un archivo XML que desee convertir en un archivo sin formato.
Codificador JSON de BizTalk | Especifique un archivo JSON que desee convertir a XML. O bien, especifique un archivo XML que desee convertir a JSON. 
Reglas de BizTalk | Agregue los vocabularios y cree sus reglas IF THEN. Vea [Uso de reglas de BizTalk](app-service-logic-use-biztalk-rules.md). 
Transformación de BizTalk | Agregue una asignación y especifique un esquema XML de entrada y un esquema XML de salida. Use las funciones integradas para manipular el mensaje o los datos entrantes para que coincidan con el esquema XML de salida. Vea [Transformación de documentos XML](app-service-logic-transform-xml-documents.md). 
Validador XML de BizTalk | Escriba el código XML para la validación en un esquema XML predefinido. Puede usar esquemas basándose en instancias de un archivo sin formato, instancias JSON o conectores existentes. 
Extractor XPath de BizTalk | Busque y extraiga datos de contenido XML basándose en una expresión XPath específica.
Esperar | Escriba una duración de tiempo o una hora específica para ejecutar las aplicaciones web, aplicaciones móviles o aplicaciones lógicas.


## Supervisión de las aplicaciones de API
En el Portal de administración de Azure, abra su aplicación de API de BizTalk. En la sección **Operaciones**, puede ver distintas operaciones de administración. Por ejemplo, puede:

- Ver sucesos informativos y de error
- Ver el uso de memoria y el recuento de subprocesos del proceso de trabajo (w3wp)
- Ver los registros de la aplicación y del servidor web

Más información en [Supervisión de las aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md).


## Adición de aplicaciones de API de BizTalk a la aplicación 
Servicio de aplicaciones de Microsoft Azure expone diferentes tipos de aplicación que pueden usar estas aplicaciones API de integración. Puede crear aplicaciones de API de BizTalk nuevas o agregar las existentes a aplicaciones lógicas, aplicaciones móviles o aplicaciones web.

Dentro de la aplicación, con el simple hecho de seleccionar sus aplicaciones de API de BizTalk desde la Galería, automáticamente se agregan a la aplicación.

Los siguientes pasos agregan aplicaciones de API de BizTalk a aplicaciones lógicas, aplicaciones móviles o aplicaciones web:

1. En el Panel de inicio del Portal de Azure (página principal), vaya a **Marketplace** y busque las aplicaciones lógicas, móviles o web. 

	Si va a crear una nueva aplicación, busque aplicaciones lógicas, aplicaciones móviles o aplicaciones web. Seleccione la aplicación en la nueva hoja, seleccione **Crear**. En [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md) se muestran los pasos.

2. Abra su aplicación y seleccione **Desencadenadores y acciones**.

3. Desde el **Galería**, seleccione la aplicación de API de BizTalk, lo que automáticamente la agrega a la aplicación.

4. Seleccione **Aceptar** para guardar los cambios.


## Más recursos de aplicaciones de API de integración
[Creación de una aplicación lógica de EAI con VETR](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/> [Transformación de documentos XML](app-service-logic-transform-xml-documents.md)<br/> [Uso de reglas de BizTalk](app-service-logic-use-biztalk-rules.md)<br/> [Qué son las aplicaciones de API de BizTalk y los conectores](app-service-logic-what-are-biztalk-api-apps.md)


## Lea acerca de las aplicaciones lógicas y las aplicaciones web
[¿Qué son Aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)<br/> [Sitios web y aplicaciones web en el Servicio de aplicaciones de Azure](../app-service-web/app-service-web-overview.md)


## Más conectores

[Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)<br/><br/> [Qué son los conectores y las aplicaciones de API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO4-->