---
title: "Adición o cambio de roles de administrador de suscripción de Azure | Microsoft Docs"
description: "Se describe cómo agregar o cambiar el coadministrador, el administrador de servicios y el administrador de cuenta de Azure."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/12/2017
ms.author: genli
ms.openlocfilehash: 0252a084061c89f3d0198fc9a08e8ee65581524b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios

Puede cambiar el administrador de Azure que administra su suscripción de Azure o los servicios de Azure que se usan en su suscripción. Para ver la información de facturación de Azure y administrar las suscripciones, debe iniciar sesión en el Centro de cuentas como el administrador de cuenta. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Adición de un administrador de propietario RBAC para una suscripción en Azure Portal 

Para agregar a alguien como administrador de una suscripción en Azure Portal, se recomienda otorgarle el rol de propietario [RBAC](../active-directory/role-based-access-control-configure.md). El rol de propietario puede administrar los recursos de la suscripción que ha asignado y no tiene privilegios de acceso a otras suscripciones. Los propietarios que agregue a través de [Azure Portal](https://portal.azure.com) no pueden administrar recursos en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Inicie sesión en la [vista de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción a la que desea que acceda el administrador.
1. Seleccione **Control de acceso (IAM)** en el menú.
1. Seleccione **Agregar** > **Rol** > **Propietario**. Escriba la dirección de correo electrónico del usuario al que desea agregar como propietario, seleccione el usuario y, luego, **Guardar**.

    ![Captura de pantalla que muestra el rol de propietario seleccionado](./media/billing-add-change-azure-subscription-administrator/add-role.png)

### <a name="add-or-change-co-administrator"></a>Adición o cambio de coadministrador

Solo se puede agregar un propietario como coadministrador. No se pueden agregar otros usuarios, con roles como colaborador y lector, como coadministradores.

1. Si no lo ha hecho ya, agregue a alguien como propietario siguiendo las instrucciones anteriores.
2. **Haga clic con el botón derecho** en el usuario de propietario que acaba de agregar y seleccione **Agregar como coadministrador**. Si no ve la opción **Agregar como coadministrador**, actualice la página o pruebe con otro explorador de Internet. 

     ![Captura de pantalla donde se agrega el coadministrador](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >Debe agregar la cuenta "Propietario" como coadministrador si el usuario debe administrar los servicios de Azure en el [Portal de Azure clásico](https://manage.windowsazure.com/).

    Para quitar el permiso de coadministrador, **haga clic con el botón derecho** en el usuario "Coadministrador" y, luego, seleccione **Quitar coadministrador**.

    ![Captura de pantalla donde se quita el coadministrador](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Cambio del administrador de servicios de una suscripción de Azure

Solo el administrador de cuenta puede cambiar el administrador de servicios de una suscripción. De forma predeterminada, al registrarse, el administrador de servicios es también el administrador de cuenta.

1. Asegúrese de que el escenario sea compatible mediante la comprobación de los [límites para cambiar los administradores de servicios](#limits).
1. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/subscriptions) como administrador de cuenta.
1. Seleccione una suscripción.
1. En la parte derecha, seleccione **Editar detalles de suscripción**.

    ![Captura de pantalla que muestra el botón Editar suscripción en el Centro de cuentas](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. En el cuadro **ADMINISTRADOR DE SERVICIOS** , escriba la dirección de correo electrónico del nuevo administrador de servicios.

    ![Captura de pantalla que muestra el cuadro para cambiar el correo electrónico del administrador de servicios](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitaciones para cambiar los administradores de servicios

* Cada suscripción está asociada a un directorio de Azure AD. Para encontrar el directorio al que está asociada la suscripción, vaya al [Portal de Azure clásico](https://manage.windowsazure.com/) y seleccione **Configuración** > **Suscripciones**. Compruebe el identificador de la suscripción para encontrar el directorio.
* Si inició sesión con una cuenta profesional o educativa, puede agregar otras cuentas de su organización como administrador de servicios. Por ejemplo, abby@contoso.com puede agregar a bob@contoso.com como administrador de servicios pero no puede agregar a john@notcontoso.com a menos que john@notcontoso.com esté presente en el directorio contoso.com. Los usuarios que iniciaron sesión con cuentas profesionales o educativas pueden continuar agregando usuarios de cuentas de Microsoft como administrador de servicios.

  | Método de inicio de sesión | ¿Agregar un usuario de cuenta Microsoft como AS? | ¿Agregar una cuenta profesional o educativa de la misma organización como AS? | ¿Agregar una cuenta profesional o educativa de otra organización como AS? |
  | --- | --- | --- | --- |
  |  Cuenta Microsoft |Sí |No |No |
  |  Cuenta profesional o educativa |Sí |Sí |No |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Cambio del administrador de cuenta de una suscripción de Azure

Para cambiar el administrador de cuenta de una suscripción, consulte [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**¿No está seguro de quién es el administrador de cuenta?** Siga estos pasos:

1. Inicie sesión en la [vista de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
1. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .  

## <a name="types-of-azure-admin-accounts"></a>Tipos de cuentas de administrador de Azure

 Administrador de cuenta, Administrador de servicios y Coadministrador son las tres variantes de roles de administrador de Microsoft Azure. La tabla siguiente describe la diferencia entre estos tres roles administrativos.

| Rol administrativo | Límite | Description |
| --- | --- | --- |
| Administrador de cuenta (AA) |1 por cuenta de Azure |Es la persona que se registró en una suscripción de Azure o que compró una, y está autorizada para tener acceso al [Centro de cuentas](https://account.azure.com/Subscriptions) y realizar diversas tareas de administración. Entre estas se incluyen poder crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción y cambiar el administrador de servicios. |
| Administrador de servicios (SA) |1 por cada suscripción de Azure |Este rol está autorizado a administrar servicios en el [Portal de Azure](https://portal.azure.com). De forma predeterminada, en una nueva suscripción, el administrador de cuenta es también el administrador de servicios. |
| Coadministrador (CA) en el [Portal de Azure clásico](https://manage.windowsazure.com) |200 por suscripción |Este rol tiene los mismos privilegios de acceso que el administrador de servicios, pero no puede cambiar la asociación de suscripciones a directorios de Azure. |

Control de acceso basado en roles de Azure Active Directory (RBAC) permite que se agreguen usuarios a varios roles. Para más información, consulte [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Más información sobre el control de acceso a los recursos y Active Directory

* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](../active-directory/active-directory-understanding-resource-access.md).
* Si desea más información sobre Azure Active Directory, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) y [Asignación de roles de administrador en Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
