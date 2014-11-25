<properties pageTitle="How to add operations to an API in Azure API Management" metaKeywords="" description="Learn how to add operations to an API in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to add operations to an API in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Incorporación de operaciones a una API en Administración de API de Azure

Es necesario agregar operaciones para poder usar una API en Administración de API (vista previa). En esta guía se muestra cómo agregar y configurar diferentes tipos de operaciones a una API en Administración de API.

## En este tema

-   [Agregar una operación][Agregar una operación]
-   [Almacenamiento en caché de operaciones][Almacenamiento en caché de operaciones]
-   [Parámetros de la solicitud][Parámetros de la solicitud]
-   [Cuerpo de la solicitud][Cuerpo de la solicitud]
-   [Respuestas][Respuestas]
-   [Pasos siguientes][Pasos siguientes]

## <a name="add-operation"> </a>Agregar una operación

En primer lugar, se agregan operaciones y se configuran para una API en la consola de administración. Para obtener acceso a la consola de administración, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management console][API Management console]

Seleccione en primer lugar las API que desee en el portal de Administración de API y, a continuación, la pestaña **Operaciones**.

![Operaciones][Operaciones]

Haga clic en **Agregar operación** para agregar una nueva operación. Se mostrará la ventana **Nueva operación** y la pestaña **Firma** se seleccionará de forma predeterminada.

![Add operation][Add operation]

Especifique el **verbo HTTP** seleccionándolo en la lista desplegable.

![HTTP method][HTTP method]

Defina la plantilla de URL escribiendo un fragmento de la dirección URL que conste de uno o más segmentos de ruta de la URL y de cero o más parámetros de la cadena de consulta. La plantilla de URL, que se anexa a la dirección URL base de la API, identifica una sola operación HTTP. Puede contener uno o más elementos de variable con nombre que se identifican mediante llaves. Estos elementos de variable se denominan parámetros de plantilla y son valores asignados automáticamente que se extraen de la dirección URL de la solicitud al procesar la solicitud en la plataforma Administración de API.

![URL template][URL template]

Si lo desea, especifique la **plantilla de la URL de reescritura**. Esto permite usar la plantilla estándar de URL para procesar solicitudes entrantes en el front-end, al tiempo que se llama al back-end mediante una URL convertida en función de la plantilla de reescritura. Deben usarse los parámetros de plantilla de la plantilla de URL en la plantilla de reescritura. En el siguiente ejemplo se muestra cómo se puede incorporar tipo de contenido codificado en forma de segmento de ruta al servicio web del ejemplo anterior como parámetro de consulta de la API publicada mediante la plataforma Administración de API con los modelos de URL.

![URL template rewrite][URL template rewrite]

Quienes llamen a la operación usarán el formato `/customers?customerid=ALFKI`, que se asignará a `/Customers('ALFKI')` al invocar al servicio back-end.

**Nombre para mostrar** y **Descripción** ofrecen una descripción de la operación y se usan para proporcionar documentación a los desarrolladores que usen esta API en el portal para desarrolladores.

![Descripción][Descripción]

La descripción de la operación se puede especificar como texto sin formato o HTML en el cuadro de texto **Descripción**.

## <a name="operation-caching"> </a>Almacenamiento en caché de operaciones

El almacenamiento en caché de respuestas reduce la latencia que perciben los consumidores de la API, rebaja el consumo de ancho de banda y disminuye la carga en el servicio web HTTP que implementa la API.

Para habilitar fácil y rápidamente el almacenamiento en caché de la operación, seleccione la pestaña **Caching** y active la casilla **Habilitar**.

![Almacenamiento en caché][Almacenamiento en caché]

**Duración** especifica el período de tiempo durante el que la respuesta de la operación permanece en la caché. El valor predeterminado es 3.600 segundos o 1 hora.

Se usan claves de caché para diferenciar entre respuestas de forma que la respuesta correspondiente a cada clave de caché distinta obtenga su propio valor almacenado en caché por separado. Opcionalmente, introduzca los parámetros específicos de la cadena de consulta y/o los encabezados HTTP que se usarán para calcular los valores de clave de caché en los cuadros de texto **Variar por parámetros de cadena de consulta** y **Variar por encabezados**, respectivamente. Cuando no se especifica ninguno, se usan la dirección URL de la solicitud completa y los siguientes valores de encabezado HTTP en la generación de claves de caché: **Accept** y **Accept-Charset**.

> Para obtener más información sobre el almacenamiento en caché y las directivas de almacenamiento en caché, consulte [Almacenamiento en caché de resultados de operaciones en Administración de API de Azure][Almacenamiento en caché de resultados de operaciones en Administración de API de Azure].

## <a name="request-parameters"> </a>Parámetros de la solicitud

Los parámetros de la operación se administran en la pestaña Parámetros. Los parámetros especificados en **Modelo de URL** en la pestaña **Firma** se agregan automáticamente y solo pueden cambiarse modificando la plantilla de URL. Se pueden introducir manualmente parámetros adicionales.

Para agregar un nuevo parámetro de consulta, haga clic en **Agregar parámetro de consulta** e introduzca la siguiente información:

-   **Nombre**: nombre del parámetro.
-   **Descripción**: breve descripción del parámetro (opcional).
-   **Tipo**: tipo de parámetro, seleccionado en la lista desplegable.
-   **Valores**: valores que se pueden asignar a este parámetro. Uno de los valores se puede marcar como predeterminado (opcional).
-   **Obligatorio**: convierte el parámetro en obligatorio activando la casilla.

![Request parameters][Request parameters]

## <a name="request-body"> </a>Cuerpo de la solicitud

Si la operación lo permite (por ejemplo, PUT, POST) y requiere un cuerpo, puede proporcionar un ejemplo del mismo en todos los formatos de representación compatibles (por ejemplo, json, XML).

> El cuerpo de la solicitud solo se usa a efectos de documentación y no se valida.

Para especificar un cuerpo de la solicitud, cambie a la pestaña **Cuerpo**.

Haga clic en **Agregar representación**, comience a escribir el nombre del contenido que desee (por ejemplo, aplicación/json), selecciónelo en la lista desplegable y, en el cuadro de texto, pegue el ejemplo de cuerpo de la solicitud que desee en el formato seleccionado.

![Request body][Request body]

En representaciones adicionales, también puede especificar una descripción opcional de texto en el cuadro de texto **Descripción**.

## <a name="responses"> </a>Respuestas

Es recomendable proporcionar ejemplos de respuestas para todos los códigos de estado que puede producir la operación. Cada código de estado puede tener más de un ejemplo de cuerpo de respuesta, uno para cada tipo de contenido admitido.

Para agregar una respuesta, haga clic en **Agregar** y comience a escribir el código de estado que desee. En este ejemplo, el código de estado es **200 OK**. Cuando el código aparezca en la lista desplegable, selecciónelo; el código de respuesta se creará y se agregará a la operación.

![Response code][Response code]

Haga clic en **Agregar representación**, comience a escribir el nombre del contenido que desee (por ejemplo, aplicación/json) y selecciónelo en la lista desplegable.

![Body content type][Body content type]

Pegue el ejemplo de cuerpo de la respuesta en el formato seleccionado en el cuadro de texto.

![Response body][Response body]

Si lo desea, agregue una descripción opcional en el cuadro de texto **Descripción**.

Una vez configurada la operación, haga clic en **Guardar**.

## <a name="next-steps"> </a>Pasos siguientes

Una vez agregadas las operaciones a una API, el paso siguiente es asociar la API al producto y publicarlo para que los desarrolladores pueden llamar a las operaciones.

-   [Creación y publicación de un producto][Creación y publicación de un producto]

  [Agregar una operación]: #add-operation
  [Almacenamiento en caché de operaciones]: #operation-caching
  [Parámetros de la solicitud]: #request-parameters
  [Cuerpo de la solicitud]: #request-body
  [Respuestas]: #responses
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-add-operations/api-management-management-console.png
  [Operaciones]: ./media/api-management-howto-add-operations/api-management-operations.png
  [Add operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
  [HTTP method]: ./media/api-management-howto-add-operations/api-management-http-method.png
  [URL template]: ./media/api-management-howto-add-operations/api-management-url-template.png
  [URL template rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
  [Descripción]: ./media/api-management-howto-add-operations/api-management-description.png
  [Almacenamiento en caché]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
  [Almacenamiento en caché de resultados de operaciones en Administración de API de Azure]: ../api-management-howto-cache
  [Request parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
  [Request body]: ./media/api-management-howto-add-operations/api-management-request-body.png
  [Response code]: ./media/api-management-howto-add-operations/api-management-response-code.png
  [Body content type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
  [Response body]: ./media/api-management-howto-add-operations/api-management-response-body.png
  [Creación y publicación de un producto]: ../api-management-howto-add-products
