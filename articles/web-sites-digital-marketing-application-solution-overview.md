<properties 
	pageTitle="Creación de una campaña de marketing digital en Sitios web Azure" 
	description="En esta guía se ofrece información general de carácter técnico acerca de cómo usar Sitios web Azure para crear campañas de marketing digital. Esto incluye la implementación, la integración de medios sociales, las estrategias de escalado y la supervisión." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>

# Campañas de marketing digital en Sitios web Azure
[Sitios web Azure] es una opción excelente para las campañas de marketing digital. Las campañas de marketing digital son normalmente de corta duración y están diseñadas para impulsar objetivos de marketing a corto plazo. Hay dos escenarios principales que se han de tener en cuenta. En el primer escenario, una empresa de marketing externa crea y administra la campaña para sus clientes mientras dura la promoción. En el segundo escenario, la empresa de marketing crea y, a continuación, transfiere la titularidad de los recursos de la campaña de marketing digital a sus clientes. A continuación, el cliente ejecuta y administra la campaña de marketing digital por sí mismo. Es una buena opción para ambos escenarios. 

> [AZURE.NOTE] Si desea obtener una introducción a Sitios web Azure antes de inscribirse para abrir una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

A continuación se muestra un ejemplo de una campaña de marketing digital global, multicanal con Sitios web Azure. Se muestra lo que puede hacer simplemente al componer los Sitios web Azure junto con otros servicios con inversiones técnicas mínimas. **Haga clic en un elemento de la topografía para obtener más información sobre él.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> En esta guía se muestran algunas de las áreas y las tareas más comunes que se alinean con la ejecución de una campaña de marketing digital en Sitios web Azure. Sin embargo, hay otras soluciones comunes que se pueden implementar en Sitios web Azure. Para revisar estas soluciones, consulte las otras guías sobre <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">presencia web global</a> y <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">aplicaciones empresariales</a>.

### Aporte recursos existentes o créelos desde cero

Cree rápidamente nuevos sitios desde un CMS popular en la galería o aporte sus recursos web existentes en Sitios web Azure desde una variedad de lenguajes y marcos.

La galería de Azure ofrece plantillas de los sistemas de administración de contenido (CMS) de sitios web más conocidos, como [Orchard], [Umbraco], [Drupal] y [WordPress]. Puede crear un sitio web con un toque de su CMS favorito. Puede elegir entre varios back-ends de bases de datos para cubrir sus necesidades, entre las que se incluyen la [Base de datos SQL de Azure] y [MySQL].

Sus recursos web existentes pueden ejecutarse en Sitios web de Azure, ya sean de .NET, PHP, Java, Node.js o Python. Puede moverlos a Sitios web Azure con las herramientas [FTP] que le resulten familiares. Si crea campañas de marketing digital con frecuencia, es posible que disponga de recursos web existentes en un sistema de administración de control de código fuente. Puede implementar en Sitios web Azure directamente desde las opciones de control de origen más conocidas, como [Visual Studio], [Visual Studio Online] y [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Conserve la agilidad

Manténgase ágil mediante la publicación continua directamente desde su control de código fuente existente y ejecute pruebas A/B en Sitios web Azure. 

Durante la planificación, la creación de prototipos y la implementación inicial de un sitio, usted y su cliente podrán ver las versiones funcionales reales del sitio de campaña antes de lanzarlo mediante la [implementación en un espacio de ensayo] de su Sitio web Azure. Al integrar el control de código fuente con Sitios web de Azure, puede [publicar continuamente] en un espacio de ensayo y cambiar a producción sin tiempo de inactividad cuando esté listo. 

Asimismo, cuando planifique los cambios de un sitio web en vivo, puede [ejecutar pruebas A/B] con facilidad en las actualizaciones propuestas con la característica Prueba en producción y analizar el comportamiento real de los usuarios para ayudarle a tomar decisiones fundamentadas sobre el diseño del sitio.


### Entre en las redes sociales

La campaña de marketing digital en Sitios web Azure puede integrarse con los medios sociales mediante la autenticación de los proveedores más populares, como Facebook y Twitter. Para ver un ejemplo de este enfoque con una aplicación ASP.NET, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure]. 

Además, normalmente cada sitio de red social proporciona información acerca de otras formas para su integrarlo con .NET y muchos otros marcos.

### Use medios enriquecidos y llegue a todos los dispositivos

Enriquezca su campaña de marketing digital con otros servicios de Azure, como:

-  La carga y el streaming de vídeos de forma global con [Servicios multimedia de Azure]
-  El envío de correos electrónicos a los usuarios con el [servicio SendGrid en Azure Marketplace]
-  El establecimiento de la presencia en dispositivos Windows, iOS y Android con [Servicios móviles]
-  El envío de notificaciones de inserción a millones de dispositivos con el [Centro de notificaciones]

### Sea global

Sea global al proporcionarle a las oficinas regionales el Administrador de tráfico de Azure y entregar contenido con una velocidad increíble con CDN de Azure.

Para servir a clientes internacionales en sus respectivas regiones, use el [Administrador de tráfico de Azure] para enrutar a los visitantes del sitio a un sitio regional que ofrezca el mejor rendimiento. Como alternativa, puede distribuir uniformemente la carga del sitio en varias copias de su sitio web alojadas en varias regiones.

Ofrezca el contenido estático a los usuarios de todo el mundo con una velocidad increíble al [integrar su sitio web con CDN de Azure]. CDN de Azure almacena en caché el contenido estático en el [nodo CDN] más cercano al usuario, lo que reduce la latencia y las conexiones a su sitio web.

### Optimización

Optimice su sitio mediante el escalado automático con Autoscale, el almacenamiento en caché con Azure Redis Cache, la ejecución de tareas en segundo plano con WebJobs y el mantenimiento de una alta disponibilidad con Azure Traffic Manager.

La capacidad de sitios web Azure de [escalado vertical y horizontal] es perfecta para las cargas de trabajo impredecibles, que es el caso de campañas de marketing digital. Escale horizontalmente de forma manual su sitio web a través del [Portal de administración de Azure], mediante programación a través de la [API de administración de servicios] o de [scripting de PowerShell], o automáticamente con la función Autoscale. En el plan de hospedaje **Standard**, Autoscale permite escalar horizontalmente un sitio web de forma automática, basándose en el uso de CPU. Esta característica ayuda a maximizar la agilidad y minimizar los costes al mismo tiempo, mediante el escalado horizontal del sitio web solo cuando sea necesario, según la actividad de usuario. Para conocer los procedimientos recomendados, consulte la publicación de [Troy Hunt], [10 things I learned about rapidly scaling websites with Azure].

Consiga que su sitio web tenga más capacidad de respuesta con [Caché en Redis de Azure]. Úsela para almacenar en caché datos de bases de datos de back-end y otras cosas como el [estado de sesión e ASP.NET] y la [caché de resultados].

Mantenga una alta disponibilidad de su sitio web con [Azure Traffic Manager]. Gracias al método **Failover**, Traffic Manager enruta automáticamente el tráfico a un sitio secundario si se produce un problema en el sitio primario.

### Supervisión y análisis

Manténgase informado sobre el rendimiento de su sitio web con herramientas de Azure o de terceros. Reciba alertas sobre eventos críticos del sitio web. Obtenga fácilmente información sobre los usuarios con Application Insight o con los análisis de registros web de HDInsight. 

Obtenga una [vista rápida] de las métricas de rendimiento actuales del sitio web y de las cuotas de recursos en el panel de Sitios web Azure. Para obtener una vista completa de la aplicación en cuanto a disponibilidad, rendimiento y uso, utilice [Azure Application Insights] para ofrecer rápidamente información potente de uso, de diagnóstico y de solución de problemas. O bien, utilice una herramienta de terceros como [New Relic] para proporcionar datos de supervisión avanzada de sus sitios web.

En el plan de hospedaje **Standard**, la capacidad de respuesta del sitio de supervisión recibe notificaciones por correo electrónico cuando su sitio deja de responder. Para obtener más información, consulte [Inserción de notificaciones de alerta y administración de reglas de alerta en Azure].

## Más recursos

- [Documentación de Sitios web Azure](/es-es/documentation/services/websites/)
- [Mapa de aprendizaje de Sitios web Azure](websites-learning-map.md)
- [Blog de web de Azure](/blog/topics/web/)


[Aplicaciones y Sitios web]:/es-es/services/websites/

[Orchard]:/es-es/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/es-es/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/es-es/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/es-es/documentation/articles/web-sites-php-web-site-gallery/
  
[MySQL]:/es-es/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Base de datos SQL de Azure]:/es-es/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/es-es/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/es-es/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/es-es/documentation/articles/web-sites-publish-source-control/

[Implementación de ensayo en Sitios web Microsoft Azure]:/es-es/documentation/articles/web-sites-staged-publishing/ 
[continuously publish]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[run A/B tests]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure]:/es-es/develop/net/tutorials/web-site-with-sql-database/

[Servicios multimedia de Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Servicio SendGrid en Azure Marketplace]:/es-es/documentation/articles/sendgrid-dotnet-how-to-send-email/
[Servicios móviles]:/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[Centro de notificaciones]:/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Administrador de tráfico de Azure]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integración de un sitio web de Azure con la red CDN de Azure]:/es-es/documentation/articles/cdn-websites-with-cdn/ 
[Nodo de CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[escalado vertical y horizontal]:/es-es/manage/services/web-sites/how-to-scale-websites/
[Portal de administración de Azure]:http://manage.windowsazure.com/
[Referencia de la API de REST de administración de servicios]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[Scripting de PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Caché en Redis de Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[Estado de la sesión de ASP.NET]:https://msdn.microsoft.com/es-es/library/azure/dn690522.aspx
[caché de resultados]:https://msdn.microsoft.com/es-es/library/azure/dn798898.aspx

[vista rápida]:/es-es/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/es-es/develop/net/how-to-guides/new-relic/
[Uso de notificaciones de alerta y administración de reglas de alerta en Azure].:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  




<!--HONumber=42-->
