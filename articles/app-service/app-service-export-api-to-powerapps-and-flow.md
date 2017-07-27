---
title: "Exportación de una API hospedada en Azure a PowerApps y Microsoft Flow | Microsoft Docs"
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
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportación de una API hospedada en Azure a PowerApps y Microsoft Flow

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>Creación de conectores personalizados para PowerApps y Microsoft Flow

[PowerApps](https://powerapps.com) es un servicio para generar y utilizar aplicaciones empresariales personalizadas que se conectan a los datos y funcionan en otras plataformas. [Microsoft Flow](https://flow.microsoft.com) facilita la automatización de los flujos de trabajo y los procesos empresariales en sus aplicaciones y servicios favoritos. Tanto PowerApps como Microsoft Flow vienen con una variedad de conectores integrados a los orígenes de datos, como Office 365, 365 Dynamics, Salesforce y mucho más. Sin embargo, los usuarios también necesitan aprovechar los orígenes de datos y las API que genera la organización.

De igual forma, los desarrolladores que desean exponer su API de manera más amplia dentro de la organización pueden querer que sus API estén disponibles para los usuarios de PowerApps y Microsoft Flow. Este tema le mostrará cómo exponer una API generada con Azure App Service o Azure Functions para PowerApps y Microsoft Flow. [Azure App Service](https://azure.microsoft.com/services/app-service/) es una oferta de plataforma como servicio que permite a los desarrolladores crear fácil y rápidamente aplicaciones web, móviles y de API a nivel empresarial. [Azure Functions](https://azure.microsoft.com/services/functions/) es una solución de proceso sin servidor basado en eventos que le permite crear rápidamente código que pueda reaccionar a otras partes del sistema y de escala a petición.

Para más información acerca de estos servicios, consulte:
- [PowerApps Guided Learning](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) (Aprendizaje guiado de PowerApps) 
- [Aprendizaje guiado de Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [¿Qué es App Service?](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Qué es Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Uso compartido de una definición de API

Las API a menudo se describen mediante un [documento de Open API](https://www.openapis.org/) (en ocasiones denominado documento de "Swagger"). Contiene toda la información sobre qué operaciones están disponibles y cómo se deben estructurar los datos. PowerApps y Microsoft Flow pueden crear conectores personalizados para cualquier documento de Open API 2.0. Una vez creado un conector personalizado, puede utilizarse exactamente de la misma forma que uno de los conectores integrados y se puede integrar rápidamente en una aplicación.

Azure App Service y Azure Functions tienen [compatibilidad integrada](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) para crear, hospedar y administrar un documento de Open API. Para crear un conector personalizado para una aplicación web, móvil, una API o una aplicación de función, es necesario proporcionar una copia de la definición a PowerApps y Flow.

> [!NOTE]
> Como se está usando una copia de la definición de la API, PowerApps y Microsoft Flow no conocerán inmediatamente las actualizaciones o los cambios de la aplicación. Si está disponible una nueva versión de la API, se deben repetir estos pasos para la nueva versión. 

Para proporcionar a PowerApps y Microsoft Flow la definición de la API hospedada para la aplicación, siga estos pasos:

1. Abra [Azure Portal](https://portal.azure.com) y vaya a la aplicación de App Service o Azure Functions.

    Si usa Azure App Service, seleccione **Definición de la API** de la lista de valores. 
    
    Si usa Azure Functions, seleccione la aplicación de función y después elija **Características de la plataforma** y **Definición de la API**. En su lugar, también puede abrir la pestaña **Definición de API (vista previa)**.

2. Si se ha proporcionado una definición de API, verá un botón **Export to PowerApps + Microsoft Flow** (Exportar a PowerApps + Microsoft Flow). Haga clic en este botón para iniciar la exportación.

3. Seleccione el **Modo de exportación**. Esto determina los pasos que debe seguir para crear un conector. App Service ofrece dos opciones para proporcionar a PowerApps y Microsoft Flow la definición de la API:

    **Rápida** permite crear el conector personalizado desde dentro Azure Portal. Requiere que el usuario registrado actualmente tenga permiso para crear conectores en el entorno de destino. Se trata del enfoque recomendado si se puede cumplir dicho requisito. Si usa este modo, siga las instrucciones de [Exportación rápida](#express) especificadas más abajo.

    **Manual** permite exportar una copia de la definición de la API que puede importarse mediante los portales de PowerApps o Microsoft Flow. Este es el enfoque recomendado si el usuario de Azure y el usuario con permiso para crear conectores son personas diferentes o si el conector debe crearse en otro inquilino. Si usa este modo, siga las instrucciones de [Exportación e importación manual](#manual) especificadas más abajo.

<a name="express"></a>
## <a name="express-export"></a>Exportación rápida

En esta sección, creará un conector personalizado desde dentro Azure Portal. Debe estar registrado en el inquilino en que desea realizar la exportación y, además, debe disponer de permisos para crear un conector personalizado en el entorno de destino.

1. Seleccione el entorno en que desea crear el conector. Después, proporcione un nombre para el conector personalizado.

2. Si la definición de la API incluye cualquier definición de seguridad, estas se indicarán en el paso 2. Si es necesario, proporcione los detalles de configuración de seguridad requeridos para conceder a los usuarios acceso a la API. Para más información, vea el apartado [Autenticación](#auth) más abajo. 

3. Haga clic en **Aceptar** para crear el conector personalizado.


<a name="manual"></a>
## <a name="manual-export-and-import"></a>Exportación e importación manual

Para crear un conector personalizado para una aplicación web, móvil, de API o de función, se necesitan dos pasos:

1. [Recuperación de la definición de la API de App Service o Azure Functions](#export)
2. [Importación de la definición de la API en PowerApps y Microsoft Flow](#import)

Es posible que estos dos pasos deban llevarlos a cabo personas distintas de una organización, ya que un usuario concreto puede no tener permiso para realizar ambas acciones. En este caso, un desarrollador que tenga acceso de colaborador a la aplicación de App Service o de Azure Functions deberá obtener la definición de la API (un único archivo JSON) o un vínculo a esta. A continuación, deberá proporcionar esa definición a un propietario de PowerApps o Microsoft Flow. Ese propietario puede utilizar los metadatos para crear el conector personalizado.

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperación de la definición de la API de App Service o Azure Functions

En esta sección, exportará la definición de la API para la API de App Service, que se usará después en los portales de PowerApps o Microsoft Flow.

1. Puede elegir **Download the API definition** (Descargar la definición de la API) u **Obtener un vínculo**. El resultado se proporcionará en la sección siguiente, independientemente de su elección. Seleccione una de estas opciones y siga las instrucciones.
 
2. Si la definición de la API incluye cualquier definición de seguridad, estas se indicarán en el paso 2. Durante la importación, PowerApps y Microsoft Flow las detectarán y solicitarán información de seguridad. Recopile las credenciales relacionadas con cada definición para su uso en la sección siguiente. Para más información, vea el apartado [Autenticación](#auth) más abajo. 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importación de la definición de la API en PowerApps y Microsoft Flow

En esta sección, creará un conector personalizado en PowerApps y Microsoft Flow mediante la definición de API obtenida anteriormente. Los conectores personalizados se comparten entre los dos servicios, por lo que solo tiene que importar la definición una vez. Para más información sobre los conectores personalizados, consulte [Registrar y usar conectores personalizados en PowerApps] y [Registro y uso de conectores personalizados en Microsoft Flow].

1. Abra el [portal web de Powerapps](https://web.powerapps.com) o el de [Microsoft Flow](https://flow.microsoft.com/) e inicie sesión. 

2. Haga clic en el botón **Configuración** situado en la esquina superior derecha de la página (un icono de engranaje) y seleccione **Custom connectors** (Conectores personalizados). 

3. Haga clic en **Create custom connector** (Crear conector personalizado).

4. En la pestaña **General**, proporcione un nombre para la API y cargue la definición de OpenAPI o pegue la dirección URL de metadatos. Haga clic en **Continue**.

4. En la pestaña **Seguridad**, si se le pide que proporcione detalles de autenticación, escriba los valores obtenidos en la sección anterior. Si no, siga con el paso siguiente.

5. En la pestaña **Definiciones**, todas las operaciones definidas en el archivo de OpenAPI se rellenan automáticamente. Si se han definido todas las operaciones necesarias, puede ir al paso siguiente. Si no es así, puede agregar y modificar las operaciones aquí.

6. Haga clic en **Crear conector**. Si desea probar las llamadas de API, vaya al paso siguiente.

7. En la pestaña **Probar**, cree una conexión, seleccione una operación para probarla y escriba los datos necesarios de la operación.

8. Haga clic en **Operación de prueba**.


<a name="auth"></a>
## <a name="authentication"></a>Autenticación

PowerApps y Microsoft Flow admiten de forma nativa una colección de proveedores de identidades que se puede usar para que los usuarios inicien sesión en el conector personalizado. Si la API requiere autenticación, asegúrese de que se captura como _definición de seguridad_ en el documento Open API. Durante la exportación, deberá proporcionar los valores de configuración que permiten a PowerApps y Microsoft Flow realizar las siguientes acciones de inicio de sesión.

En esta sección se describen los tipos de autenticación admitidos en el flujo rápido: clave de API, Azure Active Directory y OAuth 2.0 genérico. Para acceder a una lista completa de los proveedores y las credenciales que cada uno requiere, vea [Registrar y usar conectores personalizados en PowerApps] y [Registro y uso de conectores personalizados en Microsoft Flow].

### <a name="api-key"></a>Clave de API
Cuando se usa este esquema de seguridad, a los usuarios de su conector se les pedirá que proporcionen la clave cuando creen una conexión. Puede proporcionar un nombre para la clave de API para ayudarlos a saber qué clave necesitan. En Azure Functions, normalmente será una de las claves de host, que abarcan varias funciones en una aplicación de función.

### <a name="azure-active-directory"></a>Azure Active Directory
Al configurar un conector personalizado que requiere el inicio de sesión de AAD, se necesitan dos registros de aplicaciones de AAD: uno para modelar la API de back-end y otro para modelar el conector en PowerApps y Flow.

La API debe configurarse para que funcione con el primer registro, y esto ya se habrá tenido en cuenta si usó la característica [Autenticación/Autorización de App Service](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication).

Tendrá que crear manualmente el segundo registro para el conector mediante los pasos descritos en [Agregar una aplicación de AAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). El registro necesita tener acceso delegado a la API y una dirección URL de respuesta de `https://msmanaged-na.consent.azure-apim.net/redirect`. Consulte [este ejemplo](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) para información más detallada y sustituya la API por Azure Resource Manager.

Se requieren los siguientes valores de configuración:
- **Id. de cliente**: el identificador de cliente del registro de AAD del conector
- **Secreto de cliente**: el secreto de cliente del registro de AAD del conector
- **Dirección URL de inicio de sesión**: la dirección URL base para AAD. En Azure público, normalmente será `https://login.windows.net`.
- **Identificador de inquilino**: el identificador del inquilino que se usará para el inicio de sesión. Debe ser "common" o el identificador del inquilino en el que se va a crear el conector.
- **URL de recursos**: la dirección URL del recurso del registro de AAD de back-end de la API

> [!IMPORTANT]
> Si otra persona va a importar la definición de la API en PowerApps y Microsoft Flow como parte del flujo manual, tendrá que proporcionarle el identificador y el secreto de cliente **del registro del conector**, así como la dirección URL de recursos de la API. Asegúrese de que estos secretos se administran de forma segura. **No comparta las credenciales de seguridad de la propia API.**

### <a name="generic-oauth-20"></a>OAuth 2.0 genérico
La compatibilidad de OAuth 2.0 genérico permite realizar la integración con cualquier proveedor de OAuth 2.0. Esto permite integrar cualquier proveedor personalizado que no se admite de forma nativa.

Se requieren los siguientes valores de configuración:
- **Id. de cliente**: el identificador de cliente de OAuth 2.0
- **Secreto de cliente**: el secreto de cliente de OAuth 2.0
- **Dirección URL de autorización**: la dirección URL de autorización de OAuth 2.0
- **Dirección URL de token**: la dirección URL del token de OAuth 2.0
- **Dirección URL de actualización**: la dirección URL de actualización de OAuth 2.0



[Registrar y usar conectores personalizados en PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Registro y uso de conectores personalizados en Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/

