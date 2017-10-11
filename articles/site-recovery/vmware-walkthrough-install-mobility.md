---
title: "Instalación de Mobility Service para VMware en la replicación de Azure | Microsoft Docs"
description: "En este artículo se describe cómo instalar el agente de Mobility Service para VMware en la replicación de Azure con el servicio Azure Site Recovery."
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
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>Paso 10: Instalación de Mobility Service


En este artículo se describe cómo configurar opciones de origen y destino al replicar máquinas virtuales de VMware locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Mobility Service captura las escrituras de datos en una máquina y las reenvía al servidor de procesos. Se debe instalar en cada máquina que quiera se replicar en Azure.

Puede realizar la instalación manual de Mobility Service con una instalación de inserción desde el servidor de procesos de Site Recovery cuando la replicación está habilitada o usar una herramienta de System Center Configuration Manager. Si usa la instalación de inserción, el servicio se instala en la VM cuando se habilita la replicación.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Instalación manual

1. Compruebe los [requisitos previos](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) para la instalación manual.
2. Siga [estas instrucciones](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) para la instalación manual con el portal.
3. Si prefiere realizar la instalación desde la línea de comandos, siga [estas instrucciones](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Instalación desde el servidor de procesos

Si quiere insertar la instalación de Mobility Service desde el servidor de procesos al habilitar la replicación de una máquina virtual, necesita una cuenta que el servidor de procesos pueda usar para acceder a la máquina virtual. La cuenta solo se utiliza para la instalación de inserción.

1. Debe haber [creado una cuenta](vmware-walkthrough-prepare-vmware.md) que pueda usarse para la instalación de inserción. Especifique después la cuenta que quiere usar al establecer las opciones de configuración del origen durante la implementación de Site Recovery.
2. Siga luego [estas instrucciones](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) si quiere insertar Mobility Service en máquinas virtuales que ejecutan Windows o Linux.

## <a name="other-methods"></a>Otros métodos

Obtenga más información sobre la [instalación de Mobility Service con Configuration Manager](site-recovery-install-mobility-service-using-sccm.md) o con [DSC de Azure Automation](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Pasos siguientes

Ir a [Paso 11: Habilitación de la replicación](vmware-walkthrough-enable-replication.md)
