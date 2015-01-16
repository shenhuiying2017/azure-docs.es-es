<properties title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Conexiones híbridas paso a paso: Conexión a SQL Server local desde un sitio web de Azure" description="Crear un sitio web de Microsoft Azure y conectarlo a una base de datos de SQL Server local" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Conexión a un servidor SQL Server local desde un sitio web de Azure usando Conexiones híbridas

##Introducción##
Conexiones híbridas puede conectar sitios web de Microsoft Azure con recursos locales que usan un puerto TCP estático. Los recursos admitidos incluyen Microsoft SQL Server, MySQL, API web HTTP, Servicios móviles y la mayoría de servicios web personalizados. 

En este tutorial aprenderemos a crear un sitio web en el portal de vista previa de Azure, a conectar el sitio web a una base de datos de SQL Server local usando la nueva característica Conexiones híbridas, a crear una aplicación web ASP.NET simple que usará la conexión híbrida y a implementar la aplicación en el sitio web de Azure. El sitio web completado en Azure almacena credenciales de usuario en una base de datos de miembros de pertenencia local. En el tutorial se asume que no tiene ninguna experiencia anterior con Azure o ASP.NET.

> [WACOM.NOTE] La parte de sitios web de la característica Conexiones híbridas solo está disponible en el [Portal de vista previa de Azure](https://portal.azure.com). Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Requisitos previos##
Para completar este tutorial, necesitará los siguientes productos. Todos están disponibles en versión gratuita, así que puede comenzar a desarrollar para Azure completamente gratis.

- **Suscripción de Azure** - Para obtener información sobre una suscripción gratuita, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/en-us/pricing/free-trial/). 

- **Visual Studio 2013** - Para descargar una versión de evaluación gratuita de Visual Studio 2013, consulte [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale esta aplicación antes de continuar.

- **Microsoft .NET Framework 3.5 Service Pack 1** - Si su sistema operativo es Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 o Windows Server 2008 R2, puede habilitar este producto en Panel de control > Programas y características > Activar o desactivar las características de Windows. De lo contrario, puede descargarlo desde el [Centro de descarga de Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014 Express with Tools** - Descargue Microsoft SQL Server Express de forma gratuita en la [página de bases de datos de Plataforma web de Microsoft](http://www.microsoft.com/web/platform/database.aspx). Elija la versión **Express** (no LocalDB). La versión **Express with Tools** incluye SQL Server Management Studio, que usará en este tutorial.

- **SQL Server Management Studio Express** - Este producto se incluye en la descarga de SQL Server 2014 Express with Tools mencionada anteriormente, pero si lo instala por separado, puede descargarlo e instalarlo desde la [página de descarga de SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

En este tutorial se supone que tiene una suscripción de Azure, que ha instalado Visual Studio 2013 y que ha instalado o habilitado .NET Framework 3.5. En el tutorial se muestra cómo instalar SQL Server 2014 Express en una configuración que funcione bien con la característica Conexiones híbridas de Azure (una instancia predeterminada con un puerto TCP estático). Antes de iniciar el tutorial, descargue SQL Server 2014 Express with Tools desde la ubicación mencionada anteriormente sino tiene SQL Server instalado.

### Notas ###
Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Las instancias predeterminadas de SQL Server usan el puerto estático 1433, mientras que las instancias con nombre no. 

El equipo en el que instala el administrador de conexiones híbridas local:

- Debe tener conectividad de salida a Azure a través de:

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Porqué</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Requerido</strong> para el puerto HTTP para la validación de certificados y, de forma opcional, para la conectividad de datos.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Opcional</strong> para conectividad de datos. Si la conectividad de salida para 443 no está disponible, se usa el puerto TCP 80.</td>
    </tr>
	<tr>
        <td>5671 y 9352</td>
        <td><strong>Recomendado</strong> pero opcional para la conectividad de datos. Tenga en cuenta que este modo obtiene mayor rendimiento. Si la conectividad de salida para estos puertos no está disponible, se usa el puerto TCP 443.</td>
	</tr>
</table>

- Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local. 

En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.

Si ya tiene SQL Server instalado en una configuración y en un entorno que cumple las condiciones descritas anteriormente, puede continuar y empezar con [Creación de una base de datos de SQL Server local](#CreateSQLDB).

##En este artículo##
[A. Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos de SQL Server local](#InstallSQL)

[B. Creación de un sitio web en el portal de vista previa de Azure](#CreateSite)

[C. Creación de una conexión híbrida en un servicio de BizTalk](#CreateHC)

[D. Instalación del administrador de conexiones híbridas local para completar la conexión](#InstallHCM)

[E. Creación de un proyecto ASP.NET básico, edición de la cadena de conexión de base de datos y ejecución del proyecto localmente](#CreateASPNET)

[F. Publicación de la aplicación web en Azure y prueba de la misma](#PubNTest)

<a name="InstallSQL"></a>
## A. Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos de SQL Server local ##

En esta sección se muestra cómo instalar SQL Server Express, habilitar TCP/IP y crear una base de datos de forma que la aplicación web funcione con el entorno de vista previa de Azure.

###Instalación de SQL Server Express###

1. Para instalar SQL Server Express, ejecute el archivo **SQLEXPRWT_x64_ENU.exe** o **SQLEXPR_x86_ENU.exe** que descargó. Aparecerá el asistente Centro de instalación de SQL Server.
	
	![SQL Server Install][SQLServerInstall]
	
2. Elija **Nueva instalación independiente de SQL Server o agregar características a una instalación existente.**. Siga las instrucciones, aceptando las elecciones y configuraciones predeterminadas, hasta llegar a la página **Configuración de instancia**.
	
3. En la página **Configuración de instancia**, elija **Instancia predeterminada**.
	
	![Choose Default Instance][ChooseDefaultInstance]
	
	De forma predeterminada, la instancia predeterminada de SQL Server escucha solicitudes de clientes de SQL Server en el puerto estático 1433, que es el que requiere la característica Conexiones híbridas. Las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite.
	
4. Acepte las configuraciones predeterminadas de la página**Configuración del servidor**.
	
5. En la página **Configuración del Motor de base de datos**, bajo **Modo de autenticación**, elija **Modo mixto (autenticación de SQL Server y de Windows)** y proporcione una contraseña.
	
	![Choose Mixed Mode][ChooseMixedMode]
	
	En este tutorial se usará la autenticación de SQL Server. Asegúrese de recordar la contraseña proporcionada, ya que la necesitará más tarde.
	
6. Recorra el resto del asistente para completar la instalación.

###Habilitar TCP/IP###
Para habilitar TCP/IP, usará el Administrador de configuración de SQL Server, que se instaló al instalar SQL Server Express. Siga los pasos que figuran en [Habilitar el protocolo de red TCP/IP para SQL Server](http://technet.microsoft.com/en-us/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
###Creación de una base de datos de SQL Server local###

La aplicación web de Visual Studio requiere una base de datos de pertenencia a la que pueda acceder Azure. Para ello, se necesita una base de datos de SQL Server o SQL Server Express (no la base de datos LocalDB que usa la plantilla MVC de forma predeterminada), por lo que seguidamente creará la base de datos de pertenencia. 

1. En SQL Server Management Studio, conéctese al servidor SQL Server que acaba de instalar. (Si el cuadro de diálogo **Conectar al servidor** no se abre automáticamente, vaya al **Explorador de objetos** en el panel izquierdo, haga clic en **Conectar** y, a continuación, haga clic en **Motor de la base de datos**). 	
	![Connect to Server][SSMSConnectToServer]
	
	En **Tipo de servidor**, elija **Motor de base de datos**. Para **Server name**, puede usar **localhost** o el nombre del equipo que esté usando. Elija **Autenticación de SQL Server** y después inicie sesión con el nombre de usuario sa y la contraseña que creó anteriormente. 
	
2. Para crear una nueva base de datos usando SQL Server Management Studio, haga clic con el botón secundario en **Bases de datos** en el Explorador de objetos y, a continuación, haga clic en **Nueva base de datos**.
	
	![Create new database][SSMScreateNewDB]
	
3. En el cuadro de diálogo **Nueva base de datos**, escriba MembershipDB para el nombre de base de datos y después haga clic en **Aceptar**. 
	
	![Provide database name][SSMSprovideDBname]
	
	Tenga en cuenta que, llegados a este punto, no realiza ningún cambio en la base de datos. La aplicación web agregará más tarde la información de pertenencia automáticamente cuando se ejecute.
	
4. En el Explorador de objetos, si expande **Bases de datos**, verá que se ha creado la base de datos de pertenencia.
	
	![MembershipDB created][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## B. Creación de un sitio web en el portal de vista previa de Azure ##

> [WACOM.NOTE] Si ya ha creado un sitio web en el Portal de Azure en vista previa que quiere usar en este tutorial, puede omitir este paso e ir directamente a [Creación de una conexión híbrida y un servicio de BizTalk](#CreateHC) y continuar desde ahí.

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
## C. Creación de una conexión híbrida en un servicio de BizTalk ##

1. De vuelta en el portal de vista previa, desplácese al cuadro de su sitio web y elija **Conexiones híbridas**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. En el cuadro de conexiones híbridas, haga clic en **Agregar**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Se abre el cuadro **Agregar una conexión híbrida**.  Dado que esta es su primera conexión híbrida, la opción **Nueva conexión híbrida** aparece preseleccionada y se abre el cuadro **Crear conexión híbrida**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	En el cuadro **Crear conexión híbrida**, haga lo siguiente:
	- En **Nombre**, escriba un nombre para la conexión.
	- En **Nombre de host**, escriba el nombre del equipo del equipo host SQL Server.
	- En **Puerto**, escriba 1433 (puerto predeterminado para SQL Server).
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
## D. Instalación del administrador de conexiones híbridas local para completar la conexión ##

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

Ahora que la infraestructura de la conexión híbrida se ha completado, creará una aplicación web que la use.

<a name="CreateASPNET"></a>
## E. Creación de un proyecto ASP.NET básico, edición de la cadena de conexión de base de datos y ejecución del proyecto localmente ##

###Creación de un proyecto ASP.NET básico ###
1. En Visual Studio, en el menú **Archivo**, cree un proyecto nuevo:
	
	![New Visual Studio project][HCVSNewProject]
	
2. En la sección **Plantillas** del cuadro de diálogo **Nuevo proyecto**, seleccione **Web** y elija **Aplicación web de ASP.NET** y, a continuación, haga clic en **Aceptar**.
	
	![Choose ASP.NET Web Application][HCVSChooseASPNET]
	
3. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, elija **MVC** y, a continuación, haga clic en **Aceptar**.
	
	![Choose MVC][HCVSChooseMVC]
	
4. Cuando el proyecto se haya creado, aparecerá la página Léame de la aplicación. No ejecute el proyecto web todavía.
	
	![Readme page][HCVSReadmePage]

###Edición de la cadena de conexión de la base de datos para la aplicación###

En este paso editará la cadena de conexión que indica a la aplicación dónde buscar la base de datos de SQL Server Express local. La cadena de conexión es un archivo Web.config de la aplicación que contiene información de configuración para dicha aplicación. 

> [WACOM.NOTE] Para garantizar que la aplicación usa la base de datos creada en SQL Server Express y no la base de datos LocalDB predeterminada de Visual Studio, es importante que complete este paso antes de ejecutar el proyecto.

1. En el Explorador de soluciones, haga doble clic en el archivo Web.config.
	
	![Web.config][HCVSChooseWebConfig]
	
2. Edite la sección **connectionStrings** para apuntar a la base de datos de SQL Server en la máquina local, siguiendo la sintaxis del ejemplo que se muestra continuación: 
	
	![Connection string][HCVSConnectionString]
	
	Cuando escriba la cadena de conexión, tenga en cuenta lo siguiente:
	
	- Si se está conectando a una instancia con nombre en lugar de a una instancia predeterminada (por ejemplo SuServidor\SQLEXPRESS), debe configurar su servidor SQL Server para usar puertos estáticos. Para obtener información sobre la configuración de puertos estáticos, consulte [Cómo configurar SQL Server para que escuche en un puerto específico](http://support.microsoft.com/kb/823938). De forma predeterminada, las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite. 
	
	- Es recomendable que especifique el puerto (1433 de forma predeterminada, como se muestra en el ejemplo) en la cadena de conexión de forma que pueda asegurarse de que su servidor SQL Server local tiene la funcionalidad TCP habilitada y usa el puerto correcto. 
	
	- Recuerde usar Autenticación de SQL Server para conectarse, especificando el identificador y la contraseña del usuario en la cadena de conexión.
	
3. Haga clic en **Guardar** en Visual Studio para guardar el archivo Web.config.

###Ejecución del proyecto localmente y registro de un nuevo usuario ###

1. Ahora, ejecute el nuevo proyecto web localmente haciendo clic en el botón Examinar que se encuentra debajo de Depurar. En este ejemplo se usa Internet Explorer.
	
	![Run project][HCVSRunProject]
	
2. En la parte superior derecha de la página web predeterminada, elija **Registrar** para registrar una nueva cuenta:
	
	![Register a new account][HCVSRegisterLocally]
	
3. Escriba un nombre de usuario y una contraseña:
	
	![Enter user name and password][HCVSCreateNewAccount]
	
	Esta operación creará automáticamente una base de datos en su servidor SQL Server local que hospedará la información de pertenencia de la aplicación. Una de las tablas (**dbo.AspNetUsers**) hospeda las credenciales de usuario del sitio web como las que acaba de introducir. Verá esta tabla posteriormente en el tutorial.
	
4. Cierre la ventana del explorador de la página web predeterminada. Esta operación detendrá la aplicación en Visual Studio.

Ahora está preparado para continuar con el paso siguiente, que consiste en publicar la aplicación en Azure y probarla.

<a name="PubNTest"></a>
## F. Publicación de la aplicación web en Azure y prueba de la misma ##

Ahora publicará la aplicación en su sitio web en Azure y después la probará para ver cómo se usa la conexión híbrida que configuró anteriormente para conectar la aplicación del sitio web a la base de datos en la máquina local. 

###Publicación de la aplicación###

1. Puede descargar el perfil de publicación para el sitio web en el Portal de Azure. En el cuadro del sitio web, elija **Descargar perfil de publicación** y guarde el archivo en su equipo.
	
	![Download publish profile][PortalDownloadPublishProfile]
	
	![Publish profile in downloads folder][HCVSPublishProfileInDownloadsFolder]
	
	A continuación, importará este archivo en la aplicación web de Visual Studio. 
	
2. En Visual Studio, haga clic con el botón secundario en el nombre del proyecto en el Explorador de soluciones y seleccione **Publicar**.
	
	![Select publish][HCVSRightClickProjectSelectPublish]
	
3. En el cuadro de diálogo **Publicación web**, en la pestaña **Perfil**, elija **Importar**.
	
	![Import][HCVSPublishWebDialogImport]
	
4. Examine el perfil de publicación descargado, selecciónelo y, a continuación, haga clic en **Aceptar**.
	
	![Browse to profile][HCVSBrowseToImportPubProfile]
	
5. Su información de publicación se importará y mostrará en la pestaña **Conexión** del cuadro de diálogo. 
	
	![Click Publish][HCVSClickPublish]
	
	Haga clic en **Publicar**.
	
	Cuando la publicación se complete, el explorador se iniciará y mostrará su, ahora, sitio web familiar, ¡con la diferencia de que ahora está activo en la nube de Azure!

A continuación, usará la aplicación web activa para ver su conexión híbrida en acción.

###Prueba de la aplicación web completada en Azure ###
1. En la parte superior de la página web en Azure, elija **Iniciar sesión**.
	
	![Test log in][HCTestLogIn]
	
2. El sitio web de Azure ahora estará conectado a la base de datos de pertenencia de la aplicación web en la máquina local. Para comprobarlo, inicie sesión con las mismas credenciales que introdujo en la base de datos local anteriormente.
	
	![Hello greeting][HCTestHelloContoso]
	
3. Para seguir probando su nueva conexión híbrida, cierre la sesión de la aplicación web de Azure y regístrese como otro usuario. Proporcione un nuevo nombre de usuario y contraseña y haga clic en **Registrar**.
	
	![Test register another user][HCTestRegisterRelecloud]
	
4. Para comprobar que las credenciales del nuevo usuario se han almacenado en la base de datos local a través de la conexión híbrida, abra SQL Management Studio en el equipo local. En el Explorador de objetos, expanda la base de datos **MembershipDB** y, a continuación, expanda **Tablas**. Haga clic con el botón secundario en la tabla de pertenencia **dbo.AspNetUsers** y elija **Seleccionar las primeras 1000 filas** para ver los resultados.
	
	![View the results][HCTestSSMSTree]
	
5. La tabla de pertenencia local ahora muestra ambas cuentas, la que creó localmente y la que creó en la nube de Azure. La que creó en la nube se ha guardado en la base de datos local a través de la característica Conexión híbrida de Azure.
	
	![Registered users in on-premises database][HCTestShowMemberDb]
	
Ya ha creado e implementado una aplicación web ASP.NET que usa una conexión híbrida entre un sitio web y la nube de Azure y una base de datos de SQL Server local. ¡Enhorabuena!

##Otras referencias##
[Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sitio web de conexiones híbridas](http://azure.microsoft.com/en-us/services/biztalk-services/)

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas](http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conexión a un SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Introducción a la identidad de ASP.NET](http://www.asp.net/identity)

<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!--HONumber=35.1-->
