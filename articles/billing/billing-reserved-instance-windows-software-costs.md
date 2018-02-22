---
title: "Costos de software de Windows de las instancias reservadas de máquina virtual de Azure | Documentos de Microsoft"
description: "Descubra qué medidores de software de Windows no están incluidos en los costos de las instancias reservadas de máquina virtual."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: a0bb559369877e1cc5333394102bfb85d3f0bb11
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>Costos de software de Windows no incluidos con las instancias reservadas

Si no dispone de la Ventaja híbrida de Azure en las máquinas virtuales de instancias reservadas, se le cobrará por los medidores de software de Windows que se indican en la sección siguiente.

## <a name="windows-software-meters-not-included-in-reserved-instance-cost"></a>Medidores de software de Windows no incluidos en los costos de instancias reservadas

| Id. del medidor | Nombre del medidor en el archivo de uso | Usado por máquina virtual |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Instancias reservadas de ráfagas de Windows Server (1 núcleo) | Serie B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Instancias reservadas de ráfagas de Windows Server (2 núcleos) | Serie B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Instancias reservadas de ráfagas de Windows Server (4 núcleos) | Serie B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Instancias reservadas de ráfagas de Windows Server (8 núcleos) | Serie B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Instancias reservadas de Windows Server (1 núcleo) | Todos excepto serie B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Instancias reservadas de Windows Server (2 núcleos) | Todos excepto serie B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Instancias reservadas de Windows Server (4 núcleos) | Todos excepto serie B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Instancias reservadas de Windows Server (6 núcleos) | Todos excepto serie B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Instancias reservadas de Windows Server (8 núcleos) | Todos excepto serie B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Instancias reservadas de Windows Server (12 núcleos) | Todos excepto serie B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Instancias reservadas de Windows Server (16 núcleos) | Todos excepto serie B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Instancias reservadas de Windows Server (20 núcleos) | Todos excepto serie B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Instancias reservadas de Windows Server (24 núcleos) | Todos excepto serie B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Instancias reservadas de Windows Server (32 núcleos) | Todos excepto serie B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Instancias reservadas de Windows Server (40 núcleos) | Todos excepto serie B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Instancias reservadas de Windows Server (64 núcleos) | Todos excepto serie B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Instancias reservadas de Windows Server (72 núcleos) | Todos excepto serie B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Instancias reservadas de Windows Server (128 núcleos) | Todos excepto serie B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Instancias reservadas de Windows Server (256 núcleos) | Todos excepto serie B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Instancias reservadas de Windows Server (96 núcleos) | Todos excepto serie B |

El costo de cada uno de estos medidores se puede consultar a través de la API RateCard de Azure. Para obtener información sobre las tasas de un medidor de Azure, consulte [Información de precios y metadatos de los recursos usados en una suscripción de Azure](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>pasos siguientes
Para más información acerca de las instancias reservadas de máquina virtual, consulte los siguientes artículos.

- [Pagar por adelantado máquinas virtuales con instancias reservadas de máquina virtual](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administración de instancias reservadas de máquina virtual](billing-manage-reserved-vm-instance.md)
- [Ahorrar en máquinas virtuales con instancias reservadas de máquina virtual](billing-save-compute-costs-reservations.md)
- [Información sobre cómo se aplica el descuento de instancia reservada de máquina virtual](billing-understand-vm-reservation-charges.md)
- [Información sobre el uso de instancias reservadas para suscripciones de pago por uso](billing-understand-reserved-instance-usage.md)
- [Interpretación del uso de instancias reservadas para la inscripción Enterprise](billing-understand-reserved-instance-usage-ea.md)
