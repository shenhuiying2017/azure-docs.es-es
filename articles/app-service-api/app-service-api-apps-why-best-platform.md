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

Las Aplicaciones de API de este conjunto ofrecen una plataforma y un ecosistema enriquecidos para la compilación, el hospedaje, el uso y la distribución de las API, tanto en la nube como de forma local. Implemente la API como una aplicación de API y benefíciese de la seguridad de nivel empresarial, el control de acceso simple, la conectividad SaaS e híbrida, la generación automática de SDK y la integración completa con las [aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md).

Aplicaciones de API forma parte del [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md), que también incluye aplicaciones web, móviles y lógicas.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## ¿Por qué las Aplicaciones de API?

Aplicaciones de API proporciona capacidades para desarrollar, implementar, publicar, consumir y administrar las API web de RESTful. Servicio de aplicaciones proporciona las siguientes características, que están disponibles actualmente en la versión de vista previa pública:

- **Fácil consumo**: la compatibilidad integrada de [Swagger](http://swagger.io/) permite que una gran variedad de clientes pueda consumir las API de forma sencilla. El SDK de Aplicaciones de API puede generar código de cliente para las API en diversos lenguajes, como C#, Java y Javascript.

- **Control de acceso simple**: los servicios de autenticación integrados admiten Azure Active Directory o servicios de terceros como Facebook y Twitter. Puede proteger una aplicación de API contra el acceso no autenticado sin realizar ningún cambio en el código. Si está familiarizado con los servicios de autenticación proporcionados por [Servicios móviles de Azure](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication), las aplicaciones de API se basan en ese marco de trabajo y lo amplían a las API alojadas por las aplicaciones de API. El SDK del Servicio de aplicaciones también permite usar una sintaxis simplificada para el código de autorización. Para obtener más información, consulte [Autenticación para aplicaciones de API y aplicaciones móviles en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).

- **Fácil conexión con las plataformas SaaS**: las [aplicaciones de API de conectores](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) de Azure Marketplace las proporcionan tanto Microsoft como otros fabricantes para simplificar el código que se escribe para interactuar con SalesForce, Office 365, Twitter, Facebook, Dropbox y muchos más.

- **Integración con aplicaciones lógicas**: las [aplicaciones lógicas del Servicio de aplicaciones](../app-service-logic/app-service-logic-what-are-logic-apps.md) pueden consumir las aplicaciones de API que cree.

- **Integración con visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de [creación](app-service-dotnet-create-api-app.md), [implementación](app-service-dotnet-deploy-api-app.md), [depuración](app-service-dotnet-remotely-debug-api-app) y administración de las aplicaciones de API.

La API existente puede incorporarse tal cual: no es necesario realizar ningún cambio en el código de las API existentes para aprovechar las características de las aplicaciones de API, solo tiene que implementar el código en una aplicación de API. Para las API, puede usar ASP.NET, Java, PHP, Node.js o Python.

Aplicaciones de API incluye además [características de las aplicaciones web del Servicio de aplicaciones](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE] [Azure API Management](/services/api-management/) es un servicio independiente que ofrece características como la consolidación de extremos y la limitación. Administración de API se puede usar con Aplicaciones de API.
>
>Aplicaciones de API se encuentra actualmente en versión de vista previa pública. [Aplicaciones web del Servicio de aplicaciones](../app-service-web/app-service-web-overview.md) es un servicio disponible con carácter general diseñado para compilar y hospedar aplicaciones críticas seguras a escala global. Si busca un servicio de disponibilidad general para crear una API en la actualidad, Aplicaciones web es una opción ideal. Cuando Aplicaciones de API pase a estar disponible con carácter general, proporcionaremos una ruta de acceso para usar las aplicaciones web existentes y aprovechar las características adicionales de Aplicaciones de API.

### Características de Aplicaciones de API disponibles en un futuro

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

 

<!---HONumber=July15_HO3-->