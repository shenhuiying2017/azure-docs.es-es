<properties
	pageTitle="Cómo crear una aplicación web: administración de servicios de Azure"
	description="Obtenga información sobre cómo crear una aplicación web con el Portal de Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#Cómo crear una aplicación web

En este tema se muestra cómo puede crear una aplicación web desde la galería o con el Portal de Azure.

Para obtener información sobre cómo implementar su contenido en una aplicación web que ha creado, consulte la sección **Implementación** en [Aplicaciones web de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de inicio de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##<a name="createawebsiteportal"></a>Cómo: Crear una aplicación web con el Portal de Azure

Siga estos pasos para crear una aplicación web en Azure.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

2. Haga clic en el icono **Nueva**, situado en la parte inferior izquierda del portal.

3. Haga clic en el icono **Web y móvil**, haga clic en el icono **Aplicación web**, escriba un valor para la dirección URL y luego haga clic en **Crear** en la esquina inferior derecha de la hoja Crear.

4. Cuando se haya creado la aplicación web, se mostrará el texto **La implementación en el grupo de recursos <nombre del grupo de recursos> se realizó correctamente**.

5. En el portal, haga clic en el nombre de la aplicación web que se muestra en la lista de aplicaciones web para abrir la hoja de la aplicación web.

6. En la hoja, se le proporciona la posibilidad de obtener herramientas de desarrollo de aplicaciones web, configurar las publicaciones de su aplicación web o configurar la implementación desde un proveedor de control de código fuente como TFS o Git. Publicación FTP está configurada de forma predeterminada para las aplicaciones web y el nombre de FTP Host se muestra en la sección **Essentials** de la hoja de la aplicación web. Antes de publicar con FTP o Git, elija la opción de **Restablecer perfil de publicación** en la hoja de la aplicación web para que pueda autenticarse en el FTP Host o en el repositorio al distribuir contenido a la aplicación web.

7. La hoja **Configuración** expone la configuración de la aplicación web, como:

	- versión de. NET, PHP, Java o Python para la edición de la aplicación web
	- en Visual Studio Online.
	- Enlaces SSL
	- Nombres de dominio personalizados
	- autenticación y autorización
	- diagnóstico de aplicaciones y sitios
	- supervisión de extremos
	- Opciones de registro
	- configuración de la aplicación para el entorno de Azure (sobrescribiendo <appSettings> en el archivo Web.config del entorno de desarrollo, por ejemplo)
	- cadenas de conexión (sobrescribiendo <connectionStrings> en el archivo Web.config del entorno de desarrollo, por ejemplo)
	- procesadores de scripts para extensiones de archivo específicas como *.php

##<a name="howtocreatefromgallery"></a> Eliminación Creación de una aplicación web desde la Galería

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Cómo: Eliminar una aplicación web
Las aplicaciones web se eliminan con el icono **Eliminar** del Portal de Azure. El icono **Eliminar** se encuentra en la parte superior de la hoja de la aplicación web.

##<a name="nextsteps"></a> Pasos siguientes

Para obtener más información, consulte [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

## Lo que ha cambiado
* Para obtener instrucciones sobre el cambio de sitios web al servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener instrucciones sobre el cambio del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->