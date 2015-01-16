<properties title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Conexión híbrida: Conexión de un sitio web de Azure a un recurso local" description="Crear una conexión entre un sitio web de Azure y un recurso local que usa un puerto TCP estático" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Conexión de un stio web de Azure a un recurso local mediante conexiones híbridas

Un sitio web de Microsoft Azure se puede conectar a cualquier recurso local que utilice un puerto TCP estático, como SQL Server, MySQL, API Web HTTP, Servicio móviles y la mayoría de los servicios web personalizados. En este artículo se muestra cómo crear una conexión híbrida entre un sitio web de Azure y una base de datos de SQL Server local.

> [WACOM.NOTE] La parte de sitios web de la característica Conexiones híbridas solo está disponible en el [Portal de vista previa de Azure](https://portal.azure.com). Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Requisitos previos
- Una suscripción de Azure. Para obtener una suscripción gratuita, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/en-us/pricing/free-trial/). 

- Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Se recomienda usar una instancia predeterminada en SQL Server porque usa el puerto estático 1433. Para obtener información sobre cómo crear una aplicación web de ASP.NET que use una conexión híbrida, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- El equipo en el que instala el agente local de Administrador de conexiones híbridas se describe más adelante en este artículo:

	- Debe ser capaz de conectarse a Azure a través del puerto 5671
	- Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local. 

> [WACOM.NOTE] En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.


##En este artículo##


[Creación de un sitio web en el Portal de Azure en vista previa](#CreateSite)

[Creación de una conexión híbrida y un servicio de BizTalk](#CreateHC)

[Instalación del Administrador de conexiones híbridas local para completar la conexión](#InstallHCM)

[Pasos siguientes](#NextSteps)


## Creación de un sitio web en el Portal de Azure en vista previa ##

> [WACOM.NOTE] Si ya ha creado un sitio web en el Portal de Azure en vista previa que quiere usar en este tutorial, puede omitir este paso e ir directamente a [Creación de una conexión híbrida y un servicio de BizTalk](#CreateHC) y comenzar desde ahí.

1. En la esquina inferior izquierda del [Portal de vista previa de Azure](https://portal.azure.com), haga clic en **Nuevo** y, a continuación, seleccione **Sitio web**.
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. En el cuadro **Sitio web**, proporcione un nombre para el sitio web y haga clic en **Crear**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Transcurridos unos segundos, el sitio web se creará y aparecerá su cuadro de sitio web. El cuadro es un panel que se desplaza en vertical y que le permite administrar su sitio.
	
	![Website running][WebSiteRunningBlade]
	
4. Para comprobar que el sitio está activo, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
A continuación, creará una conexión híbrida y un servicio de BizTalk para el sitio web.

<a name="CreateHC"></a>
## Creación de una conexión híbrida y un servicio de BizTalk ##

1. De vuelta en el portal de vista previa, desplácese al cuadro de su sitio web y elija **Conexiones híbridas**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. En el cuadro de conexiones híbridas, haga clic en **Agregar**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Se abre el cuadro **Agregar una conexión híbrida**.  Dado que esta es su primera conexión híbrida, la opción **Nueva conexión híbrida** aparece preseleccionada y se abre el cuadro **Crear conexión híbrida**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	En el cuadro **Crear conexión híbrida**, haga lo siguiente:
	- En **Nombre**, escriba un nombre para la conexión.
	- En **Nombre de host**, escriba el nombre del equipo local que hospeda su recurso.
	- En el **Puerto**, escriba el número de puerto que usa su recurso local (1433 para una instancia predeterminada de SQL Server).
	- Haga clic en **Servicio de BizTalk**


4. Se abre el cuadro **Crear servicio de BizTalk**. Escriba un nombre para el servicio de BizTalk y luego haga clic en **Aceptar**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	El cuadro **Crear servicio de BizTalk** se cierra y vuelve al cuadro **Crear conexión híbrida**.
	
5. En el cuadro Crear conexión híbrida, haga clic en **Aceptar**. 
	
	![Click OK][CreateBTScomplete]
	
6. Al finalizar el proceso, el área Notificaciones del portal le informa de que la conexión se ha creado correctamente.
	
	![Success notification][CreateHCSuccessNotification]
	
7. En el cuadro de sitio web, el icono de **Conexiones híbridas** ahora muestra que se ha creado 1 conexión híbrida.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>
## Instalación del Administrador de conexiones híbridas local para completar la conexión ##

1. En el cuadro de sitio web, haga clic en el icono de Conexiones híbridas. 
	
	![Hybrid connections icon][HCIcon]
	
2. En el cuadro **Conexiones híbridas**, la columna **Estado** del extremo agregado recientemente muestra **No conectado**. Haga clic en la conexión para configurarla.
	
	![Not connected][NotConnected]
	
	Se abre el cuadro de la conexión híbrida.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. En el cuadro, haga clic en **Configuración del proceso de escucha**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Se abre el cuadro **Propiedades de conexión híbrida**. En **Administrador de conexiones híbridas local**, elija **Haga clic aquí para instalar**.
	
	![Click here to install][ClickToInstallHCM]
	
5. En el cuadro de diálogo de advertencia de seguridad Ejecución de la aplicación, elija **Ejecutar** para continuar.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	En el cuadro de diálogo **Control de cuentas de usuario**, elija **Sí**.
	
	![Choose Yes][UAC]
	
7. El Administrador de conexiones híbridas se descarga y se instala. 
	
	![Installing][HCMInstalling]
	
8. Cuando finalice la instalación, haga clic en **Cerrar**.
	
	![Click Close][HCMInstallComplete]
	
	En el cuadro **Conexiones híbridas**, la columna **Estado** muestra ahora **Conectado**. 
	
	![Connected Status][HCStatusConnected]

Ahora que la infraestructura de conexión híbrida se ha completado, puede crear una aplicación híbrida que la utilice. 

<a name="NextSteps"></a>
## Pasos siguientes ##

- Para obtener información sobre cómo crear una aplicación web de ASP.NET que use una conexión híbrida, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).

- Para obtener información sobre el uso de una conexión híbrida con un servicio móvil, consulte [Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante conexiones híbridas](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/).

###Recursos adicionales

[Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sitio web de conexiones híbridas](http://azure.microsoft.com/en-us/services/biztalk-services/)

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

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

<!--HONumber=35.1-->
