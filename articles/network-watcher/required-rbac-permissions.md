---
title: Permisos necesarios para usar las funcionalidades de Azure Network Watcher | Microsoft Docs
description: Obtenga información acerca de los permisos de control de acceso basado en roles de Azure necesarios para trabajar con las funcionalidades de Network Watcher.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jdial
ms.openlocfilehash: 09f3a1e1d9c6796cb55ae8f0ab18bf8e1b3fa198
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077760"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Permisos de control de acceso basado en roles necesarios para usar las funcionalidades de Network Watcher

El control de acceso basado en roles (RBAC) de Azure permite asignar solo las acciones específicas a los miembros de su organización que necesitan para completar sus responsabilidades asignadas. Para usar las funcionalidades de Network Watcher, debe asignar a la cuenta con la que inicia sesión en Azure a los roles integrados de [propietario](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [colaborador](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), o [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor), o se le debe asignar un [rol personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) al que se asignan las acciones que se enumeran para cada funcionalidad de Network Watcher en las secciones siguientes. Para obtener más información sobre las funcionalidades de Network Watcher, consulte [¿Qué es Network Watcher?](network-watcher-monitoring-overview.md)

## <a name="network-watcher"></a>Network Watcher

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Obtener Network Watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Crear o actualizar Network Watcher                             |
| Microsoft.Network/networkWatchers/delete                            | Eliminar Network Watcher                                       |

## <a name="nsg-flow-logs"></a>Registros de flujo de NSG

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurar un registro de flujo                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Consultar el estado de un registro de flujo                                    |

## <a name="connection-troubleshoot"></a>Solución de problemas de conexión

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Consultar resultados de una prueba de solución de problemas de conexión                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Ejecutar una prueba de solución de problemas de conexión                             |

## <a name="connection-monitor"></a>Monitor de conexión

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Iniciar un monitor de conexión                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Detener un monitor de conexión                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Consultar un monitor de conexión                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Obtener un monitor de conexión                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Creación de un monitor de conexión                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Eliminar un monitor de conexión                                    |

## <a name="packet-capture"></a>Captura de paquetes

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Consultar el estado de una captura de paquetes                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Detención de una captura de paquetes                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Obtención de una captura de paquetes                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Crear una captura de paquetes                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Eliminación de una captura de paquetes                                        |

## <a name="ip-flow-verify"></a>Comprobación de flujo de IP

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verificar un flujo de IP                                              |

## <a name="next-hop"></a>Próximo salto

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Obtener el próximo salto de una VM                                     |

## <a name="network-security-group-view"></a>Vista de grupo de seguridad de red

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Ver grupos de seguridad                                           |

## <a name="topology"></a>Topología

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Obtener topología                                                   |

## <a name="reachability-report"></a>Informe de disponibilidad

| .                                                              | NOMBRE                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Obtener un informe de disponibilidad de Azure                               |

## <a name="additional-actions"></a>Acciones adicionales

Las funcionalidades de Network Watcher también requieren las siguientes acciones:

- Microsoft.Storage/Read
- Microsoft.Authorization/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*