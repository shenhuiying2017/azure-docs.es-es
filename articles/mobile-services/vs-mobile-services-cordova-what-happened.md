<properties 
	pageTitle="" 
	description="Describe qué ha ocurrido con el proyecto de Servicio móvil de Azure en Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# ¿Qué le ha ocurrido a mi proyecto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

#####Se han agregado referencias.

Se ha habilitado el complemento de cliente de servicio móvil de Azure con todas las aplicaciones híbridas multidispositivo.
  
#####Conexión de valores de cadena para Servicios móviles

En `services\mobileServices\settings`, se ha generado un nuevo archivo JavaScript (.js) con **MobileServiceClient** que contiene la dirección URL y la clave de aplicación del servicio móvil seleccionado. El archivo contiene la inicialización de un objeto de cliente de servicio móvil, similar al código siguiente.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Más información acerca de Servicios móviles](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO2-->