<properties
	pageTitle="Metadatos de aplicaciones de API del Servicio de aplicaciones para la detección de API y la generación de código | Microsoft Azure"
	description="Aprenda cómo las aplicaciones de API del Servicio de aplicaciones de Azure usan los metadatos de Swagger para facilitar la detección de API y la generación de código."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# Metadatos de aplicaciones de API del Servicio de aplicaciones para la detección de API y la generación de código 

La compatibilidad con los metadatos de la API de [Swagger 2.0](http://swagger.io/) está integrada en las aplicaciones de API del Servicio de aplicaciones. Aunque no es necesario usar Swagger, si lo usa, puede sacar partido de las características de aplicaciones de API que facilitan la detección y el consumo.

## Punto de conexión de Swagger

Puede especificar un punto de conexión que proporcione metadatos JSON de Swagger 2.0 para una aplicación de API en una propiedad de la aplicación de API. El punto de conexión puede ser relativo a la dirección URL base de la aplicación de API o a una dirección URL absoluta. Las direcciones URL absolutas pueden señalar fuera de la aplicación de API.

La dirección URL debe ser accesible públicamente (no estar protegida por la autenticación de usuarios o servicios).

En el [Portal de Azure](https://portal.azure.com/) la dirección URL del punto de conexión se puede ver y cambiar en la hoja **Definición de API**.

![](./media/app-service-api-metadata/apidefblade.png)

Cuando utiliza Visual Studio para crear una aplicación de API, el punto de conexión de la definición de API se establece automáticamente en la dirección URL base de la aplicación de API más `/swagger/docs/v1`. Se trata de la dirección URL predeterminada que el paquete NuGet de [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) utiliza para enviar los metadatos de Swagger generados dinámicamente para un proyecto de API web ASP.NET.

## Generación de código

Una de las ventajas de integrar Swagger en aplicaciones de API de Azure es la generación automática de código. Las clases de cliente generadas hacen que sea más fácil escribir código que llame a una aplicación de API.

Puede generar código de cliente para una aplicación de API con Visual Studio o desde la línea de comandos. Para información sobre cómo generar clases de cliente en Visual Studio para un proyecto de API web ASP.NET, consulte [Introducción a las aplicaciones de API y ASP.NET](app-service-api-dotnet-get-started.md#codegen). Para información sobre cómo hacerlo desde la línea de comandos para todos los lenguajes admitidos, consulte el archivo Léame del repositorio [Azure/autorest](https://github.com/azure/autorest) en GitHub.com.
 
## Pasos siguientes

Para obtener un tutorial paso a paso que le guíe en el proceso de creación, implementación y consumo de una aplicación de API, consulte [Introducción a las aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

<!---HONumber=AcomDC_1203_2015-->