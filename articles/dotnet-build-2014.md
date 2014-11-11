<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mollybos" />

# Información destacada de la versión de Azure de la primavera de 2014

En este artículo se resumen las herramientas, las características y los temas nuevos que se han presentado para los desarrolladores de Azure .NET en la conferencia Build 2014 y que se encuentran disponibles en el SDK de Azure para NET 2.3, Visual Studio 2013 Update 2 y otras versiones publicadas en primavera.

**Obtenga las herramientas:**

-   [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC]
-   [Azure SDK 2.3][Azure SDK 2.3]
-   [Azure PowerShell][Azure PowerShell]
-   [Interfaz de línea de comandos entre plataformas de Azure][Interfaz de línea de comandos entre plataformas de Azure]

Para obtener información completa sobre las versiones de las herramientas de la primavera de 2014, consulte las [notas de la versión de Azure SDK para .NET 2.3][notas de la versión de Azure SDK para .NET 2.3] y la [página de actualización de productos de Visual Studio 2013][página de actualización de productos de Visual Studio 2013].

[Vea los vídeos de Build][Vea los vídeos de Build] en streaming a petición.

## Tabla de contenido

-   [Publicación y desarrollo web][Publicación y desarrollo web]
-   [Diagnóstico y depuración][Diagnóstico y depuración]
-   [Administración de servicios de Azure en Visual Studio][Administración de servicios de Azure en Visual Studio]
-   [Automatización con PowerShell][Automatización con PowerShell]
-   [Desarrollo móvil con .NET][Desarrollo móvil con .NET]
-   [La biblioteca del cliente de almacenamiento 3.0 y el nuevo emulador de almacenamiento][La biblioteca del cliente de almacenamiento 3.0 y el nuevo emulador de almacenamiento]
-   [Resource Manager][Resource Manager]

## <span id="webdeploy"></span></a>Publicación y desarrollo web

Azure SDK 2.3 y Visual Studio 2013 Update 2 RC incluyen varias actualizaciones que agilizan el desarrollo web y la publicación con Azure. Ahora puede crear un sitio web o una máquina virtual de Azure cuando crea una nueva aplicación web. Cuando esté preparado para publicar el sitio web, puede usar el cuadro de diálogo de publicación web actualizado o los scripts de PowerShell que se agregan a la solución para implementar el sitio directamente en un sitio web o una máquina virtual de Azure. Consulte los siguientes recursos para obtener más información y tutoriales que describen cómo sacar provecho de las características nuevas:

-   [Introducción a Azure y ASP.NET][Introducción a Azure y ASP.NET]
-   [Introducción a Azure Tools para Visual Studio][Introducción a Azure Tools para Visual Studio]
-   [Creación de proyectos Web ASP.NET en Visual Studio 2013][Creación de proyectos Web ASP.NET en Visual Studio 2013]
-   [Build 2014: Novedades para ASP.NET y web en Visual Studio 2013 Update 2 y posterior (vídeo)][Build 2014: Novedades para ASP.NET y web en Visual Studio 2013 Update 2 y posterior (vídeo)]

## <span id="diagnostics"></span></a>Diagnóstico y depuración

Diagnostique de forma remota problemas de la aplicación con la nueva depuración remota para máquinas virtuales y una nueva depuración de código nativo:

-   [Depurar un servicio en la nube o una máquina virtual en Visual Studio][Depurar un servicio en la nube o una máquina virtual en Visual Studio]

Emulator Express es el nuevo emulador local ligero para servicios en la nube. Conozca cómo usarlo para realizar una prueba de los servicios en la nube en la máquina virtual:

-   [Utilizar Emulator Express para ejecutar y depurar un servicio en la nube localmente][Utilizar Emulator Express para ejecutar y depurar un servicio en la nube localmente]

Con Azure SDK 2.3 instalado, ahora puede ver y editar de forma remota sus archivos de sitios web directamente desde el Explorador de servidores, y también ver los archivos de registro de los sitios. Cuando guarda un archivo editado, se vuelve a guardar en el sitio sin necesidad de publicarlo. Para obtener información, consulte:

-   [Solución de problemas de Sitios web Azure en Visual Studio][Solución de problemas de Sitios web Azure en Visual Studio]

## <span id="service-management"></span></a>Administración de servicios de Azure en Visual Studio

Saque provecho de la administración de máquina virtual de Visual Studio, incluida la capacidad de crear VM desde dentro del IDE:

-   [Crear máquinas virtuales de Azure en Visual Studio][Crear máquinas virtuales de Azure en Visual Studio]
-   [Tener acceso a Máquinas virtuales de Azure desde el Explorador de servidores][Tener acceso a Máquinas virtuales de Azure desde el Explorador de servidores]

También hemos realizado varias mejoras para ayudarle a administrar otros servicios de Azure de forma más eficaz desde el Explorador de servidores. Para obtener información, consulte:

-   [Exploración de los recursos del Service Bus con el Explorador de servidores de Visual Studio][Exploración de los recursos del Service Bus con el Explorador de servidores de Visual Studio]
-   [Explorar recursos de almacenamiento con el Explorador de servidores][Explorar recursos de almacenamiento con el Explorador de servidores]

## <span id="automation"></span></a>Automatización con PowerShell y las API

Instale Azure PowerShell para sacar provecho de los nuevos cmdlets para sitios web, WebJobs, etc. Escriba automatización con un nivel mayor de personalización mediante la API de administración de servicios para .NET. Para obtener información, consulte:

-   [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell]
-   [Azure PowerShell][1]
-   [Build 2014: Automatización en todas partes con los nuevos SDK; herramientas y servicios de Azure (vídeo)][Build 2014: Automatización en todas partes con los nuevos SDK; herramientas y servicios de Azure (vídeo)]

Creer scripts de PowerShell directamente en Visual Studio y úselos para automatizar la creación de entornos:

-   [Utilizar scripts de Windows PowerShell para la publicación en entornos de desarrollo y pruebas][Utilizar scripts de Windows PowerShell para la publicación en entornos de desarrollo y pruebas]

## <span id="mobile"></span></a>Desarrollo móvil con .NET

Servicios móviles de Azure ahora proporciona una opción para usar back-end basados en .NET para las plataformas móviles de destino de aplicaciones móviles, incluida la Tienda Windows, Windows Phone, iOS y Android. Para obtener más información, consulte:

-   [Descripción de la nube: el backend .NET de Servicios móviles de Azure (vídeo)][Descripción de la nube: el backend .NET de Servicios móviles de Azure (vídeo)]
-   [Centro de desarrollo móvil de Azure][Centro de desarrollo móvil de Azure]

Visual Studio 2013 Update 2 incluye también nueva compatibilidad con el desarrollo móvil, lo que incluye compatibilidad con depuración remota y la integración de Centros de notificaciones en el Explorador de servidores. Para obtener información, consulte:

-   [Inicio rápido: Agregar un servicio móvil][Inicio rápido: Agregar un servicio móvil]
-   [Cómo enviar notificaciones de inserción a una aplicación en ejecución con Visual Studio][Cómo enviar notificaciones de inserción a una aplicación en ejecución con Visual Studio]
-   [Cómo crear API personalizadas y trabajos programados en un servicio móvil (con back-end de .NET)][Cómo crear API personalizadas y trabajos programados en un servicio móvil (con back-end de .NET)]

## <span id="storage"></span></a>Biblioteca del cliente de almacenamiento 3.0

Azure SDK 2.3 incluye un emulador de almacenamiento y la blbioteca del cliente de almacenamiento 3.0 está integrada en las plantillas de proyecto que se incluyen en el SDK.

Para obtener información, consulte:

-   [Biblioteca del cliente de almacenamiento 3.0 de Azure][Biblioteca del cliente de almacenamiento 3.0 de Azure]
-   [Introducción a Almacenamiento de Azure][Introducción a Almacenamiento de Azure]
-   [Build 2014: Almacenamiento de Microsoft Azure: novedades, prácticas recomendadas y patrones (vídeo)][Build 2014: Almacenamiento de Microsoft Azure: novedades, prácticas recomendadas y patrones (vídeo)]
-   [Almacenamiento de Microsoft Azure @ BUILD 2014][Almacenamiento de Microsoft Azure @ BUILD 2014]

## <span id="arm"></span></a>Resource Manager

Resource Manager es un nuevo marco para la implementación y administración de aplicaciones entre recursos. Utilice Resource Manager con el nuevo editor JSON, los cmdlets de PowerShell y la compatibilidad con la CLI. Para obtener más información, consulte:

-   [Uso de Azure PowerShell con Resource Manager][Uso de Azure PowerShell con Resource Manager]
-   [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager][Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager]
-   [Build 2014: Modelo de grupo de recursos de Azure: administración moderna para la nube moderna (vídeo)][Build 2014: Modelo de grupo de recursos de Azure: administración moderna para la nube moderna (vídeo)]

  [Visual Studio 2013 Update 2 RC]: http://aka.ms/vs2013update2rc
  [Azure SDK 2.3]: http://www.windowsazure.com/es-es/downloads/
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175
  [Interfaz de línea de comandos entre plataformas de Azure]: http://go.microsoft.com/?linkid=9828653
  [notas de la versión de Azure SDK para .NET 2.3]: http://go.microsoft.com/fwlink/p/?LinkId=393548
  [página de actualización de productos de Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=272487
  [Vea los vídeos de Build]: http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409
  [Publicación y desarrollo web]: #webdeploy
  [Diagnóstico y depuración]: #diagnostics
  [Administración de servicios de Azure en Visual Studio]: #service-management
  [Automatización con PowerShell]: #automation
  [Desarrollo móvil con .NET]: #mobile
  [La biblioteca del cliente de almacenamiento 3.0 y el nuevo emulador de almacenamiento]: #storage
  [Resource Manager]: #arm
  [Introducción a Azure y ASP.NET]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-dotnet-get-started/
  [Introducción a Azure Tools para Visual Studio]: http://msdn.microsoft.com/es-es/library/azure/ff687127.aspx
  [Creación de proyectos Web ASP.NET en Visual Studio 2013]: http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio
  [Depurar un servicio en la nube o una máquina virtual en Visual Studio]: http://msdn.microsoft.com/es-es/library/azure/ff683670.aspx
  [Utilizar Emulator Express para ejecutar y depurar un servicio en la nube localmente]: http://msdn.microsoft.com/es-es/library/windowsazure/dn339018.aspx
  [Solución de problemas de Sitios web Azure en Visual Studio]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio
  [Crear máquinas virtuales de Azure en Visual Studio]: http://msdn.microsoft.com/es-es/library/windowsazure/dn569263.aspx
  [Tener acceso a Máquinas virtuales de Azure desde el Explorador de servidores]: http://msdn.microsoft.com/es-es/library/windowsazure/jj131259.aspx
  [Exploración de los recursos del Service Bus con el Explorador de servidores de Visual Studio]: http://msdn.microsoft.com/es-es/library/windowsazure/jj149828.aspx
  [Explorar recursos de almacenamiento con el Explorador de servidores]: http://msdn.microsoft.com/es-es/library/windowsazure/ff683677.aspx
  [Instalación y configuración de Azure PowerShell]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/
  [1]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156055.aspx
  [Utilizar scripts de Windows PowerShell para la publicación en entornos de desarrollo y pruebas]: http://msdn.microsoft.com/es-es/library/windowsazure/dn642480.aspx
  [Centro de desarrollo móvil de Azure]: /es-es/develop/mobile/
  [Inicio rápido: Agregar un servicio móvil]: http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn629482.aspx
  [Cómo enviar notificaciones de inserción a una aplicación en ejecución con Visual Studio]: http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn614131.aspx
  [Biblioteca del cliente de almacenamiento 3.0 de Azure]: http://go.microsoft.com/fwlink/?LinkId=394927
  [Introducción a Almacenamiento de Azure]: /es-es/documentation/articles/storage-introduction/
  [Almacenamiento de Microsoft Azure @ BUILD 2014]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/08/microsoft-azure-storage-build-2014.aspx
  [Uso de Azure PowerShell con Resource Manager]: http://go.microsoft.com/fwlink/?LinkID=394767
  [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager]: /es-es/documentation/articles/xplat-cli-azure-resource-manager/
