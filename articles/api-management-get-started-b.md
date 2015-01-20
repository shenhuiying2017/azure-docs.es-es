<properties pageTitle="Introducción a la Administración de API de Azure" metaKeywords="" description="Vea cómo crear API, operaciones y comenzar a usar Administración de API." metaCanonical="" services="api-management" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="sdanie" />

# Introducción a Administración de API de Azure

En esta guía se muestra cómo empezar a usar rápidamente la Administración de API y hacer la primera llamada API.

> para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

El primer paso para trabajar con Administración de API es crear una instancia de servicio. Inicie sesión en el [Portal de administración][] y haga clic en **Nuevo**, **Servicios de aplicaciones**, **Administración de API**, **Crear**.

![API Management new instance][api-management-create-instance-menu]

En **URL**, especifique el nombre de subdominio único que desee usar para la dirección URL de servicio.

Elija la **suscripción** deseada y la **Región** para la instancia de servicio. Después de realizar las selecciones pertinentes, haga clic en el botón Siguiente.

![New API Management service][api-management-create-instance-step1]

Escriba **Contoso Ltd.** en **Nombre de organización** y especifique su dirección de correo electrónico en el campo de correo electrónico del administrador.

>Esta dirección de correo electrónico se utiliza para notificaciones por parte del sistema Administración de API. Para obtener más información, consulte [Configuración de notificaciones][]

![New API Management service][api-management-create-instance-step2]

Las instancias de servicio de Administración de API están disponibles en dos niveles: Desarrollador y estándar. De forma predeterminada, las nuevas instancias de servicio de administración de API se crean en el nivel de desarrollador. Para seleccionar el nivel estándar, active la casilla **Configuración avanzada** y seleccione el nivel Estándar de nivel en la pantalla siguiente.

>Microsoft Azure ofrece dos niveles en los que puede ejecutar el servicio de Administración de API: Desarrollador y estándar. El nivel de desarrollador está orientado al desarrollo, las pruebas y los programas de API piloto para los que la disponibilidad no es un problema. En el nivel estándar, puede escalar el número de unidades reservadas para administrar más tráfico. El nivel estándar proporciona los servicios de administración de API con la mayor potencia de procesamiento y rendimiento. Este tutorial se puede completar con cualquier nivel. Para obtener más información acerca de los niveles de administración de API, consulte [Precios de administración de API][].

Haga clic en la casilla para crear su instancia de servicio.

![New API Management service][api-management-instance-created]

Una vez creada la instancia de servicio, el paso siguiente es crear una API.

## <a name="create-api"> </a>Creación de una API

Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación cliente. Las operaciones API se realizan con proxy en servicios web existentes.

Cada instancia del servicio Administración de API viene previamente configurada con una Echo API de ejemplo en la que puede invocar cualquier verbo HTTP, y el valor devuelto será igual a los encabezados y el cuerpo que envió. Este tutorial usa el servicio web de back-end en la Echo API para crear una nueva API en la Administración de API denominada **My Echo Service**.

Las API se crean y configuran desde la consola de Administración de API, a la que se tiene acceso a través del Portal de administración de Azure. Para llegar a la consola de administración de API, haga clic en **Consola de administración** en el Portal de Azure de su servicio de Administración de API.

![New API Management console][api-management-management-console]

Para crear **My Echo API**, haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Agregar API**.

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

Para configurar la nueva API se usan los tres campos siguientes.

-	Escriba **My Echo API** en el cuadro de texto **Título de la API web**. **El título de la API web** proporciona un nombre único y descriptivo para la API. Se muestra en el portal para desarrolladores y en el Portal de administración.
-	Escriba **http://echoapi.cloudapp.net/api** en **Dirección URL del servicio web**. **La dirección URL del servicio web** hace referencia al servicio HTTP que implementa la API. Administración de API envía las solicitudes a esta dirección.
-	Escriba **myecho** en **Sufijo de dirección URL API web**. **El sufijo de dirección URL API web** se anexa a la dirección URL base para el servicio Administración de API. Las API compartirán una dirección URL base común y se distinguirán por un sufijo único anexado después de la base.
-	**El esquema de dirección URL de la API web** determina los protocolos que pueden utilizarse para tener acceso a la API. HTTPs es el protocolo especificado de forma predeterminada.

Haga clic en **Guardar** para crear la API. Una vez creada la nueva API, su página de resumen se mostrará en el Portal de administración.

![API summary][api-management-new-api-summary]


>La API echo de ejemplo no usa autenticación. Si desea obtener más información acerca de la configuración de autenticación, consulte [Definición de la configuración de la API][].


## <a name="add-operation"> </a>Agregar una operación

Haga clic en **Operaciones** para mostrar el panel de operaciones de la API. Las definiciones de operación se usan para validar las solicitudes entrantes y para generar documentación automáticamente. Dado que todavía no hemos agregado ninguna operación, no se muestra nada.

![Operations][api-management-myecho-operations]

Haga clic en **Agregar operación** para agregar una nueva operación. Se mostrará la ventana **Nueva operación** y la pestaña **Firma** se seleccionará de forma predeterminada.

![Operation signature][api-management-operation-signature]

En este ejemplo, especificaremos una operación GET en el servicio de eco. Escriba los siguientes valores en los campos en la pestaña **Firma**.

-	Escriba **GET** en el cuadro de texto **Verbo HTTP**. A medida que escribe, puede seleccionar **GET** en la lista mostrada de verbos http.
-	Escriba **/resource** en el cuadro de texto **Modelo de URL**.
-	Escriba **Recurso GET** en el cuadro de texto **Nombre**.
-	Escriba **Demostración de una llamada GET en un recurso de ejemplo. Se controla mediante un back-end "echo" que devuelve una respuesta igual a la solicitud (los encabezados y el cuerpo suministrados se devuelven como se recibieron).** en el cuadro de texto **Descripción**. Esta descripción se usa para generar documentación para esta operación cuando los desarrolladores usan esta API.

Haga clic en **Parámetros** para configurar los parámetros de la cadena de consulta para esta operación. Para agregar un parámetro de consulta, haga clic en **Agregar parámetro de consulta** y especifique los siguientes valores.

-	Escriba **param1** en el cuadro de texto **Nombre**.
-	Escriba un **Parámetro de ejemplo necesario.** en el cuadro de texto **Descripción**.
-	Haga clic en el campo **Tipo** y elija **cadena** en la lista. Los tipos admitidos son **cadena**, **número**, **booleano** y **fecha y hora**.
-	Haga clic en el campo **Valores**, escriba **ejemplo** en el cuadro de texto y haga clic en el signo más para agregar el texto de valor predeterminado al parámetro. Para agregar el texto predeterminado, haga clic en cualquier lugar fuera del campo **Valores** para descartar la ventana para agregar valores.
-	Active la casilla **Requerido**.

Haga clic en **Guardar** para agregar la operación recientemente configurada a la API.


## <a name="add-api-to-product"> </a>Agregar la nueva API al producto

Para poder crear llamadas de API, previamente los desarrolladores deben suscribirse al producto. Un producto proporciona acceso a una o varias API y puede contener restricciones de acceso como cuotas de uso y límites de tasa. En este paso del tutorial agregará Mi API Eco a un producto existente.

Haga clic en **Productos** en el menú **Administración de API** a la izquierda de la vista y configure los productos disponibles en esta instancia de API.

![Products][api-management-list-products]

De forma predeterminada, cada instancia de Administración de API incluye dos productos de ejemplo:

-	**Starter**
-	**Unlimited**

En este tutorial usaremos el producto **Starter**. Haga clic en **Inicio** para ver la configuración, incluidas las API asociadas con ese producto.

![Add API][api-management-add-api-to-product]

Haga clic en **Agregar API al producto**.

![Add API][api-management-add-myechoapi-to-product]

Active la casilla correspondiente a **My Echo API** y haga clic en **Guardar**.

![API added][api-management-api-added-to-product]

Ahora que **My Echo API** se ha asociado a un producto, los desarrolladores pueden suscribirse a esta API y comenzar a usarla.

>En este paso del tutorial se usa el producto **Starter**, que viene previamente configurado y listo para usarse. Para obtener instrucciones paso a paso sobre la creación y publicación de un nuevo producto, consulte [Creación y publicación de un producto][].

El usuario Administrador se suscribe automáticamente a todos los productos y puede obtener acceso a las API a las que proporcionan acceso. Por consiguiente, no es necesario suscribirse manualmente al producto recién creado para poder realizar una llamada.

## <a name="call-operation"> </a>Llamada a una operación desde el portal para desarrolladores

Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso del tutorial llamará al método Get que se agregó a **My Echo API**. Haga clic en **Portal para desarrolladores** en el menú que se encuentra en la parte superior derecha del Portal de administración.

![Developer portal][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y, a continuación, haga clic en **My Echo API** para ver las operaciones disponibles.

![Developer portal][api-management-developer-portal-myecho-api]

Tenga en cuenta que se muestran la descripción y los parámetros que se agregaron cuando creó la operación, lo que proporciona documentación para los desarrolladores que usarán esta operación.

Haga clic en **Recurso GET** y, a continuación, en **Abrir consola**. 

![Operation console][api-management-developer-portal-myecho-api-console]

Escriba algunos valores para los parámetros, especifique su clave de desarrollador y haga clic en **HTTP Get**.

€

Después de invocar una operación, el portal para desarrolladores mostrará el campo **Dirección URL solicitada** en el servicio de back-end, así como los campos **Estado de respuesta**, **Encabezados de respuesta** y **Contenido de respuesta**. 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>Pasos siguientes

-   Configuración de directivas
-   Personalización del portal para desarrolladores
-   Seguimiento de llamadas con API Inspector

[Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/

[Creación de una instancia de Administración de API]: #create-service-instance
[Creación de una API]: #create-api
[Agregar una operación]: #add-operation
[Agregar la nueva API al producto]: #add-api-to-product
[Suscripción al producto que contiene la API]: #subscribe
[Llamada a una operación desde el portal para desarrolladores]: #call-operation
[Visualización de análisis]: #view-analytics
[Pasos siguientes]: #next-steps

[Definición de la configuración de la API]: ../api-management-howto-create-apis/#configure-api-settings
[Configuración de notificaciones]: ../api-management-howto-configure-notifications
[Respuestas]: ../api-management-howto-add-operations/#responses
[Creación y publicación de un producto]: ../api-management-howto-add-products
[Introducción a la configuración de API avanzada]: ../api-management-get-started-advanced
[Precios de Administración de API]: http://azure.microsoft.com/pricing/details/api-management/
[Portal de administración]: https://manage.windowsazure.com/

[Configuración de directivas]: ../api-management-howto-policies
[Personalización del portal para desarrolladores]: ../api-management-customize-portal
[Seguimiento de llamadas mediante el inspector de API]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->
