---
title: "Administración de datos de Azure Automation | Microsoft Docs"
description: "Este artículo contiene varios temas para administrar un entorno de Azure Automation.  Actualmente incluye la retención de datos y la realización de copias de seguridad de la recuperación ante desastres en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: 2896f129-82e3-43ce-b9ee-a3860be0423a
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/02/201
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: 2afcf918ffa104bd0e13048c152e04992f55ffe1
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="managing-azure-automation-data"></a>Administración de datos de Azure Automation
Este artículo contiene varios temas para administrar un entorno de Azure Automation.

## <a name="data-retention"></a>Retención de datos
Cuando elimina un recurso en Azure Automation, se conserva durante 90 días para fines de auditoría antes de quitarlo de manera permanente.  Durante este tiempo, no es posible ver ni usar el recurso.  Esta directiva también se aplica a recursos que pertenecen a una cuenta de automatización eliminada.

Azure Automation elimina automáticamente y quita de manera permanente los trabajos que tengan más de 90 días.

La tabla siguiente resume la directiva de retención para distintos recursos.

| Datos | Directiva |
|:--- |:--- |
| Cuentas |Se quitan de manera permanente 90 días después de que un usuario elimina la cuenta. |
| Recursos |Se quitan de manera permanente 90 días después de que un usuario elimina el recurso o 90 días después de que un usuario elimina la cuenta que contiene el recurso. |
| Módulos |Se quitan de manera permanente 90 días después de que un usuario elimina el módulo o 90 días después de que un usuario elimina la cuenta que contiene el módulo. |
| Runbooks |Se quitan de manera permanente 90 días después de que un usuario elimina el recurso o 90 días después de que un usuario elimina la cuenta que contiene el recurso. |
| Trabajos |Se eliminan y quitan de manera permanente 90 días después de la última modificación. Esto puede ser después de completar, detener o suspender el trabajo. |
| Configuraciones de nodo y archivos MOF |La configuración de nodo anterior se quita de forma permanente 90 días después de que se genere una nueva configuración. |
| Nodos de DSC |Se quitan de forma permanente 90 días después de que se anule el registro del nodo de la cuenta de Automatización mediante el Portal de Azure o el cmdlet [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) de Windows PowerShell. Los nodos también se quitan permanentemente 90 días después de que el usuario elimina la cuenta que contiene el nodo. |
| Informes de nodo |Se quitan de forma permanente 90 días después de que se genere un nuevo informe para ese nodo. |

La directiva de retención se aplica a todos los usuarios y, por el momento, no se puede personalizar.

Sin embargo, si tiene que conservar los datos durante un periodo de tiempo mayor, puede reenviar los registros de trabajos del runbook a Log Analytics.  Para más información, consulte [Reenvío de datos del trabajo de Azure Automation a Log Analytics de OMS](automation-manage-send-joblogs-log-analytics.md)   

## <a name="backing-up-azure-automation"></a>Copia de seguridad de Azure Automation
Cuando se elimina una cuenta de automatización en Microsoft Azure, se eliminan todos los objetos de la cuenta, incluidos runbooks, módulos, configuraciones, trabajos y recursos. No es posible recuperar los objetos una vez eliminada la cuenta.  Puede usar la información siguiente para crear una copia de seguridad de los contenidos de la cuenta de automatización antes de eliminarla. 

### <a name="runbooks"></a>Runbooks
Puede exportar los runbooks a archivos de script con Azure Portal o el cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) en Windows PowerShell.  Es posible importar estos archivos de script a otra cuenta de automatización, tal como se describe en [Creación o importación de un runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Módulos de integración
No es posible exportar módulos de integración desde Azure Automation.  Debe asegurarse de que se encuentren disponibles fuera de la cuenta de automatización.

### <a name="assets"></a>Recursos
No es posible exportar [recursos](https://msdn.microsoft.com/library/dn939988.aspx) desde Azure Automation.  Si utiliza Azure Portal, debe anotar los detalles de las variables, las credenciales, los certificados, las conexiones y las programaciones.  Luego debe crear manualmente todos los recursos utilizados por los runbooks que importa a otra automatización.

Puede usar [cmdlets de Azure](https://msdn.microsoft.com/library/dn690262.aspx) para recuperar los detalles de recursos no cifrados y guardarlos para una referencia futura o crear recursos equivalentes en otra cuenta de automatización.

No es posible recuperar el valor de variables cifradas o del campo de contraseña de las credenciales mediante el uso de cmdlets.  Si no conoce estos valores, puede recuperarlos de un runbook con las actividades [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) y [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

No es posible exportar certificados desde Azure Automation.  Debe asegurarse de que todos los certificados estén disponibles fuera de Azure.

### <a name="dsc-configurations"></a>Configuraciones DSC
Puede exportar las configuraciones a archivos de script con Azure Portal o con el cmdlet [Export-AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) en Windows PowerShell. Estas configuraciones se pueden importar y usar en otra cuenta de automatización.

## <a name="geo-replication-in-azure-automation"></a>Replicación geográfica en Azure Automation
La replicación geográfica, una función estándar de las cuentas de Azure Automation, realiza una copia de seguridad de los datos de la cuenta en una región geográfica diferente con fines de redundancia. Puede elegir una región primaria al configurar la cuenta, y entonces se le asigna automáticamente a esta una región secundaria. Los datos secundarios, copiados de la región primaria, se actualizan continuamente en caso de pérdida de los datos.  

La siguiente tabla muestra los emparejamientos de la región primaria y secundaria.

| Principal | Secundario |
| --- | --- |
| Centro-Sur de EE. UU |Centro-Norte de EE. UU |
| Este de EE. UU. - 2 |Central EE. UU: |
| Europa occidental |Europa del Norte |
| Sudeste de Asia |Asia oriental |
| Este de Japón |Oeste de Japón |

En el improbable caso de que se pierdan datos de una región primaria, Microsoft intenta recuperarlos. Cuando no es posible recuperar los datos principales, se lleva a cabo la conmutación por error geográfica y se notificará a los clientes afectados al respecto a través de su suscripción.

