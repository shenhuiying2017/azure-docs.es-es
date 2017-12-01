---
title: "Interpretación del uso de instancias reservadas de Azure en el nivel Enterprise | Microsoft Docs"
description: "Obtenga información sobre cómo interpretar su uso para comprender la aplicación de instancias reservadas para su inscripción Enterprise."
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
ms.openlocfilehash: 7ef601033b36ee968cb766d40a0a6b05afa9a1a4
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Interpretación del uso de instancias reservadas para la inscripción Enterprise
Le explicamos el uso de instancias reservadas con el valor de ReservationId de la [Página de reservas](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) y el archivo de uso del [portal de EA.](https://ea.azure.com) También puede ver el uso de reserva en la sección de resumen de uso del [portal de EA.](https://ea.azure.com)

>[!NOTE]
>Si compró la reserva en un contexto de facturación de Pago por uso, consulte [Understand Reserved Instance usage for your Pay-As-You-Go subscription (Interpretación del uso de instancias reservadas para su suscripción de Pago por uso).](billing-understand-reserved-instance-usage.md)

En la siguiente sección se dará por hecho que está ejecutando una máquina virtual Windows Standard_D1_v2 en la región Este de EE. UU. y que la información de la reserva tiene un aspecto similar al de la siguiente tabla:

| Campo | Valor |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Cantidad |1|
|SKU | Standard_D1|
|Region | estado |

## <a name="reservation-application"></a>Aplicación de reserva

La parte de hardware de la máquina virtual está cubierta, dado que la máquina virtual implementada coincide con los atributos de la reserva. Para ver qué software de Windows no está cubierto por la instancia reservada, vaya a los costos de software de instancias reservadas de máquinas virtuales de Azure y vea los [costos de software de Windows de instancias reservadas de máquinas virtuales de Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reservation-usage-in-csv"></a>Uso de la reserva en CSV
Puede descargar el archivo CSV de uso de EA desde el portal de EA. En el archivo CSV descargado, filtre por la columna Additional Info (Información adicional) y escriba su id. de reserva. En la siguiente captura de pantalla se muestran los campos relacionados con la reserva:

![Archivo CSV de EA para instancias reservadas](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. ReservationId en el campo Additional Info representa la reserva que se usó para aplicar las ventajas a la máquina virtual.
2. ConsumptionMeter es el valor de MeterId de la máquina virtual.
3. Se trata de ReservationMeter sin ningún costo, ya que el costo de la máquina virtual en ejecución ya se ha pagado con la reserva. 
4. Standard_D1 es una máquina virtual de vCPU y se implementa sin la Ventaja híbrida de Azure. Por lo tanto, este medidor cubre el costo extra del software de Windows. Consulte los [costos de software de Windows de las instancias reservadas de máquina virtual de Azure](billing-reserved-instance-windows-software-costs.md) para buscar el medidor correspondiente a la máquina virtual de un núcleo de la serie D. Si se usa la Ventaja híbrida de Azure, no se aplicará este cargo adicional.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Uso de reserva en la página de resumen de uso del portal de EA

El uso de instancias reservadas también aparece en la sección de resumen de uso del portal de EA: ![Resumen de uso de EA](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. No se le cobra por el componente de hardware de la máquina virtual, ya que está cubierto por la instancia reservada. 
2. Se le cobra por el software de Windows, ya que la Ventaja híbrida de Azure no se usa. 

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.