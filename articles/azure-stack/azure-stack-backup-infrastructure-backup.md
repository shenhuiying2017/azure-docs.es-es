---
title: "Copia de seguridad y recuperación de datos para Azure Stack con el servicio Infrastructure Backup | Microsoft Docs"
description: "Puede realizar una copia de seguridad de la configuración y los datos de servicio y restaurarlos con el servicio Infrastructure Backup."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 84ce0d72ff826ecb3f5deff165db00a1e50ae89d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Copia de seguridad y recuperación de datos para Azure Stack con el servicio Infrastructure Backup

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Puede realizar una copia de seguridad de la configuración y los datos de servicio y restaurarlos con el servicio Infrastructure Backup. Cada instalación de Azure Stack contiene una instancia del servicio. Puede usar copias de seguridad creadas por el servicio para volver a implementar la nube de Azure Stack a fin de restaurar la identidad, la seguridad y los datos de Azure Resource Manager.

Puede habilitar la copia de seguridad cuando esté listo para poner la nube en producción. No habilite la copia de seguridad si tiene previsto realizar tareas de pruebas y validación durante un largo período de tiempo.

Antes de habilitar el servicio de copia de seguridad, asegúrese de que cumple con los [requisitos](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> El servicio Infrastructure Backup no incluye aplicaciones ni datos de usuario. Consulte los artículos siguientes para obtener instrucciones sobre cómo realizar copias de seguridad y restaurar proveedores de recursos y datos de usuarios asociados de [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql) y [MySQL](https://aka.ms/azure-stack-mysql).

## <a name="the-infrastructure-backup-service"></a>Servicio Infrastructure Backup

El servicio contiene las características siguientes.

| Característica                                            | DESCRIPCIÓN                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Servicios de Infrastructure Backup                     | Coordine la copia de seguridad en un subconjunto de servicios de infraestructura en Azure Stack. Si se produce un desastre, se pueden restaurar los datos como parte de la nueva implementación. |
| Compresión y cifrado de los datos exportados de copia de seguridad | Los datos de copia de seguridad se comprimen y cifran por el sistema antes de que se exporten a la ubicación de almacenamiento externo proporcionada por el administrador.                |
| Supervisión de trabajo de copia de seguridad                              | El sistema notifica cuando se produce un error en los trabajos de copia de seguridad e informa de los pasos de solución.                                                                                                |
| Experiencia de administración de copia de seguridad                       | Los proveedores de recursos de copia de seguridad admiten la habilitación de la copia de seguridad.                                                                                                                         |
| Recuperación en la nube                                     | Si se produce una pérdida de datos catastrófica, pueden utilizarse las copias de seguridad para restaurar la información básica de Azure Stack como parte de la implementación.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Comprobación de los requisitos para el servicio Infrastructure Backup

- **Ubicación de almacenamiento**  
  Necesita un recurso compartido de archivos accesible desde Azure Stack que pueda contener siete copias de seguridad. Cada copia de seguridad ocupa aproximadamente 10 GB. El recurso compartido debe ser capaz de almacenar 140 GB de copias de seguridad. Para obtener más información acerca de cómo seleccionar una ubicación de almacenamiento para el servicio Infrastructure Backup de Azure Stack, consulte [Backup Controller requirements](azure-stack-backup-reference.md#backup-controller-requirements) (Requisitos de Backup Controller).
- **Credenciales**  
  Se necesita una cuenta de usuario de dominio y credenciales, por ejemplo, puede utilizar las credenciales de administrador de Azure Stack.
- **Clave de cifrado**  
  Los archivos de copia de seguridad se cifran mediante esta clave. Asegúrese de almacenar esta clave en una ubicación segura. Una vez que configure esta clave por primera vez o la rote en el futuro, no podrá verla desde esta interfaz. Para obtener más instrucciones sobre cómo generar una clave precompartida, siga los scripts de [Habilitación de la copia de seguridad para Azure Stack con PowerShell ](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>pasos siguientes

- Consulte [Habilitación de la copia de seguridad de Azure Stack desde el portal de administración](azure-stack-backup-enable-backup-console.md).
- Consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Consulte [Recover from catastrophic data loss](azure-stack-backup-recover-data.md) (Recuperación después de una pérdida de datos grave).
