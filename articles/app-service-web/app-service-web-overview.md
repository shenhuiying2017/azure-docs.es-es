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
	ms.date="05/25/2016"
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

Estas son algunas características clave del Servicio de aplicaciones que se aplican a Aplicaciones web:

- **Plataforma totalmente administrada**: revisiones automáticas del sistema operativo y el marco, compatibilidad integrada para copia de seguridad y recuperación ante desastres. 

- **Uso de sus conocimientos**: codifique en su lenguaje, marco y entorno de desarrollo favoritos. El Servicio de aplicaciones es compatible con .NET, Node.js, Java, PHP y Python.

- **Rápida implementación**: aprovisione nuevas aplicaciones e implemente el código en ellas en segundos.

- **Integración continua**: configure la [integración y la implementación continuas](../app-service-web/app-service-continous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket.

- **Entornos de prueba y ensayo**: ponga en práctica una [implementación de ensayo](../app-service-web/web-sites-staged-publishing.md) para comprobar el código en un entorno de preproducción que sea idéntico al de producción. Cuando esté listo, publique una nueva versión de la aplicación sin tiempo de inactividad mediante una operación de intercambio.

- **Prueba de producción**: lleve las implementaciones de ensayo a un nivel superior y [realice pruebas A/B](../app-service-web/app-service-web-test-in-production-get-start.md) para comprobar el nuevo código con una fracción configurable de su tráfico real.

- **Autenticación y autorización**: puede proteger una aplicación frente al acceso no autenticado sin realizar ningún cambio en el código. Los servicios de autenticación integrados protegen las aplicaciones frente al acceso de otros servicios, usuarios o clientes que representan a los usuarios. Los proveedores de identidad compatibles incluyen Azure Active Directory, Facebook, Twitter, Google y cuenta Microsoft. Para más información, consulte [Autenticación y autorización en el Servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).

- **Conexión a cualquier servicio**: conecte su aplicación a sistemas empresariales o plataformas de software como servicio (SaaS) en cuestión de minutos con los [conectores](../connectors/apis-list.md) incorporados. Elija entre más de 50 conectores para sistemas empresariales, como SAP, Siebel y Oracle, servicios de SaaS empresariales conocidos como Salesforce y Office 365 y servicios de Internet populares, como Facebook, Twitter y Dropbox.

- **Escala global**: escale [vertical](../app-service/app-service-scale.md) u [horizontalmente](../azure-portal/insights-how-to-scale.md) para hacer frente a cualquier carga de trabajo entrante de los clientes. Seleccione manualmente el número y el tamaño de las máquinas virtuales o configure el escalado automático en función de la carga o la programación. La infraestructura de centro de datos global de Microsoft hospeda las aplicaciones y facilita la tarea de replicar datos y servicios de hospedaje en varias ubicaciones.

- **Carácter empresarial**: el Servicio de aplicaciones se ha diseñado para compilar y hospedar aplicaciones críticas seguras. Compile aplicaciones empresariales integradas de Active Directory que se conecten de forma segura a recursos locales y hospédelas en una plataforma segura en la nube que es compatible con [ISO, SOC y PCI](https://www.microsoft.com/TrustCenter/), todo ello con un [contrato de nivel de servicio](https://azure.microsoft.com/support/legal/sla/app-service/) empresarial.

- **Azure Marketplace**: seleccione en una [lista de plantillas de aplicaciones](https://azure.microsoft.com/marketplace/) que sigue ampliándose. Aproveche lo mejor de la comunidad de aplicaciones OSS mediante la instalación con un solo clic de paquetes como Wordpress, Joomla y Drupal.

- **WebJobs**: [ejecute cualquier programa o script](../app-service-web/web-sites-create-web-jobs.md) en las máquinas virtuales del Servicio de aplicaciones. Ejecute trabajos de forma continua, según una programación o desencadenados por eventos. El [SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) de Azure simplifica el código que escribe para integrarlo con otros servicios de Azure y de terceros.

- **Conexiones híbridas**: acceda a los datos locales mediante [conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y [redes virtual de Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integración con Visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación, consumo, depuración y administración de aplicaciones web, móviles y de API.

Además, una aplicación web puede aprovechar las características ofrecidas por [Aplicaciones de API](../app-service-api/app-service-api-apps-why-best-platform.md) (como la compatibilidad con CORS) y [Aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md) (por ejemplo, las notificaciones push). La única diferencia entre estos tres tipos de aplicaciones (API, web, móvil) radica en el nombre y el icono utilizados para ellas en el Portal de Azure. Para más información sobre los tipos de aplicaciones del Servicio de aplicaciones, consulte [¿Qué es el Servicio de aplicaciones de Azure?](../app-service/app-service-value-prop-what-is.md)

Además de Aplicaciones web en el Servicio de aplicaciones, Azure ofrece otros servicios que se pueden utilizar para hospedar aplicaciones web y sitios web. En la mayoría de los casos, Aplicaciones web es la mejor opción. Para arquitectura de microservicios, puede usar [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric), y si necesita más control sobre las máquinas virtuales en las que se ejecuta el código, considere la posibilidad de utilizar [Máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para más información sobre qué servicio de Azure elegir, consulte [Comparación de Servicio de aplicaciones de Azure, Servicios en la nube de Azure, Máquinas virtuales de Azure y Azure Service Fabric](choose-web-site-cloud-service-vm.md).

## Introducción

Para comenzar implementando código de ejemplo en una nueva aplicación web en el Servicio de aplicaciones, siga el tutorial [Implementación de su primera aplicación web en Azure en 5 minutos](app-service-web-get-started.md). Necesitará una cuenta gratis de Azure.

Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<!---HONumber=AcomDC_0601_2016-->