<properties 
	pageTitle="Crear una aplicación web de ASP.NET en el servicio de aplicaciones de Azure" 
	description="En este tutorial se muestra cómo se crea un proyecto web de ASP.NET en Visual Studio 2013 y se implementa en una aplicación web en el servicio de aplicaciones de Azure. En menos de 15 minutos tendrá una aplicación lista y en ejecución en la nube." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Crear una aplicación web de ASP.NET en el servicio de aplicaciones de Azure

## Información general

Este tutorial muestra cómo crear una aplicación web ASP.NET e implementarla en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) utilizando Visual Studio 2013 o Visual Studio Express 2013 for Web. En este tutorial se supone que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

Aprenderá a realizar los siguientes procedimientos:

* Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure.
* Crear un proyecto web Visual Studio ASP.NET e implementarlo en una aplicación web de Azure.
* Hacer cambios en el proyecto web y volver a implementar la aplicación.
* Cómo utilizar el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para supervisar y administrar su aplicación web.

Para completar este tutorial, deberá tener una cuenta de Azure:

* Puede [abrir una cuenta de Azure de manera gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) - Obtiene crédito que puede utilizar para probar los servicios de Azure de pago, e incluso una vez agotado este podrá mantener la cuenta y utilizar servicios gratuitos de Azure, como Aplicaciones web del Servicio de aplicaciones.
* Puede [activar las ventajas de suscriptor de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Su suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.</li>

La siguiente ilustración muestra la aplicación completada:

![Página de inicio de la aplicación web](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

##<a name="video"></a>Inicio de sesión en Microsoft Azure (vídeo)

En este vídeo, Scott Hanselman muestra lo sencillo que resulta registrarse para obtener una prueba gratuita de Microsoft Azure. (Duración: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creación de una aplicación web ASP.NET

El primer paso es crear un proyecto de aplicación web. Visual Studio creará automáticamente la aplicación web de Azure en la que implementará el proyecto más adelante. 

<!-- por hacer: restablecer el diagrama después de cambiar el sitio web a la aplicación web en los títulos
En el siguiente diagrama se muestra lo que sucede en estos dos pasos.
>>>>>>> 67f4fc9fc261d3288cdba263d3236b9617b6a7df

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)
-->

1. Abra Visual Studio 2013 o Visual Studio 2013 Express para Web.

2. En el menú **Archivo**, haga clic en **Nuevo proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **C#** > **Web** > **Aplicación web ASP.NET**. Si lo prefiere, puede elegir **Visual Basic**.

3. Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.

4. Póngale a la aplicación el nombre **MyExample** y haga clic en **OK**.

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**. Si prefiere trabajar con ASP.NET Web Forms, seleccione la plantilla **Web Forms**. 

	[MVC y formularios Web Forms](http://www.asp.net/get-started/websites) son marcos de ASP.NET para desarrollar aplicaciones web. Para este tutorial, puede elegir cualquiera de los dos, pero, si elige Web Forms, deberá editar más adelante el archivo  *Default.aspx* donde el tutorial indica que se edite el archivo  *Index.cshtml*.

7. Haga clic en **Cambiar autenticación**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. En el cuadro de diálogo **Change Authentication**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	La aplicación de ejemplo que va a crear no permitirá a los usuarios iniciar sesión. La sección [Pasos siguientes](#next-steps) vincula a un tutorial que implementa la autenticación y autorización.

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, mantenga la configuración predeterminada de la sección **Azure** y haga clic en **Aceptar**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	La configuración predeterminada especifica que Visual Studio creará una aplicación web de Azure para el proyecto web. En la siguiente sección del tutorial, se implementará el proyecto web en la aplicación web recién creada.

5. Si no ha iniciado sesión todavía en Azure, Visual Studio le indicará que lo haga. Inicie sesión con el identificador y la contraseña de la cuenta que usa para administrar la suscripción de Azure.
	
	Cuando haya iniciado sesión, el cuadro de diálogo **Configuración de aplicación web de Microsoft Azure** le preguntará los recursos que quiere crear.

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio proporciona un **nombre de aplicación web** predeterminado, que Azure usará como el prefijo de la dirección URL de la aplicación. Si lo prefiere, escriba un nombre de aplicación web distinto.

	La dirección URL completa consistirá en lo que escriba más *.azurewebsites.net* (como se muestra junto al cuadro de texto **Nombre de la aplicación web**). Por ejemplo, si el nombre es `MyExample6442`, the URL will be `MyExample6442.azurewebsites.net`. La dirección URL debe ser única. Si elige una dirección URL que ya utiliza otro usuario, aparecerá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y deberá proporcionar un nombre distinto.

4. En la lista desplegable **Region**, elija la ubicación más cercana.

	Este valor especifica el centro de datos de Azure en el que se ejecutará la aplicación web. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, pero, para una aplicación web del producción, lo normal es que quiera tener el servidor web lo más cercano posible a los exploradores que accederán al sitio para minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. No modifique los campos de la base de datos,

	ya que en este tutorial no se utiliza una base de datos. En la sección [Pasos siguientes](#next-steps) al final de este documento encontrará un enlace a un tutorial que muestra cómo utilizar una base de datos.

6. Haga clic en **OK**.

	En pocos segundos, Visual Studio crea el proyecto web en la carpeta especificada y crea la aplicación web en la región de Azure especificada.  

	La ventana del **Explorador de soluciones** muestra los archivos y las carpetas del nuevo proyecto.

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	La ventana **Actividad de publicación web** muestra que se ha creado la aplicación web.

	![Web app created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	Y puede ver la aplicación web en el Explorador de servidores.

	![Web app created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Implementación de la aplicación en Azure

7. En la ventana **Actividad de publicación web**, haga clic en **Publicar mi ejemplo en esta aplicación web ahora**.

	![Web app created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	En unos segundos aparece el asistente para **publicación web**. 

	La configuración que Visual Studio necesita para implementar el proyecto en Azure se ha guardado en un *publish profile*. El asistente permite revisar y cambiar esta configuración.

8. En la pestaña **Connection** del asistente para **publicación web**, haga clic en **Validate Connection** para asegurarse de que Visual Studio pueda conectar con Azure para implementar el proyecto web.

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Una vez que se valide la conexión, aparecerá una marca de verificación verde junto al botón **Validate Connection**. 

9. Haga clic en **Next**.

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. En la pestaña **Settings**, haga clic en **Next**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Puede aceptar los valores predeterminados de **Configuración** y **Opciones de publicación de archivos**.

	La lista desplegable **Configuración** permite implementar una compilación para depuración en modo remoto. En la sección [Pasos siguientes](#next-steps) , encontrará vínculos a un tutorial que muestra cómo ejecutar Visual Studio en modo de depuración de forma remota.

	Si expande **Opciones de publicación de archivos**, se muestran varios valores de configuración que permiten controlar escenarios no aplicables a este tutorial:
 
	* Quitar archivos adicionales en destino.
	  
		Elimina los archivos del servidor que no están en el proyecto. Esto puede ser necesario si implementa un proyecto en una aplicación web en la que había implementado antes otro proyecto diferente.

	* Precompilar durante la publicación. 
	 
		Puede reducir los tiempos de carga inicial de la primera solicitud de las aplicaciones de gran tamaño.

	* Excluir archivos de la carpeta App_Data. 
	 
		Con fines de prueba, puede tener a veces un archivo de base de datos de SQL Server en App_Data que luego no quiera implementar en el entorno de producción.
	
11. En la pestaña **Preview**, haga clic en **Start Preview**.

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	En esta pestaña aparece la lista de archivos que se copiarán en el servidor. Para publicar la aplicación no es necesario mostrar la vista previa, pero se trata de una función útil que conviene conocer.

12. Haga clic en **Publicar**.

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.

	Las ventanas **Output** y **Web Publish Activity** muestran qué acciones de implementación se realizaron e informa de la correcta finalización de la implementación.

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Tras la correcta finalización de la implementación, el explorador predeterminado se dirige automáticamente a la URL de la aplicación web implementada. La aplicación que ha creado se ejecuta ahora en la nube. La dirección URL en la barra de direcciones del explorador muestra que la aplicación web se está cargando desde Internet.

	![Web app running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Cierre el explorador.

## Realización de cambios y nueva implementación

En esta sección del tutorial, va a cambiar el encabezado **h1** de la página principal, ejecutará el proyecto en modo local en su equipo de desarrollo para comprobar el cambio y luego implementará el cambio en Azure.

2. Abra el archivo *Views/Home/Index.cshtml* o *.vbhtml* en el **Explorador de soluciones**, cambie el título **h1** de "ASP.NET" a "ASP.NET y Azure" y guarde el archivo. 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Presione CTRL+F5 para ver el título actualizado mediante la ejecución de la aplicación web en el equipo local.

	![Web app running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	La dirección URL `http://localhost` muestra que se está ejecutando en su equipo local. De manera predeterminada, se ejecuta en IIS Express, que es una versión ligera de IIS diseñada para el desarrollo de aplicaciones web.


1. Cierre el explorador.

1. En el **Explorador de soluciones**, haga clic con el botón secundario y seleccione **Publicar**.

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	En unos segundos aparece el asistente para **publicación web**. Si tuviera que cambiar alguna configuración de publicación podría elegir otra pestaña; sin embargo, ahora todo lo que desea es repetir la implementación con la misma configuración.

2. En el asistente para **publicación web**, haga clic en **Publicar**.

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Visual Studio implementa el proyecto en Azure y abre la aplicación web en el explorador predeterminado.

	![Changed web app deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Sugerencia:** puede habilitar la barra de herramientas **Publicación en Web con un solo clic** para una implementación aún más rápida. Haga clic en **Vista** > **Barras de herramientas** y, a continuación, seleccione **Publicación en Web con un solo clic**. La barra de herramientas le permite seleccionar un perfil, hacer clic en un botón para publicar o hacer clic en un botón para abrir el asistente de **Publicación web**.

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Supervisar y administrar la aplicación web en el portal de administración

El [Portal de administración de Azure](/services/management-portal/) es una interfaz web que puede utilizar para administrar y supervisar todos los servicios de Azure, como el sitio web que acaba de crear. En esta sección del tutorial podrá ver algo de lo que puede hacer en el portal.
  
1. En el explorador, vaya a [http://portal.azure.com]() e inicie sesión con sus credenciales de Azure.

2. Haga clic en **Examinar > Aplicaciones web** y, a continuación, haga clic en el nombre de la aplicación web.

	La hoja de **Aplicación web** de su aplicación web muestra una visión general de las estadísticas de uso y los vínculos de las funciones de administración de aplicaciones web de uso frecuente.
  
	![Web app blade](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)-->

	Por ahora, su aplicación web no habrá tenido mucho tráfico y puede que no se muestre nada en el gráfico. Si explora la aplicación, actualice la página varias veces y, después, actualice la página del portal; verá que aparecen algunas estadísticas.

3. Haga clic en **Todas las configuraciones** para ver más opciones para configurar la aplicación web.

	Verá una lista de tipos de configuración.
  
	![](./media/web-sites-dotnet-get-started-vs2013/portalconfigure1.png)-->

4. Haga clic en**Configuración de la aplicación**para ver un ejemplo de los tipos de configuraciones que se pueden establecer en el portal.

	Por ejemplo, puede controlar la versión de .NET que se usa para la aplicación web, habilitar características como [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/), establecer [Valores de cadena de conexión](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) y mucho más. 

	![Portal web app configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure2.png)

Estas son solo algunas de las características del portal de administración. Puede crear nuevas aplicaciones web, eliminar las existentes, detener y reiniciar aplicaciones web y administrar otros tipos de servicios de Azure, como bases de datos y máquinas virtuales.  

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web inicial de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Pasos siguientes

En este tutorial, ha aprendido a crear una aplicación web sencilla e implementarla en una aplicación web de Azure. Estos son algunos temas y recursos relacionados que le permitirán obtener más información al respecto.

* Otros métodos de implementar un proyecto web

	En este tutorial ha visto la manera más rápida para crear una aplicación web e implementarla en una sola operación. Para obtener información general acerca de otros métodos de implementación, ya sea mediante Visual Studio o mediante una [implementación automatizada (información en inglés)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde el [sistema de control de código fuente (información en inglés)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Implementación de una aplicación web de Azure](web-sites-deploy.md).. 

	Visual Studio puede generar también scripts de Windows PowerShell que permiten automatizar la implementación. Para obtener más información, consulte [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Administración de una aplicación web en Visual Studio

	Para obtener información sobre las funciones de administración de aplicaciones web que se puede usar en el **Explorador de servidores**, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](troubleshoot-web-sites-in-visual-studio.md).

* Solución de problemas de una aplicación web

	Visual Studio proporciona características que facilitan la visualización de los registros de Azure conforme se generan, en tiempo real. También puede ejecutar el sitio en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](troubleshoot-web-sites-in-visual-studio.md).

* Incorporación de funcionalidades de base de datos y autorización

	Para obtener un tutorial que muestre cómo acceder a una base de datos y restringir algunas funciones de aplicación a usuarios autorizados, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure](/develop/net/tutorials/web-site-with-sql-database/).

* Incorporación de un nombre de dominio personalizado y SSL

	Para obtener información sobre el uso de SSL y su propio dominio (por ejemplo, www.contoso.com en lugar de contoso.azurewebsites.net), consulte los recursos siguientes:

	* [Configuración de un nombre de dominio personalizado para un sitio web de Azure](web-sites-custom-domain-name.md).
	* [Habilitación de HTTPS en un sitio web de Azure](web-sites-configure-ssl-certificate.md)

* Cómo evitar el tiempo de reactivación después de períodos de inactividad 

	De forma predeterminada, las aplicaciones web se descargan si han estado inactivas durante un período de tiempo. La primera solicitud después de eso tiene que esperar a que la aplicación web se vuelva a cargar. Para evitar el tiempo de espera, puede habilitar la característica AlwaysOn. Para obtener más información, consulte las opciones de configuración en [Configuración de la aplicación web](web-sites-configure.md).

* Cómo agregar características en tiempo real (por ejemplo, chat)

	Si la aplicación web incluirá características en tiempo real (como un servicio de chat, un juego, un tablero de cotizaciones, etc.), puede optimizar el rendimiento usando [ASP.NET SignalR](http://www.asp.net/signalr) con el método de transporte [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Para obtener más información, consulte [Uso de SignalR con aplicaciones web de Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites). 

* Cómo elegir entre el servicio de aplicaciones, los servicios en la nube y las máquinas virtuales para las aplicaciones web

	En Azure puede ejecutar aplicaciones web en Aplicaciones web del Servicio de aplicaciones, como se muestra en este tutorial, o bien en Servicios en la nube o Máquinas virtuales. Para obtener más información, consulte [Modelos de ejecución de Azure](/develop/net/fundamentals/compute/) y [Aplicaciones web, servicios en la nube y máquinas virtuales de Azure: cuándo usar cada uno](/manage/services/web-sites/choose-web-app-service/).

## Lo que ha cambiado
* Para obtener una guía para cambiar de sitios web al servicio de aplicaciones, consulte: [El servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía para cambiar del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->