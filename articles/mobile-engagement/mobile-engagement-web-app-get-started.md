<properties
	pageTitle="Introducción a Azure Mobile Engagement para Aplicaciones web | Microsoft Azure"
	description="Aprenda a usar Azure Mobile Engagement con análisis y notificaciones push para Aplicaciones web."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="js"
	ms.topic="hero-article"
	ms.date="06/01/2016"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para Aplicaciones web

[AZURE.INCLUDE [Modificador del tutorial de imagen prominente](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile Engagement para conocer el uso de Aplicaciones web.

Este tutorial requiere lo siguiente:

+ Visual Studio 2015, o cualquier otro editor que prefiera
+ [SDK web](http://aka.ms/P7b453) 

Este SDK web está en vista previa, solo admite Analytics por el momento y aún no admite el envío de notificaciones push desde el explorador o desde la aplicación.

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##Configuración de Mobile Engagement para una aplicación web

[AZURE.INCLUDE [Creación de la aplicación de Mobile Engagement en el portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement

En este tutorial se presenta una "integración básica", que es el conjunto mínimo requerido para recopilar los datos.

Crearemos una aplicación web básica con Visual Studio para demostrar la integración, aunque también puede seguir los pasos con cualquier aplicación web que no se haya creado con Visual Studio.

###Creación de una aplicación web nueva

En los siguientes pasos se supone el uso de Visual Studio de 2015 aunque los pasos son similares en versiones anteriores de Visual Studio.

1. Inicie Visual Studio y, en la pantalla **Principal**, seleccione **Nuevo proyecto**.

2. En el elemento emergente, seleccione **Web** -> **ASP.Net Web Application** (Aplicación Web ASP.Net). Rellene los campos **Nombre**, **Ubicación** y **Nombre de la solución**, y haga clic en **Aceptar**.

3. En el elemento emergente **Seleccione una plantilla**, seleccione **Vacío** en **ASP.Net 4.5 Templates** (Plantillas de ASP.Net 4.5) y haga clic en **Aceptar**.

Ya ha creado un nuevo proyecto de aplicación web en blanco en el que se integrará el SDK de web de Azure Mobile Engagement.

###Conectar la aplicación al back-end de Mobile Engagement

1. Cree una carpeta nueva llamada **javascript** en la solución y agréguele el archivo JS de SDK de web **azure engagement.js**. 

2. Agregue un nuevo archivo llamado **main.js** a esta carpeta con el código siguiente. Asegúrese de que actualiza la cadena de conexión. Este objeto `azureEngagement` se utilizará para acceder a los métodos del SDK de web.

		var azureEngagement = {
		    debug: true,
		    connectionString: 'xxxxx'
		};

	![Visual Studio con archivos js][1]

##Habilitación de la supervisión en tiempo real

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, es preciso enviar al menos una actividad al back-end de Mobile Engagement. Una actividad en el contexto de una aplicación web es una página web.

1. Cree una nueva página denominada **home.html** en la solución y establézcala como página de inicio de la aplicación web. 
2. Incluya los dos archivos de JavaScript que agregamos antes en esta página, para lo que debe agregar lo siguiente dentro de la etiqueta body. 

	    <script type="text/javascript" src="javascript/main.js"></script>
	    <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Actualice la etiqueta body para llamar al método `startActivity` de EngagementAgent.
		
		<body onload="engagement.agent.startActivity('Home')">

4. Así será **home.html**.
		
		<html>
		<head>
			...
		</head>
		<body onload="engagement.agent.startActivity('Home')">
		    <script type="text/javascript" src="javascript/main.js"></script>
		    <script type="text/javascript" src="javascript/azure-engagement.js"></script>
		</body>
		</html>

##Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Conectar la aplicación con la supervisión en tiempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##Ampliación del análisis

Estos son todos los métodos disponibles actualmente en el SDK de web que se pueden utilizar para realizar análisis:

1. Actividades y páginas web:

		engagement.agent.startActivity(name);
		engagement.agent.endActivity();

2. Eventos
		
		engagement.agent.sendEvent(name, extras);

3. Errors

		engagement.agent.sendError(name, extras);

4. Trabajos

		engagement.agent.startJob(name);
		engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

<!----HONumber=AcomDC_0615_2016-->