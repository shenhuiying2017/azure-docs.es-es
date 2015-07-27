<properties 
	pageTitle="Recursos de documentación de WebJobs de Azure" 
	description="Recursos recomendados para aprender a utilizar WebJobs de Azure y el SDK de WebJobs de Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="tdykstra"/>

# Recursos de documentación de WebJobs de Azure

## Información general

Este tema establece vínculos a recursos de documentación acerca de cómo usar WebJobs de Azure y el SDK de WebJobs de Azure. WebJobs de Azure proporciona una manera fácil de ejecutar scripts o programas como procesos en segundo plano en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). Puede cargar y ejecutar un archivo ejecutable como cmd, bat, exe (.NET), ps1, sh, php, py, js y jar. Estos programas se ejecutan como WebJobs según una programación (cron) o de forma continua.

El SDK de WebJobs facilita el uso del Almacenamiento de Azure. El SDK de WebJobs tiene un sistema de enlace y desencadenador que funciona con Blobs de almacenamiento, Colas y Tablas de Microsoft Azure, así como con Colas de Bus de servicio.

La creación, implementación y administración de WebJobs se realiza de manera fluida gracias al conjunto de herramientas integradas en Visual Studio. Puede crear WebJobs a partir de plantillas, así como publicarlos y administrarlos (procesos de ejecución, detención, supervisión o implementación).

El panel de WebJobs en el Portal de Azure proporciona capacidades de administración eficaces que ofrecen un control completo sobre la ejecución de WebJobs, incluida la capacidad para invocar funciones individuales dentro de WebJobs. El panel también muestra los tiempos de ejecución de las funciones y el resultado del registro.

##<a name="getstarted"></a>Introducción a WebJobs y el SDK de WebJobs

* [Introducción a WebJobs de Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs are awesome and you should start using them right now!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (¡Los WebJobs de Azure son impresionantes y debería comenzar a usarlos ya!) (Blog publicado por Troy Hunt.)
* [Características de WebJobs de Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [¿Qué es el SDK de WebJobs](websites-dotnet-webjobs-sdk.md)
* [Orientación sobre trabajos en segundo plano: Microsoft Patterns and Practices](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Anuncio de la versión RTM 1.0.0 del SDK de WebJobs de Microsoft Azure](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Introducción al SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Uso del almacenamiento de colas de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Uso del almacenamiento de tablas de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Uso del Bus de servicio de Azure con el SDK de trabajos web](websites-dotnet-webjobs-sdk-service-bus.md)
* [Referencia rápida del SDK de WebJobs de Azure (descarga de PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Vídeos
	* [WebJobs y SDK de WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Serie de vídeos de WebJobs de Azure en Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Presentación del conjunto de herramientas de WebJobs de Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Herramientas de WebJobs y depuración remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Consulte también las siguientes secciones en [Implementación de WebJobs](#deploy) y [Prueba y depuración de WebJobs](#debug).

##<a name="deploy"></a>Implementación de WebJobs

* [Cómo implementar trabajos web de Azure con Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Implementación de WebJobs mediante el Portal de administración de Azure](web-sites-create-web-jobs.md)
* [Habilitación de la línea de comandos o entrega continua de WebJobs de Azure](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Implementación de Git de una aplicación de consola .NET en Azure usando WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Deploying an F# WebJob to Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx) (Implementación de un WebJob de F# en Azure)
* Vídeos
	* [Presentación del conjunto de herramientas de WebJobs de Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Herramientas de WebJobs y depuración remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Programación de WebJobs

* [Cuadro de diálogo Agregar WebJob de Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Creación de un WebJob programado en el Portal de administración de Azure](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>Prueba y depuración de WebJobs

* [Nuevas características para desarrolladores y depuración de WebJobs de Azure en Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Visualización del panel de WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Cómo escribir registros mediante el SDK de WebJobs y verlos en el panel](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs de depuración remota](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [¿Quién escribió ese blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Código interactivo de hospedaje en la nube](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Obtención de un panel de información para el desarrollo local con el SDK de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Incorporación de seguimiento a Sitios web de Azure y WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Vídeos
	* [Herramientas de WebJobs y depuración remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Escalado de WebJobs

* [Escalado de su aplicación web con Sitios web de Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Arquitectura de aplicaciones web de gran escala listas para los negocios](https://channel9.msdn.com/Events/Build/2014/3-626). Trata el escalado de aplicaciones web con WebJobs, incluido el SDK de WebJobs.
* Vídeos
	* [Escalado horizontal de WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Recursos adicionales de WebJobs

* [Entrada de blog sobre disponibilidad general de WebJobs de Azure por Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Documentación de configuración de WebJobs en GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Ejecución de WebJobs de PowerShell en Sitios web de Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Recepción de notificación cuando se completen los WebJobs desencadenados en Azure](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Directiva sencilla de retención de copia de seguridad de sitio web con WebJobs](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Sitios web y Servicios en la nube de Microsoft Azure lentos en la primera solicitud](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Muestra cómo utilizar WebJobs para simular la característica AlwaysOn que solo está disponible para el nivel Estándar de precios.
* [Cierre estable de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Para obtener información sobre el cierre estable de SDK de WebJobs, consulte [Cierre estable](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).
* [Automatización de las copias de seguridad con Azure WebJobs y AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vídeos
	* [Vídeos de WebJobs de Azure por Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Serie de vídeos de WebJobs de Azure en Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Recursos adicionales de SDK de WebJobs

* [Código fuente del SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk)
* [¿Cómo funciona [BlobTrigger]?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Enlaces avanzados con el SDK de WebJobs de Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [WebJob para cargar archivos FREB al Almacenamiento de Azure con el SDK de WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hospedaje de WebJobs de Azure fuera de Azure, con las ventajas del registro desde un WebJob hospedado de Azure](http://bypassion.dk/?p=510)
* [Creación de una herramienta de importación de datos con WebJobs de Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Vídeos
	* [Serie de vídeos de WebJobs de Azure en Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Aplicaciones de WebJob de ejemplo

* [Aplicaciones de ejemplo proporcionadas por el equipo de WebJobs en GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Sitio web sencillo de Azure con back-end de WebJobs utilizando el SDK de WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Muestra el uso de WebJobs programados y controlados por eventos. Consulte la entrada de blog [Nueva compilación de SiteMonitR con el SDK de WebJobs de Azure](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog sobre Azure ](/blog)
* [Blog de Amit Apple](http://blog.amitapple.com/) Se centra en WebJobs (no el SDK).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Ayuda de WebJobs

* [StackOverflow para WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para el SDK de WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Foro de Azure y ASP.NET](http://forums.asp.net/1247.aspx)
* [Foro de aplicaciones web del Servicio de aplicaciones de Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Sitio para el usuario de Aplicaciones web de Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Utilice el hashtag #AzureWebJobs.
* [Report a WebJobs bug or issue](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues) (Notificar un problema o error de WebJobs)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->