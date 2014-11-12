<properties linkid="develop-net-tutorials-get-started-vs2013" pageTitle="Get started with Azure Websites for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Website. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Introducción a Sitios web Azure y ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/es-es/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

Este tutorial muestra cómo crear una aplicación web ASP.NET e implementarla en un sitio web de Azure utilizando Visual Studio 2013 o Visual Studio Express 2013 for Web. En este tutorial se asume que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una aplicación web sencilla ejecutándose en la nube.

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. De este modo, puede empezar a desarrollar contenido para Azure sin coste alguno.

Aprenderá a realizar los siguientes procedimientos:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Crear un proyecto web de Visual Studio ASP.NET e implementarlo en un sitio web de Azure.
-   Hacer cambios en el proyecto web y volver a implementar la aplicación.
-   Usar el Portal de administración de Azure para supervisar y administrar el sitio web.

La siguiente ilustración muestra la aplicación completada:

![Página principal del sitio web](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para completar este tutorial, deber&aacute; tener una cuenta de Azure:</h5>
  <ul>
    <li>Puede <a href="/es-es/pricing/free-trial/?WT.mc_id=A261C142F">abrir una cuenta de Azure de manera gratuita</a> - Obtiene cr&eacute;dito que puede utilizar para probar los servicios de Azure de pago, e incluso una vez agotado este podr&aacute; mantener la cuenta y utilizar servicios gratuitos de Azure, como Sitios web.</li>
    <li>Puede <a href="/es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">activar las ventajas de suscriptor de MSDN</a> - Su suscripci&oacute;n a MSDN le proporciona cr&eacute;dito todos los meses que puede utilizar para servicios de Azure de pago.</li>
  <ul>
</div>

### Secciones del tutorial

-   [Configuración del entorno de desarrollo][Configuración del entorno de desarrollo]
-   [Creación de una aplicación web ASP.NET en Visual Studio][Creación de una aplicación web ASP.NET en Visual Studio]
-   [Implementación de la aplicación en Azure][Implementación de la aplicación en Azure]
-   [Realización de cambios y nueva implementación][Realización de cambios y nueva implementación]
-   [Supervisión y administración del sitio en el Portal de administración][Supervisión y administración del sitio en el Portal de administración]
-   [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creación de una aplicación web ASP.NET

El primer paso es crear un proyecto de aplicación web. Visual Studio creará automáticamente el sitio web de Azure en el que implementará el proyecto más adelante. En el siguiente diagrama se muestra lo que sucede en estos dos pasos.

![Diagrama que muestra los pasos de creación e implementación de un proyecto](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1.  Abra Visual Studio 2013 o Visual Studio 2013 Express para Web.

2.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.

3.  En el cuadro de diálogo **Nuevo proyecto**, haga clic en **C\#** \> **Web** \> **Aplicación web ASP.NET**. Si lo prefiere, puede elegir **Visual Basic**.

4.  Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.

5.  Póngale a la aplicación el nombre **MyExample** y haga clic en **OK**.

    ![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**. Si prefiere trabajar con ASP.NET Web Forms, seleccione la plantilla **Web Forms**.

    [MVC y Web Forms][MVC y Web Forms] son marcos de ASP.NET para el desarrollo de sitios web. Para este tutorial, puede elegir cualquiera de los dos, pero, si elige Web Forms, deberá editar más adelante el archivo *Default.aspx* donde el tutorial indica que se edite el archivo *Index.cshtml*.

7.  Haga clic en **Cambiar autenticación**.

    ![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

8.  En el cuadro de diálogo **Change Authentication**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

    ![Sin autenticación][Sin autenticación]

    La aplicación de ejemplo que va a crear no permitirá que los usuarios inicien sesión. En la sección [Pasos siguientes][Pasos siguientes], encontrará vínculos a un tutorial en el que se implementa autenticación y autorización.

9.  En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, mantenga la configuración predeterminada de la sección **Azure** y haga clic en **Aceptar**.

    ![Cuadro de diálogo New ASP.NET Project][1]

    Con esta configuración se especifica que Visual Studio creará un sitio web de Azure para su proyecto web. En la siguiente sección del tutorial, implementará el proyecto web en el sitio web recién creado.

    El texto de la casilla puede ser **Host en la nube** o **Crear recursos remotos**. En cualquier caso, el resultado es el mismo.

10. Si no ha iniciado sesión todavía en Azure, Visual Studio le indicará que lo haga. Haga clic en **Sign In**.

    ![Inicio de sesión en Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

11. En el cuadro de diálogo **Iniciar sesión en Azure**, escriba el identificador y la contraseña de la cuenta que utiliza para administrar su suscripción a Azure.

    Cuando haya iniciado sesión, el cuadro de diálogo **Configure Azure Site Settings** le preguntará que recursos desea crear.

    ![Sesión iniciada en Azure][Sesión iniciada en Azure]

12. Visual Studio ofrece un **nombre de sitio** predeterminado, que Azure utilizará como prefijo para la URL de su aplicación. Si lo prefiere, escriba un nombre de sitio diferente.

    La dirección URL completa consta de lo que escriba aquí más *.azurewebsites.net* (como se muestra junto al cuadro de texto **Nombre del sitio**). Por ejemplo, si el nombre de sitio es `MyExample6442`, la URL será `MyExample6442.azurewebsites.net`. La dirección URL debe ser única. Si elige una dirección URL que ya utiliza otro usuario, aparecerá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y deberá proporcionar un nombre de sitio diferente.

13. En la lista desplegable **Region**, elija la ubicación más cercana.

    Este valor especifica el centro de datos de Azure en el que se ejecutará el sitio web. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, pero, para un sitio de producción, lo normal es que quiera tener el servidor web lo más cercano posible a los exploradores que accederán al sitio para minimizar la [latencia][latencia].

14. No modifique los campos de la base de datos,

    ya que en este tutorial no se utiliza una base de datos. En la sección [Pasos siguientes][Pasos siguientes] al final de este documento encontrará un enlace a un tutorial que muestra cómo utilizar una base de datos.

15. Haga clic en **OK**.

    En pocos segundos, Visual Studio crea el proyecto web en la carpeta especificada y crea el sitio web en la región de Azure especificada.

    La ventana del **Explorador de soluciones** muestra los archivos y las carpetas del nuevo proyecto.

    ![Explorador de soluciones](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

    La ventana **Web Publish Activity** muestra que el sitio se ha creado.

    ![Sitio web creado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

    Y puede ver el sitio en el Explorador de soluciones.

    ![Sitio web creado](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)

## Implementación de la aplicación en Azure

1.  En la ventana **Web Publish Activity** haga clic en **Publish MyExample to this site now**.

    ![Sitio web creado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

    En unos segundos aparece el asistente para **publicación web**.

    La configuración que Visual Studio necesita para implementar el proyecto en Azure se ha guardado en un *perfil público*. El asistente permite revisar y cambiar esta configuración.

2.  En la pestaña **Connection** del asistente para **publicación web**, haga clic en **Validate Connection** para asegurarse de que Visual Studio pueda conectar con Azure para implementar el proyecto web.

    ![Validar conexión](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

    Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**.

3.  Haga clic en **Next**.

    ![Conexión validada correctamente](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

4.  En la pestaña **Settings**, haga clic en **Next**.

    ![Pestaña Settings](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

    Puede aceptar los valores predeterminados de **Configuración** y **Opciones de publicación de archivos**.

    La lista desplegable **Configuración** permite implementar una compilación para depuración en modo remoto. En la sección [Pasos siguientes][Pasos siguientes], encontrará vínculos a un tutorial que muestra cómo ejecutar Visual Studio en modo de depuración de forma remota.

    Si expande **Opciones de publicación de archivos**, se muestran varios valores de configuración que permiten controlar escenarios no aplicables a este tutorial:

    -   Quitar archivos adicionales en destino.

        Elimina los archivos del servidor que no están en el proyecto. Esto puede ser necesario si implementa un proyecto en un sitio en el que había implementado antes otro proyecto diferente.

    -   Precompilar durante la publicación.

        Puede reducir el tiempo de preparación en la primera solicitud para sitios de gran envergadura.

    -   Excluir archivos de la carpeta App_Data.

        Con fines de prueba, puede tener a veces un archivo de base de datos de SQL Server en App\_Data que luego no quiera implementar en el entorno de producción.

5.  En la pestaña **Preview**, haga clic en **Start Preview**.

    ![Botón StartPreview de la pestaña Preview ](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

    En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer.

6.  Haga clic en **Publicar**.

    ![Salida de archivo StartPreview](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

    Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.

    Las ventanas **Output** y **Web Publish Activity** muestran qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

    ![Ventana Resultado que informa de que la implementación se realizó correctamente](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

    Una vez que la implementación se ha realizado correctamente, el explorador predeterminado abre automáticamente la dirección URL del sitio web implementado
    y la aplicación que ha creado se está ejecutando ya en la nube. La URL de la barra de direcciones del explorador muestra que el sitio se está cargando desde Internet.

    ![Sitio web ejecutándose en Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

7.  Cierre el explorador.

## Realización de cambios y nueva implementación

En esta sección del tutorial, va a cambiar el encabezado **h1** de la página principal, ejecutará el proyecto en modo local en su equipo de desarrollo para comprobar el cambio y luego implementará el cambio en Azure.

1.  Abra el archivo *Views/Home/Index.cshtml* o *.vbhtml* en el **Explorador de soluciones**, cambie el encabezado **h1** de "ASP.NET" a "ASP.NET and Azure" y guarde el archivo.

    ![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

    ![cambio en h1 de MVC](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

2.  Presione CTRL + F5 para ejecutar el sitio en su equipo local y ver el encabezado actualizado.

    ![Sitio web ejecutándose de forma local](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

    La dirección URL `http://localhost` muestra que se está ejecutando en su equipo local. De manera predeterminada, se ejecuta en IIS Express, que es una versión ligera de IIS diseñada para el desarrollo de aplicaciones web.

3.  Cierre el explorador.

4.  En el **Explorador de soluciones**, haga clic con el botón secundario y seleccione **Publicar**.

    ![Seleccionar Publicar](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

    En unos segundos aparece el asistente para **publicación web**. Si tuviera que cambiar alguna configuración de publicación podría elegir otra pestaña; sin embargo, ahora todo lo que desea es repetir la implementación con la misma configuración.

5.  En el asistente para **publicación web**, haga clic en **Publicar**.

    ![Haga clic en Publicar.](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

    Visual Studio implementa el proyecto en Azure y abre el sitio en el explorador predeterminado.

    ![Implementación del sitio con cambios](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Sugerencia:** Habilite la barra de herramientas **Publicación web con un clic** para agilizar aún más la implementación. Haga clic en **Ver** \> **Barras de herramientas** y seleccione **Publicación web con un clic**. Esta barra de herramientas permite seleccionar un perfil, hacer clic en un botón para publicar o para abrir el asistente **Publicación web**.

![Barra de herramientas Publicación en Web con un solo clic](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Supervisión y administración del sitio en el Portal de administración

El [Portal de administración de Azure][Portal de administración de Azure] es una interfaz web que puede utilizar para administrar y supervisar todos los servicios de Azure, como el sitio web que acaba de crear. En esta sección del tutorial podrá ver algo de lo que puede hacer en el portal.

1.  En el explorador, vaya a [http://manage.windowsazure.com]() e inicie sesión con sus credenciales de Azure.

    El portal muestra una lista de los servicios de Azure.

2.  Haga clic en el nombre del sitio web.

    ![Página principal del portal con el nuevo sitio web destacado](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)

3.  Haga clic en la pestaña **Panel**.

    La pestaña **Panel** muestra información general sobre las estadísticas de uso y vínculos a una serie de funciones de administración de sitios habituales. En **Vista rápida**, encontrará un vínculo a la página principal de su aplicación.

    ![Pestaña Panel del sitio web en el portal](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)

    En este momento, el sitio no ha tenido mucho tráfico aún y no puede mostrar nada en el gráfico. Si explora la aplicación, actualiza la página varias veces y después actualiza la página **Panel** del portal, verá que aparecen algunas estadísticas. Haga clic en la pestaña **Supervisar** para obtener más información.

4.  Haga clic en la pestaña **Configure**.

    La pestaña [Configurar][Configurar] permite controlar la versión de .NET Framework que se utiliza para el sitio, habilitar características como [WebSockets][WebSockets] y [registros de diagnóstico][registros de diagnóstico], establecer [valores de cadena de conexión][valores de cadena de conexión] y mucho más.

    ![Pestaña Configurar del sitio web en el portal](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)

5.  Haga clic en la pestaña **Scale**.

    Para los niveles de pago del servicio Sitios web, la pestaña [Escalar][Escalar] permite controlar el tamaño y el número de máquinas que sustentan su aplicación web para controlar las variaciones del tráfico.

    Puede escalar manualmente o configurar programaciones para escalado automático.

    ![Pestaña Escalar del sitio web en el portal](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

Estas son solo algunas de las características del portal de administración. También puede crear sitios web nuevos, eliminar otros existentes, detener y reiniciar sitios, y administrar otros tipos de servicios de Azure, como bases de datos y máquinas virtuales.

**Sugerencia:** Hay un nuevo portal de administración en vista previa que finalmente reemplazará al que está usando ahora. Para obtener más información, vea [Portal de Azure en vista previa][Portal de Azure en vista previa].

## Pasos siguientes

En este tutorial, ha aprendido a crear una aplicación web sencilla e implementarla en un sitio web de Azure. Estos son algunos temas y recursos relacionados que le permitirán obtener más información al respecto:

-   Otros métodos de implementar un proyecto web

    En este tutorial hemos visto el método más rápido de crear un sitio e implementarlo en una sola operación. Para obtener información general sobre otras formas de implementación, ya sea con Visual Studio o con [automatización de la implementación][automatización de la implementación] desde un [sistema de control de código fuente][sistema de control de código fuente], consulte [Implementación de un sitio web de Azure][Implementación de un sitio web de Azure].

    Visual Studio puede generar también scripts de Windows PowerShell que permiten automatizar la implementación. Para obtener más información, consulte [Automate Everything (Building Real-World Cloud Apps with Azure)][Automate Everything (Building Real-World Cloud Apps with Azure)].

-   Administración de un sitio web en Visual Studio

    Para obtener información acerca de las funciones de administración de sitios que puede usar en el **Explorador de servidores**, consulte [Solución de problemas de Sitios web Azure en Visual Studio][Solución de problemas de Sitios web Azure en Visual Studio].

-   Solución de problemas de un sitio web

    Visual Studio proporciona características que facilitan la visualización de los registros de Azure conforme se generan, en tiempo real. También puede ejecutar el sitio en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de Sitios web Azure en Visual Studio][Solución de problemas de Sitios web Azure en Visual Studio].

-   Incorporación de funcionalidad de base de datos y autorización

    Para hacer un tutorial que muestra cómo acceder a una base de datos y restringir algunas funciones del sitio a usuarios autorizados, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure][Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure].

-   Incorporación de un nombre de dominio personalizado y SSL

    Para obtener información sobre el uso de SSL y su propio dominio (por ejemplo, www.contoso.com en lugar de contoso.azurewebsites.net), consulte los recursos siguientes:

    -   [Configuración de un nombre de dominio personalizado para un sitio web de Azure][Configuración de un nombre de dominio personalizado para un sitio web de Azure].
    -   [Habilitación de HTTPS en un sitio web de Azure][Habilitación de HTTPS en un sitio web de Azure]

-   Cómo evitar el tiempo de reactivación después de períodos de inactividad

    De forma predeterminada, los sitios web se descargan si han estado inactivos durante algún tiempo. La primera solicitud después de este tiempo de inactividad tiene que esperar para que el sitio se cargue de nuevo. Para evitar el tiempo de espera, puede habilitar la característica AlwaysOn. Para obtener más información, consulte las opciones de configuración en [Configuración de sitios web][Configuración de sitios web].

-   Cómo agregar características en tiempo real (por ejemplo, chat)

    Si el sitio web incluirá características en tiempo real (como un servicio de chat, un juego, un tablero de cotizaciones, etc.), puede optimizar el rendimiento usando [ASP.NET SignalR][ASP.NET SignalR] con el método de transporte [WebSockets][WebSockets]. Para obtener más información, consulte [Uso de SignalR con sitios web de Windows Azure][Uso de SignalR con sitios web de Windows Azure].

-   Selección entre Sitios web, Servicios en la nube y Máquinas virtuales de Azure para aplicaciones web

    En Azure puede ejecutar aplicaciones web en Sitios web, como se muestra en este tutorial, o bien en Servicios en la nube o Máquinas virtuales. Para obtener más información, consulte [Modelos de ejecución de Azure][Modelos de ejecución de Azure] y [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno][Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno].

  [Visual Studio 2013]: /es-es/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /es-es/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [Página principal del sitio web]: ./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png
  [abrir una cuenta de Azure de manera gratuita]: /es-es/pricing/free-trial/?WT.mc_id=A261C142F
  [activar las ventajas de suscriptor de MSDN]: /es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [Configuración del entorno de desarrollo]: #set-up-the-development-environment
  [Creación de una aplicación web ASP.NET en Visual Studio]: #create-an-aspnet-web-application
  [Implementación de la aplicación en Azure]: #deploy-the-application-to-azure
  [Realización de cambios y nueva implementación]: #make-a-change-and-redeploy
  [Supervisión y administración del sitio en el Portal de administración]: #monitor-and-manage-the-site-in-the-management-portal
  [Pasos siguientes]: #next-steps
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Diagrama que muestra los pasos de creación e implementación de un proyecto]: ./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png
  [Cuadro de diálogo Nuevo proyecto]: ./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png
  [MVC y Web Forms]: http://www.asp.net/get-started/websites
  [Cuadro de diálogo New ASP.NET Project]: ./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png
  [Sin autenticación]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [1]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [Inicio de sesión en Azure]: ./media/web-sites-dotnet-get-started-vs2013/signin.png
  [Sesión iniciada en Azure]: ./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png
  [latencia]: http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090
  [Explorador de soluciones]: ./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png
  [Sitio web creado]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png
  [2]: ./media/web-sites-dotnet-get-started-vs2013/siteinse.png
  [3]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png
  [Validar conexión]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png
  [Conexión validada correctamente]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png
  [Pestaña Settings]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [Botón StartPreview de la pestaña Preview ]: ./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png
  [Salida de archivo StartPreview]: ./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png
  [Ventana Resultado que informa de que la implementación se realizó correctamente]: ./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png
  [Sitio web ejecutándose en Azure]: ./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png
  [MVC index.cshtml]: ./media/web-sites-dotnet-get-started-vs2013/index.png
  [cambio en h1 de MVC]: ./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png
  [Sitio web ejecutándose de forma local]: ./media/web-sites-dotnet-get-started-vs2013/localandazure.png
  [Seleccionar Publicar]: ./media/web-sites-dotnet-get-started-vs2013/choosepublish.png
  [Haga clic en Publicar.]: ./media/web-sites-dotnet-get-started-vs2013/clickpublish.png
  [Barra de herramientas Publicación en Web con un solo clic]: ./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png
  [Portal de administración de Azure]: /es-es/services/management-portal/ 
  [Página principal del portal con el nuevo sitio web destacado]: ./media/web-sites-dotnet-get-started-vs2013/portalhome.png
  [Pestaña Panel del sitio web en el portal]: ./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png
  [Configurar]: /es-es/documentation/articles/web-sites-configure//
  [WebSockets]: /blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/
  [registros de diagnóstico]: /es-es/documentation/articles/web-sites-enable-diagnostic-log/
  [valores de cadena de conexión]: /blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Pestaña Configurar del sitio web en el portal]: ./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png
  [Escalar]: /es-es/documentation/articles/web-sites-scale/
  [Pestaña Escalar del sitio web en el portal]: ./media/web-sites-dotnet-get-started-vs2013/portalscale.png
  [Portal de Azure en vista previa]: /es-es/overview/preview-portal/
  [automatización de la implementación]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [sistema de control de código fuente]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Implementación de un sitio web de Azure]: /es-es/documentation/articles/web-sites-deploy/"
  [Automate Everything (Building Real-World Cloud Apps with Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Solución de problemas de Sitios web Azure en Visual Studio]: /es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure]: /es-es/develop/net/tutorials/web-site-with-sql-database/
  [Configuración de un nombre de dominio personalizado para un sitio web de Azure]: /es-es/documentation/articles/web-sites-custom-domain-name/
  [Habilitación de HTTPS en un sitio web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure-ssl-certificate/
  [Configuración de sitios web]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [Uso de SignalR con sitios web de Windows Azure]: http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites
  [Modelos de ejecución de Azure]: /es-es/develop/net/fundamentals/compute/
  [Sitios web, Servicios en la nube y Máquinas virtuales de Azure: cuándo usar cada uno]: /es-es/manage/services/web-sites/choose-web-app-service/
