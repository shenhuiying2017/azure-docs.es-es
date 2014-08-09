<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Introducción a Azure y ASP.NET
==============================

[Visual Studio 2013](/es-es/develop/net/tutorials/get-started/ "Visual Studio 2013")[Visual Studio 2012](/es-es/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012")

**Nota:**

Hay disponible una [versión más reciente de este tutorial](/es-es/develop/net/tutorials/get-started/). Puede seguir utilizando esta versión si desea emplear Visual Studio 2012, pero aquí no se muestran todas las características del SDK de Azure más reciente.

Este tutorial muestra cómo implementar una aplicación web ASP.NET en un sitio web de Azure utilizando el Asistente para publicación web de Visual Studio 2012 o Visual Studio 2012 Express para Web. Si lo prefiere, puede seguir los pasos del tutorial con Visual Studio 2010 o Visual Web Developer Express 2010.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2012, el SDK instala automáticamente Visual Studio 2012 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

Aprenderá a realizar los siguientes procedimientos:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Crear un proyecto ASP.NET MVC 4 de Visual Studio y publicarlo en un sitio web de Azure

La siguiente ilustración muestra la aplicación completada:

![Ejemplo de sitio web](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

**Nota**: para completar este tutorial, deberá tener una cuenta de Azure. Si aún no la tiene, puede [activar los beneficios de suscripción a MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), o bien [regístrese para obtener una evaluación gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Secciones del tutorial

1.  [Configuración del entorno de desarrollo](#setupdevenv)
2.  [Creación de un sitio web en Azure](#setupwindowsazure)
3.  [Creación de una aplicación ASP.NET MVC 4](#createmvc4app)
4.  [Implementación de la aplicación en Azure](#deploytowindowsazure)
5.  [Pasos siguientes](#nextsteps)

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

Creación de sitioCreación de un sitio web
-----------------------------------------

El paso siguiente es crear el sitio web de Azure.

1.  En el [Portal de administración de Azure](http://manage.windowsazure.com), haga clic en **Sitios web** y, a continuación, en **New**.

	![Sitio web nuevo](./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png)

1.  Haga clic en **Quick Create**.

    ![Creación rápida](./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png)

2.  En el paso **Create Web Site** del asistente, escriba una cadena en el cuadro **URL** para utilizar como la dirección URL única para la aplicación. La dirección URL completa consistirá en la información que escriba aquí, además del sufijo que ve junto al cuadro de texto. La ilustración muestra **ejemplo1**, pero si alguien ya ha tomado esa cadena para una dirección URL, deberá escribir un valor distinto.

3.  En la lista desplegable **Región**, elija la región más cercana. Este valor especifica el centro de datos en el que se ejecutará el sitio web.

4.  Haga clic en la flecha **Create Web Site**.

    ![Crear un sitio web nuevo](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

    El Portal de administración vuelve a la página de Sitios web y la columna **Status** indica que se está creando el sitio. Después de unos segundos, la columna **Status** indica que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Sitios web**.

    ![Página Sitios web del Portal de administración, sitio web creado](./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png)

Creación de la aplicaciónCreación de una aplicación ASP.NET MVC 4
-----------------------------------------------------------------

Ya creó un sitio web de Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear el proyecto de aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1.  Inicie Visual Studio 2012 o Visual Studio 2012 Express para Web.

2.  En el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

	![Nuevo proyecto en el menú Archivo](./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png)

1.  En el cuadro de diálogo **Nuevo proyecto**, expanda **C\#** y seleccione **Web** debajo de **Plantillas instaladas**; a continuación, seleccione **Aplicación web de ASP.NET MVC 4**.

2.  Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.

3.  Póngale a la aplicación el nombre **MyExample** y haga clic en **OK**.

	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png)

1.  En el cuadro de diálogo **New ASP.NET MVC 4 Project**, seleccione la plantilla **Internet Application** y haga clic en **OK**.

	![Cuadro de diálogo de nuevo proyecto de ASP.NET MVC 4](./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png)

### Ejecución de la aplicación de forma local

1.  Presione **CTRL**+**F5** para ejecutar la aplicación. Aparece la página principal de la aplicación en el explorador predeterminado.

	![Sitio web ejecutándose de forma local](./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png)

Esto es todo lo que necesita hacer para crear una aplicación simple que va a implementar en Azure.

Implementación de la aplicaciónImplementación de la aplicación en Azure
-----------------------------------------------------------------------

1.  En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.

    ![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png)

	Se abre el asistente para **publicación web**.

1.  En la pestańa **Perfil** del asistente para **publicación web**, haga clic en **Importar**.

    ![Importar configuración de publicación](./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png)

	Aparecerá el cuadro de diálogo **Import Publish Profile**.

1.  Lleve a cabo los siguientes pasos si no ha agregado anteriormente su suscripción de Azure en Visual Studio: con estos pasos agregará su suscripción, de manera tal que la lista desplegable que aparece bajo **Import from an Azure web site** incluirá su sitio web.

    -   En el cuadro de diálogo **Import Publish Profile**, haga clic en **Import from an Azure web site** y, a continuación, en **Add Azure subscription**.

    ![Agregar suscripción de Azure](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)

    -   En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Download subscription file**.

    ![Descargar archivo de suscripción](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)

    -   En la ventana del explorador, guarde el archivo *.publishsettings*.

    ![Descargar archivo .publishsettings](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

    -   En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Browse** y navegue al archivo *.publishsettings*.

    ![Descargar sub](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

    -   Haga clic en **Import**.

    ![Importar](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

    > [WACOM.NOTE]
    >  El archivo .publishsettings contiene sus credenciales (sin codificar) que se utilizan para administrar sus suscripciones y servicios de Azure. El procedimiento recomendado para este archivo consiste en almacenarlo temporalmente fuera de los directorios de origen (por ejemplo en la carpeta Bibliotecas\\Documentos) y, a continuación, eliminarlo cuando la importación se haya completado. Un usuario malintencionado que obtuviera acceso al archivo .publishsettings podría modificar, crear y eliminar sus servicios de Azure.

2.  En el cuadro de diálogo **Import Publish Profile**, seleccione **Import from an Azure web site**, seleccione su sitio web en la lista desplegable y, a continuación, haga clic en **OK**.

    ![Opción Import Publish Profile](./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png)

3.  En la pestańa **Conexión**, haga clic en **Validate Connection** para asegurarse de que la configuración es correcta.

    ![Opción Validate Connection](./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png)

4.  Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**. Haga clic en **Next**.

    ![Conexión validada correctamente](./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png)

5.  En la pestańa **Settings**, desactive la opción **Use this connection string at runtime**, debido a que esta aplicación no utiliza una base de datos. Puede aceptar la configuración predeterminada para los elementos restantes de esta página. Está implementando una configuración de compilación de versión y no es necesario eliminar archivos del servidor de destino, precompilar la aplicación ni excluir archivos de la carpeta App\_Data. Haga clic en **Next**.

    ![Pestańa Settings](./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png)

6.  En la pestańa **Preview**, haga clic en **Start Preview**.

    ![Botón StartPreview de la pestańa Preview ](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png)

    En esta pestańa aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer. En este caso, no tiene que hacer nada con la lista de archivos que se muestra.

    ![Salida de archivo StartPreview](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png)

7.  Haga clic en **Publicar**. Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.

8.  La ventana **Output** muestra qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

    ![Ventana Resultado que informa de que la implementación se realizó correctamente](./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png)

9.  Una vez que se realiza correctamente la implementación, el explorador predeterminado se dirige automáticamente a la URL del sitio web implementado. La aplicación creada ahora se ejecuta en la nube.

    ![Sitio web ejecutándose en Azure](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

Pasos siguientesPasos siguientes
--------------------------------

En este tutorial, ha aprendido a implementar una aplicación web sencilla en un sitio web de Azure. Hay disponibles otros recursos para mostrarle cómo administrar, escalar y solucionar problemas en el sitio, cómo agregar la funcionalidad de base de datos, autenticación y autorización y cómo decidir si la aplicación debe ejecutarse en un Servicio en la nube de Azure en lugar de hacerlo en un sitio web de Azure.

### Administración de un sitio web

Una vez que termine de utilizar el sitio, puede eliminarlo; además, en algunas ocasiones, es posible que desee dejarlo sin conexión de manera temporal o bien cambiar su configuración. Puede realizar algunas de estas funciones directamente desde el **Explorador de servidores** en Visual Studio.

![Sitios web de Azure en el Explorador de servidores](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Configuración del sitio web en Visual Studio](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

Puede utilizar el Portal de administración de Azure para eliminar el sitio web. La siguiente captura de pantalla muestra los botones **Stop**, **Restart** y **Delete** en la pestańa **Panel** del portal de administración.

![Pestańa Panel del portal de administración](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

Puede cambiar la configuración del sitio en la pestańa **Configure**. Para obtener más información, consulte [Administración de sitios web](/es-es/manage/services/web-sites/how-to-manage-websites/).

### Escalación de un sitio web

Si el sitio es público y empieza a tener más tráfico, es posible que los tiempos de respuesta se alarguen. Para solucionarlo, puede agregar fácilmente recursos de servidores en la pestańa **Escalar** del portal de administración.

![Pestańa Escalar del Portal de administración](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

Para obtener más información, consulte [Escalación de un sitio web](/es-es/manage/services/web-sites/how-to-scale-websites/). (Agregar recursos de servidores para escalar un sitio web no es gratuito).

### Solucionar problemas de un sitio web

Es posible que desee consultar los resultados de seguimiento o los registros para obtener ayuda con la solución de problemas. Visual Studio ofrece herramientas integradas que facilitan la visualización de los registros de Azure cuando se generan, en tiempo real.

![Registros en Visual Studio](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

Para obtener más información, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Incorporación de funcionalidad de base de datos y autorización

La mayoría de los sitios web de producción utilizan una base de datos y restringen algunas funciones del sitio a los usuarios autorizados. En el siguiente vínculo encontrará un tutorial introductorio acerca del acceso a base de datos, la autenticación y la autorización: [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure](/es-es/develop/net/tutorials/web-site-with-sql-database/).

### Decidir si la aplicación debe ejecutarse en un Servicio en la nube

En algunos escenarios, es posible que desee ejecutar su aplicación en un Servicio en la nube de Azure en lugar de hacerlo en un sitio web de Azure. Para obtener más información, consulte [Modelos de ejecución de Azure](/es-es/develop/net/fundamentals/compute/) y [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno](http://msdn.microsoft.com/es-es/library/windowsazure/jj218759.aspx). Si desea ver una serie de tutoriales que muestran cómo crear una aplicación web ASP.NET de niveles múltiples e implementarla en un Servicio en la nube, consulte [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/).

