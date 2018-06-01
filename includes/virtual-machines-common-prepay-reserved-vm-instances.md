---
ms.topic: include
ms.openlocfilehash: 8e710bebf979b60f61552593ae550e95a8340d2b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307573"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Pago por adelantado de máquinas virtuales con Azure Reserved VM Instances

Pague las máquinas virtuales por adelantado y ahorre dinero con Azure Reserved Virtual Machine (VM) Instances. Para más información, consulte las [ofertas de Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Azure Reserved Instances se puede comprar en [Azure Portal](https://portal.azure.com). Para comprar una instancia reservada de máquina virtual:
-   Debe tener un rol de propietario al menos en suscripción Enterprise o de Pago por uso.
-   En el caso de las suscripciones Enterprise, las compras de instancias reservadas de máquina virtual deben habilitarse en el [portal de EA](https://ea.azure.com).
-   En el caso del programa del Proveedor de soluciones en la nube (CSP) los únicos que pueden comprarlas son los agentes de administración o de ventas.

## <a name="buy-a-reserved-instance"></a>Comprar una instancia reservada de máquina virtual
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Reservations**.
3. Seleccione **Agregar** para comprar una nueva instancia reservada de máquina virtual.
4. Rellene todos los campos obligatorios. Las instancias de máquina virtual en ejecución que coinciden con los atributos seleccionados cumplen los requisitos para obtener el descuento de las instancias reservadas de máquina virtual. El número real de las instancias de máquina virtual que obtienen el descuento depende el ámbito y la cantidad seleccionada.

    | Campo      | Descripción|
    |:------------|:--------------|
    |Nombre        |El nombre de esta instancia reservada de máquina virtual.| 
    |Suscripción|Suscripción que se usa para pagar la instancia reservada de máquina virtual. Los costos anticipados de la instancia reservada de máquina virtual se cobran en el método de pago de la suscripción. El tipo de suscripción debe ser un contrato Enterprise (número de la oferta: MS-AZR-0017P) o de Pago por uso (número de la oferta: MS-AZR-0003P). Para una suscripción Enterprise, los cargos se deducen del saldo de compromiso monetario de la inscripción o se cobran como uso por encima del límite. Para la suscripción Pago por uso, los cargos se cobran en el método de pago de tarjeta de crédito o factura de la suscripción.|    
    |Scope       |El ámbito de la instancia reservada de máquina virtual puede cubrir una o varias suscripciones (ámbito compartido). Si selecciona: <ul><li>Suscripción individual: el descuento de la instancia reservada de máquina virtual se aplica a las máquinas virtuales de esta suscripción. </li><li>Compartida: el descuento de la instancia reservada de máquina virtual se aplica a las máquinas virtuales que se ejecuten en cualquiera de las suscripciones del contexto de facturación. Para los clientes de Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones (excepto las suscripciones de desarrollo y pruebas) dentro de la inscripción. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.</li></ul>|
    |Ubicación    |La región de Azure que cubre la instancia reservada de máquina virtual.|    
    |Tamaño de VM     |Tamaño de las instancias de máquina virtual.|
    |Término        |Un año o tres años.|
    |Cantidad    |El número de instancias que se compran en la instancia reservada de máquina virtual. La cantidad es el número de instancias de máquina virtual en ejecución a las que se aplica el descuento de facturación. Por ejemplo, si tiene 10 máquinas virtuales Standard_D2 en la región Este de EE. UU., debería especificar 10 como cantidad para maximizar el beneficio de todas las máquinas en ejecución. |
5. El costo de la instancia reservada de máquina virtual se puede ver al hacer clic en **Calcular costo**.

    ![Captura de pantalla antes de enviar la compra de la instancia reservada de máquina virtual](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Seleccione **Comprar**.
7. Haga clic en **Ver esta reserva** para conocer el estado de la compra.

    ![Captura de pantalla después de enviar la compra de la instancia reservada de máquina virtual](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Pasos siguientes 
El descuento de la instancia reservada de máquina virtual se aplica automáticamente al número de máquinas virtuales en ejecución que coinciden con el ámbito y los atributos de la instancia reservada de máquina virtual. Dicho ámbito se puede actualizar desde [Azure Portal](https://portal.azure.com), PowerShell, la CLI o la API. 

Para aprender a administrar una instancia reservada de máquina virtual, consulte [Administración de instancias reservadas de máquina virtual](../articles/billing/billing-manage-reserved-vm-instance.md).

Para más información acerca de las instancias reservadas de Azure, consulte los siguientes artículos:

- [Ahorro en máquinas virtuales con las instancias reservadas de máquina virtual](../articles/billing/billing-save-compute-costs-reservations.md)
- [Administración de instancias reservadas de máquina virtual](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Información sobre cómo se aplica el descuento de instancia reservada de máquina virtual](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Información sobre el uso de instancias reservadas para suscripciones de pago por uso](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Interpretación del uso de instancias reservadas para la inscripción Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Costos de software de Windows no incluidos con las instancias reservadas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Instancias reservadas en el programa del Proveedor de soluciones en la nube (CSP) del centro de asociados](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.