---
title: "Preparación de un disco duro virtual de Windows para cargarlo en Azure | Microsoft Docs"
description: "Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 91c443f6d9998f15f4b1b1eaad33f0a319fcf4d4
ms.contentlocale: es-es
ms.lasthandoff: 08/02/2017

---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure
Antes de cargar una máquina virtual Windows desde un entorno local en Microsoft Azure, debe preparar el disco duro virtual (VHD o VHDX). Azure admite solo máquinas virtuales de generación 1 que estén en el formato de archivo VHD y tengan un disco de tamaño fijo. El tamaño máximo permitido para los discos duros virtuales es de 1023 GB. Puede convertir una máquina virtual de generación 1 del sistema de archivos VHDX a VHD y de un disco de expansión dinámica a uno de tamaño fijo. Sin embargo, no puede cambiar la generación de una máquina virtual. Para obtener más información, consulte [¿Debería crear una máquina virtual de generación 1 o 2 en Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

Para obtener información sobre la directiva de soporte de software de servidor de Microsoft ejecutado en Azure, consulte [Soporte de software de servidor de Microsoft para las máquinas virtuales de Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Las instrucciones que aparecen en este artículo se aplican a la versión de 64 bits de Windows Server 2008 R2 y un sistema operativo Windows Server. Para obtener información sobre cómo ejecutar la versión de 32 bits del sistema operativo en Azure, consulte [Soporte para sistemas operativos de 32 bits en máquinas virtuales de Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Conversión del disco virtual a VHD y disco de tamaño fijo 
Si necesita convertir el disco virtual al formato requerido para Azure, utilice uno de los métodos de esta sección. Haga una copia de seguridad de la máquina virtual antes de ejecutar el proceso de conversión de disco virtual y asegúrese de que el VHD de Windows funciona correctamente en el servidor local. Resuelva los errores dentro de la propia máquina virtual antes de intentar la conversión o la carga en Azure.

Después de convertir el disco, cree una máquina virtual que use el disco convertido. Comience e inicie sesión en la máquina virtual a fin de preparar la máquina virtual para la carga.

### <a name="convert-disk-using-hyper-v-manager"></a>Conversión del disco mediante el Administrador de Hyper-V
1. Abra el Administrador de Hyper-V y seleccione el equipo local de la izquierda. En el menú situado sobre la lista de equipos, haga clic en **Acción** > **Editar disco**.
2. En la pantalla **Locate Virtual Hard Disk** (Localizar disco duro virtual), localice y seleccione el disco virtual.
3. En la pantalla **Elegir acción**, seleccione **Convertir** y **Siguiente**.
4. Si necesita convertir VHDX, seleccione **VHD** y, a continuación, haga clic en **Siguiente**
5. Si necesita convertir desde un disco de expansión dinámica, seleccione **Tamaño fijo** y, después, haga clic en **Siguiente**
6. Localice y seleccione una ruta de acceso para guardar el nuevo archivo VHD en ella.
7. Haga clic en **Finalizar**

>[!NOTE]
>Los comandos de este artículo se deben ejecutar en una sesión de PowerShell con privilegios elevados.

### <a name="convert-disk-by-using-powershell"></a>Conversión de disco con PowerShell
Puede convertir un disco virtual mediante el comando [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) en Windows PowerShell. Seleccione **Ejecutar como administrador** al iniciar PowerShell. 

El comando de ejemplo siguiente convierte de VHDX a VHD y de un disco de expansión dinámica a uno de tamaño fijo:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
En este comando, reemplace el valor de "-Path" por la ruta de acceso al disco duro virtual que desea convertir y el valor de "-DestinationPath" por la nueva ruta de acceso y el nombre del disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversión del formato de disco VMDK de VMware
Si tiene una imagen de máquina virtual Windows en el [formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), conviértalo en un disco duro virtual con [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consulte el artículo [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Cómo convertir un VMDK de VMWare a VHD de Hyper-V) del blog para obtener más información.

## <a name="set-windows-configurations-for-azure"></a>Establecimiento de configuraciones de Windows para Azure

En la máquina virtual que tiene previsto cargar en Azure, ejecute todos los comandos en los pasos siguientes desde una [ventana del símbolo del sistema con privilegios elevados](https://technet.microsoft.com/library/cc947813.aspx):

1. Quite cualquier ruta estática persistente de la tabla de enrutamiento:
   
   * Para ver la tabla de rutas, ejecute `route print` en el símbolo del sistema.
   * Compruebe las secciones **Persistence Routes** (Rutas de persistencia). Si hay una ruta persistente, use [route delete](https://technet.microsoft.com/library/cc739598.apx) para quitarla.
2. Quite al proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Configure la directiva SAN de disco como [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    En la ventana del símbolo del sistema abierta, escriba los comandos siguientes:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Establezca la hora universal coordinada (UTC) para Windows y el tipo de inicio del Servicio de hora de Windows (w32time) en **Automáticamente**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Establezca el perfil de energía en **Alto rendimiento**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Comprobación de los servicios de Windows
Asegúrese de que cada uno de los siguientes servicios de Windows esté establecido en los **valores predeterminados de Windows**. Esta es la cantidad de servicios mínima que debe configurarse para garantizar que la máquina virtual tenga conectividad. Para restablecer la configuración de inicio, ejecute los siguientes comandos:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Actualización de la configuración del Registro de Escritorio remoto
Asegúrese de que la siguiente configuración está establecida correctamente para la conexión a Escritorio remoto:

>[!Note] 
>Puede recibir un mensaje de error al ejecutar el **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;nombre de objeto&gt; &lt;valor&gt;** en estos pasos. El mensaje de error puede pasarse por alto de forma segura. Solo significa que el dominio no obliga a esa configuración a aceptar un objeto de directiva de grupo.
>
>

1. Protocolo de escritorio remoto (RDP) está habilitado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. El puerto de RDP está configurado correctamente (puerto 3389 predeterminado):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Al implementar una máquina virtual, las reglas predeterminadas se crean en el puerto 3389. Si desea cambiar el número de puerto, hágalo una vez implementada la máquina virtual en Azure.

3. El agente de escucha está escuchando en todas las interfaces de red:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Configure el modo Autenticación a nivel de red para las conexiones RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Establezca el valor de conexión persistente:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Vuelva a conectarse:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Limite el número de conexiones simultáneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Si hay algún certificado autofirmado enlazado al agente de escucha del Protocolo de escritorio remoto, quítelo:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Esto es para asegurarse de que puede conectarse al principio al implementar la máquina virtual. También puede revisar esto más tarde una vez implementada la máquina virtual en Azure en caso necesario.

9. Si la máquina virtual va a formar parte de un dominio, compruebe toda la configuración siguiente para asegurarse de que la configuración anterior no se revierte. Las directivas que deben comprobarse son los siguientes:
    
    - RDP está habilitado:

         Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\ Componentes\Servicios de Escritorio remoto\Host de sesión de Escritorio remoto\Conexiones:
         
         **Permitir a los usuarios conectarse de forma remota con Escritorio remoto**

    - Directiva de grupo de NLA:

        Configuración\Plantillas administrativas\Componentes\Servicios de Escritorio remoto\Host de sesión de Escritorio remoto\Seguridad: 
        
        **Exigir la autenticación de usuarios para conexiones remotas con Autenticación a nivel de red**
    
    - Configuración de conexión persistente:

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\ Componentes Windows\Servicios de Escritorio remoto\Host de sesión de Escritorio remoto\Conexiones: 
        
        **Configurar el intervalo de conexión persistente**

    - Vuelva a conectar la configuración:

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\ Componentes Windows\Servicios de Escritorio remoto\Host de sesión de Escritorio remoto\Conexiones: 
        
        **Reconexión automática**

    - Limite el número de valores de configuración de conexiones:

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\ Componentes Windows\Servicios de Escritorio remoto\Host de sesión de Escritorio remoto\Conexiones: 
        
        **Limitar el número de conexiones**

## <a name="configure-windows-firewall-rules"></a>Configuración de reglas de firewall de Windows
1. Active Firewall de Windows en los tres perfiles (Dominio, Estándar y Público):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. Ejecute el comando siguiente en PowerShell para permitir WinRM mediante los tres perfiles de firewall (Dominio, Privado y Público) y habilitar el servicio remoto de PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Habilitar las siguientes reglas de firewall para permitir el tráfico RDP 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Habilite la regla Compartir archivos e impresoras para que la máquina virtual pueda responder a un comando ping dentro de la red virtual:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Si la máquina virtual va a formar parte de un dominio, compruebe la configuración siguiente para asegurarse de que la configuración anterior no se revierte. Las directivas de AD que deben comprobarse son las siguientes:

    - Habilitar los perfiles de Firewall de Windows

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\Red\Conexión de red\Firewall de Windows\Perfil de dominio\Firewall de Windows: **Proteger todas las conexiones de red**

       Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\Red\Conexión de red\Firewall de Windows\Perfil estándar\Firewall de Windows: **Proteger todas las conexiones de red**

    - Habilitar RDP 

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\Red\Conexión de red\Firewall de Windows\Perfil de dominio\Firewall de Windows: **Permitir las excepciones de Escritorio remoto de entrada**

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\Red\Conexión de red\Firewall de Windows\Perfil estándar\Firewall de Windows: **Permitir las excepciones de Escritorio remoto de entrada**

    - Habilitar ICMP-V4

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\Red\Conexión de red\Firewall de Windows\Perfil de dominio\Firewall de Windows: **Permitir las excepciones ICMP**

        Configuración del equipo\Directivas\Configuración de Windows\Plantillas administrativas\Red\Conexión de red\Firewall de Windows\Perfil estándar\Firewall de Windows: **Permitir las excepciones ICMP**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Comprobación de que la que máquina virtual es correcta, está segura y es accesible con RDP 
1. Para garantizar que el disco sea coherente y esté en buen estado, ejecute una operación de comprobación de disco en el próximo reinicio de máquina virtual:

    ```PowerShell
    Chkdsk /f
    ```
    Asegúrese de que el informe muestra un disco limpio y en buen estado.

2. Establezca la configuración de datos de la configuración de arranque (BCD). Ejecute estos comandos en una ventana de CMD con privilegios elevados:
   
   ```PowerShell
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Compruebe que el repositorio de Instrumental de administración de Windows es coherente. Para ello, ejecute el siguiente comando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Si el repositorio está dañado, consulte [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: ¿daños en el repositorio o no?).

4. Asegúrese de que ninguna otra aplicación está utilizando el puerto 3389. Este puerto se usa para el servicio RDP en Azure. Puede ejecutar **netstat -anob** para ver qué puertos se utilizan en la máquina virtual:

    ```PowerShell
    netstat -anob
    ```

5. Si el disco duro virtual de Windows que quiere cargar es un controlador de dominio, siga estos pasos:

    A. Siga [estos pasos adicionales](https://support.microsoft.com/kb/2904015) para preparar el disco.

    B. Asegúrese de que conoce la contraseña de DSRM en caso de tener que iniciar la máquina virtual en DSRM en algún momento. Puede que desee hacer referencia a este vínculo para establecer la [contraseña de DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Asegúrese de que conoce la contraseña y la cuenta predefinida de administrador. Es posible que quiera restablecer la contraseña de administrador local actual y asegurarse de que puede usar esta cuenta para iniciar sesión en Windows mediante la conexión RDP. Este permiso de acceso se controla mediante el objeto de directiva de grupo "Permitir inicio de sesión a través de Servicios de Escritorio remoto". Puede ver este objeto en el Editor de directivas de grupo local en:

    Configuración del equipo\Configuración de Windows\Configuración de seguridad\Directivas locales\Asignación de derechos de usuario

7. Compruebe las siguientes directivas de AD para asegurarse de que no bloquea su acceso de RDP a través de RDP ni desde la red:

    - Configuración del equipo\Configuración de Windows\Configuración de seguridad\Directivas locales\Asignación de derechos de usuario\Denegar el acceso a este equipo desde la red

    - Configuración del equipo\Configuración de Windows\Configuración de seguridad\Directivas locales\Asignación de derechos de usuario\Denegar inicio de sesión a través de Servicios de Escritorio remoto


8. Reinicie la máquina virtual para asegurarse de que Windows funciona aún correctamente y se puede conectar a él mediante RDP. En este momento, puede que desee crear una máquina virtual en el Hyper-V local para asegurarse de que esta se inicia completamente y, a continuación, probar si es accesible desde RDP.

9. Quite los filtros adicionales de la Interfaz de controlador de transporte, como el software que analiza los paquetes TCP o los firewalls adicionales. También puede revisar esto más tarde una vez implementada la máquina virtual en Azure en caso necesario.

10. Desinstale cualquier otro software de terceros y controlador relacionado con componentes físicos o cualquier otra tecnología de virtualización.

### <a name="install-windows-updates"></a>Instalación de actualizaciones de Windows
La configuración ideal es **tener el nivel de revisión de la máquina en la versión más reciente**. Si no es posible, asegúrese de que las siguientes actualizaciones están instaladas:

|                       |                   |           |                                       Versión del archivo mínima x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Componente               | Binary            | Windows 7 y Windows Server 2008 R2 | Windows 8 y Windows Server 2012             | Windows 8.1 y Windows Server 2012 R2 | Windows 10 y Windows Server 2016 RS1 | Windows 10 RS2             |
| Almacenamiento                 | disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | ntfs.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | Msdsm.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | Mpio.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Red                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | http.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Núcleo                    | ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Servicios de Escritorio remoto | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | termsrv.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Seguridad                | Debido a WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### Cuándo usar sysprep <a id="step23"></a>    

Sysprep es un proceso que podría ejecutar en una instalación de Windows que restablecerá la instalación del sistema y proporcionará una “configuración rápida” quitando todos los datos personales y restableciendo varios componentes. Suele hacer esto si desea crear una plantilla desde la que pueda implementar muchas otras máquinas virtuales que tengan una configuración específica. Esto se conoce como **imagen generalizada**.

Si, en su lugar, solo desea crear una máquina virtual desde un disco, no tiene que usar sysprep. En esta situación, puede limitarse a crear la máquina virtual desde lo que se conoce como **imagen especializada**.

Para obtener más información sobre cómo crear una máquina virtual desde un disco especializado, consulte:

- [Creación de una máquina virtual a partir de un disco especializado](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/) (Creación de una máquina virtual a partir de un disco duro virtual especializado)

Si desea crear una imagen generalizada, debe ejecutar sysprep. Para obtener más información sobre Sysprep, consulte [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx). 

No todos los roles o aplicaciones instalados en un equipo basado en Windows admiten esta generalización. Así pues, antes de ejecutar este procedimiento, consulte el siguiente artículo para asegurarse de que sysprep admite el rol de ese equipo. Para obtener más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Compatibilidad de Sysprep con los roles de servidor).

### <a name="steps-to-generalize-a-vhd"></a>Pasos para generalizar un disco duro virtual

>[!NOTE]
> Después de ejecutar sysprep.exe tal como se especifica en los pasos siguientes, desactive la máquina virtual y no vuelva a activarla hasta que cree una imagen de ella en Azure.

1. Inicie sesión en la máquina virtual Windows.
2. Ejecute **Símbolo del sistema** como administrador. 
3. Cambie el directorio a: **%windir%\system32\sysprep** y, a continuación, ejecute **sysprep.exe**.
3. En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la casilla **Generalizar** está activada.

    ![Herramienta de preparación del sistema](media/prepare-for-upload-vhd-image/syspre.png)
4. En **Opciones de apagado**, seleccione **Apagar**.
5. Haga clic en **Aceptar**.
6. Cuando finalice Sysprep, apague la máquina virtual. No use **Reiniciar** para apagar la máquina virtual.
7. Ahora el disco duro virtual está listo para cargarse. Para obtener más información sobre cómo crear una máquina virtual desde un disco generalizado, consulte [Carga de un VHD generalizado en Azure para crear una máquina virtual nueva](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Realice las configuraciones recomendadas
Los siguientes valores de configuración no afectan a la carga de discos duros virtuales. Sin embargo, se recomienda firmemente que los configure.

* Instale el [agente de máquina virtual de Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A continuación, puede habilitar las extensiones de máquina virtual. Las extensiones de máquina virtual implementan la mayor parte de la funcionalidad crítica que es posible que quiera usar con las máquinas virtuales, como el restablecimiento de contraseñas, la configuración de RDP, etc. Para más información, consulte:

    - [VM Agent and Extensions – Part 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/) (Agente de VM y extensiones: parte 1)
    - [VM Agent and Extensions – Part 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) (Agente de VM y extensiones: parte 2)
* El registro de volcado de memoria puede ser útil para solucionar problemas de bloqueo de Windows. Habilite la colección de registros de volcado de memoria:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Si recibe errores durante alguno de los pasos de procedimientos de este artículo, significa que las claves del Registro ya existen. En esta situación, use los siguientes comandos en su lugar:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Después de crearse la máquina virtual en Azure, recomendamos que ponga el archivo de paginación en el volumen "Unidad temporal" para mejorar el rendimiento. Puede configurar esto como se muestra a continuación:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Si hay algún disco de datos conectado a la máquina virtual, la letra de unidad del volumen Unidad temporal suele ser "D". Esta designación podría ser diferente, dependiendo del número de unidades disponibles y de la configuración creada.

## <a name="next-steps"></a>Pasos siguientes
* [Carga de una imagen de máquina virtual de Windows en Azure para implementaciones de Resource Manager](upload-generalized-managed.md)


