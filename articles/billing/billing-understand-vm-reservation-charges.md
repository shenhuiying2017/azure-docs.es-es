---
title: 'Información sobre el descuento de Azure Reserved Instances: Facturación de Azure | Microsoft Docs'
description: Obtenga información sobre cómo se aplica el descuento de Azure Reserved VM Instance a las máquinas virtuales en ejecución.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: a0800bafc3d6b858387e28a3b75bc7b3a6bfe6e8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301420"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Información sobre cómo se aplica el descuento de Reserved Instance
Después de comprar Azure Reserved VM Instance, el descuento de Azure Reserved Instance se aplica automáticamente a las máquinas virtuales que coincidan con los atributos y la cantidad de la instancia reservada. Una instancia reservada cubre los costos de infraestructura de las máquinas virtuales. En la tabla siguiente se muestran los costos de la máquina virtual después de comprar una instancia reservada. En cualquier caso, se le cobrará por el almacenamiento y la administración de redes según las tarifas normales.

| Tipo de máquina virtual  | Cargos con instancia reservada |    
|-----------------------|--------------------------------------------|
|Máquinas virtuales con Linux sin software adicional | La instancia reservada cubre los costos de infraestructura de las máquinas virtuales.|
|Máquinas virtuales con Linux con cargos de software (por ejemplo, Red Hat) | La instancia reservada cubre los costos de infraestructura. Se le cobra por el software adicional.|
|Máquinas virtuales con Windows sin software adicional |La instancia reservada cubre los costos de infraestructura. Se le cobra por software de Windows.|
|Máquinas virtuales con Windows con software adicional (por ejemplo, SQL Server) | La instancia reservada cubre los costos de infraestructura. Se le cobrará por software de Windows y por el software adicional.|
|Máquinas virtuales con Windows con la [Ventaja híbrida de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | La instancia reservada cubre los costos de infraestructura. Los costos de software de Windows están cubiertos por la Ventaja híbrida de Azure. El software adicional se cobra por separado.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Aplicación del descuento de instancia reservada en máquinas virtuales que no ejecutan Windows
 El descuento de instancia reservada se aplica a las instancias de máquinas virtuales en ejecución en períodos de una hora. Las instancias reservadas que haya comprado se vinculan con el uso emitido por las máquinas virtuales en ejecución para aplicar el descuento de instancia reservada. Para las máquinas virtuales que puedan no ejecutarse la hora completa, se rellenará la instancia reservada desde otras máquinas virtuales que no usen una instancia reservada, incluidas las que se ejecutan simultáneamente. Al final de la hora, se bloquea la aplicación de la instancia reservada para las máquinas virtuales de dicha hora. En el caso de que una máquina virtual no se ejecute durante una hora o que haya máquinas virtuales concurrentes dentro de la hora que no llenen la hora de la instancia reservada, la instancia reservada estará infrautilizada durante esa hora. En el siguiente gráfico se muestra la aplicación de una instancia reservada a un uso de máquina virtual facturable. La ilustración se basa en una compra de instancia reservada y dos instancias de máquina virtual coincidentes.

![Captura de pantalla de una instancia reservada aplicada y dos instancias de máquina virtual coincidentes](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Cualquier uso por encima de la línea de instancia reservada se cobrará según las tarifas normales de pago por uso. No se le cobrará por ningún uso por debajo de la línea de instancia reservada, ya que el pago se ha hecho como parte de la compra de la instancia reservada.
2.  En la hora 1, la instancia 1 se ejecuta durante 0,75 horas y la instancia 2 se ejecuta durante 0,5 horas. El uso total de la hora 1 es de 1,25 horas. Se le cobrará según las tarifas de pago por uso para las 0,25 horas restantes.
3.  Para las horas 2 y 3, ambas instancias se ejecutaron durante 1 hora cada una. Una instancia está cubierta por la instancia reservada, mientras que la otra se cobra según las tarifas de pago por uso.
4.  Durante la hora 4, la instancia 1 se ejecuta durante 0,5 horas y la instancia 2 se ejecuta durante 1 hora. La instancia 1 está cubierta al completo por la instancia reservada y 0,5 horas de la instancia 2 también. Se le cobrará según la tarifa de pago por uso por las 0,5 horas restantes.

Para obtener información sobre la aplicación de las instancias reservadas en informes de uso de facturación y ver cómo hacerlo, consulte [Interpretación del uso de instancias reservadas para la inscripción Enterprise](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Aplicación del descuento de instancia reservada en máquinas virtuales Windows
Cuando ejecuta instancias de máquina virtual con Windows, la instancia reservada se aplica para cubrir los costos de infraestructuras. La aplicación de la instancia reservada a los costos de infraestructuras de máquinas virtuales con Windows es la misma que para las máquinas virtuales que no ejecutan Windows. Se le cobra por separado por el software de Windows según el número de vCPU. Consulte [Costos de software de Windows no incluidos con las instancias reservadas](https://go.microsoft.com/fwlink/?linkid=862756). Puede cubrir los costos de licencia de Windows con la [Ventaja híbrida de Azure para Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)).

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de las instancias reservadas, consulte los siguientes artículos:

- [Ahorrar en máquinas virtuales con instancias reservadas de máquina virtual](billing-save-compute-costs-reservations.md)
- [Pagar por adelantado máquinas virtuales con instancias reservadas de máquina virtual](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administración de instancias reservadas de máquina virtual](billing-manage-reserved-vm-instance.md)
- [Información sobre cómo se aplica el descuento de instancia reservada de máquina virtual](billing-understand-vm-reservation-charges.md)
- [Información sobre el uso de instancias reservadas para suscripciones de pago por uso](billing-understand-reserved-instance-usage.md)
- [Interpretación del uso de instancias reservadas para la inscripción Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Descripción del uso de las instancias reservadas en suscripciones de CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Costos de software de Windows no incluidos con las instancias reservadas](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
