<properties
	pageTitle="Creación de una aplicación web de Java en Servicio de aplicaciones de Azure | Microsoft Azure"
	description="En este tutorial se muestra cómo implementar una aplicación web de Java en el Servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="robmcm"/>

# Creación de una aplicación web de Java en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [pestañas](../../includes/app-service-web-get-started-nav-tabs.md)]

En este tutorial se muestra cómo crear una [aplicación web de Java en el Servicio de aplicaciones de Azure] mediante el [Portal de Azure]. El Portal de Azure es una interfaz web que puede usarse para administrar los recursos de Azure.

> [AZURE.NOTE] Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio] o bien [registrarse para obtener una evaluación gratuita].
>
> Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [prueba gratuita del Servicio de aplicaciones]. Ahí puede crear de forma inmediata una aplicación web de inicio de corta duración en el Servicio de aplicaciones; no se requiere ninguna tarjeta de crédito y no se establece ningún compromiso.

## Opciones de la aplicación Java

Hay varias maneras de configurar una aplicación de Java en una aplicación web de Servicio de aplicaciones.

1. Cree una aplicación y después configure **Configuración de la aplicación**.

	El Servicio de aplicaciones proporciona varias versiones de Tomcat y Jetty, con la configuración predeterminada. Si la aplicación que va a hospedar funcionará con una de las versiones integradas, este método de configuración de un contenedor web es el más sencillo y cumple su función si todo lo que necesita es cargar un archivo WAR en un contenedor web. Para este método, creará una aplicación en el Portal de Azure e irá a la hoja **Configuración de la aplicación** de la aplicación para elegir la versión de Java, junto con el contenedor web de Java deseado. Cuando se usa este método, tanto el código Java como el contenedor web se ejecutan desde Archivos de programa. Los demás métodos colocan el contenedor web y posiblemente el código JVM en espacio del disco. Cuando se usa este modelo, no tiene acceso para editar archivos en esta parte del sistema de archivos. Esto significa que no puede hacer cosas como configurar el archivo *server.xml* o colocar los archivos de la biblioteca en la carpeta */lib*. Para obtener más información, consulte la sección [Creación y configuración de una aplicación web de Java](#appsettings) más adelante en este tutorial.
	
2. Use una plantilla en Azure Marketplace.

	Azure Marketplace incluye plantillas que crean y configuran automáticamente aplicaciones web de Java con contenedores web de Tomcat o Jetty. Los contenedores web creados por las plantillas son configurables. Para obtener más información, consulte la sección [Uso de una plantilla de Java en Azure Marketplace](#marketplace) de este tutorial.
  
3. Creación de una aplicación y, después, copia y edición manual de los archivos de configuración

	Es posible que quiera hospedar una aplicación Java personalizada que no se implemente en ninguno de los contenedores web proporcionados por el Servicio de aplicaciones. Por ejemplo:
	
	* Su aplicación Java requiere una versión de Tomcat o Jetty que no es directamente compatible con el Servicio de aplicaciones o que no se proporciona en la galería.
	* La aplicación Java toma las solicitudes HTTP y no se implementa como un WAR en un contenedor web ya existente.
	* Desea configurar el contenedor web desde el principio. 
	* Quiere usar una versión de Java que no es compatible con el Servicio de aplicaciones y desea cargarla.

	En todos estos casos, puede crear una aplicación mediante el Portal de Azure y después proporcionar los archivos en tiempo de ejecución adecuados manualmente. En este caso, los archivos se deducirán de las cuotas de espacio de almacenamiento para su plan del Servicio de aplicaciones. Para obtener más información, consulte [Carga de una aplicación web de Java personalizada en Azure].

## <a name="portal"></a> Creación y configuración de una aplicación web de Java

En esta sección se muestra cómo crear una aplicación web y configurarla para Java mediante la hoja **Configuración de la aplicación** del portal.

1. Inicie sesión en el [Portal de Azure].

2. Haga clic en **Nuevo > Web y móvil > Aplicación web**.

	![Nueva aplicación web][newwebapp]

4. Escriba un nombre para la aplicación web en el cuadro **Aplicación web**.

	Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.

5. Seleccione un **Grupo de recursos** o cree uno nuevo.

	Para obtener más información acerca de los grupos de recursos, consulte [Uso del Portal de Azure para administrar los recursos de Azure].

6. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.

	Para más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure].

7. Haga clic en **Crear**.

	![Crear aplicación web][newwebapp2]
 
8. Cuando se haya creado la aplicación web, haga clic en **Aplicaciones web > {su aplicación web}**.
 
	![Seleccionar aplicación web][selectwebapp]

9. En la hoja **Aplicación web**, haga clic en **Configuración**.

10. Haga clic en **Configuración de la aplicación**.

11. Elija la **versión Java** deseada.

12. Elija la **versión secundaria de Java** deseada. Si selecciona **Más reciente**, la aplicación usará la versión secundaria más reciente que está disponible en el Servicio de aplicaciones para esa versión principal de Java. El elemento **Más reciente** es único para las aplicaciones Java creadas desde **Configuración de la aplicación**. Si crea su aplicación de Java desde la galería, tendrá que administrar su propio contenedor y los cambios de JVM.

12. Elija el **contenedor web** deseado. Si selecciona un nombre de contenedor que comienza con **Más reciente**, la aplicación se mantendrá en la versión más reciente de esa versión principal del contenedor web que está disponible en el Servicio de aplicaciones.

	![Versiones de contenedor web][versions]

13. Haga clic en **Guardar**.

	En unos momentos, su aplicación web estará basada en Java y configurada para usar el contenedor web seleccionado.

14. Haga clic en **Aplicaciones web > {su nueva aplicación web}**.

15. Haga clic en la **URL** para buscar el nuevo sitio.

	La página web confirma que ha creado una aplicación web basada en Java.

## <a name="marketplace"></a> Uso de una plantilla Java en Azure Marketplace

En esta sección se muestra cómo usar Azure Marketplace para crear una aplicación web de Java. También puede usarse el mismo flujo general para crear una aplicación de API o una aplicación móvil basada en Java.

1. Inicie sesión en el [Portal de Azure].

2. Haga clic en **Nuevo > Marketplace**.

	![Nuevo Marketplace][newmarketplace]

3. Haga clic en **Web y móvil**.

	Puede que tenga que desplazarse a la izquierda para ver la hoja **Marketplace** donde puede seleccionar **Web y móvil**.

4. En el cuadro de texto Buscar, escriba el nombre de un servidor de aplicaciones Java, como **Apache Tomcat** o **Jetty** y luego presione Entrar.

5. En los resultados de búsqueda, haga clic en el servidor de aplicaciones Java.

	![Jetty web y móvil][webmobilejetty]

6. En la primera hoja **Apache Tomcat** o **Jetty**, haga clic en **Crear**.

	![Hoja del portal Jetty][jettyblade]

7. En la siguiente hoja **Apache Tomcat** o **Jetty**, escriba un nombre para la aplicación web en el cuadro **Aplicación web**.

	Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.

8. Seleccione un **Grupo de recursos** o cree uno nuevo.

	Para obtener más información acerca de los grupos de recursos, consulte [Uso del Portal de Azure para administrar los recursos de Azure].

9. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.

	Para más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure].

10. Haga clic en **Crear**.

	![Portal Jetty: creación][jettyportalcreate2]

	En poco tiempo, normalmente menos de un minuto, Azure termina de crear la nueva aplicación web.

11. Haga clic en **Aplicaciones web > {su nueva aplicación web}**.

12. Haga clic en la **URL** para buscar el nuevo sitio.

	![URL de Jetty][jettyurl]

	Tomcat incluye un conjunto predeterminado de páginas; si eligió Tomcat, verá una página similar a la del ejemplo siguiente.

	![Aplicación web con Apache Tomcat][tomcat]

	Si eligió Jetty, verá una página similar al ejemplo siguiente. Jetty no tiene un conjunto de páginas predeterminadas, por lo que se vuelve a usar aquí el mismo JSP usado para un sitio Java vacío.

	![Aplicación web con Jetty][jetty]

Ahora que ha creado la aplicación web con un contenedor de aplicaciones, consulte la sección [Pasos siguientes](#next-steps) para obtener información sobre cómo cargar la aplicación en la aplicación web.

## Pasos siguientes

En este momento, dispone de un servidor de aplicaciones Java que se ejecuta en la aplicación web en el Servicio de aplicaciones de Azure. Para implementar su propio código en la aplicación web, consulte [Incorporación de una aplicación o página web a la aplicación web de Java].

Para obtener más información sobre el desarrollo de aplicaciones Java en Azure, consulte el [Centro de desarrolladores de Java].

<!-- URL List -->

[Incorporación de una aplicación o página web a la aplicación web de Java]: ./web-sites-java-add-app.md
[Introducción detallada sobre los planes del Servicio de aplicaciones de Azure]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portal de Azure]: https://portal.azure.com/
[activar los beneficios de suscripción a Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[registrarse para obtener una evaluación gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[prueba gratuita del Servicio de aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=523751
[aplicación web de Java en el Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro de desarrolladores de Java]: /develop/java/
[Uso del Portal de Azure para administrar los recursos de Azure]: ../azure-portal/resource-group-portal.md
[Carga de una aplicación web de Java personalizada en Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png

<!---HONumber=AcomDC_0608_2016-->