<properties  linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Azure for ASP.NET" pageTitle="Get started with Azure for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Web Site. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Introducción a Azure y ASP.NET

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/es-es/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

 Este tutorial muestra cómo crear una aplicación web ASP.NET e implementarla en un Sitio web Azure utilizando Visual Studio 2013 o Visual Studio 2013 Express para Web. En este tutorial se asume que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una aplicación web sencilla ejecutándose en la nube.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

Aprenderá a realizar los siguientes procedimientos:

* Habilitar su equipo para desarrollar contenido de Azure mediante la
  instalación del SDK de Azure.
* Crear un proyecto web Visual Studio ASP.NET e implementarlo en un
  Sitio web Azure.
* Hacer cambios en el proyecto y volver a implementarlo.

La siguiente ilustración muestra la aplicación completada:

![Página principal del sitio
web](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este
> tutorial. Si aún no la tiene, puede [activar los beneficios de
> suscripción a MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), o bien [regístrese para obtener
> una evaluación gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Secciones del tutorial

* [Configuración del entorno de
  desarrollo](#set-up-the-development-environment)
* [Creación de un proyecto web ASP.NET en Visual
  Studio](#create-an-asp.net-web-project)
* [Implementación de la aplicación en
  Azure](#deploy-the-application-to-azure)
* [Realización de cambios y nueva
  implementación](#make-a-change-and-redeploy)
* [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creación de una aplicación web ASP.NET

El primer paso es crear un proyecto de aplicación web. Visual Studio creará automáticamente el Sitio web Azure en el que implementará a continuación su proyecto.

1.  Abra Visual Studio 2013 o Visual Studio 2013 Express para Web.

2.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.
    
    ![Nuevo proyecto en el menú
    Archivo](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  En el cuadro de diálogo **Nuevo proyecto**, expanda **C#** o **Visual Basic** y seleccione **Web** debajo de **Plantillas instaladas**; a continuación, seleccione <strong>Aplicación web ASP.NET </strong>.

4.  Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.

5.  Póngale a la aplicación el nombre **MyExample** y haga clic en **Aceptar**.
    
    ![Cuadro de diálogo Nuevo
    proyecto](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  En el cuadro de diálogo **New ASP.NET Project**, seleccione la plantilla **MVC** o **Web Forms** y, a continuación, haga clic en **Change Authentication**.
    
    [MVC y Web Forms (información en inglés)][1] son marcos de ASP.NET para la creación de sitios web. Si le da igual uno que otro y tiene previsto realizar otros tutoriales de Azure, es recomendable elegir MVC porque es el que se utiliza en ellos.
    
    ![Cuadro de diálogo New ASP.NET
    Project](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  En el cuadro de diálogo **Change Authentication**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.
    
    ![Sin
    autenticación](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    La aplicación de ejemplo que está creando no incluirá características que requieran que los usuarios inicien sesión. Para obtener información acerca de cómo implementar características de autenticación y autorización, consulte la sección [Pasos siguientes](#next-steps) al final de este tutorial.

8.  En el cuadro de diálogo, en **Azure**, deje activada la casilla y la selección de **Sitio web** en el cuadro desplegable.
    
    Es posible que esta casilla lleve el nombre **Host in the cloud** o **Create remote resources**. En cualquier caso, el resultado será el mismo.
    
    Con esta configuración se especifica que Visual Studio creará un Sitio web Azure para su proyecto web. Después implementará el proyecto web en el sitio web recién creado. También puede cambiar la selección del cuadro desplegable para que Visual Studio cree una Máquina virtual de Azure que ejecute IIS, pero este tutorial no detalla los pasos para esa opción.

9.  En el cuadro de diálogo **New ASP.NET Project**, haga clic en
    **Aceptar**.
    
    ![Cuadro de diálogo New ASP.NET
    Project](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)
    
    La captura de pantalla muestra la plantilla MVC seleccionada; en todo caso, si elige Web Forms, **Web Forms** será la opción seleccionada.

10. Si no ha iniciado sesión todavía en Azure, Visual Studio le indicará que lo haga. Haga clic en **Sign In**.
    
    ![Iniciar sesión en
    Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

11. Escriba el identificador y la contraseña de la cuenta que desea usar para administrar la suscripción a Azure.
    
    ![Iniciar sesión en
    Azure](./media/web-sites-dotnet-get-started-vs2013/signindb.png)
    
    Cuando haya iniciado sesión, el cuadro de diálogo **Configure Azure Site Settings** le preguntará que recursos desea crear.
    
    ![Sesión iniciada en
    Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

12. Visual Studio ofrece un **nombre de sitio** predeterminado, que Azure utilizará como prefijo para la URL de su aplicación. Si lo prefiere, escriba un nombre de sitio diferente.
    
    La URL completa consistirá en el valor que escriba más el dominio que aparece junto al cuadro de texto. Por ejemplo, si el nombre de sitio es `MyExample6442`, la URL será `MyExample6442.azurewebsites.net`. Si elige una URL ya utilizada por otro usuario, verá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y deberá buscar un nombre diferente.

13. En la lista desplegable **Region**, elija la ubicación más cercana.
    
    Este valor especifica el centro de datos de Azure en el que se ejecutará el sitio web.

14. No modifique los campos de la base de datos,
    
    ya que en este tutorial no se utiliza una base de datos. En la sección [Pasos siguientes](#next-steps) al final de este documento encontrará un enlace a un tutorial que muestra cómo utilizar una base de datos.

15. Haga clic en **OK**.
    
    En pocos segundos, Visual Studio crea el proyecto web en la carpeta especificada y crea el sitio web en la región de Azure especificada.
    
    La ventana del **Explorador de soluciones** muestra los archivos y las carpetas del nuevo proyecto. (La captura de pantalla corresponde a un proyecto Web Forms; los proyectos MVC tienen diferentes carpetas y archivos).
    
    ![Explorador de
    soluciones](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)
    
    La ventana **Web Publish Activity** muestra que el sitio se ha creado.
    
    ![Sitio web
    creado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

## Implementación de la aplicación en Azure

1.  En la ventana **Web Publish Activity** haga clic en **Publish MyExample to this site now**.
    
    ![Sitio web
    creado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)
    
    En unos segundos aparece el asistente para **publicación web**. El asistente crea un nuevo *perfil de publicación* que contiene valores como la URL del sitio web que Visual Studio necesita para implementar su proyecto en Azure. El perfil se guarda automáticamente para que más tarde, cuando haga cambios en el proyecto, pueda volver a implementar el proyecto fácilmente en el mismo sitio.

2.  En la pestaña **Connection** del asistente para **publicación web**, haga clic en **Validate Connection** para asegurarse de que Visual Studio pueda conectar con Azure para implementar el proyecto web.
    
    ![Validar conexión](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**.

3.  Haga clic en **Next**.
    
    ![Conexión validada
    correctamente](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

4.  En la pestaña **Settings**, haga clic en **Next**.
    
    ![Pestaña
    Settings](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    Puede aceptar la configuración predeterminada de esta pestaña. Está implementando una compilación de versión y no es necesario eliminar archivos del servidor de destino, precompilar la aplicación ni excluir archivos de la carpeta App\_Data. En la sección [Pasos siguientes](#next-steps) al final de este documento encontrará un enlace a un tutorial que implementa una compilación de depuración y muestra cómo ejecutar Visual Studio en modo de depuración de manera remota.

5.  En la pestaña **Preview**, haga clic en **Start Preview**.
    
    ![Botón StartPreview de la pestaña Preview
    ](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer.

6.  Haga clic en **Publicar**.
    
    ![Salida de archivo
    StartPreview](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.
    
    Las ventanas **Output** y **Web Publish Activity** muestran qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.
    
    ![Ventana Resultado que informa de que la implementación se realizó correctamente](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)
    
    Tras la correcta finalización de la implementación, el explorador predeterminado se dirige automáticamente a la URL del sitio web implementado. La aplicación que ha creado se ejecuta ahora en la nube. La URL de la barra de direcciones del explorador muestra que el sitio se está cargando desde Internet.
    
    ![Sitio web ejecutándose en
    Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

7.  Cierre el explorador.

## Realización de cambios y nueva implementación

En esta sección opcional del tutorial, cambiará el proyecto web, ejecutará el proyecto localmente en su equipo de desarrollo para comprobar el cambio y, a continuación, implementará el cambio en Azure.

1.  Si creó un proyecto MVC, abra el archivo *Views/Home/Index.cshtml* o *.vbhtml* en el **Explorador de soluciones**, cambie el encabezado **h1** de "ASP.NET" a "ASP.NET y Azure" y guarde el archivo.
    
    ![MVC
    index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)
    
    ![cambio en h1 de
    MVC](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

2.  Si creó un proyecto Web Forms, abra el archivo *Default.aspx* en el **Explorador de soluciones**, cambie el encabezado **h1** de "ASP.NET" a "ASP.NET y Azure" y guarde el archivo.
    
    ![Web Forms
    default.aspx](./media/web-sites-dotnet-get-started-vs2013/default.png)
    
    ![cambio en h1 de Web
    Forms](./media/web-sites-dotnet-get-started-vs2013/wfandazure.png)

3.  Presione CTRL+F5 para probar el cambio ejecutando el sitio en su equipo local.
    
    ![Sitio web ejecutándose de forma
    local](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)
    
    La URL `http://localhost` indica que se está ejecutando en el equipo local. De manera predeterminada, se ejecuta en IIS Express, que es una versión ligera de IIS diseñada para el desarrollo de aplicaciones web.

4.  Cierre el explorador.

5.  En el **Explorador de soluciones**, haga clic con el botón secundario y seleccione **Publicar**.
    
    ![Seleccionar
    Publicar](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)
    
    En unos segundos aparece el asistente para **publicación web**. Si tuviera que cambiar alguna configuración de publicación podría elegir otra pestaña; sin embargo, ahora todo lo que desea es repetir la implementación con la misma configuración.

6.  En el asistente para **publicación web**, haga clic en **Publicar**.
    
    ![Haga clic en
    Publicar.](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)
    
    Visual Studio implementa el proyecto en Azure y abre el sitio en el explorador predeterminado.
    
    ![Implementación del sitio con
    cambios](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

Hay una manera aún más rápida de volver a implementar cuando no es necesario cambiar la configuración de publicación: utilizar la barra de herramientas **Publicación en Web con un solo clic**.

![Barra de herramientas Publicación en Web con un solo clic](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

La barra de herramientas no está habilitada de manera predeterminada, puede habilitarla en el menú **Ver - Barras de herramientas**. Puede utilizarla para seleccionar un perfil, hacer clic en un botón para publicar o hacer clic en un botón para abrir el asistente para
**publicación web**.

## Pasos siguientes

En este tutorial, ha aprendido a crear una aplicación web sencilla e implementarla en un Sitio web Azure. Estos son algunos temas y recursos relacionados que le permitirán obtener más información al respecto:

* Otros métodos de implementar un proyecto web
* Administración, escalación y solución de problemas de un sitio
* Incorporación de funcionalidad de base de datos y autorización
* Selección entre Sitios web, Servicios en la nube y Máquinas virtuales
  para aplicaciones web

### Otros métodos de implementar un proyecto web

En este tutorial hemos visto el método más rápido de crear un sitio e implementarlo en una sola operación. Para obtener información general acerca de otros métodos de implementación, ya sea mediante Visual Studio o mediante una [implementación automatizada (información en inglés)][2] desde el [sistema de control de código fuente (información en inglés)][3], consulte [Implementación de un sitio web Azure](/es-es/documentation/articles/web-sites-deploy/").

Una forma de automatizar la implementación es utilizar los scripts de Windows PowerShell. Visual Studio y Azure simplifican la tarea generando scripts de PowerShell que puede utilizar para realizar las mismas operaciones de implementación que lleva a cabo en Visual Studio. Para obtener más información, consulte [Automate Everything (Building Real-World Cloud Apps with Azure)][4].

### Administración de un sitio web

El [Portal de administración de Azure](en-us/services/management-portal/) es una interfaz web que puede utilizar para administrar y supervisar todos los servicios de Azure, como sitios web, servicios en la nube, máquinas virtuales, bases de datos y más. Para ver qué puede hacer en el portal, vaya a [https://manage.windowsazure.com]() e inicie sesión con el nombre de usuario y la contraseña de la cuenta que tiene los derechos administrativos de la suscripción a Azure. Para obtener más información, consulte [Administración de sitios web](/es-es/manage/services/web-sites/how-to-manage-websites/).

También puede realizar algunas tareas de administración de sitios web directamente desde el **Explorador de servidores** en Visual Studio. Para obtener más información acerca de lo que puede hacer en el **Explorador de servidores**, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Escalación de un sitio web

Si el sitio es público y empieza a tener más tráfico, es posible que los tiempos de respuesta se alarguen. Para solucionarlo, puede agregar fácilmente recursos de servidores en la pestaña **Escalar** del portal de administración. Para obtener más información, consulte [Escalación de un sitio web](/es-es/manage/services/web-sites/how-to-scale-websites/). (Agregar recursos de servidores para escalar un sitio web no es
gratuito).

### Solucionar problemas de un sitio web

Es posible que desee consultar los resultados de seguimiento o los registros para obtener ayuda con la solución de problemas. Visual Studio ofrece herramientas integradas que facilitan la visualización de los registros de Azure cuando se generan, en tiempo real. También puede ejecutar el sitio en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Incorporación de funcionalidad de base de datos y autorización

La mayoría de los sitios web de producción utilizan una base de datos y restringen algunas funciones del sitio a los usuarios autorizados. En el siguiente enlace encontrará un tutorial introductorio acerca del acceso a base de datos, la autenticación y la autorización: [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure](/es-es/develop/net/tutorials/web-site-with-sql-database/).

### Decidir si la aplicación debe ejecutarse en un Servicio en la nube

En Azure puede ejecutar aplicaciones web en Sitios web, como se muestra en este tutorial, o en Servicios en la nube o Máquinas virtuales. Para obtener más información, consulte [Modelos de ejecución de Azure](/es-es/develop/net/fundamentals/compute/) y [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno](/es-es/manage/services/web-sites/choose-web-app-service/).



[1]: http://www.asp.net/get-started/websites
[2]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
[3]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything