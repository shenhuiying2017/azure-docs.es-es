<properties 
	pageTitle="¿Qué son las Aplicaciones de API?" 
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
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# ¿Qué son las Aplicaciones de API?

Aplicaciones de API forma parte del conjunto de aplicaciones [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md) que también incluye las aplicaciones web, móviles y lógicas.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

La parte Aplicaciones de API de este conjunto ofrece una plataforma y un ecosistema enriquecidos para la compilación, el uso y la distribución de las API tanto en la nube como de forma local.

>[AZURE.NOTE]Aplicaciones de API se encuentra actualmente en versión de vista previa pública. Se basa en [Aplicaciones web del Servicio de aplicaciones](../app-service-web/app-service-web-overview.md), un servicio de disponibilidad general (GA) diseñado para crear y hospedar aplicaciones críticas seguras a escala global. Si busca un servicio de disponibilidad general para crear una API en la actualidad, Aplicaciones web es una opción ideal. Cuando Aplicaciones de API pase a tener disponibilidad general, proporcionaremos una ruta de acceso para usar las aplicaciones web existentes y aprovechar las características de Aplicaciones de API.

## ¿Por qué las Aplicaciones de API?

Una aplicación de API es una [aplicación web del Servicio de aplicaciones](../app-service-web/app-service-web-overview.md) con características adicionales que mejoran la experiencia de desarrollo, implementación, publicación, consumo, administración y monetización de las API web de RESTful.

Esto significa que las aplicaciones de API comparten con las aplicaciones web todas las características de hospedaje web de la plataforma Servicio de aplicaciones de Azure:

- Revisiones automáticas del SO
- Seguridad de nivel empresarial
- Alta disponibilidad
- Ajuste de escala automático y equilibrio de carga
- [WebJobs](../app-service-web/websites-webjobs-resources.md) para procesamiento en segundo plano
- Implementación rápida y fácil con numerosas opciones de entrega continua: para obtener información sobre la gama de opciones de implementación disponibles para las aplicaciones de API, consulte [Implementar una aplicación web en el servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md). 

Las características adicionales ofrecidas por la plataforma Aplicaciones de API facilitan el desarrollo, el hospedaje y el consumo de las API:

- **Traiga su API tal cual**: use ASP.NET, Java, PHP, Node.js o Python para las API. Estas pueden aprovechar las ventajas de la plataforma Aplicaciones de API sin cambio alguno.

- **Control de acceso simple**: configure las API para la autenticación de Azure Active Directory o de servicios de terceros, como Facebook y Twitter, sin realizar cambios en el código. Use una sintaxis simplificada para el código de autorización. Para obtener más información, consulte [Protección de una aplicación de API](app-service-api-dotnet-add-authentication.md).

- **Fácil conexión con las plataformas SaaS**: las [aplicaciones de API de conectores](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) de Azure Marketplace las proporcionan tanto Microsoft como otros fabricantes para simplificar el código que se escribe para interactuar con SalesForce, Office 365, Twitter, Facebook, Dropbox y muchos más.

- **Acceso a datos locales**: las Aplicaciones de API pueden exponer las API que consumen datos de su propio centro de datos mediante [conexiones híbridas](../integration-hybrid-connection-overview.md) y [VNET](../app-service-web/web-sites-integrate-with-vnet.md).

- **Fácil consumo**: use el soporte integrado de [Swagger](http://swagger.io/) para permitir que una gran variedad de clientes puedan consumir las API de forma sencilla. También puede usar el SDK de Aplicaciones de API a fin de generar código de cliente para las API en diversos lenguajes, como C#, Java y Javascript.

- **Integración con aplicaciones lógicas**: las aplicaciones lógicas del Servicio de aplicaciones pueden consumir las aplicaciones de API que cree.

- **Integración con visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de [creación](app-service-dotnet-create-api-app.md), [implementación](app-service-dotnet-deploy-api-app.md), [depuración](app-service-dotnet-remotely-debug-api-app) y administración de las aplicaciones de API.

En un futuro próximo, la plataforma Aplicaciones de API facilitará el uso compartido del código mediante la creación de un ecosistema de API enriquecido:

- **Catálogos de soluciones públicos y privados**: [Azure Marketplace](http://azure.microsoft.com/marketplace/) facilitará la tarea de buscar e implementar aplicaciones de API preempaquetadas desarrolladas por Microsoft y otros fabricantes en la suscripción de Azure. Además, podrá empaquetar y publicar las aplicaciones de API propias que desarrolle para que otros desarrolladores puedan implementarlas en sus suscripciones de Azure. Al publicar las API en Azure Marketplace, puede hacer que estas solo sean visibles para otros miembros de la organización. 

- **Implementación de dependencia automática**: siempre que se implemente una aplicación de API de Marketplace en la suscripción de Azure, Azure implementará automáticamente las aplicaciones de API dependientes y creará los recursos necesarios. Un paquete de aplicaciones de API especificará las aplicaciones de API de las que depende y los recursos de Azure que requiere.

- **Actualizaciones automáticas**: al actualizar el código de uno de los paquetes de aplicaciones de API compartidos, podrá promover la actualización a todos los usuarios que hayan instalado y que ejecuten dicha aplicación de API. Esto funcionará en el caso de cambios no radicales y de usuarios que hayan optado por recibir las actualizaciones.

Muchas de estas características, como el catálogo público y las actualizaciones automáticas, ya están disponibles para las aplicaciones de API proporcionadas por Microsoft.

## Conceptos de Aplicaciones de API ##

- **Puerta de enlace**: una aplicación web que controla las funciones de administración de API y la autenticación de todas las aplicaciones de API en un grupo de recursos. 
- **Swagger**: un marco para la documentación interactiva y la detección de una API de RESTful que se usa de forma predeterminada en las aplicaciones de API. Para obtener más información, consulte [http://swagger.io/](http://swagger.io/).
- **Conector**: un tipo de aplicación de API que facilita la tarea de conectarse a plataformas SaaS como Salesforce y Office 365. Para obtener más información, consulte [Qué son los conectores y las aplicaciones de API de BizTalk](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md).
- **Desencadenador**: una API de REST que las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden llamar para iniciar un proceso de flujo de trabajo cuando se cumple una determinada condición. Por ejemplo, una aplicación de API puede proporcionar un método al que la aplicación lógica llame periódicamente para buscar una frase determinada en una fuente de Twitter. Para obtener más información, consulte [Desencadenadores de aplicación de API](app-service-api-dotnet-triggers.md).
- **Acción**: una API de REST a la que las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden llamar para procesar datos después de que un desencadenador haya iniciado un flujo de trabajo. Por ejemplo, una aplicación de API puede proporcionar un método al que la aplicación lógica llame para responder a un tweet encontrado por el desencadenador de Twitter. Las acciones son métodos de API expuestos por una definición de la API de Swagger.

## Introducción

Para empezar a trabajar con Aplicaciones de API, siga el tutorial [Creación de una aplicación de API](app-service-dotnet-create-api-app.md).

Para ver una lista de problemas conocidos de las aplicaciones de API, consulte [esta publicación del foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).


<!--HONumber=54--> 