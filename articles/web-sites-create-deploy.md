<properties linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create websites - Azure service management" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Creación de un sitio web

Este tema muestra cómo puede crear un sitio web desde la galería o con el portal de administración.

Para obtener información acerca de cómo implementar contenidos en un sitio web Azure que haya creado previamente, consulte la sección **Implementar** en [Sitios web Azure][].

## Tabla de contenido

-   [Direccionamiento del un Sitio web con el Portal de administración][]
-   [Direccionamiento del un Sitio web desde la galería][]
-   [Direccionamiento del un Sitio web][]
-   [Pasos siguientes][]

## <a name="createawebsiteportal"></a>Direccionamiento del Creación de un sitio web con el Portal de administración

Siga estos pasos para crear un sitio web en Azure.

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  Haga clic en el icono **Create New**, situado en la parte inferior izquierda del Portal de administración.

3.  Haga clic en el icono **Sitio web**, haga clic en el icono **Quick Create**, introduzca un valor en el campo URL y, a continuación, haga clic en la marca de verificación situada junto a **Create Web Site** en la esquina inferior derecha de la página.

4.  Una vez creado el sitio web, se mostrará el texto **Creating website \<*nombre del sitio web*\> succeeded**. Puede explorar el sitio web haciendo clic en **Browse** en la parte inferior de la página del portal.

5.  En el portal, haga clic en el nombre del sitio web que se muestra en la lista para abrir la página de administración **Quick Start** del sitio web.

6.  En la página **Quick Start**, se le proporciona la posibilidad de obtener herramientas de desarrollo de sitios web, configurar las publicaciones de su sitio web o configurar la implementación desde un proveedor de control de código fuente como TFS o Git. La publicación FTP está configurada para sitios web de manera predeterminada, y el nombre del host FTP se muestra en la sección **Quick Glance** de la página **Panel**, bajo el título **FTP Host Name**. Antes de publicar con FTP o Git, elija la opción de **Reset deployment credentials** en la página **Panel** para poder autenticarse en el host FTP o el repositorio Git al implementar contenidos en su sitio web.

7.  La página de administración **Configure** muestra la configuración de su sitio web en las siguientes categorías:

-   **General**: Establezca la versión de .NET framework o PHP necesaria para su aplicación web. En los sitios en modo estándar, la opción **Platform** le permite elegir un entorno de 32 o 64 bits.

-   **Certificates**: En modo estándar, puede cargar certificados SSL para los dominios personalizados.

-   **Domain Names**: En los modos compartido y estándar, puede visualizar y administrar los nombres de los dominios personalizados de su sitio web.

-   **SSL Bindings**: En el modo estándar, puede asignar certificados SSL a los nombres de sus dominios personalizados con SSL basada en IP o en SNI.

-   **Implementaciones**: Si configura la implementación desde el control de código fuente, puede configurar sus implementaciones aquí.

-   **Diagnóstico de aplicaciones**: Permite establecer las opciones de recopilación de seguimientos de diagnóstico desde una aplicación web que se haya instrumentado con seguimientos.

-   **Site Diagnostics**: Permite establecer las opciones de registro de información de diagnóstico para su sitio web. Entre las opciones se incluyen el registro de servidores web, los mensajes de error detallados y el seguimiento de solicitudes con error.

-   **Supervisión**: En los sitios web en modo estándar, puede usar esta característica para probar la disponibilidad de los extremos HTTP o HTTPS.

-   **App Settings**: Especifique los pares nombre/valor que se cargarán en su aplicación web al iniciarse. En los sitios .NET, estas configuraciones se insertarán en **AppSettings** de la configuración .NET en tiempo de ejecución y reemplazará la configuración existente. En los sitios PHP y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución.

-   **Connection Strings**: Permite visualizar y editar las cadenas de conexión. En los sitios .NET, estas cadenas de conexión se insertarán en la configuración de **connectionStrings** de la configuración .NET en tiempo de ejecución y reemplazarán las entradas existentes en las que la clave equivalga al nombre de la base de datos vinculada. En los sitios PHP y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución.

-   **Default Documents**: Un documento predeterminado de un sitio web es la página que se muestra de manera predeterminada cuando un usuario navega por un sitio web. Agregue el documento predeterminado de su aplicación web a esta lista si todavía no está presente en ella. El documento predeterminado de su aplicación web debe aparecer en la parte superior de la lista.

-   **Handler Mappings**: Permite especificar los procesadores de script que controlarán solicitudes de extensiones de archivo específicas, como \*.php.

## <a name="howtocreatefromgallery"></a>Direccionamiento del Creación de un sitio web desde la galería

[WACOM.INCLUDE [website-from-gallery][]]

## <a name="deleteawebsite"></a>Direccionamiento del Eliminación de un sitio web

Los sitios web se eliminan mediante el icono **Delete** del Portal de administración de Azure. El icono **Delete** está disponible en el portal de Azure al hacer clic en **Sitios web** para visualizar todos sus sitios web y en la parte inferior de todas las páginas de administración del sitio web.

## <a name="nextsteps"></a>Pasos siguientes

Para obtener más información, consulte [Sitios web Azure][].

  [Sitios web Azure]: /es-es/documentation/services/web-sites/
  [Direccionamiento del un Sitio web con el Portal de administración]: #createawebsiteportal
  [Direccionamiento del un Sitio web desde la galería]: #howtocreatefromgallery
  [Direccionamiento del un Sitio web]: #deleteawebsite
  [Pasos siguientes]: #nextsteps
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [website-from-gallery]: ../includes/website-from-gallery.md
