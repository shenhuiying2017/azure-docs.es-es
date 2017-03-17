---
title: "Instalación de Servicio de movilidad (VMware/físico a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo instalar el agente de Servicio de movilidad en las máquinas locales para comenzar a protegerlas."
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
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1f4075d6a3ab81bdbde614bbee400bd23f6cea20
ms.openlocfilehash: 1fd481d06f355547fd15200999c4bca3a503ec31
ms.lasthandoff: 02/23/2017

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>Instalación de Servicio de movilidad (VMware/físico a Azure)
Servicio de movilidad se debe implementar en cada máquina (máquina virtual de VMware o servidor físico) que desea replicar en Azure. Captura las escrituras de datos en la máquina y los reenvía al servidor de procesos. Servicio de movilidad se puede implementar en los servidores que requieren protección según los métodos siguientes


1. [Instalación de Servicio de movilidad mediante herramientas de implementación herramientas de implementación de software como System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
2. [Instalación de Servicio de movilidad mediante Azure Automation y la configuración de estado deseado (DSC)](site-recovery-automate-mobility-service-install.md)
3. [Instalación manual de Servicio de movilidad mediante la interfaz gráfica de usuario (GUI)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [Instalación manual de Servicio de movilidad mediante la línea de comandos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [Instalación de Servicio de movilidad mediante la instalación de inserción de Azure Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)


>[!IMPORTANT]
> A partir de la versión 9.7.0.0, en las máquinas virtuales Windows, el instalador del servicio de movilidad también instalará la versión más reciente del [Agente de máquina virtual de Azure](../virtual-machines/virtual-machines-windows-extensions-features.md#azure-vm-agent). Esto garantiza que al conmutarse por error la máquina virtual en Azure se cumpla este requisito previo necesario para utilizar cualquier extensión de máquina virtual.

## <a name="prerequisites"></a>Requisitos previos
Debe cumplir con estos requisitos previos antes de comenzar la instalación manual de Servicio de movilidad en los servidores.
1. Inicie sesión en el servidor de configuración y abra un símbolo del sistema con privilegios de administrador.
2. Cambie el directorio a la carpeta Bin y cree un archivo de frase de contraseña

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. Almacene este archivo en una ubicación segura, porque se usará durante la instalación de Servicio de movilidad.
4. Los instaladores de Servicio de movilidad para todos los sistemas operativos compatibles se encuentran dentro del directorio     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>Instalador de Servicio de movilidad para la asignación de sistemas operativos

| Nombre de plantilla de archivo de instalador| Sistema operativo |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 (64 bits) SP1</br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4, 6.5, 6.6, 6.7 y 6.8 (solo de 64 bits) </br> CentOS 6.4, 6.5, 6.6 y 6.7. 6.8 (solo de&64; bits) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits)|


## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>Instalación manual de Servicio de movilidad mediante la interfaz gráfica de usuario

>[!NOTE]
> La instalación mediante la interfaz gráfica de usuario solo es compatible con los sistemas operativos de Microsoft Windows.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>Instalación manual de Servicio de movilidad mediante la línea de comandos
### <a name="command-line-based-install-on-windows-computers"></a>Instalación mediante la línea de comandos en equipos Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Instalación mediante la línea de comandos en equipos Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>Instalación de Servicio de movilidad mediante la instalación de inserción de Azure Site Recovery
Para poder realizar la instalación de inserción de Servicio de movilidad con Azure Site Recovery, debe asegurarse de cumplir con los siguientes requisitos previos en todos los equipos de destino.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>Pasos siguientes
Una vez instalado Mobility Service, puede usar el botón **+Replicar** de Azure Portal para comenzar a habilitar la protección de estas máquinas virtuales.

