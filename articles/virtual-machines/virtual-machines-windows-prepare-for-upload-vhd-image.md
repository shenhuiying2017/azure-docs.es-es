---
title: "Preparación de un disco duro virtual de Windows para cargarlo en Azure | Microsoft Docs"
description: "Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: glimoli;genli
translationtype: Human Translation
ms.sourcegitcommit: 5d8274f61c3de178c9d418adb9be1efe0fe62bc1
ms.openlocfilehash: 6fbfc74cb1cce744b51345c0732b40b95be21c94


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure
Para cargar una máquina virtual Windows desde un entorno local en Azure, debe preparar el disco duro virtual (VHD o VHDX). Azure solo admite máquinas virtuales de generación 1 que estén en el formato de archivo VHD y tengan un disco de tamaño fijo. El tamaño máximo permitido para los discos duros virtuales es de 1023 GB. Puede convertir una máquina virtual de generación 1 de VHDX para el formato de archivo de VHD desde un disco de expansión dinámica a otro de tamaño fijo. Sin embargo, no puede cambiar la generación de una máquina virtual. Para obtener más información, vea [¿Debería crear una máquina virtual de generación 1 o 2 en Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Conversión del disco virtual a VHD y disco de tamaño fijo 
Si necesita convertir el disco virtual al formato requerido para Azure, utilice uno de los métodos de esta sección. Haga una copia de seguridad de la máquina virtual antes de ejecutar el proceso de conversión de disco virtual y asegúrese de que el VHD de Windows funciona correctamente en el servidor local. Resuelva los errores dentro de la propia máquina virtual antes de intentar la conversión o la carga en Azure.

Después de convertir el disco, cree una máquina virtual que use el disco convertido. Comience e inicie sesión en la máquina virtual a fin de preparar la máquina virtual para la carga.

### <a name="convert-disk-using-hyper-v-manager"></a>Conversión del disco mediante el Administrador de Hyper-V
1. Abra el Administrador de Hyper-V y seleccione el equipo local de la izquierda. En el menú superior, haga clic en **Acción** > **Editar disco**.
2. En la pantalla **Locate Virtual Hard Disk** (Localizar disco duro virtual), busque y seleccione el disco virtual.
3. En la pantalla **Elegir acción**, seleccione **Convertir** y **Siguiente**.
4. Si necesita convertir VHDX, seleccione **VHD** y haga clic en **Siguiente**
5. Si necesita convertir desde un disco de expansión dinámica, seleccione **Tamaño fijo** y después **Siguiente**
6. Busque y seleccione una ruta de acceso para guardar el nuevo archivo de VHD.
7. Haga clic en **Finalizar** para cerrar.

### <a name="convert-disk-using-powershell"></a>Conversión de disco con PowerShell
Puede convertir un disco virtual mediante el cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) en Windows PowerShell. Seleccione **Ejecutar como administrador** al iniciar PowerShell. En el ejemplo siguiente se muestra cómo convertir desde VHDX a VHD y desde un disco de expansión dinámica a un tamaño fijo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Reemplace los valores de -Path por la ruta de acceso al disco duro virtual que desea convertir y -DestinationPath por la nueva ruta de acceso y el nombre para el disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Conversión del formato de disco VMDK de VMware
Si tiene una imagen de máquina virtual de Windows en el [formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), conviértalo en un disco duro virtual con [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consulte el blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Cómo convertir un VMDK de VMWare a VHD de Hyper-V) para obtener más información.

## <a name="set-windows-configurations-for-azure"></a>Establecimiento de configuraciones de Windows para Azure

En la máquina virtual que tiene previsto cargar en Azure, ejecute los siguientes comandos desde la ventana de símbolo del sistema con [privilegios administrativos](https://technet.microsoft.com/library/cc947813.aspx).

1. Quite cualquier ruta estática persistente de la tabla de enrutamiento:
   
   * Para ver la tabla de rutas, ejecute `route print` desde la ventana de símbolo del sistema.
   * Compruebe las secciones **Persistence Routes** (Rutas de persistencia). Si hay una ruta persistente, use [route delete](https://technet.microsoft.com/library/cc739598.apx) para quitarla.
2. Quite al proxy WinHTTP:
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. Configure la directiva SAN de disco como [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. Establezca la hora universal coordinada (UTC) para Windows y el tipo de inicio del Servicio de hora de Windows (w32time) en **Automáticamente**:
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>Configuración del inicio de los servicios en los valores predeterminados de Windows
Asegúrese de que cada uno de los siguientes servicios de Windows esté establecido en los **valores predeterminados de Windows**. Para restablecer la configuración de inicio, ejecute los siguientes comandos:
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>Actualización de la configuración del Registro de Escritorio remoto
1. Si hay algún certificado autofirmado enlazado al agente de escucha del Protocolo de escritorio remoto (RDP), quítelo:
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    Para más información sobre cómo configurar certificados para el agente de escucha de RDP, consulte [Listener Certificate Configurations in Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. Configure los valores de [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) para el servicio RDP:
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. Configure el modo de autenticación para el servicio RDP:
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. Habilite el servicio RDP agregando las siguientes subclaves del Registro:
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Configuración de reglas de firewall de Windows
1. Ejecute el comando siguiente en PowerShell para permitir WinRM mediante los tres perfiles de firewall (Dominio, Privado y Público) y habilitar el servicio remoto de PowerShell:
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. Ejecute los comandos siguientes en la ventana de símbolo del sistema para asegurarse de que están implantadas las siguientes reglas de firewall del sistema operativo invitado:
   
   * Entrada
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * Entrada y salida
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * Salida
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Comprobación de que la que máquina virtual es correcta, está segura y es accesible con RDP 
1. En la ventana del símbolo del sistema, ejecute `winmgmt /verifyrepository` para confirmar que el repositorio de Instrumental de administración de Windows (WMI) es coherente. Si el repositorio está dañado, consulte el blog [WMI: Repository Corruption, or Not?](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not) (WMI: ¿daños en el repositorio o no?).
2. Establezca los ajustes de los datos de configuración de arranque:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Quite los filtros adicionales de la Interfaz de controlador de transporte, como el software que analiza los paquetes TCP.
4. Para asegurarse de que el disco está en buen estado y es coherente, ejecute el comando `CHKDSK /f` en la ventana de símbolo del sistema. Escriba "Y" para programar la comprobación y reinicie la máquina virtual.
5. Desinstale cualquier otro software de terceros y controlador relacionado con componentes físicos o cualquier otra tecnología de virtualización.
6. Asegúrese de que una aplicación de terceros no está utilizando el puerto 3389. Este puerto se usa para el servicio RDP en Azure. Puede ejecutar `netstat -anob` en la ventana de símbolo del sistema para ver los puertos utilizados por las aplicaciones.
7. Si el disco duro virtual de Windows que quiere cargar es un controlador de dominio, siga [estos pasos adicionales](https://support.microsoft.com/kb/2904015) para preparar el disco.
8. Reinicie la máquina virtual para asegurarse de que Windows funciona aun correctamente y se puede conectar a él mediante RDP.
9. Restablezca la contraseña de administrador local actual y asegúrese de que puede usar esta cuenta para iniciar sesión en Windows mediante la conexión RDP. Este permiso de acceso se controla mediante el objeto de directiva de grupo "Permitir inicio de sesión a través de Servicios de Escritorio remoto". Puede ver este objeto en el Editor de directivas de grupo local en "Configuración del equipo\Configuración de Windows\Configuración de seguridad\Directivas locales\Asignación de derechos de usuario".

## <a name="install-windows-updates"></a>Instalación de actualizaciones de Windows
Instale las actualizaciones más recientes de Windows. Si no es posible, asegúrese de que las siguientes actualizaciones estén instaladas:
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) Las máquinas virtuales de Microsoft Azure no se recuperan de una interrupción de la red y se producen problemas de corrupción de datos
   * [KB3115224](https://support.microsoft.com/kb/3115224) Mejoras en la confiabilidad para las máquinas virtuales que se ejecutan en un host de Windows Server 2012 R2 o Windows Server 2012
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Actualización de seguridad para Microsoft Windows para solucionar la elevación de privilegios: 8 de marzo de 2016
   * [KB3063075](https://support.microsoft.com/kb/3063075) Se registran muchos eventos con el ID 129 al ejecutar una máquina virtual de Windows Server 2012 R2 en Microsoft Azure
   * [KB3137061](https://support.microsoft.com/kb/3137061) Las máquinas virtuales de Microsoft Azure no se recuperan de una interrupción de la red y se producen problemas de corrupción de datos
   * [KB3114025](https://support.microsoft.com/kb/3114025) Rendimiento lento cuando tiene acceso al almacenamiento de archivos de Azure desde Windows 8.1 o Server 2012 R2
   * [KB3033930](https://support.microsoft.com/kb/3033930) La revisión aumenta el límite de 64 K en búferes RIO por proceso para servicios de Azure en Windows
   * [KB3004545](https://support.microsoft.com/kb/3004545) No se puede tener acceso a máquinas virtuales que se hospedan en servicios de hospedaje de Azure a través de una conexión VPN en Windows
   * [KB3082343](https://support.microsoft.com/kb/3082343) Se pierde la conectividad de VPN entre entornos cuando los túneles de VPN de sitio a sitio de Azure usan RRAS de Windows Server 2012 R2
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Actualización de seguridad para Microsoft Windows para solucionar la elevación de privilegios: 8 de marzo de 2016
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Descripción de la actualización de seguridad para CSRSS: 12 de abril de 2016
   * [KB2904100](https://support.microsoft.com/kb/2904100) El sistema se bloquea durante la E/S del disco en Windows
     
## <a name="run-sysprep--a-idstep23a"></a>Ejecute Sysprep  <a id="step23"></a>    
Si quiere crear una imagen para implementar a varias máquinas virtuales, debe [generalizar la imagen mediante la ejecución de Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de cargar el VHD en Azure. No es necesario ejecutar Sysprep para usar un VHD especializado. Para más información, consulte los siguientes artículos.
   
   * [Generalización de una máquina virtual de Windows con Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Compatibilidad de Sysprep con los roles de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="complete-recommended-configurations"></a>Realice las configuraciones recomendadas
Los siguientes valores de configuración no afectan a la carga de discos duros virtuales. Sin embargo, se recomienda firmemente que los tenga configurados.

* Instale el [agente de Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Después de instalar al agente, puede habilitar extensiones de máquina virtual. Las extensiones de máquina virtual implementan la mayor parte de la funcionalidad crítica que querrá usar con las máquinas virtuales, como el restablecimiento de contraseñas, la configuración de RDP y muchas otras.
* El registro de volcado de memoria puede ser útil para solucionar problemas de bloqueo de Windows. Habilite la colección de registros de volcado de memoria:
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* Después de crea la máquina virtual en Azure, configure el archivo de paginación de tamaño definido por el sistema en la unidad D:
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Carga de una imagen de máquina virtual de Windows en Azure para implementaciones de Resource Manager](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->


