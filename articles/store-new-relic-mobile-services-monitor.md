<properties 
	pageTitle="Almacenamiento de scripts de servidor en control de código fuente - Servicios móviles de Azure" 
	description="Aprenda la manera de usar el complemento New Relic para supervisar el servicio móvil." 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>

# Uso de New Relic para supervisar Servicios móviles

Este tema muestra cómo configurar el complemento New Relic de terceros para trabajar con Servicios móviles de Azure y proporcionar una supervisión mejorada del servicio móvil. 

El tutorial le guiará a través de los siguientes pasos:

1. [Suscripción a New Relic a través de la Tienda de Azure].
2. [Instalación del módulo New Relic].
3. [Habilitación del análisis del desarrollador de New Relic para el servicio móvil].
4. [Supervisión del servicio móvil en el panel de New Relic].

Para completar este tutorial, ya debe haber creado un servicio móvil tras completar los tutoriales [Introducción a los Servicios móviles] o [Introducción a los datos].

##<a name="sign-up"></a>Suscripción a New Relic a través de la Tienda de Azure

El primer paso es comprar el servicio New Relic. Este tutorial le muestra cómo comprar este servicio desde la Tienda de Azure. Servicios móviles es compatible con las suscripciones de New Relic adquiridas fuera de la Tienda de Azure.

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

##<a name="install-module"></a>Instalación del módulo New Relic

Una vez que se haya suscrito al servicio New Relic, tiene que instalar el módulo New Relic Node.js en el servicio móvil. Debe tener el control del código fuente habilitado para el servicio móvil a fin de cargar este módulo.

1. Si aún no lo ha hecho, siga los pasos del tutorial [Almacenamiento de scripts de servidor en control de código fuente] para habilitar el control de código fuente para el servicio móvil, clone el repositorio e instale <a href="http://nodejs.org/" target="_blank">Node Package Manager (NPM)</a>.

2. Desplácese a la carpeta  `.\service` del repositorio Git local y, a continuación, ejecute el siguiente comando desde el símbolo del sistema:

		npm install newrelic

	NPM instala el [módulo New Relic][newrelic] en el subdirectorio  `\newrelic`. 

3. Abra una herramienta de línea de comandos Git, como **GitBash** (Windows) o **Bash** (shell de Unix) y escriba el siguiente comando en el símbolo del sistema de Git: 

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	De esta forma, se carga un nuevo módulo  `newrelic` en el servicio móvil. 

A continuación, habilite la supervisión de New Relic para el servicio móvil en el [Portal de administración][Portal de administración de Azure]. 

##<a name="enable-service"></a>Habilitación del análisis del desarrollador de New Relic para el servicio móvil

1. En el [Portal de administración][Portal de administración de Azure], seleccione el servicio móvil y haga clic en la pestaña **Configure**.

	![][0]

2. Desplácese a**Análisis del desarrollador** y realice uno de los procedimientos siguientes, según cómo haya efectuado la compra de la suscripción a New Relic:

	+ Compra en la Tienda de Azure:

		Haga clic en **Add-on**, seleccione el complemento New Relic en **Choose add-on** y, a continuación, haga clic en **Save**.

		![][1]

	+ Compra directamente desde New Relic: 

		Haga clic en **Custom**, seleccione New Relic en **Provider**, escriba la clave y, a continuación, haga clic en **Save**.

		![][2]

		La clave puede obtenerse desde el panel de New Relic.

3. Una vez que se haya completado el registro, verá un nuevo valor en **App settings**:

	![][3] 

##<a name="monitor"></a>Supervisión del servicio móvil en el panel de New Relic

1. Ejecute la aplicación de cliente para generar solicitudes de lectura, creación, actualización y eliminación del servicio móvil.

2. Espere unos minutos para que se procesen los datos y, a continuación, desplácese al panel de New Relic.

	Si ha adquirido la suscripción a New Relic como un complemento, selecciónela en el [Portal de administración ][Portal de administración de Azure] y haga clic en **Administrar**.

3. En New Relic, haga clic en **Applications** y, a continuación, haga clic en el servicio móvil correspondiente.

	![][4]

4. Haga clic en **Web transactions** para ver las solicitudes recientes que acaba de realizar en el servicio móvil:

	![][5]

##<a name="next-steps">Pasos siguientes</a>

+ Para optimizar el rendimiento de la aplicación móvil **iOS**/**Android**, consulte [New Relic Mobile].
+ Para obtener información sobre el precio, consulte la [página de New Relic en la Tienda de Azure].
+ Para obtener más información sobre el uso de New Relic, consulte la [información general sobre aplicaciones] en la documentación de New Relic. 

<!-- Anchors. -->
[Suscripción a New Relic a través de la Tienda de Azure]: #sign-up
[Instalación del módulo New Relic]: #install-module
[Habilitación del análisis del desarrollador de New Relic para el servicio móvil]: #enable-service
[Supervisión del servicio móvil en el panel de New Relic]: #monitor
[Pasos siguientes]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Control de código fuente]: http://msdn.microsoft.com/es-es/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Uso de scripts del servidor en Servicios móviles]: /es-es/develop/mobile/how-to-guides/work-with-server-scripts.md

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Documentación sobre la API de Node.js: Módulos]: http://nodejs.org/api/modules.html
[Almacenamiento de scripts de servidor en control de fuente]: /es-es/develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Página de New Relic en la Tienda de Azure]: /es-es/gallery/store/new-relic/new-relic/
[Información general sobre aplicaciones]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring







<!--HONumber=42-->
