<properties 
	pageTitle="Creación de una aplicación de línea de negocio en Sitios web Azure" 
	description="En esta guía se ofrece información general de carácter técnico acerca de cómo usar Sitios web Azure para crear Intranet y aplicaciones de líneas de negocios. Esto incluye las estrategias de autenticación, la retransmisión del bus de servicio y la supervisión. 
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



# Creación de una aplicación de línea de negocio en Sitios web Azure

[Sitios web Azure] es una elección excelente para las aplicaciones de línea de negocio. Estas aplicaciones son aplicaciones de intranet que deberían protegerse para su uso comercial interno. Normalmente requieren autenticación, de forma habitual en un directorio corporativo, y con algún acceso o integración con servicios y datos locales. 

Existen ventajas importantes al mover aplicaciones de línea de negocio móviles a los sitios web Azure, como:

-  el escalado hacia arriba y abajo con cargas de trabajo dinámicas, como una aplicación que controle el rendimiento anual de revisión. Durante el período de revisión, el tráfico aumentará notablemente en el caso de las empresas de gran tamaño. Azure ofrece opciones de escalado que permiten a la empresa escalar su capacidad horizontalmente para soportar la carga durante el período de revisión de mucho tráfico y ahorrar dinero volviendo a reducir su capacidad el resto del año. 
-  una dedicación mayor al desarrollo de aplicaciones y menor a la adquisición y administración de infraestructuras
-  mejor compatibilidad para que los empleados y partners usen la aplicación desde cualquier lugar. Los usuarios no necesitan estar conectados a la red corporativa todo el tiempo para utilizar la aplicación, con lo que el departamento de TI evita complejas soluciones de proxy inverso. Existen diversas opciones de autenticación para garantizar la protección del acceso a las aplicaciones de empresa.

A continuación se muestra un ejemplo de una aplicación de línea de negocio que se ejecuta en Sitios web Azure. Se muestra lo que puede hacer simplemente al componer los Sitios web Azure junto con otros servicios con inversiones técnicas mínimas. **Haga clic en un elemento de la topografía para obtener más información sobre él.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>Nota</strong>
<p>En esta guía se muestran algunas de las áreas y las tareas más comunes que están alineadas con las aplicaciones de línea de negocio. Sin embargo, existen otras funciones de Sitios web Azure que puede utilizar en su implementación concreta. Para obtener información acerca de estas funciones, consulte también las otras guías sobre <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">presencia web mundial</a> y <a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">campañas de marketing digital</a>.</p>
</div>

### Aportación de los recursos existentes

Aporte los recursos web existentes a Sitios web de Azure de entre una variedad de lenguajes y marcos.

Sus recursos web existentes pueden ejecutarse en Sitios web de Azure, ya sean de .NET, PHP, Java, Node.js o Python. Puede moverlos a Sitios web de Azure con sus herramientas [FTP] habituales o con su sistema de administración de control de código fuente. Sitios web Azure admite la publicación directa desde las opciones de control de código fuente más conocidas, como [Visual Studio], [Visual Studio Online] y [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

### Protección de sus recursos

Proteja los recursos mediante el cifrado, autentique usuarios corporativos locales o remotos, y autorice el uso de recursos. 

Proteja los recursos internos contra interceptores con [HTTPS]. El nombre de dominio **\*.azurewebsites.net** ya viene con un certificado SSL y, si usa su dominio personalizado, puede aportar su certificado SSL para Sitios web Azure. Hay un cargo mensual (prorrateado por horas) asociado a cada certificado SSL. Para obtener más información, consulte [Detalles de precios de Sitios web].

[Autenticar a los usuarios] en el directorio corporativo. Sitios web Azure puede autenticar a los usuarios con proveedores de identidades locales, como los Servicios de federación de Active Directory (AD FS), o con un inquilino de Azure Active Directory que se haya sincronizado con su implementación de Active Directory corporativo. Los usuarios pueden acceder a sus propiedades web en Sitios web de Azure a través de un inicio de sesión único, tanto cuando se encuentran de forma local como de forma remota. Los servicios existentes, como Office 365 o Windows Intune, ya usan Azure Active Directory. Mediante [Easy Auth], la activación de la autenticación con el mismo inquilino de Azure Active Directory para su sitio web es muy fácil. 

[Autorice que los usuarios] usen sus propiedades web. Con un mínimo de código adicional, puede adoptar el mismo patrón de codificación de ASP.NET local en Sitios web de Azure, por ejemplo con la decoración `[Authorize]`. Conserve la misma flexibilidad de control de acceso detallado de las aplicaciones que mantiene de forma local.

### Conexión con recursos locales ###

Conéctese con los recursos o datos de su sitio web, ya sea en la nube por motivos de rendimiento o de forma local por cumplimiento normativo. Para obtener más información acerca de cómo mantener los datos en Azure, consulte [Centro de confianza de Azure]. 

Puede elegir varios back-ends de base de datos en Azure para cubrir las necesidades de su sitio web, entre los que se incluyen [Base de datos SQL de Azure] y [MySQL]. Al mantener los datos de forma segura en Azure, consigue que los datos estén más cerca de su sitio web geográficamente y optimiza su rendimiento.

Sin embargo, su empresa puede necesitar que sus datos se mantengan de forma local. Sitios web Azure permite configurar fácilmente una [conexión híbrida] con el recurso local, como un back-end de base de datos. Si desea una administración unificada de las conexiones locales, integre varios Sitios web Azure en una [Red virtual de Azure] que tenga una VPN de sitio a sitio. Así, tendrá acceso a recursos locales como si sus sitios web Azure estuvieran de forma local. [Enterprise Pizza: conexión de sitios web de forma local mediante el bus de servicio][enterprisepizza]

### Optimización

Optimice la aplicación de línea de negocio mediante el escalado automático con Autoscale, el almacenamiento en caché con Caché en Redis de Azure, la ejecución de tareas en segundo plano con WebJobs y el mantenimiento de una alta disponibilidad con Azure Traffic Manager.

La capacidad de Sitios web Azure para [escalar vertical y horizontalmente] cubre las necesidades de la aplicación de línea de negocio, independientemente del tamaño de la carga de trabajo. Escale horizontalmente de forma manual su sitio web a través del [Portal de administración de Azure], mediante programación a través de la [API de administración de servicios] o de [scripting de PowerShell], o automáticamente con la función Autoscale. En el plan de hospedaje **Standard**, Autoscale permite escalar horizontalmente un sitio web de forma automática, basándose en el uso de CPU. Para conocer los procedimientos recomendados, consulte la publicación de [Troy Hunt], [10 things I learned about rapidly scaling websites with Azure].

Consiga que su sitio web tenga más capacidad de respuesta con [Caché en Redis de Azure]. Úsela para almacenar en caché datos de bases de datos de back-end y otras cosas como el [estado de sesión e ASP.NET] y la [caché de resultados].

Mantenga una alta disponibilidad de su sitio web con [Azure Traffic Manager]. Gracias al método **Failover**, Traffic Manager enruta automáticamente el tráfico a un sitio secundario si se produce un problema en el sitio primario.

### Supervisión y análisis

Manténgase informado sobre el rendimiento de su sitio web con herramientas de Azure o de terceros. Reciba alertas sobre eventos críticos del sitio web. Obtenga fácilmente información sobre los usuarios con Application Insight o con los análisis de registros web de HDInsight. 

Obtenga una [vista rápida] de las métricas de rendimiento actuales del sitio web y de las cuotas de recursos en el panel de Sitios web Azure. Para obtener una vista completa de la aplicación en cuanto a disponibilidad, rendimiento y uso, utilice [Azure Application Insights] para ofrecer rápidamente información potente de uso, de diagnóstico y de solución de problemas. O bien, utilice una herramienta de terceros como [New Relic] para proporcionar datos de supervisión avanzada de sus sitios web.

En el plan de hospedaje **Standard**, la capacidad de respuesta del sitio de supervisión recibe notificaciones por correo electrónico cuando su sitio deja de responder. Para obtener más información, consulte [Inserción de notificaciones de alerta y administración de reglas de alerta en Azure].

## Más recursos

- [Documentación de Sitios web Azure](/es-es/documentation/services/websites/)
- [Mapa de aprendizaje de Sitios web Azure](/es-es/documentation/articles/websites-learning-map/)
- [Blog de web de Azure](/blog/topics/web/)



[Aplicaciones y Sitios web]:/es-es/services/websites/

[FTP]:/es-es/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/es-es/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/es-es/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/es-es/documentation/articles/web-sites-configure-ssl-certificate/
[Detalles de precios de Sitios web]:/es-es/pricing/details/web-sites/#service-ssl
[Autenticación de usuarios]:/es-es/documentation/articles/web-sites-authentication-authorization/
[Easy Auth]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Autorización de los usuarios]:/es-es/documentation/articles/web-sites-authentication-authorization/

[Centro de confianza de Microsoft Azure]:/es-es/support/trust-center/
[MySQL]:/es-es/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Base de datos SQL de Azure]:/es-es/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[conexión híbrida]:/es-es/documentation/articles/web-sites-hybrid-connection-get-started/
[Red virtual de Azure]:/es-es/documentation/articles/web-sites-integrate-with-vnet/

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





<!--HONumber=42-->
