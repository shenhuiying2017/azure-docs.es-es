<properties 
	pageTitle="¿Qué sucede cuando agrega Servicios móviles a una aplicación Javascript mediante Visual Studio Connected Services?" 
	description="Describe lo que le ha ocurrido al proyecto de Servicios móviles de Azure en Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="tarcher"/>

# ¿Qué sucede con mi proyecto de Javascript al agregar Servicios móviles de Azure para dispositivos móviles mediante Connected Visual Studio Services?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###¿Qué le ha ocurrido a mi proyecto?

#####Paquete de NuGet agregado

Se instaló el paquete de NuGet **WindowsAzure.MobileServices.WinJS**, incluida la biblioteca de Servicios móviles de Azure en el archivo `js\MobileServices.js`.
  
#####Conexión de valores de cadena para Servicios móviles 

En la carpeta `services\mobileServices\settings`, se ha generado un nuevo archivo JavaScript (.js) con **MobileServiceClient** que contiene la dirección URL y la clave de aplicación del servicio móvil seleccionado.


#####Referencias agregadas a default.html

Las referencias a `MobileServices.js` y el archivo de configuración se agregaron a `default.html`.


#####Archivos de servicios conectados agregados

En la carpeta servicios, se han agregado los archivos de configuración de servicios conectados.



 

<!---HONumber=Nov15_HO3-->