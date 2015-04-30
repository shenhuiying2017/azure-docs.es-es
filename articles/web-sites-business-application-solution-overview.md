<properties 
	pageTitle="Creación de una aplicación web de línea de negocio en el Servicio de aplicaciones de Azure" 
	description="En esta guía se ofrece información general de carácter técnico acerca de cómo usar Aplicaciones web del Servicio de aplicaciones de Azure para crear Intranet y aplicaciones de línea de negocio. Esto incluye las estrategias de autenticación, la retransmisión del bus de servicio y la supervisión." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="cephalin"/>



# Creación de una aplicación web de línea de negocio en el Servicio de aplicaciones de Azure

Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) es una excelente opción para las aplicaciones de línea de negocio. Estas aplicaciones son aplicaciones de intranet que se deben proteger para uso empresarial interno. Normalmente requieren autenticación, generalmente en un directorio corporativo, y algún acceso a los datos o servicios locales o su integración con estos. 

Existen importantes ventajas en la migración de aplicaciones de línea de negocio a Aplicaciones web del Servicio de aplicaciones. Por ejemplo:

-  escalar y reducir verticalmente con cargas de trabajo dinámicas, como una aplicación que gestiona revisiones anuales del rendimiento. Durante el período de revisión, el tráfico alcanzaría niveles máximos en el caso de una gran empresa. Azure ofrece opciones de escalado que permiten a la empresa escalar su capacidad horizontalmente para soportar la carga durante el período de revisión de mucho tráfico y ahorrar dinero volviendo a reducir su capacidad el resto del año. 
-  posibilidad de centrarse más en el desarrollo de aplicaciones y menos en la adquisición y la administración de la infraestructura
-  mayor compatibilidad con el uso de la aplicación desde cualquier parte por empleados y socios. Los usuarios no necesitan estar conectados a la red corporativa todo el tiempo para utilizar la aplicación, con lo que el departamento de TI evita complejas soluciones de proxy inverso. Existen diversas opciones de autenticación para garantizar la protección del acceso a las aplicaciones de empresa.

A continuación se muestra un ejemplo de una aplicación de línea de negocio que se ejecuta en Aplicaciones web del Servicio de aplicaciones. Se demuestra lo que puede hacer con tan solo crear aplicaciones web junto con otros servicios con inversiones técnicas mínimas. **Haga clic en un elemento de la topografía para obtener más información sobre él.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> En esta guía se presentan algunos de las áreas y tareas más comunes relacionadas con el desarrollo de aplicaciones de línea de negocio. Sin embargo, existen otras funciones de Aplicaciones web del Servicio de aplicaciones de Azure que puede utilizar en su implementación concreta. Para revisar estas capacidades, consulte también las otras guías sobre [presencia web global](web-sites-global-web-presence-solution-overview.md) y [campañas de marketing digital](web-sites-digital-marketing-application-solution-overview.md).

## Traer los recursos existentes

Traiga sus recursos web existentes a Aplicaciones web del Servicio de aplicaciones desde una diversidad de lenguajes y marcos de trabajo.

Sus recursos web existentes se pueden ejecutar en Aplicaciones web del Servicio de aplicaciones, tanto si son .NET, PHP, Java, Node.js o Python. Además, puede moverlos a Aplicaciones web con las herramientas [FTP] que suele utilizar o con el sistema de administración de control del código fuente. Aplicaciones web es compatible con la publicación directa desde opciones conocidas de control del código fuente, como [Visual Studio], [Visual Studio Online] y [Git] (local, GitHub, BitBucket, DropBox, Mercurial, etc.).

## Protección de los activos

Proteja los activos mediante cifrado, autentique a los usuarios corporativos locales y remotos y autorice su uso de los recursos. 

Proteja los recursos internos frente a los espías con [HTTPS]. El nombre de dominio **\*.azurewebsites.net** ya incluye un certificado SSL, y si usa su dominio personalizado, puede utilizar su certificado SSL con él en Aplicaciones web del Servicio de aplicaciones. Existe un cargo mensual (se prorratea por horas) asociado con cada certificado SSL. Para obtener más información, consulte [Detalles de precios del Servicio de aplicaciones].

[Autenticación de usuarios] en el directorio corporativo. Aplicaciones web del Servicio de aplicaciones puede autenticar a los usuarios con proveedores de identidad locales, como los Servicios de federación de Active Directory (AD FS) o con un inquilino de Azure Active Directory que se haya sincronizado con su implementación de Active Directory corporativa. Los usuarios pueden acceder a sus propiedades web en Aplicaciones web mediante un solo inicio de sesión tanto si trabajan de forma local como remota. Los servicios existentes, como Office 365 o Windows Intune, ya usan Azure Active Directory. A través de [Easy Auth], activar la autenticación con el mismo inquilino de Azure Active Directory para su aplicación web es muy fácil. 

[Autorización de usuarios] el uso de las propiedades web. Con un código adicional mínimo, puede llevar el mismo patrón de codificación de ASP.NET local a Aplicaciones web del Servicio de aplicaciones mediante, por ejemplo, la acción `[Autorizar]`. Se conserva la misma flexibilidad en el control de acceso específico a las aplicaciones que el que mantiene en local.

## Conexión a los recursos locales ##

Conéctese a los datos o recursos de sus aplicaciones web, tanto si están en la nube para aumentar el rendimiento o en local con vistas al cumplimiento. Para obtener más información sobre cómo mantener datos en Azur, consulte [Centro de confianza de Azure]. 

Puede elegir entre varios back-ends de base de datos de Azure para satisfacer las necesidades de su aplicación web, incluidos [Base de datos SQL de Azure] y [MySQL]. Mantener los datos protegidos en Azure permite acercar los datos geográficamente a su aplicación web y optimiza su rendimiento.

Sin embargo, puede que su empresa necesite que los datos se mantengan en el entorno local. Aplicaciones web del Servicio de aplicaciones le permite configurar fácilmente una [conexión híbrida] a su recurso local, como un back-end de base de datos. Si desea la administración unificada de sus conexiones locales, integre muchas aplicaciones web con una [Red virtual de Azure] que tenga una VPN sitio a sitio. Luego, puede acceder a los recursos locales como si sus aplicaciones web fueran locales.

## Optimización

Optimice su aplicación de línea de negocio. Puede escalarla automáticamente con el escalado automático, almacenar los datos en caché con la Caché en Redis de Azure, ejecutar tareas en segundo plano con trabajos web y mantener una alta disponibilidad con el Administrador de tráfico de Azure.

La posibilidad de Aplicaciones web del Servicio de aplicaciones de [escalado vertical y horizontal] satisface la necesidad de su aplicación de línea de negocio, independientemente del tamaño de la carga de trabajo. Escale horizontalmente su aplicación web de forma manual a través del [Portal de administración de Azure], mediante programación a través de la [API de administración de servicios] o los [scripts de PowerShell], o bien de forma automática a través de la característica de escalado automático. En el nivel **Estándar**, el escalado automático le permite escalar horizontalmente una aplicación web de forma automática en función del uso de CPU. Para ver los procedimientos recomendados, consulte el artículo [10 things I learned about rapidly scaling web apps with Azure] de [Troy Hunt].

Dote a sus aplicaciones de una mayor capacidad de respuesta con la [Caché en Redis de Azure]. Úsela para almacenar en caché datos de bases de datos back-end, como el [estado de sesión de ASP.NET] y la [caché de resultados].

Mantenga la alta disponibilidad de su aplicación web con el [Administrador de tráfico de Azure]. Mediante el método de **conmutación por error**, el Administrador de tráfico enruta automáticamente el tráfico a un sitio secundario si existe un problema en el principal.

## Supervisión y análisis

Permanezca actualizado sobre el rendimiento de su aplicación web con Azure o con herramientas de terceros. Reciba alertas sobre eventos críticos de sus aplicaciones web. Obtenga información sobre los usuarios con Application Insight o el análisis de registros web de HDInsight. 

Tenga una [visión rápida] de las métricas de rendimiento actuales de la aplicación y de las cuotas de recursos en la hoja de la aplicación web en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para obtener una visión de 360° de su aplicación en lo relativo a disponibilidad, rendimiento y uso, utilice [Azure Application Insights] para que le proporcione soluciones de problemas rápidas y eficaces, diagnóstico e información de uso. O bien, use una herramienta de terceros como [New Relic] para obtener datos de supervisión avanzados para sus aplicaciones web.

En el nivel **Estándar**, supervise la capacidad de respuesta de la aplicación y reciba notificaciones por correo electrónico cada vez que su aplicación deje de responder. Para obtener más información, consulte [Instrucciones acerca de cómo: notificaciones de alerta y administración de reglas de alerta en Azure].

## Más recursos

- [Documentación de Aplicaciones web del Servicio de aplicaciones](/services/app-service/web/)
- [Mapa de aprendizaje para Aplicaciones web del Servicio de aplicaciones de Azure](websites-learning-map.md)
- [Blog web de Azure](/blog/topics/web/)

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [El servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)


[Servicio de aplicaciones de Azure]: /services/app-service/web/

[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Online]:cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[HTTPS]:web-sites-configure-ssl-certificate.md
[Detalles de precios del Servicio de aplicaciones]: /pricing/details/app-service/#ssl-connections
[Autenticación de usuarios]:web-sites-authentication-authorization.md
[Easy Auth]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Autorización de usuarios]:web-sites-authentication-authorization.md

[Centro de confianza de Azure]:/support/trust-center/
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[Base de datos SQL de Azure]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[conexión híbrida]:web-sites-hybrid-connection-get-started.md
[Red virtual de Azure]:web-sites-integrate-with-vnet.md

[escalado vertical y horizontal]:web-sites-scale.md
[Portal de administración de Azure]:http://manage.windowsazure.com/
[API de administración de servicios]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[Scripts de PowerShell]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Caché en Redis de Azure]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[Estado de sesión de ASP.NET]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[caché de resultados]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[visión rápida]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:store-new-relic-cloud-services-dotnet-application-performance-management.md
[Instrucciones acerca de cómo: notificaciones de alerta y administración de reglas de alerta en Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx


<!--HONumber=52-->