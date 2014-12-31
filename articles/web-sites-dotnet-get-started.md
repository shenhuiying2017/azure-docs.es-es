<properties pageTitle="Introducción a Sitios web Azure para ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Website. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/12/2014" ms.author="tdykstra" />

# Introducción a Sitios web Azure y ASP.NET

Este tutorial muestra cómo crear una aplicación web ASP.NET e implementarla en un sitio web de Azure utilizando Visual Studio 2013 o Visual Studio Express 2013 for Web. En este tutorial se supone que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

Aprenderá a realizar los siguientes procedimientos:

* Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
* Crear un proyecto web de Visual Studio ASP.NET e implementarlo en un sitio web de Azure.
* Hacer cambios en el proyecto web y volver a implementar la aplicación.
* Usar el Portal de administración de Azure para supervisar y administrar el sitio web.

Para completar este tutorial, deberá tener una cuenta de Azure:

* Puede [abrir una cuenta de Azure de forma gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F) - Obtiene crédito que puede utilizar para probar los servicios de Azure de pago, e incluso una vez agotado este podrá mantener la cuenta y utilizar servicios gratuitos de Azure, como Sitios web.
* Puede [activar las ventajas de suscriptor de MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Su suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.</li>

> [WACOM.NOTE]
> Si desea obtener una introducción a Sitios web Azure antes de suscribirse a una cuenta, visite <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente un sitio básico de ASP.NET de corta duración en Sitios web Azure.

La siguiente ilustración muestra la aplicación completada:

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### Secciones del tutorial

* [Inicio de sesión en Microsoft Azure (vídeo)](#video)
* [Configuración del entorno de desarrollo](#set-up-the-development-environment)
* [Creación de una aplicación web ASP.NET en Visual Studio](#create-an-aspnet-web-application)
* [Implementación de la aplicación en Azure](#deploy-the-application-to-azure)
* [Realización de cambios y nueva implementación](#make-a-change-and-redeploy)
* [Supervisión y administración del sitio en el Portal de administración](#monitor-and-manage-the-site-in-the-management-portal)
* [Pasos siguientes](#next-steps)

##<a name="video"></a>Inicio de sesión en Microsoft Azure (vídeo)

En este vídeo, Scott Hanselman presenta lo sencillo que resulta iniciar sesión en una prueba gratuita de Microsoft Azure. (Duración: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creación de una aplicación web ASP.NET

El primer paso es crear un proyecto de aplicación web. Visual Studio creará automáticamente el sitio web de Azure en el que implementará el proyecto más adelante. En el siguiente diagrama se muestra lo que sucede en estos dos pasos.

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. Abra Visual Studio 2013 o Visual Studio 2013 Express para Web.

2. En el menú **Archivo**, haga clic en **Nuevo proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **C#** > **Web** > **Aplicación web ASP.NET**. Si lo prefiere, puede elegir **Visual Basic**.

3. Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.

4. Póngale a la aplicación el nombre **MyExample** y haga clic en **Aceptar**.

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**. Si prefiere trabajar con ASP.NET Web Forms, seleccione la plantilla **Web Forms**. 

	[MVC y Web Forms](http://www.asp.net/get-started/websites) son marcos ASP.NET para desarrollar sitios web. Para este tutorial, puede elegir cualquiera de los dos, pero, si elige Web Forms, deberá editar más adelante el archivo *Default.aspx* donde el tutorial indica que se edite el archivo *Index.cshtml*.

7. Haga clic en **Cambiar autenticación**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. En el cuadro de diálogo **Cambiar autenticación**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	La aplicación de ejemplo que va a crear no permitirá a los usuarios iniciar sesión. La sección [Pasos siguientes](#next-steps) vincula a un tutorial que implementa la autenticación y autorización.

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, no altere la configuración de **Azure** y, a continuación, haga clic en **Aceptar**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	Con esta configuración se especifica que Visual Studio creará un sitio web de Azure para su proyecto web. En la siguiente sección del tutorial, implementará el proyecto web en el sitio web recién creado.

	(Es posible que esta casilla lleve el nombre **Host en la nube** o **Crear recursos remotos**. En cualquier caso, el resultado es el mismo.
	
5. Si no ha iniciado sesión todavía en Azure, Visual Studio le indicará que lo haga. Haga clic en **Iniciar sesión**.

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. En el cuadro de diálogo **Iniciar sesión en Azure**, escriba el identificador y la contraseña de la cuenta que utiliza para administrar su suscripción a Azure.
	
	Cuando haya iniciado sesión, el cuadro de diálogo **Configurar sitio de Azure** le preguntará qué recursos desea crear.

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio ofrece un **nombre de sitio** predeterminado, que Azure utilizará como prefijo para la URL de su aplicación. Si lo prefiere, escriba un nombre de sitio diferente.

	La dirección URL completa consta de lo que escriba aquí más .azurewebsites.net (como se muestra junto al cuadro de texto **Nombre del sitio**). Por ejemplo, si el nombre de sitio es "MyExample6442", la URL será "MyExample6442.azurewebsites.net". La dirección URL debe ser única. Si elige una dirección URL que ya utiliza otro usuario, aparecerá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y deberá proporcionar un nombre de sitio diferente.

4. En la lista desplegable **Región**, elija la ubicación más cercana.

	Este valor especifica el centro de datos de Azure en el que se ejecutará el sitio web. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, pero, para un sitio de producción, lo normal es que quiera tener el servidor web lo más cercano posible a los exploradores que accederán al sitio para minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. No modifique los campos de la base de datos,

	ya que en este tutorial no se utiliza una base de datos. En la sección [Pasos siguientes](#next-steps) al final de este documento encontrará un enlace a un tutorial que muestra cómo utilizar una base de datos.

6. Haga clic en **Aceptar**.

	En pocos segundos, Visual Studio crea el proyecto web en la carpeta especificada y crea el sitio web en la región de Azure especificada.  

	La ventana del **Explorador de soluciones** muestra los archivos y las carpetas del nuevo proyecto.

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	La ventana **Actividad de publicación web** muestra que el sitio se ha creado.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	Y puede ver el sitio en el Explorador de soluciones.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Implementación de la aplicación en Azure

7. En la ventana **Actividad de publicación web**, haga clic en **Publicar MyExample en este sitio ahora**.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	En unos segundos aparece el asistente para **publicación web **. 

	La configuración que Visual Studio necesita para implementar el proyecto en Azure se ha guardado en un *perfil público*. El asistente permite revisar y cambiar esta configuración.

8. En la pestaña **Conexión** del asistente para **publicación web**, haga clic en **Validar conexión** para asegurarse de que Visual Studio pueda conectar con Azure para implementar el proyecto web.

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validar conexión**. 

9. Haga clic en **Siguiente**.

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. En la pestaña **Configuración**, haga clic en **Siguiente**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Puede aceptar los valores predeterminados de **Configuración** y **Opciones de publicación de archivos**.

	La lista desplegable **Configuración** permite implementar una compilación para depuración en modo remoto. En la sección [Pasos siguientes](#next-steps) , encontrará vínculos a un tutorial que muestra cómo ejecutar Visual Studio en modo de depuración de forma remota.

	Si expande **Opciones de publicación de archivos**, se muestran varios valores de configuración que permiten controlar escenarios no aplicables a este tutorial:
 
	* Quitar archivos adicionales en destino.
	  
		Elimina los archivos del servidor que no están en el proyecto. Esto puede ser necesario si implementa un proyecto en un sitio en el que había implementado antes otro proyecto diferente.

	* Precompilar durante la publicación. 
	 
		Puede reducir el tiempo de preparación en la primera solicitud para sitios de gran envergadura.

	* Excluir archivos de la carpeta App_Data. 
	 
		Con fines de prueba, puede tener a veces un archivo de base de datos de SQL Server en App_Data que luego no quiera implementar en el entorno de producción.
	
11. En la pestaña **Vista previa**, haga clic en **Iniciar vista previa**.

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer.

12. Haga clic en **Publicar**.

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.

	Las ventanas **Salida** y **Actividad de publicación web** muestran qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Una vez que la implementación se ha realizado correctamente, el explorador predeterminado abre automáticamente la dirección URL del sitio web implementado y la aplicación que ha creado se está ejecutando ya en la nube. La URL de la barra de direcciones del explorador muestra que el sitio se está cargando desde Internet.

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Cierre el explorador.

## Realización de cambios y nueva implementación

En esta sección del tutorial, va a cambiar el encabezado **h1** de la página principal, ejecutará el proyecto en modo local en su equipo de desarrollo para comprobar el cambio y luego implementará el cambio en Azure.

2. Abra el archivo *Views/Home/Index.cshtml* o *.vbhtml* en el **Explorador de soluciones**, cambie el encabezado **h1** de "ASP.NET" a "ASP.NET and Azure" y guarde el archivo. 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Presione CTRL + F5 para ejecutar el sitio en su equipo local y ver el encabezado actualizado.

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	La URL "http://localhost" indica que se está ejecutando en el equipo local. De manera predeterminada, se ejecuta en IIS Express, que es una versión ligera de IIS diseñada para el desarrollo de aplicaciones web.


1. Cierre el explorador.

1. En el **Explorador de soluciones**, haga clic con el botón secundario y seleccione **Publicar**.

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	En unos segundos aparece el asistente para **publicación web**. Si tuviera que cambiar alguna configuración de publicación podría elegir otra pestaña; sin embargo, ahora todo lo que desea es repetir la implementación con la misma configuración.

2. En el asistente para **publicación web**, haga clic en **Publicar**.

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Visual Studio implementa el proyecto en Azure y abre el sitio en el explorador predeterminado.

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Sugerencia:** Habilite la barra de herramientas  **Publicación web con un clic** para agilizar aún más la implementación. Haga clic en **Ver** > **Barras de herramientas** y, a continuación, seleccione **Publicación web con un clic**. Esta barra de herramientas permite seleccionar un perfil, hacer clic en un botón para publicar o para abrir el asistente **Publicación web**. 

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Supervisión y administración del sitio en el Portal de administración

El [Portal de administración de Azure](/en-us/services/management-portal/) es una interfaz web que puede utilizar para administrar y supervisar todos los servicios de Azure, como el sitio web que acaba de crear. En esta sección del tutorial podrá ver algo de lo que puede hacer en el portal.

1. En el explorador, vaya a [http://manage.windowsazure.com]() e inicie sesión con sus credenciales de Azure.

	El portal muestra una lista de los servicios de Azure.

2. Haga clic en el nombre del sitio web.

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. Haga clic en la pestaña **Panel**.

	La pestaña **Panel** muestra información general sobre las estadísticas de uso y vínculos a una serie de funciones de administración de sitios habituales. En **Vista rápida**, encontrará un vínculo a la página principal de su aplicación.

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	En este momento, el sitio no ha tenido mucho tráfico aún y no puede mostrar nada en el gráfico. Si explora la aplicación, actualiza la página varias veces y después actualiza la página **Panel** del portal, verá que aparecen algunas estadísticas. Haga clic en la pestaña **Supervisar** para obtener más información.

4. Haga clic en la pestaña **Configurar**.

	La pestaña [Configurar](/en-us/documentation/articles/web-sites-configure//) le permite controlar la versión de .NET utilizada para el sitio, habilitar características como [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) y [registro de diagnósticos](/en-us/documentation/articles/web-sites-enable-diagnostic-log/), definir los [valores de la cadena de conexión](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)y mucho más. 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. Haga clic en la pestaña **Escalar**.

	Para los niveles de pago del servicio Sitios web, la pestaña [Escalar](/en-us/documentation/articles/web-sites-scale/) permite controlar el tamaño y el número de máquinas que sustentan su aplicación web para controlar las variaciones del tráfico.

	Puede escalar manualmente o configurar programaciones para escalado automático.

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

Estas son solo algunas de las características del portal de administración. También puede crear sitios web nuevos, eliminar otros existentes, detener y reiniciar sitios, y administrar otros tipos de servicios de Azure, como bases de datos y máquinas virtuales.  

**Sugerencia:** Hay un nuevo portal de administración en vista previa que finalmente reemplazará al que está usando ahora. Para obtener más información, consulte [Portal de vista previa de Azure](/en-us/overview/preview-portal/).

## Pasos siguientes

En este tutorial, ha aprendido a crear una aplicación web sencilla e implementarla en un sitio web de Azure. Estos son algunos temas y recursos relacionados que le permitirán obtener más información al respecto:

* Otros métodos de implementar un proyecto web

	En este tutorial hemos visto el método más rápido de crear un sitio e implementarlo en una sola operación. Para obtener información general sobre las formas de realizar implementaciones, mediante Visual Studio o con la [implementación automática](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Implementación de un sitio web de Azure](/en-us/documentation/articles/web-sites-deploy/"). 

	Visual Studio can also generate Windows PowerShell scripts that enable you to automate deployment. For more information, see [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* How to manage a website in Visual Studio

	For information about site management functions that you can do in **Server Explorer**, see [Troubleshooting Azure Websites in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Solución de problemas de un sitio web

	Visual Studio proporciona características que facilitan la visualización de los registros de Azure conforme se generan, en tiempo real. También puede ejecutar el sitio en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Incorporación de funcionalidad de base de datos y autorización

	Para hacer un tutorial que muestra cómo acceder a una base de datos y restringir algunas funciones del sitio a usuarios autorizados, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).

* Incorporación de un nombre de dominio personalizado y SSL

	Para obtener información sobre el uso de SSL y su propio dominio (por ejemplo, www.contoso.com en lugar de contoso.azurewebsites.net), consulte los recursos siguientes:

	* [Configuración de un nombre de dominio personalizado para un sitio web de Azure](/en-us/documentation/articles/web-sites-custom-domain-name/). 
	* [Habilitación de HTTPS en un sitio web de Azure](http://azure.microsoft.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/)

* Cómo evitar el tiempo de reactivación después de períodos de inactividad 

	De forma predeterminada, los sitios web se descargan si han estado inactivos durante algún tiempo. La primera solicitud después de este tiempo de inactividad tiene que esperar para que el sitio se cargue de nuevo. Para evitar el tiempo de espera, puede habilitar la característica AlwaysOn. Para obtener más información, consulte las opciones de configuración en [Configuración de sitios web](http://azure.microsoft.com/en-us/documentation/articles/web-sites-configure/).

* Cómo agregar características en tiempo real (por ejemplo, chat)

	Si el sitio web incluirá características en tiempo real (como un servicio de chat, un juego, un tablero de cotizaciones, etc.), puede optimizar el rendimiento usando [ASP.NET SignalR](http://www.asp.net/signalr) con el método de transporte [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) . Para obtener más información, consulte [Uso de SignalR con sitios web de Windows Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites). 

* Selección entre Sitios web, Servicios en la nube y Máquinas virtuales de Azure para aplicaciones web

	En Azure puede ejecutar aplicaciones web en Sitios web, como se muestra en este tutorial, o bien en Servicios en la nube o Máquinas virtuales. Para obtener más información, consulte [Modelos de ejecución de Azure](/en-us/develop/net/fundamentals/compute/) y [Sitios web Azure, Servicios en la nube y Máquinas virtuales: cuándo usar cada uno](/en-us/manage/services/web-sites/choose-web-app-service/).

<!--HONumber=35_1-->
