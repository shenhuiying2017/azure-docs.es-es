<properties title="Using Mobile Services with Cordova Projects" pageTitle="" metaKeywords="Azure, Cordova, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/11/2014" ms.author="ghogen" />

### Uso de Servicios móviles con proyectos Cordova en Visual Studio 2015 Preview

Para utilizar Servicios móviles de Azure en proyectos Cordova en Visual Studio 2015 Preview, deberá aplicar la siguiente solución.

1. En el proyecto Cordova de Visual Studio 2015 Preview, abra Config.xml y en la ficha **Complementos**, habilite el complemento **Servicios móviles de Microsoft Azure**.<br/>
![][1]

2. En index.html, elimine las líneas que hacen referencia a **MobileServices.Web-1.2.2.min.js**.<br/>

<PRE style="prettyprint">
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>MyCordovaApp>/title>

    <!--MyCordovaApp references -->
    <link href="css/index.css" rel="stylesheet" />
</head>
<body>
    <p>Hello world!&lt/p>

    <!--Cordova reference, this is added to your app when it's build. -->
    <script src="cordova.js"></script>
    <script src="scripts/platformOverrides.js"></script>

    <script src="scripts/index.js"></script>

    <!-- yourservicename references -->
    <span style="background-color:yellow"><script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js">&lt/script></span>
    <script src="/services/mobileservices/settings/yourservicename.js"></script>
</body>
</html>
</PRE>

3. Abra {sunombredeservicio}.js en servicios -> mobileServices -> carpetas de configuración, y sustituya el fragmento de código existente por el siguiente:

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 
