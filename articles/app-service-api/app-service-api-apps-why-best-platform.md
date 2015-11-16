<properties 
	pageTitle="Información general sobre Aplicaciones de API" 
	description="Descubra por qué el Servicio de aplicaciones de Azure es la mejor plataforma para desarrollar, publicar y hospedar API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# Información general sobre Aplicaciones de API

Aplicaciones de API son uno de los cuatro tipos que ofrece el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Las aplicaciones de API ofrecen una plataforma completa para la compilación, el hospedaje y el consumo de las API, tanto en la nube como de forma local. Implemente la API como una aplicación de API del Servicio de aplicaciones y benefíciese de seguridad de nivel empresarial, control de acceso sencillo, conectividad híbrida, generación automática de SDK e integración completa con [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## ¿Por qué las Aplicaciones de API?

Las Aplicaciones de API proporcionan las siguientes características, que están disponibles actualmente en la versión de vista previa pública:

- **Fácil consumo**: la compatibilidad integrada de [Swagger](#concepts) permite que una gran variedad de clientes pueda consumir las API de forma sencilla. Configure fácilmente [CORS](#concepts) y genere automáticamente código de cliente para sus API en diversos lenguajes, como C#, Java y Javascript.

- **Control de acceso sencillo**: puede proteger una aplicación de API frente al acceso no autenticado sin realizar ningún cambio en el código. Los servicios de autenticación integrados protegen las API frente al acceso por otros servicios o por clientes que representan a los usuarios. Los proveedores de identidad admitidos incluyen Azure Active Directory y proveedores de terceros, como Facebook y Twitter. Los clientes pueden usar la biblioteca de autenticación de Active Directory (AAL) o el SDK de Aplicaciones móviles. Para obtener más información, consulte [Autenticación para aplicaciones de API y aplicaciones móviles en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).

- **Integración con visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación, depuración y administración de las aplicaciones de API.

- **Integración con aplicaciones lógicas**: las [aplicaciones lógicas del Servicio de aplicaciones](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden consumir las aplicaciones de API que cree.

- **La API existente puede incorporarse tal cual**: no es necesario realizar ningún cambio en el código de las API existentes para aprovechar las características de las aplicaciones de API, solo tiene que implementar el código en una aplicación de API. La API puede usar cualquier lenguaje o marco admitidos por el Servicio de aplicaciones, como ASP.NET y C#, Java, PHP, Node.js y Python.

Estos son solo algunos aspectos destacados. Para conocer otras características que pueden aprovechar las aplicaciones de API, consulte la [Información general sobre Aplicaciones web](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE]Puede usar [Administración de API de Azure](../api-management/api-management-key-concepts.md) para controlar el acceso a las API que se hospedan en Aplicaciones de API del Servicio de aplicaciones. Aunque las Aplicaciones de API proporcionan servicios de autenticación, hay otras características de administración de acceso que no ofrecen, que sí ofrece la Administración de API, como la consolidación y la limitación de los puntos de conexión.
>
>Aplicaciones de API se encuentra actualmente en versión de vista previa pública. [Aplicaciones web del Servicio de aplicaciones](../app-service-web/app-service-web-overview.md) es un servicio disponible con carácter general diseñado para compilar y hospedar aplicaciones críticas seguras a escala global. Si busca un servicio de disponibilidad general para crear una API en la actualidad, Aplicaciones web es una opción ideal. Cuando Aplicaciones de API pase a estar disponible con carácter general, proporcionaremos una ruta de acceso para usar las aplicaciones web existentes y aprovechar las características adicionales de Aplicaciones de API.

## Conceptos de Aplicaciones de API ##

- **Swagger**: un marco para la documentación y la detección de una API de RESTful que se usa de forma predeterminada en las aplicaciones de API. Para obtener más información, consulte [http://swagger.io/](http://swagger.io/).
- **Uso compartido de recursos entre orígenes (CORS)**: un mecanismo que permite a JavaScript ejecutarse en un explorador para realizar llamadas a una API hospedada en un dominio diferente a aquel en el que se cargó la página web.
- **Desencadenador**: una API de REST que las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden llamar para iniciar un proceso de flujo de trabajo cuando se cumple una determinada condición. Por ejemplo, una aplicación de API puede proporcionar un método al que la aplicación lógica llame periódicamente para buscar una frase determinada en una fuente de Twitter. Para obtener más información, consulte [Desencadenadores de aplicación de API](app-service-api-dotnet-triggers.md).
- **Acción**: una API de REST a la que las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden llamar para procesar datos después de que un desencadenador haya iniciado un flujo de trabajo. Por ejemplo, una aplicación de API puede proporcionar un método al que la aplicación lógica llame para responder a un tweet encontrado por el desencadenador de Twitter. Las acciones son métodos de API expuestos por una definición de la API de Swagger.
- **Puerta de enlace**: una aplicación web que controla las funciones de administración de API y la autenticación de todas las aplicaciones de API en un grupo de recursos.

## Introducción

Para empezar a trabajar con Aplicaciones de API, siga el tutorial [Creación de una aplicación de API](app-service-dotnet-create-api-app.md).

Para ver una lista de problemas conocidos con Aplicaciones de API, consulte [la entrada del foro de problemas conocidos de Aplicaciones de API](https://social.msdn.microsoft.com/Forums/es-ES/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Nov15_HO2-->