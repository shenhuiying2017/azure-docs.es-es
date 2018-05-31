---
title: ¿Qué es Azure Reserved Instances? Facturación de Azure | Microsoft Docs
description: Obtenga información sobre Azure Reserved VM Instances y los precios de VM para ahorrar en los costos de máquinas virtuales y obtener el mejor precio efectivo.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 93be4bb037af400599b88bb71f34143ee65a5deb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301709"
---
# <a name="what-are-azure-reserved-vm-instances"></a>¿Qué es Azure Reserved VM Instances?
[Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances) le permite ahorrar dinero pagar por adelantado por uno o tres años de capacidad de proceso. De esta forma, obtendrá un descuento en las máquinas virtuales que use. Azure Reserved Instances puede reducir significativamente los costos de máquinas virtuales (hasta un 72 % con respecto a precios de pago por uso) con el compromiso por adelantado de uno o tres años. Las instancias reservadas ofrecen un descuento en la facturación y no afectarán al estado de tiempo de ejecución de las máquinas virtuales.

Puede comprar una instancia reservada (RI) en [Azure Portal](https://aka.ms/reservations). Para obtener más información, consulte [Pagar por adelantado máquinas virtuales con instancias reservadas de máquina virtual](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>¿Por qué debería comprar una instancia reservada?
Si tiene máquinas virtuales que se ejecuten durante largos períodos, la compra de una instancia reservada le proporcionará la opción más rentable. Por ejemplo, si ejecuta continuamente cuatro instancias de VM Estándar D2 en la región Oeste de EE. UU., sin una instancia reservada se le cobra según las tarifas de pago por uso. Si compra una instancia reservada para esas cuatro máquinas virtuales, estas disfrutarán inmediatamente de las ventajas de facturación. Ya no se aplicarán las tarifas de pago por uso. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>¿Qué cargos cubre una instancia reservada?
Una instancia reservada solo cubre los cargos de infraestructura de máquina virtual para las máquinas virtuales Windows o Linux. Una instancia reservada no cubre cargos por software adicional, administración de redes ni almacenamiento. Para máquinas virtuales de Windows, puede cubrir los costos de licencia de Windows con la [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>¿Quién puede comprar una instancia reservada?
Para poder comprar una instancia reservada, es necesario ser cliente de Azure con uno de estos tipos de suscripción:
-   Tipo de oferta de suscripción Contrato Enterprise (MS-AZR-0017P).
-   Tipo de oferta de suscripción [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Debe tener el rol "Propietario" en la suscripción para poder comprar una instancia reservada. Para comprar instancias reservadas en una inscripción de empresa, el administrador de empresa debe habilitar las compras de instancias reservadas en el portal de EA. Esta opción está habilitada de manera predeterminada.
-   Los proveedores de soluciones en la nube (CSP) asociados pueden usar Azure Portal o el [Centro de partners](https://docs.microsoft.com/partner-center/azure-reservations) para comprar instancias reservadas.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>¿Cómo se factura la compra de una instancia reservada?
La compra de una instancia reservada se cobra en el método de pago vinculado a la suscripción. Si tiene una suscripción Enterprise, el costo de la instancia reservada se deducirá del saldo del compromiso monetario. Si este no es suficiente para cubrir el costo de la instancia reservada, se le cobrará el monto restante.
Si tiene una suscripción de pago por uso, el cargo se aplicará inmediatamente en la tarjeta de crédito vinculada a su cuenta. Si se le factura por factura, verá los cargos la próxima vez que reciba una.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>¿Cómo se aplica el descuento de la instancia reservada?
El descuento de la instancia reservada se aplica a las máquinas virtuales que coinciden con los atributos que selecciona al comprar la instancia reservada. Estos atributos incluyen el ámbito en el que se ejecutan las máquinas virtuales. Por ejemplo, si quiere un descuento de instancia reservada para sus máquinas Estándar D2 en la región Oeste de EE. UU., seleccione la suscripción en la que se ejecutan las máquinas virtuales. Si, por el contrario, sus máquinas virtuales se ejecutan en diferentes suscripciones dentro de su cuenta o inscripción, seleccione el ámbito compartido. El ámbito compartido le permite aplicar el descuento de la instancia reservada en varias suscripciones. También puede cambiar el ámbito después de la compra de la instancia reservada. Para cambiar el ámbito, lea la documentación sobre cómo administrar instancias reservadas.

El descuento de la instancia reservada solo se aplica a máquinas virtuales asociadas a tipos de suscripción Enterprise o Pago por uso. Las máquinas virtuales que se ejecuten en una suscripción con otros tipos de oferta no recibirán el descuento por la instancia reservada. Para las inscripciones Enterprise, las suscripciones Desarrollo/pruebas de Enterprise no tienen disponibles las ventajas de instancias reservadas.

Para entender mejor la manera en que las instancias reservadas afectan a la facturación de las máquinas virtuales, consulte [Información sobre cómo se aplica el descuento de instancia reservada de máquina virtual](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>¿Qué ocurre cuando expira el plazo de la instancia reservada?
Al final del plazo de la instancia reservada, el descuento en la facturación expira y la infraestructura de máquina virtual se factura según los precios de pago por uso. Las instancias reservadas no se renuevan automáticamente. Para seguir disfrutando del descuento de facturación, debe comprar otra instancia reservada. 

## <a name="sizes-and-regional-availability"></a>Tamaños y disponibilidad regional
Las instancias reservadas están disponibles para la mayoría de los tamaños de máquina virtual, a excepción de los siguientes:
- Máquinas virtuales en versión preliminar: las series de VM o los tamaños que estén en versión preliminar no estarán disponibles para la compra de instancias reservadas.
- Nubes: la compra de instancias reservadas no está disponible en las regiones de Azure Gobierno de EE. UU., Alemania y China. 
- Cuota insuficiente: una instancia reservada cuyo ámbito sea de una sola suscripción debe tener cuota de vCPU disponible en la suscripción para la nueva instancia reservada. Por ejemplo, si la suscripción de destino tiene un límite de cuota de 10 vCPU para la serie D, no podrá comprar una instancia reservada para 11 instancias Standard_D1. La comprobación de cuotas para las instancias reservadas incluye las máquinas virtuales ya implementadas en la suscripción. Por ejemplo, si la suscripción tiene una cuota de 10 vCPU para la serie D y tiene implementadas dos instancias standard_D1, podrá comprar una instancia reservada para 10 instancias standard_D1 en esta suscripción. 
- Restricciones de capacidad: en algunas circunstancias poco frecuentes, Azure limita la compra de nuevas instancias reservadas para un subconjunto de tamaños de máquina virtual debido a que la capacidad en una región es baja.

## <a name="next-steps"></a>Pasos siguientes
Empiece a ahorrar en sus máquinas virtuales con la compra de [Azure Reserved Instance](https://go.microsoft.com/fwlink/?linkid=861721). 

Para más información acerca de las instancias reservadas, consulte los siguientes artículos:

- [Pagar por adelantado máquinas virtuales con instancias reservadas de máquina virtual](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administración de instancias reservadas de máquina virtual](billing-manage-reserved-vm-instance.md)
- [Información sobre cómo se aplica el descuento de instancia reservada de máquina virtual](billing-understand-vm-reservation-charges.md)
- [Información sobre el uso de instancias reservadas para suscripciones de pago por uso](billing-understand-reserved-instance-usage.md)
- [Interpretación del uso de instancias reservadas para la inscripción Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costos de software de Windows no incluidos con las instancias reservadas](billing-reserved-instance-windows-software-costs.md)
- [Instancias reservadas en el programa del Proveedor de soluciones en la nube (CSP) del centro de asociados](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
