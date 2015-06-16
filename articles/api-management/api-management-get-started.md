<properties 
	pageTitle="Introducción a la Administración de API de Azure" 
	description="Vea cómo crear API, operaciones y comenzar a usar Administración de API." 
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
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Introducción a Administración de API de Azure

En esta guía se muestra cómo empezar a usar rápidamente la Administración de API y hacer la primera llamada API.

## En este tema

-   [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API]
-   [Creación de una API][Creación de una API]
-   [Agregar una operación][Agregar una operación]
-   [Agregar la nueva API al producto][Agregar la nueva API al producto]
-   [Suscripción al producto que contiene la API][Suscripción al producto que contiene la API]
-   [Llamada a una operación desde el portal para desarrolladores][Llamada a una operación desde el portal para desarrolladores]
-   [Ver análisis][Ver análisis]
-   [Pasos siguientes][Pasos siguientes]

## <a name="create-service-instance"> </a>Creación de una instancia de Administración de API

> para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

El primer paso para trabajar con Administración de API es crear una instancia de servicio. Inicie sesión en el [Portal de administración][Portal de administración] y haga clic en **Nuevo**, **Servicios de aplicaciones**, **Administración de API**, **Crear**.

![API Management new instance][API Management new instance]

En **URL**, especifique un nombre de subdominio único que desee usar para la dirección URL de servicio.

Elija la información que desee en **Nivel de previos**, **Suscripción** y **Región** para la instancia de servicio. Para este tutorial se pueden utilizar todos los niveles de precios. Después de realizar las selecciones pertinentes, haga clic en el botón Siguiente.

![New API Management service][New API Management service]

Escriba **Contoso Ltd.** en **Nombre de organización** y escriba su dirección de correo electrónico en el campo de correo electrónico del administrador.

> Esta dirección de correo electrónico se utiliza para notificaciones por parte del sistema Administración de API. Para obtener más información, consulte [Configuración de notificaciones][Configuración de notificaciones]

Haga clic en la casilla para crear su instancia de servicio.

![New API Management service][New API Management service]

![New API Management service][New API Management service]

Una vez creada la instancia de servicio, el paso siguiente es crear una API.

## <a name="create-api"> </a>Creación de una API

Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación cliente. Las operaciones API se realizan con proxy en servicios web existentes.

Cada instancia del servicio Administración de API viene previamente configurada con una API Eco de ejemplo que devuelve la entrada que se le envió. Para usarla, puede invocar cualquier verbo HTTP y el valor devuelto será igual a los encabezados y el cuerpo que envió.

Este tutorial usa el servicio web <http://echoapi.cloudapp.net/api> para crear una nueva API en Administración de API llamada **Mi servicio Eco**.

Las API se crean y configuran desde la consola de Administración de API, a la que se tiene acceso a través del Portal de administración de Azure. Para llegar a la consola de administración de API, haga clic en **Consola de administración** en el Portal de Azure de su servicio Administración de API.

![New API Management console][New API Management console]

Para crear **Mi API Eco**, haga clic en **API** en el menú **Administración de API** situado a la izquierda y, a continuación, haga clic en **Agregar API**.

![Create API][Create API]

![Add new API][Add new API]

Para configurar la nueva API se usan los tres campos siguientes.

-   Escriba **Mi API Eco** en el cuadro de texto **Título de la API web**. **Título de la API web** proporciona un nombre único y descriptivo para la API. Se muestra en el portal para desarrolladores y en el Portal de administración.
-   Escriba **<http://echoapi.cloudapp.net/api>** en **Dirección URL del servicio web**. **Dirección URL del servicio web** hace referencia al servicio HTTP que implementa la API. Administración de API envía las solicitudes a esta dirección.
-   Escriba **mieco** en **Sufijo de dirección URL API web**. El **Sufijo de dirección URL API web** se anexa a la dirección URL base para el servicio Administración de API. Las API compartirán una dirección URL base común y se distinguirán por un sufijo único anexado después de la base.

Haga clic en **Guardar** para crear la API. Una vez creada la nueva API, su página de resumen se mostrará en el Portal de administración.

![API summary][API summary]

La sección API tiene cuatro pestañas. La pestaña **Resumen** muestra métricas e información básicas acerca de la API. La pestaña **Configuración** se usa para ver y editar la configuración para una API, incluidas las credenciales de autenticación para el servicio back-end. La pestaña **Operaciones** se usa para administrar las operaciones de la API (vea el siguiente paso del tutorial) y la pestaña **Problemas** se puede usar para ver problemas de los que informan los desarrolladores que usan sus API.

> La API Eco de ejemplo no usa autenticación. Si desea obtener más información acerca de la configuración de autenticación, consulte [Definición de la configuración de la API][Definición de la configuración de la API].

Una vez creada la API y definida su configuración, el paso siguiente es agregar las operaciones a dicha API. Las definiciones de operación se usan para validar las solicitudes entrantes y para generar documentación automáticamente.

## <a name="add-operation"> </a>Agregar una operación

Haga clic en **Operaciones** para mostrar el panel de operaciones de la API. Dado que todavía no hemos agregado ninguna operación, no se muestra nada.

![Operaciones][Operaciones]

Haga clic en **Agregar operación** para agregar una nueva operación. Se mostrará la ventana **Nueva operación** y la pestaña **Firma** se seleccionará de forma predeterminada.

![Operation signature][Operation signature]

En este ejemplo, especificaremos una operación GET en el servicio de eco. Escriba los siguientes valores en los campos en la pestaña **Firma**.

-   Escriba **GET** en el cuadro de texto **Verbo HTTP**. A medida que escribe, puede seleccionar **GET** en la lista mostrada de verbos http.
-   Escriba **/resource** en el cuadro de texto **Modelo de URL**.
-   Escriba **Recurso GET** en el cuadro de texto **Nombre para mostrar**.
-   Escriba **Demostración de una llamada GET en un recurso de ejemplo. Se controla mediante un back-end "eco" que devuelve una respuesta igual a la solicitud (los encabezados y el cuerpo suministrado se devuelven como se recibieron).** en el cuadro de texto **Descripción**. Esta descripción se usa para generar documentación para esta operación cuando los desarrolladores usan esta API.

Haga clic en **Parámetros** para configurar los parámetros de la cadena de consulta para esta operación. En este ejemplo, hay dos parámetros de cadena de consulta. Para agregar un parámetro de consulta, haga clic en **Agregar parámetro de consulta** y especifique los siguientes valores.

Para el primer parámetro de consulta, configure los siguientes valores.

-   Escriba **param1** en el cuadro de texto **Nombre**.
-   Escriba **Parámetro de ejemplo necesario.** en el cuadro de texto **Descripción**.
-   Haga clic en el campo **Tipo** y elija **cadena** en la lista. Los tipos admitidos son **cadena**, **número**, **booleano** y **fecha y hora**.
-   Haga clic en el campo **Valores**, escriba **ejemplo** en el cuadro de texto y haga clic en el signo más para agregar el texto de valor predeterminado al parámetro. Para agregar el texto predeterminado, haga clic en cualquier lugar fuera del campo **Valores** para descartar la ventana para agregar valores.
-   Active la casilla **Requerido**.

Para el segundo parámetro de consulta, escriba los siguientes valores.

-   **Name**: **param2**
-   **Descripción**: **Otro parámetro de ejemplo; establecer en no requerido.**
-   **Tipo**: **número**

Es recomendable proporcionar ejemplos de respuestas para todos los códigos de estado que puede producir la operación. Cada código de estado puede tener más de un ejemplo de cuerpo de respuesta, uno para cada tipo de contenido admitido. En este tutorial, hemos agregado un código de respuesta **200 OK**.

Haga clic en **Agregar** en la sección Respuestas, comience a escribir **200** en el cuadro de texto y después seleccione **200 OK** en la lista desplegable.

![Add response][Add response]

Una vez seleccionado **200 OK**, se agrega un nuevo código de respuesta a la operación y se muestra la ventana de respuestas. Escriba **Devuelto en todos los casos.** en el cuadro de texto **Descripción**.

![Add response][Add response]

> **Agregar representación** se usa para configurar respuestas en varias representaciones. Para obtener más información, consulte [Respuestas][Respuestas].

Haga clic en **Guardar** para agregar la operación recientemente configurada a la API.

## <a name="add-api-to-product"> </a>Agregar la nueva API al producto

Para poder crear llamadas de API, previamente los desarrolladores deben suscribirse al producto. Un producto proporciona acceso a una o varias API y puede contener restricciones de acceso como cuotas de uso y límites de tasa. En este paso del tutorial agregará Mi API Eco a un producto existente.

Haga clic en **Productos** en el menú **Administración de API** a la izquierda de la vista y configure los productos disponibles en esta instancia de API.

![Productos][Productos]

De forma predeterminada, cada instancia de Administración de API incluye dos productos de ejemplo:

-   **Inicio**
-   **Sin límite**

En este tutorial usaremos el producto **Inicio**. Haga clic en **Inicio** para ver la configuración, incluidas las API asociadas con ese producto.

![Add API][Add API]

Haga clic en **Agregar API al producto**.

![Add API][Add API]

Active la casilla correspondiente a **Mi API Eco** y haga clic en **Guardar**.

![API added][API added]

Ahora que **Mi API Eco** está asociada a un producto, los desarrolladores pueden suscribirse a él y comenzar a usar dicha API.

> En este paso del tutorial se usa el producto Inicio, que ya viene previamente configurado y listo para usarse. Para obtener instrucciones paso a paso sobre la creación y publicación de un nuevo producto, consulte [Creación y publicación de un producto][Creación y publicación de un producto].

## <a name="subscribe"> </a>Suscripción al producto que contiene la API

Para realizar llamadas a una API, los desarrolladores deben suscribirse primero a un producto que les proporcione acceso a ella. Los desarrolladores pueden suscribirse a productos en el portal para desarrolladores; asimismo, los administradores pueden suscribir a los desarrolladores a productos en la consola de administración. De forma predeterminada, usted es un administrador, ya que creó la instancia de Administración de API en los pasos anteriores del tutorial, por lo que suscribirá una cuenta al producto **Inicio**.

Haga clic en **Desarrolladores** en el menú **Administración de API** a la izquierda de la vista y configure los desarrolladores en esta instancia de API.

![Desarrolladores][Desarrolladores]

Haga clic en el nombre del desarrollador para definir la configuración para el usuario, incluidas las suscripciones.

> En este ejemplo, estamos suscribiendo un desarrollador llamado Clayton Gragg. Si no tiene ninguna cuenta de desarrollador creada, puede suscribirse a la cuenta de administrador. Para obtener información acerca de la creación de cuentas de desarrollador, consulte [Administración de cuentas de desarrollador en Administración de API de Azure][Administración de cuentas de desarrollador en Administración de API de Azure].

![Add subscription][Add subscription]

Haga clic en **Agregar suscripción**.

![Add subscription][Add subscription]

Active la casilla correspondiente a **Inicio** y haga clic en **Suscribirse**.

![Subscription added][Subscription added]

Una vez suscrita la cuenta de desarrollador, puede llamar a las API de ese producto.

## <a name="call-operation"> </a>Llamada a una operación desde el portal para desarrolladores

Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso del tutorial llamará al método Get que se agregó a **Mi API Eco**. Haga clic en **Portal para desarrolladores** en el menú que se encuentra en la parte superior derecha del Portal de administración.

![Portal para desarrolladores][Portal para desarrolladores]

Haga clic en **API** en el menú superior y, a continuación, haga clic en **Mi API Eco** para ver las operaciones disponibles.

![Portal para desarrolladores][Portal para desarrolladores]

Tenga en cuenta que se muestran la descripción y los parámetros que se agregaron cuando creó la operación, lo que proporciona documentación para los desarrolladores que usarán esta operación.

Haga clic en **Recurso GET** y, a continuación, en **Abrir consola**.

![Operation console][Operation console]

Escriba algunos valores para los parámetros, especifique su clave de desarrollador y haga clic en **Get HTTP**.

![HTTP Get][HTTP Get]

Después de invocar una operación, el portal para desarrolladores mostrará el campo **Dirección URL solicitada** en el servicio, así como los campos **Estado de respuesta**, **Encabezados de respuesta** y los **contenidos de respuesta**.

![Response][Response]

## <a name="view-analytics"> </a>Ver análisis

Para ver un análisis de **Mi API Eco**, vuelva al Portal de administración seleccionando **Administrar** en el menú de usuario situado en la parte superior del portal para desarrolladores.

![Manage][Manage]

La vista predeterminada del Portal de administración es el Panel, que proporciona información general de su instancia de Administración de API.

![Panel][Panel]

Pase el mouse por encima del gráfico de Mi API Eco para ver las métricas específicas de uso de la API durante un período de tiempo determinado.

> Si no ve ninguna línea en el gráfico, vuelva al portal para desarrolladores y haga algunas llamadas a la API, espere unos instantes y vuelva al Panel

![Analytics][Analytics]

Haga clic en **Ver detalles** para ver la página de resumen de la API, incluida una versión superior de las métricas mostradas.

![Resumen][Resumen]

Para obtener información detallada sobre métricas e informes, haga clic en **Análisis** en el menú **Administración de API** a la izquierda.

![Información general][Información general]

La sección **Análisis** consta de las cuatro pestañas siguientes.

-   **De un vistazo** proporciona métricas de uso global y de mantenimiento así como los principales desarrolladores, productos, API y operaciones.
-   **Uso** proporciona información detallada de llamadas de API y del ancho de banda que incluye una representación geográfica.
-   **Mantenimiento** se centra en los códigos de estado, las tasas de éxito de caché, los tiempos de respuesta y los tiempos de respuesta de API y de servicio.
-   **Actividad** proporciona informes que detallan la actividad específica por desarrollador, producto, API y operación.

## <a name="next-steps"> </a>Pasos siguientes

-   Consulte el resto de temas en el tutorial [Introducción a la configuración de API avanzada][Introducción a la configuración de API avanzada].

  [Creación de una instancia de Administración de API]: #create-service-instance
  [Creación de una API]: #create-api
  [Agregar una operación]: #add-operation
  [Agregar la nueva API al producto]: #add-api-to-product
  [Suscripción al producto que contiene la API]: #subscribe
  [Llamada a una operación desde el portal para desarrolladores]: #call-operation
  [Ver análisis]: #view-analytics
  [Pasos siguientes]: #next-steps
  [Evaluación gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a
  [Portal de administración]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Configuración de notificaciones]: ../api-management-howto-configure-notifications
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Definición de la configuración de la API]: ../api-management-howto-create-apis/#configure-api-settings
  [Operaciones]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Add response]: ./media/api-management-get-started/api-management-add-response.png
  [Respuestas]: ../api-management-howto-add-operations/#responses
  [Productos]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Creación y publicación de un producto]: ../api-management-howto-add-products
  [Desarrolladores]: ./media/api-management-get-started/api-management-developers.png
  [Administración de cuentas de desarrollador en Administración de API de Azure]: ../api-management-howto-create-or-invite-developers/
  [Add subscription]: ./media/api-management-get-started/api-management-add-subscription.png
  [Subscription added]: ./media/api-management-get-started/api-management-subscription-added.png
  [Portal para desarrolladores]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [Manage]: ./media/api-management-get-started/api-management-manage-menu.png
  [Panel]: ./media/api-management-get-started/api-management-dashboard.png
  [Analytics]: ./media/api-management-get-started/api-management-mouse-over.png
  [Resumen]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [Información general]: ./media/api-management-get-started/api-management-analytics-overview.png
  [Introducción a la configuración de API avanzada]: ../api-management-get-started-advanced

<!--HONumber=46--> 

<!--HONumber=46--> 
 