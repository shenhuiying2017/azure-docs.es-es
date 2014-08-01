<properties title="Troubleshooting Azure Web Sites in Visual Studio" pageTitle="Troubleshooting Azure Web Sites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Web Site by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter="Web Sites" authors="tdykstra" solutions="" />
# Solución de problemas de Sitios web Azure en Visual Studio

Durante el desarrollo y la prueba de una aplicación web, puede solucionar problemas si la [ejecuta en modo de depuración](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx) o si utiliza [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx). Puede ejecutar en modo de depuración locamente en IIS Express o de manera remota en un sitio web de Azure. Pero en lo que respecta a errores que solo se producen en producción, la mejor manera de depurarlos podría ser revisar los registros que crea el código de la aplicación o el servidor web. En este tutorial se muestra cómo utilizar herramientas de Visual Studio para ayudar a depurar una aplicación mientras se ejecuta en un sitio web de Azure, ya sea en modo de depuración de manera remota o viendo los registros del servidor web o de aplicación.

Aprenderá a realizar los siguientes procedimientos:

-   Funciones de administración de sitios Azure disponibles en Visual Studio.
-   Uso de la vista remota de Visual Studio para realizar cambios rápidos en un sitio web remoto.
-   Ejecución remota del modo de depuración mientras se ejecuta un proyecto en un sitio web de Azure.
-   Creación de registros de seguimiento de aplicación y su visualización mientras los crea la aplicación.
-   Visualización de registros de servidor web, incluidos mensajes de error detallados y seguimiento de solicitudes con error.
-   Envío de registros de diagnóstico a una cuenta de almacenamiento de Azure y su visualización aquí.

### Secciones del tutorial

1.  [Requisitos previos](#prerequisites)
2.  [Configuración y administración de sitios](#sitemanagement)
3.  [Vista remota](#remoteview)
4.  [Depuración remota](#remotedebug)
5.  [Información general de registros de diagnóstico](#logsoverview)
6.  [Creación y visualización de registros de seguimiento de aplicación](#apptracelogs)
7.  [Visualización de registros de servidor web](#webserverlogs)
8.  [Visualización de registros de mensajes de error detallados](#detailederrorlogs)
9.  [Descarga de registros de sistema de archivos](#downloadlogs)
10. [Visualización de registros de almacenamiento](#storagelogs)
11. [Visualización de registros de solicitudes con error](#failedrequestlogs)
12. [Pasos siguientes](#nextsteps)

<h2><a name="prerequisites"></a><span class="short-header">Requisitos previos</span></h2>

Este tutorial funciona con el entorno de desarrollo, proyecto web y sitio web de Azure que configuró en [Introducción a Azure y ASP.NET](http://azure.microsoft.com/en-us/develop/net/tutorials/get-started/). Los ejemplos de código que aparecen en este tutorial son para una aplicación web C\# MVC, pero los procedimientos de solución de problemas son los mismos para las aplicaciones de Web Forms y Visual Basic.

La depuración remota requiere Visual Studio 2013 o Visual Studio 2012 con actualización 4. Las demás características que aparecen en el tutorial también funcionan con Visual Studio 2013 Express para Web y Visual Studio 2012 Express para Web.

La característica de registros de streaming solo funciona para aplicaciones dirigidas a .NET Framework 4 o superior.

<h2><a name="sitemanagement"></a>Configuración y administración de sitios</h2>

Visual Studio proporciona acceso a un subconjunto de las funciones de administración de sitios y los ajustes de configuración disponibles en el portal de administración. En esta sección podrá ver las opciones y funciones disponibles.

1.  Si todavía no ha iniciado sesión en Azure en Visual Studio, haga clic en el botón **Connect to Azure** en el **Explorador de servidores**.

	Una alternativa es instalar un certificado de administración que permita el acceso a su cuenta. El certificado de administración da al Explorador de servidores acceso a servicios adicionales de Azure (Base de datos SQL y Servicios móviles). Si elige instalar un certificado, haga clic con el botón secundario en el nodo **Azure** del **Explorador de servidores** y, a continuación, haga clic en **Manage Subscriptions** en el menú contextual. En el cuadro de diálogo **Manage Azure Subscriptions**, haga clic en la pestaña **Certificados** y, a continuación, en **Import**. Siga las instrucciones para descargar y luego importar un archivo de suscripción (también conocido como archivo *.publishsettings*) para su cuenta de Azure.

	> [WACOM.NOTE]
	> Si descarga un archivo de suscripción, guárdelo en una carpeta ajena a los directorios de código fuente (por ejemplo, en la carpeta Descargas) y elimínelo una vez que finalice la importación. Si un usuario malintencionado obtuviera acceso al archivo de suscripción, podría editar, crear y eliminar servicios de Azure.

	Para obtener más información acerca de la conexión a recursos de Azure desde Visual Studio, consulte [Administrar cuentas, suscripciones y roles administrativos](http://go.microsoft.com/fwlink/?linkid=324796#bkmk_accountvcert&clcid=0x409).

1.  En el **Explorador de servidores**, expanda **Azure** y, a continuación, **Sitios web**.
2.  Haga clic con el botón secundario en el nodo del sitio web que creó en [Introducción a Azure y ASP.NET](http://azure.microsoft.com/en-us/develop/net/tutorials/get-started/) y, a continuación, haga clic en **View Settings**.

	![Ver configuración en el Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	Aparece la pestaña **Azure Web Site** y podrá ver las tareas de configuración y administración de sitios disponibles en Visual Studio.

	![Ventana Azure Web Site](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	En este tutorial usará los menús desplegables de registro y seguimiento. También utilizará la depuración remota, pero va a utilizar un método distinto para habilitarla.

	Para obtener información acerca de los cuadros App Settings y Connection Strings de esta ventana, consulte[Azure Web Sites: How Application Strings and Connection Strings Work](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Si desea realizar una tarea de administración de sitios que no se puede hacer en esta ventana, puede hacer clic en **Full Web Site Settings** para abrir una ventana del explorador con el portal de administración. Para obtener más información, consulte [Configuración de Sitios web](http://azure.microsoft.com/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Vista remota</h2>

Normalmente implementa un sitio con la marca `customErrors` en el archivo Web.config definida en `On` o en `RemoteOnly`, lo que significa que no recibirá un práctico mensaje de error cuando algo se realice de manera incorrecta. Para muchos errores, todo lo que verá será una página como alguna de las siguientes.

**Error del servidor en la aplicación '/':** 
![Página de error poco práctica](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Se produjo un error:** 
![Página de error poco práctica](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**El sitio web no puede mostrar la página** 
![Página de error poco práctica](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Con frecuencia, la manera más fácil de encontrar la causa de un error es habilitar los mensajes detallados de error, una acción que se explica en la primera de las capturas de pantalla anteriores. Eso requiere un cambio en el archivo Web.config implementado. Podría editar el archivo *Web.config* en el proyecto y volver a implementar el proyecto, o bien, crear una [transformación de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar una compilación de depuración. Sin embargo, hay una manera más rápida: en el **Explorador de soluciones**, puede ver y editar directamente los archivos en el sitio remoto con la característica de *vista remota*.

1.  En el **Explorador de servidores**, expanda **Azure**, **Sitios web** y expanda el nodo del sitio web en el que está realizando la implementación.

	Puede ver los nodos que le proporcionan acceso a los archivos de registro y de contenido del sitio web.

	![Archivos y archivos de registro](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

1.  Expanda el nodo **Files** y haga doble clic en el archivo *Web.config*.

	![Abrir Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Visual Studio abre el archivo Web.config desde el sitio remoto y muestra [Remote] junto al nombre de archivo en la barra de títulos.

1.  Agregue la siguiente línea al elemento `system.web`:

	`<customErrors mode="off"></customErrors>`

	![Editar Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

1.  Actualice el explorador que actualmente muestra el mensaje de error poco práctico y ahora podrá ver un mensaje detallado del error, tal como muestra el siguiente ejemplo:

	![Mensaje de error detallado](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(El error que aparece se generó al agregar la línea en rojo a *Views\\Home\\Index.cshtml*).

Editar el archivo Web.config es solo un ejemplo de las situaciones en las que la capacidad de leer y editar archivos en su sitio web de Azure facilita la solución de problemas.


<h2><a name="remotedebug"></a>Depuración remota</h2>

Si el mensaje de error detallado no proporciona información suficiente y no es posible recrear el error de manera local, otra forma de solucionar problemas es ejecutar remotamente en modo de depuración. Puede definir puntos de interrupción, manipular directamente la memoria, revisar el código e, incluso, cambiar la ruta del código.

La depuración remota no funciona en ediciones Express de Visual Studio.

Esta sección muestra cómo depurar remotamente con el proyecto que crea en [Introducción a Azure y ASP.NET](http://azure.microsoft.com/en-us/develop/net/tutorials/get-started/).

1.  Abra el proyecto web que creó en [Introducción a Azure y ASP.NET](http://azure.microsoft.com/en-us/develop/net/tutorials/get-started/).

2.  Abra *Controllers\\HomeController.cs*.

3.  Elimine el método `About()` e inserte el siguiente código en su lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

4.  [Defina un punto de interrupción](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx) en la línea `ViewBag.Message`.

5.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, en **Publicar**.

6.  En la lista desplegable **Perfil**, seleccione el mismo perfil que utilizó en [Introducción a Azure y ASP.NET](http://azure.microsoft.com/en-us/develop/net/tutorials/get-started/).

7.  Haga clic en la pestaña **Configuración**, cambie la **Configuración** a **Depurar** y, a continuación, haga clic en **Publicar**.

	![Publicar en modo de depuración](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8.  Una vez que ha finalizado la implementación y que el explorador abre la dirección URL de Azure de su sitio, cierre el explorador.
9.  Para Visual Studio 2013: En el **Explorador de servidores**, expanda **Azure**, **Sitios web**, haga clic con el botón secundario en el sitio web y, a continuación, haga clic en **Attach Debugger**.

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	El explorador se abre automáticamente en su página inicial en ejecución en Azure. Es posible que deba esperar unos 20 segundos mientras Azure configura el servidor para la depuración. Esta demora solo se produce la primera vez que ejecuta el modo de depuración en un sitio web. Las veces posteriores dentro de las próximas 48 horas no habrá demora cuando vuelva a iniciar la depuración.

10. Para Visual Studio 2012 con actualización 4:

	* En el Portal de administración de Azure, vaya a la pestaña **Configure** para su sitio web y desplácese hacia abajo hasta la sección **Site Diagnostics**.
	
	* Defina **Remote Debugging** en **On** y **Remote Debugging Visual Studio Version** en **2012**.

	![Definir la depuración remota en el portal de administración](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)

	* En el menú **Depurar** de Visual Studio, haga clic en **Asociar al proceso**.
	
	* En el cuadro **Calificador**, escriba la dirección URL de su sitio web, sin el prefijo `http://`.
	
	* Seleccione **Mostrar los procesos de todos los usuarios**. 
	
	* Cuando se le soliciten credenciales, escriba el nombre de usuario y la contraseña con permisos para publicar el sitio web. Para obtener estas credenciales, vaya a la pestaña Panel del sitio web en el portal de administración y haga clic en **Download the publish profile**. Abra el archivo en un editor de texto y verá el nombre de usuario y la contraseña después de las primeras apariciones de **userName=** y **userPWD=**. 
	
	* Cuando los procesos aparezcan en la tabla **Available Processes**, seleccione **w3wp.exe** y haga clic en **Attach**.
	 
	* Abra la dirección URL de su sitio en el explorador.

	Es posible que deba esperar unos 20 segundos mientras Azure configura el servidor para la depuración. Esta demora solo se produce la primera vez que ejecuta el modo de depuración en un sitio web. Las veces posteriores dentro de las próximas 48 horas no habrá demora cuando vuelva a iniciar la depuración.

1.  Haga clic en **About** en el menú.

	Visual Studio se detiene en el punto de interrupción y el código se ejecuta en Azure, no en su equipo local.

1.  Mantenga el mouse sobre la variable `currentTime` para ver el valor de tiempo.

	![Ver la variable en ejecución en modo de depuración en Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	La hora que ve es la hora del servidor Azure, que puede estar en una zona horaria distinta a la de su equipo local.

1.  Escriba un valor nuevo para la variable `currentTime`, como "Ahora en ejecución en Azure".

2.  Presione F5 para continuar la ejecución.

	La página About que se ejecuta en Azure muestra el valor nuevo que escribió en la variable currentTime.

	![Página About con el valor nuevo](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### Notas acerca de la depuración remota

-   No se recomienda ejecutar el modo de depuración en producción. Si su sitio de producción no está escalado horizontalmente a varias instancias de servidor, la depuración conectará el tráfico a la única instancia de servidor web. Si tiene varias instancias de servidor web, cuando se asocie al depurador recibirá una instancia aleatoria y no tendrá forma de asegurarse de que las solicitudes del explorador se dirijan a esa instancia. Además, normalmente no implementará una compilación de depuración en producción, y las optimizaciones de compilador para compilaciones de versión podrían hacer imposible mostrar lo que ocurre línea a línea en su código fuente. Para solucionar problemas de producción, su mejor recurso son los registros de servidor web y de seguimiento de la aplicación.

-   Evite detenciones prolongadas en los puntos de interrupción cuando ejecute la depuración remota. Azure considera un proceso detenido durante más de unos minutos como un proceso sin respuesta y lo apaga.

-   Mientras realiza la depuración, el servidor envía datos a Visual Studio, lo que podría afectar a los cargos de ancho de banda. Para obtener información acerca de las tarifas de ancho de banda, consulte [Precios de Azure](http://azure.microsoft.com/en-us/pricing/calculator/).

-   Asegúrese de que el atributo `debug` del elemento `compilation` en el archivo *Web.config* esté definido como verdadero. Este es el valor predeterminado cuando publica una configuración de compilación de depuración.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

-   Si encuentra que el depurador no entra en el código que desea depurar, es posible que deba cambiar la configuración de Solo mi código. Para obtener más información, consulte [Restringir la ejecución paso a paso a Solo mi código](http://msdn.microsoft.com/en-us/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

-   Cuando habilita la característica de depuración remota, se inicia un temporizador en el servidor y 48 horas después la característica se desactiva automáticamente. Este límite de 48 horas es por motivos de seguridad y rendimiento. Puede activar fácilmente la característica las veces que lo desee. Recomendamos dejarla deshabilitada cuando no esté realizando activamente una depuración.

-   Puede asociar manualmente el depurador a cualquier proceso. No solo puede depurar el proceso del sitio web (w3wp.exe), sino que también puede depurar otros procesos, como [WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Para obtener más información acerca de cómo utilizar el modo de depuración en Visual Studio, consulte [Depurar en Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a><span class="short-header">Logs overview</span>Información general de registros de diagnóstico</h2>

Una aplicación de ASP.NET que se ejecuta en un sitio web de Azure puede crear las siguientes clases de registros:

-   **Registros de seguimiento de la aplicación**
     La aplicación crea estos registros mediante la llamada a métodos de la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/en-us/library/system.diagnostics.trace.aspx).
-   **Registros de servidor web**
     El servidor web crea una entrada de registro para cada solicitud HTTP al sitio.
-   **Registros de mensajes de error detallados**
     El servidor web crea una página HTML con algo de información adicional para solicitudes HTTP con error (las que generan el código de error 400 o superior).
-   **Registros de seguimiento de solicitudes con error**
     El servidor web crea un archivo XML con información de seguimiento detallada para solicitudes HTTP con error. El servidor web también proporciona un archivo XSL para dar formato al XML en un explorador.

El registro afecta el rendimiento del sitio, por lo que Azure le ofrece la posibilidad de habilitar o deshabilitar cada tipo de registro según sea necesario. En el caso de registros de aplicaciones, puede especificar que solo se escriban los registros por encima de un determinado nivel de gravedad. Cuando crea un sitio web, todos los registros están deshabilitados de manera predeterminada.

Los registros se escriben en archivos en una carpeta *LogFiles* en el sistema de archivos del sitio web. Los registros de servidor web y registros de aplicaciones también se pueden escribir en una cuenta de almacenamiento de Azure. Puede conservar un volumen mayor de registros en una cuenta de almacenamiento de lo que es posible en el sistema de archivos. Cuando utiliza el sistema de archivos, tiene un límite máximo de 100 megabytes de registros. (Los registros del sistema de archivos solo sirven para la conservación a corto plazo. Azure elimina los archivos de registro antiguos para dejar espacio para los nuevos una vez que se alcanza el límite).

<h2><a name="apptracelogs"></a><span class="short-header">Application logs</span>Creación y visualización de registros de seguimiento de aplicación</h2>

En esta sección realizará las siguientes tareas:

-   Agregar instrucciones de seguimiento al proyecto web que creó en[el tutorial anterior](http://azure.microsoft.com/en-us/develop/net/tutorials/get-started/).
-   Ver los registros cuando ejecute localmente el proyecto.
-   Ver los registros a medida que los genera la aplicación que se ejecuta en Azure.

### Adición de instrucciones de seguimiento a la aplicación

1.  Abra *Controllers\\HomeController.cs* y reemplace el contenido del archivo por el siguiente código para agregar instrucciones `Trace` y una instrucción `using` para `System.Diagnostics`:

		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
		    public class HomeController : Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Entering Index method");
		            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
		            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Index method");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entering About method");
		            ViewBag.Message = "Your app description page.";
		            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Leaving About method");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entering Contact method");
		            ViewBag.Message = "Your contact page.";
		            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Contact method");
		            return View();
		        }
		    }
		}

### Visualización del resultado de seguimiento localmente

1.  Presione F5 para ejecutar la aplicación en modo de depuración.

	La escucha de seguimiento predeterminada escribe todo el resultado del seguimiento en la ventana **Resultados**, además del resultado de la depuración. La siguiente ilustración muestra el resultado de las instrucciones de seguimiento que agregó al método `Index`.

	![Ventana de seguimiento en depuración](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	Los pasos siguientes muestran cómo ver el resultado del seguimiento en una página web, sin compilar en modo de depuración.

1.  Abra el archivo Web.config de la aplicación (el que está ubicado en la carpeta del proyecto) y agregue un elemento `<\system.diagnostics>` al final del archivo, justo antes del elemento `<\configuration>` de cierre:

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>

	`WebPageTraceListener` le permite ver el resultado del seguimiento si navega a `/trace.axd`.

1.  Agregue un [elemento de seguimiento](http://msdn.microsoft.com/en-us/library/vstudio/6915t83k%28v=vs.100%29.aspx) bajo `<system.web>` en el archivo Web.config, tal como en el siguiente ejemplo:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" /\>

1.  Pulse CTRL+F5 para ejecutar la aplicación.
2.  En la barra de direcciones de la ventana del explorador, agregue *trace.axd* a la dirección URL y, a continuación, presione Entrar (la dirección URL será similar a http://localhost:53370/trace.axd).
3.  En la página **Application Trace**, haga clic en **View Details**.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	Aparece la página **Request Details** y en la sección **Trace Information**, podrá ver el resultado de las instrucciones de seguimiento que agregó al método `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	De manera predeterminada, `trace.axd` solo está disponible localmente. Si desea que esté disponible desde un sitio remoto, puede agregar `localOnly="false"` al elemento `trace` en el archivo *Web.config*, tal como aparece en el siguiente ejemplo:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Sin embargo, por motivos de seguridad, generalmente no se recomienda habilitar `trace.axd` en un sitio de producción, y en las siguientes secciones verá una manera más simple de leer los registros de seguimiento en un sitio web de Azure.

### Visualización del resultado de seguimiento en Azure

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto web y, a continuación, en **Publicar**.
2.  En el cuadro de diálogo **Publish Web**, haga clic en **Publicar**.

	Después de que Visual Studio publica su actualización, se abre una ventana del explorador en su página principal (suponiendo que no desactivó **Destination URL** en la pestaña **Connection**).

1.  En el **Explorador de servidores**, haga clic con el botón secundario en su sitio web y seleccione **View Streaming Logs in Output Window**.

	![Ver registros de streaming en el menú contextual](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	La ventana **Resultados** muestra que está conectado al servicio de streaming de registros y agrega una línea de notificación cada minuto que pasa sin un registro para mostrar.

	![Ver registros de streaming en el menú contextual](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

1.  En la ventana del explorador que muestra la página principal de su aplicación, haga clic en **Contact**.

	En unos pocos segundos, el resultado del seguimiento en el nivel de error que agregó al método `Contact` aparecerá en la ventana **Output**.

	![Seguimiento de errores en la ventana Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio muestra solo seguimientos en el nivel de errores porque es el valor predeterminado cuando habilita el servicio de supervisión de registros. Cuando crea un sitio web de Azure, todos los registros están deshabilitados de manera predeterminada, tal como vio cuando abrió anteriormente la página de configuración del sitio:

	![Registro de aplicación desactivado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

	Sin embargo, cuando seleccionó **View Streaming Logs in Output Window**, Visual Studio cambió automáticamente **Application Logging(File System)** a **Error**, lo que significa que se notifican los registros de nivel de error. Para ver todos los registros de seguimiento, puede cambiar esta configuración a **Verbose**. Cuando selecciona un nivel de gravedad inferior al error, también se notifican todos los registros para niveles de gravedad más altos. Por lo tanto, cuando selecciona detallado, también verá registros de errores, advertencias e información.

1.  En el **Explorador de servidores**, haga clic con el botón secundario en el sitio web y, a continuación, haga clic en **View Settings** tal como hizo anteriormente.
2.  Cambie **Application Logging (File System)** a **Verbose** y, a continuación, haga clic en **Save**.

	![Definir el nivel de seguimiento como Verbose]((./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

1.  En la ventana del explorador que ahora muestra su página **Contact**, haga clic en **Home**, en **About** y, a continuación, en **Contact**.

	En unos pocos segundos, la ventana **Resultados** mostrará todos los resultados del seguimiento.

	![Resultados detallados del seguimiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	En esta sección aprendió a habilitar y deshabilitar el registro mediante la configuración del sitio web de Azure. También puede habilitar y deshabilitar escuchas de seguimiento si modifica el archivo Web.config. Sin embargo, modificar el archivo Web.config hace que el dominio de la aplicación se recicle, mientras que habilitar el registro a través del sitio web no provoca eso. Si el problema tarda mucho en reproducirse, o bien es intermitente, reciclar el dominio de la aplicación podría "arreglarlo" y forzarle a esperar hasta que vuelva a ocurrir. Permitir el diagnóstico en Azure no lo hace, por lo que puede comenzar inmediatamente a capturar información sobre el error.

### Características de la ventana de salida

La pestaña **Azure Logs** de la ventana **Resultados** tiene varios botones y un cuadro de texto:

![Botones de la pestaña de registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Estos elementos realizan las siguientes funciones:

-   Limpiar la ventana **Resultados**.
-   Habilitar o deshabilitar el ajuste automático de línea.
-   Iniciar o detener la supervisión de los registros.
-   Especificar los registros para supervisar.
-   Descargar registros.
-   Filtrar los registros según una cadena de búsqueda o una expresión regular.
-   Cerrar la ventana **Resultados**.

Si especificó una cadena de búsqueda o una expresión regular, Visual Studio filtra la información de registro en el cliente. Esto significa que puede especificar los criterios después de que aparezcan los registros en la ventana **Resultados** y que puede cambiar esos criterios de filtrado sin tener que volver a generar los registros.

<h2><a name="webserverlogs"></a><span class="short-header">Web server logs</span>Visualización de registros de servidor web</h2>

Los registros de servidor web registran toda la actividad HTTP en el sitio. Para verlos en la ventana **Output**, debe habilitarlos en el sitio e indicarle a Visual Studio que desea supervisarlos.

1.  En la pestaña **Azure Web Site Configuration** que abrió desde el **Explorador de servidores**, cambie el registro de servidor web a **On** y haga clic en **Save**.

	![Habilitar el registro de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

1.  En la ventana **Resultados**, haga clic en el botón **Specify which Azure logs to monitor**.

	![Especificar los registros de Azure para supervisar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

1.  En el cuadro de diálogo **Azure Logging Options**, seleccione **Web server logs** y haga clic en **OK**.

	![Supervisar los registros de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

1.  En la ventana del explorador que muestra el sitio web, haga clic en **Home**, en **About** y, a continuación, en **Contact**.

	Los registros de aplicación generalmente aparecen primero y luego aparecen los registros de servidor web. Es posible que deba esperar un poco hasta que aparezcan.

	![Registros de servidor web en la ventana Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

De manera predeterminada, cuando habilita por primera vez los registros de servidor web mediante el uso de Visual Studio, Azure escribe los registros en el sistema de archivos. De manera alternativa, puede utilizar el portal de administración para especificar que los registros de servidor web se deben escribir en un contenedor de blob en una cuenta de almacenamiento. Para obtener más información, consulte la sección sobre **diagnóstico del sitio** en [Configuración de Sitios web](http://azure.microsoft.com/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Si utiliza el portal de administración para habilitar el registro de servidor web para una cuenta de almacenamiento de Azure y luego deshabilitar el registro en Visual Studio, cuando vuelve a habilitar el registro en Visual Studio, se restaurará la configuración de su cuenta de almacenamiento.

<h2><a name="detailederrorlogs"></a><span class="short-header">Error logs</span>Visualización de registros de mensajes de error detallados</h2>

Los registros de error detallados proporcionan información adicional acerca de las solicitudes HTTP que generaron códigos de respuesta con error (400 o superiores). Para verlos en la ventana **Resultados**, debe habilitarlos en el sitio e indicarle a Visual Studio que desea supervisarlos.

1.  En la pestaña **Azure Web Site Configuration** que abrió desde el **Explorador de servidores**, cambie **Detailed Error Messages** a **On** y haga clic en **Save**.

	![Habilitar los mensajes de error detallados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

1.  En la ventana **Resultados**, haga clic en el botón **Specify which Azure logs to monitor**.
2.  En el cuadro de diálogo **Azure Logging Options**, haga clic en **All logs** y haga clic en **OK**.

	![Supervisar todos los registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

1.  En la barra de direcciones de la ventana del explorador, agregue un carácter adicional a la dirección URL para generar un error 404 (por ejemplo, `http://localhost:53370/Home/Contactx)` y presione Entrar.

	Después de varios segundos, el registro detallado del error aparecerá en la ventana **Resultados** de Visual Studio.

	![Registro de error detallado en la ventana Resultados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Presione Ctrl y haga clic en el vínculo para ver el resultado del registro formateado en un explorador:

	![Registro de error detallado en la ventana del explorador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a><span class="short-header">Download logs</span>Descarga de registros de sistema de archivos</h2>

Todo registro que pueda supervisar en la ventana **Resultados** se puede también descargar como archivo *.zip*.

1.  En la ventana **Resultados**, haga clic en **Download Streaming Logs**.

	![Botones de la pestaña de registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	El Explorador de archivos abre la carpeta *Descargas* con el archivo descargado seleccionado.

	![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

1.  Extraiga el archivo *.zip* y verá la siguiente estructura de carpeta:

	![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	-   Los registros de seguimiento de la aplicación están en los archivos *.txt* de la carpeta *LogFiles\\Application*.
	-   Los registros de servidor web están en los archivos *.log* de la carpeta *LogFiles\\http\\RawLogs*. Puede utilizar una herramienta como [Analizador del registro](http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659) para ver y manipular estos archivos.
	-   Los registros de mensajes de error detallados están en los archivos *.html* de la carpeta *LogFiles\\DetailedErrors*.

	(La carpeta *deployments* es para archivos creados por la publicación del control del código fuente; no tiene ninguna relación con la publicación en Visual Studio. La carpeta *Git* es para seguimientos relacionados con la publicación del control del código fuente y el servicio de streaming de archivos de registro).

<h2><a name="storagelogs"></a><span class="short-header">Storage logs</span>Visualización de registros de almacenamiento</h2>

Los registros de seguimiento de la aplicación también se pueden enviar a una cuenta de almacenamiento de Azure y puede verlos en Visual Studio. Para ello, creará una cuenta de almacenamiento, habilitará los registros de almacenamiento en el portal de administración y los verá en la pestaña **Logs** de la ventana **Azure Web Site**.

Puede enviar registros a todos o a cualquiera de estos tres destinos:

-   El sistema de archivos.
-   Tablas de cuentas de almacenamiento.
-   Blobs de cuentas de almacenamiento.

Puede especificar un nivel de gravedad distinto para cada destino.

Las tablas facilitan la visualización de los detalles de registros en línea y son compatibles con streaming; puede consultar los registros en las tablas y ver nuevos registros a medida que se van creando. Los blobs facilitan la descarga de registros en archivos y su análisis con HDInsight, porque HDInsight sabe cómo trabajar con el almacenamiento de blobs. Para obtener más información, consulte **Hadoop and MapReduce** en [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Actualmente tiene los registros de sistema de archivos definidos en el nivel detallado; los pasos siguientes le guiarán por la configuración de registros en el nivel de información para ir a tablas de cuentas de almacenamiento. El nivel de información se refiere a que se mostrarán todos los registros creados al llamar a `Trace.TraceInformation`, `Trace.TraceWarning` y `Trace.TraceError`, pero no los creados al llamar a `Trace.WriteLine`.

Las cuentas de almacenamiento ofrecen más almacenamiento y retención más prolongada de registros en comparación con el sistema de archivos. Otra ventaja de enviar los registros de seguimiento de la aplicación al almacenamiento es que obtiene información adicional con cada registro que no obtiene de los registros del sistema de archivos.

1.  En el **Explorador de servidores**, haga clic con el botón secundario en el sitio web y, a continuación, haga clic en **Open in Management Portal**.

2.  En el portal de administración, haga clic en la pestaña **Almacenamiento** y, a continuación, haga clic en **Create a Storage Account**.

	![Crear una cuenta de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png)

1.  Escriba una dirección URL única para usarla en la cuenta de almacenamiento y haga clic en **Create Storage Account**.

	![Escribir una dirección URL](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png)

1.  En la ventana **Azure Web Site** de Visual Studio, haga clic en la pestaña **Registros** y luego en **Configure Logging**.

	![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Esto abre la pestaña **Configure** en el portal de administración de su sitio web. También puede llegar ahí mediante un clic en la pestaña **Sitios web**, donde debe hacer clic en su sitio web y, a continuación, en la pestaña **Configure**.

1.  En la pestaña **Configure** del portal de administración, desplácese hacia abajo hasta la sección de diagnóstico de la aplicación y cambie **Application Logging (Table Storage)** a **On**.

2.  Cambie **Logging Level** a **Information**.

3.  Haga clic en **Manage Table Storage**.

	![Hacer clic en Manage Table Storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	En el cuadro **Manage table storage for application diagnostics** puede elegir su cuenta de almacenamiento si tiene más de una. Puede crear una tabla o usar una existente.

	![Administrar el almacenamiento de tablas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

1.  En el cuadro **Manage table storage for application diagnostics**, haga clic en la marca de verificación junto al cuadro.
2.  En la pestaña **Configure** del portal de administración, haga clic en **Save**.
3.  En la ventana del explorador que muestra el sitio web de la aplicación, haga clic en **Home**, en **About** y, a continuación, en **Contact**.

	La información de registro generada por la navegación en estas páginas web se escribirá en la cuenta de almacenamiento.

1.  En la pestaña **Registros** de la ventana **Azure Web Site** en Visual Studio, haga clic en **Refresh** en **Diagnostic Summary**.

	![Hacer clic en Actualizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	De manera predeterminada, la sección **Diagnostic Summary** muestra registros de los últimos 15 minutos. Puede cambiar el período para ver más registros.

	(Si recibe un error de "no se encontró tabla", compruebe que navegó a las páginas que realizan el seguimiento después de haber habilitado **Application Logging (Storage)** y después de haber hecho clic en **Save**).

	![Registros de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Tenga en cuenta que en esta vista puede ver el **identificador del proceso** y el **identificador del subproceso** para cada registro, los cuales no vería en los registros del sistema de archivos. Si desea ver campos adicionales, puede ver directamente la tabla de almacenamiento de Azure.

1.  Haga clic en **View all application logs**.

	La tabla de registros de seguimiento aparece en el visor de tablas de almacenamiento de Azure.

	(Si recibe un error de "la secuencia no contiene elementos", abra el **Explorador de servidores**, expanda el nodo para la cuenta de almacenamiento en el nodo **Azure**, haga clic con el botón secundario en **Tablas** y haga clic en **Actualizar**).

	![Tabla de seguimiento en el Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Registros de almacenamiento en vista de tabla](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Esta vista muestra campos adicionales que no están disponibles en otras vistas. Esta vista también le permite filtrar registros mediante una interfaz de usuario especial del Generador de consultas para construir una consulta. Para obtener más información, consulte Trabajar con recursos de tabla - Filtrar entidades en [Explorar recursos de almacenamiento con el Explorador de servidores](http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx).

1.  Para ver los detalles de una sola fila, haga clic con el botón secundario en una de las filas y, a continuación, haga clic en **Editar**.

	![Tabla de seguimiento en el Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a><span class="short-header">Visualización de registros de seguimiento de solicitudes con error</span>View failed request tracing logs</h2>

Los registros de seguimiento de solicitudes con error son útiles cuando necesita comprender los detalles de cómo IIS maneja una solicitud HTTP, en situaciones tales como problemas de autenticación o reconfiguración de URL.

Sitios web Azure usa la misma funcionalidad de seguimiento de solicitudes con error que ha estado disponible con IIS 7.0 y posterior. Sin embargo, no tiene acceso a la configuración de IIS que determina los errores que se registran. Cuando habilita el seguimiento de solicitudes con error, se capturan todos los errores.

Puede habilitar el seguimiento de solicitudes con error a través de Visual Studio, pero no puede verlas ahí. Estos registros son archivos XML. El servicio de registro de streaming solo supervisa archivos que se pueden leer en modo de texto sin formato: archivos *.txt*, *.html* y *.log*.

Puede ver los registros de seguimiento de solicitudes en un explorador directamente vía FTP o de manera local después de utilizar una herramienta FTP para descargarlos en el equipo local. En esta sección los verá directamente en un explorador.

1.  En la pestaña **Configuration** de la ventana **Azure Web Site** que abrió desde el **Explorador de servidores**, cambie **Failed Request Tracing** a **On** y haga clic en **Save**.

	![Habilitar el seguimiento de solicitudes con error](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

1.  En la barra de direcciones de la ventana del explorador que muestra el sitio web, agregue un carácter adicional a la dirección URL y haga clic en Entrar para generar un error 404.

	Esto hará que se cree un registro de seguimiento de solicitud con error; los pasos siguientes muestran cómo ver o descargar el registro.

1.  En Visual Studio, en la pestaña **Configuration** de la ventana **Azure Web Site**, haga clic en **Open in Management Portal**.
2.  En el portal de administración, haga clic en **Panel** y luego en **Reset your deployment credentials** en la sección **Quick Glance**.

	![Restablecer el vínculo a las credenciales de FTP en el panel](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

1.  Escriba un nuevo nombre de usuario y contraseña.

	![Nuevo nombre de usuario y contraseña de FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

1.  En la pestaña **Panel** del portal de administración, presione F5 para actualizar la página y desplácese hacia abajo hasta **Deployment / FTP User**. Observe que el nombre del usuario tiene como prefijo el nombre del sitio. **Cuando inicie sesión, debe utilizar este nombre de usuario completo, con el nombre del sitio como prefijo, tal como aparece aquí.**
2.  En una ventana nueva del explorador, vaya a la dirección URL que aparece bajo **FTP Host Name** en la pestaña **Panel** de la página del portal de administración para su sitio web. **FTP Host Name** está cerca de **Deployment / FTP User** en la sección **Quick Glance**.
3.  Inicie sesión con las credenciales de FTP que creó anteriormente (el nombre de usuario debe incluir como prefijo el nombre del sitio).

	El explorador muestra la carpeta raíz del sitio.

1.  Abra la carpeta *LogFiles*.

	![Abrir la carpeta LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

1.  Abra la carpeta llamada W3SVC más un valor numérico.

	![Abrir la carpeta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	La carpeta contiene archivos XML de todo error que se haya registrado después de haber habilitado el seguimiento de solicitudes con error, además de un archivo XSL que un explorador puede utilizar para dar formato al XML.

	![Carpeta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

1.  Haga clic en el archivo XML de la solicitud con error para la que desea ver información de seguimiento.

	La siguiente ilustración muestra parte de la información de seguimiento de un error de muestra.

	![Seguimiento de solicitudes con error en un explorador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

<h2><a name="nextsteps"></a><span class="short-header">Next Steps</span>Pasos siguientes</h2>

Ha visto cómo Visual Studio facilita la visualización de registros creados por un sitio web de Azure. Es posible que desee obtener más información acerca de cómo solucionar problemas en los sitios web de Azure, realizar el seguimiento en aplicaciones de ASP.NET y analizar registros de servidor web.

**Solución de problemas del sitio web de Azure**

Para obtener más información acerca de la solución de problemas en Sitios web Azure (WAWS), consulte los siguientes recursos:

-   [Solución de problemas en Azure](http://azure.microsoft.com/en-us/develop/net/best-practices/troubleshooting/)
     Una introducción básica que incluye una breve sección sobre WAWS.
-   [Solución de problemas de un sitio web](http://azure.microsoft.com/en-us/develop/net/best-practices/troubleshooting-web-sites/)
     Una introducción que se centra en WAWS.
-   [Habilitación del registro de diagnóstico para Sitios web Azure](http://azure.microsoft.com/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
     Abarca gran parte de la información que se ofrece en este tutorial, pero se centra en cómo obtener registros de diagnóstico sin utilizar Visual Studio.
-   [Supervisión de sitios web](http://azure.microsoft.com/en-us/manage/services/web-sites/how-to-monitor-websites/)
     La sección de [configuración de diagnóstico y descarga de registros](http://azure.microsoft.com/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics) contiene información valiosa que no está incluida en los documentos para la solución de problemas.

Si desea obtener ayuda para una pregunta específica sobre la solución de problemas, comience una conversación en alguno de los siguientes foros:

-   [El foro sobre Azure en el sitio de ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET) (en inglés).
-   [El foro de Azure en MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/) (en inglés).
-   [StackOverflow.com](http://www.stackoverflow.com/).

### Depuración en Visual Studio

Para obtener más información acerca del uso del modo de depuración en Visual Studio, consulte el tema [Depurar en Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx) de MSDN y [Debugging Tips with Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

Si su sitio web utiliza una API web de Azure o el back-end de Servicios móviles y necesita depurarlo, consulte [Debugging .NET Backend in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Seguimiento en aplicaciones de ASP.NET

No hay introducciones rigurosas ni actualizadas al seguimiento de ASP.NET disponibles en Internet. Lo mejor que puede hacer es comenzar con materiales de introducción antiguos escritos para Web Forms, porque MVC todavía no existía, y complementarlos con las publicaciones más recientes en blogs que se centran en temas específicos. Algunos buenos sitios para comenzar son los siguientes recursos:

-   [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).
     Capítulo de un libro electrónico con recomendaciones para realizar seguimiento en aplicaciones de la nube de Azure.
-   [ASP.NET Tracing](http://msdn.microsoft.com/en-us/library/ms972204.aspx)
     Un recurso antiguo, pero siempre útil, con una introducción básica al tema.
-   [Agentes de escucha de seguimiento](http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx)
     Información acerca de las escuchas de seguimiento, pero no menciona [WebPageTraceListener](http://msdn.microsoft.com/en-us/library/system.web.webpagetracelistener.aspx).
-   [Tutorial: Integrar el seguimiento en ASP.NET con las técnicas de seguimiento de System.Diagnostics](http://msdn.microsoft.com/en-us/library/b0ectfxd.aspx)
     También antiguo, pero incluye cierta información adicional que el artículo de introducción no incluye.
-   [Tracing in ASP.NET MVC Razor Views](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)
     Además del seguimiento en vistas de Razor, la publicación también explica cómo crear un filtro de error para registrar todas las excepciones no manejadas en una aplicación MVC. Si desea obtener información sobre cómo registrar todas las excepciones no controladas en una aplicación de Web Forms, consulte el ejemplo Global.asax en [Ejemplo completo de controladores de errores](http://msdn.microsoft.com/en-us/library/bb397417.aspx) en MSDN. Ya sea en MVC o en Web Forms, si desea registrar ciertas excepciones, pero permitir que la gestión del marco predeterminado las afecte, puede volver a intentarlo como en el siguiente ejemplo:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

-   [Streaming Diagnostics Trace Logging from the Azure Command Line (plus Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)
     Uso de la línea de comandos para hacer lo que muestra este tutorial en Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) es una herramienta de depuración de aplicaciones ASP.NET.
-   [Using Azure Web Site Logging and Diagnostics, con David Ebbo](http://www.windowsazure.com/en-us/documentation/videos/azure-web-site-logging-and-diagnostics/) y [Streaming Logs from Azure Web Sites, con David Ebbo](http://www.windowsazure.com/en-us/documentation/videos/log-streaming-with-azure-web-sites/)
     Vídeos de Scott Hanselman y David Ebbo.

Para el registro de errores, una alternativa a escribir su propio código de seguimiento es utilizar un marco de registro de código abierto, como [ELMAH](http://nuget.org/packages/elmah/). Para obtener más información, consulte [Scott Hanselman's blog posts about ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Además, tenga en cuenta que no tiene que utilizar el seguimiento de System.Diagnostics o ASP.NET si desea obtener registros de streaming de Azure. El servicio de registros de streaming del sitio web de Azure transmitirá todo archivo *.txt*, *.html* o *.log* que encuentre en la carpeta *LogFiles*. Por lo tanto, podría crear su propio sistema de registro que escribe en el sistema de archivos del sitio web y su archivo se transmitirá y descargará automáticamente. Todo lo que tiene que hacer es escribir el código de aplicación que crea archivos en la carpeta *d:\\home\\logfiles*.

### Análisis de registros de servidor web

Para obtener más información acerca del análisis de registros de servidor web, consulte los siguientes recursos:

-   [LogParser](http://www.microsoft.com/en-us/download/details.aspx?id=24659)
     Una herramienta para visualizar datos en registros de servidor web (archivos *.log*).
-   [Troubleshooting IIS Performance Issues or Application Errors using LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)
     Una introducción a la herramienta de analizador del registro que puede utilizar para analizar registros de servidor web.
-   [Publicaciones en el blog de Robert McMurray sobre el uso de LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)
-   [El código de estado HTTP en IIS 7.0, IIS 7.5 y IIS 8.0](http://support.microsoft.com/kb/943891)

### Análisis de registros de seguimiento de solicitudes con error

El sitio web de Microsoft TechNet incluye una sección llamada [Using Failed Request Tracing](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que puede ser muy práctica para comprender cómo utilizar estos registros. Sin embargo, esta documentación se centra principalmente en la configuración del seguimiento de solicitudes con error en IIS, algo que no puede hacer en Sitios web Azure.

### Depuración de Servicios en la nube

Si desea depurar un servicio en la nube de Azure en lugar de un sitio web, consulte [Depurar servicio en la nube](http://msdnstage.redmond.corp.microsoft.com/en-us/library/windowsazure/ee405479.aspx).

 
[GetStarted]: /en-us/develop/net/tutorials/get-started/

