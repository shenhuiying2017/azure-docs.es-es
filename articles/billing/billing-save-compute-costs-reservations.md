---
title: "Ahorrar mediante el pago por adelantado de máquinas virtuales de Azure - Azure | Microsoft Docs"
description: "Obtenga información sobre cómo puede ahorrar costos de máquinas virtuales con las instancias reservadas de máquina virtual de Azure."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 96e9cf2fed0b22fd7aa7b9ffeab0e94738ce510d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Ahorrar en máquinas virtuales con instancias reservadas de máquina virtual 
Las instancias reservadas de máquina virtual le permiten pagar por adelantado por uno o tres años de capacidad de proceso. De esta forma, obtendrá un descuento en las máquinas virtuales que use. Reduce significativamente los costos de máquinas virtuales (hasta un 72 % con respecto a precios de pago por uso) con el compromiso por adelantado de uno o tres años. Debido a que las instancias reservadas de máquina virtual conforman un descuento en la facturación, su compra no afectará al estado de tiempo de ejecución de las máquinas virtuales.

Puede comprar instancias reservadas de máquina virtual en [Azure Portal](https://aka.ms/reservations). Para obtener más información, consulte [Pago por adelantado de máquinas virtuales para ahorrar dinero con instancias reservadas de máquina virtual](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>¿Por qué debería comprar una instancia reservada de máquina virtual?
Si tiene máquinas virtuales que se ejecuten durante largos períodos de tiempo, la compra de una instancia reservada de máquina virtual le proporcionará el mejor precio efectivo. Por ejemplo, si ejecuta continuamente cuatro instancias de Estándar D2 en la región Oeste de EE. UU., sin una reserva se le cobra según las tarifas de pago por uso. Si compra una instancia reservada de máquina virtual para esas máquinas, estas obtendrán inmediatamente las ventajas de facturación, por lo que ya no se le cobrará según las tarifas de pago por uso. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>¿Qué cargos cubre la instancia reservada de máquina virtual?
Una reserva solo cubre los cargos de infraestructura de máquina virtual para las máquinas virtuales Windows o Linux. Por lo tanto, no cubre cargos por software adicional, administración de redes ni almacenamiento. Para máquinas virtuales de Windows, puede cubrir los costos de licencia de Windows con la [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>¿Quién puede comprar una instancia reservada de máquina virtual?
Para poder comprar una instancia reservada de máquina virtual es necesario ser cliente de Azure con uno de estos tipos de suscripción:
-   Tipo de oferta de suscripción Contrato Enterprise (MS-AZR-0017P).
-   Tipo de oferta de suscripción [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P).
Debe tener el rol "Propietario" en la suscripción para poder comprar una instancia reservada. Para comprar reservas en una inscripción de empresa, el administrador debe habilitar las compras de reservas en el portal de EA. Esta opción está habilitada de manera predeterminada.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>¿Cómo se facturan las compras de instancias reservadas de máquina virtual?
La compra de la reserva se cobra en el método de pago vinculado a la suscripción. Si tiene una suscripción Enterprise, el costo de la reserva se deducirá del saldo del compromiso monetario. Si este no es suficiente para cubrir el costo de la reserva, se le cobrará el monto restante.
Si tiene una suscripción de pago por uso, el cargo se aplicará inmediatamente en la tarjeta de crédito vinculada a su cuenta. Si se le factura por factura, verá los cargos la próxima vez que reciba una.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>¿Cómo se aplica el descuento de la instancia reservada de máquina virtual?
Dicho descuento se aplica a las instancias reservadas de máquina virtual que coinciden con los atributos que selecciona al comprar la reserva. Estos atributos incluyen el ámbito en el que se ejecutan las máquinas virtuales. Por ejemplo, si quiere un descuento de instancia reservada de máquina virtual para sus máquinas Estándar D2 en la región Oeste de EE. UU., seleccione la suscripción en la que se ejecutan las máquinas virtuales. Si, por el contrario, sus máquinas virtuales se ejecutan en diferentes suscripciones dentro de su cuenta o inscripción, seleccione el ámbito compartido. El ámbito compartido le permite aplicar el descuento de la reserva en varias suscripciones.
También puede cambiar el ámbito después de la compra de la instancia reservada de máquina virtual. Para cambiar el ámbito, lea la documentación sobre cómo administrar reservas.

El descuento de reserva solo se aplica a máquinas virtuales en suscripciones con los tipos de oferta Pago por uso o Enterprise. Las máquinas virtuales que se ejecuten en una suscripción con otros tipos de oferta no recibirán el descuento por la reserva. Para las inscripciones Enterprise, las suscripciones Desarrollo/pruebas de Enterprise no tienen disponibles las ventajas de instancias reservadas.

El modo en que la reserva afecta a la facturación de máquinas virtuales se explica en el artículo de [explicación de la aplicación de las ventajas de facturación de la reserva](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>¿Qué ocurre cuando expira el plazo de reserva?
Al final del plazo de reserva, el descuento en la facturación expira y la infraestructura de máquina virtual se factura según los precios de pago por uso. Las reservas no se renuevan automáticamente. Para seguir disfrutando del descuento de facturación, debe comprar otra instancia reservada de máquina virtual. 

## <a name="sizes-and-regional-availability"></a>Tamaños y disponibilidad regional
Las reservas están disponibles para la mayoría de tamaños de máquina virtual, a excepción de los siguientes casos:
- Tamaños de máquinas virtuales en versión preliminar: los tamaños que estén en versión preliminar no estarán disponibles para la compra de instancias reservadas de máquina virtual.
- Nubes: la compra de instancias reservadas de máquina virtual no está disponible en las regiones de Azure Gobierno de EE. UU., Alemania y China. 
- Cuota insuficiente: una instancia reservada de máquina virtual cuyo ámbito sea de una sola suscripción debe tener cuota de vCPU disponible en la suscripción para la nueva instancia reservada. Por ejemplo, si la suscripción de destino tiene un límite de cuota de 10 vCPU para la familia de la serie D, no podrá comprar una instancia reservada de máquina virtual para 11 instancias Standard_D1. La comprobación de cuota para las reservas incluye las máquinas virtuales ya implementadas en la suscripción. Por ejemplo, si la suscripción tiene una cuota de 10 vCPU para la familia de serie D. Si esta suscripción tiene dos instancias standard_D1 implementadas, podrá comprar una instancia reservada de VM para 10 instancias standard_D1 en esta suscripción. 
- Restricciones de capacidad: en algunas circunstancias poco frecuentes, Azure limita la compra de nuevas reservas para un subconjunto de tamaños de máquina virtual debido a que la capacidad en una región es baja.

## <a name="next-steps"></a>Pasos siguientes
Empiece a ahorrar en sus máquinas virtuales con la compra de una [instancia reservada de máquina virtual](https://go.microsoft.com/fwlink/?linkid=861721). 

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
