<properties 
	pageTitle="Creación de una presencia web global en Sitios web Azure" 
	description="En esta guía se ofrece información general de carácter técnico acerca de cómo hospedar el sito (.COM) de su organización en sitios web de Azure. Esto incluye la implementación, los dominios personalizados, SSL y la supervisión." 
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


# Creación de una presencia web global en Sitios web Azure

[Sitios web Azure] tiene todas las funciones que necesita para establecer una presencia web global para su sitio .COM. Independientemente del tamaño de su organización, necesita una plataforma robusta, segura y escalable para impulsar su negocio, el conocimiento de la marca y las comunicaciones con los clientes. Sitios web Azure puede ayudar a mantener su marca corporativa y su identidad con una continuidad del negocio respaldada por Microsoft.

> [AZURE.NOTE] Si desea obtener una introducción a Sitios web Azure antes de inscribirse para abrir una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

A continuación se muestra un ejemplo de un sitio web .COM que se ejecuta en Sitios web Azure. Se muestra lo que puede hacer simplemente al componer los sitios web de Azure junto con otros servicios con inversiones técnicas mínimas. **Haga clic en un elemento de la topografía para obtener más información sobre él.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> En esta guía se presentan algunas de las áreas y las tareas más comunes que se alinean con la ejecución de un sitio .COM público en Sitios web Azure. Sin embargo, hay otras soluciones comunes que se pueden implementar en Sitios web Azure. Para revisar estas soluciones, consulte las otras guías sobre <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">campañas de marketing digital</a> y <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">aplicaciones empresariales</a>.

### Aporte recursos existentes o créelos desde cero

Cree rápidamente nuevos sitios desde un CMS popular en la galería o aporte sus recursos web existentes en Sitios web Azure desde una variedad de lenguajes y marcos.

La galería de Azure ofrece plantillas de los sistemas de administración de contenido (CMS) de sitios web más conocidos, como [Orchard], [Umbraco], [Drupal] y [WordPress]. Puede crear un sitio web con un toque de su CMS favorito. Puede elegir entre varios back-ends de bases de datos para cubrir sus necesidades, entre las que se incluyen la [Base de datos SQL de Azure] y [MySQL].

Sus recursos web existentes pueden ejecutarse en Sitios web de Azure, ya sean de .NET, PHP, Java, Node.js o Python. Puede moverlos a Sitios web de Azure con sus herramientas [FTP] habituales o con su sistema de administración de control de código fuente. Sitios web Azure admite la publicación directa desde las opciones de control de código fuente más conocidas, como [Visual Studio], [Visual Studio Online] y [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Publicación de manera confiable

Publique su sitio web de forma confiable mediante la publicación continua directamente desde su sistema de control de código fuente existente y probar de forma activa el contenido. 

Durante la planificación, la creación de prototipos y la implementación inicial de un sitio, puede mirar las versiones funcionales reales del sitio web antes de lanzarlo de forma pública mediante la [implementación en un espacio de ensayo] de su Sitio web Azure. Al integrar el control de código fuente con Sitios web Azure, puede [publicar de forma continua] en un espacio de ensayo y cambiar a producción sin tiempo de inactividad cuando esté listo para hacerlo. Si surge algún problema en el sitio de producción, también se puede cambiar a una versión anterior del sitio inmediatamente. 

Asimismo, cuando planifique los cambios de un sitio web en vivo, puede [ejecutar pruebas A/B] con facilidad en las actualizaciones propuestas con la característica Prueba en producción y analizar el comportamiento real de los usuarios para ayudarle a tomar decisiones fundamentadas sobre el diseño del sitio.

### Marca y protección

Use el dominio de los sitios web de forma gratuita o asigne a su nombre de dominio registrado y proteja su marca con el certificado SSL firmado de CA.

El dominio **\*.azurewebsites.net** es gratuito cuando ejecuta su sitio web en Sitios web Azure. O bien, puede asignar el sitio web a un [dominio personalizado] (por ejemplo, contoso.com), que haya obtenido en cualquier registro de DNS, como GoDaddy.

Si recopila cualquier información de usuario, realizar labores de comercio electrónico o administre otra información confidencial, puede proteger la reputación de su marca y a sus clientes con [HTTPS]. El nombre de dominio **\*.azurewebsites.net** ya viene con un certificado SSL y, si usa su dominio personalizado, puede aportar su certificado SSL para Sitios web Azure. Hay un cargo mensual (prorrateado por horas) asociado a cada certificado SSL. Para obtener más información, consulte [Detalles de precios de Sitios web].

### Sea global

Sea global al proporcionarle a las oficinas regionales el Administrador de tráfico de Azure y entregar contenido con una velocidad increíble con CDN de Azure.

Para servir a clientes internacionales en sus respectivas regiones, use el [Administrador de tráfico de Azure] para enrutar a los visitantes del sitio a un sitio regional que ofrezca el mejor rendimiento. Como alternativa, puede distribuir uniformemente la carga del sitio en varias copias de su sitio web alojadas en varias regiones.

Ofrezca el contenido estático a los usuarios de todo el mundo con una velocidad increíble al [integrar su sitio web con CDN de Azure]. CDN de Azure almacena en caché el contenido estático en el [nodo CDN] más cercano al usuario, lo que reduce la latencia y las conexiones a su sitio web.

### Optimización

Optimice su sitio .COM mediante el escalado automático con Autoscale, el almacenamiento en caché con Azure Redis Cache, la ejecución de tareas en segundo plano con WebJobs y el mantenimiento de una alta disponibilidad con el Administrador de tráfico de Azure.

La capacidad de Sitios web Azure de [escalar vertical y horizontalmente] cubre las necesidades de su sitio .COM, independientemente del tamaño de la carga de trabajo. Escale horizontalmente de forma manual su sitio web a través del [Portal de administración de Azure], mediante programación a través de la [API de administración de servicios] o de [scripting de PowerShell], o automáticamente con la función Autoscale. En el plan de hospedaje **Standard**, Autoscale permite escalar horizontalmente un sitio web de forma automática, basándose en el uso de CPU. Para conocer los procedimientos recomendados, consulte la publicación de [Troy Hunt], [10 things I learned about rapidly scaling websites with Azure].

Consiga que su sitio web tenga más capacidad de respuesta con [Caché en Redis de Azure]. Úsela para almacenar en caché datos de bases de datos de back-end y otras cosas como el [estado de sesión e ASP.NET] y la [caché de resultados].

Mantenga una alta disponibilidad de su sitio web con [Azure Traffic Manager]. Gracias al método **Failover**, Traffic Manager enruta automáticamente el tráfico a un sitio secundario si se produce un problema en el sitio primario.

### Supervisión y análisis

Manténgase informado sobre el rendimiento de su sitio web con herramientas de Azure o de terceros. Reciba alertas sobre eventos críticos del sitio web. Obtenga fácilmente información sobre los usuarios con Application Insight o con los análisis de registros web de HDInsight. 

Obtenga una [vista rápida] de las métricas de rendimiento actuales del sitio web y de las cuotas de recursos en el panel de Sitios web Azure. Para obtener una vista completa de la aplicación en cuanto a disponibilidad, rendimiento y uso, utilice [Azure Application Insights] para ofrecer rápidamente información potente de uso, de diagnóstico y de solución de problemas. O bien, utilice una herramienta de terceros como [New Relic] para proporcionar datos de supervisión avanzada de sus sitios web.

En el plan de hospedaje **Standard**, la capacidad de respuesta del sitio de supervisión recibe notificaciones por correo electrónico cuando su sitio deja de responder. Para obtener más información, consulte [Inserción de notificaciones de alerta y administración de reglas de alerta en Azure].

### Use medios enriquecidos y llegue a todos los dispositivos

Haga que su sitio .COM sea más atractivo con medios enriquecidos, como:

-  La carga y el streaming de vídeos de forma global con [Servicios multimedia de Azure]
-  El envío de correos electrónicos a los usuarios con el [servicio SendGrid en Azure Marketplace]

## Más recursos

- [Documentación de Sitios web Azure](/es-es/documentation/services/websites/)
- [Mapa de aprendizaje de Sitios web Azure](websites-learning-map.md)
- [Blog de web de Azure](/blog/topics/web/)



[Sitios web Azure]:/es-es/services/websites/

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

[dominios personalizados]:/es-es/documentation/articles/web-sites-custom-domain-name/
[HTTPS]:/es-es/documentation/articles/web-sites-configure-ssl-certificate/
[Detalles de precios de Sitios web]:/es-es/pricing/details/web-sites/#service-ssl

[Administrador de tráfico de Azure]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integración de un sitio web de Azure con la red CDN de Azure]:/es-es/documentation/articles/cdn-websites-with-cdn/ 
[nodo CDN]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[escalar vertical y horizontalmente]:/es-es/manage/services/web-sites/how-to-scale-websites/
[Portal de administración de Azure]:http://manage.windowsazure.com/
[API de administración de servicios]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[Scripting de PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Caché en Redis de Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[estado de sesión e ASP.NET]:https://msdn.microsoft.com/es-es/library/azure/dn690522.aspx
[caché de resultados]:https://msdn.microsoft.com/es-es/library/azure/dn798898.aspx

[vista rápida]:/es-es/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/es-es/develop/net/how-to-guides/new-relic/
[Inserción de notificaciones de alerta y administración de reglas de alerta en Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Servicios multimedia de Azure]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Servicio SendGrid en Azure Marketplace]:/es-es/documentation/articles/sendgrid-dotnet-how-to-send-email/





<!--HONumber=42-->
