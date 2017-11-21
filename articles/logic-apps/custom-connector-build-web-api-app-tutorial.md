---
title: "Creación de conectores personalizados desde API web - Azure Logic Apps | Microsoft Docs"
description: Compile los conectores personalizados desde API web.
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Creación de conectores personalizados desde API web

A fin de crear un conector personalizado que se pueda utilizar en Azure Logic Apps, Microsoft Flow o Microsoft PowerApps, primero cree una API web que pueda hospedar con Azure Web Apps, autenticar con Azure Active Directory y registrar como conector con Logic Apps, Flow o PowerApps. En este tutorial se muestra cómo realizar estas tareas mediante la creación de una aplicación de ASP.NET Web API. Para conocer los modelos que muestran el modo de estructurar el código para los desencadenadores y las acciones del conector, vea [Creación de API personalizadas como conectores para aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Requisitos previos

* [Visual Studio 2013 o posterior.](https://www.visualstudio.com/vs/) En este tutorial se usa Visual Studio 2015.

* Código para su API web. Si no tiene ninguno, pruebe este tutorial: [Getting Started with ASP.NET Web API 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) (Introducción a ASP.NET Web API 2 [C#]).

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede registrarse para obtener una [suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Creación e implementación de una aplicación web ASP.NET en Azure

Para este tutorial, cree una aplicación web ASP.NET de Visual C#. 

1. Abra Visual Studio 2015 y seleccione **Archivo** > **Nuevo proyecto**.

   1. Expanda **Instalado**, vaya a **Plantillas** > **Visual C#** > **Web** y seleccione **Aplicación web ASP.NET**.

   2. Proporcione un nombre para el proyecto, la ubicación y el nombre de la solución para la aplicación y después elija **Aceptar**.

   Por ejemplo:

   ![Creación de una aplicación web ASP.NET de Visual C#](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. En el cuadro **Nueva aplicación web ASP.NET**, seleccione la plantilla **API web**. Seleccione **Host en la nube** si aún no está seleccionado. Elija **Cambiar autenticación**.

   ![Selección de la plantilla "API web", "Host en la nube, "Cambiar autenticación"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Seleccione **Sin autenticación** y elija **Aceptar**. Puede configurar la autenticación más adelante.

   ![Selección de "Sin autenticación"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. Cuando vuelva a aparecer el cuadro **Nueva aplicación web ASP.NET**, elija **Aceptar**. 

5. En el cuadro **Crear App Service**, reviese la configuración de hospedaje descrita en la tabla, realice los cambios que desee y elija **Crear**. 

   Un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) representa una colección de recursos físicos que se usa para hospedar aplicaciones en su suscripción de Azure. Obtenga información acerca de [App Service](../app-service/app-service-value-prop-what-is.md).

   ![Creación de un elemento App Service](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | Su cuenta profesional o educativa de Azure o su cuenta personal de Microsoft | *cuenta-usuario* | Seleccione su cuenta de usuario. | 
   | **Nombre de aplicación web** | *nombre-aplicación-api-web-personalizada* o nombre predeterminado | Escriba el nombre de la aplicación de API web, que se usa en la dirección URL de la aplicación; por ejemplo, http://*nombre-aplicación-api.web*. | 
   | **Suscripción** | *nombre-suscripción-Azure* | Seleccione la suscripción de Azure que quiera usar. | 
   | **Grupo de recursos** | *nombre-grupo-recursos-Azure* | Seleccione un grupo de recursos de Azure existente o, si aún no lo ha hecho, cree un grupo de recursos. <p>**Nota**: Un grupo de recursos de Azure organiza los recursos de Azure en una suscripción de Azure. | 
   | **Plan de App Service** | *nombre-plan-App-Service* | Seleccione un plan actual de App Service o, si aún no lo ha hecho, cree un plan. | 
   |||| 

   Si crea un plan de App Service, especifique esta configuración:

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Ubicación** | *región-implementación* | Seleccione la región para la implementación de la aplicación. | 
   | **Tamaño** | *tamaño-plan-App-Service* | Seleccione el tamaño de plan, lo cual determina el coste y la capacidad de recursos de proceso para el plan de servicio. | 
   |||| 

   Para configurar cualquier otro recurso requerido por la aplicación, seleccione **Explorar servicios adicionales de Azure**.

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Tipo de recurso** | *tipo-recurso-Azure* | Seleccione y configure todos los recursos adicionales requeridos por la aplicación. | 
   |||| 

6. Una vez que Visual Studio implemente el proyecto, compile el código de la aplicación.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Creación de un archivo de OpenAPI (Swagger) que describa la API web

Para conectar la aplicación de API web a Logic Apps, es necesario un [archivo de OpenAPI (anteriormente Swagger)](http://swagger.io/) que describa las operaciones de la API. Puede escribir su propia definición de OpenAPI para su API con el [editor en línea de Swagger](http://editor.swagger.io/), pero este tutorial utiliza una herramienta de código abierto denominada [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Si aún no lo ha hecho, instale el paquete de Swashbuckle Nuget en el proyecto de Visual Studio.

   1. En Visual Studio, seleccione **Herramientas** > **Administrador de paquetes de NuGet** > 
   **Consola del Administrador de paquetes**.

   2. En la **Consola del Administrador de paquetes**, vaya al directorio del proyecto de la aplicación si aún no está ahí (ejecute `Set-Location "project-path"`) y ejecute este commandlet de PowerShell: 
   
      `Install-Package Swashbuckle`

      Por ejemplo:

      ![Consola del Administrador de paquetes, instalación de Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Si ejecuta la aplicación después de instalar Swashbuckle, Swashbuckle genera un archivo de OpenAPI en esta dirección URL: 
   >
   > http://*{URL-raíz-aplicación-api-web}*/swagger/docs/v1
   > 
   > Swashbuckle también genera una interfaz de usuario en esta dirección URL: 
   > 
   > http://*{URL-raíz-aplicación-api-web}*/swagger

3. Cuando esté listo, publique la aplicación de API web en Azure. Para publicar desde Visual Studio, haga clic con el botón derecho en el proyecto web en el Explorador de soluciones, elija **Publicar...** y siga las indicaciones.

   > [!IMPORTANT]
   > Los identificadores de operación duplicados invalidan un documento de OpenAPI. Si utiliza la plantilla de ejemplo de C#, la plantilla *repite dos veces este identificador de operación*: `Values_Get` 
   > 
   > Para corregir este problema, cambie una instancia a `Value_Get` y vuelva a publicar.

4. Obtenga el documento de OpenAPI a través de esta ubicación: 

   http://*{URL-raíz-aplicación-api-web}*/swagger/docs/v1

   También puede descargar un [documento de OpenAPI de ejemplo](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) desde este tutorial. 
   Asegúrese de quitar los comentarios, que empiezan por "//", antes de utilizar el documento.

5. Guarde el contenido como un archivo JSON. En función de su explorador, tendrá que copiar y pegar el texto en un archivo de texto vacío.

## <a name="next-steps"></a>Pasos siguientes

* [Set up authentication for custom connectors](../logic-apps/custom-connector-azure-active-directory-authentication.md) (Configuración de la autenticación para conectores personalizados)











