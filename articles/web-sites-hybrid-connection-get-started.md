<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# Conexión de un stio web de Azure a un recurso local mediante conexiones híbridas

Un sitio web de Microsoft Azure se puede conectar a cualquier recurso local que utilice un puerto TCP estático, como SQL Server, MySQL, API Web HTTP, Servicio móviles y la mayoría de los servicios web personalizados. En este artículo se muestra cómo crear una conexión híbrida entre un sitio web de Azure y una base de datos de SQL Server local.

> [WACOM.NOTE] La parte de sitios web de la característica Conexiones híbridas solo está disponible en el [Portal de Azure en vista previa][Portal de Azure en vista previa]. Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas][Conexiones híbridas].

## Requisitos previos

-   Una suscripción de Azure. Para obtener una suscripción gratuita, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

-   Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Se recomienda usar una instancia predeterminada en SQL Server dado que utiliza el puerto estático 1433. Para obtener información acerca de la instalación y configuración de SQL Server Express para usarlo con conexiones híbridas, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas][Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas].

-   El equipo en el que instala el agente local de Administrador de conexiones híbridas se describe más adelante en este artículo:

    -   Debe ser capaz de conectarse a Azure a través del puerto 5671
    -   Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local.

> [WACOM.NOTE] En los pasos que se describen en este artículo se supone que usa un explorador desde el equipo que hospedará el agente de conexiones híbridas local.

## En este artículo

[Creación de un sitio web en el Portal de Azure en vista previa][Creación de un sitio web en el Portal de Azure en vista previa]

[Creación de una conexión híbrida y un servicio de BizTalk][Creación de una conexión híbrida y un servicio de BizTalk]

[Instalación del Administrador de conexiones híbridas local para completar la conexión][Instalación del Administrador de conexiones híbridas local para completar la conexión]

[Pasos siguientes][Pasos siguientes]

## Creación de un sitio web en el Portal de Azure en vista previa

> [WACOM.NOTE] Si ya ha creado un sitio web en el Portal de Azure en vista previa que quiere usar en este tutorial, puede omitir este paso e ir directamente a [Creación de una conexión híbrida y un servicio de BizTalk][Creación de una conexión híbrida y un servicio de BizTalk] y comenzar desde aquí.

1.  En la esquina inferior izquierda del [Portal de Azure en vista previa][Portal de Azure en vista previa], haga clic en **Nuevo** y elija **Sitio web**.

    ![New button][New button]

    ![New website][New website]

2.  En el cuadro **Sitio web**, proporcione un nombre para el sitio web y haga clic en **Crear**.

    ![Website name][Website name]

3.  Transcurridos unos segundos, el sitio web se creará y aparecerá su cuadro de sitio web. El cuadro es un panel que se desplaza en vertical y que le permite administrar su sitio.

    ![Website running][Website running]

4.  Para comprobar que el sitio está activo, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.

    ![Click browse to see your website][Click browse to see your website]

    ![Default website page][Default website page]

A continuación, creará una conexión híbrida y un servicio de BizTalk para el sitio web.

<a name="CreateHC"></a>

## Creación de una conexión híbrida y un servicio de BizTalk

1.  De vuelta en el portal en vista previa, desplácese al cuadro de su sitio web y elija **Conexiones híbridas**.

    ![Hybrid connections][Hybrid connections]

2.  En el cuadro de conexiones híbridas, haga clic en **Agregar**.

    ![Add a hybrid connnection][Add a hybrid connnection]

3.  Se abre el cuadro **Agregar una conexión híbrida**. Dado que esta es su primera conexión híbrida, la opción **Nueva conexión híbrida** aparece preseleccionada y se abre el cuadro **Crear conexión híbrida**.

    ![Create a hybrid connection][Create a hybrid connection]

    En el cuadro **Crear conexión híbrida**, haga lo siguiente:

    -   En **Nombre**, escriba un nombre para la conexión.
    -   En **Nombre de host**, escriba el nombre del equipo local que hospeda su recurso.
    -   En **Puerto**, escriba el número de puerto que usa su recurso local (1433 para una instancia predeterminada de SQL Server).
    -   Haga clic en **Servicio de Biz Talk**.

4.  Se abre el cuadro **Crear servicio de Biz Talk**. Escriba un nombre para el servicio de BizTalk y luego haga clic en **Aceptar**.

    ![Create BizTalk service][Create BizTalk service]

    El cuadro **Crear servicio de Biz Talk** se cierra y vuelve al cuadro **Crear conexión híbrida**.

5.  En el cuadro Crear conexión híbrida, haga clic en **Aceptar**.

    ![Click OK][Click OK]

6.  Al finalizar el proceso, el área Notificaciones del portal le informa de que la conexión se ha creado correctamente.

    ![Success notification][Success notification]

7.  En el cuadro de sitio web, el icono de **Conexiones híbridas** ahora muestra que se ha creado 1 conexión híbrida.

    ![One hybrid connection created][One hybrid connection created]

Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>

## Instalación del Administrador de conexiones híbridas local para completar la conexión

1.  En el cuadro de sitio web, haga clic en el icono de Conexiones híbridas.

    ![Hybrid connections icon][Hybrid connections icon]

2.  En el cuadro **Conexiones híbridas**, la columna **Estado** del extremo agregado recientemente muestra **No conectado**. Haga clic en la conexión para configurarla.

    ![Not connected][Not connected]

    Se abre el cuadro de la conexión híbrida.

    ![NotConnectedBlade][NotConnectedBlade]

3.  En el cuadro, haga clic en **Configuración del proceso de escucha**.

    ![Click Listener Setup][Click Listener Setup]

4.  Se abre el cuadro **Propiedades de conexión híbrida**. En **Administrador de conexiones híbridas local**, elija **Haga clic aquí para instalar**.

    ![Click here to install][Click here to install]

5.  En el cuadro de diálogo de advertencia de seguridad Ejecución de la aplicación, elija **Ejecutar** para continuar.

    ![Choose Run to continue][Choose Run to continue]

6.  En el cuadro de diálogo **Control de cuentas de usuario**, elija **Sí**.

    ![Choose Yes][Choose Yes]

7.  El Administrador de conexiones híbridas se descarga y se instala.

    ![Instalación][Instalación]

8.  Cuando finalice la instalación, haga clic en **Cerrar**.

    ![Click Close][Click Close]

    En el cuadro **Conexiones híbridas**, la columna **Estado** muestra ahora **Conectado**.

    ![Connected Status][Connected Status]

Ahora que la infraestructura de conexión híbrida se ha completado, puede crear una aplicación híbrida que la utilice.

<a name="NextSteps"></a>

## Pasos siguientes

-   Para obtener información sobre cómo crear una aplicación web de ASP.NET que use una conexión híbrida, consulte [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas][Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas].

-   Para obtener información sobre el uso de una conexión híbrida con un servicio móvil, consulte [Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante conexiones híbridas][Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante conexiones híbridas].

### Recursos adicionales

[Introducción a las conexiones híbridas][Conexiones híbridas]

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)][Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)]

[Sitio web de conexiones híbridas][Sitio web de conexiones híbridas]

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas][Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas]

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)][Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)]

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)][Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)]

<!-- IMAGES -->

  [Portal de Azure en vista previa]: https://portal.azure.com
  [Conexiones híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Evaluación gratuita de Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [Conexión a un servidor SQL Server local desde un sitio web de Azure mediante conexiones híbridas]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Creación de un sitio web en el Portal de Azure en vista previa]: #CreateSite
  [Creación de una conexión híbrida y un servicio de BizTalk]: #CreateHC
  [Instalación del Administrador de conexiones híbridas local para completar la conexión]: #InstallHCM
  [Pasos siguientes]: #NextSteps
  [New button]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [Instalación]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante conexiones híbridas]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Sitio web de conexiones híbridas]: http://azure.microsoft.com/es-es/services/biztalk-services/
  [Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas]: http://azure.microsoft.com/es-es/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
