---
title: "Solución de problemas de errores del cliente Docker en Windows con Visual Studio | Microsoft Docs"
description: Solucione los problemas que encuentre al usar Visual Studio para crear e implementar aplicaciones web en Docker en Windows mediante Visual Studio.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: 649cc1a78f3a9f343533cb18fb7d763e4f9ea196
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.contentlocale: es-es
ms.lasthandoff: 02/04/2017

---

# <a name="troubleshoot-visual-studio-docker-development"></a>Solución de problemas de desarrollo de Docker en Visual Studio

Cuando se trabaja con la versión preliminar de Visual Studio Tools para Docker, pueden surgir ciertos problemas debido a la propia naturaleza de la versión preliminar.
Estos son algunos problemas frecuentes y sus soluciones.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Contenedores de Linux**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Se producen errores de compilación cuando se depura una aplicación de consola o web de .NET Core.  

Esto puede deberse a que no se comparte la unidad donde reside el proyecto con Docker para Windows.  Puede recibir un error como el siguiente:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Para resolver este problema:

1. Haga clic derecho en **Docker para Windows** en el área de notificación y, a continuación, seleccione **Configuración**.  
2. Seleccione **Shared Drives** (Unidades compartidas) y comparta la unidad donde reside el proyecto.

### <a name="windows-containers"></a>**Contenedores de Windows**

Los problemas siguientes son específicos de la depuración de aplicaciones de consola y web de .NET Framework en contenedores de Windows.

#### <a name="prerequisites"></a>Requisitos previos

1. Debe estar instalado Visual Studio 2017 RC (o posterior) con la carga de trabajo de .NET Core y Docker (versión preliminar).
2. Actualización de aniversario de Windows 10 con las revisiones de Windows Update más recientes. En concreto, [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) debe estar instalado. 
3. [Docker para Windows](https://docs.docker.com/docker-for-windows/) (compilación 1.13.0 o posterior) debe estar instalado.
4. Debe seleccionarse **Switch to Windows containers** (Cambiar a contenedores de Windows). En el área de notificación, haga clic en **Docker para Windows** y, a continuación, seleccione **Switch to Windows containers** (Cambiar a contenedores de Windows). Una vez que se reinicie la máquina, asegúrese de que se conserva esta configuración.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>La salida de la consola no aparece en la ventana de salida de Visual Studio cuando se depura una aplicación de consola.

Se trata de un problema conocido con el depurador de Visual Studio (msvsmon.exe), que actualmente no está diseñado para este escenario. En un futuro podría incluirse compatibilidad para este escenario. Para ver el resultado de la aplicación de consola en Visual Studio, utilice **Docker: Iniciar proyecto**, que es equivalente a **Iniciar sin depurar**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Se produce un error en la depuración de aplicaciones web con la configuración de versión con el error 403 Prohibido.

Para solucionar el problema, abra el archivo web.release.config en la solución y comente o elimine las líneas siguientes:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contenedores de Linux**

#### <a name="unable-to-validate-volume-mapping"></a>No se puede validar la asignación de volumen
Se necesita la asignación de volumen para compartir el código fuente y los archivos binarios de la aplicación con la carpeta de la aplicación en el contenedor.  Las asignaciones de volumen específicas se encuentran dentro de los archivos docker-compose.dev.debug.yml y docker-compose.dev.release.yml. A medida que se cambian los archivos en el equipo host, los contenedores reflejan estos cambios en una estructura de carpetas similar.

Para habilitar la asignación de volúmenes:

1. Haga clic en **Moby** en el área de notificación y seleccione **Settings** (Configuración).
2. Seleccione **Shared Drives** (Unidades compartidas).
3. Seleccione la unidad que hospeda el proyecto y la unidad donde reside %USERPROFILE%.
4. Haga clic en **Apply**.

Para probar si funciona la asignación de volumen, recompile y presione F5 desde Visual Studio una vez que se hayan compartido una o varias unidades, o ejecute el código siguiente desde un símbolo del sistema.

> [!NOTE]
> En este ejemplo da por supuesto que la carpeta Usuarios se encuentra en la unidad "C" y se ha compartido.
> Revise en caso de que haya compartido una unidad diferente.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Ejecute el código siguiente en el contenedor de Linux.

```
/ # ls
```

Debería ver una lista de directorios de la carpeta Usuarios/Público. Si no se muestran archivos y la carpeta /c/Usuarios/Público no está vacía, la asignación de volúmenes no está configurada correctamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Cambie al directorio wormhole para ver el contenido del directorio `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Al trabajar con máquinas virtuales Linux, el sistema de archivos del contenedor distingue entre mayúsculas y minúsculas.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Compilación: Error inesperado en la tarea "PrepareForBuild".

Microsoft.DotNet.Docker.CommandLine.ClientException: An error occurred trying to connect: (Microsoft.DotNet.Docker.CommandLine.ClientException: Error al intentar conectarse).

Compruebe que se ejecuta el host de Docker predeterminado. Abra un símbolo del sistema y ejecute:

```
docker info
```

Si esto devuelve un error, intente iniciar la aplicación de escritorio **Docker para Windows** . Si está ejecutando la aplicación de escritorio, **Moby** debería estar visible en el área de notificación. Haga clic en **Moby** y abra **Settings** (Configuración). Haga clic en **Reset** (Restablecer) y, a continuación, reinicie Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Aparece un cuadro de diálogo de error al intentar agregar la compatibilidad con Docker o depurar (F5) una aplicación ASP.NET Core en un contenedor.

Después de desinstalar e instalar extensiones, la memoria caché de Managed Extensibility Framework (MEF) de Visual Studio puede dañarse. Esta situación puede provocar que aparezcan diversos mensajes de error cuando se agrega la compatibilidad con Docker o se intenta ejecutar o depurar (F5) la aplicación ASP.NET Core. Como solución temporal, siga los siguientes pasos para eliminar la caché de MEF y volver a generarla.

1. Cierre todas las instancias de Visual Studio.
1. Abra %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Elimine las siguientes carpetas:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abra Visual Studio.
1. Vuelva a probar el escenario.

