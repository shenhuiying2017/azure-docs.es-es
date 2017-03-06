---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>Instalación de Mobility Service
El primer paso para habilitar la protección de las máquinas virtuales y los servidores físicos es instalar el servicio de movilidad. Puede hacerlo de dos maneras:

* **Procesar inserción de servidor**: al habilitar la replicación en una máquina, inserte e instale el componente Mobility Service desde el servidor de procesos. 
*Tenga en cuenta* que la instalación de inserción no se producirá si las máquinas ya ejecutan una versión actualizada del componente.
* **Inserción empresarial**: instale automáticamente el componente con el proceso de inserción empresarial, como WSUS, System Center Configuration Manager o la [configuración del estado deseado y de Azure Automation](site-recovery-automate-mobility-service-install.md). Configure el servidor de configuración antes de realizar esta tarea.
* **Instalación manual**: instale el componente manualmente en cada máquina que desea replicar. Configure el servidor de configuración antes de realizar esta tarea.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparación de la inserción automática en máquinas Windows
A continuación, le mostramos cómo preparar las máquinas Windows para que el servidor de procesos pueda instalar automáticamente el servicio de movilidad.

1. Cree una cuenta que el servidor de procesos pueda utilizar para tener acceso a la máquina. La cuenta debe tener privilegios de administrador (local o dominio) y solo se utiliza para la instalación de inserción.

   > [!NOTE]
   > Si no utiliza una cuenta de dominio, deberá deshabilitar el control de acceso de usuario remoto en la máquina local. Para ello, en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System del registro, agregue la entrada LocalAccountTokenFilterPolicy DWORD con un valor de 1. Para agregar la entrada del Registro desde una CLI, escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. En el Firewall de Windows de la máquina que desea proteger, seleccione **Permitir una aplicación o una característica a través de Firewall**. Habilite **Compartir archivos e impresoras** e **Instrumental de administración de Windows**. Para las máquinas que pertenecen a un dominio, puede configurar el firewall con un GPO.

   ![Configuración de firewall](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Agregue la cuenta que creó:

   * Abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\home\svsystems\bin.
   * En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
   * Agregue la cuenta que creó. Después de agregar la cuenta, tiene que proporcionar las credenciales para habilitar la replicación de una máquina.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparación para la inserción automática en los servidores Linux
1. Asegúrese de que la máquina con Linux que quiere proteger sea compatible, tal como se describe en los [requisitos previos de equipos protegidos](#protected-machine-prerequisites). Asegúrese de que haya conectividad de red entre la máquina de Linux y el servidor de procesos.
2. Cree una cuenta que el servidor de procesos pueda utilizar para tener acceso a la máquina. La cuenta debe ser un usuario raíz en el servidor de origen de Linux y solo se utiliza para la instalación de inserción.

   * Abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\home\svsystems\bin.
   * En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
   * Agregue la cuenta que creó. Después de agregar la cuenta, tiene que proporcionar las credenciales para habilitar la replicación de una máquina.
3. Compruebe que el archivo /etc/hosts del servidor Linux de origen contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
4. Instale los paquetes openssh, openssh-server, openssl más recientes en la máquina que desea replicar.
5. Asegúrese de que SSH está habilitado y ejecutándose en el puerto 22.
6. Habilite la autenticación de la contraseña y del subsistema SFTP en el archivo sshd_config:

   * Inicie sesión como root.
   * En el archivo /etc/ssh/sshd_config, encuentre la línea que comienza con **PasswordAuthentication**.
   * Quite la marca de comentario de la línea y cambie el valor de **no** a **yes**.
   * Busque la línea que comienza por **Subsystem** y quite la marca de comentario.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Instalación manual de Mobility Service
Los instaladores están disponibles en el servidor de configuración de **C:\Archivos de programa (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Sistema operativo de origen | Archivo de instalación del servicio de movilidad |
| --- | --- |
| Windows Server&64; (solo&64; bits) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (solo 64 bits) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Instalación de Mobility Service en un servidor de Windows Server
1. Descargue y ejecute el instalador pertinente.
2. En **Antes de comenzar**, seleccione **Mobility Service**.

    ![Mobility Service](./media/site-recovery-vmware-to-azure/mobility3.png)
3. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de configuración y la frase de contraseña que se generó al ejecutar la instalación unificada. Puede recuperar la frase de contraseña mediante la ejecución de **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v** en el servidor de configuración.

    ![Mobility Service](./media/site-recovery-vmware-to-azure/mobility6.png)
4. En **Ubicación de instalación**, deje la configuración predeterminada y haga clic en **Siguiente** para comenzar la instalación.
5. En **Progreso de la instalación**, supervise la instalación y reinicie la máquina, si así se le solicita. Después de instalar el servicio, la actualización del estado en el portal puede tardar unos 15 minutos.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Instalación de Mobility Service en un servidor de Windows Server mediante el símbolo del sistema
1. Copie el instalador en una carpeta local (por ejemplo, C:\Temp) del servidor que desea proteger. El instalador puede encontrarse en el servidor de configuración, en **[ubicación de instalación]\home\svsystems\pushinstallsvc\repository**. El paquete para sistemas operativos Windows tiene un nombre similar a Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe.
2. Cambie el nombre de este archivo a MobilitySvcInstaller.exe.
3. Ejecute el comando siguiente para extraer el instalador MSI:

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "dirección IP del servidor de configuración" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Sintaxis de línea de comandos completa
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parámetros**

* **/Role:** obligatorio. Especifica si se debe instalar el servicio de movilidad. Valores de entrada: Agent|MasterTarget.
* **/InstallLocation:** obligatorio. Especifica dónde instalar el servicio.
* **/PassphraseFilePath:** obligatorio. Frase de contraseña del servidor de configuración.
* **/LogFilePath:** obligatorio. Ubicación donde se deben crear los archivos de registro de instalación.

#### <a name="uninstall-the-mobility-service-manually"></a>Desinstalación manual de Mobility Service
Es posible desinstalar Mobility Service mediante la opción Agregar o quitar programas del Panel de control o esta instrucción de la línea de comandos: MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Instalación de Mobility Service en un servidor Linux
1. Copie el archivo tar correspondiente, según la tabla anterior, en la máquina Linux que desea replicar.
2. Abra un programa de shell y extraiga el archivo TAR comprimido en una ruta de acceso local mediante la ejecución de: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Cree un archivo passphrase.txt en el directorio local en el que extrajo el contenido del archivo tar. Para ello, copie la frase de contraseña de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase en el servidor de configuración y guárdela en el archivo passphrase.txt mediante la ejecución de *`echo <passphrase> >passphrase.txt`* en el shell.
4. Instale Mobility Service; para ello, ejecute *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique la dirección IP interna del servidor de configuración y asegúrese de que el puerto 443 está seleccionado. Después de instalar el servicio, la actualización del estado en el portal puede tardar unos 15 minutos.

**También puede realizar la instalación desde la línea de comandos**:

Copie la frase de contraseña desde C:\Archivos de programa (x86)\InMage Systems\private\connection en el servidor de configuración y guárdela como "passphrase.txt" en el servidor de configuración. Luego, ejecute estos comandos. En nuestro ejemplo, la dirección IP del servidor de configuración es 104.40.75.37 y el puerto HTTPS debe ser 443:


Para instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar en el servidor de destino maestro:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




