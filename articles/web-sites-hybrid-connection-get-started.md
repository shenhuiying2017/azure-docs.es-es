<properties 
	pageTitle="Acceso a recursos locales mediante conexiones híbridas en el Servicio de aplicaciones de Azure" 
	description="Creación de una conexión entre una aplicación web en Servicio de aplicaciones de Azure y un recurso local que usa un puerto TCP estático" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Acceso a recursos locales mediante conexiones híbridas en el Servicio de aplicaciones de Azure

Se puede conectar una aplicación web de Servicio de aplicaciones de Azure a cualquier recurso local que utilice un puerto TCP estático, como SQL Server, MySQL, API Web HTTP, Servicios móviles y la mayoría de los servicios web personalizados. En este artículo se muestra cómo crear una conexión híbrida entre una aplicación web del Servicio de aplicaciones y una base de datos de SQL Server local.

> [AZURE.NOTE] La parte Aplicaciones web de la característica Conexiones híbridas solo está disponible en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## Requisitos previos
- Una suscripción de Azure. Para obtener una suscripción gratuita, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Se recomienda usar una instancia predeterminada en SQL Server porque usa el puerto estático 1433. Para obtener información sobre cómo instalar y configurar SQL Server Express para que use una conexión híbrida, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas](http://go.microsoft.com/fwlink/?LinkID=397979)

- El equipo en el que instala el agente local de Administrador de conexiones híbridas se describe más adelante en este artículo:

	- Debe ser capaz de conectarse a Azure a través del puerto 5671
	- Debe poder establecer comunicación con el  *hostname*:*portnumber* de su recurso local. 

> [AZURE.NOTE] En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.


## Creación de una aplicación web en el Portal de Azure ##

> [AZURE.NOTE] Si ya ha creado una aplicación web en el Portal de Azure que desea usar en este tutorial, puede omitir este paso e ir directamente a [Creación de una conexión híbrida y un servicio de BizTalk](#CreateHC) y comenzar desde ahí.

1. En la esquina inferior izquierda del [Portal de Azure](https://portal.azure.com), haga clic en **Nuevo** > **Web + Móvil** > **Sitio web**.
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. En la hoja **Aplicación web**, indique una dirección URL > **Crear**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Transcurridos unos segundos, la aplicación web se creará y aparecerá su hoja de aplicación web. El cuadro es un panel que se desplaza en vertical y que le permite administrar su sitio.
	
	![Website running][WebSiteRunningBlade]
	
4. Para comprobar que el sitio está activo, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
A continuación, creará una conexión híbrida y un servicio de BizTalk para la aplicación web.

<a name="CreateHC"></a>
## Creación de una conexión híbrida y un servicio de BizTalk ##

1. Desplácese hacia abajo por la hoja de la aplicación web y elija **Conexiones híbridas**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. En la hoja Conexiones híbridas, haga clic en **Agregar**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Se abre la hoja **Agregar una conexión híbrida**.  Dado que esta es su primera conexión híbrida, la opción **Nueva conexión híbrida** aparece preseleccionada y se abre la hoja **Crear conexión híbrida**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	En la hoja **Crear conexión híbrida**, haga lo siguiente:
	- En **Nombre**, escriba un nombre para la conexión.
	- En **Nombre de host**, escriba el nombre del equipo local que hospeda su recurso.
	- En **Puerto**, escriba el número de puerto que usa su recurso local (1433 para una instancia predeterminada de SQL Server).
	- Haga clic en **Servicio de Biz Talk**.


4. Se abre la hoja **Crear servicio de BizTalk**. Escriba un nombre para el servicio de BizTalk y luego haga clic en **Aceptar**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	La hoja **Crear servicio de BizTalk** se cierra y vuelve a la hoja **Crear conexión híbrida**.
	
5. En el cuadro Crear conexión híbrida, haga clic en **Aceptar**. 
	
	![Click OK][CreateBTScomplete]
	
6. Al finalizar el proceso, el área de notificaciones del portal le informa de que la conexión se ha creado correctamente.
	<!-- TODO

    Todo falla en este paso. No se puede crear un servicio de BizTalk en el portal con software propio. Me cambio al portal anterior
	(portal completo) y crearé el servicio de BizTalk pero no parece que deje conectar. Cuando finalice el paso
	Crear conexión híbrida, obtendrá el siguiente error
	No se pudo crear la conexión híbrida RelecIoudHC. No se pudo encontrar 
	el tipo de recurso en el espacio de nombres 
	'Microsoft.BizTaIkServices for api version 2014-06-01'.
	
	El error indica que no pudo encontrar el tipo, no la instancia.
	![Success notification][CreateHCSuccessNotification]
	-->
7. En la hoja de la aplicación web, el icono **Conexiones híbridas** ahora muestra que se ha creado 1 conexión híbrida.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>
## Instalación del Administrador de conexiones híbridas local para completar la conexión ##

1. En la hoja de aplicación web, haga clic en el icono Conexiones híbridas. 
	
	![Hybrid connections icon][HCIcon]
	
2. En la hoja **Conexiones híbridas**, la columna **Estado** del extremo agregado recientemente muestra **No conectado**. Haga clic en la conexión para configurarla.
	
	![Not connected][NotConnected]
	
	Se abre el cuadro de la conexión híbrida.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. En la hoja, haga clic en **Configuración del proceso de escucha**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Se abre la hoja **Propiedades de conexión híbrida**. En **Administrador de conexiones híbridas local**, elija **Haga clic aquí para instalar**.
	
	![Click here to install][ClickToInstallHCM]
	
5. En el cuadro de diálogo de advertencia de seguridad Ejecución de la aplicación, elija **Ejecutar** para continuar.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	En el cuadro de diálogo **Control de cuentas de usuario**, elija **Sí**.
	
	![Choose Yes][UAC]
	
7. El Administrador de conexiones híbridas se descarga y se instala. 
	
	![Installing][HCMInstalling]
	
8. Cuando finalice la instalación, haga clic en **Cerrar**.
	
	![Click Close][HCMInstallComplete]
	
	En la hoja **Conexiones híbridas**, la columna **Estado** muestra ahora **Conectado**. 
	
	![Connected Status][HCStatusConnected]

Ahora que la infraestructura de conexión híbrida se ha completado, puede crear una aplicación híbrida que la utilice. 

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<a name="NextSteps"></a>
## Pasos siguientes ##

- Para obtener información sobre cómo crear una aplicación web de ASP.NET que use una conexión híbrida, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- Para obtener información sobre el uso de una conexión híbrida con un servicio móvil, consulte [Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante conexiones híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Recursos adicionales

[Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sitio web de conexiones híbridas](http://azure.microsoft.com/services/biztalk-services/)

[Servicios de BizTalk pestañas Panel, Supervisar, Escala, Configurar y Conexiones híbridas](../biztalk-dashboard-monitor-scale-tabs/)

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web al Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal antiguo al nuevo, consulte: [Referencia sobre sitios web y aplicaciones web en el Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=49-->