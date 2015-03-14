<properties 
	pageTitle="Administración del rendimiento de la aplicación New Relic en Azure" 
	description="Aprenda a utilizar la supervisión del rendimiento de New Relic en Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>



#Administración del rendimiento de la aplicación New Relic en sitios web de Azure

En esta guía se describe cómo agregar la supervisión del rendimiento de primer nivel de New Relic en su sitio web de Azure. Se verá cómo agregar de forma fácil y sencilla New Relic a la aplicación y se mostrarán algunas de las
nuevas características de New Relic. Para obtener más información sobre el uso de New Relic, consulte [Uso de New Relic)]#using-new-relic).

¿Qué es New Relic?
--

New Relic es una herramienta para desarrolladores que supervisa las aplicaciones de producción y proporciona un conocimiento profundo de su rendimiento y confiabilidad. Está diseñado para ahorrar tiempo al identificar y diagnosticar problemas de rendimiento, poniendo al alcance de la mano toda la información necesaria para solucionar estos problemas.

New Relic realiza el seguimiento del tiempo de carga y de la capacidad de proceso de su transacción web, tanto para los exploradores del servidor como de los usuarios. Muestra cuánto tiempo pasa en la base de datos, analiza las consultas lentas y las solicitudes web, proporciona supervisión y alertas del tiempo de actividad, realiza el seguimiento de excepciones de la aplicación y mucho más.

Precio especial de New Relic en la Tienda de Azure
--

La versión estándar de New Relic es gratuita para usuarios de Azure.
La versión profesional de New Relic se ofrece en varios paquetes basados en el modo de sitio web que use y el tamaño de instancia si está usando el modo reservado.

Para obtener información sobre el precio, consulte la [página de New Relic en la Tienda de Azure](http://azure.microsoft.com/gallery/store/new-relic/new-relic/).

> [AZURE.NOTE] El precio solo se muestra para 10 equipos como máximo. Para cantidades mayores, póngase en contacto con New Relic (sales@newrelic.com) para obtener información del precio por volumen.

Los clientes de Azure reciben una suscripción de prueba a New Relic Pro de dos semanas de duración cuando implementan el agente de New Relic.

Suscripción en New Relic mediante el uso de la Tienda de Azure
--

New Relic se integra perfectamente con los roles web y de trabajo y los sitios web de Azure.

Para suscribirse directamente a New Relic desde la Tienda de Azure, siga estos cuatro sencillos pasos.

### Paso 1. Suscribirse a través de la Tienda de Azure

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. En el panel inferior del portal de administración, haga clic en **New**.
3. Haga clic en **Store**.
4. En el cuadro de diálogo **Choose an Add-on**, seleccione **New Relic** y haga clic en **Next**.
5. En el cuadro de diálogo **Personalize Add-on**, seleccione el nuevo plan de New Relic que desee.
7. Escriba el nombre que quiera para el servicio de New Relic en la configuración de Azure o use el valor predeterminado de **NewRelic**. El nombre debe ser único en la lista de elementos suscritos de la Tienda de Azure.
8. Elija un valor para la región; por ejemplo, **Oeste de EE. UU.**
9. Haga clic en **Next**.
10. En el cuadro de diálogo **Review Purchase**, revise la información del plan y de los precios, y consulte los términos legales. Si acepta los términos, haga clic en **Purchase**.
11. Después de haber hecho clic en **Purchase**, comenzará el proceso de creación de la cuenta de New Relic. Puede supervisar el estado en el Portal de administración de Azure.
12. Para recuperar la clave de licencia de New Relic, haga clic en el complemento que acaba de crear y, a continuación, haga clic en **Información de conexión**. 
13. Copie la clave de licencia que se muestra. Tendrá que escribirla cuando instale el paquete New Relic Nuget.

### Paso 2. Instalar el paquete New Relic

El Agente de sitios web de New Relic se distribuye como un paquete NuGet, que puede agregarse al sitio web mediante Visual Studio o WebMatrix. Si no tiene experiencia en el uso de Visual Studio o WebMatrix con un sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure con Visual Studio][vswebsite] o [Desarrollo e implementación de un sitio web con Microsoft WebMatrix][webmatrixwebsite].

Realice los siguientes pasos para el entorno de desarrollo específico que está usando:

**Visual Studio**

1. Abra la solución del sitio web de Visual Studio.

2. Abra la consola del Administrador de paquetes seleccionando **Tools > Library Package Manager > Package Manager Console**. Establezca su proyecto para que sea el predeterminado en la parte superior de la ventana de la Consola del Administrador de paquetes.

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. En el símbolo del sistema del Administrador de paquetes, use el siguiente comando para instalar el paquete:

		Install-Package NewRelic.Azure.WebSites

4. Cuando se le solicite la clave de licencia, escriba la clave que ha recibido de la Tienda de Azure.

	![enter license key][vslicensekey]

<!--5. Opcional: en el aviso del nombre de aplicación, escriba el nombre de la aplicación tal como aparecerá en el panel de New Relic. O bien, utilice su nombre de solución como valor predeterminado.

	![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Abra el sitio web con WebMatrix.

2. En la pestaña **Inicio** de la cinta de opciones, seleccione **NuGet**.

	![nuget buton on home tab][wmnugetbutton]

3. En la galería de NuGet Gallery, establezca el origen en **NuGet Official Package Source** (Origen del paquete oficinal de NuGet) y, a continuación, busque NewRelic.Azure.WebSites.

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. Seleccione la entrada **New Relic for Azure Websites** (New Relic para Sitios web Azure) y, a continuación, haga clic en **Install** (Instalar).

5. Después de instalar el paquete, el sitio contendrá una carpeta con el nombre **newrelic**. Expanda esta carpeta y abra el archivo **newrelic.config**. En este archivo, reemplace el valor **REPLACE\_WITH\_LICENSE_KEY** por la clave de licencia recibida de la Tienda de Azure.

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	Después de agregar la información de la clave de licencia, guarde los cambios en el archivo **newrelic.config**.

### Paso 3. Configurar el sitio web y publicar la aplicación.

El paquete New Relic agregado a la aplicación en el paso anterior se configura mediante la **configuración de la aplicación** agregada al sitio web de Azure. Realice los siguientes pasos y agregue esta configuración.

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) y diríjase al sitio web.

2. En el sitio web, seleccione **Configurar**. En la sección **Developer Analytics**, seleccione **Add-on** o **Custom**. Ambos métodos producen el mismo resultado, pero requieren una entrada ligeramente distinta. **Complemento** enumera las licencias de New Relic actuales y le permite seleccionar una, mientras que **Personalizar** le solicita que especifique manualmente la clave de licencia.

	Si ha seleccionado **Complemento**, use el campo **Elegir complemento** para seleccionar la licencia de New Relic.

	![Image of the add-on fields][add-on]

	Si ha seleccionado **Personalizar**, use el New Relic seleccionado como **Proveedor** y, a continuación, especifique la licencia en el campo **Clave de proveedor**.

	![Image of the custom fields][custom]

3. Después de especificar una licencia en **Análisis del desarrollador**, haga clic en **Guardar**. Una vez que la operación de guardado se haya completado, se agregarán los siguientes valores a la sección **Configuración de la aplicación** de la página para admitir New Relic:

	<table border="1">
	<thead>
	<tr>
	<td>Clave</td>
	<td>Valor</td>
	</tr>
	</thead>
	<tbody>
	<tr>
	<td>COR\_ENABLE\_PROFILING</td><td>1</td>
	</tr>
	<tr>
	<td>COR\_PROFILER</td><td>{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr>
	<td>COR\_PROFILER\_PATH</td><td>C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr>
	<td>NEWRELIC\_HOME</td><td>C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr>
	<td>NEWRELIC\_LICENSEKEY</td><td>Clave de licencia</td>
	</tr>
	</tbody>
	</table><br/>

	> [AZURE.NOTE] Se puede tardar hasta 30 segundos en que la nueva <strong>Configuración de aplicaciones</strong> surta efecto. Para forzar la configuración a fin de que surta efecto de inmediato, reinicie el sitio web.


4. Use Visual Studio o WebMatrix para publicar la aplicación.

### Paso 4. Comprobar el rendimiento de la aplicación en New Relic.

Para ver el panel de New Relic:

1. En el portal de Azure, haga clic en el botón **Administrar**.
2. Inicie sesión con el correo electrónico y la contraseña de la cuenta de New Relic.
3. En la barra de menús de New Relic, seleccione **Applications > (nombre de la aplicación)**.

	El panel **Monitoring > Overview** se muestra automáticamente.

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	Después de seleccionar una aplicación en la lista en el menú **Applications**, el panel **Overview** muestra información del explorador y del servidor de aplicaciones actual.

### <a id="using-new-relic"></a>Uso de New Relic

Después de seleccionar la aplicación de la lista en el menú Applications, el panel Overview muestra información actual del explorador y del servidor de aplicaciones. Para alternar entre las dos vistas, haga clic en el botón **App server** o **Browser**.

Además de las funciones de <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">la interfaz de usuario estándar de New Relic</a> y la <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">información detallada del panel</a>, el panel Applications Overview muestra funciones adicionales.

<table border="1">
  <thead>
    <tr>
      <th><b>Si desea...</b></th>
      <th><b>Haga esto...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Mostrar la información del panel para el explorador o servidor de aplicaciones seleccionado</td>
       <td>Haga clic en el botón <b>App Server</b> o <b>Browser</b>.</td>
    </tr>
     <tr>
       <td>Ver los niveles de umbral para la puntuación de <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de su aplicación</td>
       <td>Seleccione el icono <b>?<b> de la puntuación de Apdex.</b></b></td>
    </tr>
    <tr>
       <td>Ver los detalles de Apdex globales</td>
       <td>Desde la vista de <b>explorador</b> de la información general, seleccione el mapa Global Apdex.<br /><b>Sugerencia:</b> para ir directamente al panel <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> de la aplicación seleccionada, haga clic en el título <b>Global Apdex</b> o haga clic en cualquier lugar del mapa Global Apdex.</td>
    </tr>
    <tr>
       <td>Ver el panel <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
       <td>Haga clic en la tabla Web Transactions en el panel Applications Overview. O bien, para ver los detalles sobre una transacción web específica (incluyendo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>), haga clic en su nombre.</td>
    </tr>
    <tr>
       <td>Ver el panel <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td>Haga clic en el título del gráfico de tasas de error en el panel Applications Overview.<br /><b>Sugerencia:</b> también puede ver el panel de errores en <b>Applications</b> &gt; (su aplicación) &gt; eventos &gt; errores.</td>
    </tr>
    <tr>
       <td>Ver los detalles del servidor de aplicaciones</td>
       <td><p>Realice una de las operaciones siguientes:<p>
        <ul>
          <li>Alterne entre una vista de tabla de los hosts o desglose los detalles de las métricas de cada host.</li>
          <li>Haga clic en un nombre de servidor individual.</li>
          <li>Seleccione una puntuación Apdex del servidor individual.</li>
          <li>Haga clic en una memoria o uso de CPU del servidor individual.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

A continuación se muestra un ejemplo del panel Applications Overview cuando se selecciona la vista de explorador.

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Pasos siguientes

Consulte estos recursos adicionales para obtener más información:

 * [Installing the .NET Agent for Azure Web Sites](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): procedimientos de instalación de .NET Agent de New Relic. 
 * [The New Relic User Interface](https://newrelic.com/docs/site/the-new-relic-ui): 
información general de la nueva interfaz de usuario de New Relic, configuración de derechos de usuario y perfiles, uso de funciones estándar e información detallada del panel.
 * [Applications Overview](https://newrelic.com/docs/site/applications-overview): características y funciones cuando se utiliza el panel Applications Overview de New Relic.
 * [Apdex](https://newrelic.com/docs/site/apdex): información general de cómo Apdex mide la satisfacción de los usuarios finales con la aplicación.
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): información general sobre cómo RUM detalla el tiempo que tardan los exploradores de los usuarios en cargar las páginas web, de dónde proceden y qué exploradores usan.
 * [Finding Help](https://newrelic.com/docs/site/finding-help): recursos disponibles en el centro de ayuda en línea de New Relic.


[webmatrixwebsite]: http://azure.microsoft.com/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://azure.microsoft.com/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png






<!--HONumber=42-->
