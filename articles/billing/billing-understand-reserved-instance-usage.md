---
title: "Información sobre el uso de instancias reservadas de Azure para suscripciones de pago por uso | Microsoft Docs"
description: "Obtenga información sobre cómo interpretar su uso para comprender la aplicación de instancias reservadas para suscripciones de pago por uso."
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
ms.openlocfilehash: 6982d6177ef5c94436a28cd68beb9feb5cc343e5
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Información sobre el uso de instancias reservadas para suscripciones Pago por uso

Le explicamos el uso de instancias reservadas con el valor de ReservationId de la [Página de reservas](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) y el archivo de uso del [Portal de cuentas de Azure](https://account.azure.com).


>[!NOTE]
>El contenido de este artículo no se aplica a los clientes de EA. Si es un cliente de EA, consulte [Información sobre el uso de instancias reservadas para suscripciones Pago por uso](billing-understand-reserved-instance-usage-ea.md). En este artículo también se da por hecho que la reserva está aplicada a una única suscripción. Si está aplicada a más de una suscripción, es posible que las ventajas de la reserva abarquen varios archivos CSV de uso. 

En la siguiente sección se dará por hecho que está ejecutando una máquina virtual Windows Standard_DS1_v2 en la región Este de EE. UU. y que la información de la reserva tiene un aspecto similar al de la siguiente tabla:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Cantidad |1|
|SKU | Standard_DS1_v2|
|Region | eastus |

## <a name="reservation-application"></a>Aplicación de reserva

La parte de hardware de la máquina virtual está cubierta, dado que la máquina virtual implementada coincide con los atributos de la reserva. Para ver qué software de Windows no está cubierto por la instancia reservada, vea los [costos de software de Windows de instancias reservadas de máquinas virtuales de Azure](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv"></a>Sección de instrucción del archivo CSV
En esta sección del archivo CSV se muestra el uso total de su reserva. Aplique el filtro del campo Meter Sub-category que contiene "Reservation-" y los datos aparecerán como en la siguiente captura de pantalla: ![Reserva de instrucciones directas](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png).

La línea Reservation-Base VM tiene el número total de horas cubiertas por la reserva. En esta línea se muestra 0,00 $ porque los gastos están cubiertos por la instancia reservada. En la línea Reservation-Windows Svr (1 Core) se cubre el costo del software de Windows.

### <a name="daily-usage-section-of-csv"></a>Sección de uso diario del archivo CSV
Filtre la columna Additional Info y escriba su id. de reserva. En la siguiente captura de pantalla se muestran los campos relacionados con la reserva. 

![Cargos de uso diario](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. ReservationId en el campo Additional Info es la reserva que se usó para aplicar las ventajas a la máquina virtual.
2. ConsumptionMeter es el id. de medidor de la máquina virtual.
3. La línea Reservation-Base VM de la columna Meter Sub-category representa la línea de costo de 0 $ en la sección de instrucciones. El costo de la ejecución de esta máquina virtual ya lo cubre la reserva.
4. Este es el id. de medidor de la reserva. El costo del medidor es de 0 $. Cualquier máquina virtual apta para la instancia reservada tiene este MeterId en el CSV para cubrir el costo. 
5. Standard_DS1_v2 es una máquina virtual de vCPU y se implementa sin la Ventaja híbrida de Azure. Por lo tanto, este medidor cubre el costo extra del software de Windows. Consulte los [costos de software de Windows de las instancias reservadas de máquina virtual de Azure](billing-reserved-instance-windows-software-costs.md). para buscar el medidor correspondiente a la máquina virtual de 1 núcleo de la serie D. Si se usa la Ventaja híbrida de Azure, no se aplicará este cargo adicional. 

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.