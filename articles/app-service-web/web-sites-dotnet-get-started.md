<properties
	pageTitle="Creación de una aplicación web con Visual Studio | Microsoft Azure"
	description="En este tutorial se muestra cómo crear un proyecto web de ASP.NET en Visual Studio e implementarlo en una aplicación web en el Servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/02/2016"
	ms.author="tdykstra"/>

# Creación de una aplicación web de ASP.NET en el Servicio de aplicaciones de Azure mediante Visual Studio

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

## Información general

Este tutorial muestra cómo implementar una aplicación web ASP.NET en una [aplicación web del Servicio de aplicaciones de Azure](app-service-web-overview.md) mediante Visual Studio 2015. En este tutorial se considera que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una sencilla aplicación web que se ejecutará en la nube.

La siguiente ilustración muestra la aplicación completada:

![Aplicación Web que se ejecuta en Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

Aprenderá a realizar los siguientes procedimientos:

* Cómo crear una nueva aplicación web del Servicio de aplicaciones servicio mientras crea un proyecto web en Visual Studio.
* Cómo implementar un proyecto web en una aplicación web de servicio de aplicación mediante Visual Studio.
* Uso del [Portal de Azure](/features/azure-portal/) para supervisar y administrar la aplicación web.

Al final del tutorial, una sección [Solución de problemas](#troubleshooting) le proporciona ideas sobre qué hacer si algo no funciona y una sección [Pasos siguientes](#next-steps) proporciona vínculos a otros tutoriales que profundizan más en el uso del Servicio de aplicaciones de Azure.

> [AZURE.NOTE] Ayúdenos a conformar el ámbito y el enfoque de este tutorial; si hay otros temas que le gustaría que se trataran aquí o en un tutorial de introducción, déjenos un comentario en [Comentarios](#comments) al final del tutorial.

[AZURE.INCLUDE [Requisitos previos](../../includes/app-service-web-dotnet-get-started-prereqs.md)]

## Creación de un proyecto web y una aplicación web en el Servicio de aplicaciones de Azure

El primer paso es crear un proyecto web en Visual Studio y una aplicación web en el Servicio de aplicaciones de Azure. Cuando esté listo, implemente el proyecto en la aplicación web para que esté disponible en Internet.

En el diagrama se muestra lo que se hace en los pasos de creación e implementación.

![Diagrama de creación e implementación de Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

1. Abra Visual Studio 2015.

2. Haga clic en **Archivo > Nuevo > Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C# > Web > Aplicación web ASP.NET**. (Si lo prefiere, puede elegir **Visual Basic**).

3. Asegúrese de que **.NET Framework 4.5.2** es el marco de trabajo de destino seleccionado.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) supervisa la aplicación web en cuanto a disponibilidad, rendimiento y uso. La casilla **Agregar Application Insights al proyecto** está seleccionada de forma predeterminada la primera vez que crea un proyecto web después de instalar Visual Studio. Desactive la casilla Agregar Application Insights al proyecto si no desea probar Application Insights.

4. Asigne a la aplicación el nombre **MyExample**.

5. Haga clic en **Aceptar**.

	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**.

7. Haga clic en **Cambiar autenticación**.

	![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. En el cuadro de diálogo **Cambiar autenticación**, haga clic en **Sin autenticación** y, a continuación, en **Aceptar**.

	![Sin autenticación](./media/web-sites-dotnet-get-started/GS13noauth.png)

	La aplicación que está creando para este tutorial es simple y no admite el inicio de sesión de usuario.

5. En la sección **Microsoft Azure** del cuadro de diálogo **Nuevo proyecto de ASP.NET**, asegúrese de que **Host en la nube** está seleccionado y que **Servicio de aplicaciones** está seleccionado en la lista desplegable.

	![Cuadro de diálogo Nuevo proyecto de ASP.NET](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	Con esta configuración se indica a Visual Studio que cree una aplicación web de Azure para su proyecto web.

6. Haga clic en **Aceptar**.

5. Si aún no ha iniciado sesión en Azure, Visual Studio le pedirá que lo haga. Inicie sesión con el identificador y la contraseña de la cuenta que utiliza para administrar la suscripción de Azure.

	Cuando haya iniciado sesión, el cuadro de diálogo **Crear servicio de aplicaciones** le preguntará qué recursos desea crear.

	![Conectado a Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. En el cuadro de diálogo **Crear servicio de aplicaciones**, escriba un nombre en **Nombre de la aplicación web** que sea único en el dominio *azurewebsites.net*. Puede denominarlo MyExample con números a la derecha para que sea único, por ejemplo, MyExample810. Si se crea automáticamente un nombre de web predeterminado, será exclusivo y puede usarlo.

	Si escribe un nombre que ya usa otro usuario, aparecerá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y tendrá que especificar otro nombre.

	La dirección URL completa de la aplicación es el nombre más *.azurewebsites.net* (como se muestra junto al cuadro de texto **Nombre de la aplicación web**). Por ejemplo, si el nombre es `MyExample810`, la dirección URL será `myexample810.azurewebsites.net`.

	La sección [Pasos siguientes](#next-steps) incluye un vínculo a un tutorial donde se explica cómo usar su propio dominio personalizado con una aplicación web de Azure.

6. Al lado del cuadro **Grupo de recursos**, haga clic en **Nuevo** y escriba "MyExample" u otro nombre que prefiera.

	Este cuadro combinado permite seleccionar un grupo de recursos existente o crear uno nuevo, para lo que se debe escribir un nombre de grupo de recursos que no exista en la suscripción.

	Un grupo de recursos es una colección de recursos de Azure tales como aplicaciones web, bases de datos y máquinas virtuales. Para un tutorial, suele ser mejor crear un nuevo grupo de recursos, porque así será fácil eliminar en un solo paso todos los recursos de Azure que se creen para el tutorial. Para más información, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

4. Haga clic en el botón **Nuevo** situado junto a la lista desplegable **Plan de servicio de aplicaciones**.

	![Cuadro de diálogo Crear servicio de aplicaciones](./media/web-sites-dotnet-get-started/createas.png)

	La captura de pantalla muestra los valores de ejemplo de **Nombre de la aplicación web**, **Suscripción** y **Grupo de recursos**. Sus valores serán diferentes.

	En los siguientes pasos se creará un plan del Servicio de aplicaciones para el nuevo grupo de recursos. Un plan del Servicio de aplicaciones especifica los recursos de proceso en los que se ejecuta una aplicación web. Por ejemplo, si elige el nivel Gratis, la aplicación de API se ejecuta en máquinas virtuales compartidas, mientras que para algunos niveles de pago, se ejecuta en máquinas virtuales dedicadas. Para más información, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En el cuadro de diálogo **Configurar el plan de servicio de aplicaciones**, escriba "MyExamplePlan" u otro nombre que prefiera.

5. En la lista desplegable **Ubicación**, elija la ubicación más cercana.

	Esta opción especifica en qué centro de datos de Azure se ejecutará su aplicación. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, Pero para una aplicación de producción, quiere que el servidor esté lo más próximo posible a los clientes que acceden a él con el fin de minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. En la lista desplegable **Tamaño**, haga clic en **Gratis**.

	Para este tutorial, el plan de tarifa Gratis proporcionará un rendimiento suficiente.

6. En el cuadro de diálogo **Configurar plan del servicio de aplicaciones**, haga clic en **Aceptar**.

	![Cuadro de diálogo Configurar servicio de aplicaciones](./media/web-sites-dotnet-get-started/configasp.png)

7. En el cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Crear**.

	![Cuadro de diálogo Crear servicio de aplicaciones](./media/web-sites-dotnet-get-started/clickcreate.png)

	En poco tiempo, normalmente menos de un minuto, Visual Studio crea el proyecto web y la aplicación web.

	La ventana del **Explorador de soluciones** ventana muestra los archivos y carpetas del nuevo proyecto.

	![Explorador de soluciones](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	La ventana **Actividad del Servicio de aplicaciones de Azure** muestra que se ha creado la aplicación web.

	![Aplicación web creada en la ventana Actividad del Servicio de aplicaciones de Azure](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	Y la aplicación web se puede ver en la ventana **Cloud Explorer** de Visual Studio.

	![Aplicación web creada en Cloud Explorer](./media/web-sites-dotnet-get-started/siteinse.png)
	
	Esta ventana le permite ver y administrar un amplio intervalo de recursos de Azure. La captura de pantalla solo muestra aplicaciones web, pero en su ventana **Cloud Explorer** verá más tipos de recursos. Haga clic con el botón derecho en un recurso, como una aplicación web, para ver las opciones disponibles para administrarlo.

## Implementación del proyecto web en la aplicación web de Azure

En esta sección, implementará el proyecto web en la aplicación web, como se muestra en el paso 2 del diagrama.

![Diagrama de creación e implementación de Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

1. En el **Explorador de soluciones**, haga clic con el botón derecho y seleccione **Publicar**.

	![Selección de Publicar en el menú de Visual Studio](./media/web-sites-dotnet-get-started/choosepublish.png)

	En unos segundos aparecerá el Asistente para **publicación web**. El asistente abre un *perfil de publicación* que incluye la configuración para implementar el proyecto web en la nueva aplicación web. Si desea realizar la implementación en otra aplicación web distinta, puede hacer clic en la pestaña **Perfil** ficha para crear un perfil diferente. En este tutorial, aceptará la configuración que se implementa en la aplicación web que creó anteriormente.

	El perfil de publicación incluye un nombre de usuario y una contraseña para la implementación. Estas credenciales se han generado automáticamente y no es preciso que las escriba o las cambie. La contraseña está cifrada en un archivo oculto específico del usuario en la carpeta `Properties\PublishProfiles`.

8. En la pestaña **Conexión** del Asistente para **publicación web**, haga clic en **Siguiente**.

	![Acción de hacer clic en Siguiente en la pestaña Conexión del Asistente para publicación web](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

	La pestaña siguiente es **Configuración**. Aquí puede cambiar la pestaña de configuración de compilación para implementar una compilación de depuración para la [depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). La pestaña también ofrece varias **opciones de publicación de archivos**:

	* Quitar archivos adicionales en el destino.
	* Precompilar durante la publicación.
	* Excluir archivos de la carpeta App\_Data.

	Para este tutorial no necesita ninguna de ellas. Para obtener explicaciones detalladas de lo que hacen, consulte [Cómo: Implementar un proyecto de aplicación web mediante Publicación con un solo clic en Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

10. En la pestaña **Configuración**, haga clic en **Siguiente**.

	![Pestaña Configuración del Asistente para publicación web](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

	La siguiente pestaña es **Vista previa**. Aquí puede ver qué archivos se van a copiar desde el proyecto a la aplicación de API. Al implementar un proyecto en una aplicación de API para la que ya implementó anteriormente, solo se copian los archivos modificados. Si desea ver una lista de lo que se va a copiar, haga clic en el botón **Iniciar vista previa**.

11. En la pestaña **Vista previa**, haga clic en **Publicar**.

	![Pestaña Vista previa del Asistente para publicación web](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Al hacer clic en **Publicar**, Visual Studio comienza el proceso de copia de los archivos en el servidor de Azure. Esto puede tardar un par de minutos.

	Las ventanas **Salida** y **Actividad del Servicio de aplicaciones de Azure** muestran las acciones de implementación que se realizaron e informan de la correcta finalización de la implementación.

	![Ventana de salida de Visual Studio informando de que la implementación se ha realizado correctamente](./media/web-sites-dotnet-get-started/PublishOutput.png)

	Tras una implementación correcta, el explorador predeterminado se abre automáticamente en la dirección URL de la aplicación web implementada y la aplicación que creó se ejecuta ahora en la nube. La dirección URL en la barra de direcciones del explorador muestra que la aplicación web se carga desde Internet.

	![Aplicación Web que se ejecuta en Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

> [AZURE.TIP] Para agilizar la implementación, puede habilitar la barra de herramientas **Publicación en Web con un solo clic**. Haga clic en **Vista > Barras de herramientas** y seleccione **Publicación en Web con un solo clic**. Esta barra de herramientas se puede usar para seleccionar un perfil, hacer clic en un botón para publicar o hacer clic en un botón para abrir el Asistente para **publicación web**. ![Barra de herramientas Publicación en Web con un solo clic](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## <a id="portal"></a> Supervisión y administración de la aplicación web en el Portal de Azure

El [Portal de Azure](/services/management-portal/) es una interfaz web que puede usar para administrar y supervisar los servicios de Azure; por ejemplo, la aplicación web que acaba de crear. En esta sección del tutorial podrá ver algo de lo que puede hacer en el portal.

1. En el explorador, vaya a [https://portal.azure.com](https://portal.azure.com) e inicie sesión con las credenciales que usa para administrar su cuenta de Azure.

2. Haga clic en **Servicios de aplicaciones** y, a continuación, en el nombre de la aplicación web.

	![Servicios de aplicaciones en el Portal de Azure](./media/web-sites-dotnet-get-started/selinportal.png)

	La hoja **Aplicación web** muestra información general acerca de la configuración y las estadísticas de uso de su aplicación web. (Las ventanas que se abren a la derecha del portal se llaman *hojas*.)

	![Hoja Aplicación web en el Portal de Azure](./media/web-sites-dotnet-get-started/portaldashboard.png)

	La aplicación web no ha tenido mucho tráfico todavía y puede que no se muestre nada en el gráfico. Si explora la aplicación, actualiza la página varias veces y después actualiza la página la página del Portal, verá que aparecen algunas estadísticas.

3. La hoja **Configuración** muestra más opciones para configurar una aplicación web.

	![Hoja Configuración en el Portal de Azure](./media/web-sites-dotnet-get-started/portalconfigure1.png)

	Observe el vínculo **Credenciales de implementación** en la sección **Publicación**. Ahí es donde puede crear un nombre de usuario y una contraseña personalizados para la implementación. Haga clic en el botón **Guardar** de la parte superior de la hoja para enviar el cambio. Si crea un nombre de usuario y una contraseña nuevos, es preciso que especifique los mismos valores en la pestaña **Conexión** del Asistente para **publicación web**.
	
	La captura de pantalla es solo una vista parcial de la hoja **Configuración**. Hay más secciones en esta hoja que las que se muestran.

4. Haga clic en **Configuración de la aplicación** en la sección **General** de la hoja **Configuración** para ver un ejemplo de los tipos de configuración que se pueden realizar en el portal.

	Por ejemplo, puede controlar la versión de .NET que se usa para la aplicación web, habilitar características tales como [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) y establecer [valores de cadena de conexión](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

	![Pestaña Configurar de la aplicación web en el Portal de Azure](./media/web-sites-dotnet-get-started/portalconfigure2.png)

Estas son solo algunas de las características del Portal. Puede crear nuevas aplicaciones web, eliminar las existentes, detener y reiniciar aplicaciones web, y administrar otros tipos de servicios de Azure, como bases de datos y máquinas virtuales.

## Eliminación de la aplicación web de Azure

Cuando ya no necesite la aplicación web que ha creado en este tutorial, puede eliminarla.

Una manera fácil de eliminar la aplicación web es hacer clic en el botón **Eliminar** de la parte superior de su hoja **Aplicación web** en el Portal de Azure. Pero lo mejor es eliminar el grupo de recursos que ha creado como contenedor de la aplicación web. En este tutorial, el grupo de recursos contiene solo la aplicación web, pero normalmente un grupo de recursos contiene una colección de recursos relacionados. Por ejemplo, su aplicación web podría usar una base de datos o una cuenta de Almacenamiento de Azure que dejarán de necesitarse cuando se elimine la aplicación web. Cuando se elimina un grupo de recursos, todo lo que contiene también se elimina. Para eliminar un grupo de recursos mediante el Portal de Azure, realice los pasos siguientes:

1. Vaya a la página principal del [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Grupos de recursos**.

3. En la lista de grupos de recursos, haga clic en el grupo de recursos que quiere eliminar.

	Cuando aparece la hoja **Grupo de recursos**, incluye una lista de los recursos que contiene.

4. En la hoja **Grupo de recursos**, haga clic en **Eliminar**.

	![Eliminación de un grupo de recursos en el Portal de Azure](./media/web-sites-dotnet-get-started/delresgrp.png)

## Solución de problemas

Si experimenta problemas mientras avanza por este tutorial, asegúrese de que está usando la versión más reciente del SDK de Azure para. NET. La forma más fácil de hacerlo es [descargar el SDK de Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003); si tiene instalada la versión actual, el Instalador de plataforma web indicará que no es preciso realizar la instalación.

Si está en una red corporativa y está intentando realizar la implementación en el Servicio de aplicaciones de Azure mediante un firewall, asegúrese de que los puertos 443 y 8172 estén abiertos para Web Deploy. Si no puede abrir esos puertos, consulte la siguiente Pasos siguientes a continuación para ver otras opciones de implementación.

Una vez que la aplicación web de ASP.NET se está ejecutando en el Servicio de aplicaciones de Azure, puede que quiera aprender más sobre las características de Visual Studio que simplifican la solución de problemas. Para más información sobre el registro, la depuración remota, etc., consulte [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## Pasos siguientes

En este tutorial, aprendió a crear una aplicación web sencilla e implementarla en una aplicación web de Azure. Estos son algunos temas y recursos relacionados que le permitirán saber más sobre el Servicio de aplicaciones de Azure:

* Implementación de un proyecto web existente en una nueva aplicación web

	Para iniciar el proceso de implementación de un proyecto web existente, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y luego haga clic en **Publicar**. Elija **Servicio de aplicaciones de Microsoft Azure** como destino de publicación. Luego, elija una aplicación web de Azure existente como destino o haga clic en **Nueva** para crear una. Si hace clic en **Nueva** para crear una nueva, el proceso será el mismo que el que aparece en este tutorial.

* Otras maneras de crear aplicaciones web

	También se puede crear aplicaciones web mediante el [Portal de Azure](https://portal.azure.com/), [cmdlets de Azure para Windows PowerShell](../powershell-install-configure.md) o la [interfaz de línea de comandos multiplataforma](../xplat-cli.md).

* Creación de aplicaciones de API

	Ha visto cómo crear una instancia del Servicio de aplicaciones de Azure pensada principalmente para hospedar un sitio web. Dichas instancias se denominan aplicaciones web. El Servicio de aplicaciones también tiene características diseñadas para que sea más fácil desarrollar, probar y hospedar API. Las instancias del Servicio de aplicaciones destinadas principalmente a hospedar API se denominan aplicaciones de API. Para más información, consulte [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](../app-service-api/app-service-api-dotnet-get-started.md).

* Otros métodos de implementar un proyecto web

	Para más información sobre otras formas de implementar proyectos web en aplicaciones web, ya sea con Visual Studio o mediante la [automatización de la implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Documentación de implementación del Servicio de aplicaciones de Azure](web-sites-deploy.md).

* Incorporación de un nombre de dominio personalizado y SSL

	Para obtener información acerca de cómo usar SSL y su propio dominio (por ejemplo, www.contoso.com, en lugar de contoso.azurewebsites.net), consulte los siguientes recursos:

	* [Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](web-sites-custom-domain-name.md)
	* [Habilitación de HTTPS en un sitio web de Azure](web-sites-configure-ssl-certificate.md)

* Cómo agregar características en tiempo real (por ejemplo, chat)

	En el caso de las características en tiempo real (como un servicio de chat, un juego o un tablero de cotizaciones), el mejor rendimiento se puede obtener mediante el uso de [ASP.NET SignalR](http://www.asp.net/signalr) con el método de transporte [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Para obtener más información, consulte [Uso de SignalR con aplicaciones web de Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites).

* Selección entre Servicio de aplicaciones, Servicios en la nube y Máquinas virtuales de Azure para aplicaciones web

	En Azure puede ejecutar aplicaciones web en Aplicaciones web del Servicio de aplicaciones, como se muestra en este tutorial, o bien en Servicios en la nube o en Máquinas virtuales. Para más información, consulte [Comparación de Servicio de aplicaciones de Azure, Servicios en la nube de Azure, Máquinas virtuales de Azure y Azure Service Fabric](/manage/services/web-sites/choose-web-app-service/).

<!---HONumber=AcomDC_0323_2016-->