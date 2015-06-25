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
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>

# Uso de New Relic para supervisar Servicios móviles

Este tema muestra cómo configurar el complemento New Relic de terceros para trabajar con Servicios móviles de Azure y proporcionar una supervisión mejorada del servicio móvil.

El tutorial le guiará a través de los siguientes pasos:

1. [Suscripción a New Relic a través de la Tienda de Azure]
2. [Instalación del módulo New Relic].
3. [Habilitación del análisis del desarrollador de New Relic para el servicio móvil].
4. [Supervisión del servicio móvil en el panel de New Relic].

Para completar este tutorial, ya debe haber creado un servicio móvil tras completar el tutorial [Introducción a los Servicios móviles] o [Introducción a los datos].

##<a name="sign-up"></a>Suscripción a New Relic a través de la Tienda de Azure

El primer paso es comprar el servicio New Relic. Este tutorial le muestra cómo comprar este servicio desde la Tienda de Azure. Servicios móviles es compatible con las suscripciones de New Relic adquiridas fuera de la Tienda de Azure.

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).

2. En el panel inferior del portal de administración, haga clic en **Nuevo**.

3. Haga clic en **Tienda**.

4. En el cuadro de diálogo **Elegir un complemento**, seleccione **New Relic** y haga clic en **Siguiente**.

5. En el cuadro de diálogo **Personalizar complemento**, seleccione el plan de New Relic que quiera.

7. Escriba el nombre con el que aparecerá el servicio New Relic en la configuración de Azure o use el valor predeterminado **NewRelic**. El nombre debe ser único en la lista de elementos suscritos de la Tienda de Azure.

8. Elija un valor para la región; por ejemplo, **Oeste de EE. UU.**.

9. Haga clic en **Siguiente**.

10. En el cuadro de diálogo **Revisar compra**, revise la información del plan y los precios, así como los términos legales. Si acepta los términos, haga clic en **Comprar**.

11. Cuando haya hecho clic en **Comprar**, comenzará el proceso de creación de la cuenta de New Relic. Puede supervisar el estado en el Portal de administración de Azure.

##<a name="install-module"></a>Instalación del módulo New Relic

Una vez que se haya suscrito al servicio New Relic, tiene que instalar el módulo New Relic Node.js en el servicio móvil. Debe tener el control del código fuente habilitado para el servicio móvil a fin de cargar este módulo.

1. Si aún no lo ha hecho, siga los pasos del tutorial [Almacenamiento de scripts de servidor en control de código fuente] para habilitar el control de código fuente para el servicio móvil, clone el repositorio e instale <a href="http://nodejs.org/" target="_blank">Node Package Manager (NPM)</a>.

2. Desplácese a la carpeta `.\service` del repositorio Git local y ejecute el siguiente comando desde el símbolo del sistema:

		npm install newrelic

	NPM instala el [módulo New Relic][newrelic] (información en inglés) en el subdirectorio `\newrelic`.

3. Abra una herramienta de línea de comandos Git, como **GitBash** (Windows) o **Bash** (shell de Unix) y escriba el siguiente comando en el símbolo del sistema de Git:

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	De esta forma, se carga un nuevo módulo `newrelic` en el servicio móvil.

A continuación, habilitará la supervisión de New Relic para el servicio móvil en el [Portal de administración][Azure Management Portal].

##<a name="enable-service"></a>Habilitación del análisis del desarrollador de New Relic para el servicio móvil

1. En el [Portal de administración][Azure Management Portal], seleccione el servicio móvil y haga clic en la pestaña **Configurar**.

	![][0]

2. Desplácese a **Análisis del desarrollador** y, en función de cómo compró la suscripción a New Relic, realice uno de los procedimientos siguientes:

	+ Compra en la Tienda de Azure:

		Haga clic en **Complemento**, seleccione el complemento New Relic en **Elegir complemento** y haga clic en **Guardar**.

		![][1]

	+ Compra directamente desde New Relic:

		Haga clic en **Personalizar**, seleccione **New Relic** en **Proveedor**, escriba la clave y haga clic en Guardar.

		![][2]

		La clave puede obtenerse desde el panel de New Relic.

3. Una vez completado el registro, verá un nuevo valor en **Configuración de la aplicación**:

	![][3]

##<a name="monitor"></a>Supervisión del servicio móvil en el panel de New Relic

1. Ejecute la aplicación de cliente para generar solicitudes de lectura, creación, actualización y eliminación del servicio móvil.

2. Espere unos minutos para que se procesen los datos y, a continuación, desplácese al panel de New Relic.

	Si adquirió la suscripción a New Relic como complemento, selecciónela en el [Portal de administración][Azure Management Portal] y haga clic en **Administrar**.

3. En New Relic, haga clic en **Applications** (Aplicaciones) y luego haga clic en el servicio móvil correspondiente.

	![][4]

4. Haga clic en **Web transactions** (Transacciones web) para ver las solicitudes recientes que acaba de realizar en el servicio móvil:

	![][5]

##<a name="next-steps"> </a>Pasos siguientes

+ Para optimizar el rendimiento de la aplicación móvil **iOS**/**Android**, consulte [New Relic Mobile].
+ Para obtener información sobre precios, consulte la [página de New Relic en la Tienda de Azure].
+ Para obtener más información sobre el uso de New Relic, consulte la sección de [descripción general de las aplicaciones] en la documentación de New Relic. 

<!-- Anchors. -->
[Suscripción a New Relic a través de la Tienda de Azure]: #sign-up
[Instalación del módulo New Relic]: #install-module
[Habilitación del análisis del desarrollador de New Relic para el servicio móvil]: #enable-service
[Supervisión del servicio móvil en el panel de New Relic]: #monitor
[Next steps]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Work with server scripts in Mobile Services]: ../work-with-server-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[Almacenamiento de scripts de servidor en control de código fuente]: /develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[página de New Relic en la Tienda de Azure]: /gallery/store/new-relic/new-relic/
[descripción general de las aplicaciones]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started/
[Introducción a los datos]: /develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring


<!--HONumber=54--> 