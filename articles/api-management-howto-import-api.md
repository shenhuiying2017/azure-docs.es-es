<properties 
	pageTitle="Conceptos clave de Administración de API" 
	description="Obtenga información acerca de las API, los productos, los roles, los grupos y otros conceptos clave de Administración de API." 
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

# Importación de la definición de una API con operaciones en Administración de API de Azure

En Administración de API (vista previa) se pueden crear nuevas API y agregar las operaciones manualmente o se puede importar la API junto con las operaciones en un solo paso.

Las API y sus operaciones se pueden importar con los siguientes formatos.

-   WADL
-   Swagger

En esta guía se muestra cómo crear una API e importar sus operaciones en un solo paso.

> Para obtener información sobre la creación manual de una API y la incorporación de operaciones, consulte [Creación de API][Creación de API] y [Incorporación de operaciones a una API][Incorporación de operaciones a una API].

## En este tema

-   [Importación de una API][Importación de una API]
-   [Exportación de una API][Exportación de una API]
-   [Pasos siguientes][Pasos siguientes]

## <a name="import-api"> </a>Importación de una API

Para crear y configurar una API, haga clic en la **consola de administración** en el Portal de Azure del servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![Management console][Management console]

Haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Importar API**.

![Import API][Import API]

La ventana **Importar API** tiene tres pestañas que corresponden a tres formas de proporcionar la especificación de API.

-   **Desde el portapapeles** permite pegar la especificación de API en el cuadro de texto que se designe.
-   **Desde el archivo** permite buscar el archivo que contiene la especificación de API y seleccionarlo.
-   **Desde URL** permite suministrar la dirección URL de la especificación para la API.

![Import API format][Import API format]

Después de proporcionar la especificación de API, use los botones de radio de la derecha para indicar el formato de especificación. Se admiten los siguientes formatos.

-   WADL
-   Swagger

A continuación, introduzca un **sufijo de URL de la API web**. Este se anexa a la dirección URL base del servicio Administración de API. La dirección URL base es común para todas las API hospedadas en cada instancia de un servicio Administración de API. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de una instancia específica de un servicio de Administración de API.

Una vez introducidos todos los valores, haga clic en **Guardar** para crear la API y las operaciones asociadas.

## <a name="export-api"> </a> Exportación de una API

Además de importar nuevas API, puede exportar las definiciones de sus API desde la consola de administración. Para ello, haga clic en **Exportar API** en la pestaña **Resumen** de la **API**.

![Export API][Export API]

Las API se pueden exportar con WADL o Swagger. Seleccione el formato que desee, haga clic en **Guardar** y elija la ubicación en la que desea guardar el archivo.

![Export API format][Export API format]

## <a name="next-steps"> </a>Pasos siguientes

Una vez creada una API e importadas las operaciones, se pueden revisar y definir las configuraciones adicionales, agregar la API a un producto y publicarlo para ponerlo a disposición de los desarrolladores. Para obtener más información, consulte las siguientes guías.

-   [Definición de la configuración de la API][Definición de la configuración de la API]
-   [Creación y publicación de un producto][Creación y publicación de un producto]

  [Creación de API]: ../api-management-howto-create-apis
  [Incorporación de operaciones a una API]: ../api-management-howto-add-operations
  [Importación de una API]: #import-api
  [Exportación de una API]: #export-api
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [Management console]: ./media/api-management-howto-import-api/api-management-management-console.png
  [Import API]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
  [Import API format]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
  [Export API]: ./media/api-management-howto-import-api/api-management-export-api.png
  [Export API format]: ./media/api-management-howto-import-api/api-management-export-api-format.png
  [Definición de la configuración de la API]: ../api-management-howto-create-apis/#configure-api-settings
  [Creación y publicación de un producto]: ../api-management-howto-add-products

<!--HONumber=46--> 
