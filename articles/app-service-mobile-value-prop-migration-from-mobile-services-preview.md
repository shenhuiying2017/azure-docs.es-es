<properties
	pageTitle="Uso de Servicios móviles: ¿cómo ayuda el Servicio de aplicaciones?"
	description="Obtenga información sobre qué ventajas aporta Servicios de aplicaciones a los proyectos de Servicios móviles existentes."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Uso de Servicios móviles: ¿cómo ayuda el Servicio de aplicaciones?

##Información general
Servicios móviles existentes es seguro y seguirá siendo compatible. Sin embargo hay una serie de ventajas que proporciona la plataforma  *Azure App Service* para su aplicación móvil que no está disponible hoy en día con Servicios móviles:  

- Oferta más simple, más fácil y más rentable para las aplicaciones que incluyen clientes móviles y web
- Nuevas características de host como trabajos web, CNAME personalizado y una mejor supervisión
- Integración inmediata con Office 365, Dynamics CRM, Salesforce y otras API de SaaS fundamentales.
- Compatibilidad con el código de back-end de Java y PHP, además de Node.js y .NET 
- Integración inmediata con Administrador de tráfico
- Conectividad a los recursos locales y VPN mediante una red virtual además de las conexiones híbridas
- Supervisión y solución de problemas para su aplicación mediante NewRelic o AppInsights, así como alertas
- Espectro más completo de los recursos de procesos subyacentes, por ejemplo, los tamaños de máquina virtual 
- Escalado automático integrado, equilibrio de carga y supervisión del rendimiento
- Capacidades de prueba en producción, reversión, copia de seguridad y almacenamiento provisional incorporadas 

## Nuevas características de hospedaje
En *Azure App Service*, código de back-end de  *Mobile App* se ejecuta en el mismo contenedor que la aplicación web y la aplicación de la API. De esta forma, puede beneficiarse de todas las características de este contenedor, incluidas la que no están actualmente en Servicios móviles: 

- adición de lógica de back-end de ejecución continua a través de Trabajos web 
- garantía de que el código de back-end siempre está ejecutándose
- uso de CNames para proporcionar nombres descriptivos y estables en los extremos de back-end móvil
- escala geográfica de la aplicación con Administrador de tráfico


##Conexión a *Mobile App* para la API de SaaS
*Servicio de aplicaciones de Azure* facilita la conexión de su aplicación móvil con API de Saas, incluidas Office 365, Dynamics, Salesforce, SAP, etc. *Azure App Service* ofrece autenticación inmediata en nombre del usuario y le permite un verdadero inicio de sesión único en todas las API de Saas que usa mediante la asociación de tokens para API de Saas individuales con la identidad principal

##Acceso a datos locales con red virtual
Con Servicios móviles ahora puede usar las conexiones híbridas para acceder a los recursos locales. Sin embargo, hay situaciones en las que es preferible una solución de VPN. Con *Azure App Service* puede usar la red virtual de Azure para el código de back-end de la aplicación móvil.

##Uso del lenguaje de back-end favorito
*Servicio de aplicaciones de Azure* ofrece una compatibilidad más amplia y completa para plataformas para desarrolladores incluidos Java, PHP y Python, además de .NET y Node.js compatibles con Servicios móviles.

##Configuración de la escala automática
Con Servicios móviles, todas las instancias de su código de back-end se instalaron en máquinas virtuales pequeñas. *Azure App Service* le permite seleccionar el tamaño de las máquinas virtuales desde un conjunto mucho más completo de opciones. También puede escalar de forma vertical u horizontal rápidamente para controlar cualquier carga de cliente entrante basándose en varias métricas de rendimiento. 

##Sepa lo que ocurre
Reaccione a los problemas en tiempo real gracias a la supervisión y a las alertas que permiten el aviso automático a usted o a su equipo. Integre análisis de aplicaciones avanzadas y la funcionalidad de supervisión de New Relic y AppInsights para obtener una perspectiva incluso más completa acerca de cómo funciona la aplicación móvil. Con *Azure App Service* puede ahora mediante programación y a través del portal configurar alertas basadas en una serie de métricas de rendimiento.

##Proteja los activos
Realice una copia de seguridad automática del back-end y la base de datos. El código y los datos están seguros frente a desastres y se restauran fácilmente, por lo que puede llevar a cabo su actividad comercial con confianza.

##Preparado, listo...¡ya!
Con *Azure App Service* ahora puede crear varios entornos de almacenamiento provisional y prueba privados para las aplicaciones móviles. Úselos para realizar la prueba antes de la implementación. Cambie a una producción sin tiempo de inactividad. Las aplicaciones web se cargan previamente, por lo que se garantiza la mejor experiencia del cliente.



En el momento en el que la característica de *Azure App Service Mobile App* se encuentre disponible de forma general, proporcionaremos una experiencia de migración continua para los Servicios móviles existentes para el servicio de aplicación que elija para la migración.  Mientras tanto, puede empezar a explorar *App Service* y aprovechar *App Service* para el servicio móvil existente siguiendo este [tutorial](app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md).



<!--HONumber=49-->