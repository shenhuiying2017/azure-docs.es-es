<properties
	pageTitle="Introducción a Aplicaciones web | Microsoft Azure"
	description="Vea cómo el Servicio de aplicaciones de Azure lo ayuda a desarrollar y hospedar aplicaciones web."
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/16/2016"
	ms.author="tdykstra"/>

# Introducción a Aplicaciones web

*Aplicaciones web del Servicio de aplicaciones* es una plataforma de procesos completamente administrada que se ha optimizado para el hospedaje de sitios y aplicaciones web. La oferta de [plataforma como servicio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure lo permite centrarse en la lógica de negocios mientras Azure se encarga de la infraestructura para ejecutar y escalar las aplicaciones.

Para ver un vídeo de introducción de 5 minutos, consulte [Azure App Service Web Apps with Yochay Kiriaty](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/) (Aplicaciones web del Servicio de aplicaciones de Azure con Yochay Kiriaty).

## ¿Qué es una aplicación web en el Servicio de aplicaciones?

En el Servicio de aplicaciones, una *aplicación web* son recursos de procesos que Azure proporciona para hospedar un sitio web o una aplicación web.

Los recursos de procesos pueden ser máquinas virtuales compartidas o dedicadas, según el plan de tarifa que elija. El código de la aplicación se ejecuta en una máquina virtual administrada que se aísla de otros clientes.

El código puede utilizar cualquier lenguaje o marco que sea compatible con el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md), como ASP.NET, Node.js, Java, PHP o Python. También puede ejecutar scripts que usen [PowerShell y otros lenguajes de scripting](web-sites-create-web-jobs.md#acceptablefiles) en una aplicación web.

Para ver ejemplos de escenarios de aplicación típicos para los que puede usar Aplicaciones web, consulte [Escenarios de aplicaciones web](https://azure.microsoft.com/documentation/scenarios/web-app/).

## ¿Por qué usar Aplicaciones web?

Estas son algunas características clave de Aplicaciones web:

- **Uso familiar y rápido**: use sus conocimientos para codificar en el lenguaje, el marco y el entorno de desarrollo integrado (IDE) que prefiera. Aprovisione nuevas aplicaciones web e implemente el código en ellas en segundos.

- **Carácter empresarial**: Aplicaciones web se ha diseñado para compilar y hospedar aplicaciones críticas seguras. Compile aplicaciones empresariales integradas de Active Directory que se conecten de forma segura a recursos locales y hospédelas en una plataforma segura en la nube que es compatible con [ISO](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [SOC](https://www.microsoft.com/TrustCenter/Compliance/SOC) y [PCI](https://www.microsoft.com/TrustCenter/Compliance/pci), Todo ello con un [Acuerdo de Nivel de servicio](https://azure.microsoft.com/support/legal/sla/app-service/) empresarial.

- **Escala global**: Aplicaciones web le permite escalar [vertical](../app-service/app-service-scale.md) u [horizontalmente](../azure-portal/insights-how-to-scale.md) de forma rápida para hacer frente a cualquier carga de trabajo entrante de los clientes. Seleccione manualmente el número y el tamaño de las máquinas virtuales o configure el escalado automático en función de la carga o la programación. La infraestructura de centro de datos global de Microsoft hospeda las aplicaciones web y facilita la tarea replicar datos y servicios de hospedaje en varias ubicaciones.

- **Azure Marketplace**: seleccione en una [lista de plantillas de aplicaciones web](https://azure.microsoft.com/marketplace/) que sigue ampliándose. Aproveche lo mejor de la comunidad de aplicaciones OSS mediante la instalación con un solo clic de paquetes como Wordpress, Joomla y Drupal.

- **Integración continua**: configure los flujos de trabajo de [integración e implementación continuas](app-service-continous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. Compile, pruebe e implemente de forma automática la aplicación web cada vez que se inserte el código en el repositorio o se completen pruebas de integración satisfactoriamente.

- **Entornos de prueba y ensayo**: ponga en práctica una [implementación de ensayo](web-sites-staged-publishing.md) para comprobar el código en un entorno de preproducción que sea idéntico al de producción. Cuando esté listo, publique una nueva versión de la aplicación sin tiempo de inactividad mediante una operación de intercambio.

- **Prueba de producción**: lleve las implementaciones de ensayo a un nivel superior y [realice pruebas A/B](app-service-web-test-in-production-get-start.md) para comprobar el nuevo código con una fracción configurable de su tráfico real.

- **Trabajos web**: [ejecute cualquier programa o script](web-sites-create-web-jobs.md) en las máquinas virtuales de Aplicaciones web. Ejecute trabajos de forma continua, según una programación o desencadenados por eventos. El [SDK de WebJobs](websites-dotnet-webjobs-sdk-get-started.md) de Azure simplifica el código que se escribe para integrarse con otros servicios de Azure, como las colas, los blobs y las tablas de Almacenamiento, así como las colas y los temas del Bus de servicio.

- **Conexiones híbridas**: acceda a los datos locales mediante [conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y una [red virtual](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integración con Visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación, consumo, depuración y administración de las aplicaciones web. Para más información, consulte [Anuncio de la versión 2.8.1 del SDK de Azure para .NET](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Además, una aplicación web puede aprovechar las características ofrecidas por [Aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md) (como la compatibilidad con CORS) y [Aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md) (por ejemplo, las notificaciones push). Lo contrario también es cierto: puede usar una aplicación de API o móvil para hospedar una aplicación web y aprovechar las características de Aplicaciones web, como el escalado automático y la implementación de ensayo. La única diferencia entre estos tres tipos de aplicaciones (API, web, móvil) radica en el nombre y el icono utilizados para ellas en el Portal de Azure. Para más información sobre los tipos de aplicaciones del Servicio de aplicaciones, consulte [¿Qué es el Servicio de aplicaciones de Azure?](../app-service/app-service-value-prop-what-is.md)

## Introducción

Para comenzar implementando código de ejemplo en una nueva aplicación web en el Servicio de aplicaciones, siga el tutorial [Implementación de su primera aplicación web en Azure en 5 minutos](app-service-web-get-started.md). Necesitará una cuenta gratis de Azure.

Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<!---HONumber=AcomDC_0525_2016-->