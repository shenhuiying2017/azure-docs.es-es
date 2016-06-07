<properties 
	pageTitle="¿Qué es Servicio de aplicaciones de Azure? | Microsoft Azure" 
	description="Descubra cómo el Servicio de aplicaciones de Azure le ayuda a desarrollar, implementar y administrar aplicaciones web y móviles." 
	keywords="servicio de aplicaciones, servicio de aplicaciones de azure, costo de servicio de aplicaciones, escala, escalable, implementación de aplicaciones, implementación de aplicaciones de azure, paas, plataforma como servicio"
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# ¿Qué es Servicios de aplicaciones de Azure?

El *Servicio de aplicaciones* es una oferta de [plataforma como servicio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure que le permite crear aplicaciones web y móviles para cualquier plataforma o dispositivo. Puede integrar fácilmente sus aplicaciones con soluciones SaaS (como Office 365, Dynamics CRM, Salesforce o Twilio), conectarlas con facilidad a aplicaciones locales (como SAP, Oracle o Siebel) y automatizar fácilmente procesos empresariales ajustándose a unos estrictos requisitos de seguridad, fiabilidad y escalabilidad.

El Servicio de aplicaciones incluye las funcionalidades web y móviles que anteriormente se ofrecían por separado como Sitios web de Azure y Servicios móviles de Azure. También incluye nuevas funcionalidades para automatizar procesos empresariales y hospedar las API en la nube.

## Tipos de aplicaciones en el Servicio de aplicaciones

El Servicio de aplicaciones ofrece los siguientes tipos de aplicaciones para la ejecución de procesos de flujo de trabajo o código de aplicación.

- [**Aplicaciones web**](../app-service-web/app-service-web-overview.md): para hospedar sitios y aplicaciones web.

- [**Aplicaciones móviles**](../app-service-mobile/app-service-mobile-value-prop.md): para hospedar back-end de aplicaciones móviles.
   
- [**Aplicaciones de API**](../app-service-api/app-service-api-apps-why-best-platform.md): para hospedar API en la nube.
 
- [**Aplicaciones lógicas**](../app-service-logic/app-service-logic-what-are-logic-apps.md): para automatizar el acceso y el uso de datos a través de nubes sin escribir código.

En cuanto servicio integrado único, el Servicio de aplicaciones facilita la creación de varios tipos de aplicaciones en una única solución.

## Planes y entornos del Servicio de aplicaciones

Los [planes del Servicio de aplicaciones](azure-web-sites-web-hosting-plans-in-depth-overview.md) representan los recursos de procesos con los que se ejecutan sus aplicaciones. Con los planes de tarifa más económicos, las aplicaciones se ejecutan en máquinas virtuales compartidas. Con los planes de tarifa de mayor precio, las aplicaciones se ejecutan en máquinas virtuales dedicadas. Puede elegir máquinas virtuales de distintos tamaños y cambiar el plan de tarifa sin experimentar tiempo de inactividad. Si necesita mayor escalabilidad y aislamiento de red, puede ejecutar las aplicaciones en un [entorno del Servicio de aplicaciones](../app-service-web/app-service-app-service-environment-intro.md).

## ¿Por qué usar el Servicio de aplicaciones?

Estas son algunas de las características y funcionalidades principales del Servicio de aplicaciones:

- **Plataforma totalmente administrada**: revisiones automáticas del sistema operativo y el marco, compatibilidad integrada para copia de seguridad y recuperación ante desastres. 

- **Uso de sus conocimientos**: codifique en su lenguaje, marco y entorno de desarrollo favoritos. El Servicio de aplicaciones es compatible con .NET, Node.js, Java, PHP y Python.

- **Rápida implementación**: aprovisione nuevas aplicaciones e implemente el código en ellas en segundos.

- **Integración continua**: configure la [integración y la implementación continuas](../app-service-web/app-service-continous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket.

- **Entornos de prueba y ensayo**: ponga en práctica una [implementación de ensayo](../app-service-web/web-sites-staged-publishing.md) para comprobar el código en un entorno de preproducción que sea idéntico al de producción. Cuando esté listo, publique una nueva versión de la aplicación sin tiempo de inactividad mediante una operación de intercambio.

- **Prueba de producción**: lleve las implementaciones de ensayo a un nivel superior y [realice pruebas A/B](../app-service-web/app-service-web-test-in-production-get-start.md) para comprobar el nuevo código con una fracción configurable de su tráfico real.

- **Autenticación y autorización**: puede proteger una aplicación frente al acceso no autenticado sin realizar ningún cambio en el código. Los servicios de autenticación integrados protegen las aplicaciones frente al acceso de otros servicios, usuarios o clientes que representan a los usuarios. Los proveedores de identidad compatibles incluyen Azure Active Directory, Facebook, Twitter, Google y cuenta Microsoft. Para más información, consulte [Autenticación y autorización en el Servicio de aplicaciones de Azure](app-service-authentication-overview.md).

- **Conexión a cualquier servicio**: conecte su aplicación a sistemas empresariales o plataformas de software como servicio (SaaS) en cuestión de minutos con los [conectores](../connectors/apis-list.md) incorporados. Elija entre más de 50 conectores para sistemas empresariales, como SAP, Siebel y Oracle, servicios de SaaS empresariales conocidos como Salesforce y Office 365 y servicios de Internet populares, como Facebook, Twitter y Dropbox.

- **Escala global**: escale [vertical](../app-service/app-service-scale.md) u [horizontalmente](../azure-portal/insights-how-to-scale.md) para hacer frente a cualquier carga de trabajo entrante de los clientes. Seleccione manualmente el número y el tamaño de las máquinas virtuales o configure el escalado automático en función de la carga o la programación. La infraestructura de centro de datos global de Microsoft hospeda las aplicaciones y facilita la tarea de replicar datos y servicios de hospedaje en varias ubicaciones.

- **Carácter empresarial**: el Servicio de aplicaciones se ha diseñado para compilar y hospedar aplicaciones críticas seguras. Compile aplicaciones empresariales integradas de Active Directory que se conecten de forma segura a recursos locales y hospédelas en una plataforma segura en la nube que es compatible con [ISO, SOC y PCI](https://www.microsoft.com/TrustCenter/), todo ello con un [contrato de nivel de servicio](https://azure.microsoft.com/support/legal/sla/app-service/) empresarial.

- **Azure Marketplace**: seleccione en una [lista de plantillas de aplicaciones](https://azure.microsoft.com/marketplace/) que sigue ampliándose. Aproveche lo mejor de la comunidad de aplicaciones OSS mediante la instalación con un solo clic de paquetes como Wordpress, Joomla y Drupal.

- **WebJobs**: [ejecute cualquier programa o script](../app-service-web/web-sites-create-web-jobs.md) en las máquinas virtuales del Servicio de aplicaciones. Ejecute trabajos de forma continua, según una programación o desencadenados por eventos. El [SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) de Azure simplifica el código que escribe para integrarlo con otros servicios de Azure y de terceros.

- **Conexiones híbridas**: acceda a los datos locales mediante [conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y [redes virtual de Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integración con Visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación, consumo, depuración y administración de aplicaciones web, móviles y de API.

## Introducción a Servicios de aplicaciones

[Cree una aplicación web, móvil o lógica temporal](http://go.microsoft.com/fwlink/?LinkId=523751) inmediatamente y sin coste alguno, sin necesidad de proporcionar ninguna tarjeta de crédito, sin compromiso y sin complicaciones.

También puede abrir una [cuenta de Azure gratuita](https://azure.microsoft.com/pricing/free-trial/) y seguir uno de nuestros tutoriales de introducción:

* [Aplicaciones web](https://azure.microsoft.com/documentation/services/app-service/web/)
* [Aplicaciones móviles](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [Aplicaciones de API](https://azure.microsoft.com/documentation/services/app-service/api/)
* [Aplicaciones lógicas](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->