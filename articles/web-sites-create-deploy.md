<properties 
	pageTitle="Creación de sitios web - Administración de servicios de Azure" 
	description="Obtenga información sobre cómo crear un sitio web con el Portal de administración de Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Creación de un sitio web

Este tema muestra cómo puede crear un sitio web desde la galería o con el portal de administración.

Para obtener información acerca de cómo implementar contenidos en un sitio web Azure que haya creado previamente, consulte la sección **Implementar** en [Sitios web Azure](/es-es/documentation/services/web-sites/).

## Tabla de contenido ##

- [Uso de un Sitio web con el Portal de administración](#createawebsiteportal)
- [Uso de un Sitio web desde la galería](#howtocreatefromgallery)
- [Uso de un Sitio web](#deleteawebsite)
- [Pasos siguientes](#nextsteps)

##<a name="createawebsiteportal"></a>Cómo: Creación de un sitio web con el Portal de administración

Siga estos pasos para crear un sitio web en Azure.
	
1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).

2. Haga clic en el botón **Crear nuevo**, situado en la parte inferior izquierda del Portal de administración.

3. Haga clic en el icono **Website**, en el icono **Creación rápida**, escriba un valor para la dirección URL y, a continuación, haga clic en la marca de verificación situada junto a **Crear sitio web** en la esquina inferior derecha de la página.

4. Cuando se ha creado el sitio Web, se mostrará el texto **sitio Web de creación <*nombre del sitio Web*> correcta**. Puede explorar el sitio web; para ello, haga clic en **Examinar** en la parte inferior de la página del portal.

5. En el portal, haga clic en el nombre del sitio web que se muestra en la lista para abrir la página de administración**Inicio rápido** del sitio web.

6. En la página **Inicio rápido**, se le proporciona la posibilidad de obtener herramientas de desarrollo de sitios web, configurar las publicaciones de su sitio web o configurar la implementación desde un proveedor de control de código fuente como TFS o Git. La publicación FTP está configurada para sitios web de manera predeterminada, y el nombre del host FTP se muestra en la sección **Vista rápida** de la página **Panel** en **Nombre del host FTP**. Antes de publicar con FTP o Git, elija la opción**Restablecer credenciales de implementación** en la página **Panel** para poder autenticarse en el host FTP o el repositorio Git al implementar contenidos en su sitio web.

7. La página de administración **Configurar** expone la configuración del sitio web, como:

	- La versión de .NET Framework o PHP para la aplicación web
	- Enlaces SSL
	- Nombres de dominio personalizados
	- Opciones de registro
	- La configuración de la aplicación para el entorno de Azure (por ejemplo, mediante el reemplazo <appSettings> en el archivo Web.config de su entorno de desarrollo)
	- Las cadenas de conexión (por ejemplo, mediante el reemplazo <connectionStrings> en el archivo Web.config de su entorno de desarrollo)
	- procesadores de scripts para extensiones de archivo específicas como *.php

##<a name="howtocreatefromgallery"></a>Cómo: Creación de un sitio web desde la galería

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Cómo: Eliminación de un sitio web
Los sitios web se eliminan mediante el icono**Eliminar** del Portal de administración de Azure. El icono **Eliminar** está disponible en el portal de Azure al hacer clic en **Sitios web** para visualizar todos sus sitios web y en la parte inferior de todas las páginas de administración del sitio web.

##<a name="nextstepsPasos siguientes"></a>

Para obtener más información, consulte [Sitios web Azure](/es-es/documentation/services/web-sites/).




<!--HONumber=42-->
