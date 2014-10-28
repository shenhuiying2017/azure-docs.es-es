<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

# Introducción a Administración de API de Azure

En esta guía se muestra cómo empezar a usar rápidamente la Administración de API y hacer la primera llamada API.

> para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

El primer paso para trabajar con Administración de API es crear una instancia de servicio. Inicie sesión en el [Portal de administración][Portal de administración] y haga clic en **Nuevo**, **Servicios de aplicaciones**, **Administración de API**, **Crear**.

![API Management new instance][API Management new instance]

En **URL**, especifique un nombre de subdominio único que desee usar para la dirección URL de servicio.

Elija la información que desee en **Nivel de previos**, **Suscripción** y **Región** para la instancia de servicio. Para este tutorial se pueden utilizar todos los niveles de precios. Después de realizar las selecciones pertinentes, haga clic en el botón Siguiente.

![New API Management service][New API Management service]

Escriba **Contoso Ltd.** en **Nombre de organización** y escriba su dirección de correo electrónico en el campo de correo electrónico del administrador.

> Esta dirección de correo electrónico se utiliza para notificaciones por parte del sistema Administración de API. Para obtener más información, consulte [Configuración de notificaciones][Configuración de notificaciones]

Haga clic en la casilla para crear su instancia de servicio.

![New API Management service][1]

![New API Management service][2]

Una vez creada la instancia de servicio, el paso siguiente es crear una API.

## <a name="create-api"> </a>Creación de una API

Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación cliente. Las operaciones API se realizan con proxy en servicios web existentes.

Cada instancia del servicio Administración de API viene previamente configurada con una Echo API de ejemplo en la que puede invocar cualquier verbo HTTP, y el valor devuelto será igual a los encabezados y el cuerpo que envió. Este tutorial usa el servicio web backend en la Echo API para crear otra API en Administración de API denominada **My Echo Service**.

Las API se crean y configuran desde la consola de Administración de API, a la que se tiene acceso a través del Portal de administración de Azure. Para llegar a la consola de administración de API, haga clic en **Consola de administración** en el Portal de Azure de su servicio Administración de API.

![New API Management console][New API Management console]

Para crear **My Echo API**, haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Agregar API**.

![Create API][Create API]

![Add new API][Add new API]

Para configurar la nueva API se usan los tres campos siguientes.

-   Escriba **Mi API Eco** en el cuadro de texto **Título de la API web**. **Título de la API web** proporciona un nombre único y descriptivo para la API. Se muestra en el portal para desarrolladores y en el Portal de administración.
-   Escriba **<http://echoapi.cloudapp.net/api>** en **Dirección URL del servicio web**. **Dirección URL del servicio web** hace referencia al servicio HTTP que implementa la API. Administración de API envía las solicitudes a esta dirección.
-   Escriba **mieco** en **Sufijo de dirección URL API web**. El **Sufijo de dirección URL API web** se anexa a la dirección URL base para el servicio Administración de API. Las API compartirán una dirección URL base común y se distinguirán por un sufijo único anexado después de la base.

Haga clic en **Guardar** para crear la API. Una vez creada la nueva API, su página de resumen se mostrará en el Portal de administración.

![API summary][API summary]

> La API Eco de ejemplo no usa autenticación. Si desea obtener más información acerca de la configuración de autenticación, consulte [Definición de la configuración de la API][Definición de la configuración de la API].

## <a name="add-operation"> </a>Agregar una operación

Haga clic en **Operaciones** para mostrar el panel de operaciones de la API. Las definiciones de operación se usan para validar las solicitudes entrantes y para generar documentación automáticamente. Dado que todavía no hemos agregado ninguna operación, no se muestra nada.

![Operaciones][Operaciones]

Haga clic en **Agregar operación** para agregar una nueva operación. Se mostrará la ventana **Nueva operación** y la pestaña **Firma** se seleccionará de forma predeterminada.

![Operation signature][Operation signature]

En este ejemplo, especificaremos una operación GET en el servicio de eco. Escriba los siguientes valores en los campos en la pestaña **Firma**.

-   Escriba **GET** en el cuadro de texto **Verbo HTTP**. A medida que escribe, puede seleccionar **GET** en la lista mostrada de verbos http.
-   Escriba **/resource** en el cuadro de texto **Modelo de URL**.
-   Escriba **Recurso GET** en el cuadro de texto **Nombre para mostrar**.
-   Escriba **Demostración de una llamada GET en un recurso de ejemplo. Se controla mediante un backend "eco" que devuelve una respuesta igual a la solicitud (los encabezados y el cuerpo suministrados se devuelven como se recibieron).** en el cuadro de texto **Descripción**. Esta descripción se usa para generar documentación para esta operación cuando los desarrolladores usan esta API.

Haga clic en **Parámetros** para configurar los parámetros de la cadena de consulta para esta operación. Para agregar un parámetro de consulta, haga clic en **Agregar parámetro de consulta** y especifique los siguientes valores.

-   Escriba **param1** en el cuadro de texto **Nombre**.
-   Escriba **Parámetro de ejemplo necesario.** en el cuadro de texto **Descripción**.
-   Haga clic en el campo **Tipo** y elija **cadena** en la lista. Los tipos admitidos son **cadena**, **número**, **booleano** y **fecha y hora**.
-   Haga clic en el campo **Valores**, escriba **ejemplo** en el cuadro de texto y haga clic en el signo más para agregar el texto de valor predeterminado al parámetro. Para agregar el texto predeterminado, haga clic en cualquier lugar fuera del campo **Valores** para descartar la ventana para agregar valores.
-   Active la casilla **Requerido**.

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

![Add API][3]

Active la casilla correspondiente a **Mi API Eco** y haga clic en **Guardar**.

![API added][API added]

Ahora que **Mi API Eco** está asociada a un producto, los desarrolladores pueden suscribirse a él y comenzar a usar dicha API.

> En este paso del tutorial se usa el producto **Starter**, que viene previamente configurado y listo para usarse. Para obtener instrucciones paso a paso sobre la creación y publicación de un nuevo producto, consulte [Creación y publicación de un producto][Creación y publicación de un producto].

El usuario Administrador se suscribe automáticamente a todos los productos y puede obtener acceso a las API a las que proporcionan acceso. Por consiguiente, no es necesario suscribirse manualmente al producto recién creado para poder realizar una llamada.

## <a name="call-operation"> </a>Llamada a una operación desde el portal para desarrolladores

Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso del tutorial llamará al método Get que se agregó a **Mi API Eco**. Haga clic en **Portal para desarrolladores** en el menú que se encuentra en la parte superior derecha del Portal de administración.

![Portal para desarrolladores][Portal para desarrolladores]

Haga clic en **API** en el menú superior y, a continuación, haga clic en **Mi API Eco** para ver las operaciones disponibles.

![Portal para desarrolladores][4]

Tenga en cuenta que se muestran la descripción y los parámetros que se agregaron cuando creó la operación, lo que proporciona documentación para los desarrolladores que usarán esta operación.

Haga clic en **Recurso GET** y, a continuación, en **Abrir consola**.

![Operation console][Operation console]

Escriba algunos valores para los parámetros, especifique su clave de desarrollador y haga clic en **Get HTTP**.

![HTTP Get][HTTP Get]

Después de invocar una operación, el portal para desarrolladores mostrará el campo **Dirección URL solicitada** en el servicio, así como los campos **Estado de respuesta**, **Encabezados de respuesta** y los **contenidos de respuesta**.

![Response][Response]

## <a name="next-steps"> </a>Pasos siguientes

-   Configuración de directivas
-   Personalización del portal para desarrolladores
-   Seguimiento de llamadas con API Inspector

  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/
  [Portal de administración]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Configuración de notificaciones]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Definición de la configuración de la API]: ../api-management-howto-create-apis/#configure-api-settings
  [Operaciones]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Productos]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Creación y publicación de un producto]: ../api-management-howto-add-products
  [Portal para desarrolladores]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
