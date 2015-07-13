<properties 
	pageTitle="Depuración de una aplicación de API en el Servicio de aplicaciones de Azure" 
	description="Aprenda a crear una aplicación de API mientras se ejecuta en el Servicio de aplicaciones de Azure, con Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradyg;tarcher"/>

# Depuración de una aplicación de API en el Servicio de aplicaciones de Azure

## Información general

En este tutorial, aprenderá a depurar código de API de web de ASP.NET que se ha configurado para ejecutarse en una [aplicación de API](app-service-api-apps-why-best-platform.md) en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). Realizará depuraciones locales y remotas (mientras se ejecuta en Azure). El tutorial funciona con la aplicación de la API que ha [creado](app-service-dotnet-create-api-app.md) e [implementado](app-service-dotnet-deploy-api-app.md) en los tutoriales anteriores de esta serie.

## Depuración de una aplicación de API de forma remota 

Los siguientes pasos permiten depurar la aplicación de API mientras se ejecuta en la nube con Swagger UI como el cliente de prueba.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el proyecto que [ha implementado en el tutorial anterior](app-service-dotnet-deploy-api-app.md), y seleccione **Publicar**.

	![Publicar proyecto](./media/app-service-api-dotnet-debug/rd-publish.png)

2. En el cuadro de diálogo **Publicación web**, seleccione la pestaña Configuración y compruebe que la configuración de compilación **Depurar** esté seleccionada. Cuando termine, haga clic en **Publicar** para enviar los cambios a su suscripción de Azure.

	![Publicar proyecto](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. Una ventana del explorador debe abrirse y mostrar un mensaje que confirma que la aplicación de API se ha creado correctamente.

4. En la barra de direcciones del explorador, agregue /swagger al final de la URL y pulse &lt;Intro>. Se mostrará al cliente de Swagger UI.

	![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Regrese a Visual Studio y, en el menú **Ver**, seleccione **Explorador de servidores**.

6. En la ventana **Explorador de servidores**, expanda el nodo **Azure > Servicio de aplicaciones**.

7. Localice el grupo de recursos que ha creado cuando implementaba la aplicación de API.

8. En el grupo de recursos, haga clic con el botón secundario en el nodo de la aplicación de API y seleccione **Asociar depurador**.

	![Asociación del depurador](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	El depurador remoto intentará conectarse. En algunos casos, puede que necesite volver a hacer clic en **Asociar depurador** para establecer una conexión, por lo que si se produce un error, vuelva a intentarlo.

	![Asociación del depurador](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Una vez establecida la conexión, abra el archivo **ContactsController.cs** en el proyecto de la aplicación de API y agregue puntos de interrupción en los métodos `Get` y `Post`. Es posible que no aparezca como activa al principio, pero si se asocia el depurador remoto, ya estará listo para depurar.

	![Aplicación de puntos de interrupción al controlador](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Vuelva a la sesión del explorador, haga clic en el verbo **Obtener** para mostrar el esquema del objeto *Contacto* y, a continuación, haga clic en **Probar**. Si establece un punto de interrupción en el método **Obtener** del controlador, Visual Studio detendrá la ejecución del programa, y se puede depurar la lógica del controlador.

	![Prueba](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Depuración de una aplicación de API de forma local 

Puede haber ocasiones en que desee depurar la aplicación de API localmente; Por ejemplo, para evitar ciclos de ida y vuelta potencialmente lentos durante la prueba/depuración. Los pasos siguientes muestran cómo depurar la aplicación de API localmente mediante Swagger UI como el cliente de prueba.

1. En Visual Studio, abra el archivo *web.config* del proyecto de aplicación de API. 
 
2. En el explorador, vaya al [Portal de vista previa de Azure](http://portal.azure.com).

3. Haga clic en el botón **Examinar** de la barra lateral y seleccione **Aplicaciones de API**.

	![Examen de opciones en el portal de Azure](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Seleccione la aplicación de API que creó en la lista de aplicaciones de API de su suscripción.

	![Lista de aplicaciones de API](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. En la hoja Aplicación de API, haga clic en **Host de aplicación de API**.

	![Host de aplicación de API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. En la hoja del host de aplicación de API, haga clic en **Todas las configuraciones**.

	![Host de aplicación de API: todas las configuraciones](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. Vuelva a la hoja **Configuración** y haga clic en **Configuración de la aplicación**.

	![Host de aplicación de API: configuraciones de la aplicación](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. En la hoja **Configuración de la aplicación web**, acceda a la sección **Configuración de la aplicación**.

	![Host de aplicación de API: configuración de aplicaciones para depuración local](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

9. En **Configuración de la aplicación**, busque los siguientes valores y agréguelos a la sección *appSettings* del archivo **web.config**.
	- **EMA_MicroserviceId**
	- **EMA_Secret**
	- **EMA_RuntimeUrl**

	Cuando haya terminado, la sección **appSettings** de *web.config* debería ser similar a la captura de pantalla siguiente.

	![Host de aplicación de API: configuración de aplicaciones para depuración local](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Nota:** los valores *EMA_* que ha agregado al archivo *web.config* de esta sección contienen información confidencial. Por lo tanto, se recomienda tener cuidado al colocar este archivo en un medio de control de código fuente público (como *github*), puesto que estos secretos será visibles para otros usuarios. Consulte el artículo [Procedimientos recomendados para implementar contraseñas y otra información confidencial en ASP.NET y el Servicio de aplicaciones de Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) para obtener más información.

10. Coloque uno o más puntos de interrupción en el código del controlador de la aplicación de API (en los métodos `Get` y `Post`).

	![Definición de puntos de interrupción](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. Haga clic en &lt;F5 > para iniciar una sesión de depuración de Visual Studio. Cuando el explorador carga la página, se mostrará un mensaje de error. En la barra de direcciones del explorador, agregue */swagger* al final de la URL y pulse &lt;Intro>.

12. Una vez que se haya cargado Swagger UI, haga clic en el verbo **Obtener** para mostrar el esquema del objeto Contacto y, a continuación, haga clic en **Probar**. Visual Studio detendrá ahora la ejecución del programa en los puntos de interrupción que estableció anteriormente, así podrá depurar la lógica del controlador.

	![Prueba](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

## Pasos siguientes

La depuración remota de las Aplicaciones de API permite ver fácilmente cómo se ejecuta el código en el Servicio de aplicaciones de Azure. El diagnóstico enriquecido y los datos de depuración están disponibles directamente en el IDE de Visual Studio para las Aplicaciones de API de Azure.

Aplicaciones de API del Servicio de aplicaciones son aplicaciones web del Servicio de aplicaciones que tienen características adicionales para el hospedaje de servicios web, para que pueda utilizar las mismas herramientas de depuración y de solución de problemas para aplicaciones de API que se usan para las aplicaciones web. Para obtener más información, consulte [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

La aplicación de API que creó en esta serie está disponible públicamente para que cualquiera la llame. Para obtener información acerca de cómo proteger la aplicación de API para que solo puedan llamarla usuarios autenticados, consulte [Protección de una aplicación de API: agregar la autenticación de Azure Active Directory o de un proveedor social](app-service-api-dotnet-add-authentication.md).
 
<!--HONumber=62-->