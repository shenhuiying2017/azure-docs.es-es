<properties 
	pageTitle="Creación de API en Administración de API de Azure" 
	description="Obtenga información acerca de cómo crear y configurar las API en Administración de API de Azure." 
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

# Creación de API en Administración de API de Azure

En Administración de API (vista previa), una API representa un conjunto de operaciones que las aplicaciones cliente pueden invocar. Se crean nuevas API en la consola de administración y luego se agregan las operaciones que se deseen. Una vez agregadas las operaciones, la API se agrega a un producto y se puede publicar. Una vez publicada una API, pueden usarla quienes estén suscritos a ella, así como los desarrolladores.

En esta guía se muestra el primer paso del proceso: creación y configuración de una nueva API en Administración de API. Para obtener más información sobre cómo agregar operaciones y publicar un producto, consulte [Incorporación de operaciones a una API][Incorporación de operaciones a una API] y [Creación y publicación de un producto][Creación y publicación de un producto].

## En este tema

-   [Creación de una API][Creación de una API]
-   [Definición de la configuración de la API][Definición de la configuración de la API]
-   [Pasos siguientes][Pasos siguientes]

## <a name="create-new-api"> </a>Creación de una API

Para crear y configurar una API, haga clic en la **consola de administración** en el Portal de Azure de la instancia del servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![Management console][Management console]

Haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Agregar API**.

![Create API][Create API]

Use la ventana **Agregar nueva API** para configurar la nueva API.

![Add new API][Add new API]

Para configurar la nueva API se usan los tres campos siguientes.

-   **Título de la API de web** proporciona un nombre único y descriptivo para la API. Se muestra en el portal para desarrolladores y en el Portal de administración.
-   **Dirección URL del servicio web** hace referencia al servicio HTTP que implementa la API. Administración de API envía las solicitudes a esta dirección.
-   El **Sufijo de dirección URL API web** se anexa a la dirección URL base para el servicio Administración de API. La dirección URL base es común para todas las API hospedadas en una instancia del servicio Administración de API. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de un publicador determinado.

Una vez configurados los tres valores, haga clic en **Guardar**. Una vez creada la nueva API, su página de resumen se mostrará en el Portal de administración.

![API summary][API summary]

## <a name="configure-api-settings"> </a>Definición de la configuración de la API

Puede usar la pestaña **Configuración** para comprobar y editar la configuración de una API. **Título de la API de web**, **URL de servicio Web** y **Sufijo de URL de la API de web** se establecen inicialmente al crear la API y se pueden modificar aquí. **Descripción** ofrece una descripción opcional y **Con credenciales** permite configurar la autenticación básica HTTP.

![API settings][API settings]

Para configurar la autenticación básica HTTP para el servicio web que implementa la API, seleccione **Básica** en la lista desplegable **Con credenciales** y especifique las credenciales que desee.

![Basic authentication settings][Basic authentication settings]

Haga clic en **Guardar** para guardar los cambios que efectúe a la configuración de la API.

## <a name="next-steps"> </a>Pasos siguientes

Una vez creada una API y definida la configuración, los pasos siguientes permiten agregar las operaciones a la API, agregar la API a un producto y publicarlo para ponerlo a disposición de los desarrolladores. Para obtener más información, consulte las dos guías siguientes.

-   [Incorporación de operaciones a una API][Incorporación de operaciones a una API]
-   [Creación y publicación de un producto][Creación y publicación de un producto]

  [Incorporación de operaciones a una API]: ../api-management-howto-add-operations
  [Creación y publicación de un producto]: ../api-management-howto-add-products
  [Creación de una API]: #create-new-api
  [Definición de la configuración de la API]: #configure-api-settings
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [Management console]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [Create API]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Add new API]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [API summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [API settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Basic authentication settings]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 