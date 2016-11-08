---
title: Solución de problemas de errores del cliente Docker en Windows con Visual Studio | Microsoft Docs
description: Solucione los problemas que encuentre al usar Visual Studio para crear e implementar aplicaciones web en Docker en Windows mediante Visual Studio.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''

ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: allclark

---
# Solución de problemas de desarrollo de Docker en Visual Studio
Cuando se trabaja con la versión preliminar de Visual Studio Tools para Docker, pueden surgir ciertos problemas debido a la propia naturaleza de la versión preliminar. Éstos son algunos problemas frecuentes y sus soluciones.

## No se puede validar la asignación de volumen
Se necesita la asignación de volumen para compartir el código fuente y los archivos binarios de la aplicación con la carpeta de la aplicación en el contenedor. Las asignaciones de volumen específicas se encuentran dentro de los archivos docker-compose.dev.debug.yml y docker-compose.dev.release.yml. A medida que se cambian los archivos en el equipo host, los contenedores reflejan estos cambios en una estructura de carpetas similar.

Para habilitar la asignación de volumen, abra **Settings...** (Configuración) en el icono de bandeja "moby" de Docker for Windows y seleccione la pestaña **Shared Drives** (Unidades compartidas). Para asegurarse de que la letra de la unidad que hospeda el proyecto y la letra de la unidad donde reside % USERPROFILE % se compartan, actívelas y haga clic en **Apply** (Aplicar).

Para probar si funciona la asignación de volumen, una vez que las unidades se hayan compartido, recompile y presione F5 desde Visual Studio o pruebe lo siguiente desde un símbolo del sistema:

*En un símbolo del sistema de Windows*

*[Nota: Esto da por supuesto que la carpeta Usuarios se encuentra en la unidad "C" y se ha compartido. Se debe actualizar si ha compartido una unidad diferente]*

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*En el contenedor de Linux*

```
/ # ls
```

Debería ver una lista de directorios de la carpeta Usuarios/Público. Si no se muestran archivos y la carpeta /c/Usuarios/Público no está vacía, significa que la asignación de volúmenes no está configurada correctamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Cambie al directorio wormhole para ver el contenido del directorio `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Nota:** *Al trabajar con máquinas virtuales Linux, el sistema de archivos del contenedor distingue mayúsculas de minúsculas.*

## Compilación: Error inesperado en la tarea "PrepareForBuild".
Microsoft.DotNet.Docker.CommandLine.ClientException: An error occurred trying to connect: (Microsoft.DotNet.Docker.CommandLine.ClientException: Error al intentar conectarse:)

Compruebe que se ejecuta el host de Docker predeterminado. Abra un símbolo del sistema y ejecute:

```
docker info
```

Si esto devuelve un error, intente iniciar la aplicación de escritorio **Docker for Windows**. Si la aplicación de escritorio se está ejecutando, el icono **moby** de la bandeja debería estar visible. Haga clic con el botón derecho en el icono de bandeja y abra **Settings** (Configuración). Haga clic en la pestaña **Reset** (Restablecer) y en **Restart Docker** (Reiniciar Docker).

## Actualización manual de la versión 0.31 a la 0.40
1. Haga una copia de seguridad del proyecto.
2. Elimine los siguientes archivos en el proyecto:
   
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
3. Cierre la solución y quite las líneas siguientes del archivo .xproj:
   
    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```
4. Vuelva a abrir la solución.
5. Quite las líneas siguientes del archivo Properties\\launchSettings.json:
   
    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```
6. Elimine los siguientes archivos relacionados con Docker de project.json, bajo publishOptions:
   
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
7. Desinstale la versión anterior e instale Docker Tools 0.40; Después, elija otra vez **Agregar->Compatibilidad con Docker** en el menú contextual de la aplicación de consola o web ASP.NET Core. Esto volverá a agregar los nuevos artefactos Docker necesarios a su proyecto.

## Aparece un cuadro de diálogo de error al intentar seleccionar **Agregar->Compatibilidad con Docker** o depurar (F5) una aplicación ASP.NET Core en un contenedor
En algunas ocasiones, después de desinstalar e instalar extensiones, la memoria caché de MEF (Managed Extensibility Framework) de Visual Studio puede dañarse. Cuando esto ocurre, puede hacer que aparezcan diversos cuadros de diálogo de error cuando se agrega la compatibilidad con Docker o se intenta ejecutar o depurar (F5) la aplicación ASP.NET Core. Como solución temporal, ejecute los siguientes pasos para eliminar la caché de MEF y volver a generarla.

1. Cierre todas las instancias de Visual Studio.
2. Abra %USERPROFILE%\\AppData\\Local\\Microsoft\\VisualStudio\\14.0\\.
3. Elimine las siguientes carpetas.
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
4. Abra Visual Studio.
5. Vuelva a intentar el escenario.

<!---HONumber=AcomDC_0921_2016-->