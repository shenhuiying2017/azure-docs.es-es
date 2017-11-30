---
title: "Administración de instancias reservadas de máquina virtual de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo cambiar el ámbito de la suscripción y administrar el acceso para instancias reservadas de máquina virtual de Azure."
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
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: e23eea52ff5d27beacf938a1ef153172e24f1aee
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="manage-reserved-virtual-machine-instances"></a>Administración de instancias reservadas de máquina virtual

Tras adquirir una instancia reservada de máquina virtual de Azure, puede que quiera aplicar la reserva a otra suscripción distinta de la que se especificó durante la compra. O bien, si las máquinas virtuales correspondientes se ejecutan en varias suscripciones, quizá le interese cambiar el ámbito de reserva para que sea compartido. Para obtener el máximo descuento en la reserva, asegúrese de que el número de instancias compradas coincida con los atributos y el número de máquinas virtuales que se estén ejecutando. Para obtener más información sobre instancias reservadas de máquina virtual, consulte [Ahorrar mediante el pago por adelantado de máquinas virtuales de Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Cambio del ámbito de una reserva
 El descuento de la reserva se aplica a las máquinas virtuales que se corresponden con la reserva y que se ejecutan dentro del ámbito de esta. El ámbito de una reserva puede ser una suscripción única o todas las suscripciones del contexto de facturación. Si establece que el ámbito es una suscripción única, la reserva se corresponderá con las máquinas virtuales que se ejecuten en la suscripción seleccionada. Si establece que ámbito es compartido, Azure asociará la reserva con las máquinas virtuales que se ejecuten en todas las suscripciones del contexto de facturación. El contexto de facturación depende de la suscripción que se usó para comprar la reserva. Para obtener más información, consulte [Prepago de máquinas virtuales con instancias reservadas de máquina virtual](https://go.microsoft.com/fwlink/?linkid=861721).

Para cambiar el ámbito de una reserva: 
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Más servicios** > **Reservas**.
3. Seleccione la reserva.
4. Seleccione **Configuración** > **Configuración**.
5. Cambie el ámbito. Si cambia el ámbito para que pase de ser compartido a único, solo podrá seleccionar suscripciones de las que sea el propietario. Únicamente se pueden seleccionar las suscripciones que pertenezcan al mismo contexto de facturación que la reserva. El contexto de facturación viene determinado por la suscripción que se seleccionó al adquirir la reserva. El ámbito solo se aplica a las suscripciones de las ofertas Pago por uso MS-AZR-0003P y Enterprise MS-AZR-0017P. Las suscripciones de desarrollo y pruebas de contratos Enterprise no son aptas para obtener el descuento de reserva.

## <a name="split-a-single-reservation-into-two-reservations"></a>División de una reserva única en dos
 Si se compran varias instancias, se pueden asignar algunas instancias de una reserva a distintas suscripciones. De forma predeterminada, todas las instancias (la cantidad especificada durante la compra) tienen un ámbito, que puede ser de suscripción única o compartida. Pongamos por ejemplo que se han comprado 10 máquinas virtuales D2 estándar y como ámbito se ha especificado la suscripción A. En caso de que interese, se puede establecer el ámbito de 7 instancias reservadas de máquina virtual en la suscripción A y el de las 3 restantes en la suscripción B. Dividir una reserva permite distribuir las instancias para lograr una administración más precisa del ámbito. Si quiere simplificar la asignación a las suscripciones, elija el ámbito compartido. Sin embargo, para la administración de costos o fines presupuestarios, puede asignar las cantidades a suscripciones concretas.

 Una reserva se puede dividir en dos mediante PowerShell, CLI o la API.

### <a name="split-a-reservation-by-using-powershell"></a>División de una reserva con PowerShell
1. Ejecute el comando siguiente para obtener el identificador de pedido de reserva:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Obtenga los detalles de una reserva:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Divida la reserva en dos y distribuya las instancias:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Si quiere actualizar el ámbito, ejecute el siguiente comando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Agregar o cambiar los usuarios que pueden administrar una reserva
Para delegar la administración de una reserva, agregue usuarios a roles en dicha reserva. De forma predeterminada, la persona que compró la reserva y el administrador de cuenta tienen el rol de propietario en la reserva. 

Puede administrar el acceso a las reservas de forma independiente de las suscripciones que obtienen el descuento de reserva. El hecho de que a un usuario se le concedan permisos para administrar una reserva no implica que también se le otorguen derechos para administrar la suscripción. Y si a un usuario se le conceden permisos para administrar una suscripción dentro del ámbito de la reserva, no significa que se le otorguen derechos para administrar la reserva.
 
Para delegar la administración de acceso en una reserva: 
1.  Inicie sesión en [Azure Portal](https://portal.azure.com).
2.  Seleccione **Más servicios** > **Reserva** para mostrar las reservas a las que tiene acceso.
3.  Seleccione la reserva cuyo acceso quiere delegar a otros usuarios.
4.  En el menú, seleccione **Control de acceso (IAM)**.
5.  Seleccione **Agregar** > **Rol** > **Propietario** (u otro rol si quiere conceder acceso limitado). 
6. Escriba la dirección de correo electrónico del usuario al que quiera agregar como propietario. 
7. Seleccione el usuario y, después, **Guardar**.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
