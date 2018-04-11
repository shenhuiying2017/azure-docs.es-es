---
title: Consola serie de máquina virtual de Azure | Microsoft Docs
description: Consola serie bidireccional para máquinas virtuales de Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 2ff0dcba0912461d8528fc76c7c67d90febc0324
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Consola serie de máquina virtual (versión preliminar) 


La consola serie de máquina virtual en Azure ofrece acceso a una consola basada en texto para máquinas virtuales Linux y Windows. Esta conexión de serie es al puerto de serie COM1 de la máquina virtual y ofrece acceso a la misma, sin estar relacionada con el estado del sistema operativo o la red de la máquina virtual. En este momento, el acceso a la consola serie para una máquina virtual solo es posible mediante Azure Portal, y se permite únicamente a los usuarios que tengan acceso de colaborador o superior a la máquina virtual. 

> [!Note] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Actualmente este servicio está en **versión preliminar pública** y el acceso a la consola serie para las máquinas virtuales está disponible para las regiones globales de Azure. En este momento, la consola serie no está disponible en las nubes Azure Government, Azure Alemania y Azure China.

 

## <a name="prerequisites"></a>requisitos previos 

* La máquina virtual TIENE QUE tener los [diagnósticos de arranque](boot-diagnostics.md) habilitados 
* La cuenta que utilice la consola serie tiene que tener el [rol Colaborador](../../active-directory/role-based-access-built-in-roles.md) para la máquina virtual y la cuenta de almacenamiento de [diagnósticos de arranque](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Apertura de la consola serie
La consola serie para las máquinas virtuales solo es accesible mediante [Azure Portal](https://portal.azure.com). A continuación se muestran los pasos requeridos para acceder a la consola serie para las máquinas virtuales en el portal 

  1. Abra Azure Portal.
  2. En el menú de la izquierda, seleccione Máquinas virtuales.
  3. Haga clic en la máquina virtual en la lista. Se abrirá la página de información general de la máquina virtual.
  4. Desplácese hacia abajo hasta la sección de Soporte técnico y solución de problemas y haga clic en la opción de la consola serie (versión preliminar). Se abrirá un panel nuevo con la consola serie y se iniciará la conexión.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Deshabilitación de la característica
La funcionalidad de la consola serie puede desactivarse para determinadas máquinas virtuales deshabilitando el valor de ese parámetro de diagnósticos de arranque de la máquina virtual específica.

## <a name="serial-console-security"></a>Seguridad de la consola serie 

### <a name="access-security"></a>Seguridad de acceso 
El acceso a la consola serie está limitado a los usuarios que tienen un acceso de [colaborador de la máquina virtual](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) o superior en relación con la máquina virtual. Si su inquilino de AAD requiere Multi-Factor Authentication, el acceso a la consola serie también necesitará MFA para acceder mediante [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Seguridad del canal
Todos los datos enviados y recibidos se cifran en la conexión.

### <a name="audit-logs"></a>Registros de auditoría
Todo el acceso a la consola serie queda registrado en los registros de los [diagnósticos de arranque](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la máquina virtual. El administrador de la máquina virtual de Azure es el propietario y el que controla el acceso a estos registros.  

>[!CAUTION] 
Si bien no se registra ninguna contraseña de acceso para la consola, si los comandos ejecutados en la consola contienen o producen contraseñas, secretos, nombres de usuario o cualquier otra forma de información de identificación personal (PII), estos se escribirán en los registros de los diagnósticos de arranque de la máquina virtual, junto con todo el demás texto visible, como parte de la implementación de la funcionalidad de desplazamiento de la consola serie. Estos registros son circulares y solo aquellas personas con permisos de lectura para la cuenta de almacenamiento de diagnósticos tienen acceso a ellos; de todas formas, se recomienda seguir la práctica recomendada de usar el Escritorio remoto para cualquier elemento que pueda implicar secretos o información de identificación personal. 

### <a name="concurrent-usage"></a>Uso simultáneo
Si un usuario se conecta a la consola serie y otro usuario solicita correctamente el acceso a esa misma máquina virtual, se desconectará al primer usuario y se conectará al segundo de una forma similar a la acción del primer usuario levantándose y abandonando la consola física y el nuevo usuario sentándose a ocupar su sitio.

>[!CAUTION] 
Esto quiere decir que no se cerrará la sesión del usuario que se desconecta. La posibilidad de forzar el cierre de sesión con la desconexión (mediante SIGHUP u otro mecanismo similar) sigue en proceso de valoración. En Windows hay un tiempo de espera automático habilitado en SAC; sin embargo, en Linux puede configurar el valor de tiempo de espera del terminal. 


## <a name="accessing-serial-console-for-windows"></a>Acceso a la consola serie para Windows 
Las imágenes más nuevas de Windows Server en Azure tendrán la [consola de administración especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitada de forma predeterminada. SAC se admite en versiones de servidor de Windows, pero no está disponible en versiones de cliente (por ejemplo, Windows 10, Windows 8 o Windows 7). Para habilitar la consola serie en máquinas virtuales Windows creadas usando imágenes de Feb2018 o inferiores, siga estos pasos: 

1. Conéctese a la máquina virtual Windows mediante Escritorio remoto
2. Ejecute los siguientes comandos como administrador en un símbolo del sistema 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Reinicie el sistema para que la consola de SAC se habilite

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si es necesario SAC también puede habilitarse sin conexión, 

1. Conecte el disco de Windows para el que desee Configurar SAC como disco de datos a la máquina virtual existente. 
2. Ejecute los siguientes comandos como administrador en un símbolo del sistema 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>¿Cómo se puede saber si SAC está habilitada o no? 

Si [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) no está habilitada la consola serie no mostrará el símbolo del sistema de SAC. Puede mostrar una información de mantenimiento de la máquina virtual en algunos casos, o puede estar en blanco.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Habilitación del menú de arranque para mostrar la consola serie 

Si tiene que habilitar los mensajes del cargador de arranque de Windows para mostrar la consola serie, puede agregar las siguientes opciones adicionales al cargador de arranque de Windows.

1. Conéctese a la máquina virtual Windows mediante Escritorio remoto
2. Ejecute los siguientes comandos como administrador en un símbolo del sistema 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Reinicie el sistema para que el menú de arranque se habilite

> [!NOTE] 
> En este momento la compatibilidad para las teclas de función no está habilitada, si necesita opciones de arranque avanzadas use bcdedit /set {current} onetimeadvancedoptions on, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) para más información

## <a name="windows-commands---cmd"></a>Comandos de Windows: CMD 

En esta sección se incluyen ejemplos de comandos para realizar tareas comunes en escenarios donde puede ser necesario usar SAC para acceder a la máquina virtual, como cuando necesita solucionar errores de conexión de RDP.

SAC se ha incluido en todas las versiones de Windows desde Windows Server 2003, pero está deshabilitado de forma predeterminada. SAC se basa en el controlador de kernel `sacdrv.sys`, el servicio `Special Administration Console Helper` (`sacsvr`) y el proceso `sacsess.exe`. Para más información, consulte [Emergency Management Services Tools and Settings](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)) (Herramientas y configuraciones de Servicios de administración de emergencias).

SAC le permite conectarse a su sistema operativo a través del puerto serie. Cuando se inicia CMD desde SAC, `sacsess.exe` inicia `cmd.exe` dentro del sistema operativo en ejecución. Esto se puede ver en Task Manager si se conecta mediante RDP a su máquina virtual al mismo tiempo que está conectado a SAC mediante la característica de la consola serie. El CMD al que se accede a través de SAC es el mismo `cmd.exe` que usa cuando se conecta mediante RDP. Están disponibles las mismas herramientas y comandos, incluida la posibilidad de iniciar PowerShell desde esa instancia de CMD. Hay una diferencia importante entre SAC y el Entorno de recuperación de Windows (WinRE) y es que SAC le permite administrar su sistema operativo en ejecución, mientras que WinRE se inicia en un sistema operativo mínimo diferente. Aunque las máquinas virtuales no admiten la posibilidad de acceder a WinRE, con la característica de la consola serie las máquinas virtuales de Azure se pueden administrar mediante SAC.

Dado que SAC está limitado a un búfer de pantalla de 80 x 24 sin desplazamiento, agregue `| more` a los comandos para mostrar la salida en una página cada vez. Use `<spacebar>` para ver la página siguiente o `<enter>` para ver la línea siguiente.  

`SHIFT+INSERT` es el método abreviado de pegar de la ventana de la consola serie.

Como el búfer de pantalla de SAC está limitado, puede que sea más fácil escribir los comandos más largos en un editor de texto local y luego pegarlos en SAC.

### <a name="view-and-edit-windows-registry-settings"></a>Visualización y edición de la configuración del Registro de Windows
#### <a name="verify-rdp-is-enabled"></a>Comprobar que RDP está habilitado
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

La segunda clave (en \Policies) solo existe si está configurada la opción de directiva de grupo pertinente.

#### <a name="enable-rdp"></a>Habilitar RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

La segunda clave (en \Policies) solo se necesitaría si se ha configurado la opción de directiva de grupo pertinente. El valor se volverá a escribir en la siguiente actualización de directiva de grupo si está configurado en la directiva de grupo.

### <a name="manage-windows-services"></a>Administración de los servicios de Windows

#### <a name="view-service-state"></a>Ver el estado del servicio
`sc query termservice`
####  <a name="view-service-logon-account"></a>Ver la cuenta de inicio de sesión del servicio
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Establecer la cuenta de inicio de sesión del servicio 
`sc config termservice obj= "NT Authority\NetworkService"`

Se requiere un espacio después del signo igual.
#### <a name="set-service-start-type"></a>Establecer el tipo de inicio del servicio
`sc config termservice start= demand` 

Se requiere un espacio después del signo igual. Los valores de inicio posibles son `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Establecer las dependencias del servicio
`sc config termservice depend= RPCSS`

Se requiere un espacio después del signo igual.
#### <a name="start-service"></a>Iniciar el servicio
`net start termservice`

o

`sc start termservice`
#### <a name="stop-service"></a>Detener el servicio
`net stop termservice`

o

`sc stop termservice`
### <a name="manage-networking-features"></a>Administración de las características de red
#### <a name="show-nic-properties"></a>Mostrar las propiedades de NIC
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Mostrar las propiedades de IP
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Mostrar la configuración de IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Habilitar NIC
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Establecer NIC para usar DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Las máquinas virtuales de Azure se deben configurar siempre en el sistema operativo invitado para usar DHCP para obtener una dirección IP. La configuración de IP estática de Azure sigue usando DHCP para proporcionar la dirección IP estática a la máquina virtual.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Ping de puerto  
Instalar al cliente de Telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Comprobación de la conectividad

`telnet bing.com 80`

Quitar al cliente de Telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Cuando se limita a los métodos disponibles en Windows de forma predeterminada, PowerShell puede ser un enfoque mejor para probar la conectividad del puerto. Consulte la sección de PowerShell a continuación para ver ejemplos.
#### <a name="test-dns-name-resolution"></a>Probar la resolución de nombres DNS
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Mostrar la regla de Firewall de Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Deshabilitar el Firewall de Windows
`netsh advfirewall set allprofiles state off`

Puede usar este comando al solucionar problemas para excluir temporalmente el Firewall de Windows. Se habilitará en el siguiente reinicio o cuando lo habilite mediante el siguiente comando. No detenga el servicio Firewall de Windows (MPSSVC) o el servicio Motor de filtrado de base (BFE) como forma de excluir el Firewall de Windows. Si lo hace, se bloqueará toda la conectividad.
#### <a name="enable-windows-firewall"></a>Habilitar el Firewall de Windows
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Administración de usuarios y grupos
#### <a name="create-local-user-account"></a>Crear una cuenta de usuario local
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Agregar un usuario local a un grupo local
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Comprobar que la cuenta de usuario está habilitada
`net user <username> | find /i "active"`

El nombre de la cuenta de administrador local de las máquinas virtuales de Azure creadas a partir de la imagen generalizada tomará el especificado durante el aprovisionamiento de la máquina virtual. Así que normalmente no será `Administrator`.
#### <a name="enable-user-account"></a>Habilitar la cuenta de usuario
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Ver las propiedades de la cuenta de usuario
`net user <username>`

Líneas de ejemplo de interés de una cuenta de administrador local:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Ver los grupos locales
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Administración del registro de eventos de Windows
#### <a name="query-event-log-errors"></a>Consultar los errores del registro de eventos
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Cambie `/c:10` por el número deseado de eventos que se devolverán o muévalo para que se devuelvan todos los eventos que coincidan con el filtro.
#### <a name="query-event-log-by-event-id"></a>Consultar el registro de eventos por identificador de evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Consultar el registro de eventos por identificador de evento y proveedor
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar el registro de eventos por identificador de evento y proveedor durante las últimas 24 horas
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para recuperar 7 días en lugar de 24 horas.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar el registro de eventos por identificador de evento, proveedor y datos de evento en los últimos 7 días
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Visualización o eliminación de las aplicaciones instaladas
#### <a name="list-installed-applications"></a>Mostrar las aplicaciones instaladas
`wmic product get Name,InstallDate | sort /r | more`

El comando `sort /r` ordena de forma descendente por fecha de instalación para que sea fácil ver lo que se ha instalado recientemente. Use `<spacebar>` para avanzar a la página siguiente de salida o `<enter>` para avanzar una línea.
#### <a name="uninstall-an-application"></a>Desinstalar una aplicación
`wmic path win32_product where name="<name>" call uninstall`

Reemplace `<name>` por el nombre devuelto en el comando anterior en la aplicación que quiere quitar.

### <a name="file-system-management"></a>Administración del sistema de archivos
#### <a name="get-file-version"></a>Obtener la versión del archivo
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

En este ejemplo se devuelve la versión del archivo del controlador NIC virtual, que es netvsc.sys, netvsc63.sys o netvsc60.sys, según la versión de Windows.
#### <a name="scan-for-system-file-corruption"></a>Examinar los archivos del sistema en busca de daños
`sfc /scannow`

Consulte también [Repair a Windows Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image) (Reparación de una imagen de Windows).
#### <a name="scan-for-system-file-corruption"></a>Examinar los archivos del sistema en busca de daños
`dism /online /cleanup-image /scanhealth`

Consulte también [Repair a Windows Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image) (Reparación de una imagen de Windows).
#### <a name="export-file-permissions-to-text-file"></a>Exportar los permisos de archivo a un archivo de texto
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Guardar los permisos de archivo en el archivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Restaurar los permisos de archivo del archivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

La ruta de acceso al usar `/restore` debe ser la carpeta primaria de la carpeta que especificó al utilizar `/save`. En este ejemplo, `\RSA` es la carpeta primaria de la carpeta `\MachineKeys` especificada en el ejemplo anterior con `/save`.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Tomar la propiedad de NTFS de una carpeta
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Conceder permisos NTFS a una carpeta de forma recursiva
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Administración de dispositivos
#### <a name="remove-non-present-pnp-devices"></a>Quitar dispositivos Plug and Play no existentes
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Administración de directiva de grupo
#### <a name="force-group-policy-update"></a>Forzar la actualización de la directiva de grupo
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Herramientas varias
#### <a name="show-os-version"></a>Mostrar la versión del sistema operativo
`ver`

o 

`wmic os get caption,version,buildnumber /format:list`

o 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Ver la fecha de instalación del sistema operativo
`systeminfo | find /i "original"`

o 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Ver la hora del último arranque
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Ver la zona horaria
`systeminfo | find /i "time zone"`

o

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Reiniciar Windows
`shutdown /r /t 0`

Al agregar `/f` se forzará a que las aplicaciones en ejecución se cierren sin avisar a los usuarios.
#### <a name="detect-safe-mode-boot"></a>Detectar el modo de arranque seguro
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Comandos de Windows: PowerShell

Para ejecutar PowerShell en SAC, después de llegar a un símbolo del sistema CMD, escriba:

`powershell <enter>`

>[!CAUTION]
Quite el módulo PSReadLine de la sesión de PowerShell antes de ejecutar otros comandos de PowerShell. Existe un problema conocido en el que se pueden introducir caracteres adicionales en el texto pegado desde el portapapeles si PSReadLine se ejecuta en una sesión de PowerShell en SAC.

En primer lugar, compruebe si PSReadLine está cargado. Se carga de forma predeterminada en Windows Server 2016, Windows 10 y versiones posteriores de Windows. Solo existiría en versiones anteriores de Windows si se hubiera instalado manualmente. 

Si este comando devuelve un símbolo del sistema sin ninguna salida, significa que el módulo no se ha cargado y puede seguir usando la sesión de PowerShell en SAC de la forma habitual.

`get-module psreadline`

Si el comando anterior devuelve la versión del módulo PSReadLine, ejecute el siguiente comando para descargarlo. Este comando no elimina ni desinstala el módulo, solo lo descarga de la sesión actual de PowerShell.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Visualización y edición de la configuración del Registro de Windows
#### <a name="verify-rdp-is-enabled"></a>Comprobar que RDP está habilitado
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

La segunda clave (en \Policies) solo existe si está configurada la opción de directiva de grupo pertinente.
#### <a name="enable-rdp"></a>Habilitar RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

La segunda clave (en \Policies) solo se necesitaría si se ha configurado la opción de directiva de grupo pertinente. El valor se volverá a escribir en la siguiente actualización de directiva de grupo si está configurado en la directiva de grupo.
### <a name="manage-windows-services"></a>Administración de los servicios de Windows
#### <a name="view-service-details"></a>Ver los detalles del servicio
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

Aunque se puede usar `Get-Service`, no incluye la cuenta de inicio de sesión del servicio. `Get-WmiObject win32-service` sí la incluye.
#### <a name="set-service-logon-account"></a>Establecer la cuenta de inicio de sesión del servicio
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Cuando se use una cuenta de servicio distinta de `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` o `LocalSystem`, especifique la contraseña de la cuenta como el último argumento (octavo) después del nombre de cuenta.
#### <a name="set-service-startup-type"></a>Establecer el tipo de inicio del servicio
`set-service termservice -startuptype Manual`

`Set-service` acepta `Automatic`, `Manual` o `Disabled` como tipo de inicio.
#### <a name="set-service-dependencies"></a>Establecer las dependencias del servicio
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Iniciar el servicio
`start-service termservice`
#### <a name="stop-service"></a>Detener el servicio
`stop-service termservice`
### <a name="manage-networking-features"></a>Administración de las características de red
#### <a name="show-nic-properties"></a>Mostrar las propiedades de NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

o 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` está disponible en 2012+; para 2008R2, use `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Mostrar las propiedades de IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Habilitar NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

o

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` está disponible en 2012+; para 2008R2, use `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Establecer NIC para usar DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` está disponible en 2012+. Para 2008R2, use `Get-WmiObject`. Las máquinas virtuales de Azure se deben configurar siempre en el sistema operativo invitado para usar DHCP para obtener una dirección IP. La configuración de IP estática de Azure sigue usando DHCP para proporcionar la dirección IP a la máquina virtual.
#### <a name="ping"></a>Ping
`test-netconnection`

o

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` sin ningún parámetro intentará hacer ping a `internetbeacon.msedge.net`. Está disponible en 2012+. Para 2008R2, use `Get-WmiObject` como en el segundo ejemplo.
#### <a name="port-ping"></a>Ping de puerto
`test-netconnection -ComputerName bing.com -Port 80`

o

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` está disponible en 2012+. Para 2008R2, use `Net.Sockets.TcpClient`.
#### <a name="test-dns-name-resolution"></a>Probar la resolución de nombres DNS
`resolve-dnsname bing.com` 

o 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` está disponible en 2012+. Para 2008R2, use `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Mostrar la regla de Firewall de Windows por nombre
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Mostrar la regla de Firewall de Windows por puerto
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

o

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` está disponible en 2012+. Para 2008R2, use el objeto COM `hnetcfg.fwpolicy2`. 
#### <a name="disable-windows-firewall"></a>Deshabilitar el Firewall de Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` está disponible en 2012+. Para 2008R2, use `netsh advfirewall` como se hace referencia en la sección de CMD anterior.
### <a name="manage-users-and-groups"></a>Administración de usuarios y grupos
#### <a name="create-local-user-account"></a>Crear una cuenta de usuario local
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Comprobar que la cuenta de usuario está habilitada
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

o 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` está disponible en 2012+. Para 2008R2, use `Get-WmiObject`. En este ejemplo se muestra la cuenta de administrador local integrada, que siempre tiene el SID `S-1-5-21-*-500`. El nombre de la cuenta de administrador local de las máquinas virtuales de Azure creadas a partir de la imagen generalizada tomará el especificado durante el aprovisionamiento de la máquina virtual. Así que normalmente no será `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Agregar un usuario local a un grupo local
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Habilitar la cuenta de usuario local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

En este ejemplo se habilita la cuenta de administrador local integrada, que siempre tiene el SID `S-1-5-21-*-500`. El nombre de la cuenta de administrador local de las máquinas virtuales de Azure creadas a partir de la imagen generalizada tomará el especificado durante el aprovisionamiento de la máquina virtual. Así que normalmente no será `Administrator`.
#### <a name="view-user-account-properties"></a>Ver las propiedades de la cuenta de usuario
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

o 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` está disponible en 2012+. Para 2008R2, use `Get-WmiObject`. En este ejemplo se muestra la cuenta de administrador local integrada, que siempre tiene el SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Ver los grupos locales
`(get-localgroup).name | sort``(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` está disponible en 2012+. Para 2008R2, use `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Administración del registro de eventos de Windows
#### <a name="query-event-log-errors"></a>Consultar los errores del registro de eventos
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Cambie `/c:10` por el número deseado de eventos que se devolverán o muévalo para que se devuelvan todos los eventos que coincidan con el filtro.
#### <a name="query-event-log-by-event-id"></a>Consultar el registro de eventos por identificador de evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Consultar el registro de eventos por identificador de evento y proveedor
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar el registro de eventos por identificador de evento y proveedor durante las últimas 24 horas
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para recuperar 7 días en lugar de 24 horas. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar el registro de eventos por identificador de evento, proveedor y datos de evento en los últimos 7 días
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Visualización o eliminación de las aplicaciones instaladas
#### <a name="list-installed-software"></a>Mostrar el software instalado
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Desinstalar el software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Administración del sistema de archivos
#### <a name="get-file-version"></a>Obtener la versión del archivo
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

En este ejemplo se devuelve la versión del archivo del controlador NIC virtual, que se llama netvsc.sys, netvsc63.sys o netvsc60.sys, según la versión de Windows.
#### <a name="download-and-extract-file"></a>Descargar y extraer el archivo
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

En este ejemplo se crea una carpeta `c:\bin`, y luego se descarga y se extrae el conjunto de herramientas de Sysinternals en `c:\bin`.
### <a name="miscellaneous-tasks"></a>Herramientas varias
#### <a name="show-os-version"></a>Mostrar la versión del sistema operativo
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Ver la fecha de instalación del sistema operativo
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Ver la hora del último arranque
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Ver el tiempo de actividad de Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Devuelve el tiempo de actividad como `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, por ejemplo `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Reiniciar Windows
`restart-computer`

Al agregar `-force` se forzará a que las aplicaciones en ejecución se cierren sin avisar a los usuarios.
### <a name="instance-metadata"></a>Metadatos de instancia

Puede consultar los metadatos de instancia de Azure desde la máquina virtual de Azure para ver detalles como osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress y publicIpAddress.

Para consultar los metadatos de instancia se requiere una buena conectividad de la red del invitado, dado que se realiza una llamada REST a través del host de Azure al servicio de metadatos de instancia. De modo que, si puede consultar los metadatos de instancia, significa que su invitado puede comunicarse a través de la red a un servicio hospedado por Azure.

Para más información, consulte [Servicio de metadatos de instancia de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Metadatos de instancia
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Tipo de sistema operativo (metadatos de instancia)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Ubicación (metadatos de instancia)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Tamaño (metadatos de instancia)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>Identificador de máquina virtual (metadatos de instancia)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Nombre de máquina virtual (metadatos de instancia)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Nombre del grupo de recursos (metadatos de instancia)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>Identificador de suscripción (metadatos de instancia)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Etiquetas (metadatos de instancia)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>Identificador de grupo de selección de ubicación (metadatos de instancia)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Dominio de error de plataforma (metadatos de instancia)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Dominio de actualización de plataforma (metadatos de instancia)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>Dirección IP privadas de IPv4 (metadatos de instancia)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Dirección IP pública de IPv4 (metadatos de instancia)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Dirección/prefijo de subred IPv4 (metadatos de instancia)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>Dirección IP de IPv6 (metadatos de instancia)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>Dirección MAC (metadatos de instancia)
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
La mayoría de errores son transitorios por naturaleza, y basta con intentar establecer la conexión de nuevo para solucionarlos. La tabla siguiente muestra una lista de errores y su solución 

Error                            |   Mitigación 
:---------------------------------|:--------------------------------------------|
No se puede recuperar la configuración de diagnóstico de arranque para "<VMNAME>". Para usar la consola serie, asegúrese de que el diagnóstico de arranque está habilitado para esta máquina virtual. | Asegúrese de que la máquina virtual tiene los [diagnósticos de arranque](boot-diagnostics.md) habilitados. 
La máquina virtual se encuentra en un estado desasignado detenido. Inicie la máquina virtual y vuelva a intentar la conexión de la consola serie. | La máquina virtual tiene que estar en un estado iniciado para tener acceso a la consola serie
No tiene los permisos necesarios para usar la consola serie de esta máquina virtual. Asegúrese de tener al menos los permisos del rol de colaborador de máquina virtual.| El acceso a la consola serie requiere cierto nivel de permiso. Consulte los [requisitos de acceso](#prerequisites) para más información
No se puede determinar el grupo de recursos para la cuenta de almacenamiento de diagnósticos de arranque "<STORAGEACCOUNTNAME>". Compruebe que el diagnóstico de arranque está habilitado para esta máquina virtual y que tiene acceso a esta cuenta de almacenamiento. | El acceso a la consola serie requiere cierto nivel de permiso. Consulte los [requisitos de acceso](#prerequisites) para más información

## <a name="known-issues"></a>Problemas conocidos 
Como nos encontramos aún en las etapas de versión preliminar para el acceso a la consola serie, estamos trabajando en algunos problemas conocidos. Presentamos aquí la lista con algunas posibles soluciones 

Problema                           |   Mitigación 
:---------------------------------|:--------------------------------------------|
No hay opción para la consola serie con la instancia de conjunto de escalado de máquinas virtuales | Con esta versión preliminar, no se admite el acceso a la consola serie para las instancias del conjunto de escalado de máquinas virtuales.
Al pulsar Entrar tras un banner de conexión no aparece la solicitud de inicio de sesión | [Pulsar Entrar no hace nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Se muestra solamente información de mantenimiento cuando se conecta a una máquina virtual Windows| [Señales de mantenimiento de Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
No se puede escribir en el símbolo del sistema de SAC si la depuración del kernel está habilitada | Conéctese mediante RDP a la máquina virtual y ejecute `bcdedit /debug {current} off` desde un símbolo del sistema con privilegios elevados. Si no puede conectarse mediante RDP, en su lugar puede asociar el disco del sistema operativo a otra máquina virtual de Azure y modificarlo mientras está asociado como un disco de datos mediante `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`. Luego, puede volver a intercambiar el disco.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
**P. ¿Cómo puedo enviar comentarios?**

A. Envíe comentarios como problemas que tenga acudiendo a https://aka.ms/serialconsolefeedback. También tiene la posibilidad (aunque se prefiere menos) de enviar comentarios a azserialhelp@microsoft.com, o en la categoría de máquinas virtuales en http://feedback.azure.com 

**P. Aparece el error "Existing console has conflicting OS type "Windows" with the requested OS type of Linux?" (¿Tiene la consola existente un tipo de SO "Windows" en conflicto con el tipo de SO solicitado de Linux?)**

A. Se trata de un problema conocido; para solucionarlo, basta con abrir [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en modo Bash e intentarlo de nuevo.

**P. No puedo acceder a la consola serie, ¿dónde puedo presentar un caso de soporte técnico?**

A. Esta característica de versión preliminar viene recogida en los términos de las versiones preliminares de Azure. La mejor manera de obtener soporte técnico es recurrir a los canales mencionados anteriormente. 

## <a name="next-steps"></a>Pasos siguientes
* La consola serie también está disponible para máquinas virtuales [Linux](../linux/serial-console.md)
* Más información acerca de los [diagnósticos de arranque](boot-diagnostics.md)
