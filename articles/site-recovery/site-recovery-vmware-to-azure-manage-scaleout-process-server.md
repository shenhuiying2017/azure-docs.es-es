---
title: " Administración de un servidor de procesos de escalado horizontal en Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo configurar y administrar un servidor de procesos de escalado horizontal en Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-scale-out-process-server"></a>Administración de un servidor de procesos de escalado horizontal

El servidor de procesos de escalado horizontal actúa como coordinador de la transferencia de datos entre los servicios de Site Recovery y la infraestructura local. En este artículo se describe cómo instalar, configurar y administrar un servidor de procesos de escalado horizontal.

## <a name="prerequisites"></a>Requisitos previos
A continuación se indica la configuración mínima recomendada de hardware, software y red para instalar un servidor de procesos de escalado horizontal.

> [!NOTE]
> La [planificación de la capacidad](site-recovery-capacity-planner.md) es un paso importante para tener la seguridad de que implementa el servidor de procesos de escalado horizontal con una configuración que satisfaga sus requisitos de carga. Lea más sobre las [características de escalado de un servidor de procesos de escalado horizontal](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Descarga del software del servidor de procesos de escalado horizontal
1. Inicie sesión en Azure Portal y busque el almacén de Recovery Services.
2. Vaya a **Site Recovery Infrastructure** (Infraestructura de Site Recovery)  > **Servidores de configuración** (en For VMware & Physical Machines [Para máquinas físicas y VMware]).
3. Seleccione el servidor de configuración para profundizar en su página de detalles.
4. Haga clic en el botón **+ Servidor de procesos**.
5. En la página **Agregar servidor de procesos**, seleccione la opción **Implementar un servidor de procesos de escalado horizontal local** en la lista desplegable **Elegir dónde quiere implementar el servidor de procesos**.

  ![Página Agregar servidores](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Haga clic en el vínculo **Download the Microsoft Azure Site Recovery Unified Setup** (Descargar la instalación unificada de Microsoft Azure Site Recovery) para descargar la versión más reciente de la instalación del servidor de procesos de escalado horizontal.

  > [!WARNING]
  La versión de su servidor de procesos de escalado horizontal debe ser igual o menor que la versión del servidor de configuración que se ejecuta en su entorno. Una forma sencilla de garantizar la compatibilidad de la versión es usar los mismos bits del instalador que usó recientemente para instalar o actualizar el servidor de configuración.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Instalación y registro de un servidor de procesos de escalado horizontal desde la interfaz gráfica de usuario
Si debe escalar horizontalmente la implementación a más de 200 máquinas de origen o si la tasa de renovación diaria total supera los 2 TB, necesitará servidores de procesos adicionales para controlar el volumen del tráfico.

Consulte las [recomendaciones de tamaño para los servidores de procesos](#size-recommendations-for-the-process-server) y siga estas instrucciones para configurar el servidor de procesos. Después de configurar el servidor, debe migrar las máquinas de origen para que lo utilicen.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Instalación y registro de un servidor de procesos de escalado horizontal mediante la línea de comandos

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Ejemplo de uso
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Argumentos de línea de comandos del instalador del servidor de procesos de escalado horizontal
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Creación de un archivo de configuración de proxy
El parámetro ProxySettingsFilePath toma un archivo como entrada. Cree el archivo con el siguiente formato y páselo como parámetro de entrada ProxySettingsFilePath.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Modificación de la configuración de proxy del servidor de procesos de escalado horizontal
1. Inicie sesión en su servidor de procesos de escalado horizontal.
2. Abra una ventana de comandos de administrador de PowerShell.
3. Ejecute el siguiente comando.
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. A continuación, vaya al directorio **%PROGRAMDATA%\ASR\Agent** y ejecute el siguiente comando
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Registro repetido de un servidor de procesos de escalado horizontal
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Después abra un símbolo del sistema de administrador.
* Vaya al directorio **%PROGRAMDATA%\ASR\Agent** y ejecute el comando

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Actualización de un servidor de procesos de escalado horizontal
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Retirada de un servidor de procesos de escalado horizontal
1. Asegúrese de que:
  - El estado del servidor de configuración muestre **Conectado** en Azure Portal.
  - El servidor de procesos pueda aún comunicarse con el servidor de configuración.
2. Inicie sesión en el servidor de procesos como administrador.
3. Abra Panel de Control > Programas > Desinstalar programas.
4. Desinstale los programas en el orden siguiente:
  * Servidor de configuración/servidor de procesos de Microsoft Azure Site Recovery
  * Dependencias del servidor de configuración de Microsoft Azure Site Recovery
  * Agente de Microsoft Azure Recovery Services

Es posible que la eliminación del servidor de procesos no se refleje en Azure Portal hasta pasados 15 minutos.

  > [!NOTE]
  Si el servidor de procesos no puede comunicarse con el servidor de configuración (el estado de la conexión en el portal es desconectado), debe seguir estos pasos para purgarlo del servidor de configuración.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Anulación del registro de un servidor de procesos de escalado horizontal desconectado de un servidor de configuración

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Requisitos de tamaño para un servidor de procesos de escalado horizontal

| **Servidores de procesos adicionales** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas** |
| --- | --- | --- | --- |
|4 vCPU (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memoria |< 300 GB |250 GB o menos |Replicar 85 máquinas o menos. |
|8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memoria |600 GB |250 GB a 1 TB |Replicar entre 85 y 150 máquinas. |
|12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz) 24 GB de memoria |1 TB |1 TB a 2 TB |Replicar entre 150 y 225 máquinas. |
