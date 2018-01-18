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
ms.date: 01/04/2018
ms.author: genli
ms.openlocfilehash: dc09f29fec78d408e1560bfa0a943f16ab50c760
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>Agregar o cambiar los administradores de la suscripción de Azure

Los administradores de la suscripción de Azure clásico y [Control de acceso basado en rol (RBAC)](../active-directory/role-based-access-control-what-is.md) de Azure son dos sistemas para administrar el acceso a Azure Resources:

* Los roles de administrador clásico de las suscripciones ofrecen administración de acceso básico e incluyen administrador de cuenta, administrador de servicio y coadministradores.
    * Cuando se registra para una nueva suscripción de Azure, de forma predeterminada su cuenta se establece como administrador de cuenta y como administrador de servicio.
    * Se pueden agregar coadministradores después de la suscripción.
* RBAC es un sistema más reciente que ofrece administración de acceso específico con muchos roles integrados, flexibilidad de ámbito y roles personalizados.
    * Sin embargo, los usuarios que solo cuentan con roles de RBAC y ningún rol de administrador de suscripciones clásico no pueden administrar las implementaciones de Azure clásico.

Para asegurarse de tener un mejor control y para simplificar la administración de acceso, se recomienda utilizar RBAC para todas las necesidades de administración de acceso. Si es posible, se recomienda que vuelva a configurar las directivas de acceso existentes mediante RBAC. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Adición de un administrador de propietario RBAC para una suscripción en Azure Portal 

Para agregar a alguien como administrador para la administración del servicio de suscripción de Azure, se le debe asignar un rol de propietario de RBAC para la suscripción. El rol de propietario puede administrar los recursos de la suscripción que ha asignado y no tiene privilegios de acceso a otras suscripciones.

1. Consulte [**Suscripciones** en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Seleccione la suscripción a la que quiere dar acceso.
3. Seleccione **Control de acceso (IAM)** en el menú.
4. En el cuadro **Rol**, seleccione **Propietario**. 
5. En el cuadro **Asignar acceso a**, seleccione **Aplicación, grupo o usuario de Azure AD**. 
6. En el cuadro **Seleccionar**, escriba la dirección de correo electrónico del usuario al que quiera agregar como propietario. Seleccione el usuario y, después, **Guardar**.

    ![Captura de pantalla que muestra el rol de propietario seleccionado](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Así dará al usuario acceso completo a todos los recursos, incluido el derecho a delegar este acceso a otros. Para proporcionar acceso en un ámbito diferente, como un grupo de recursos, consulte el menú de administración de identidad y acceso para ese ámbito. 

## <a name="add-or-change-co-administrator"></a>Adición o cambio de coadministrador

Solo se puede agregar un propietario como coadministrador. No se pueden agregar otros usuarios, con roles como colaborador y lector, como coadministradores.

> [!TIP]
> Si el usuario necesita administrar las implementaciones de Azure clásico, tan solo debe agregar la cuenta "Propietario" como coadministrador. Se recomienda utilizar RBAC para cualquier otro fin.

1. Si no lo ha hecho ya, agregue a alguien como propietario siguiendo las instrucciones anteriores.
2. **Haga clic con el botón derecho** en el usuario de propietario que acaba de agregar y seleccione **Agregar como coadministrador**. Si no ve la opción **Agregar como coadministrador**, actualice la página o pruebe con otro explorador de Internet. 

    ![Captura de pantalla donde se agrega el coadministrador](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Para quitar el permiso de coadministrador, **haga clic con el botón derecho** en el usuario "Coadministrador" y, luego, seleccione **Quitar coadministrador**.

    ![Captura de pantalla donde se quita el coadministrador](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Cambio del administrador de servicios de una suscripción de Azure

Solo el administrador de cuenta puede cambiar el administrador de servicios de una suscripción. De forma predeterminada, al registrarse, el administrador de servicios es también el administrador de cuenta. Si el administrador de servicio se cambia a otro usuario, el administrador de cuenta pierde el acceso a Azure Portal. Sin embargo, el administrador de cuenta siempre puede usar el Centro de cuentas para cambiar el administrador de servicio de nuevo a sí mismo.

1. Asegúrese de que el escenario sea compatible mediante la comprobación de los [límites para cambiar los administradores de servicios](#limits).
1. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/subscriptions) como administrador de cuenta.
1. Seleccione una suscripción.
1. En la parte derecha, seleccione **Editar detalles de suscripción**.

    ![Captura de pantalla que muestra el botón Editar suscripción en el Centro de cuentas](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. En el cuadro **ADMINISTRADOR DE SERVICIOS** , escriba la dirección de correo electrónico del nuevo administrador de servicios.

    ![Captura de pantalla que muestra el cuadro para cambiar el correo electrónico del administrador de servicios](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitaciones para cambiar los administradores de servicios

* Cada suscripción está asociada a un directorio de Azure AD. Para encontrar el directorio al que está asociada la suscripción, vaya a [**Suscripciones**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) y, a continuación, seleccione una suscripción para ver el directorio.
* Si inició sesión con una cuenta profesional o educativa, puede agregar otras cuentas de su organización como administrador de servicios. Por ejemplo, abby@contoso.com puede agregar a bob@contoso.com como administrador de servicios pero no puede agregar a john@notcontoso.com a menos que john@notcontoso.com esté presente en el directorio contoso.com. Los usuarios que iniciaron sesión con cuentas profesionales o educativas pueden continuar agregando usuarios de cuentas de Microsoft como administrador de servicios.

  | Método de inicio de sesión | ¿Agregar un usuario de cuenta Microsoft como AS? | ¿Agregar una cuenta profesional o educativa de la misma organización como AS? | ¿Agregar una cuenta profesional o educativa de otra organización como AS? |
  | --- | --- | --- | --- |
  |  Cuenta Microsoft |Sí |Sin  |Sin  |
  |  Cuenta profesional o educativa |Sí |Sí |Sin  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Cambio del administrador de cuenta de una suscripción de Azure

El administrador de cuenta es el usuario que se registró inicialmente en la suscripción de Azure y es el responsable de la suscripción como propietario de la facturación. Para cambiar el administrador de cuenta de una suscripción, consulte [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**¿No está seguro de quién es el administrador de cuenta?** Siga estos pasos:

1. Consulte [**Suscripciones** en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
1. Seleccione **Propiedades**. El administrador de cuenta de la suscripción se muestra en el cuadro **Administrador de cuenta**.  

## <a name="types-of-classic-subscription-admins"></a>Tipos de administradores clásicos de la suscripción

 Administrador de cuenta, administrador de servicio y coadministrador son las tres variantes de roles de administrador de suscripciones clásico de Azure. La cuenta que se utiliza para suscribirse a Azure se establece automáticamente como administrador de cuenta y administrador de servicio. A continuación, se pueden agregar coadministradores adicionales. La tabla siguiente describe la diferencia exacta entre estos tres roles administrativos. 

> [!TIP]
> Para un mejor control y administración de acceso específico, se recomienda usar el control de acceso basado en rol (RBAC) de Azure, que permite agregar a los usuarios a varios roles. Para más información, consulte [Control de acceso basado en rol de Azure Active Directory](../active-directory/role-based-access-control-what-is.md).

| Administrador de suscripciones clásicas | Límite | DESCRIPCIÓN |
| --- | --- | --- |
| Administrador de cuenta (AA) |1 por cuenta de Azure |Es el usuario que se registró en una suscripción de Azure y está autorizado para acceder al [Centro de cuentas](https://account.azure.com/Subscriptions) y realizar diversas tareas de administración. Entre estas se incluye poder crear nuevas suscripciones, cancelar suscripciones, cambiar los detalles de facturación de una suscripción y cambiar el administrador de servicio. Desde un punto de vista conceptual, el administrador de cuenta es el propietario de la facturación de la suscripción. En RBAC, el administrador de cuenta no está asignado a un rol.|
| Administrador de servicios (SA) |1 por cada suscripción de Azure |Este rol está autorizado a administrar servicios en el [Portal de Azure](https://portal.azure.com). De forma predeterminada, en una nueva suscripción, el administrador de cuenta es también el administrador de servicios. En RBAC, el rol de propietario se asigna al administrador de servicio en el ámbito de la suscripción.|
| Coadministrador (CA) |200 por suscripción |Este rol tiene los mismos privilegios de acceso que el administrador de servicios, pero no puede cambiar la asociación de suscripciones a directorios de Azure. En RBAC, el rol de propietario se asigna al coadministrador en el ámbito de la suscripción.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Más información sobre el control de acceso a los recursos y Active Directory

* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](../active-directory/active-directory-understanding-resource-access.md).
* Si desea más información sobre Azure Active Directory, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) y [Asignación de roles de administrador en Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
