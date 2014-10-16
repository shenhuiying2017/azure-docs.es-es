<properties linkid="web-sites-hybrid-connection-getting-started" title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Conexión a un servidor SQL Server local desde un sitio web de Azure usando Conexiones híbridas

## Introducción

Conexiones híbridas puede conectar sitios web de Microsoft Azure con recursos locales que usan un puerto TCP estático. Los recursos admitidos incluyen Microsoft SQL Server, MySQL, API web HTTP, Servicios móviles y la mayoría de servicios web personalizados.

En este tutorial aprenderemos a crear un sitio web en el portal de vista previa de Azure, a conectar el sitio web a una base de datos de SQL Server local usando la nueva característica Conexiones híbridas, a crear una aplicación web ASP.NET simple que usará la conexión híbrida y a implementar la aplicación en el sitio web de Azure. El sitio web completado en Azure almacena credenciales de usuario en una base de datos de miembros de pertenencia local. En el tutorial se asume que no tiene ninguna experiencia anterior con Azure o ASP.NET.

> [WACOM.NOTE] La parte de sitios web de la característica Conexiones híbridas solo está disponible en el [Portal de Azure en vista previa][]. Para crear una conexión en Servicios de BizTalk, consulte [Conexiones híbridas][].

## Requisitos previos

Para completar este tutorial, necesitará los siguientes productos. Todos están disponibles en versión gratuita, así que puede comenzar a desarrollar para Azure completamente gratis.

-   **Suscripción de Azure** - Para obtener información sobre una suscripción gratuita, vea [Evaluación gratuita de Azure][].

-   **Visual Studio 2013**: para descargar una versión de evaluación gratuita de Visual Studio 2013, consulte [Descargas de Visual Studio][]. Instale esta aplicación antes de continuar.

-   **Microsoft .NET Framework 3.5 Service Pack 1** - Si su sistema operativo es Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 o Windows Server 2008 R2, puede habilitar este producto en Panel de control \> Programas y características \> Activar o desactivar las características de Windows. De lo contrario, puede descargarlo desde el [Centro de descarga de Microsoft][].

-   **SQL Server 2014 Express with Tools**: descargue Microsoft SQL Server Express de forma gratuita en la [página de bases de datos de Plataforma web de Microsoft][]. Elija la versión **Express** (no LocalDB). La versión **Express with Tools** incluye SQL Server Management Studio, que usará en este tutorial.

-   **SQL Server Management Studio Express** - Este producto se incluye en la descarga de SQL Server 2014 Express with Tools mencionada anteriormente, pero si lo instala por separado, puede descargarlo e instalarlo desde la [página de descarga de SQL Server Express][página de bases de datos de Plataforma web de Microsoft].

En este tutorial se supone que tiene una suscripción de Azure, que ha instalado Visual Studio 2013 y que ha instalado o habilitado .NET Framework 3.5. En el tutorial se muestra cómo instalar SQL Server 2014 Express en una configuración que funcione bien con la característica Conexiones híbridas de Azure (una instancia predeterminada con un puerto TCP estático). Antes de iniciar el tutorial, descargue SQL Server 2014 Express with Tools desde la ubicación mencionada anteriormente sino tiene SQL Server instalado.

### Notas

Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Las instancias predeterminadas de SQL Server usan el puerto estático 1433, mientras que las instancias con nombre no.

El equipo en el que instala el administrador de conexiones híbridas local:

-   Debe ser capaz de conectarse a Azure a través del puerto 5671
-   Debe poder establecer comunicación con el *nombre de host*:*número de puerto* de su recurso local.

En estos pasos de este artículo se supone que usa el explorador del equipo que hospeda el agente de conexiones híbridas local.

Si ya tiene SQL Server instalado en una configuración y en un entorno que cumple las condiciones descritas anteriormente, puede continuar y empezar con [Creación de una base de datos de SQL Server local][].

## En este artículo

[A. Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos de SQL Server local][]

[B. Creación de un sitio web en el portal de vista previa de Azure][]

[C. Creación de una conexión híbrida en un servicio de BizTalk][]

[D. Instalación del administrador de conexiones híbridas local para completar la conexión][]

[E. Creación de un proyecto ASP.NET básico, edición de la cadena de conexión de base de datos y ejecución del proyecto localmente][]

[F. Publicación de la aplicación web en Azure y prueba de la misma][]

<a name="InstallSQL"></a>

## A. Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos de SQL Server local

En esta sección se muestra cómo instalar SQL Server Express, habilitar TCP/IP y crear una base de datos de forma que la aplicación web funcione con el entorno de vista previa de Azure.

### Instalación de SQL Server Express

1.  Para instalar SQL Server Express, ejecute el archivo **SQLEXPRWT\_x64\_ENU.exe** o **SQLEXPR\_x86\_ENU.exe** que descargó. Aparecerá el asistente Centro de instalación de SQL Server.

    ![SQL Server Install][]

2.  Elija **Nueva instalación independiente de SQL Server o agregar características a una instalación existente**. Siga las instrucciones, aceptando las elecciones y configuraciones predeterminadas, hasta llegar a la página **Configuración de instancia**.

3.  En la página **Configuración de instancia**, elija **Instancia predeterminada**.

    ![Choose Default Instance][]

    De forma predeterminada, la instancia predeterminada de SQL Server escucha solicitudes de clientes de SQL Server en el puerto estático 1433, que es el que requiere la característica Conexiones híbridas. Las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite.

4.  Acepte las configuraciones predeterminadas de la página **Configuración del servidor**.

5.  En la página **Configuración del Motor de base de datos**, bajo **Modo de autenticación**, elija **Modo mixto (autenticación de SQL Server y de Windows)** y proporcione una contraseña.

    ![Choose Mixed Mode][]

    En este tutorial se usará la autenticación de SQL Server. Asegúrese de recordar la contraseña proporcionada, ya que la necesitará más tarde.

6.  Recorra el resto del asistente para completar la instalación.

### Habilitar TCP/IP

Para habilitar TCP/IP, usará el Administrador de configuración de SQL Server, que se instaló al instalar SQL Server Express. Siga los pasos que figuran en [Habilitar el protocolo de red TCP/IP para SQL Server][] antes de continuar.

<a name="CreateSQLDB"></a>

### Creación de una base de datos de SQL Server local

La aplicación web de Visual Studio requiere una base de datos de pertenencia a la que pueda acceder Azure. Para ello, se necesita una base de datos de SQL Server o SQL Server Express (no la base de datos LocalDB que usa la plantilla MVC de forma predeterminada), por lo que seguidamente creará la base de datos de pertenencia.

1.  En SQL Server Management Studio, conéctese al servidor SQL Server que acaba de instalar. (Si el cuadro de diálogo **Conectar con el servidor** no aparece automáticamente, navegue hasta **Explorador de objetos** en el panel izquierdo, haga clic en **Conectar** y después en **Motor de base de datos**.)
    ![Connect to Server][]

    En **Tipo de servidor**, elija **Motor de base de datos**. Para **Nombre de servidor**, puede usar **localhost** o el nombre del equipo que esté usando. Elija **Autenticación de SQL server** y después inicie sesión con el nombre de usuario sa y la contraseña que creó anteriormente.

2.  Para crear una nueva base de datos usando SQL Server Management Studio, haga clic con el botón secundario en **Bases de datos** en el Explorador de objetos y después haga clic en **Nueva base de datos**.

    ![Create new database][]

3.  En el cuadro de diálogo **Nueva base de datos**, escriba MembershipDB para el nombre de base de datos y después haga clic en **Aceptar**.

    ![Provide database name][]

    Tenga en cuenta que, llegados a este punto, no realiza ningún cambio en la base de datos. La aplicación web agregará más tarde la información de pertenencia automáticamente cuando se ejecute.

4.  En el explorador de objetos, si expande **Bases de datos**, verá que se ha creado la base de datos de pertenencia.

    ![MembershipDB created][]

<a name="CreateSite"></a>

## B. Creación de un sitio web en el portal de vista previa de Azure

> [WACOM.NOTE] Si ya ha creado un sitio web en el portal de vista previa de Azure que desea usar para este tutorial, puede seguir con [Creación de una conexión híbrida en un servicio de BizTalk][C. Creación de una conexión híbrida en un servicio de BizTalk] y continuar a partir de ahí.

1.  En la esquina inferior izquierda del [Portal de Azure en vista previa][], haga clic en **Nuevo** y elija **Sitio web**.

    ![New button][]

    ![New website][]

2.  En el cuadro **Sitio web**, proporcione un nombre para el sitio web y haga clic en **Crear**.

    ![Website name][]

3.  Transcurridos unos segundos, el sitio web se creará y aparecerá su cuadro de sitio web. El cuadro es un panel que se desplaza en vertical y que le permite administrar su sitio.

    ![Website running][]

4.  Para comprobar que el sitio está activo, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.

    ![Click browse to see your website][]

    ![Default website page][]

A continuación, creará una conexión híbrida y un servicio de BizTalk para el sitio web.

<a name="CreateHC"></a>

## C. Creación de una conexión híbrida en un servicio de BizTalk

1.  De vuelta en el portal en vista previa, desplácese al cuadro de su sitio web y elija **Conexiones híbridas**.

    ![Hybrid connections][]

2.  En el cuadro de conexiones híbridas, haga clic en **Agregar**.

    ![Add a hybrid connnection][]

3.  Se abre el cuadro **Agregar una conexión híbrida**. Dado que esta es su primera conexión híbrida, la opción **Nueva conexión híbrida** aparece preseleccionada y se abre el cuadro **Crear conexión híbrida**.

    ![Create a hybrid connection][]

    En el cuadro **Crear conexión híbrida**, haga lo siguiente:

    -   En **Nombre**, escriba un nombre para la conexión.
    -   En **Nombre de host**, escriba el nombre del equipo del equipo host SQL Server.
    -   En **Puerto**, escriba 1433 (puerto predeterminado para SQL Server).
    -   Haga clic en **Servicio de Biz Talk**.

4.  Se abre el cuadro **Crear servicio de Biz Talk**. Escriba un nombre para el servicio de BizTalk y luego haga clic en **Aceptar**.

    ![Create BizTalk service][]

    El cuadro **Crear servicio de Biz Talk** se cierra y vuelve al cuadro **Crear conexión híbrida**.

5.  En el cuadro Crear conexión híbrida, haga clic en **Aceptar**.

    ![Click OK][]

6.  Al finalizar el proceso, el área Notificaciones del portal le informa de que la conexión se ha creado correctamente.

    ![Success notification][]

7.  En el cuadro de sitio web, el icono de **Conexiones híbridas** ahora muestra que se ha creado 1 conexión híbrida.

    ![One hybrid connection created][]

Llegados a este punto, ha completado una parte importante de la infraestructura de conexión híbrida en la nube. A continuación, creará la parte local correspondiente.

<a name="InstallHCM"></a>

## D. Instalación del administrador de conexiones híbridas local para completar la conexión

1.  En el cuadro de sitio web, haga clic en el icono de Conexiones híbridas.

    ![Hybrid connections icon][]

2.  En el cuadro **Conexiones híbridas**, la columna **Estado** del extremo agregado recientemente muestra **No conectado**. Haga clic en la conexión para configurarla.

    ![Not connected][]

    Se abre el cuadro de la conexión híbrida.

    ![NotConnectedBlade][]

3.  En el cuadro, haga clic en **Configuración del proceso de escucha**.

    ![Click Listener Setup][]

4.  Se abre el cuadro **Propiedades de conexión híbrida**. En **Administrador de conexiones híbridas local**, elija **Haga clic aquí para instalar**.

    ![Click here to install][]

5.  En el cuadro de diálogo de advertencia de seguridad Ejecución de la aplicación, elija **Ejecutar** para continuar.

    ![Choose Run to continue][]

6.  En el cuadro de diálogo **Control de cuentas de usuario**, elija **Sí**.

    ![Choose Yes][]

7.  El Administrador de conexiones híbridas se descarga y se instala.

    ![Instalación][]

8.  Cuando finalice la instalación, haga clic en **Cerrar**.

    ![Click Close][]

    En el cuadro **Conexiones híbridas**, la columna **Estado** muestra ahora **Conectado**.

    ![Connected Status][]

Ahora que la infraestructura de la conexión híbrida se ha completado, creará una aplicación web que la use.

<a name="CreateASPNET"></a>

## E. Creación de un proyecto ASP.NET básico, edición de la cadena de conexión de base de datos y ejecución del proyecto localmente

### Creación de un proyecto ASP.NET básico

1.  En Visual Studio, en el menú **Archivo**, elija Nuevo proyecto:

    ![New Visual Studio project][]

2.  En la sección **Plantillas** del cuadro de diálogo **Nuevo proyecto**, seleccione **Web**, elija **Aplicación web ASP.NET** y haga clic en **Aceptar**.

    ![Choose ASP.NET Web Application][]

3.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, seleccione **MVC** y haga clic en **Aceptar**.

    ![Choose MVC][]

4.  Cuando el proyecto se haya creado, aparecerá la página Léame de la aplicación. No ejecute el proyecto web todavía.

    ![Readme page][]

### Edición de la cadena de conexión de la base de datos para la aplicación

En este paso editará la cadena de conexión que indica a la aplicación dónde buscar la base de datos de SQL Server Express local. La cadena de conexión es un archivo Web.config de la aplicación que contiene información de configuración para dicha aplicación.

> [WACOM.NOTE] Para garantizar que la aplicación usa la base de datos creada en SQL Server Express y no la base de datos LocalDB predeterminada de Visual Studio, es importante que complete este paso antes de ejecutar el proyecto.

1.  En el Explorador de soluciones, haga doble clic en el archivo Web.config.

    ![Web.config][]

2.  Edite la sección **connectionStrings** para apuntar a la base de datos de SQL Server en la máquina local, siguiendo la sintaxis del ejemplo que se muestra continuación:

    ![Cadena de conexión][]

    Cuando escriba la cadena de conexión, tenga en cuenta lo siguiente:

    -   Si se está conectando a una instancia con nombre en lugar de a una instancia predeterminada (por ejemplo SuServidor\\SQLEXPRESS), debe configurar su servidor SQL Server para usar puertos estáticos. Para obtener información sobre la configuración de puertos estáticos, vea [Cómo configurar SQL Server para que escuche en un puerto específico][]. De forma predeterminada, las instancias con nombre usan puertos dinámicos y UDP, que Conexiones híbridas no admite.

    -   Es recomendable que especifique el puerto (1433 de forma predeterminada, como se muestra en el ejemplo) en la cadena de conexión de forma que pueda asegurarse de que su servidor SQL Server local tiene la funcionalidad TCP habilitada y usa el puerto correcto.

    -   Recuerde usar Autenticación de SQL Server para conectarse, especificando el identificador y la contraseña del usuario en la cadena de conexión.

3.  Haga clic en **Guardar** en Visual Studio para guardar el archivo Web.config.

### Ejecución del proyecto localmente y registro de un nuevo usuario

1.  Ahora, ejecute el nuevo proyecto web localmente haciendo clic en el botón Examinar que se encuentra debajo de Depurar. En este ejemplo se usa Internet Explorer.

    ![Run project][]

2.  En la parte superior derecha de la página web predeterminada, elija **Registrar** para registrar una nueva cuenta:

    ![Register a new account][]

3.  Escriba un nombre de usuario y una contraseña:

    ![Enter user name and password][]

    Esta operación creará automáticamente una base de datos en su servidor SQL Server local que hospedará la información de pertenencia de la aplicación. Una de las tablas (**dbo.AspNetUsers**) hospeda las credenciales de usuario del sitio web como las que acaba de introducir. Verá esta tabla posteriormente en el tutorial.

4.  Cierre la ventana del explorador de la página web predeterminada. Esta operación detendrá la aplicación en Visual Studio.

Ahora está preparado para continuar con el paso siguiente, que consiste en publicar la aplicación en Azure y probarla.

<a name="PubNTest"></a>

## F. Publicación de la aplicación web en Azure y prueba de la misma

Ahora publicará la aplicación en su sitio web en Azure y después la probará para ver cómo se usa la conexión híbrida que configuró anteriormente para conectar la aplicación del sitio web a la base de datos en la máquina local.

### Publicación de la aplicación

1.  Puede descargar el perfil de publicación para el sitio web en el Portal de Azure. En el cuadro del sitio web, elija **Descargar perfil de publicación** y guarde el archivo en su PC.

    ![Descargar archivo de publicación][]

    ![Publish profile in downloads folder][]

    A continuación, importará este archivo en la aplicación web de Visual Studio.

2.  En Visual Studio, haga clic con el botón secundario en el nombre del proyecto en el Explorador de soluciones y seleccione **Publicar**.

    ![Select publish][]

3.  En el cuadro de diálogo **Publicación web**, en la pestaña **Perfil**, elija **Importar**.

    ![Importación][]

4.  Busque su perfil descargado, selecciónelo y haga clic en **Aceptar**.

    ![Browse to profile][]

5.  Su información de publicación se importará y mostrará en la pestaña **Conexión** del cuadro de diálogo.

    ![Haga clic en Publicar.][]

    Haga clic en **Publicar**.

    Cuando la publicación se complete, el explorador se iniciará y mostrará su, ahora, sitio web familiar, ¡con la diferencia de que ahora está activo en la nube de Azure!

A continuación, usará la aplicación web activa para ver su conexión híbrida en acción.

### Prueba de la aplicación web completada en Azure

1.  En la parte superior de la página web en Azure, elija **Iniciar sesión**.

    ![Test log in][]

2.  El sitio web de Azure ahora estará conectado a la base de datos de pertenencia de la aplicación web en la máquina local. Para comprobarlo, inicie sesión con las mismas credenciales que introdujo en la base de datos local anteriormente.

    ![Hello greeting][]

3.  Para seguir probando su nueva conexión híbrida, cierre la sesión de la aplicación web de Azure y regístrese como otro usuario. Proporcione un nuevo nombre de usuario y contraseña y haga clic en **Registrar**.

    ![Test register another user][]

4.  Para comprobar que las credenciales del nuevo usuario se han almacenado en la base de datos local a través de la conexión híbrida, abra SQL Management Studio en el equipo local. En el Explorador de objetos, expanda la base de datos **MembershipDB** y expanda **Tablas**. Haga clic con el botón secundario en la tabla de pertenencia **dbo.AspNetUsers** y elija **Seleccionar las primeras 1000 filas** para ver los resultados.

    ![View the results][]

5.  La tabla de pertenencia local ahora muestra ambas cuentas, la que creó localmente y la que creó en la nube de Azure. La que creó en la nube se ha guardado en la base de datos local a través de la característica Conexión híbrida de Azure.

    ![Registered users in on-premises database][]

Ya ha creado e implementado una aplicación web ASP.NET que usa una conexión híbrida entre un sitio web y la nube de Azure y una base de datos de SQL Server local. ¡Enhorabuena!

## Otras referencias

[Introducción a las conexiones híbridas][Conexiones híbridas]

[Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)][]

[Sitio web de conexiones híbridas][]

[Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas][]

[Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)][]

[Conexión a un SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas][]

[Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)][]

[Introducción a la identidad de ASP.NET][]

<!-- IMAGES -->

  [Portal de Azure en vista previa]: https://portal.azure.com
  [Conexiones híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Evaluación gratuita de Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Descargas de Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Centro de descarga de Microsoft]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22
  [página de bases de datos de Plataforma web de Microsoft]: http://www.microsoft.com/web/platform/database.aspx
  [Creación de una base de datos de SQL Server local]: #CreateSQLDB
  [A. Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos de SQL Server local]: #InstallSQL
  [B. Creación de un sitio web en el portal de vista previa de Azure]: #CreateSite
  [C. Creación de una conexión híbrida en un servicio de BizTalk]: #CreateHC
  [D. Instalación del administrador de conexiones híbridas local para completar la conexión]: #InstallHCM
  [E. Creación de un proyecto ASP.NET básico, edición de la cadena de conexión de base de datos y ejecución del proyecto localmente]: #CreateASPNET
  [F. Publicación de la aplicación web en Azure y prueba de la misma]: #PubNTest
  [SQL Server Install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
  [Choose Default Instance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
  [Choose Mixed Mode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
  [Habilitar el protocolo de red TCP/IP para SQL Server]: http://technet.microsoft.com/en-us/library/hh231672%28v=sql.110%29.aspx
  [Connect to Server]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
  [Create new database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
  [Provide database name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
  [MembershipDB created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
  [New button]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
  [Instalación]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
  [New Visual Studio project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
  [Choose ASP.NET Web Application]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
  [Choose MVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
  [Readme page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
  [Web.config]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
  [Cadena de conexión]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
  [Cómo configurar SQL Server para que escuche en un puerto específico]: http://support.microsoft.com/kb/823938
  [Run project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
  [Register a new account]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
  [Enter user name and password]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
  [Descargar archivo de publicación]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
  [Publish profile in downloads folder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
  [Select publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
  [Importación]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
  [Browse to profile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
  [Haga clic en Publicar.]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
  [Test log in]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
  [Hello greeting]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
  [Test register another user]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
  [View the results]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
  [Registered users in on-premises database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
  [Josh Twist presenta las conexiones híbridas (vídeo de Channel 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Sitio web de conexiones híbridas]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas]: http://azure.microsoft.com/es-es/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Creación de una nube híbrida del mundo real con una perfecta portabilidad de aplicaciones (vídeo de Canal 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Conexión a un SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Conexión a un servidor SQL Server local desde Servicios móviles de Azure mediante conexiones híbridas (vídeo de Canal 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
  [Introducción a la identidad de ASP.NET]: http://www.asp.net/identity
