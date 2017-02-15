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
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Solución de problemas de desarrollo de Docker en Visual Studio

Cuando se trabaja con la versión preliminar de Visual Studio Tools para Docker, pueden surgir ciertos problemas debido a la propia naturaleza de la versión preliminar.
Éstos son algunos problemas frecuentes y sus soluciones.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Contenedores de Linux**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Se producen errores de compilación cuando se depura una aplicación de consola o web de .NET Core.  

Esto puede deberse a que no se comparte la unidad donde reside el proyecto con Docker para Windows.  Puede recibir un error como el siguiente:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Para resolverlo, haga doble clic con el botón derecho en el icono de la bandeja del sistema de Docker para Windows y seleccione "Configuración…".  Haga clic en la pestaña "Unidades compartidas" y comparta la letra de unidad donde se encuentra el proyecto.

### <a name="windows-containers"></a>**Contenedores de Windows**

La información siguiente es específica de los problemas de solución de problemas al depurar aplicaciones de consola y web de .NET Framework en los contenedores de Windows

### <a name="pre-requisites"></a>Requisitos previos

1. Visual Studio 2017 RC (o posterior) con la carga de trabajo de .NET Core y Docker (versión preliminar) instalada.
2. Windows 10 Anniversary Update con los parches de actualización de Windows más recientes instalados.
3. Docker para Windows (Beta) - https://docs.docker.com/docker-for-windows/ (versión 1.12.2-beta28 7813 o posterior).
4. En el icono de bandeja del sistema de Docker para Windows, seleccione "Cambiar a contenedores de Windows".  Una vez que se reinicie la máquina, asegúrese de que se conserva esta configuración.

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>Si hace clic en **Docker: Depurar proyecto**, se produce un error, "versión del cliente 1.22 es demasiado antigua.  La versión de la API mínima compatible es la 1.24, actualice el cliente a una versión más reciente".

La versión 1.13-RC2-beta31 (9123) o posterior de Docker para Windows requiere que se use "2.1" de Docker Compose.   Para solucionar este problema, cambie todas las apariciones de la versión "2" en los archivos docker-compose*.yml dentro del proyecto. Las plantillas predeterminadas que Visual Studio agregan al proyecto se actualizarán en futuras versiones de las herramientas. 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>La salida de la consola no aparece en la ventana de salida de Visual Studio cuando se depura una aplicación de consola.

Se trata de un problema conocido con el depurador de Visual Studio (msvsmon.exe), que actualmente no está diseñado para este escenario.  Estamos estudiando proporcionar soporte técnico para esto en una versión futura.  Para ver el resultado de la aplicación de consola en Visual Studio, utilice "Docker: Iniciar proyecto", que es equivalente a "Iniciar sin depurar".

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Se produce un error en la depuración aplicaciones web con la configuración de versión con el error&403; Prohibido.

Para solucionar el problema, abra el archivo web.release.config en la solución y comente o elimine las líneas siguientes:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>Al cambiar a contenedores de Windows, posiblemente vea un cuadro de diálogo de error que indica "Respuesta de error de demonio: tiempo de espera de e/s".

Puede realizar un seguimiento de este problema en Docker para Windows en https://github.com/docker/for-win/issues/178.


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contenedores de Linux**

### <a name="unable-to-validate-volume-mapping"></a>No se puede validar la asignación de volumen
Se necesita la asignación de volumen para compartir el código fuente y los archivos binarios de la aplicación con la carpeta de la aplicación en el contenedor.  Las asignaciones de volumen específicas se encuentran dentro de los archivos docker-compose.dev.debug.yml y docker-compose.dev.release.yml. A medida que se cambian los archivos en el equipo host, los contenedores reflejan estos cambios en una estructura de carpetas similar.

Para habilitar la asignación de volumen, abra **Settings...** (Configuración) en el icono de bandeja "moby" de Docker for Windows y seleccione la pestaña **Shared Drives** (Unidades compartidas).  Para asegurarse de que la letra de la unidad que hospeda el proyecto y la letra de la unidad donde reside % USERPROFILE % se compartan, actívelas y haga clic en **Apply**(Aplicar).

Para probar si funciona la asignación de volumen, una vez que las unidades se hayan compartido, recompile y presione F5 desde Visual Studio o pruebe lo siguiente desde un símbolo del sistema:

*En un símbolo del sistema de Windows*

> [!Note]
> Este ejemplo da por supuesto que la carpeta Usuarios se encuentra en la unidad "C" y se ha compartido.
> Se debe actualizar si ha compartido una unidad diferente.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*En el contenedor de Linux*

```
/ # ls
```

Debería ver una lista de directorios de la carpeta Usuarios/Público.
Si no se muestran archivos y la carpeta /c/Usuarios/Público no está vacía, significa que la asignación de volúmenes no está configurada correctamente.

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

> [!Note]
> Al trabajar con máquinas virtuales Linux, el sistema de archivos del contenedor distingue entre mayúsculas y minúsculas.

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Compilación: Error inesperado en la tarea "PrepareForBuild".

Microsoft.DotNet.Docker.CommandLine.ClientException: An error occurred trying to connect: (Microsoft.DotNet.Docker.CommandLine.ClientException: Error al intentar conectarse:)

Compruebe que se ejecuta el host de Docker predeterminado. Abra un símbolo del sistema y ejecute:

```
docker info
```

Si esto devuelve un error, intente iniciar la aplicación de escritorio **Docker for Windows** .  Si la aplicación de escritorio se está ejecutando, el icono **moby** de la bandeja debería estar visible. Haga clic con el botón derecho en el icono de bandeja y abra **Settings**(Configuración).  Haga clic en la pestaña **Reset** (Restablecer) y en **Restart Docker** (Reiniciar Docker).

##<a name="manually-upgrading-from-version-031-to-040"></a>Actualización manual de la versión 0.31 a la 0.40


1. Haga una copia de seguridad del proyecto.
1. Elimine los siguientes archivos en el proyecto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Cierre la solución y quite las líneas siguientes del archivo .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Vuelva a abrir la solución.
1. Quite las líneas siguientes del archivo Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Elimine los siguientes archivos relacionados con Docker de project.json, bajo publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Desinstale la versión anterior e instale Docker Tools 0.40; Después, elija otra vez **Agregar->Compatibilidad con Docker** en el menú contextual de la aplicación de consola o web ASP.NET Core. Esto volverá a agregar los nuevos artefactos Docker necesarios a su proyecto.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Aparece un cuadro de diálogo de error al intentar seleccionar **Agregar->Compatibilidad con Docker** o depurar (F5) una aplicación ASP.NET Core en un contenedor

En algunas ocasiones, después de desinstalar e instalar extensiones, la memoria caché de MEF (Managed Extensibility Framework) de Visual Studio puede dañarse. Cuando esto ocurre, puede hacer que aparezcan diversos cuadros de diálogo de error cuando se agrega la compatibilidad con Docker o se intenta ejecutar o depurar (F5) la aplicación ASP.NET Core. Como solución temporal, ejecute los siguientes pasos para eliminar la caché de MEF y volver a generarla.

1. Cierre todas las instancias de Visual Studio.
1. Abra %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Elimine las siguientes carpetas.
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abra Visual Studio.
1. Vuelva a intentar el escenario.



<!--HONumber=Dec16_HO2-->


