---
title: "Adición o cambio de roles de administrador de suscripción de Azure | Microsoft Docs"
description: "Se describe cómo agregar o cambiar el coadministrador, el administrador de servicios y el administrador de cuenta de Azure."
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 6d438cde704323a07cc125eb75d08cb64b2432ec
ms.openlocfilehash: 00b71701c4c114007e2d9a6d23f20b3df5fb2534
ms.lasthandoff: 02/21/2017


---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios
Puede cambiar el administrador de Azure que administra su suscripción de Azure o los servicios de Azure que se usan en su suscripción. Para ver la información de facturación de Azure y administrar las suscripciones, debe iniciar sesión en el [Centro de cuentas](https://account.windowsazure.com/Home/Index) como el administrador de cuenta. 

## <a name="add-an-admin-for-a-subscription"></a>Adición de un administrador para una suscripción
Puede agregar un administrador de Azure en Azure Portal o en el Portal de Azure clásico.

**Azure Portal**

Para agregar a alguna persona como administrador de una suscripción en Azure Portal, debe otorgarle el rol de propietario. El rol de propietario solo puede administrar los recursos de la suscripción que se asignó. No tiene privilegios de acceso a otras suscripciones. Los propietarios que agregue a través de [Azure Portal](https://portal.azure.com) no pueden administrar recursos en el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú Concentrador, seleccione **Suscripción** > *la suscripción a la que desea que acceda el administrador*.

    ![Captura de pantalla que muestra la suscripción seleccionada](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. En la hoja de suscripción, seleccione **Access control (IAM)**> **Agregar**.

    ![Captura de pantalla que muestra el botón Agregar seleccionado](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Seleccione **Seleccionar rol** > **Propietario**.

    ![Captura de pantalla que muestra el rol de propietario seleccionado](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

5. Escriba la dirección de correo electrónico del usuario al que desee agregar como propietario, haga clic en el usuario y luego haga clic en **Seleccionar**.

    ![Captura de pantalla que muestra un correo electrónico del usuario agregado y seleccionado](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portal de Azure clásico**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. En el panel de navegación, seleccione **Configuración**> **Administradores**> **Agregar**. </br>

    ![Captura de pantalla que muestra cómo llegar al botón Agregar](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Escriba la dirección de correo electrónico de la persona a la que quiere agregar como coadministrador y luego seleccione la suscripción a la que quiere que el coadministrador tenga acceso.</br>

    ![Captura de pantalla que muestra una suscripción seleccionada ](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Se puede agregar la siguiente dirección de correo electrónico como coadministrador:

* **Cuenta Microsoft** (anteriormente Windows Live Id) </br>
  Puede usar una cuenta Microsoft para iniciar sesión en todos los productos y servicios en la nube de Microsoft orientados al consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone y Xbox LIVE.
* **Organizational account**</br>
  Una cuenta de organización es una cuenta que se crea en Azure Active Directory. La dirección de la cuenta de organización tiene este formato:

    usuario@&lt;su dominio&gt;.onmicrosoft.com



## <a name="change-service-administrator-for-a-subscription"></a>Cambio del administrador de servicios de una suscripción
Solo el administrador de cuenta puede cambiar el administrador de servicios de una suscripción.

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de cuenta.
2. Seleccione la suscripción que desea cambiar.
3. En la parte derecha, haga clic en **Editar detalles de suscripción** . </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. En el cuadro **ADMINISTRADOR DE SERVICIOS** , escriba la dirección de correo electrónico del nuevo administrador de servicios. </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="change-the-account-administrator"></a>Cambio del administrador de cuenta
Para transferir la propiedad de la cuenta de Azure a otra cuenta, vea [Transferencia de la propiedad de una suscripción de Azure](billing-subscription-transfer.md).

Se recomienda encarecidamente que no elimine ni cambie el nombre de la dirección de correo electrónico del administrador de cuenta. Puede producirse un comportamiento inesperado y no deseado en la cuenta de Azure. Es posible que no pueda iniciar sesión en Azure con esa cuenta, realizar cambios en ella ni administrar sus recursos. 

## <a name="check-the-account-administrator-of-the-subscription"></a>Comprobación del administrador de cuenta de la suscripción
Si no está seguro de quién es el administrador de cuenta de la suscripción, use los pasos siguientes para averiguarlo.

  1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
  2. En el menú de concentrador, seleccione **Suscripción**.
  3. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
  4. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .  

## <a name="types-of-azure-admin-accounts"></a>Tipos de cuentas de administrador de Azure
 Administrador de cuenta, Administrador de servicios y Coadministrador son las tres variantes de roles de administrador de Microsoft Azure. La tabla siguiente describe la diferencia entre estos tres roles administrativos.

| Rol administrativo | Límite | Description |
| --- | --- | --- |
| Administrador de cuenta (AA) |1 por cuenta de Azure |Es la persona que se registró en una suscripción de Azure o que compró una, y está autorizada para tener acceso al [Centro de cuentas](https://account.windowsazure.com/Home/Index) y realizar diversas tareas de administración. Entre estas se incluyen poder crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción y cambiar el administrador de servicios. |
| Administrador de servicios (SA) |1 por cada suscripción de Azure |Este rol está autorizado a administrar servicios en el [Portal de Azure](https://portal.azure.com). De forma predeterminada, en una nueva suscripción, el administrador de cuenta es también el administrador de servicios. |
| Coadministrador (CA) en el [Portal de Azure clásico](https://manage.windowsazure.com) |200 por suscripción |Este rol tiene los mismos privilegios de acceso que el administrador de servicios, pero no puede cambiar la asociación de suscripciones a directorios de Azure. |

Control de acceso basado en roles de Azure Active Directory (RBAC) permite que se agreguen usuarios a varios roles. Para más información, consulte [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md).

## <a name="limitations-and-restrictions-for-admin-accounts"></a>Limitaciones y restricciones de las cuentas de administrador
* Cada suscripción está asociada a un directorio de Azure AD (conocido también como el directorio predeterminado). Para encontrar el directorio predeterminado al que está asociada la suscripción, vaya al [Portal de Azure clásico](https://manage.windowsazure.com/) y seleccione **Configuración** > **Suscripciones**. Compruebe el identificador de la suscripción para encontrar el directorio predeterminado.
* Si inició sesión con una cuenta de Microsoft, solo puede agregar otras cuentas de Microsoft o usuarios del directorio predeterminado como coadministradores.
* Si inició sesión con una cuenta de organización, puede agregar otras cuentas de organización de su organización como coadministrador. Por ejemplo, abby@contoso.com puede agregar a bob@contoso.com como administrador de servicios o coadministrador, pero no puede agregar a john@notcontoso.com a menos que john@noncontoso.com se encuentre en el directorio predeterminado. Los usuarios que iniciaron sesión con cuentas de organización pueden continuar agregando usuarios de cuentas de Microsoft como coadministrador o administrador de servicios.
* Ahora que es posible iniciar sesión en Azure con una cuenta de organización, estos son los cambios en los requisitos de la cuenta de administrador de servicios y coadministrador:

  | Método de inicio de sesión | ¿Desea agregar cuentas Microsoft o usuarios del directorio predeterminado como coadministrador o administrador de servicios? | ¿Desea agregar una cuenta de organización en la misma organización que el coadministrador o administrador de servicios? | ¿Desea agregar una cuenta de organización en una organización diferente a la del coadministrador o administrador de servicios? |
  | --- | --- | --- | --- |
  |  Cuenta Microsoft |Sí |No |No |
  |  Cuenta de organización |Sí |Sí |No |

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Más información sobre el control de acceso a los recursos y Active Directory
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](../active-directory/active-directory-understanding-resource-access.md).
* Si desea más información sobre Azure Active Directory, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) y [Asignación de roles de administrador en Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

