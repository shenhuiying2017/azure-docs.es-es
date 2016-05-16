<properties
	pageTitle="Introducción al Servicio de aplicaciones de Azure y a ASP.NET Web API 2 | Microsoft Azure"
	description="En este tutorial se muestra cómo crear un proyecto de ASP.NET Web API 2 en Visual Studio e implementarlo en una aplicación de API en el Servicio de aplicaciones de Azure."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tdykstra"/>

# Introducción al Servicio de aplicaciones de Azure y a ASP.NET Web API 2 mediante Visual Studio

## Información general

Este tutorial muestra cómo implementar una aplicación de ASP.NET Web API 2 en una [aplicación de API](app-service-api-apps-why-best-platform.md) del Servicio de aplicaciones de Azure mediante Visual Studio 2015. Creará un proyecto de Visual Studio y lo implementará en una aplicación de API, tal y como se muestra en el diagrama.

![Diagrama de creación e implementación de Visual Studio](./media/app-service-api-dotnet-get-started-template/Create_App.png)

En este tutorial se considera que no tiene ninguna experiencia previa con Azure o ASP.NET. Cuando acabe, tendrá una sencilla API web que se ejecutará en la nube.

Aprenderá a realizar los siguientes procedimientos:

* Cómo crear una nueva aplicación de API del Servicio de aplicaciones mientras crea un nuevo proyecto Web API 2 en Visual Studio.
* Cómo implementar un proyecto Web API 2 en una aplicación de API del Servicio de aplicaciones mediante Visual Studio.
* Uso del [Portal de Azure](/features/azure-portal/) para supervisar y administrar la aplicación de API.

Al final del tutorial, una sección [Solución de problemas](#troubleshooting) le proporciona ideas sobre qué hacer si algo no funciona y una sección [Pasos siguientes](#next-steps) proporciona vínculos a otros tutoriales que profundizan más en el uso del Servicio de aplicaciones de Azure.

## Requisitos previos

### Cuenta de Azure

Necesita una cuenta de Azure para completar el tutorial. Puede:

* [Abrir una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Incluso después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure.
* [Activar los beneficios de suscripción a Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751). Ahí puede crear de forma inmediata una aplicación de corta duración para iniciarse en el Servicio de aplicaciones. No se requiere tarjeta de crédito y no se establece ningún compromiso.

### <a name="setupdevenv"></a>Visual Studio 2015 con SDK de Azure para .NET

Este tutorial está escrito para Visual Studio 2015 con el [SDK de Azure para .NET](../dotnet-sdk.md) 2.8.2, o las versiones posteriores. [Descargue aquí el último SDK de Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Si aún no lo tiene, Visual Studio se instala automáticamente con el SDK.

Si tiene Visual Studio 2013, puede [descargar el último SDK de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Algunas pantallas pueden no coincidir exactamente con las ilustraciones.

>[AZURE.NOTE] Según la cantidad de dependencias de SDK que tenga ya en la máquina, la instalación del SDK puede tardar un período largo, desde unos minutos a media hora o más.

### ASP.NET Web API 2

Este tutorial trata del uso de ASP.NET Web API 2 con el Servicio de aplicaciones de Azure; no enseña cómo desarrollar una aplicación ASP.NET Web API. Para ver una introducción a ASP.NET Web API 2, consulte [Getting Started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) (Introducción a ASP.NET Web API 2) en el sitio de [ASP.NET](http://asp.net/).

## Creación de un proyecto y una aplicación de API en el Servicio de aplicaciones de Azure

El primer paso es crear un proyecto ASP.NET Web API 2 en Visual Studio y una aplicación de API en el Servicio de aplicaciones de Azure. Cuando esté listo, implemente el proyecto en la aplicación de API para que la API web esté disponible en Internet.

1. Abra Visual Studio 2015.

2. Haga clic en **Archivo > Nuevo > Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C# > Web > Aplicación web ASP.NET**. (Si lo prefiere, puede elegir **Visual Basic**).

3. Asegúrese de que **.NET Framework 4.5.2** es el marco de trabajo de destino seleccionado.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) supervisa la aplicación de API en cuanto a disponibilidad, rendimiento y uso. La casilla **Agregar Application Insights al proyecto** está seleccionada de forma predeterminada la primera vez que crea un proyecto web después de instalar Visual Studio. Desactive la casilla Agregar Application Insights al proyecto si no desea probar Application Insights.

4. Asigne a la aplicación el nombre **MyExample**.

5. Haga clic en **Aceptar**.

	![Cuadro de diálogo Nuevo proyecto](./media/app-service-api-dotnet-get-started-template/GS13newprojdb.png)

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **Aplicación de API de Azure**.

5. En la sección **Microsoft Azure** del cuadro de diálogo **Nuevo proyecto de ASP.NET**, asegúrese de que **Host en la nube** está seleccionado y que **Servicio de aplicaciones** está seleccionado en la lista desplegable.

	![Cuadro de diálogo New ASP.NET Project](./media/app-service-api-dotnet-get-started-template/GS13newaspnetprojdb.png)

	Con esta configuración hace que Visual Studio cree una aplicación de API de Azure para el proyecto web.

6. Haga clic en **Aceptar**.

5. Si aún no ha iniciado sesión en Azure, Visual Studio le pedirá que lo haga. Inicie sesión con el identificador y la contraseña de la cuenta que utiliza para administrar la suscripción de Azure.

	Cuando haya iniciado sesión, el cuadro de diálogo **Crear servicio de aplicaciones** le preguntará qué recursos desea crear.

	![Conectado a Azure](./media/app-service-api-dotnet-get-started-template/configuresitesettings.png)

3. En el cuadro de diálogo **Crear servicio de aplicaciones**, escriba un nombre en **Nombre de la aplicación de API** que sea único en el dominio *azurewebsites.net*. Puede denominarlo MyExample con números a la derecha para que sea único, por ejemplo, MyExample810. Si se crea automáticamente un nombre predeterminado, será exclusivo y puede usarlo.

	Si escribe un nombre que ya usa otro usuario, aparecerá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y tendrá que especificar otro nombre.

	La dirección URL base de la API web es el nombre más *.azurewebsites.net* (como se muestra junto al cuadro de texto **Nombre de la aplicación web**). Por ejemplo, si el nombre es `MyExample810`, la dirección URL será `myexample810.azurewebsites.net`.

6. Al lado del cuadro **Grupo de recursos**, haga clic en **Nuevo** y escriba "MyExample" u otro nombre que prefiera.

	Este cuadro combinado permite seleccionar un grupo de recursos existente o crear uno nuevo, para lo que se debe escribir un nombre de grupo de recursos que no exista en la suscripción.

	Un grupo de recursos es una colección de recursos de Azure tales como aplicaciones de API, bases de datos y máquinas virtuales. Para un tutorial, suele ser mejor crear un nuevo grupo de recursos, porque así será fácil eliminar en un solo paso todos los recursos de Azure que se creen para el tutorial. Para más información, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

4. Haga clic en el botón **Nuevo** situado junto a la lista desplegable **Plan de servicio de aplicaciones**.

	![Cuadro de diálogo Crear servicio de aplicaciones](./media/app-service-api-dotnet-get-started-template/createas.png)

	La captura de pantalla muestra los valores de ejemplo de **Nombre de la aplicación web**, **Suscripción** y **Grupo de recursos**. Sus valores serán diferentes.

	En los siguientes pasos se creará un plan del Servicio de aplicaciones para el nuevo grupo de recursos. Un plan del Servicio de aplicaciones especifica los recursos de proceso en los que se ejecuta la aplicación de API. Por ejemplo, si elige el nivel Gratis, la aplicación de API se ejecuta en máquinas virtuales compartidas, mientras que para algunos niveles de pago, se ejecuta en máquinas virtuales dedicadas. Para más información, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En el cuadro de diálogo **Configurar el plan de Servicio de aplicaciones**, escriba "MyExamplePlan" u otro nombre que prefiera.

5. En la lista desplegable **Ubicación**, elija la ubicación más cercana.

	Esta opción especifica en qué centro de datos de Azure se ejecutará su aplicación. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, Pero para una aplicación de producción, quiere que el servidor esté lo más próximo posible a los clientes que acceden a él con el fin de minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. En la lista desplegable **Tamaño**, haga clic en **Gratis**.

	Para este tutorial, el plan de tarifa Gratis proporcionará un rendimiento suficiente.

6. En el cuadro de diálogo **Configurar plan del servicio de aplicaciones**, haga clic en **Aceptar**.

	![Cuadro de diálogo Configurar servicio de aplicaciones](./media/app-service-api-dotnet-get-started-template/configasp.png)

7. En el cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Crear**.

	![Cuadro de diálogo Crear servicio de aplicaciones](./media/app-service-api-dotnet-get-started-template/clickcreate.png)

	En poco tiempo, normalmente menos de un minuto, Visual Studio crea el proyecto web y la aplicación de API.

	La ventana del **Explorador de soluciones** ventana muestra los archivos y carpetas del nuevo proyecto.

	![Explorador de soluciones](./media/app-service-api-dotnet-get-started-template/solutionexplorer.png)

	La ventana **Actividad del Servicio de aplicaciones de Azure** muestra que se ha creado la aplicación de API. (Aunque el mensaje puede llamarla aplicación web).

	![Aplicación de API creada en la ventana Actividad del Servicio de aplicaciones de Azure](./media/app-service-api-dotnet-get-started-template/GS13sitecreated1.png)

	Y la aplicación de API se puede ver en la ventana **Cloud Explorer** de Visual Studio.

	![Aplicación de API creada en Cloud Explorer](./media/app-service-api-dotnet-get-started-template/siteinse.png)
	
	Esta ventana le permite ver y administrar un amplio intervalo de recursos de Azure. Puede que vea tipos de recursos en la ventana **Cloud Explorer** que sean diferentes a los que aparecen en este ejemplo. Haga clic con el botón derecho en un recurso, como una aplicación de API, para ver las opciones disponibles para administrarlo.

## Implementación del proyecto de Visual Studio en la aplicación de API de Azure

En esta sección, implementará el proyecto web en la aplicación de API, como se muestra en el paso 2 del diagrama.

![Diagrama de creación e implementación de Visual Studio](./media/app-service-api-dotnet-get-started-template/Create_App.png)

1. En el **Explorador de soluciones**, haga clic con el botón derecho y seleccione **Publicar**.

	![Selección de Publicar en el menú de Visual Studio](./media/app-service-api-dotnet-get-started-template/choosepublish.png)

	En unos segundos aparecerá el Asistente para **publicación web**. El asistente abre un *perfil de publicación* que incluye la configuración para implementar el proyecto en la nueva aplicación de API. Si desea realizar la implementación en otra aplicación de API distinta, puede hacer clic en la pestaña **Perfil** para crear un perfil diferente. En este tutorial, aceptará la configuración que se implementa en la aplicación de API que creó anteriormente.

	El perfil de publicación incluye un nombre de usuario y una contraseña para la implementación. Estas credenciales se han generado automáticamente y no es preciso que las escriba o las cambie. La contraseña está cifrada en un archivo oculto específico del usuario en la carpeta `Properties\PublishProfiles`.

8. En la pestaña **Conexión** del Asistente para **publicación web**, haga clic en **Siguiente**.

	![Acción de hacer clic en Siguiente en la pestaña Conexión del Asistente para publicación web](./media/app-service-api-dotnet-get-started-template/GS13ValidateConnection.png)

	La pestaña siguiente es la de **Configuración** (como se muestra a continuación). Aquí puede cambiar la pestaña de configuración de compilación para implementar una compilación de depuración para la [depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). La pestaña también ofrece varias **opciones de publicación de archivos**:

	* Quitar archivos adicionales en el destino.
	* Precompilar durante la publicación.
	* Excluir archivos de la carpeta App\_Data.

	Para este tutorial no necesita ninguna de ellas. Para obtener explicaciones detalladas de lo que hacen, consulte [Implementación de un proyecto de aplicación web mediante Publicación con un solo clic en Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

10. En la pestaña **Configuración**, haga clic en **Siguiente**.

	![Pestaña Configuración del Asistente para publicación web](./media/app-service-api-dotnet-get-started-template/GS13SettingsTab.png)

	La pestaña **Vista previa** (que aparece a continuación) es la siguiente. Aquí puede ver qué archivos se van a copiar desde el proyecto a la aplicación de API. Al implementar un proyecto en una aplicación de API para la que ya implementó anteriormente, solo se copian los archivos modificados. Si desea ver una lista de lo que se va a copiar, haga clic en el botón **Iniciar vista previa**.

11. En la pestaña **Vista previa**, haga clic en **Publicar**.

	![Pestaña Vista previa del Asistente para publicación web](./media/app-service-api-dotnet-get-started-template/GS13previewoutput.png)

	Al hacer clic en **Publicar**, Visual Studio comienza el proceso de copia de los archivos en el servidor de Azure. Esto puede tardar un par de minutos.

	Las ventanas **Salida** y **Actividad del Servicio de aplicaciones de Azure** muestran las acciones de implementación que se realizaron e informan de la correcta finalización de la implementación.

	![Ventana de salida de Visual Studio informando de que la implementación se ha realizado correctamente](./media/app-service-api-dotnet-get-started-template/PublishOutput.png)

	Una vez que se realiza correctamente la implementación, el explorador predeterminado se dirige automáticamente a la URL base de la aplicación de API implementada. El explorador muestra una página con un mensaje que indica "Esta aplicación web se ha creado correctamente".

	![Ventana de salida de Visual Studio informando de que la implementación se ha realizado correctamente](./media/app-service-api-dotnet-get-started-template/successfullycreated.png)

> [AZURE.TIP] Para agilizar la implementación, puede habilitar la barra de herramientas **Publicación en Web con un solo clic**. Haga clic en **Vista > Barras de herramientas** y seleccione **Publicación en Web con un solo clic**. Esta barra de herramientas se puede usar para seleccionar un perfil, hacer clic en un botón para publicar o hacer clic en un botón para abrir el Asistente para **publicación web**. ![Barra de herramientas Publicación en Web con un solo clic](./media/app-service-api-dotnet-get-started-template/weboneclickpublish.png)

## Comprobación de la API Web implementada

1. Envíe una solicitud HTTP GET a la dirección URL *{nombre de su aplicación de API}.azurewebsites.net/api/values*, mediante la herramienta de cliente HTTP que prefiera.

	La plantilla de proyecto API web define un controlador `Values` que devuelve una matriz de dos cadenas en formato JSON para una solicitud GET. La siguiente ilustración muestra una solicitud enviada por [Postman](http://www.getpostman.com/), con el JSON devuelto en el cuerpo de la respuesta.

	![Ventana de salida de Visual Studio informando de que la implementación se ha realizado correctamente](./media/app-service-api-dotnet-get-started-template/postman.png)

2. Ahora puede realizar cambios en el código, volver a implementar el proyecto de la misma manera que para la implementación inicial y ver cómo funcionan los cambios en Azure en cuestión de segundos.

## <a id="portal"></a> Opcional: Supervisión y administración de la aplicación de API en el Portal de Azure

El [Portal de Azure](/services/management-portal/) es una interfaz web que puede usar para administrar y supervisar los servicios de Azure; por ejemplo, la aplicación de API que acaba de crear. En esta sección del tutorial podrá ver algo de lo que puede hacer en el portal.

1. En el explorador, vaya a [https://portal.azure.com](https://portal.azure.com) e inicie sesión con las credenciales que usa para administrar su cuenta de Azure.
	
2. Haga clic en **Servicios de aplicaciones** y, después, en el nombre de la aplicación de API.

	![Servicios de aplicaciones en el Portal de Azure](./media/app-service-api-dotnet-get-started-template/selinportal.png)

	La hoja **Aplicación de API** muestra información general acerca de la configuración y las estadísticas de uso de su aplicación de API. (Las ventanas que se abren a la derecha se llaman *hojas*).

	![Hoja Aplicación de API en el Portal de Azure](./media/app-service-api-dotnet-get-started-template/portaldashboard.png)

	La aplicación de API no ha tenido mucho tráfico todavía y puede que no se muestre nada en el gráfico. Si realiza algunas solicitudes más a la API Web y, a continuación, actualiza la página del portal, aparecerán algunas estadísticas.

3. En la hoja **Configuración** se muestran más opciones para configurar su aplicación de API.

	![Hoja Configuración en el Portal de Azure](./media/app-service-api-dotnet-get-started-template/portalconfigure1.png)

	La sección **API** contiene vínculos a hojas para configurar un punto de conexión de metadatos de API y CORS. Consulte la sección [Pasos siguientes](#next-steps) para ver otros tutoriales que describen estas características.

	Observe el vínculo **Credenciales de implementación** en la sección **Publicación**. Ahí es donde puede crear un nombre de usuario y una contraseña personalizados para la implementación. Haga clic en el botón **Guardar** de la parte superior de la hoja para enviar el cambio. Si crea un nombre de usuario y una contraseña nuevos, es preciso que especifique los mismos valores en la pestaña **Conexión** del Asistente para **publicación web** en el proyecto web.
	
	La captura de pantalla es solo una vista parcial de la hoja **Configuración**. Hay más secciones en esta hoja que las que se muestran.

Estas son solo algunas de las características del Portal. Puede crear nuevas aplicaciones de API, eliminar las existentes, detener y reiniciar aplicaciones de API, y administrar otros tipos de servicios de Azure, como bases de datos y máquinas virtuales.

## Opcional: Eliminación de la aplicación de API de Azure

Cuando ya no necesite la aplicación de API que ha creado en este tutorial, puede eliminarla.

Una manera fácil de eliminar la aplicación de API es hacer clic en el botón **Eliminar** de la parte superior de su hoja **Aplicación de API** en el Portal de Azure. Pero lo mejor es eliminar el grupo de recursos que ha creado como contenedor de la aplicación de API. En este tutorial, el grupo de recursos contiene solo la aplicación de API, pero normalmente un grupo de recursos contiene una colección de recursos relacionados. Por ejemplo, su aplicación de API podría usar una base de datos o una cuenta de Almacenamiento de Azure que dejarán de necesitarse cuando se elimine la aplicación de API. Cuando se elimina un grupo de recursos, todo lo que contiene también se elimina. Para eliminar un grupo de recursos mediante el Portal de Azure, realice los pasos siguientes:

1. Vaya a la página principal del [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Grupos de recursos**.

3. En la lista de grupos de recursos, haga clic en el grupo de recursos que quiere eliminar.

	Cuando aparece la hoja **Grupo de recursos**, incluye una lista de los recursos que contiene.

4. En la hoja **Grupo de recursos**, haga clic en **Eliminar**.

	![Eliminación de un grupo de recursos en el Portal de Azure](./media/app-service-api-dotnet-get-started-template/delresgrp.png)

## Solución de problemas

Si experimenta problemas mientras avanza por este tutorial, asegúrese de que está usando la versión más reciente del SDK de Azure para. NET. La forma más fácil de hacerlo es [descargar el SDK de Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003); si tiene instalada la versión actual, el Instalador de plataforma web indicará que no es preciso realizar la instalación.

Si está en una red corporativa y está intentando realizar la implementación en el Servicio de aplicaciones de Azure mediante un firewall, asegúrese de que los puertos 443 y 8172 estén abiertos para Web Deploy. Si no puede abrir esos puertos, consulte la siguiente Pasos siguientes a continuación para ver otras opciones de implementación.

Una vez que la aplicación de API de ASP.NET se esté ejecutando en el Servicio de aplicaciones de Azure, podrá obtener más información acerca de las características de Visual Studio que simplifican la solución de problemas. Para más información sobre el registro, la depuración remota, etc., consulte [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Pasos siguientes

En este tutorial, aprendió a crear una API web sencilla y a implementarla en una aplicación de API del Servicio de aplicaciones de Azure. Para obtener una introducción a las características del servicio de aplicaciones que facilitan el desarrollo y utilización de las API web, consulte la serie de tutoriales que comienza con [Introducción a aplicaciones de API y ASP.NET](app-service-api-dotnet-get-started.md).

<!---HONumber=AcomDC_0504_2016-->