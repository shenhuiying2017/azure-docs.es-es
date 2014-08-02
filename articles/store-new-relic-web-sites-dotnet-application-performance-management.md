<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Web Sites" authors="larryfr" solutions="" manager="" editor="" />

Administración del rendimiento de la aplicación New Relic en Sitios web Azure
=============================================================================

En esta guía se describe cómo agregar la supervisión del rendimiento de primer nivel de New Relic en su sitio web de Azure. Veremos cómo agregar de forma fácil y sencilla New Relic a la aplicación y le mostraremos algunas de las características de New Relic. Para obtener más información sobre el uso de New Relic, consulte [Uso de New Relic](#using-new-relic).

¿Qué es New Relic?
------------------

New Relic es una herramienta para desarrolladores que supervisa las aplicaciones de producción y proporciona un conocimiento profundo de su rendimiento y confiabilidad. Está diseñado para ahorrar tiempo al identificar y diagnosticar problemas de rendimiento, poniendo al alcance de la mano toda la información necesaria para solucionar estos problemas.

New Relic realiza el seguimiento del tiempo de carga y de la capacidad de proceso de su transacción web, tanto para los exploradores del servidor como de los usuarios. Muestra cuánto tiempo pasa en la base de datos, analiza las consultas lentas y las solicitudes web, proporciona supervisión y alertas del tiempo de actividad, realiza el seguimiento de excepciones de la aplicación y mucho más.

Precio especial de New Relic en la Tienda de Azure
--------------------------------------------------

La versión estándar de New Relic es gratuita para usuarios de Azure. La versión profesional de New Relic se ofrece en varios paquetes basados en el modo de sitio web que use y el tamaño de instancia si está usando el modo reservado.

Para obtener información sobre el precio, consulte la [página de New Relic en la Tienda de Azure](http://www.windowsazure.com/en-us/gallery/store/new-relic/new-relic/).

**Nota:**

El precio solo se muestra para 10 equipos como máximo. Para cantidades mayores, póngase en contacto con New Relic (sales@newrelic.com) para obtener información del precio por volumen.

Los clientes de Azure reciben una suscripción de prueba a New Relic Pro de dos semanas de duración cuando implementan el agente de New Relic.

Suscripción en New Relic mediante el uso de la Tienda de Azure
--------------------------------------------------------------

New Relic se integra perfectamente con los roles web y de trabajo y los sitios web de Azure.

Para suscribirse directamente a New Relic desde la Tienda de Azure, siga estos cuatro sencillos pasos.

### Paso 1. Suscribirse mediante la Tienda de Azure

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.  En el panel inferior del portal de administración, haga clic en **New**.
3.  Haga clic en **Store**.
4.  En el cuadro de diálogo **Choose an Add-on**, seleccione **New Relic** y haga clic en **Next**.
5.  En el cuadro de diálogo **Personalize Add-on**, seleccione el nuevo plan de New Relic que desee.
6.  Indique el nombre que desea para el servicio de New Relic en la configuración de Azure o utilice el valor predeterminado de **NewRelic**. El nombre debe ser único en la lista de elementos suscritos de la Tienda de Azure.
7.  Elija un valor para la región; por ejemplo, **Oeste de EE. UU.**
8.  Haga clic en **Next**.
9.  En el cuadro de diálogo **Review Purchase**, revise la información del plan y de los precios, y consulte los términos legales. Si acepta los términos, haga clic en **Purchase**.
10. Después de haber hecho clic en **Purchase**, comenzará el proceso de creación de la cuenta de New Relic. Puede supervisar el estado en el Portal de administración de Azure.
11. Para recuperar la clave de licencia de New Relic, haga clic en el complemento que acaba de crear y, a continuación, haga clic en **Información de conexión**.
12. Copie la clave de licencia que se muestra. Tendrá que escribirla cuando instale el paquete New Relic Nuget.

### Paso 2. Instalar el paquete New Relic

El Agente de sitios web de New Relic se distribuye como un paquete NuGet, que puede agregarse al sitio web mediante Visual Studio o WebMatrix. Si no tiene experiencia en el uso de Visual Studio o WebMatrix con el sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure con Visual Studio](http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/) o [Desarrollo e implementación de un sitio web con Microsoft WebMatrix](http://www.windowsazure.com/en-us/develop/net/tutorials/website-with-webmatrix/).

Realice los siguientes pasos para el entorno de desarrollo específico que está usando:

**Visual Studio**

1.  Abra la solución del sitio web de Visual Studio.

2.  Abra la consola del Administrador de paquetes seleccionando **Tools \> Library Package Manager \> Package Manager Console**. Establezca su proyecto para que sea el predeterminado en la parte superior de la ventana de la Consola del Administrador de paquetes.

    ![Consola del Administrador de paquetes](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3.  En el símbolo del sistema del Administrador de paquetes, use el siguiente comando para instalar el paquete:

         Install-Package NewRelic.Azure.WebSites

4.  Cuando se le solicite la clave de licencia, escriba la clave que ha recibido de la Tienda de Azure.

    ![Introducción de clave de licencia](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png)

**WebMatrix**

1.  Abra el sitio web con WebMatrix.

2.  En la pestaña **Home** de la cinta de opciones, seleccione **NuGet**.

    ![Botón Nuget en la pestaña Home](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png)

3.  En la galería de NuGet Gallery, establezca el origen en **NuGet Official Package Source** y, a continuación, busque NewRelic.Azure.WebSites.

    ![Búsqueda en la galería Nuget de NewRelic.Azure.WebSites](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png)

4.  Seleccione la entrada **New Relic for Azure Web Sites** y, a continuación, haga clic en **Install**.

5.  Después de instalar el paquete, el sitio contendrá una carpeta con el nombre **newrelic**. Expanda esta carpeta y abra el archivo **newrelic.config**. En este archivo, reemplace el valor **REPLACE\_WITH\_LICENSE\_KEY** por la clave de licencia recibida de la Tienda de Azure.

    ![Carpeta newrelic expandida con newrelic.conf seleccionado](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png)

    Después de agregar la información de la clave de licencia, guarde los cambios en el archivo **newrelic.config**.

### Paso 3. Configurar el sitio web y publicar la aplicación

El paquete New Relic agregado a la aplicación en el paso anterior se configura mediante la **configuración de la aplicación** agregada al sitio web de Azure. Realice los siguientes pasos y agregue esta configuración.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) y diríjase al sitio web.

2.  En el sitio web, seleccione **Configure**. En la sección **Análisis del desarrollador**, seleccione **Add-on** o **Custom**. Ambos métodos producen el mismo resultado, pero requieren una entrada ligeramente distinta. **Add-on** enumera las licencias de New Relic actuales y le permite seleccionar una, mientras que **Custom** le solicita que especifique manualmente la clave de licencia.

    Si ha seleccionado **Add-on**, use el campo **Elegir complemento** para seleccionar la licencia de New Relic.

    ![Imagen de los campos de complementos](./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png)

    Si ha seleccionado **Custom**, use el New Relic seleccionado como **Proveedor** y, a continuación, especifique la licencia en el campo **Clave de proveedor**.

    ![Imagen de los campos personalizados](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png)

3.  Después de especificar una licencia en **Análisis del desarrollador**, haga clic en **Save**. Una vez que la operación de guardado se haya completado, se agregarán los siguientes valores a la sección **Configuración de aplicaciones** de la página para admitir New Relic:

  <table data-morhtml="true" border="1">
 <thead data-morhtml="true">
 <tr data-morhtml="true">
 <td data-morhtml="true">Clave</td>
 <td data-morhtml="true">Valor</td>
 </tr>
 </thead>
 <tbody data-morhtml="true">
 <tr data-morhtml="true">
 <td data-morhtml="true">COR\_ENABLE\_PROFILING</td><td data-morhtml="true">1</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">COR\_PROFILER</td><td data-morhtml="true">{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">COR\_PROFILER\_PATH</td><td data-morhtml="true">C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">NEWRELIC\_HOME</td><td data-morhtml="true">C:\Home\site\wwwroot\newrelic</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">NEWRELIC\_LICENSEKEY</td><td data-morhtml="true">Clave de licencia</td>
 </tr>
 </tbody>
 </table>

    **Nota:**

    Se puede tardar hasta 30 segundos en que la nueva **Configuración de aplicaciones** surta efecto. Para forzar la configuración a fin de que surta efecto de inmediato, reinicie el sitio web.

4.  Use Visual Studio o WebMatrix para publicar la aplicación.

### Paso 4. Comprobar el rendimiento de la aplicación en New Relic

Para ver el panel de New Relic:

1.  En el portal de Azure, haga clic en el botón **Manage**.
2.  Inicie sesión con el correo electrónico y la contraseña de la cuenta de New Relic.
3.  En la barra de menús de New Relic, seleccione **Applications \> (nombre de la aplicación)**.

    El panel **Monitoring \> Overview** se muestra automáticamente.

    ![Panel de supervisión de New Relic](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

    Después de seleccionar una aplicación en la lista en el menú **Applications**, el panel **Overview** muestra información del explorador y del servidor de aplicaciones actual.

### Uso de New Relic

Después de seleccionar la aplicación de la lista en el menú Applications, el panel Overview muestra información actual del explorador y del servidor de aplicaciones. Para alternar entre las dos vistas, haga clic en el botón **App server** o **Browser**.

Además de las funciones de [la interfaz de usuario estándar de New Relic](https://newrelic.com/docs/site/the-new-relic-ui#functions) y la [información detallada del panel](https://newrelic.com/docs/site/the-new-relic-ui#drilldown), el panel Applications Overview muestra funciones adicionales.

<table data-morhtml="true" border="1">
  <thead data-morhtml="true">
    <tr data-morhtml="true">
      <th data-morhtml="true"><b data-morhtml="true">Si desea...</b></th>
      <th data-morhtml="true"><b data-morhtml="true">Haga esto...</b></th>
    </tr>
  </thead>
  <tbody data-morhtml="true">
    <tr data-morhtml="true">
       <td data-morhtml="true">Mostrar la informaci&oacute;n del panel para el explorador o servidor de aplicaciones seleccionado</td>
       <td data-morhtml="true">Haga clic en el bot&oacute;n <b data-morhtml="true">App Server</b> o <b data-morhtml="true">Browser</b>.</td>
    </tr>
     <tr data-morhtml="true">
       <td data-morhtml="true">Ver los niveles de umbral para la puntuaci&oacute;n de <a data-morhtml="true" href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de la aplicaci&oacute;n</td>
       <td data-morhtml="true">Seleccione el icono <b data-morhtml="true">?<b data-morhtml="true"> de la puntuaci&oacute;n de Apdex.</b></b> </td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Ver los detalles de Apdex globales</td>
       <td data-morhtml="true">Desde la vista <b data-morhtml="true">Browser</b> de Overview, seleccione el mapa Global Apdex.<br data-morhtml="true" /><b data-morhtml="true">Sugerencia:</b> para ir directamente al panel <a data-morhtml="true" href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> de la aplicaci&oacute;n seleccionada, haga clic en el t&iacute;tulo <b data-morhtml="true">Global Apdex</b> o haga clic en cualquier lugar del mapa Global Apdex.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Ver el panel <a data-morhtml="true" href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a></td>
       <td data-morhtml="true">Haga clic en la tabla Web Transactions en el panel Applications Overview. O bien, para ver los detalles sobre una transacci&oacute;n web espec&iacute;fica (incluyendo <a data-morhtml="true" href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>), haga clic en su nombre.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Ver el panel <a data-morhtml="true" href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td data-morhtml="true">Haga clic en el t&iacute;tulo del gr&aacute;fico de tasas de error en el panel Applications Overview.<br data-morhtml="true" /><b data-morhtml="true">Sugerencia:</b> tambi&eacute;n puede ver el panel Errors en <b data-morhtml="true">Applications</b> &gt; (su aplicaci&oacute;n) &gt; Events &gt; Errors.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Ver los detalles del servidor de aplicaciones</td>
       <td data-morhtml="true"><p data-morhtml="true">Realice una de las operaciones siguientes:<p data-morhtml="true">
        <ul data-morhtml="true">
          <li data-morhtml="true">Alterne entre una vista de tabla de los hosts o desglose los detalles de las m&eacute;tricas de cada host.</li>
          <li data-morhtml="true">Haga clic en un nombre de servidor individual.</li>
          <li data-morhtml="true">Seleccione una puntuaci&oacute;n Apdex del servidor individual.</li>
          <li data-morhtml="true">Haga clic en una memoria o uso de CPU del servidor individual.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

A continuación se muestra un ejemplo del panel Applications Overview cuando se selecciona la vista de explorador.

![Consola del Administrador de paquetes](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

Pasos siguientes
----------------

Consulte estos recursos adicionales para obtener más información:

-   [Installing the .NET Agent for Azure Web Sites](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): procedimientos de instalación de .NET Agent de New Relic.
-   [The New Relic User Interface](https://newrelic.com/docs/site/the-new-relic-ui): información general de la nueva interfaz de usuario de New Relic, configuración de derechos de usuario y perfiles, uso de funciones estándar e información detallada del panel.
-   [Applications Overview](https://newrelic.com/docs/site/applications-overview): características y funciones cuando se utiliza el panel Applications Overview de New Relic.
-   [Apdex](https://newrelic.com/docs/site/apdex): información general de cómo Apdex mide la satisfacción de los usuarios finales con la aplicación.
-   [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): información general sobre cómo RUM detalla el tiempo que tardan los exploradores de los usuarios en cargar las páginas web, de dónde proceden y qué exploradores utilizan.
-   [Finding Help](https://newrelic.com/docs/site/finding-help): recursos disponibles en el centro de ayuda en línea de New Relic.

