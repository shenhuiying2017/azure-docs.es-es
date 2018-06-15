---
title: Actualización del agente de Azure Backup
description: Aquí se explica por qué se debe actualizar el agente de Azure Backup y dónde se descarga la actualización.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
ms.locfileid: "29973306"
---
## <a name="upgrade-the-mars-agent"></a>Actualización del agente de MARS
Las versiones del agente de Microsoft Azure Recovery Service (MARS) inferiores a la 2.0.9083.0 tienen una dependencia en Azure Access Control Service (ACS). En 2018 Azure [dejar de utilizar Azure Access Control Service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). A partir del 19 de marzo de 2018, todas las versiones del agente MARS inferiores a la 2.0.9083.0 sufrirán errores de copia de seguridad. Para evitar o resolver dichos errores, [actualice el agente de MARS a la versión más reciente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar los servidores que requieren una actualización del agente de MARS, siga los pasos del [blog de para la actualización de agentes de Azure Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). El agente de MARS se utiliza para realizar copias de seguridad de los siguientes tipos de datos en Azure:
- Archivos 
- Datos del estado del sistema
- Copias de seguridad de System Center DPM en Azure
- Servidor de Azure Backup (MABS)
