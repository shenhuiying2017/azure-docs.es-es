---
title: "Instalación de .NET en un rol de servicio en la nube | Microsoft Docs"
description: "En este artículo se describe cómo instalar manualmente .NET Framework en el rol web y el rol de trabajo del servicio en la nube"
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e6154d990e10f67d4b30b889a62a99cedcbfccbe
ms.contentlocale: es-es
ms.lasthandoff: 07/25/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>Instalación de .NET en un rol de servicio en la nube
En este artículo se describe cómo instalar diferentes versiones de .NET Framework en roles web de servicio en la nube y de trabajo distintos a los que vienen con el sistema operativo invitado. Por ejemplo, puede utilizar estos pasos para instalar .NET 4.6.1 en la familia de SO invitado de Azure 4, que no se incluye con ninguna versión de .NET 4.6. Para obtener la información más reciente sobre las versiones de sistema operativo invitado, consulte las [novedades de la versión del SO invitado de Azure](cloud-services-guestos-update-matrix.md).

>[!NOTE]
>SO invitado 5 incluye .NET 4.6

>[!IMPORTANT]
>Azure SDK 2.9 contiene una restricción sobre la implementación de .NET 4.6 en SO invitados de versión 4 o inferior. Hay disponible una corrección [aquí](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

El proceso de instalación de .NET en el rol web y el rol de trabajo implica incluir el paquete del instalador de .NET como parte del proyecto en la nube e iniciar el instalador como parte de las tareas de inicio del rol.  

## <a name="add-the-net-installer-to-your-project"></a>Agregar el instalador de .NET al proyecto
* Descargar el instalador web del .NET Framework que desea instalar
  * [Instalador web de .NET 4.7](http://go.microsoft.com/fwlink/?LinkId=825298)
  * [Instalador web de .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)

* Para un rol web
  1. En el **Explorador de soluciones**, en **Roles** del proyecto de servicio en la nube, haga clic con el botón derecho en su rol y seleccione **Agregar > Nueva carpeta**. Cree una carpeta llamada *bin*
  2. Haga clic con el botón derecho en la carpeta **bin** y seleccione **Agregar > Elemento existente**. Seleccione el instalador de .NET y agréguelo a la carpeta bin.
  
* Para un rol de trabajo
  1. Haga clic con el botón derecho en el rol y seleccione **Agregar > Elemento existente**. Seleccione el instalador de .NET y agréguelo al rol. 

Los archivos que se agregan de esta manera a la carpeta de contenido del rol se agregan automáticamente al paquete de servicios en la nube y se implementan en una ubicación coherente en la máquina virtual. Repita este proceso para todos los roles web y roles de trabajo en el servicio en la nube, de manera que todos los roles tengan una copia del instalador.

> [!NOTE]
> Debe instalar .NET 4.6.1 en su rol de servicio en la nube, incluso si la aplicación tiene como destino .NET 4.6. El SO invitado de Azure incluye las actualizaciones [3098779](https://support.microsoft.com/kb/3098779) y [3097997](https://support.microsoft.com/kb/3097997). La instalación de .NET 4.6 sobre estas actualizaciones puede producir problemas cuando se ejecutan las aplicaciones .NET, por lo que debe instalar directamente .NET 4.6.1 en lugar de .NET 4.6. Para obtener más información, consulte [KB 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Contenidos de rol con archivos de instalador][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir las tareas de inicio para los roles
Las tareas de inicio le permiten realizar operaciones antes de que se inicie un rol. Instalar .NET Framework como parte de la tarea de inicio garantiza que el marco se instala antes de que se ejecute cualquier código de la aplicación. Para más información sobre de las tareas de inicio, consulte [Configuración y ejecución de tareas de inicio para un servicio en la nube](cloud-services-startup-tasks.md). 

1. Agregue lo siguiente al archivo *ServiceDefinition.csdef* en el nodo **WebRole** o el nodo **WorkerRole** para todos los roles:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    La configuración anterior ejecuta el comando *install.cmd* de la consola con privilegios de administrador para instalar .NET Framework. La configuración también crea un *LocalStorage* con el nombre *NETFXInstall*. El script de inicio establece la carpeta temporal para utilizar este recurso de almacenamiento local. Es importante establecer el tamaño de este recurso en al menos 1024 MB para asegurarse de que el marco se instala correctamente. Para más información sobre las tareas de inicio, consulte [Tareas de inicio comunes para los servicios en la nube](cloud-services-startup-tasks-common.md). 

2. Cree un archivo **install.cmd** y agréguelo a todos los roles haciendo clic con el botón derecho en el rol y seleccionando **Agregar > Elemento existente...**. De este modo, ahora todos los roles deben tener el archivo de instalador de .NET, además del archivo install.cmd.
   
   ![Contenidos de rol con todos los archivos][2]
   
   > [!NOTE]
   > Para crear este archivo, utilice un editor de texto básico, como el Bloc de notas. Si usa Visual Studio para crear un archivo de texto y luego le cambia el nombre a ".cmd", es posible que el archivo todavía contenga una marca BOM UTF-8 y ejecutar la primera línea del script generará un error. Asegúrese de que la primera línea del archivo es un comando REM, esto puede omitir el procesamiento de la marca BOM UTF-8. 
   > 
   > 

3. Agregue el script siguiente al archivo **install.cmd** :
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    set netfx="NDP47"

    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."

    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit

    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%

    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp

    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp

    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp

    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"

    :NDP47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"

    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%

    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed

    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%

    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%

    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%

    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

    :exit
    EXIT /B 0
    ```
   
    El script de instalación consulta el registro para comprobar si la versión de .NET Framework especificada ya está instalada en la máquina. Si la versión de .NET no está instalada, se inicia el instalador web de .NET. El script registrará toda la actividad en un archivo llamado *startuptasklog-(currentdatetime).txt* almacenado en el almacenamiento local *InstallLogs* para ayudar a solucionar cualquier problema que pueda surgir.
   
   > [!NOTE]
   > El script muestra cómo instalar .NET 4.5.2 o .NET 4.6 de cara a conseguir una continuidad. No es necesario instalar .NET 4.5.2 manualmente, dado que ya está disponible en el SO invitado de Azure. En lugar de instalar .NET 4.6, debe instalar directamente .NET 4.6.1 debido a [KB 3118750](https://support.microsoft.com/kb/3118750).
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurar los diagnósticos para transferir los registros de las tareas de inicio al almacenamiento de blobs
Para simplificar la resolución de los posibles problemas de instalación, puede configurar Diagnósticos de Azure para transferir todos los archivos de registro generados por el script de inicio o el instalador de .NET al almacenamiento de blobs. Con este enfoque, es posible ver los registros simplemente al descargar los archivos de registro desde el almacenamiento de blobs, en lugar de tener que realizar una conexión de Escritorio remoto con el rol.

Para configurar los diagnósticos, abra *diagnostics.wadcfgx* y agregue lo siguiente en el nodo **Directorios**: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Este XML configura los diagnósticos de Azure para transferir todos los archivos del directorio *log* en el recurso *NETFXInstall* a la cuenta de almacenamiento de diagnóstico en el contenedor de blobs *netfx-install*.

## <a name="deploying-your-service"></a>Implementación del servicio
Cuando implemente el servicio, las tareas de inicio instalan .NET Framework si todavía no está instalado. Los roles tienen un estado de *ocupado* mientras se esté instalando el marco e, incluso, podrían reiniciarse si la instalación del marco así lo requiere. 

## <a name="additional-resources"></a>Recursos adicionales
* [Instalar .NET Framework][Installing the .NET Framework]
* [Determinación de las versiones instaladas de .NET Framework][How to: Determine Which .NET Framework Versions Are Installed]
* [Solución de problemas en instalaciones de .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



