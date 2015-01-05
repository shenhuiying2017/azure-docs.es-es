<properties urlDisplayName="How to create" pageTitle="Creación de sitios web - Administración de servicios de Azure" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Creación de un sitio web

Este tema muestra cómo puede crear un sitio web desde la galería o con el portal de administración.

Para obtener información acerca de cómo implementar contenidos en un sitio web Azure que haya creado previamente, consulte la sección **Implementar** en [Sitios web Azure](/en-us/documentation/services/web-sites/).

## Tabla de contenido ##

- [Creación de un Sitio web con el Portal de administración](#createawebsiteportal)
- [Creación de un Sitio web desde la galería](#howtocreatefromgallery)
- [Eliminación de un sitio web](#deleteawebsite)
- [Pasos siguientes](#nextsteps)

##<a name="createawebsiteportal"></a>Reproducción de Creación de un sitio web con el Portal de administración

Siga estos pasos para crear un sitio web en Azure.
	
1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).

2. Haga clic en el botón **Crear nuevo**, situado en la parte inferior izquierda del Portal de administración.

3. Haga clic en el icono **Website**, en el icono **Creación rápida**, escriba un valor para la dirección URL y, a continuación, haga clic en la marca de verificación situada junto a **Crear sitio web** en la esquina inferior derecha de la página.

4. Una vez creado el sitio web, se mostrará el texto **La creación del sitio web <*nombre del sitio web*> se ha realizado correctamente**. Puede explorar el sitio web; para ello, haga clic en **Examinar** en la parte inferior de la página del portal.

5. En el portal, haga clic en el nombre del sitio web que se muestra en la lista para abrir la página de administración **Inicio rápido** del sitio web.

6. En la página **Inicio rápido**, se le proporciona la posibilidad de obtener herramientas de desarrollo de sitios web, configurar las publicaciones de su sitio web o configurar la implementación desde un proveedor de control de código fuente como TFS o Git. La publicación FTP está configurada para sitios web de manera predeterminada, y el nombre del host FTP se muestra en la sección **Vista rápida** de la página **Panel** en **Nombre del host FTP**. Antes de publicar con FTP o Git, elija la opción **Restablecer credenciales de implementación** en la página **Panel** para poder autenticarse en el host FTP o el repositorio Git al implementar contenidos en su sitio web.

7. La página de administración **Configurar** expone la configuración del sitio web, como:

	- La versión de .NET Framework o PHP para la aplicación web
	- Enlaces SSL
	- Nombres de dominio personalizados
	- Opciones de registro
	- configuración de la aplicación para el entorno de Azure (sobrescribiendo <appSettings> en el archivo Web.config del entorno de desarrollo, por ejemplo)
	- cadenas de conexión (sobrescribiendo <connectionStrings> en el archivo Web.config del entorno de desarrollo, por ejemplo)
	- procesadores de scripts para extensiones de archivo específicas como *.php

##<a name="howtocreatefromgallery"></a>Reproducción de Creación de un sitio web desde la galería

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Reproducción de Eliminación de un sitio web
Los sitios web se eliminan mediante el icono **Eliminar** del Portal de administración de Azure. El icono **Eliminar** está disponible en el portal de Azure al hacer clic en **Sitios web** para visualizar todos sus sitios web y en la parte inferior de todas las páginas de administración del sitio web.

##<a name="nextsteps"></a>Pasos siguientes

Para obtener más información, consulte [Sitios web Azure](/en-us/documentation/services/web-sites/).

<!--HONumber=35.1-->
