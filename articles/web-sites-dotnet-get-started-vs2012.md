<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Introducción a Sitios web Azure y ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Nota:</strong><p>Hay disponible una <a href="/en-us/develop/net/tutorials/get-started/">versi&oacute;n m&aacute;s reciente de este tutorial</a>. Puede seguir utilizando esta versi&oacute;n si desea emplear Visual Studio 2012, pero aqu&iacute; no se muestran todas las caracter&iacute;sticas del SDK de Azure m&aacute;s reciente.</p></div>

En este tutorial se muestra cómo implementar una aplicación web ASP.NET en un sitio web de Azure utilizando el Asistente para publicación web de Visual Studio 2012 o Visual Studio 2012 Express para Web. Si lo prefiere, puede seguir los pasos del tutorial con Visual Studio 2010 o Visual Web Developer Express 2010.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2012, el SDK instala automáticamente Visual Studio 2012 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se supone que no tiene ninguna experiencia previa con Azure. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

Aprenderá a realizar los siguientes procedimientos:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Crear un proyecto ASP.NET MVC 4 de Visual Studio y publicarlo en un sitio web de Azure

La siguiente ilustración muestra la aplicación completada:

![Ejemplo de sitio web][]

<div class="dev-callout"><p><strong>Nota</strong>: para completar este tutorial, deber&aacute; tener una cuenta de Azure. Si a&uacute;n no la tiene, puede <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activar los beneficios de suscripci&oacute;n a MSDN</a>, o bien <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">reg&iacute;strese para obtener una evaluaci&oacute;n gratuita</a>.</p></div>

### Secciones del tutorial

1.  [Configuración del entorno de desarrollo][]
2.  [Creación de un sitio web en Azure][]
3.  [Creación de una aplicación ASP.NET MVC 4][]
4.  [Implementación de la aplicación en Azure][]
5.  [Pasos siguientes][]

[WACOM.INCLUDE [install-sdk-2012-only][]]

## <a name="setupwindowsazure"></a><span class="short-header">Creación de un sitio</span>Creación de un sitio web

El paso siguiente es crear el sitio web de Azure.

1.  En el [Portal de administración de Azure][], haga clic en **Sitios web** y elija **Nuevo**.

![Sitio web nuevo][]

1.  Haga clic en **Quick Create**.

    ![Creación rápida][]

2.  En el paso **Crear sitio web** del asistente, escriba una cadena en el cuadro **URL** para utilizarla como dirección URL única para la aplicación. La dirección URL completa consistirá en la información que escriba aquí, además del sufijo que aparece junto al cuadro de texto. La ilustración muestra **ejemplo1**, pero si alguien ya ha tomado esa cadena para una dirección URL, deberá escribir un valor distinto.

3.  En la lista desplegable **Región**, elija la región más cercana.
    Este valor especifica el centro de datos en el que se ejecutará el sitio web.

4.  Haga clic en la flecha **Crear sitio web**.

    ![Creación de un sitio web nuevo][]

    El Portal de administración vuelve a la página Sitios web y la columna **Estado** indica que se está creando el sitio. Después de unos segundos, la columna **Status** indica que el sitio se creó correctamente. En la barra de navegación de la izquierda, aparece el número de sitios que tiene en su cuenta junto al icono **Sitios web**.

    ![Página Sitios web del Portal de administración, sitio web creado][]

## <a name="createmvc4app"></a><span class="short-header">Creación de la aplicación</span>Creación de una aplicación ASP.NET MVC 4

Ya ha creado un sitio web de Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear el proyecto de aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1.  Inicie Visual Studio 2012 o Visual Studio 2012 Express para Web.

2.  En el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

![Nuevo proyecto en el menú Archivo][]

1.  En el cuadro de diálogo **Nuevo proyecto**, expanda **C\#** y seleccione **Web** debajo de **Plantillas instaladas**; a continuación, seleccione **Aplicación web de ASP.NET MVC 4**.

2.  Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.

3.  Póngale a la aplicación el nombre **MyExample** y haga clic en **OK**.

![Cuadro de diálogo Nuevo proyecto][]

1.  En el cuadro de diálogo **New ASP.NET MVC 4 Project**, seleccione la plantilla **Internet Application** y haga clic en **OK**.

![Cuadro de diálogo de nuevo proyecto de ASP.NET MVC 4][]

### Ejecución de la aplicación de forma local

1.  Presione **CTRL**+**F5** para ejecutar la aplicación. Aparece la página principal de la aplicación en el explorador predeterminado.

![Sitio web ejecutándose de forma local][]

Esto es todo lo que necesita hacer para crear una aplicación simple que va a implementar en Azure.

## <a name="deploytowindowsazure"></a><span class="short-header">Implementación de la aplicación</span>Implementación de la aplicación en Azure

1.  En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar** en el menú contextual.

    ![Opción Publicar del menú contextual del proyecto][]

Se abre el asistente para **publicación web**.

1.  En la pestaña **Perfil** del asistente para **publicación web**, haga clic en **Importar**.

    ![Importar configuración de publicación][]

Aparecerá el cuadro de diálogo **Import Publish Profile**.

1.  Lleve a cabo los siguientes pasos si no ha agregado anteriormente su suscripción de Azure en Visual Studio: con estos pasos agregará su suscripción, de manera que la lista desplegable que aparece en **Importar de un sitio web de Azure** incluya su sitio web.

    -   En el cuadro de diálogo **Importar perfil de publicación**, haga clic en **Importar desde un sitio web de Azure** y elija **Agregar suscripción de Azure**.

    ![Agregar suscripción de Azure][]

    -   En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Download subscription file**.

    ![Descargar archivo de suscripción][]

    -   En la ventana del explorador, guarde el archivo *.publishsettings*.

    ![Descargar archivo .publishsettings][]

    -   En el cuadro de diálogo **Import Azure Subscriptions**, haga clic en **Browse** y vaya al archivo *.publishsettings*.

    ![Descarga de sub][]

    -   Haga clic en **Import**.

    ![Importación][]

    > [WACOM.NOTE]
    > El archivo .publishsettings contiene sus credenciales (sin codificar) que se usan para administrar sus suscripciones y servicios de Azure. El procedimiento recomendado para este archivo consiste en almacenarlo temporalmente fuera de los directorios de origen (por ejemplo en la carpeta Bibliotecas\\Documentos) y, a continuación, eliminarlo cuando la importación se haya completado. Un usuario malintencionado que obtuviera acceso al archivo .publishsettings podría modificar, crear y eliminar sus servicios de Azure.

2.  En el cuadro de diálogo **Importar perfil de publicación**, elija **Importar desde un sitio web de Azure**, seleccione su sitio web en la lista desplegable y haga clic en **Aceptar**.

    ![Importar perfil de publicación][]

3.  En la pestaña **Conexión**, haga clic en **Validate Connection** para asegurarse de que la configuración es correcta.

    ![Validar conexión][]

4.  Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**. Haga clic en **Next**.

    ![Conexión validada correctamente][]

5.  En la pestaña **Settings**, desactive la opción **Use this connection string at runtime**, debido a que esta aplicación no utiliza una base de datos. Puede aceptar la configuración predeterminada para los elementos restantes de esta página. Va a implementar una configuración de compilación de versión y no es necesario eliminar archivos en el servidor de destino, precompilar la aplicación ni excluir archivos de la carpeta App\_Data.
    Haga clic en **Siguiente**

    ![Pestaña Settings][]

6.  En la pestaña **Preview**, haga clic en **Start Preview**.

    ![Botón StartPreview de la pestaña Preview ][]

    En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer. En este caso, no tiene que hacer nada con la lista de archivos que se muestra.

    ![Salida de archivo StartPreview][]

7.  Haga clic en **Publicar**. Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.

8.  La ventana **Output** muestra qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

    ![Ventana Resultado que informa de que la implementación se realizó correctamente][]

9.  Una vez que la implementación ha finalizado correctamente, el explorador predeterminado abre automáticamente la dirección URL del sitio web implementado.
    La aplicación que ha creado se está ejecutando ya en la nube.

    ![Sitio web ejecutándose en Azure][Ejemplo de sitio web]

## <a name="nextsteps"></a><span class="short-header">Pasos siguientes</span>Pasos siguientes

En este tutorial ha aprendido a implementar una aplicación web sencilla en un sitio web de Azure. Hay disponibles otros recursos para mostrarle cómo administrar, escalar y solucionar problemas en el sitio, cómo agregar funcionalidad de base de datos, autenticación y autorización, y cómo decidir si la aplicación debe ejecutarse en un servicio en la nube de Azure en lugar de un sitio web de Azure.

### Administración de un sitio web

Una vez que termine de utilizar el sitio, puede eliminarlo; además, en algunas ocasiones, es posible que desee dejarlo sin conexión de manera temporal o bien cambiar su configuración. Puede realizar algunas de estas funciones directamente desde el **Explorador de servidores** en Visual Studio.

![Sitios web de Azure en el Explorador de servidores][]

![Configuración del sitio web en Visual Studio][]

Puede utilizar el Portal de administración de Azure para eliminar el sitio web. La siguiente captura de pantalla muestra los botones **Stop**, **Restart** y **Delete** en la pestaña **Panel** del portal de administración.

![Pestaña Panel del portal de administración][]

Puede cambiar la configuración del sitio en la pestaña **Configure**. Para obtener más información, consulte [Administración de sitios web][].

### Escalación de un sitio web

Si el sitio es público y empieza a tener más tráfico, es posible que los tiempos de respuesta se alarguen. Para solucionarlo, puede agregar fácilmente recursos de servidores en la pestaña **Escalar** del portal de administración.

![Pestaña Escalar del Portal de administración][]

Para obtener más información, consulte [Escalación de un sitio web][]. Agregar recursos de servidor para escalar un sitio web no es gratuito.

### Solución de problemas de un sitio web

Es posible que desee consultar los resultados de seguimiento o los registros para obtener ayuda con la solución de problemas. Visual Studio ofrece herramientas integradas que facilitan la visualización de los registros de Azure cuando se generan, en tiempo real.

![Registros en Visual Studio][]

Para obtener más información, consulte [Solución de problemas de Sitios web Azure en Visual Studio][].

### Incorporación de funcionalidad de base de datos y autorización

La mayoría de los sitios web de producción utilizan una base de datos y restringen algunas funciones del sitio a usuarios autorizados. En el siguiente vínculo encontrará un tutorial introductorio acerca del acceso a base de datos, la autenticación y la autorización: [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure][].

### Decidir si la aplicación debe ejecutarse en un Servicio en la nube

En algunos escenarios, es posible que desee ejecutar su aplicación en un servicio en la nube de Azure en lugar de un sitio web de Azure. Para obtener más información, consulte [Modelos de ejecución de Azure][] y [Comparación entre Sitios web, Servicios en la nube y Máquinas virtuales de Azure][]. Si desea ver una serie de tutoriales que muestran cómo crear una aplicación web ASP.NET de niveles múltiples e implementarla en un Servicio en la nube, consulte [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento][].

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [versión más reciente de este tutorial]: /en-us/develop/net/tutorials/get-started/
  [Ejemplo de sitio web]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
  [activar los beneficios de suscripción a MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [regístrese para obtener una evaluación gratuita]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Configuración del entorno de desarrollo]: #setupdevenv
  [Creación de un sitio web en Azure]: #setupwindowsazure
  [Creación de una aplicación ASP.NET MVC 4]: #createmvc4app
  [Implementación de la aplicación en Azure]: #deploytowindowsazure
  [Pasos siguientes]: #nextsteps
  [install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Sitio web nuevo]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
  [Creación rápida]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
  [Creación de un sitio web nuevo]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
  [Página Sitios web del Portal de administración, sitio web creado]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
  [Nuevo proyecto en el menú Archivo]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
  [Cuadro de diálogo Nuevo proyecto]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
  [Cuadro de diálogo de nuevo proyecto de ASP.NET MVC 4]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
  [Sitio web ejecutándose de forma local]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
  [Opción Publicar del menú contextual del proyecto]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
  [Importar configuración de publicación]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
  [Agregar suscripción de Azure]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
  [Descargar archivo de suscripción]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
  [Descargar archivo .publishsettings]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
  [Descarga de sub]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
  [Importación]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
  [Importar perfil de publicación]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
  [Validar conexión]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
  [Conexión validada correctamente]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
  [Pestaña Settings]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
  [Botón StartPreview de la pestaña Preview ]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
  [Salida de archivo StartPreview]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
  [Ventana Resultado que informa de que la implementación se realizó correctamente]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
  [Sitios web de Azure en el Explorador de servidores]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
  [Configuración del sitio web en Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
  [Pestaña Panel del portal de administración]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
  [Administración de sitios web]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [Pestaña Escalar del Portal de administración]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
  [Escalación de un sitio web]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [Registros en Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
  [Solución de problemas de Sitios web Azure en Visual Studio]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Modelos de ejecución de Azure]: /en-us/develop/net/fundamentals/compute/
  [Comparación entre Sitios web, Servicios en la nube y Máquinas virtuales de Azure]: http://azure.microsoft.com/es-es/documentation/articles/choose-web-site-cloud-service-vm/
  [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
