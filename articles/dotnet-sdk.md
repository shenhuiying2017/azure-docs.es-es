<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# ¿Qué es el SDK de Azure para .NET?

El SDK de Azure para .NET es un conjunto de herramientas de Visual Studio, herramientas de línea de comandos, binarios en tiempo de ejecución y bibliotecas de cliente que ayudan a desarrollar, probar e implementar aplicaciones que se ejecutan en Azure. En este artículo se detalla lo que se obtiene al instalar el SDK. Puede descargar el SDK en la [Página de descargas de Azure][].

El SDK de Azure para .NET también consta de bibliotecas de cliente para el consumo de servicios de Azure. Estas bibliotecas se instalan por separado usando [NuGet][].

## Tabla de contenido

-   [Lo que se incluye al instalar el SDK de Azure para .NET][]
-   [Lo que no se incluye al instalar el SDK de Azure para .NET][]
-   [P+F][]
-   [Recursos][]

## <span id="included"></span></a>Lo que se incluye en el SDK de Azure para .NET

El SDK de Azure para .NET instala los productos siguientes:

-   [Visual Studio Express para Web][]
-   [Microsoft ASP.NET y herramientas web para Visual Studio][]
-   [Herramientas de Microsoft Azure para Microsoft Visual Studio][]
-   [Herramientas de creación de Microsoft Azure][]
-   [Emulador de Microsoft Azure][]
-   [Emulador de almacenamiento de Microsoft Azure][]
-   [Herramientas de almacenamiento de Microsoft Azure][]
-   [Bibliotecas para .NET de Microsoft Azure][]
-   [Complemento LightSwitch Azure Publishing para Visual Studio][]

### <span id="vwd"></span></a>Visual Studio Express para Web

En caso de no tener Visual Studio en su equipo, el SDK instalará [Visual Studio Express para Web][1].

### <span id="wte"></span></a>Microsoft ASP.NET y herramientas web para Visual Studio

Esto permite trabajar con Sitios web Azure:

-   [Publicar proyectos web en Sitios web Azure][].
-   [Publicar proyectos de aplicaciones de consola en WebJobs de Azure][].
-   [Crear recursos de una Base de datos SQL y de un sitio web de Azure durante la creación de un nuevo proyecto web o la publicación de un proyecto web][].
-   [Crear scripts de implementación de PowerShell durante la creación de nuevos sitios web][].
-   [Administrar y solucionar problemas de Sitios web Azure en el Explorador de servidores][].
-   [Ejecutar en modo de depuración de forma remota para sitios web y WebJobs][].

> [WACOM.NOTE] No debe instalar el SDK de Azure para .NET para usar estas características; también se incluyen en las actualizaciones de Visual Studio.

### <span id="tools"></span></a>Herramientas de Microsoft Azure para Microsoft Visual Studio

Esto permite trabajar con Servicios en la nube y Máquinas virtuales de Azure:

-   [Crear, abrir y publicar proyectos de servicio en la nube][].
-   [Crear paquetes de implementación para proyectos de servicio en la nube][].
-   [Crear Máquinas virtuales de Azure durante la creación de nuevos proyectos web][].
-   [Crear scripts de PowerShell durante la creación de nuevas máquinas virtuales][Crear scripts de implementación de PowerShell durante la creación de nuevos sitios web].
-   [Ver y administrar la configuración del proyecto de servicio en la nube en las ventanas de propiedades del proyecto de Visual Studio][].
-   Ver y administrar [servicios en la nube][], [máquinas virtuales][] y [Bus de servicio][] en el Explorador de servidores.
-   [Ejecutar en modo de depuración de forma remota para servicios en la nube y máquinas virtuales][].

### <span id="auth"></span></a>Herramientas de creación de Microsoft Azure

Aquí se incluye lo siguiente:

-   La [herramienta de línea de comandos CSPack][] para crear paquetes de implementación.
-   La [herramienta de línea de comandos CSEncrypt][] para codificar contraseñas que se usan para obtener acceso a instancias de rol de servicio en la nube a través de una conexión de escritorio remoto.
-   Binarios en tiempo real requeridos por los proyectos de servicio en la nube para la comunicación con su entorno en tiempo real y diagnósticos. Estos binarios no están disponibles en los paquetes NuGet.

### <span id="emulator"></span></a>Emulador de Microsoft Azure

El [emulador de Azure][] simula el entorno de servicio en la nube de modo que pueda probar proyectos de servicio en la nube localmente en su equipo antes de implementarlos en Azure.

### <span id="stgemulator"></span></a>Emulador de almacenamiento de Microsoft Azure

El [emulador de almacenamiento de Azure][] usa una instancia de SQL Server y el sistema de archivos local para simular el almacenamiento de Azure (colas, tablas, blobs) de modo que pueda probarlo localmente.

### <span id="stgtools"></span></a>Herramientas de almacenamiento de Microsoft Azure

Esto instala [AzCopy][], una herramienta de línea de comandos que puede usar para transferir datos dentro y fuera de una cuenta de almacenamiento de Azure.

### <span id="libraries"></span></a>Bibliotecas para .NET de Microsoft Azure

Aquí se incluye lo siguiente:

-   Paquetes NuGet para Almacenamiento de Azure, Bus de servicio y Almacenamiento en caché que se almacenan en su equipo de modo que Visual Studio pueda crear nuevos proyectos de servicio en la nube sin conexión.
-   Un complemento de Visual Studio que permite la ejecución de proyectos de la [caché en rol][] en Visual Studio localmente.

### <span id="ls"></span></a>Complemento LightSwitch Azure Publishing para Visual Studio

Este permite [publicar proyectos de LightSwitch en Sitios web Azure][]. El complemento LightSwitch se incluye en las actualizaciones de Visual Studio y en el SDK de Azure para .NET. Con la instalación del SDK se garantiza la posesión de la versión más reciente del complemento.

## <span id="notincluded"></span></a>Lo que no se incluye al instalar el SDK de Azure para .NET

Hay algunas cosas que no se incluyen al instalar el SDK y que posiblemente desee para el desarrollo de Azure. Las más importantes son las siguientes:

-   [Bibliotecas de cliente][NuGet].

    El SDK de Azure incluye bibliotecas de cliente para consumir servicios de Azure, pero no todas se instalan al instalar el SDK. Si su aplicación necesita una biblioteca de cliente que el SDK no instala, puede obtenerla en [NuGet.org][NuGet]. Si su aplicación usa una biblioteca de cliente que el SDK no instala, es conveniente actualizarla con la versión actual en NuGet.org.

    **Copias locales de bibliotecas de cliente.** El SDK de Azure para .NET copia en su equipo los paquetes NuGet para algunas bibliotecas de cliente de Azure como Almacenamiento, Bus de servicio y Almacenamiento en caché. Estas bibliotecas de cliente se incluyen automáticamente en nuevos proyectos de servicio en la nube, de modo que los paquetes NuGet permiten que Visual Studio cree proyectos incluso sin conexión a Internet. Las bibliotecas de cliente suelen actualizarse con mayor frecuencia que las nuevas versiones del SDK lanzadas, de modo que las bibliotecas de cliente de NuGet.org suelen ser más actuales que las que se obtienen con el SDK.

    **Plantillas de proyecto que incluyen bibliotecas de cliente.** Solo las plantillas de proyecto [Servicio en la nube de Azure][Crear, abrir y publicar proyectos de servicio en la nube] y [Servicio móvil de Azure][] incluyen automáticamente algunas bibliotecas de cliente. Para otras bibliotecas o plantillas, instale los [paquetes NuGet de la biblioteca de cliente][NuGet] que necesite.

-   [Azure PowerShell][].

    Azure PowerShell permite [automatizar la creación e implementación del entorno de Azure][].

-   [Plantillas de proyecto Servicio móvil de Azure][Servicio móvil de Azure].

    Las plantillas Servicio móvil solo están disponibles en Visual Studio 2013 Update 2 y posterior. No están disponibles en Visual Studio 2012 o versiones anteriores, ni tampoco en Visual Studio 2013 o anterior, incluso si instala el SDK de Azure para .NET.

## <span id="faq"></span></a>Preguntas más frecuentes

-   [Muchas características de Azure ya están en Visual Studio. ¿Es necesario instalar el SDK de Azure para .NET?][]
-   [Deseo una biblioteca de cliente. ¿Es necesario instalar el SDK de Azure para .NET para obtenerla?][]
-   [¿Dónde se pueden encontrar versiones anteriores del SDK de Azure para .NET?][]
-   [¿Cuál es la directiva de ciclo de vida para las versiones del SDK de Azure para .NET?][]
-   [¿Con qué versiones del sistema operativo invitado es compatible el SDK de Azure para .NET?][]

### <span id="azinvs"></span></a>Muchas características de Azure ya están en Visual Studio. ¿Es necesario instalar el SDK de Azure para .NET?

Es conveniente instalar el SDK si desea desarrollar Azure usando las herramientas más recientes. Si prefiere no instalar el SDK, puede hacerlo si se cumplen las condiciones siguientes:

-   Ha instalado la [actualización de Visual Studio][] más reciente.
-   Va a desarrollar solo Sitios web Azure o Servicios móviles, no Servicios en la nube ni Máquinas virtuales.
-   Su aplicación no usa Almacenamiento o lo usa pero no necesita el emulador de almacenamiento ni la herramienta AzCopy.

### <span id="clientlib"></span></a>Deseo una biblioteca de cliente. ¿Es necesario instalar el SDK de Azure para .NET para obtenerla?

El SDK solo instala bibliotecas de cliente, de modo que puede crear proyectos de servicio en la nube incluso sin conexión a Internet. Las bibliotecas de cliente actuales están disponibles en los paquetes NuGet en [NuGet.org][NuGet]. Para obtener más información, consulte [Lo que no se incluye al instalar el SDK de Azure para .NET][] anteriormente en este documento.

### <span id="olderversions"></span></a>¿Dónde se pueden encontrar versiones anteriores del SDK de Azure para .NET?

Para obtener versiones anteriores, consulte la página de descargas del [SDK de Azure para .NET][].

### <span id="lifecycle"></span></a>¿Cuál es la directiva de ciclo de vida para las versiones del SDK de Azure para .NET?

Consulte la [directiva de ciclo de vida de soporte de Servicios en la nube de Microsoft Azure][].

### <span id="guestos"></span></a>¿Con qué versiones del sistema operativo invitado es compatible el SDK de Azure para .NET?

Consulte las [versiones del sistema operativo invitado de Azure y la matriz de compatibilidad del SDK][].

## <span id="resources"></span></a>Recursos

Para descargar el SDK de Azure para .NET o una biblioteca de cliente, consulte la [Página de descargas de Azure][].

Para obtener el código fuente del SDK de Azure para .NET, incluidas las bibliotecas de cliente, consulte [GitHub.com/Azure][].

Para obtener documentación de referencia a la biblioteca de cliente de Azure, consulte [Referencia de .NET de Azure][].

  [Página de descargas de Azure]: /en-us/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [Lo que se incluye al instalar el SDK de Azure para .NET]: #included
  [Lo que no se incluye al instalar el SDK de Azure para .NET]: #notincluded
  [P+F]: #faq
  [Recursos]: #resources
  [Visual Studio Express para Web]: #vwd
  [Microsoft ASP.NET y herramientas web para Visual Studio]: #wte
  [Herramientas de Microsoft Azure para Microsoft Visual Studio]: #tools
  [Herramientas de creación de Microsoft Azure]: #auth
  [Emulador de Microsoft Azure]: #emulator
  [Emulador de almacenamiento de Microsoft Azure]: #stgemulator
  [Herramientas de almacenamiento de Microsoft Azure]: #stgtools
  [Bibliotecas para .NET de Microsoft Azure]: #libraries
  [Complemento LightSwitch Azure Publishing para Visual Studio]: #ls
  [1]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [Publicar proyectos web en Sitios web Azure]: ../web-sites-dotnet-get-started/
  [Publicar proyectos de aplicaciones de consola en WebJobs de Azure]: ../websites-dotnet-deploy-webjobs/
  [Crear recursos de una Base de datos SQL y de un sitio web de Azure durante la creación de un nuevo proyecto web o la publicación de un proyecto web]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Crear scripts de implementación de PowerShell durante la creación de nuevos sitios web]: http://msdn.microsoft.com/en-us/library/dn642480.aspx
  [Administrar y solucionar problemas de Sitios web Azure en el Explorador de servidores]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [Ejecutar en modo de depuración de forma remota para sitios web y WebJobs]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Crear, abrir y publicar proyectos de servicio en la nube]: ../cloud-services-dotnet-get-started/
  [Crear paquetes de implementación para proyectos de servicio en la nube]: http://msdn.microsoft.com/en-us/library/ff683672.aspx
  [Crear Máquinas virtuales de Azure durante la creación de nuevos proyectos web]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [Ver y administrar la configuración del proyecto de servicio en la nube en las ventanas de propiedades del proyecto de Visual Studio]: http://msdn.microsoft.com/en-us/library/ee405486.aspx
  [servicios en la nube]: http://msdn.microsoft.com/en-us/library/ff683675.aspx
  [máquinas virtuales]: http://msdn.microsoft.com/en-us/library/jj131259.aspx
  [Bus de servicio]: http://msdn.microsoft.com/en-us/library/jj149828.aspx
  [Ejecutar en modo de depuración de forma remota para servicios en la nube y máquinas virtuales]: http://msdn.microsoft.com/en-us/library/ff683670.aspx
  [herramienta de línea de comandos CSPack]: http://msdn.microsoft.com/en-us/library/gg432988.aspx
  [herramienta de línea de comandos CSEncrypt]: http://msdn.microsoft.com/en-us/library/hh404001.aspx
  [emulador de Azure]: http://msdn.microsoft.com/en-us/library/dn339018.aspx
  [emulador de almacenamiento de Azure]: http://msdn.microsoft.com/en-us/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [caché en rol]: http://msdn.microsoft.com/en-us/library/dn386103.aspx
  [publicar proyectos de LightSwitch en Sitios web Azure]: http://msdn.microsoft.com/en-us/library/jj131261.aspx
  [Servicio móvil de Azure]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [Azure PowerShell]: ../install-configure-powershell/
  [automatizar la creación e implementación del entorno de Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Muchas características de Azure ya están en Visual Studio. ¿Es necesario instalar el SDK de Azure para .NET?]: #azinvs
  [Deseo una biblioteca de cliente. ¿Es necesario instalar el SDK de Azure para .NET para obtenerla?]: #clientlib
  [¿Dónde se pueden encontrar versiones anteriores del SDK de Azure para .NET?]: #olderversions
  [¿Cuál es la directiva de ciclo de vida para las versiones del SDK de Azure para .NET?]: #lifecycle
  [¿Con qué versiones del sistema operativo invitado es compatible el SDK de Azure para .NET?]: #guestos
  [actualización de Visual Studio]: http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs#DownloadFamilies_5
  [SDK de Azure para .NET]: /en-us/downloads/archive-net-downloads/
  [directiva de ciclo de vida de soporte de Servicios en la nube de Microsoft Azure]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [versiones del sistema operativo invitado de Azure y la matriz de compatibilidad del SDK]: http://msdn.microsoft.com/en-us/library/ee924680.aspx
  [GitHub.com/Azure]: https://github.com/azure/
  [Referencia de .NET de Azure]: /en-us/develop/net/reference/
