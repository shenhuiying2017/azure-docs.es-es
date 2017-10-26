---
title: Tipos de cuota en Azure Stack | Microsoft Docs
description: Repase los diferentes tipos de cuota disponibles para los servicios y recursos de Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: erikje
ms.openlocfilehash: d9bb048ece32bf5b34e05d7459488aa0f24d0d44
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="quota-types-in-azure-stack"></a>Tipos de cuota en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Las [cuotas](azure-stack-plan-offer-quota-overview.md#plans) definen los límites de recursos que puede aprovisionar o consumir una suscripción de usuario. Por ejemplo, una cuota podría permitir que un usuario creara hasta cinco máquinas virtuales. Cada recurso puede tener sus propios tipos de cuotas.

## <a name="compute-quota-types"></a>Tipos de cuota de proceso
| **Tipo** | **Valor predeterminado** | **Descripción** |
| --- | --- | --- |
| Número máximo de máquinas virtuales | 20 | | El número máximo de máquinas virtuales que puede crear una suscripción en esta ubicación. |
| Número máximo de núcleos de máquinas virtuales | 50 | El número máximo de núcleos que puede crear una suscripción en esta ubicación (por ejemplo, una máquina virtual A3 tiene cuatro núcleos). |
| Número máximo de conjuntos de disponibilidad | 10 | El número máximo de conjuntos de disponibilidad que se pueden crear en esta ubicación. |
| Número máximo de conjuntos de escalado de máquinas virtuales | 20 | | El número máximo de conjuntos de escalado de máquinas virtuales que se pueden crear en esta ubicación. |

> [!NOTE]
> Las cuotas de proceso no se aplican a esta versión preliminar técnica.
> 
> 

## <a name="storage-quota-types"></a>Tipos de cuotas de almacenamiento
| **Elemento** | **Valor predeterminado** | **Descripción** |
| --- | --- | --- |
| Capacidad máxima (GB) |500 |Capacidad de almacenamiento total que puede consumir una suscripción en esta ubicación. |
| Número total de cuentas de almacenamiento |20 | |El número máximo de cuentas de almacenamiento que puede crear una suscripción en esta ubicación. |

## <a name="network-quota-types"></a>Tipos de cuota de red
| **Elemento** | **Valor predeterminado** | **Descripción** |
| --- | --- | --- |
| Número máximo de direcciones IP públicas |50 |El número máximo de direcciones IP públicas que puede crear una suscripción en esta ubicación. |
| Número máximo de redes virtuales |50 |El número máximo de redes virtuales que puede crear una suscripción en esta ubicación. |
| Número máximo de puertas de enlace de red virtual |1 |El número máximo de puertas de enlace de red virtual (puertas de enlace de VPN) que puede crear una suscripción en esta ubicación. |
| Número máximo de conexiones de red |2 |El número máximo de conexiones de red (punto a punto o sitio a sitio) que puede crear una suscripción en todas las puertas de enlace de red virtual de esta ubicación. |
| Número máximo de equilibradores de carga |50 |El número máximo de equilibradores de carga que puede crear una suscripción en esta ubicación. |
| Nº máx. NIC |100 |El número máximo de interfaces de red que puede crear una suscripción en esta ubicación. |
| Número máximo de grupos de seguridad de red |50 |El número máximo de grupos de seguridad de red que puede crear una suscripción en esta ubicación. |

## <a name="view-an-existing-quota"></a>Visualización de una cuota existente
1. Haga clic en **Más servicios** > **Proveedores de recursos**.
2. Seleccione el servicio con la cuota que desea ver.
3. Haga clic en **Cuotas** y seleccione la cuota que desea ver.

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de planes, ofertas y cuotas.](azure-stack-plan-offer-quota-overview.md)

[Crear cuotas durante la creación de un plan.](azure-stack-create-plan.md)
