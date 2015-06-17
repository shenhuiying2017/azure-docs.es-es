<properties 
	pageTitle="Creación y configuración de opciones de producto avanzadas en la Administración de API de Azure" 
	description="Obtenga información acerca de cómo configurar un producto con directivas de cuotas y limitaciones." 
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
	ms.date="02/11/2015" 
	ms.author="sdanie"/>

# Creación y definición de configuraciones de productos avanzadas en Administración de API de Azure

En Administración de API de Azure (vista previa), los productos son altamente configurables para cumplir los requisitos de los publicadores de API. En este tema se muestra cómo definir algunas de las configuraciones de productos avanzadas, como por ejemplo las directivas de límite de tasa y de cuota.

En este tutorial creará un producto Prueba gratuita que permite 10 llamadas por minuto, hasta un máximo de 200 llamadas a la semana, lo publicará y probará la directiva de límite de tasa.

## En este tema

-   [Creación de un producto][Creación de un producto]
-   [Agregar una API al producto][Agregar una API al producto]
-   [Configuración de las directivas de límite de tasa de llamadas y de cuota][Configuración de las directivas de límite de tasa de llamadas y de cuota]
-   [Publicación del producto][Publicación del producto]
-   [Suscripción de una cuenta de desarrollador al producto][Suscripción de una cuenta de desarrollador al producto]
-   [Llamada a una operación y prueba del límite de tasa][Llamada a una operación y prueba del límite de tasa]
-   [Pasos siguientes][Pasos siguientes]

## <a name="create-product"> </a>Creación de un producto

En este paso, creará un producto Prueba gratuita que no requiere aprobación de suscripción.

Para comenzar, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management console][API Management console]

Haga clic en **Productos** en el menú **Administración de API** a la izquierda para mostrar la página **Productos**.

![Add product][Add product]

Haga clic en **Agregar producto** para mostrar la ventana emergente **Agregar nuevo producto**.

![Add new product][Add new product]

Escriba **Prueba gratuita** en el cuadro de texto **Título**.

Escriba **Los suscriptores podrán realizar 10 llamadas/minuto hasta un máximo de 200 llamadas/semana; después, el acceso se denegará.** en el cuadro de texto **Descripción**.

Si desea que un administrador revise y acepte o rechace los intentos de suscripción a este producto, active **Requerir aprobación de suscripción**. Si la casilla no se activa, los intentos de suscripción se autoaprobarán. En este ejemplo, las suscripciones se aprueban automáticamente por lo que no tiene que activar la casilla.

Una vez especificados todos los valores, haga clic en **Guardar** para crear el producto.

![Product added][Product added]

De forma predeterminada, los usuarios pueden ver los nuevos productos en el grupo **Administradores**. Vamos a agregar el grupo **Desarrolladores**. Haga clic en **Prueba gratuita** y seleccione la pestaña **Visibilidad**.

> En Administración de API, los grupos se usan para administrar la visibilidad de productos para los desarrolladores. Los productos conceden visibilidad a los grupos y los desarrolladores pueden ver los productos visibles a los grupos a los que pertenecen y suscribirse a ellos. Para obtener información, consulte [Creación y uso de grupos en Administración de API de Azure][Creación y uso de grupos en Administración de API de Azure].

![Add developers group][Add developers group]

Active el grupo **Desarrolladores** y haga clic en **Guardar**.

## <a name="add-api"> </a>Agregar una API al producto

En este paso del tutorial, agregaremos la API Eco al nuevo producto Prueba gratuita.

> Cada instancia del servicio Administración de API viene previamente configurada con una API Eco que se puede usar para experimentar con Administración de API y aprender de esta. Para obtener información, consulte [Introducción a Administración de API de Azure][Introducción a la Administración de API de Azure].

Haga clic en **Productos** en el menú **Administración de API** a la izquierda y luego haga clic en **Prueba gratuita** para configurar el producto.

![Configure product][Configure product]

Haga clic en **Agregar API al producto**.

![Add API to product][Add API to product]

Active la casilla situada junto a **API Eco** y haga clic en **Guardar**.

![Add Echo API][Add Echo API]

## <a name="policies"> </a>Configuración de las directivas de límite de tasa de llamadas y de cuota

Los límites de tasa y las cuotas se configuran en el editor de directivas. Haga clic en **Directivas** en el menú **Administración de API** a la izquierda y seleccione **Prueba gratuita** en la lista desplegable **Producto de ámbito de directiva**.

![Product policy][Product policy]

Haga clic en **Agregar directiva** para importar la plantilla de directivas y comenzar a crear la directiva de límite de tasa y de cuota.

![Add policy][Add policy]

Para insertar directivas, posicione el cursor en la sección **inbound** u **outbound** de la plantilla de directivas. Las directivas de límite de tasa y de cuota son directivas de entrada, por lo que debe posicionar el cursor en el elemento inbound.

![Policy editor][Policy editor]

Las dos directivas que estamos agregando en este tutorial son **Límite de tasa de llamadas** y **Establecer cuota de uso**.

![Policy statements][Policy statements]

Una vez posicionado el cursor en el elemento de directiva **inbound**, haga clic en la flecha situada junto a **Límite de tasa de llamadas** para insertar su plantilla de directiva.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Límite de tasa de llamadas** se puede usar en el ámbito de producto y también en los ámbitos de API y de nombre de operación individual. En este tutorial solamente se usan directivas de ámbito de producto; por tanto, elimine los elementos **api** y **operation** del elemento **rate-limit**, tal y como se muestra en el siguiente ejemplo.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

En el producto **Prueba gratuita**, la tasa máxima de llamadas permitida es de 10 llamadas por minuto; por tanto, escriba **10** como el valor para el atributo calls y **60** para el atributo **renewal-period**.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Para configurar la directiva **Establecer cuota de uso**, posicione el cursor inmediatamente debajo del elemento **rate-limit** recién agregado dentro del elemento **inbound** y haga clic en la flecha situada a la izquierda de **Establecer cuota de uso**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Dado que esta directiva también está pensada para aplicarse en el ámbito de producto, elimine los elementos **api** y **operation** tal y como se muestra en el siguiente ejemplo.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Las cuotas se pueden basar en el número de llamadas por intervalo, ancho de banda o ambos. En este tutorial no estamos fijando límites en función del ancho de banda, por lo que puede eliminar el atributo **bandwidth**.

    <quota calls="number" renewal-period="seconds">
    </quota>

En el producto **Prueba gratuita**, la cuota es de 200 llamadas por semana. Especifique **200** como el valor del atributo calls y **604800** como el valor de renewal-period.

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> Los intervalos de directiva se especifican en segundos. Para calcular el intervalo para una semana, puede multiplicar el número de días (7) por el número de horas de un día (24) por el número de minutos de una hora (60) por el número de segundos de un minuto (60): 7 * 24 * 60 * 60 = 604800.

Cuando haya terminado de configurar la directiva, debe coincidir con el siguiente ejemplo.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
        
    </inbound>
    <outbound>
        
        <base />
        
        </outbound>
    </policies>

Una vez configuradas las directivas deseadas, haga clic en **Guardar**.

![Save policy][Save policy]

## <a name="publish-product"> </a> Publicación del producto

Ahora que las API se han agregado y las directivas configurado, los desarrolladores pueden usar el producto. Antes de que los desarrolladores usen el producto, se debe publicar. Haga clic en **Productos** en el menú **Administración de API** a la izquierda y luego haga clic en **Prueba gratuita** para configurar el producto.

![Configure product][Configure product]

Haga clic en **Publicar** y luego en **Sí, publicarlo** para confirmar la operación.

![Publish product][Publish product]

## <a name="subscribe-account"> </a>Suscripción de una cuenta de desarrollador al producto

Ahora que el producto se ha publicado, estará disponible para suscribirse a él y que los desarrolladores lo usen.

> Los administradores de una instancia de Administración de API se suscriben automáticamente a cada producto. En este paso del tutorial suscribiremos una de las cuentas de desarrollador que no es de administrador al producto Prueba gratuita. Si la cuenta de desarrollador es parte del rol Administradores, puede seguir con este paso aunque ya se haya suscrito.

Haga clic en **Desarrolladores** en el menú **Administración de API** a la izquierda y haga clic en el nombre de su cuenta de desarrollador. En este ejemplo utilizamos la cuenta del desarrollador **Clayton Gragg**.

![Configure developer][Configure developer]

Haga clic en **Agregar suscripción**.

![Add subscription][Add subscription]

Active la casilla situada junto a **Prueba gratuita** y haga clic en **Suscribirse**.

![Add subscription][1]

## <a name="test-rate-limit"> </a>Llamada a una operación y prueba del límite de tasa

Ahora que el producto Prueba gratuita está configurado y publicado, podemos llamar a algunas operaciones y probar la directiva de límite de tasa. Cambie al portal para desarrolladores haciendo clic en **Portal para desarrolladores** en el menú superior derecho.

![Portal para desarrolladores][Portal para desarrolladores]

Haga clic en **API** en el menú superior y seleccione **API Eco**.

![Portal para desarrolladores][2]

> Si solamente tiene una API configurada o visible en su cuenta, al hacer clic en API irá directamente a las operaciones de dicha API.

Seleccione la operación **Recurso GET** y haga clic en **Abrir consola**.

![Open console][Open console]

Mantenga los valores predeterminados de los parámetros y seleccione la clave de suscripción para el producto **Prueba gratuita**.

![Subscription key][Subscription key]

> Si tiene varias suscripciones, asegúrese de seleccionar la clave para **Prueba gratuita** ya que, de lo contrario, las directivas configuradas en los pasos anteriores no entrarán en vigor.

Haga clic en **Get HTTP** y vea la respuesta. Observe el **Estado de respuesta** de **200 OK**.

![Operation results][Operation results]

Haga clic en **Get HTTP** en una tasa mayor que la directiva del límite de tasa de 10 llamadas por minuto. Una vez superada la directiva del límite de tasa, se devolverá un estado de respuesta de **429 Demasiadas solicitudes**.

![Operation results][3]

**Encabezados de respuesta** y **Contenido de respuesta** indican el intervalo restante antes de que los reintentos sean correctos.

Cuando la directiva de límite de tasa de 10 llamadas por minuto se aplique, las llamadas posteriores no se podrán realizar hasta que hayan transcurrido 60 segundos desde la primera de las 10 llamadas correctas al producto antes de que se superara el límite de tasa. En este ejemplo, el intervalo restante es 43 segundos.

## <a name="next-steps"> </a>Pasos siguientes

-   Consulte el resto de temas en el tutorial [Introducción a la configuración de API avanzada][Introducción a la configuración de API avanzada].

  [Creación de un producto]: #create-product
  [Agregar una API al producto]: #add-api
  [Configuración de las directivas de límite de tasa de llamadas y de cuota]: #policies
  [Publicación del producto]: #publish-product
  [Suscripción de una cuenta de desarrollador al producto]: #subscribe-account
  [Llamada a una operación y prueba del límite de tasa]: #test-rate-limit
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Add product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Add new product]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Product added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [Creación y uso de grupos en Administración de API de Azure]: ../api-management-howto-create-groups
  [Add developers group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Configure product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [Add API to product]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Add Echo API]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Product policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Add policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Policy editor]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Policy statements]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Save policy]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [Publish product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Configure developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Add subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [Portal para desarrolladores]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Open console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Subscription key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Operation results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [Introducción a la configuración de API avanzada]: ../api-management-get-started-advanced

<!--HONumber=46--> 

<!--HONumber=46--> 
 