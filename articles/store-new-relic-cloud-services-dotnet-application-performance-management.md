<!-- saved from url=(0022)http://internet.e-mail -->

Administración del rendimiento de la aplicación New Relic en Azure
==================================================================

En esta guía se describe cómo agregar la supervisión del rendimiento de primer nivel de New Relic a sus aplicaciones hospedadas en Azure. Veremos cómo agregar de forma fácil y sencilla New Relic a la aplicación y le mostraremos algunas de las características de New Relic. Para obtener más información sobre el uso de New Relic, consulte [Uso de New Relic](#using-new-relic).

¿Qué es New Relic?
------------------

New Relic es una herramienta para desarrolladores que supervisa las aplicaciones de producción y proporciona un conocimiento profundo de su rendimiento y confiabilidad. Está diseñado para ahorrar tiempo al identificar y diagnosticar problemas de rendimiento, poniendo al alcance de la mano toda la información necesaria para solucionar estos problemas.

New Relic realiza el seguimiento del tiempo de carga y de la capacidad de proceso de su transacción web, tanto para los exploradores del servidor como de los usuarios. Muestra cuánto tiempo pasa en la base de datos, analiza las consultas lentas y las solicitudes web, proporciona supervisión y alertas del tiempo de actividad, realiza el seguimiento de excepciones de la aplicación y mucho más.

Precio especial de New Relic en la Tienda de Azure
--------------------------------------------------

Los usuarios de Azure pueden obtener la versión estándar de New Relic de manera gratuita, mientras que New Relic Pro se ofrece por tamaño de instancia para los Servicios en la nube de Azure.

Para obtener información sobre el precio, consulte la [página de New Relic en la Tienda de Azure](http://www.windowsazure.com/en-us/gallery/store/new-relic/new-relic/).

**Nota:**

El precio solo se muestra para 10 equipos como máximo. Para cantidades mayores, póngase en contacto con New Relic (sales@newrelic.com) para obtener información del precio por volumen.

Los clientes de Azure reciben una suscripción de prueba a New Relic Pro de dos semanas de duración cuando implementan el agente de New Relic.

Suscripción en New Relic mediante el uso de la Tienda de Azure
--------------------------------------------------------------

New Relic se integra perfectamente con los roles web y de trabajo de Azure.

Para suscribirse directamente a New Relic desde la Tienda de Azure, siga estos tres sencillos pasos.

### Paso 1. Suscribirse mediante la Tienda de Azure

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.  En el panel inferior del portal de administración, haga clic en **New**.
3.  Haga clic en **Store**.
4.  En el cuadro de diálogo **Choose an Add-on**, seleccione **New Relic** y haga clic en **Next**.
5.  En el cuadro de diálogo **Personalize Add-on**, seleccione el nuevo plan de New Relic que desee.
6.  Especifique, si procede, un código de promoción.
7.  Indique el nombre que desea para el servicio de New Relic en la configuración de Azure o utilice el valor predeterminado de **NewRelic**. El nombre debe ser único en la lista de elementos suscritos de la Tienda de Azure.
8.  Elija un valor para la región; por ejemplo, **Oeste de EE. UU.**.
9.  Haga clic en **Next**.
10. En el cuadro de diálogo **Review Purchase**, revise la información del plan y de los precios, y consulte los términos legales. Si acepta los términos, haga clic en **Purchase**.
11. Después de haber hecho clic en **Purchase**, comenzará el proceso de creación de la cuenta de New Relic. Puede supervisar el estado en el Portal de administración de Azure.
12. Para recuperar la clave de licencia de New Relic, haga clic en **Output Values**.
13. Copie la clave de licencia que se muestra. Tendrá que escribirla cuando instale el paquete New Relic Nuget.

### Paso 2. Instalar el paquete Nuget

1.  Abra la solución de Visual Studio o cree una nueva seleccionando **Archivo \> Nuevo \> Proyecto**.

    ![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2.  Si todavía no tiene un proyecto de Servicios en la nube de Azure en la solución, agregue uno haciendo clic con el botón secundario en la aplicación en el Explorador de soluciones y seleccione **Add Azure Cloud Service Project**.

    ![Crear un servicio en la nube](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3.  Abra la consola del Administrador de paquetes seleccionando **Tools \> Library Package Manager \> Package Manager Console**. Establezca su proyecto para que sea el predeterminado en la parte superior de la ventana de la Consola del Administrador de paquetes.

    ![Consola del Administrador de paquetes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4.  En el símbolo del sistema del Administrador de paquetes, escriba `Install-Package NewRelicWindowsAzure` y presione **Entrar**.

    ![Instalar el Administrador de paquetes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5.  Cuando se le solicite la clave de licencia, escriba la clave que ha recibido de la Tienda de Azure.

    ![Introducción de clave de licencia](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6.  Opcional: en el aviso del nombre de aplicación, escriba el nombre de la aplicación tal como aparecerá en el panel de New Relic. O bien, utilice su nombre de solución como valor predeterminado.

    ![Escribir el nombre de aplicación](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7.  Desde el Explorador de soluciones, haga clic con el botón secundario en su proyecto de Servicios en la nube de Azure y seleccione **Publish**.

    ![Publicar el proyecto en la nube](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)

**Nota:** si es la primera vez que implementa esta aplicación en Azure, se le pedirá que escriba las credenciales correspondientes. Para obtener más información, consulte [Implementación de una aplicación web ASP.NET en un sitio web de Azure](/en-us/develop/net/tutorials/get-started/).

![configuración de publicación](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Paso 3. Comprobar el rendimiento de la aplicación en New Relic

Para ver el panel de New Relic:

1.  En el portal de Azure, haga clic en el botón **Manage**.
2.  Inicie sesión con el correo electrónico y la contraseña de la cuenta de New Relic.
3.  En la barra de menús de New Relic, seleccione **Applications \> (nombre de la aplicación)**.

    El panel **Monitoring \> Overview** se muestra automáticamente.

    ![Panel de supervisión de New Relic](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

    Después de seleccionar una aplicación en la lista en el menú **Applications**, el panel **Overview** muestra información del explorador y del servidor de aplicaciones actual.

### Uso de New Relic

Después de seleccionar la aplicación de la lista en el menú Applications, el panel Overview muestra información actual del explorador y del servidor de aplicaciones. Para alternar entre las dos vistas, haga clic en el botón **App server** o **Browser**.

Además de las funciones de [la interfaz de usuario estándar de New Relic](https://newrelic.com/docs/site/the-new-relic-ui#functions) y la [información detallada del panel](https://newrelic.com/docs/site/the-new-relic-ui#drilldown), el panel Applications Overview muestra funciones adicionales.

<table  border="1">
  <thead>
    <tr>
      <th><b>Si desea...</b>
</th>

      <th><b>Haga esto...</b>
</th>

    </tr>

  </thead>

  <tbody>
    <tr>
       <td>Mostrar la información del panel para el explorador o servidor de aplicaciones seleccionado</td>

       <td>Haga clic en el botón <b>App Server</b>
 o <b>Browser</b>
.</td>

    </tr>

     <tr>
       <td>Ver los niveles de umbral para la puntuación de <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a>
 de la aplicación</td>

       <td>Seleccione el icono <b>?<b> de la puntuación de Apdex.</b>
</b>
 </td>

    </tr>

    <tr>
       <td>Ver los detalles de Apdex globales</td>

       <td>Desde la vista <b>Browser</b>
 de Overview, seleccione el mapa Global Apdex.<br  />
<b>Sugerencia:</b>
 para ir directamente al panel <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a>
 de la aplicación seleccionada, haga clic en el título <b>Global Apdex</b>
 o haga clic en cualquier lugar del mapa Global Apdex.</td>

    </tr>

    <tr>
       <td>Ver el panel <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a>
</td>

       <td>Haga clic en la tabla Web Transactions en el panel Applications Overview. O bien, para ver los detalles sobre una transacción web específica (incluyendo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>
), haga clic en su nombre.</td>

    </tr>

    <tr>
       <td>Ver el panel <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a>
</td>

       <td>Haga clic en el título del gráfico de tasas de error en el panel Applications Overview.<br  />
<b>Sugerencia:</b>
 también puede ver el panel Errors en <b>Applications</b>
 > (su aplicación) > Events > Errors.</td>

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

       </p>
</p>
</td>

    </tr>

  </tbody>

</table>

A continuación se muestra un ejemplo del panel Applications Overview cuando se selecciona la vista de explorador.

![Consola del Administrador de paquetes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

Pasos siguientes
----------------

Consulte estos recursos adicionales para obtener más información:

-   [Installing the .NET Agent on Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): Procedimientos de instalación de .NET Agent de New Relic.
-   [The New Relic User Interface](https://newrelic.com/docs/site/the-new-relic-ui): Información general de la nueva interfaz de usuario de New Relic, configuración de derechos de usuario y perfiles, uso de funciones estándar e información detallada del panel.
-   [Applications Overview](https://newrelic.com/docs/site/applications-overview): Características y funciones cuando se utiliza el panel Applications Overview de New Relic.
-   [Apdex](https://newrelic.com/docs/site/apdex): Información general de cómo Apdex mide la satisfacción de los usuarios finales con la aplicación.
-   [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Introducción general a cómo RUM detalla el tiempo que tardan los exploradores de los usuarios en cargar las páginas web, de dónde proceden y qué exploradores utilizan.
-   [Finding Help](https://newrelic.com/docs/site/finding-help): Recursos disponibles en el centro de ayuda en línea de New Relic.

