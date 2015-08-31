<properties
	pageTitle="Crear una aplicación web de ASP.NET en el servicio de aplicaciones de Azure"
	description="Este tutorial muestra cómo crear un proyecto web ASP.NET en Visual Studio 2013 e implementarlo en una aplicación web en el Servicio de aplicaciones de Azure. En menos de 15 minutos, tendrá una aplicación en funcionamiento en la nube."
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="08/10/2015"
	ms.author="tdykstra"/>

# Creación de una aplicación web ASP.NET en el Servicio de aplicaciones de Azure

## Información general

Este tutorial muestra cómo crear una aplicación web ASP.NET e implementarla en [Aplicaciones web del Servicio de aplicaciones ](app-service-web-overview.md) con Visual Studio 2015 o Visual Studio 2013. En este tutorial se supone que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

La siguiente ilustración muestra la aplicación completada:

![Página principal de la aplicación web](./media/web-sites-dotnet-get-started/deployedandazure.png)

Aprenderá a realizar los siguientes procedimientos:

* Habilitación de su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure.
* Creación de un proyecto web de Visual Studio ASP.NET y su posterior implementación en un sitio web de Azure.
* Realización de cambios en el proyecto web y reimplementación de la aplicación.
* Uso del [Portal de vista previa de Azure](/overview/preview-portal/) para supervisar y administrar la aplicación web.

##<a name="video"></a>Suscripción a Microsoft Azure

Para completar este tutorial, deberá tener una cuenta de Azure:

* Puede [abrir una cuenta de Azure de manera gratuita](/pricing/free-trial/?WT.mc_id=A261C142F): obtiene créditos que puede usar para probar los servicios de Azure de pago, e incluso una vez agotados los créditos, podrá mantener la cuenta y utilizar servicios gratuitos de Azure, como Aplicaciones web del Servicio de aplicaciones.
* Puede [activar las ventajas de suscriptor de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Su suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.

> [AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

En este vídeo, Scott Hanselman presenta lo sencillo que resulta suscribirse para una prueba gratuita de Microsoft Azure. (Duración: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

## Creación de una aplicación web ASP.NET

El primer paso es crear un proyecto web en Visual Studio y una aplicación web en el Servicio de aplicaciones de Azure. Cuando esté listo, implemente el proyecto a la aplicación web para que esté disponible en Internet.

En el diagrama se muestra lo que se hace en los pasos de creación e implementación.

![Creación e implementación](./media/web-sites-dotnet-get-started/Create_App.png)

1. Abra Visual Studio 2015 o Visual Studio 2013. 

	Si usa Visual Studio 2013, las pantallas pueden variar ligeramente con respecto a las capturas de pantalla, pero los procedimientos son básicamente los mismos.

2. En el menú **Archivo**, haga clic en **Nuevo > Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **C\#** > **Web** > **Aplicación Web ASP.NET**. Si lo prefiere, puede elegir **Visual Basic**.

3. Asegúrese de que **.NET Framework 4.5.2** es el marco de destino seleccionado.

4.  [Application Insights](app-insights-overview.md) supervisa la aplicación web en cuanto a disponibilidad, rendimiento y uso. Desactive la casilla **Agregar Application Insights al proyecto** si no desea probarlo.

4. Ponga a la aplicación el nombre **MyExample**.

5. Haga clic en **Aceptar**.

	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**.

	[MVC](http://www.asp.net/mvc) es un conjunto de marcos ASP.NET para desarrollar aplicaciones web.

7. Haga clic en **Cambiar autenticación**.

	![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. En el cuadro de diálogo **Cambiar autenticación**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

	![Sin autenticación](./media/web-sites-dotnet-get-started/GS13noauth.png)

	La aplicación de ejemplo que va a crear no permitirá a los usuarios iniciar sesión. La sección [Pasos siguientes](#next-steps) vincula a un tutorial que implementa la autenticación y autorización.

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, no altere la configuración de en **MicrosoftAzure** y, a continuación, haga clic en **Aceptar**.

	![Cuadro de diálogo Nuevo proyecto de ASP.NET](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	La configuración predeterminada especifica que Visual Studio creará una aplicación web de Azure para el proyecto web. En la siguiente sección del tutorial, implementará el proyecto web en la aplicación web recién creada.

5. Si aún no ha iniciado sesión en Azure, Visual Studio le pedirá que lo haga. Inicie sesión con el identificador y la contraseña de la cuenta que utiliza para administrar la suscripción de Azure.

	Cuando haya iniciado sesión, el cuadro de diálogo **Configuración de aplicación web de Microsoft Azure** le preguntará qué recursos desea crear.

	![Conectado a Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. En el cuadro de diálogo **Configuración de aplicación web de Microsoft Azure**, escriba un **Nombre de aplicación web** que sea único en el dominio *azurewebsites.net*. Puede denominarlo MyExample con números a la derecha para que sea único, por ejemplo, MyExample810. Si se crea automáticamente un nombre de web predeterminado, será exclusivo y puede usarlo.

	Si escribe un nombre que ya usa otro usuario, aparecerá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y tendrá que especificar otro nombre.

	Azure usará este nombre como prefijo de la dirección URL de la aplicación. La dirección URL completa se compondrá de lo que escriba aquí más *. azurewebsites.net* (como se muestra junto al cuadro de texto **Nombre de aplicación Web**). Por ejemplo, si el nombre es `MyExample810`, la dirección URL será `MyExample810.azurewebsites.net`. La dirección URL debe ser única.

4. En la lista desplegable **Plan del Servicio de aplicaciones**, seleccione **Crear nuevo plan del Servicio de aplicaciones**.

	La sección [Pasos siguientes](#next-steps), que se encuentra al final del tutorial, contiene vínculos a información acerca de los planes del Servicio de aplicaciones.

5. Escriba *MyExamplePlan*, u otro nombre si lo prefiere, como nombre del plan.

6. En la lista desplegable **Grupo de recursos**, seleccione **Crear nuevo grupo de recursos**.

	La sección [Pasos siguientes](#next-steps) contiene vínculos a información acerca de los grupos de recursos.

5. Como nombre del grupo de recursos, escriba *MyExampleGroup* u otro nombre que prefiera.

5. En la lista desplegable **Región**, elija la ubicación más cercana.

	Este valor especifica el centro de datos de Azure en el que se ejecutará la aplicación web. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, pero, para una aplicación web de producción, lo normal es que quiera tener el servidor web lo más cercano posible a los exploradores que accederán al sitio, con el fin de minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. No modifique el campo de la base de datos.

	En este tutorial no se utiliza una base de datos. En la sección [Pasos siguientes](#next-steps) encontrará un vínculo a un tutorial que muestra cómo usar una base de datos.

6. Haga clic en **Aceptar**.

	![](./media/web-sites-dotnet-get-started/configuresitesettings2.png)

	En unos segundos, Visual Studio crea el proyecto web en la carpeta especificada y crea la aplicación web en la región de Azure especificada.

	La ventana del **Explorador de soluciones** ventana muestra los archivos y carpetas del nuevo proyecto.

	![Explorador de soluciones](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	La ventana **Actividad del Servicio de aplicaciones de Azure** muestra que se ha creado la aplicación web.

	![Aplicación web creada](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	Y puede ver la aplicación web en el **Explorador de servidores**.

	![Aplicación web creada](./media/web-sites-dotnet-get-started/siteinse.png)

## Implementación de la aplicación en Azure

7. En la ventana **Actividad del Servicio de aplicaciones de Azure**, haga clic en **Publicar MyExample en esta aplicación web ahora**.

	![Aplicación web creada](./media/web-sites-dotnet-get-started/GS13sitecreated.png)

	En unos segundos aparecerá el Asistente para **publicación web**.

	La configuración que Visual Studio necesita para implementar el proyecto en Azure se ha guardado en un *perfil de publicación*. El asistente permite revisar y cambiar esta configuración.

8. En la pestaña **Conexión** del asistente para **publicación web**, haga clic en **Siguiente**.

	Visual Studio proporciona toda la configuración que hay que implementar en la aplicación web de Azure.

	![Conexión validada correctamente](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

10. En la pestaña **Configuración**, haga clic en **Siguiente**.

	Puede aceptar los valores predeterminados de **Configuración** y **Opciones de publicación de archivos**.

	La lista desplegable **Configuración** permite implementar una compilación de depuración para la depuración remota. La sección [Pasos siguientes](#next-steps) contiene vínculos a un tutorial que muestra cómo ejecutar Visual Studio en modo de depuración de forma remota.

	![Pestaña Settings](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

11. En la pestaña **Vista previa**, haga clic en **Publicar**.

	Si desea ver qué archivos se copiarán en Azure, puede hacer clic en **Iniciar vista previa** antes de hacer clic en **Publicar**.

	![](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Visual Studio comienza el proceso de copiar los archivos en el servidor de Azure.

	Las ventanas **Salida** y **Actividad de Servicio de aplicaciones de Azure** muestran las acciones de implementación que se realizaron e informan de la correcta finalización de la implementación.

	![Ventana de salida que informa de que la implementación se realizó correctamente](./media/web-sites-dotnet-get-started/PublishOutput.png)

	Tras una implementación correcta, el explorador predeterminado se abre automáticamente en la dirección URL de la aplicación web implementada y la aplicación que creó se ejecuta ahora en la nube. La dirección URL en la barra de direcciones del explorador muestra que la aplicación web se carga desde Internet.

	![Aplicación Web que se ejecuta en Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

13. Cierre el explorador.

## Realización de cambios y nueva implementación

Esta sección del tutorial es opcional. Lo que hará es cambiar el título **h1** de la página principal, ejecutar el proyecto en modo local en su equipo de desarrollo para comprobar el cambio y luego implementar el cambio en Azure.

2. Abra los archivos *Views/Home/Index.cshtml* o *.vbhtml* en el **Explorador de soluciones**, cambie el título **h1** de "ASP.NET" a "ASP.NET y Azure", y guarde el archivo.

	![MVC index.cshtml](./media/web-sites-dotnet-get-started/index.png)

	![Cambio de h1 de MVC](./media/web-sites-dotnet-get-started/mvcandazure.png)

1. Presione CTRL + F5 para ver el título actualizado mediante la ejecución de la aplicación web en el equipo local.

	![Aplicación Web que se ejecuta localmente](./media/web-sites-dotnet-get-started/localandazure.png)

	La dirección URL `localhost` muestra que se está ejecutando en el equipo local. De forma predeterminada se ejecuta en IIS Express, que es una versión ligera de IIS diseñada para el desarrollo de aplicaciones web.

1. Cierre el explorador.

1. En el **Explorador de soluciones**, haga clic con el botón derecho y seleccione **Publicar**.

	![Elija Publicar](./media/web-sites-dotnet-get-started/choosepublish.png)

	Aparecerá la pestaña **Vista previa** del asistente para **publicación web**. Si tuviera que cambiar cualquier configuración de publicación podría elegir otra pestaña; sin embargo, ahora todo lo que desea es repetir la implementación con la misma configuración.

2. En el Asistente para **publicación web**, haga clic en **Publicar**.

	![Haga clic en Publicar](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Visual Studio implementa el proyecto en Azure y abre la aplicación web en el explorador predeterminado.

	![Aplicación web modificada implementada](./media/web-sites-dotnet-get-started/deployedandazure.png)

**Sugerencia:** puede habilitar la barra de herramientas **Publicación en Web con un solo clic** para que la implementación sea aún más rápida. Haga clic en **Vista > Barras de herramientas** y seleccione **Publicación en Web con un solo clic**. La barra de herramientas permite seleccionar un perfil, hacer clic en un botón para publicar o hacer clic en un botón para abrir el Asistente para **publicación web**.

![Barra de herramientas Publicación en Web con un solo clic](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Supervisión y administración de la aplicación web en el Portal de administración

El [Portal de vista previa de Azure](/services/management-portal/) es una interfaz web que permite administrar y supervisar los servicios de Azure, por ejemplo, la aplicación web que acaba de crear. En esta sección del tutorial podrá ver algo de lo que puede hacer en el portal.

1. En el explorador, vaya a [https://portal.azure.com]() e inicie sesión con sus credenciales de Azure.

2. Haga clic en **Examinar todo > Aplicaciones web** y luego en el nombre de la aplicación web.

	La hoja **Aplicación web** muestra información general de la configuración y las estadísticas de uso de la aplicación web.

	![Hoja de aplicación Web](./media/web-sites-dotnet-get-started/portaldashboard.png)

	Por ahora, la aplicación web no ha tenido mucho tráfico y puede que no se muestre nada en el gráfico. Si explora la aplicación, actualiza la página varias veces y después actualiza la página la página del portal, verá que aparecen algunas estadísticas.

3. Haga clic en **Configuración** para ver más opciones para configurar la aplicación web.

	Verá una lista de los tipos de configuración.

	![](./media/web-sites-dotnet-get-started/portalconfigure1.png)

4. Haga clic en **Configuración de la aplicación** para ver un ejemplo de los tipos de configuración que se pueden establecer en el portal.

	Por ejemplo, puede controlar la versión de .NET que se utiliza para la aplicación web, habilitar características como [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/), establecer [valores de cadena de conexión](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) y mucho más.

	![Pestaña de configuración de aplicación de web en el portal](./media/web-sites-dotnet-get-started/portalconfigure2.png)

Éstas son sólo algunas de las características del Portal de administración. Puede crear nuevas aplicaciones web, eliminar las existentes, detener y reiniciar aplicaciones web, y administrar otros tipos de servicios de Azure, como bases de datos y máquinas virtuales.

## Pasos siguientes

En este tutorial, ha aprendido a crear una aplicación web sencilla e implementarla en una aplicación web de Azure. Estos son algunos temas y recursos relacionados que le permitirán obtener más información sobre aplicaciones web del Servicio de aplicaciones de Azure.

* Otros métodos de implementar un proyecto web

	En este tutorial ha visto el método más rápido para crear una aplicación web sencilla e implementarlo en una aplicación web de Azure. Para obtener información general sobre otras formas de implementación, ya sea con Visual Studio o con [automatización de la implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Implementación de una aplicación web de Azure](web-sites-deploy.md).

	Visual Studio también puede generar scripts de Windows PowerShell que permiten automatizar la implementación. Para obtener más información, consulte [Automatizar todo (creación de aplicaciones en la nube para el mundo real con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Administración de aplicaciones web en Visual Studio

	Para obtener información sobre las funciones de administración de aplicaciones web que se pueden usar en el **Explorador de servidores**, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Solución de problemas de aplicaciones web

	Visual Studio proporciona características que facilitan la visualización de los registros de Azure conforme se generan, en tiempo real. La ejecución también se puede realizar en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Incorporación de funcionalidad de base de datos y autorización

	Para ver un tutorial que muestra cómo acceder a una base de datos y restringir algunas funciones de aplicación a los usuarios autorizados, consulte [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en una aplicación web de Azure](/develop/net/tutorials/web-site-with-sql-database/).

* Incorporación de un nombre de dominio personalizado y SSL

	Para obtener información acerca de cómo utilizar SSL y su propio dominio (por ejemplo, www.contoso.com, en lugar de contoso.azurewebsites.net), consulte los siguientes recursos:

	* [Configuración de un nombre de dominio personalizado para un sitio web de Azure](web-sites-custom-domain-name.md).
	* [Habilitación de HTTPS en un sitio web de Azure](web-sites-configure-ssl-certificate.md)

* Cómo evitar el tiempo de espera de reactivación después de tiempos de espera de inactividad

	De forma predeterminada, las aplicaciones web se descargan si han estado inactivas durante algún tiempo. La primera solicitud después de cargar una aplicación web tiene que esperar a que la aplicación web se cargue de nuevo. Para evitar el tiempo de espera, puede habilitar la característica AlwaysOn. Para obtener más información, consulte las opciones de configuración de [Configuración de aplicaciones web](web-sites-configure.md).

* Cómo agregar características en tiempo real (por ejemplo, chat)

	Si la aplicación web va a incluir características en tiempo real (como un servicio de chat, un juego, un tablero de cotizaciones, etc.), se puede optimizar el rendimiento usando [ASP.NET SignalR](http://www.asp.net/signalr) con el método de transporte [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Para obtener más información, consulte [Uso de SignalR con aplicaciones web de Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites).

* Selección entre Servicio de aplicaciones, Servicios en la nube y Máquinas virtuales de Azure para aplicaciones web

	En Azure puede ejecutar aplicaciones web en Aplicaciones web del Servicio de aplicaciones, como se muestra en este tutorial, o bien en Servicios en la nube o en Máquinas virtuales. Para obtener más información, consulte [Aplicaciones web de Azure, Servicios en la nube y Máquinas virtuales: ¿cuándo se usa cada opción?](/manage/services/web-sites/choose-web-app-service/).

* [Cómo elegir o crear un plan del Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

* [Cómo elegir o crear un grupo de recursos](../azure-preview-portal-using-resource-groups.md)

## ¿Qué ha cambiado?

* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=August15_HO8-->