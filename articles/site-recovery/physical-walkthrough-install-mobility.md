---
title: "Instalación de Mobility Service para el servidor físico para la replicación de Azure | Microsoft Docs"
description: "En este artículo se describe cómo instalar el agente de Mobility Service en servidores físicos que se replican en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-9-install-the-mobility-service"></a>Paso 9: Instalación del servicio de movilidad


En este artículo se describe cómo instalar el componente de Mobility Service cuando se replican servidores físicos de Windows/Linux locales en Azure con el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Mobility Service captura las escrituras de datos en una máquina y las reenvía al servidor de procesos. Se debe instalar en cada servidor que quiera se replicar en Azure.

Puede realizar la instalación manual de Mobility Service, realizar una instalación de inserción desde el servidor de procesos de Site Recovery cuando la replicación está habilitada o usar una herramienta como System Center Configuration Manager. Si usa la instalación de inserción, el servicio se instala en el servidor cuando se habilita la replicación.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Instalación manual

1. Compruebe los [requisitos previos](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) para la instalación manual.
2. Siga [estas instrucciones](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) para la instalación manual con el portal.
3. Si prefiere realizar la instalación desde la línea de comandos, siga [estas instrucciones](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Instalación desde el servidor de procesos

Si quiere insertar la instalación de Mobility Service desde el servidor de procesos al habilitar la replicación de una máquina, necesita una cuenta que el servidor de procesos pueda usar para acceder a la máquina. La cuenta solo se utiliza para la instalación de inserción.

1. Si no ha creado una cuenta, hágalo haciendo uso de estas instrucciones:

    - Puede usar una cuenta local o de dominio.
    - Para Windows, si no utiliza una cuenta de dominio, debe deshabilitar Access Control de usuario remoto en la máquina local. Para hacerlo, en el Registro, en **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, agregue la entrada DWORD **LocalAccountTokenFilterPolicy** con un valor de 1.
    - Si desea agregar la entrada del Registro de Windows desde una CLI, escriba:

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - Para Linux, la cuenta debe ser una raíz en el servidor Linux de origen.

2. Siga luego [estas instrucciones](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) si quiere insertar Mobility Service en máquinas virtuales que ejecutan Windows o Linux.

## <a name="other-installation-methods"></a>Otros métodos de instalación

- [Más información sobre la ](site-recovery-install-mobility-service-using-sccm.md)instalación de Mobility Service con Configuration Manager
- [Más información sobre la ](site-recovery-automate-mobility-service-install.md)instalación con DSC de Azure Automation.


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 10: Habilitación de la replicación](physical-walkthrough-enable-replication.md)
