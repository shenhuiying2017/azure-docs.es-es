---
title: "Administrar el acceso a la facturación de Azure mediante roles | Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Administrar el acceso a la información de facturación de Azure mediante el control de acceso basado en roles

Puede conceder acceso a la información de facturación de Azure a los miembros del equipo mediante la asignación de uno de los siguientes roles de usuario a la suscripción: Cuenta de administrador, Administrador de servicios, Coadministrador, Propietario, Colaborador, Lector y Lector de facturación. Tendrán acceso a la información de facturación en [Azure Portal](https://portal.azure.com/) y pueden usar las [API de facturación](billing-usage-rate-card-overview.md) para obtener facturas (después de participar) y detalles de uso mediante programación. Para más información sobre quién puede conceder roles y qué puede hacer cada rol, vea [Roles en Azure RBAC](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a> Permitir que otros usuarios tengan acceso a las facturas

El Administrador de la cuenta debe participar en [Azure Portal](https://portal.azure.com/) para permitir el acceso a las facturas a otros usuarios y a través de la API.

1. Como administrador de la cuenta, seleccione la suscripción desde la [hoja Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) en Azure Portal.

1. Seleccione **Facturas** y, después, **Acceso a las facturas**.

    ![Captura de pantalla muestra cómo delegar acceso a las facturas](./media/billing-manage-access/AA-optin.png)

1. **Active** el acceso y luego guarde los cambios para permitir que los usuarios en roles con ámbito de suscripción descarguen la factura.

    ![Captura de pantalla muestra cómo activar o desactivar la delegación de acceso a la factura](./media/billing-manage-access/AA-optinAllow.png)

La participación permite al Administrador de servicios, Coadministrador, Propietario, Colaborador, Lector y Lector de facturación en la suscripción descargar facturas PDF en Azure Portal. Por ahora, las facturas anteriores a diciembre de 2016 solo están disponibles para el administrador de cuenta.

El administrador de la cuenta también puede configurar que las facturas se envíen por correo electrónico. Para obtener más información, vea [Obtención de la factura por correo electrónico](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Agregar usuarios al rol Lector de facturación

El rol Lector de facturación tiene acceso de solo lectura a información de facturación de suscripción en Azure Portal y no tiene acceso a los servicios, como las máquinas virtuales y las cuentas de almacenamiento. Asigne el rol Lector de facturación a otro usuario que necesite tener acceso a la información de facturación de suscripción, pero no la capacidad de administrar los servicios de Azure. Este rol es adecuado para los usuarios de una organización que solo realizan la administración financiera y de costos para las suscripciones de Azure.

1. Seleccione su suscripción en la [hoja de suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) de Azure Portal.

1. Seleccione **Control de acceso (IAM)** y haga clic en **Agregar**.

    ![Captura de pantalla que muestra IAM en la hoja de suscripción](./media/billing-manage-access/select-iam.PNG)

1. Seleccione **Lector de facturación** en la página **Seleccionar un rol**.

    ![Captura de pantalla que muestra Lector de facturación en la vista emergente](./media/billing-manage-access/select-roles.PNG)

1. Escriba el correo electrónico del usuario que quiere invitar y haga clic en **Aceptar** para enviar la invitación.

    ![Captura de pantalla que muestra el correo electrónico escrito para invitar a alguien](./media/billing-manage-access/add-user.PNG)

1. Siga las instrucciones que aparecen en el correo electrónico de invitación para iniciar sesión como un Lector de facturación.

    ![Captura de pantalla que muestra lo que puede ver el Lector de facturación en Azure Portal](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> La característica Lector de facturación está en versión preliminar y aún no admite suscripciones de Enterprise (EA) ni nubes no globales.

## <a name="adding-users-to-other-roles"></a>Agregar usuarios a los demás roles

Los usuarios de otros roles, como Propietario o Colaborador, pueden tener acceso no solo a la información de facturación, sino también a los servicios de Azure. Para administrar estos roles, vea [Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>¿Quién puede tener acceso al [Centro de cuentas](https://account.windowsazure.com)?

Solo el administrador de la cuenta puede iniciar sesión en el Centro de cuentas. El administrador de la cuenta es el propietario legal de la suscripción. De forma predeterminada, la persona que se registró o que compró la suscripción de Azure es el administrador de la cuenta, a menos que la [propiedad de la suscripción se transfiriera](billing-subscription-transfer.md) a otra persona. El Administrador de la cuenta puede crear y cancelar suscripciones, cambiar la dirección de facturación de una suscripción y administrar directivas de acceso para la suscripción.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
