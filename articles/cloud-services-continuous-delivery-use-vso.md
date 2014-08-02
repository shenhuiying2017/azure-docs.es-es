<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure web sites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="" editor="" />

Entrega continua a Azure con Visual Studio Online
=================================================

Visual Studio Online (anteriormente denominado Team Foundation Service) es una versión del servicio hospedada en la nube del popular software Team Foundation Server (TFS) de Microsoft que ofrece código fuente altamente personalizable y administración de compilaciones, ágil desarrollo y flujo de trabajo de proceso de equipo, seguimiento de problemas y elementos de trabajo, entre otras características. Puede configurar los proyectos de equipo de Visual Studio Online para que se compilen y se implementen automáticamente en los sitios web Azure o en los servicios en la nube. Para obtener información acerca de cómo configurar un sistema de compilación e implementación continuas con Team Foundation Server local, consulte [Entrega continua para Servicios en la nube de Azure](../cloud-services-dotnet-continuous-delivery).

En este tutorial se supone que tiene instalados Visual Studio 2013 y el SDK de Azure. Si todavía no tiene Visual Studio 2013, descárguelo; para ello, haga clic en el vínculo **Empiece de manera gratuita** en [www.visualstudio.com](http://www.visualstudio.com). Instale el SDK de Azure desde [aquí](http://go.microsoft.com/fwlink/?LinkId=239540).

Para configurar un servicio en la nube que se compile e implemente automáticamente en Azure con Visual Studio Online, siga los pasos que aparecen a continuación:

-   [Paso 1: Suscríbase a Visual Studio Online](#step1)

-   [Paso 2: Registre un proyecto para el control de código fuente](#step2)

-   [Paso 3: Conecte el proyecto a Azure](#step3)

-   [Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar](#step4)

-   [Paso 5: Vuelva a implementar una compilación anterior (opcional)](#step5)

-   [Paso 6: Cambie la implementación de producción (solo servicios en la nube)](#step6)

Suscripción a Visual Studio OnlinePaso 1: Suscríbase a Visual Studio Online
---------------------------------------------------------------------------

1.  Cree una cuenta de Visual Studio Online; para ello, vaya a <http://www.visualstudio.com>. Haga clic en el vínculo **Iniciar sesión**. Tendrá que iniciar sesión con una cuenta Microsoft. Si inicia sesión por primera vez, se le pedirá que proporcione información personal, como el nombre y la dirección de correo electrónico. 
![](./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG)

2.  Si no es la primera vez que inicia sesión, verá esta pantalla al hacerlo. Haga clic en el vínculo **Create a free account now**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG)

3.  Cree una dirección URL de la cuenta para el proyecto nuevo. La cuenta tendrá el formato siguiente: https://&lt;accountname\>.visualstudio.com.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG)

4.  Ahora podrá crear el primer proyecto. Especifique el nombre y la descripción del proyecto. Elija el sistema de control de versión que desee usar. Se admiten tanto el control de versiones de Team Foundation (TFVC) como Git. Puede obtener más información acerca de estas opciones en [Usar el control de versiones](http://go.microsoft.com/fwlink/?LinkId=324037). En este tutorial se supone que usa TFVC. A continuación, elija la plantilla de proceso que utilice la organización y presione el botón **Crear proyecto**. Para obtener más información acerca de las plantillas de proceso, consulte [Trabajar con artefactos de proyecto de equipo y elegir una plantilla de proceso](http://go.microsoft.com/fwlink/?LinkId=324035).<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs1.png)

5.  Cuando haya terminado la creación de un proyecto, haga clic en el botón **Abrir con Visual Studio para conectar** para iniciar automáticamente Visual Studio conectado al proyecto de equipo. Si aparece un cuadro de diálogo de seguridad, seleccione Permitir.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs2.png)

Registro de un proyecto para el control de código fuentePaso 2: Registre un proyecto para el control de código fuente
---------------------------------------------------------------------------------------------------------------------

1.  En Visual Studio, abra la solución que desee implementar o cree una nueva. Puede implementar un sitio web o un servicio en la nube (aplicación de Azure) siguiendo los pasos que se ofrecen en este tutorial. Si desea crear una nueva solución, cree un proyecto de Servicio de nube de Azure o un proyecto de MVC de ASP.NET. Asegúrese de que el proyecto se dirige a .NET Framework 4 o 4.5. Si está creando un proyecto de Servicio de nube, agregue un rol web de MVC de ASP.NET y un rol de trabajo y seleccione una aplicación de Internet para el rol web. Cuando se le solicite, elija **Aplicación de Internet**. Si desea crear un sitio web, seleccione la plantilla de proyecto de aplicación web ASP.NET y, a continuación, MVC. Consulte [Introducción a Azure y ASP.NET](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-get-started/).

2.  Abra el menú contextual de la solución y seleccione **Agregar solución al control de código fuente**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs5.png)

3.  Acepte o cambie los valores predeterminados y seleccione el botón **OK**. Una vez terminado el proceso, aparecerán los iconos de control de código fuente en el Explorador de soluciones.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs6.png)

4.  Abra el menú de acceso directo de la solución y seleccione **Check In**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs7.png)

5.  En el área Cambios pendientes de Team Explorer, escriba un comentario para el registro y seleccione el botón **Check In**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs8.png)

Fíjese en las opciones para incluir o excluir cambios concretos al registrar. Si se excluyen los cambios deseados, haga clic en el vínculo **Include All**.<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs9.png)

Conexión del proyecto a AzurePaso 3: Conecte el proyecto a Azure
----------------------------------------------------------------

1.  Ahora que tiene un proyecto de equipo de VSO con código fuente en él, esta en disposición de conectar el proyecto de equipo a Azure. En el [Portal de Azure](http://manage.windowsazure.com), seleccione el servicio en la nube o el sitio web, o bien cree uno nuevo haciendo clic en el icono + situado en la parte inferior izquierda y seleccionando **Servicio en la nube** o **Sitio web** y, a continuación, **Creación rápida**. Haga clic en el vínculo **Configurar publicación con Visual Studio Online**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs10.png)

2.  En el asistente, escriba el nombre de la cuenta de Visual Studio Online en el cuadro de texto y haga clic en el vínculo **Authorize Now**. Puede que se le solicite que inicie sesión.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs11.png)

3.  En el diálogo emergente de OAuth, seleccione **Accept** para autorizar a Azure para que configure el proyecto de equipo en VSO.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs12.png)

4.  Si la autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos de equipo de Visual Studio Online. Seleccione el nombre del proyecto de equipo que ha creado en los pasos anteriores y presione el botón de la marca de verificación del asistente.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs13.png)

5.  Una vez que el proyecto se haya vinculado, verá instrucciones para registrar los cambios en el proyecto de equipo de Visual Studio Online. La próxima vez que se registre, Visual Studio Online compilará e implementará el proyecto en Azure. Pruebe esto ahora haciendo clic en el vínculo **Proteger en Visual Studio** y, a continuación, en **Launch Visual Studio** (o el botón equivalente de **Visual Studio** situado en la parte inferior de la pantalla del portal).<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs14.png)

Desencadenamiento de una recompilaciónPaso 4: Desencadene una recompilación y vuelva a implementar su proyecto
--------------------------------------------------------------------------------------------------------------

1.  En Team Explorer de Visual Studio, haga clic en el vínculo **Explorador de control de código fuente**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs15.png)

2.  Vaya hasta su archivo de solución y ábralo.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs16.png)

3.  En el Explorador de soluciones, abra un archivo y cámbielo. Por ejemplo, cambie el archivo \_Layout.cshtml de la carpeta Views\\Shared de un rol web de MVC.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs17.png)

4.  Modifique el logotipo del sitio y presione Ctrl+S para guardar el archivo.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs18.png)

5.  En Team Explorer, haga clic en el vínculo **Cambios pendientes**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs19.png)

6.  Escriba un comentario y presione el botón **Check In**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs20.png)

7.  Presione el botón Inicio para volver a la página principal de Team Explorer.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs21.png)

8.  Haga clic en el vínculo **Compilaciones** para ver las compilaciones en curso.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs22.png)
<br/>
     Team Explorer indica que se ha desencadenado una compilación para su registro.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs23.png)

9.  Haga doble clic en el nombre de la compilación en curso para ver un registro detallado a medida que avanza la compilación.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs24.png)

10. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vincular TFS a Azure con el asistente. Abra el menú de acceso directo de la definición de compilación y seleccione **Editar definición de compilación**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs25.png)
<br/>
     En la pestaña **Desencadenar**, verá que la definición de compilación se ha configurado en cada registro de forma predeterminada.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs26.png)
<br/>
     En la pestaña **Proceso**, podrá ver que el entorno de implementación se ha configurado con el nombre del servicio en la nube o sitio web. Si trabaja con sitios web, verá propiedades diferentes a las que se muestran aquí.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs27.png)
<br/>
     Especifique los valores para las propiedades si desea que sean diferentes de los predeterminados. La tabla siguiente muestra los valores predeterminados de las propiedades para un servicio en la nube:

  <table data-morhtml="true">
<tr data-morhtml="true"><td data-morhtml="true"><b data-morhtml="true">Propiedad</b></td><td data-morhtml="true"><b data-morhtml="true">Valor predeterminado</b></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Permitir actualizaci&oacute;n</td><td data-morhtml="true">true</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Entorno del servicio de nube</td><td data-morhtml="true">Ensayo</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Nombre del servicio de nube</td><td data-morhtml="true">Nombre del servicio al que est&aacute; conectado</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Etiqueta de implementaci&oacute;n</td><td data-morhtml="true">La misma que el nombre de servicio</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Configuraci&oacute;n de servicio</td><td data-morhtml="true">ServiceConfiguration.Cloud.cscfg</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Nombre de cuenta de almacenamiento</td><td data-morhtml="true">En blanco, que significa que debe intentar encontrar una cuenta de almacenamiento</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Perfil de publicaci&oacute;n</td><td data-morhtml="true">Archivo .azurePubxml. Si registra uno, puede seleccionarlo aqu&iacute;</td></tr>
</table>

  Si la propiedad de cuenta de almacenamiento se deja en blanco, Azure busca una. Si hay una cuenta de almacenamiento con el mismo nombre que el servicio en la nube, se utiliza. Si no, se usa otra cuenta de almacenamiento o, si no hay cuenta de almacenamiento, se crea una. La cuenta de almacenamiento proporciona un lugar en Azure para los archivos de almacenamiento y otros datos. Para obtener más información, consulte [¿Qué es una cuenta de almacenamiento?](http://www.windowsazure.com/en-us/documentation/articles/storage-whatis-account)

1.  Llegados a este punto, la compilación debería haber finalizado correctamente.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs28.png)

2.  Si hace doble clic en el nombre de la compilación, Visual Studio mostrará un **Resumen de la compilación** en el que se incluyen los resultados de las pruebas de los proyectos de prueba de unidades asociadas.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs29.png)

3.  En el [Portal de Azure](http://manage.windowsazure.com), puede ver la implementación asociada en la pestaña Implementaciones cuando se selecciona el entorno de ensayo.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs30.png)

4.  Vaya a la dirección URL del sitio. Para un sitio web, simplemente haga clic en el botón Examinar de la barra de comandos. Para un servicio en la nube, seleccione la dirección URL de la sección **Vista rápida** de la página **Panel** que muestra el entorno de ensayo de un servicio en la nube. Las implementaciones de la integración continua para los servicios en la nube se publican de forma predeterminada en el entorno de ensayo. Puede cambiarlo configurando la propiedad Entorno del servicio de nube alternativo en Producción. Esta captura de pantalla indica en qué parte del sitio en la página del panel del servicio en la nube está la dirección URL:<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs31.png)
<br/>
     Se abrirá una nueva pestaña del explorador para mostrar el sitio que se está ejecutando.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs32.png)

5.  Para los servicios en la nube, si realiza otros cambios en el proyecto, debe desencadenar más compilaciones y acumular varias implementaciones. La última de ellas marcada como Activa.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs33.png)

Reimplementación de una compilación anteriorPaso 5: Vuelva a implementar una compilación anterior
-------------------------------------------------------------------------------------------------

Este paso se aplica a los servicios en la nube y es opcional. En el Portal de administración, seleccione una implementación anterior y haga clic en el botón **Volver a implementar** para que su sitio vuelva a un registro anterior. Tenga en cuenta que esto desencadenará una nueva compilación en TFS y se creará una nueva entrada en el historial de implementación.<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs34.png)

Cambio de la implementación de producciónPaso 6: Cambie la implementación de producción
---------------------------------------------------------------------------------------

Este paso solo se aplica a los servicios en la nube, no a los sitios web. Cuando esté preparado, puede promover el entorno de ensayo al de producción haciendo clic en el botón Intercambiar del Portal de administración. El entorno de ensayo recién implementado se promueve a Producción y el anterior entorno de Producción, si lo hubiera, pasa a entorno de ensayo. La implementación activa puede ser diferente para los entornos de producción y ensayo, pero el historial de implementación de las compilaciones recientes es el mismo independientemente del entorno.<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs35.png)

Para obtener más información, consulte [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Si usa Git, consulte [Comparta su código en Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) y [Publicación en Sitios web Azure desde el control de código fuente](http://www.windowsazure.com/en-us/documentation/articles/web-sites-publish-source-control).

