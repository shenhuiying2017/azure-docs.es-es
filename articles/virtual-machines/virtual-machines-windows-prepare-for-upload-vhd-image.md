<properties
	pageTitle="Preparación de un disco duro virtual de Windows para cargarlo en Azure | Microsoft Azure"
	description="Procedimientos recomendados para preparar un disco duro virtual de Windows antes de cargarlo en Azure"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="genlin"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="genli"/>

# Preparación de un disco duro virtual de Windows para cargar en Azure
Para cargar una máquina virtual Windows local en Azure, debe preparar correctamente el disco duro virtual (VHD). Antes de cargar el disco duro virtual en Azure, se recomienda realizar una serie de pasos. La ejecución de `sysprep` es un proceso común, pero un solo paso en la generalización de una imagen. En este artículo se muestra cómo preparar un disco duro virtual de Windows para cargar en Microsoft Azure.

## Preparación del disco virtual

>[AZURE.NOTE] el reciente formato VHDX no se admite en Azure. El disco duro virtual debe tener un tamaño fijo, no dinámico. Si es necesario, las instrucciones siguientes detallan la conversión de discos VHDX o dinámicos. El tamaño máximo permitido para los discos duros virtuales es de 1023 GB.

Asegúrese de que el disco duro virtual de Windows funciona correctamente en el servidor local. Resuelva los errores dentro de la propia máquina virtual antes de intentar los procesos de conversión o carga en Azure.

Si necesita convertir el disco virtual al formato requerido para Azure, utilice uno de los métodos que se indican en las secciones siguientes.

### Conversión mediante el Administrador de Hyper-V
- Abra el Administrador de Hyper-V y seleccione el equipo local de la izquierda. En el menú superior, haga clic en **Acción** > **Editar disco**.
	- En la pantalla **Locate Virtual Hard Disk** (Localizar disco duro virtual), busque y seleccione el disco virtual.
	- Seleccione **Convertir** en la pantalla siguiente.
		- Si necesita convertir VHDX, seleccione **VHD** y haga clic en **Siguiente**.
		- Si necesita convertir el disco dinámico, seleccione **Tamaño fijo** y haga clic en **Siguiente**.
		
	- Busque y seleccione **Path for the new VHD file** (Ruta de acceso para el nuevo archivo VHD).
	- Haga clic en **Finalizar** para cerrar.

### Conversión mediante PowerShell
Puede convertir un disco virtual mediante el [cmdlet Convert-VHD de PowerShell](http://technet.microsoft.com/library/hh848454.aspx). En el ejemplo siguiente, vamos a convertir de VHDX a VHD y de un tipo dinámico a uno fijo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### Conversión del formato de disco VMDK de VMware
Si tiene una imagen de máquina virtual de Windows en [formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), puede convertirla a un formato VHD mediante [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consulte el blog [How to Convert a VMWare VMDK to Hyper-V VHD (Cómo convertir un VMDK de VMWare a VHD de Hyper-V)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para más información.

## Preparación de la configuración de Windows para la carga

> [AZURE.NOTE] Ejecute los siguientes comandos con [privilegios de administrador](https://technet.microsoft.com/library/cc947813.aspx).

1. Quite cualquier ruta estática persistente de la tabla de enrutamiento:

	- Para ver la tabla de enrutamiento, ejecute `route print`.
	- Compruebe las secciones **Persistence Routes** (Rutas de persistencia). Si hay una ruta persistente, use [route delete](https://technet.microsoft.com/library/cc739598.apx) para quitarla.

2. Quite al proxy WinHTTP:

	```
	netsh winhttp reset proxy
	```

3. Configure la directiva SAN de disco como [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

	```
	dispart san policy=onlineall
	```

4. Use la hora universal coordinada (UTC) para Windows y establezca el tipo de inicio del Servicio de hora de Windows (w32time) en **Automáticamente**:

	```
	REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
	sc config w32time start= auto
	```


## Configuración de los servicios de Windows
5. Asegúrese de que cada uno de los siguientes servicios de Windows esté establecido en los **valores predeterminados de Windows**. Están configurados con los valores de inicio indicados en la siguiente lista. Puede ejecutar estos comandos para restablecer la configuración de inicio:

	```
	sc config bfe start= auto

	sc config dcomlaunch start= auto

	sc config dhcp start= auto

	sc config dnscache start= auto

	sc config IKEEXT start= auto

	sc config iphlpsvc start= auto

	sc config PolicyAgent start= manual

	sc config LSM start= auto

	sc config netlogon start= manual

	sc config netman start= manual

	sc config NcaSvc start= manual

	sc config netprofm start= manual

	sc config NlaSvc start= auto

	sc config nsi start= auto

	sc config RpcSs start= auto

	sc config RpcEptMapper start= auto

	sc config termService start= manual

	sc config MpsSvc start= auto

	sc config WinHttpAutoProxySvc start= manual

	sc config LanmanWorkstation start= auto

	sc config RemoteRegistry start= auto
	```


## Configuración de Escritorio remoto
6. Si hay algún certificado autofirmado enlazado al agente de escucha del Protocolo de escritorio remoto (RDP), quítelo:

	```
	REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
	```

	Para más información sobre cómo configurar certificados para el agente de escucha de RDP, consulte [Listener Certificate Configurations in Windows Server ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/) (Configuración de certificados para el agente de escucha en Windows Server).

7. Configure los valores de [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) para el servicio RDP:

	```
	REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
	```

8. Configure el modo de autenticación para el servicio RDP:

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
	```

9. Habilite el servicio RDP agregando las siguientes subclaves del Registro:

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
	```


## Configuración de reglas de firewall de Windows
10. Permita WinRM mediante los tres perfiles de firewall (Dominio, Privado y Público) y habilite el servicio remoto de PowerShell:

	```
	Enable-PSRemoting -force
	```

11. Asegúrese de que las siguientes reglas de firewall de sistema operativo invitado estén implantadas:

	- Entrada

	```
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

	- Entrada y salida

	```
	netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

	netsh advfirewall firewall set rule group="Core Networking" new enable=yes
	```

	- Salida

	```
	netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnPHost-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD Events-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD-Out)" new enable=yes
	```


## Pasos adicionales de configuración de Windows
12. Ejecute `winmgmt /verifyrepository` para confirmar que el repositorio de Instrumentación de administración de Windows (WMI) sea coherente. Si el repositorio está dañado, consulte [esta entrada de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Asegúrese de que los valores de configuración de datos de arranque (BCD) coincidan con lo siguiente:

	```
	bcdedit /set {bootmgr} device partition=<Boot Partition>

	bcdedit /set {bootmgr} integrityservices enable

	bcdedit /set {default} device partition=<OS Partition>

	bcdedit /set {default} integrityservices enable

	bcdedit /set {default} recoveryenabled Off

	bcdedit /set {default} osdevice partition=<OS Partition>

	bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
	```

14. Quite los filtros adicionales de la Interfaz de controlador de transporte, como el software que analiza los paquetes TCP.
15. Para asegurarse de que el disco sea correcto y coherente, ejecute el comando `CHKDSK /f`.
16.	Desinstale todos los demás controladores y software de terceros.
17. Asegúrese de que una aplicación de terceros no está utilizando el puerto 3389. Este puerto se usa para el servicio RDP en Azure.
18.	Si el disco duro virtual de Windows que quiere cargar es un controlador de dominio, siga [estos pasos adicionales](https://support.microsoft.com/kb/2904015) para preparar el disco.
19.	Reinicie la máquina virtual para asegurarse de que Windows funciona aun correctamente y se puede conectar a él mediante RDP.
20.	Restablezca la contraseña de administrador local actual y asegúrese de que puede usar esta cuenta para iniciar sesión en Windows mediante la conexión RDP. Este permiso de acceso se controla mediante el objeto de directiva "Permitir inicio de sesión a través de Servicios de Escritorio remoto". Este objeto se encuentra en "Configuración del equipo\\Configuración de Windows\\Configuración de seguridad\\Directivas locales\\Asignación de derechos de usuario".


## Instalación de actualizaciones de Windows
22. Instale las actualizaciones más recientes de Windows. Si no es posible, asegúrese de que las siguientes actualizaciones estén instaladas:

	- [KB3137061](https://support.microsoft.com/kb/3137061) Las máquinas virtuales de Microsoft Azure no se recuperan de una interrupción de la red y se producen problemas de corrupción de datos

	- [KB3115224](https://support.microsoft.com/kb/3115224) Mejoras en la confiabilidad para las máquinas virtuales que se ejecutan en un host de Windows Server 2012 R2 o Windows Server 2012

	- [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Actualización de seguridad para Microsoft Windows para solucionar la elevación de privilegios: 8 de marzo de 2016

	- [KB3063075](https://support.microsoft.com/kb/3063075) Se registran muchos eventos con el id. 129 cuando se ejecuta una máquina virtual de Windows Server 2012 R2 en Microsoft Azure

	- [KB3137061](https://support.microsoft.com/kb/3137061) Las máquinas virtuales de Microsoft Azure no se recuperan de una interrupción de la red y se producen problemas de corrupción de datos

	- [KB3114025](https://support.microsoft.com/kb/3114025) Rendimiento lento cuando tiene acceso al almacenamiento de archivos de Azure desde Windows 8.1 o Server 2012 R2

	- [KB3033930](https://support.microsoft.com/kb/3033930) La revisión aumenta el límite de 64 K en búferes RIO por proceso para servicios de Azure en Windows

	- [KB3004545](https://support.microsoft.com/kb/3004545) No se puede tener acceso a máquinas virtuales que se hospedan en servicios de hospedaje de Azure a través de una conexión VPN en Windows

	- [KB3082343](https://support.microsoft.com/kb/3082343) Se pierde la conectividad de VPN entre entornos cuando los túneles de VPN de sitio a sitio de Azure usan RRAS de Windows Server 2012 R2

	- [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Actualización de seguridad para Microsoft Windows para solucionar la elevación de privilegios: 8 de marzo de 2016

	- [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Descripción de la actualización de seguridad para CSRSS: 12 de abril de 2016
	- [KB2904100](https://support.microsoft.com/kb/2904100) El sistema se bloquea durante la E/S de disco en Windows

23. Si quiere crear una imagen para implementar varias máquinas a partir de ella, debe generalizar la imagen mediante la ejecución de `sysprep` antes de cargar el disco duro virtual en Azure. Para más información sobre cómo crear una imagen generalizada, consulte los artículos siguientes:

	- [Creación de una imagen de máquina virtual a partir de una máquina virtual de Azure existente mediante el modelo de implementación de Resource Manager](virtual-machines-windows-capture-image.md)
	- [Creación de una imagen de máquina virtual a partir de una máquina virtual de Azure existente mediante el modelo de implementación clásica](virtual-machines-windows-classic-capture-image.md)


## Configuraciones adicionales sugeridas

Los siguientes valores de configuración no afectan a la carga de discos duros virtuales. Sin embargo, se recomienda firmemente que los tenga configurados.

- Instale el [agente de Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Después de instalar al agente, puede habilitar extensiones de máquina virtual. Las extensiones de máquina virtual implementan la mayor parte de la funcionalidad crítica que querrá usar con las máquinas virtuales, como el restablecimiento de contraseñas, la configuración de RDP y muchas otras.

- El registro de volcado de memoria puede ser útil para solucionar problemas de bloqueo de Windows. Habilite la colección de registros de volcado de memoria:

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

	sc config wer start= auto
	```

- Después de crea la máquina virtual en Azure, configure el archivo de paginación de tamaño definido por el sistema en la unidad D:

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
	```


## Pasos siguientes

- [Carga de una imagen de máquina virtual de Windows en Azure para implementaciones de Resource Manager](virtual-machines-windows-upload-image.md)

<!---HONumber=AcomDC_0817_2016-->