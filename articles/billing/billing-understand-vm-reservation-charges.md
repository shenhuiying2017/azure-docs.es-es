---
title: "Información sobre la aplicación del descuento de instancias reservadas de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo se aplica el descuento de instancias reservadas de máquina virtual de Azure a las máquinas virtuales en ejecución."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: d476380fa841617f7eb914167ebd7d5b8aa611c2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>Información sobre cómo se aplica el descuento de instancia reservada de máquina virtual
Después de comprar una instancia reservada de máquina virtual, el descuento de reserva se aplica automáticamente a las máquinas virtuales que coincidan con los atributos y la cantidad de la reserva. Una reserva cubre los costos de infraestructura de las máquinas virtuales. En la tabla siguiente se muestran los costos de la máquina virtual después de comprar una reserva. En cualquier caso, se le cobrará por el almacenamiento y la administración de redes según las tarifas normales.

| Tipo de máquina virtual  | Cargos de reserva |    
|-----------------------|--------------------------------------------| 
|Máquinas virtuales con Linux sin software adicional | La reserva cubre los costos de infraestructura de la máquina virtual.|
|Máquinas virtuales con Linux con cargos de software (por ejemplo, Red Hat) | La reserva cubre los costos de infraestructura. Se le cobra por el software adicional.|
|Máquinas virtuales con Windows sin software adicional |La reserva cubre los costos de infraestructura. Se le cobra por software de Windows.|
|Máquinas virtuales con Windows con software adicional (por ejemplo, SQL Server) | La reserva cubre los costos de infraestructura. Se le cobrará por software de Windows y por el software adicional.|
|Máquinas virtuales con Windows con la [Ventaja híbrida de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | La reserva cubre los costos de infraestructura. Los costos de software de Windows están cubiertos por la Ventaja híbrida de Azure. El software adicional se cobra por separado.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Aplicación del descuento de reserva en máquinas virtuales que no ejecutan Windows
 El descuento de reserva se aplica a las instancias de máquinas virtuales en ejecución en períodos de una hora. Las reservas que haya comprado se vinculan con el uso emitido por las máquinas virtuales en ejecución para aplicar el descuento de reserva. En el siguiente gráfico se muestra la aplicación de una reserva a un uso de máquina virtual facturable. La ilustración se basa en una compra de reserva y dos instancias de máquina virtual coincidentes.

![Aplicación de instancia reservada de máquina virtual](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Cualquier uso por encima de la línea de instancia reservada de máquina virtual se cobrará según las tarifas normales de pago por uso. No se le cobrará por ningún uso por debajo de la línea de instancia reservada de máquina virtual, ya que el pago se ha hecho como parte de la compra de la reserva.
2.  En la hora 1, la instancia 1 se ejecuta durante 0,75 horas y la instancia 2 se ejecuta durante 0,5 horas. El uso total de la hora 1 es de 1,25 horas. Se le cobrará según las tarifas de pago por uso para las 0,25 horas restantes.
3.  Para las horas 2 y 3, ambas instancias se ejecutaron durante 1 hora cada una. Una instancia está cubierta por la reserva, mientras que la otra se cobra según las tarifas de pago por uso.
4.  Durante la hora 4, la instancia 1 se ejecuta durante 0,5 horas y la instancia 2 se ejecuta durante 1 hora. La instancia 1 está cubierta al completo por la reserva y 0,5 horas de la instancia 2 también. Se le cobrará según la tarifa de pago por uso por las 0,5 horas restantes.

Para obtener información sobre la aplicación de las reservas en informes de uso de facturación y ver cómo hacerlo, consulte este artículo con [información sobre el uso de instancias reservadas de máquinas virtuales](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Aplicación del descuento de reserva a máquinas virtuales con Windows
Cuando ejecuta instancias de máquina virtual con Windows, la reserva se aplica para cubrir los costos de infraestructuras. La aplicación de la reserva a los costos de infraestructuras de máquinas virtuales con Windows es la misma que para las máquinas virtuales que no ejecutan Windows. Se le cobra por separado por el software de Windows según el número de vCPU. Consulte los [costos de software de Windows con reservas](https://go.microsoft.com/fwlink/?linkid=862756). Puede cubrir los costos de licencias de Windows con la [Ventaja para uso híbrido de Azure para Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
