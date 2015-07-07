<properties
	pageTitle="Implementar y administrar copias de seguridad para Windows Server o cliente de Windows mediante Windows PowerShell | Microsoft Azure"
	description="Aprenda a implementar y administrar Copia de seguridad de Azure mediante Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Implementar y administrar copias de seguridad en Azure para Windows Server o cliente de Windows mediante Windows PowerShell
En este artículo se muestra cómo se usa la interfaz de línea de comandos de Windows PowerShell® para instalar Copia de seguridad de Azure en Windows Server o cliente de Windows y para administrar copias de seguridad y recuperaciones.

## Instalación del entorno de Windows PowerShell
Antes de poder usar Windows PowerShell para administrar Copia de seguridad de Azure, deberá tener instaladas la versión y las variables de entorno adecuadas de Windows PowerShell.

## Instalación y registro
Las siguientes tareas de instalación y registro se pueden automatizar con Windows PowerShell: instalación del agente de Copia de seguridad de Azure; registro con el servicio de Copia de seguridad de Azure; redes

### Instalación del agente de Copia de seguridad de Azure
Para instalar al agente de Copia de seguridad de Azure, necesitará tener el instalador descargado y presente en Windows Server. Puede obtener la versión más reciente del instalador en el [Centro de descarga de Microsoft ](aka.ms/azurebackup_agent). Guarde el instalador en una ubicación que tenga fácil acceso, como *C:\Downloads*. Para instalar el agente, ejecute el comando siguiente en una consola de Windows PowerShell con privilegios elevados:

```
PS C:> MARSAgentInstaller.exe /q
```

Eso instalará el agente con todas las opciones predeterminadas. La instalación está unos minutos en segundo plano. Si no se especifica la opción **/nu**, se abrirá la ventana de **Windows Update** al final de la instalación para comprobar si hay actualizaciones.

El agente se mostrará en la lista de programas instalados. Para ver la lista de programas instalados, vaya a **Panel de Control** > **programas** > **programas y características**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

#### Opciones de instalación

Para ver todas las opciones disponibles a través de la línea de comandos, use el siguiente comando:

```
PS C:> MARSAgentInstaller.exe /?
```

Las opciones disponibles incluyen:

| Opción | Detalles | Valor predeterminado |
| ---- | ----- | ----- |
| /q | Instalación desatendida | - | | /p:"ubicación" | Ruta de acceso a la carpeta de instalación del agente de Copia de seguridad de Azure. | C:\Archivos de programa\Microsoft Azure Recovery Services Agent | | /s:"ubicación" | Ruta de acceso a la carpeta de caché del agente de Copia de seguridad de Azure. | C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch | | /m | Participar en Microsoft Update | - | | /nu | No comprobar si hay actualizaciones cuando finalice la instalación | - | | /d | Desinstala el agente de Servicios de recuperación de Microsoft Azure | - | | /ph | Dirección de host del proxy | - | | /po | Número de puerto de host del proxy | - | | /pu | Nombre de usuario de host del proxy | - | | /pw | Contraseña del proxy | - |


### Registro con el servicio de Copia de seguridad de Azure
Para poder registrarse con el servicio de Copia de seguridad de Azure, asegúrese de que se cumplen los [requisitos previos](backup-try-azure-backup-in-10-mins.md): disponer de una suscripción válida a Azure; crear un almacén de copia de seguridad; descargar las credenciales del almacén de claves y almacenarlas en una ubicación práctica (como *C:\Downloads*). También se puede cambiar el nombre de las credenciales del almacén de claves para que sea más práctico.

El registro de la máquina con el almacén de claves se realiza mediante el cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx):

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]No use rutas de acceso relativas para especificar el archivo de credenciales del almacén de claves. Debe proporcionar una ruta de acceso absoluta como entrada para el cmdlet.


### Redes
Cuando la conectividad de la máquina de Windows a Internet se realiza a través de un servidor proxy, también se puede proporcionar la configuración del proxy al agente. En este ejemplo, no hay ningún servidor proxy y por tanto se borra explícitamente cualquier información relacionada con el proxy.

También se puede controlar el uso de ancho de banda con las opciones de *ancho de banda de horas laborables* y *ancho de banda de horas no laborables* para un conjunto determinado de días de la semana.

El establecimiento de los detalles de ancho de banda y del proxy se realiza mediante el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx):

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Configuración de cifrado
Los datos de copia de seguridad enviados a Copia de seguridad de Azure están cifrados para proteger la confidencialidad de los datos. La frase de contraseña de cifrado es la "contraseña" que permite descifrar los datos en el momento de la restauración. Es importante que mantenga esta información segura cuando la establezca.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Desinstalación del agente de Copia de seguridad de Azure
La desinstalación del agente de Copia de seguridad de Azure se puede realizar con el comando siguiente:

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

La desinstalación de los archivos binarios del agente de la máquina tiene otras consecuencias: quita el filtro de archivos de la máquina y se detiene el seguimiento de los cambios; toda la información de directivas se quita de la máquina, pero la información de directivas se sigue almacenando en el servicio; todas las programaciones de copias de seguridad se quitan y no se realiza ninguna copia de seguridad más.

Sin embargo, los datos almacenados en Azure permanecen y se mantienen según la configuración de la directiva de retención que haya establecido. Los puntos más antiguos vencen automáticamente.

## Administración remota
Toda la administración relacionada con el agente, las directivas y los orígenes de datos de Copia de seguridad de Azure puede realizarse de forma remota mediante Windows PowerShell. La máquina que se administrará de forma remota debe estar preparada correctamente.

De forma predeterminada, el servicio WinRM está configurado para iniciarse manualmente. El tipo de inicio debe establecerse en *Automatic* y se debe iniciar el servicio. Para comprobar que el servicio WinRM se está ejecutando, el valor de la propiedad Status debe ser *Running*.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

La máquina puede ahora administrarse de forma remota: empezando por la instalación del agente. Por ejemplo, el script siguiente copia al agente en la máquina remota y lo instala.

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## Pasos siguientes
Para obtener más información sobre Copia de seguridad de Azure para Windows Server o cliente de Windows, consulte [Introducción a Copia de seguridad de Azure](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->