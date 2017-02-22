---
title: "Exportación de una API de Azure hospedada en PowerApps y Microsoft Flow | Microsoft Docs"
description: "Introducción a la exposición de una API que se hospeda en App Service a PowerApps y Microsoft Flow"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportación de una API de Azure hospedada en PowerApps y Microsoft Flow

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>Creación de API personalizadas para PowerApps y Microsoft Flow

La [plataforma de aplicación empresarial de Microsoft](https://businessplatform.microsoft.com/) incluye una serie de productos que le ayudan a los usuarios avanzados a realizar más tareas. [PowerApps](https://powerapps.com) es un servicio para generar y utilizar aplicaciones empresariales personalizadas que se conectan a los datos y funcionan en otras plataformas. [Microsoft Flow](https://flow.microsoft.com) facilita la automatización de los flujos de trabajo y los procesos empresariales en sus aplicaciones y servicios favoritos. Tanto PowerApps como Microsoft Flow vienen con una variedad de conectores integrados a los orígenes de datos, como Office 365, 365 Dynamics, Salesforce y mucho más. Sin embargo, los usuarios también necesitan aprovechar los orígenes de datos y las API que genera la organización.

De igual forma, los desarrolladores que desean exponer su API de manera más amplia dentro de la organización pueden querer que sus API estén disponibles para los usuarios de PowerApps y Microsoft Flow. Este tema le mostrará cómo exponer una API generada con Azure App Service o Azure Functions para PowerApps y Microsoft Flow. [Azure App Service](https://azure.microsoft.com/services/app-service/) es una oferta de plataforma como servicio que permite a los desarrolladores crear fácil y rápidamente aplicaciones web, móviles y de API a nivel empresarial. [Azure Functions](https://azure.microsoft.com/services/functions/) es una solución de proceso sin servidor basado en eventos que le permite crear rápidamente código que pueda reaccionar a otras partes del sistema y de escala a petición.

Para más información acerca de estos servicios, consulte:
- [PowerApps Guided Learning](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) (Aprendizaje guiado de PowerApps) 
- [Aprendizaje guiado de Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [¿Qué es App Service?](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Qué es Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Uso compartido de una definición de API

Las API a menudo se describen mediante un [documento de Open API](https://www.openapis.org/) (en ocasiones denominado documento de "Swagger"). Contiene toda la información sobre qué operaciones están disponibles y cómo se deben estructurar los datos. PowerApps y Microsoft Flow pueden crear API personalizadas para cualquier documento de Open API 2.0. Una vez creada una API personalizada, puede utilizarse exactamente de la misma forma que uno de los conectores integrados y se puede integrar rápidamente en una aplicación.

Azure App Service y Azure Functions tienen [compatibilidad integrada](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) para crear, hospedar y administrar un documento de Open API. Para crear un conector personalizado para una aplicación web, móvil, de API o de función, se necesitan dos pasos:

1. [Recuperación de la definición de la API de App Service o Azure Functions](#export)
2. [Importación de la definición de la API en PowerApps](#import)

Es posible que estos dos pasos deban llevarlos a cabo personas distintas de una organización, ya que un usuario concreto puede no tener permiso para realizar ambas acciones. En este caso, un desarrollador que tenga acceso de colaborador a la aplicación de App Service o de Azure Functions deberá obtener la definición de la API (un único archivo JSON) o un vínculo a esta. A continuación, deberá proporcionar esa definición a un propietario de PowerApps o Microsoft Flow. Ese propietario puede utilizar los metadatos para crear la API personalizada.

> [!NOTE]
> Como se está usando una copia de la definición de la API, PowerApps y Microsoft Flow no conocerán inmediatamente las actualizaciones o los cambios de la aplicación. Si está disponible una nueva versión de la API, se deben repetir estos pasos para la nueva versión. 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperación de la definición de la API de App Service o Azure Functions

En esta sección, exportará la definición a la API de App Service, que se usará más adelante en PowerApps.

1. Abra [Azure Portal](https://portal.azure.com) y vaya a la aplicación de App Service o Azure Functions.

    Si usa Azure App Service, seleccione **Definición de la API** de la lista de valores. 
    
    Si utiliza Azure Functions, seleccione **Configuración de Function App** y **Configurar metadatos de API**.

2. Si se ha proporcionado una definición de API, verá un botón **Export to PowerApps + Microsoft Flow** (Exportar a PowerApps + Microsoft Flow). Haga clic en este botón para iniciar la exportación.

3. Puede elegir **Download the API definition** (Descargar la definición de la API) u **Obtener un vínculo**. El resultado se proporcionará a PowerApps en la sección siguiente, independientemente de su elección. Seleccione una de estas opciones y siga las instrucciones.
 
4. Si la definición de la API incluye cualquier definición de seguridad, estas se indicarán en el paso 2. Durante la importación, PowerApps y Microsoft Flow las detectarán y solicitarán información de seguridad. Los servicios utilizan esto para iniciar la sesión de los usuarios y que puedan acceder a la API. Si la API requiere autenticación, asegúrese de que se captura como _definición de seguridad_ en el documento Open API.

    Recopile las credenciales relacionadas con cada definición para su uso en la sección siguiente. Para obtener una lista de proveedores de identidades admitidos de forma nativa por PowerApps y las credenciales que cada uno requiere, consulte [Register custom APIs in PowerApps] (Registro de API personalizadas en PowerApps) y [Registro de API personalizadas en Microsoft Flow].
 
> [!NOTE]
> Si utiliza la autenticación de Azure Active Directory, se necesitarán un nuevo registro de aplicación AAD que haya delegado el acceso a la API y una dirección URL de respuesta de _https://msmanaged-na.consent.azure-apim.net/redirect_. Consulte [este ejemplo](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) para información más detallada y sustituya la API por Azure Resource Manager.
>
> Si otra persona va a importar la definición de la API en PowerApps, proporcionará el identificador y el secreto del cliente **del nuevo registro**, así como la dirección URL de recursos de la API, además del archivo de definición de la API. Asegúrese de que estos secretos se administran de forma segura. **No comparta las credenciales de seguridad de la propia API.**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importación de la definición de la API en PowerApps y Microsoft Flow

En esta sección, creará una API personalizada en PowerApps y Microsoft Flow mediante la definición de API obtenida anteriormente. Las API personalizadas se comparten entre los dos servicios, por lo que solo tiene que importar la definición una vez. Para más información sobre las API personalizadas, consulte [Register custom APIs in PowerApps] (Registro de API personalizadas en PowerApps) y [Registro de API personalizadas en Microsoft Flow].

**Para importar en PowerApps:**

1. Abra el [portal web de PowerApps](https://web.powerapps.com), inicie sesión y seleccione **Connections** (Conexiones). Haga clic en **New connection** (Nueva conexión).

2. Seleccione **Custom** (Personalizada) y haga clic en **New custom API** (Nueva API personalizada).

3. Proporcione un nombre a la API y cargue la definición de Swagger o pegue la dirección URL de metadatos. Haga clic en **Siguiente**.

4. Si se le pide que proporcione detalles de autenticación, escriba los valores obtenidos en la sección anterior. Si no, siga con el paso siguiente.

5. Haga clic en **Create**(Crear).

**Para importar en Microsoft Flow:**

1. Abra el [portal web de Microsoft Flow](https://flow.microsoft.com/) e inicie sesión. 

2. Haga clic en el botón **Settings** (Configuración) situado en la esquina superior derecha de la página (similar a un icono de engranaje) y seleccione **Custom APIs** (API personalizadas). Haga clic en **Create custom API** (Crear API personalizada).

3. Cargue la definición de Swagger y haga clic en **Continue** (Continuar).

4. Si se le pide que proporcione detalles de autenticación, escriba los valores obtenidos en la sección anterior. Si no, siga con el paso siguiente.

5. Haga clic en la casilla de la parte superior de la pantalla.



[Register custom APIs in PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/ (Registro de API personalizadas en PowerApps)
[Registro de API personalizadas en Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/



<!--HONumber=Feb17_HO2-->


