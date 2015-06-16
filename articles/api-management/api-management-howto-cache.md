<properties 
	pageTitle="Almacenamiento en caché de resultados de operaciones en Administración de API de Azure" 
	description="Obtenga información acerca de cómo mejorar la latencia, el consumo de ancho de banda y la carga de servicios web para las llamadas de servicio de Administración de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Almacenamiento en caché de resultados de operaciones en Administración de API de Azure

En Administración de (vista previa), las operaciones se pueden configurar para almacenamiento en caché de respuestas. El almacenamiento en caché de respuestas puede reducir significativamente la latencia de la API, el consumo de ancho de banda y la carga del servicio web en cuanto a datos que no cambian con frecuencia.

En este tutorial revisará la configuración y las directivas de almacenamiento en caché para una de las operaciones Echo API de ejemplo y llamará a la operación en el portal para desarrolladores para ver el almacenamiento en caché de la operación.

## En este tema

-   [Configuración de una operación para almacenamiento en caché][Configuración de una operación para almacenamiento en caché]
-   [Revisión de las directivas de almacenamiento en caché][Revisión de las directivas de almacenamiento en caché]
-   [Llamada a una operación y prueba del almacenamiento en caché][Llamada a una operación y prueba del almacenamiento en caché]
-   [Pasos siguientes][Pasos siguientes]

## <a name="configure-caching"> </a>Configuración de una operación para almacenamiento en caché

En este paso, revisará la configuración de almacenamiento en caché de la operación **Recurso GET (en caché)** de la API Echo de ejemplo.

> Cada instancia del servicio Administración de API viene previamente configurada con una API Eco que se puede usar para experimentar con Administración de API y aprender de esta. Para obtener información, consulte [Introducción a Administración de API de Azure][Introducción a Administración de API de Azure].

Para comenzar, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

![API Management console][API Management console]

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a Administración de API de Azure].

Haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Echo API**.

![Echo API][Echo API]

Seleccione la pestaña **Operaciones** y haga clic en la operación **Recurso GET (en caché)** en la lista **Operaciones**.

![Echo API operations][Echo API operations]

Seleccione la pestaña **Caching** para ver la configuración de almacenamiento en caché para esta operación.

![Caching tab][Caching tab]

Para habilitar el almacenamiento en caché para una operación, active la casilla **Habilitar**. En este ejemplo el almacenamiento en caché está habilitado.

Cada respuesta de operación incluye una clave basada en los valores de los campos **Variar por parámetros de cadena de consulta** y **Variar por encabezados**. Si desea almacenar en caché varias respuestas basadas en parámetros de la cadena de consulta o encabezados, puede configurarlas en estos dos campos.

**Duración** especifica el intervalo de expiración de las respuestas almacenadas en caché. En este ejemplo el intervalo es **3600** segundos, que equivale a una hora.

Con la configuración de almacenamiento en caché de este ejemplo, la primera solicitud para la operación **Recurso GET (en caché)** devolverá una respuesta del servicio back-end. Esta respuesta se almacenará en caché, con clave especificada mediante encabezados y parámetros de la cadena de consulta. Las siguientes llamadas a la operación, con parámetros coincidentes, devolverán la respuesta almacenada en caché hasta que el intervalo de duración en la caché haya expirado.

## <a name="caching-policies"> </a>Revisión de las directivas de almacenamiento en caché

Cuando se haya definido la configuración de almacenamiento en caché para una operación en la pestaña **Caching**, se agregarán las directivas de almacenamiento en caché para la operación. Estas directivas se pueden ver y editar en el editor de directivas.

Haga clic en **Directivas** en el menú **Administración de API** de la izquierda y seleccione **API eco /Recurso GET (en caché)** en la lista desplegable **Operación**.

![Policy scope operation][Policy scope operation]

Se muestran las directivas para esta operación en el editor de directivas.

![API Management policy editor][API Management policy editor]

La definición de directiva para esta operación incluye las directivas que definen la configuración de almacenamiento en caché que se revisó con la pestaña **Caching** en el paso anterior.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

> Los cambios efectuados a las directivas de almacenamiento en caché en el editor de directivas se reflejarán en la pestaña **Caching** de una operación, y viceversa.

## <a name="test-operation"> </a>Llamada a una operación y prueba del almacenamiento en caché

Para ver cómo funciona el almacenamiento en caché, podemos llamar a la operación desde el portal para desarrolladores. Haga clic en **Portal para desarrolladores** en el menú situado en la parte superior derecha.

![Portal para desarrolladores][Portal para desarrolladores]

Haga clic en **API** en el menú superior y seleccione **API Eco**.

![Echo API][1]

> Si solamente tiene una API configurada o visible en su cuenta, al hacer clic en API irá directamente a las operaciones de dicha API.

Seleccione la operación **Recurso GET (en caché)** y haga clic en **Abrir consola**.

![Open console][Open console]

La consola permite invocar operaciones directamente desde el portal para desarrolladores.

![Consola][Consola]

Mantenga los valores predeterminados de **param1** y **param2**.

Seleccione la clave que desee de la lista desplegable **subscription-key**. Si su cuenta tiene solo una suscripción, ya estará seleccionada.

Introduzca **sampleheader:value1** en el cuadro de texto **Encabezados de solicitud**.

Haga clic en **HTTP Get** y tome nota de los encabezados de respuesta.

Introduzca **sampleheader:value2** en el cuadro de texto **Encabezados de solicitud** y haga clic en **HTTP Get**.

Observe que el valor de **sampleheader** sigue siendo **value1** en la respuesta. Pruebe algunos valores distintos y observe que se devuelve la respuesta almacenada en caché.

Introduzca **25** en el campo **param2** y haga clic en **HTTP Get**.

Observe que el valor de **sampleheader** de la respuesta es ahora **value2**. Debido a que los resultados de la operación incluyen una clave especificada mediante la cadena de consulta, no se devolvió la anterior respuesta almacenada en caché.

## <a name="next-steps"> </a>Pasos siguientes

-   Consulte el resto de temas en el tutorial [Introducción a la configuración de API avanzada][Introducción a la configuración de API avanzada].
-   Para obtener información sobre las directivas de almacenamiento en caché, consulte [Directivas de almacenamiento en caché][Directivas de almacenamiento en caché] en la [Referencia de directivas de Administración de API][Referencia de directivas de Administración de API].

  [Configuración de una operación para almacenamiento en caché]: #configure-caching
  [Revisión de las directivas de almacenamiento en caché]: #caching-policies
  [Llamada a una operación y prueba del almacenamiento en caché]: #test-operation
  [Pasos siguientes]: #next-steps
  [Introducción a Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-cache/api-management-management-console.png
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Echo API]: ./media/api-management-howto-cache/api-management-echo-api.png
  [Echo API operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
  [Caching tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
  [Policy scope operation]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
  [API Management policy editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
  [Portal para desarrolladores]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
  [1]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
  [Open console]: ./media/api-management-howto-cache/api-management-open-console.png
  [Consola]: ./media/api-management-howto-cache/api-management-console.png
  [Introducción a la configuración de API avanzada]: ../api-management-get-started-advanced
  [Directivas de almacenamiento en caché]: ../api-management-policy-reference/#caching-policies
  [Referencia de directivas de Administración de API]: ../api-management-policy-reference

<!--HONumber=46--> 

<!--HONumber=46--> 
 