---
title: "Metadatos de App Service API Apps para la detección de API y la generación de código | Microsoft Docs"
description: "Aprenda cómo las aplicaciones de API del Servicio de aplicaciones de Azure usan los metadatos de Swagger para facilitar la detección de API y la generación de código."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: 
ms.assetid: c7f8e33a-61cc-486f-89df-4a97dc3c71d4
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: a0580f8d303c7ce33a65f0ce6faecf2492f851b0
ms.openlocfilehash: 3b41340f30239dc0102808b5e492ee7300dcd12b


---
# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Metadatos de aplicaciones de API del Servicio de aplicaciones para la detección de API y la generación de código
La compatibilidad con los metadatos de la API de [Swagger 2.0](http://swagger.io/) está integrada en las aplicaciones de API del Servicio de aplicaciones. Aunque no es necesario usar Swagger, si lo usa, puede sacar partido de las características de aplicaciones de API que facilitan la detección y el consumo.   

## <a name="swagger-endpoint"></a>Punto de conexión de Swagger
Puede especificar un punto de conexión que proporcione metadatos JSON de Swagger 2.0 para una aplicación de API en una propiedad de la aplicación de API. El punto de conexión puede ser relativo a la dirección URL base de la aplicación de API o a una dirección URL absoluta. Las direcciones URL absolutas pueden señalar fuera de la aplicación de API. 

En muchos clientes de bajada (por ejemplo, la generación de código de Visual Studio y el flujo "Agregar API" de PowerApps), es preciso que se pueda acceder públicamente a la dirección URL (que no esté protegida mediante autenticación de usuario o de servicio). Esto significa que si usa la autenticación de Servicio de aplicaciones y desea exponer la definición de la API desde su propia aplicación, será preciso que utilice la opción de autenticación que permite que llegue tráfico a la API. Para más información, consulte [Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones](app-service-api-authentication.md).

### <a name="portal-blade"></a>Hoja del portal
En el [Portal de Azure](https://portal.azure.com/) , la dirección URL del punto de conexión se puede ver y cambiar en la hoja **Definición de API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propiedad del Administrador de recursos de Azure
También se puede configurar la dirección URL de la definición de la API de una aplicación de API mediante el [Explorador de recursos](https://resources.azure.com/) o las plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) de las herramientas de línea de comandos, como [Azure PowerShell](/powershell/azureps-cmdlets-docs) y la [CLI de Azure](../xplat-cli-install.md). 

En el **Explorador de recursos**, vaya a **suscripciones > {su suscripción} > resourceGroups > {su grupo de recursos} > proveedores > Microsoft.Web > sitios > {su sitio} > configuración > web** y verá la propiedad `apiDefinition`:

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Para ver un ejemplo de una plantilla de Azure Resource Manager que establece la propiedad `apiDefinition` , abra el [archivo azuredeploy.json de la lista de tareas pendientes](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Busque la sección de la plantilla similar al ejemplo de JSON mostrado anteriormente.

### <a name="default-value"></a>Valor predeterminado
Si se usa Visual Studio para crear una aplicación de API, el punto de conexión de la definición de API se establece automáticamente en la URL base de la aplicación de API más `/swagger/docs/v1`. Se trata de la dirección URL predeterminada que el paquete NuGet de [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) utiliza para enviar los metadatos de Swagger generados dinámicamente para un proyecto de ASP.NET Web API. 

## <a name="code-generation"></a>Generación de código
Una de las ventajas de integrar Swagger en aplicaciones de API de Azure es la generación automática de código. Las clases de cliente generadas hacen que sea más fácil escribir código que llame a una aplicación de API.

Puede generar código de cliente para una aplicación de API con Visual Studio o desde la línea de comandos. Para información sobre cómo generar clases de cliente en Visual Studio para un proyecto de API web ASP.NET, consulte [Introducción a las aplicaciones de API y ASP.NET](app-service-api-dotnet-get-started.md#codegen). Para información sobre cómo hacerlo desde la línea de comandos en todos los lenguajes admitidos, consulte el archivo Léame del repositorio [Azure/autorest](https://github.com/azure/autorest) en GitHub.com.

## <a name="next-steps"></a>Pasos siguientes
Para obtener un tutorial detallado que le guíe en el proceso de creación, implementación y consumo de una aplicación de API, consulte [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

Si usa Administración de API de Azure con aplicaciones de API, puede utilizar metadatos de Swagger para importar la API a Administración de API. Para más información, consulte [Importación de la definición de una API con operaciones en Administración de API de Azure](../api-management/api-management-howto-import-api.md). 




<!--HONumber=Dec16_HO3-->


