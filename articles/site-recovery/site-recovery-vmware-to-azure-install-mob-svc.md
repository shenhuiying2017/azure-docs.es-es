---
title: "Instalación de Mobility Service (VMware o físico a Azure) | Microsoft Docs"
description: "Obtenga información acerca de cómo instalar el agente de Mobility Service para proteger los equipos locales."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 848284f37ae2470a169d8f8a8c9c0bb5b926abe3
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>Instalación de Mobility Service (VMware o físico a Azure)
Azure Site Recovery Mobility Service captura escrituras de datos en un equipo y, a continuación, las reenvía al servidor en proceso. Implemente Mobility Service en cualquier equipo (máquina virtual VMware o servidor físico) que quiera replicar en Azure. Puede implementar Mobility Service en los servidores que desee proteger mediante los métodos siguientes:


* [Instalación de Mobility Service mediante herramientas de implementación herramientas de implementación de software como System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
* [Instalación de Mobility Service mediante Azure Automation y la configuración de estado deseado (DSC de Automatización)](site-recovery-automate-mobility-service-install.md)
* [Instalación manual de Mobility Service mediante la interfaz gráfica de usuario (GUI)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [Instalación manual de Mobility Service mediante la línea de comandos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Instalación de Mobility Service mediante la instalación de inserción de Azure Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> A partir de la versión 9.7.0.0, en las máquinas virtuales (VM) Windows, el instalador de Mobility Service también instalará la versión más reciente del [Agente de máquina virtual de Azure](../virtual-machines/windows/extensions-features.md#azure-vm-agent). Cuando un equipo conmuta por error a Azure, el equipo cumple el requisito previo de instalación del agente para usar cualquier extensión de máquina virtual.

## <a name="prerequisites"></a>Requisitos previos
Complete estos pasos de requisitos previos antes de instalar manualmente Mobility Service en el servidor:
1. Inicie sesión en el servidor de configuración y, a continuación, abra una ventana del símbolo del sistema como administrador.
2. Cambie el directorio a la carpeta Bin y, a continuación, cree un archivo de frase de contraseña:

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Almacene el archivo de frase de contraseña en una ubicación segura. Use el archivo durante la instalación de Mobility Service.
4. Los instaladores de Mobility Service para todos los sistemas operativos compatibles están en la carpeta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Instalador de Mobility Service para la asignación de sistemas operativos

| Nombre de plantilla de archivo de instalador| Sistema operativo |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bits) </br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8 (solo 64 bits) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8 (solo 64 bits) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2 (solo 64-bit) </br> CentOS 7.0, 7.1, 7.2 (solo 64-bit)</br> CentOs 7.3 (solo en la migración) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (solo 64-bit)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (solo 64-bit)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Instalación manual de Mobility Service mediante la GUI

>[!IMPORTANT]
> Si utiliza un **servidor de configuración** para replicar **máquinas virtuales de IaaS de Azure** de una suscripción o región de Azure a otra, **use el método de instalación basado en línea de comandos**.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Instalación manual de Mobility Service mediante la línea de comandos

### <a name="command-line-installation-on-a-windows-computer"></a>Instalación de la línea de comandos en un equipo Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalación de la línea de comandos en un equipo Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Instalación de Mobility Service mediante la instalación de inserción de Azure Site Recovery
Para realizar una instalación de inserción de Mobility Service mediante el uso de Site Recovery, todos los equipos de destino deben cumplir los siguientes requisitos previos.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Después de instalar Mobility Service, en Azure Portal, seleccione el botón **Replicar** para empezar a proteger estas máquinas virtuales.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Desinstalación de Mobility Service en un equipo de Windows Server
Utilice uno de los métodos siguientes para desinstalar Mobility Service en un equipo con Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Desinstalación mediante la GUI
1. En el Panel de Control, seleccione **Programas**.
2. Seleccione **Microsoft Azure Site Recovery Mobility Service/Servidor de destino maestro** y, a continuación, haga clic en**Desinstalar**.

### <a name="uninstall-at-a-command-prompt"></a>Desinstalación en un símbolo del sistema
1. Abra una ventana de símbolo del sistema como administrador.
2. Para desinstalar Mobility Service, ejecute el siguiente comando:

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Desinstalación de Mobility Service en equipos Linux
1. En el servidor Linux, inicie sesión como un usuario **raíz**.
2. En un terminal, vaya a /user/local/ASR.
3. Para desinstalar Mobility Service, ejecute el siguiente comando:

```
uninstall.sh -Y
```

