---
title: "Incorporación o cambio de roles de administrador de Azure | Microsoft Docs"
description: "Se describe cómo agregar o cambiar el coadministrador, el administrador de servicios y el administrador de cuenta de Azure."
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: abc3d4119cfde69ed24f7390dc99e65614a7f489


---
# <a name="how-to-add-or-change-azure-administrator-roles"></a>Incorporación o cambio de roles de administrador de Azure
Hay tres tipos de roles de administrador en Microsoft Azure:

| Rol administrativo | Límite | Description |
| --- | --- | --- |
| Administrador de cuenta (AA) |1 por cuenta de Azure |Es la persona que se registró en una suscripción de Azure o que compró una, y está autorizada para tener acceso al [Centro de cuentas](https://account.windowsazure.com/Home/Index) y realizar diversas tareas de administración. Entre estas se incluyen poder crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción y cambiar el administrador de servicios. |
| Administrador de servicios (SA) |1 por cada suscripción de Azure |Este rol está autorizado a administrar servicios en el [Portal de Azure](https://portal.azure.com). De forma predeterminada, en una nueva suscripción, el administrador de cuenta es también el administrador de servicios. |
| Coadministrador (CA) en el [Portal de Azure clásico](https://manage.windowsazure.com) |200 por suscripción |Este rol tiene los mismos privilegios de acceso que el administrador de servicios, pero no puede cambiar la asociación de suscripciones a directorios de Azure. |

> [!NOTE]
> Control de acceso basado en roles de Azure Active Directory (RBAC) permite que se agreguen usuarios a varios roles. Para más información, consulte [Control de acceso basado en roles de Azure Active Directory](active-directory/role-based-access-control-configure.md).
> 
> [!NOTE]
> Si necesita más ayuda sobre cualquier aspecto de este artículo, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rápidamente el problema.
> 
> 

## <a name="how-to-add-an-admin-for-a-subscription"></a>Adición de un administrador para una suscripción
**Portal de Azure**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú Concentrador, seleccione **Suscripción** > *la suscripción a la que desea que acceda el administrador*.
   
    ![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)
3. En la hoja de la suscripción, seleccione **Configuración**> **Usuarios**.
   
    ![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. En la hoja Usuarios, seleccione **Agregar**>**Seleccionar un rol** > **Propietario**.
   
    ![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)
   
    **Nota:**
   
   * El rol de propietario tiene los mismos privilegios de acceso que el coadministrador. Este rol no tiene privilegios de acceso al [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions).
   * Los propietarios que agregó mediante [Azure Portal](https://portal.azure.com) no pueden administrar servicios en el [Portal de Azure clásico](https://manage.windowsazure.com).  
5. Escriba la dirección de correo electrónico del usuario al que desee agregar como propietario, haga clic en el usuario y luego haga clic en **Seleccionar**.
   
    ![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portal de Azure clásico**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. En el panel de navegación, seleccione **Configuración**> **Administradores**> **Agregar**. </br>
   
    ![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Escriba la dirección de correo electrónico de la persona a la que quiere agregar como coadministrador y luego seleccione la suscripción a la que quiere que el coadministrador tenga acceso.</br>
   
    ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Se puede agregar la siguiente dirección de correo electrónico como coadministrador:

* **Cuenta Microsoft** (anteriormente Windows Live Id) </br>
   Puede usar una cuenta Microsoft para iniciar sesión en todos los productos y servicios en la nube de Microsoft orientados al consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone y Xbox LIVE.
* **Organizational account**</br>
   Una cuenta de organización es una cuenta que se crea en Azure Active Directory. La dirección de la cuenta de organización se parece a esta: user@&lt; su dominio&gt;.onmicrosoft.com.

### <a name="limitations-and-restrictions"></a>Limitaciones y restricciones
* Cada suscripción está asociada a un directorio de Azure AD (conocido también como el directorio predeterminado). Para encontrar el directorio predeterminado al que está asociada la suscripción, vaya al [Portal de Azure clásico](https://manage.windowsazure.com/) y seleccione **Configuración** > **Suscripciones**. Compruebe el identificador de la suscripción para encontrar el directorio predeterminado.
* Si ha iniciado sesión con una cuenta Microsoft, solo puede agregar otras cuentas Microsoft o usuarios del directorio predeterminado como coadministrador.
* Si ha iniciado sesión con una cuenta de organización, puede agregar otras cuentas de organización de su organización como coadministrador. Por ejemplo, abby@contoso.com puede agregar bob@contoso.com como administrador de servicios o coadministrador, pero no puede agregar john@notcontoso.com a menos que john@noncontoso.com sea el usuario en el directorio predeterminado. Los usuarios que han iniciado sesión con cuentas de organización pueden continuar agregando usuarios de cuentas Microsoft como coadministrador o administrador de servicios.
* Ahora que es posible iniciar sesión en Azure con una cuenta profesional, estos son los cambios en los requisitos de la cuenta de administrador de servicios y coadministrador:
  
  | Método de inicio de sesión | ¿Desea agregar cuentas Microsoft o usuarios del directorio predeterminado como coadministrador o administrador de servicios? | ¿Desea agregar una cuenta de organización en la misma organización que el coadministrador o administrador de servicios? | ¿Desea agregar una cuenta de organización en una organización diferente a la del coadministrador o administrador de servicios? |
  | --- | --- | --- | --- |
  |  Cuenta Microsoft |Sí |No |No |
  |  Cuenta de organización |Sí |Sí |No |

## <a name="how-to-change-service-administrator-for-a-subscription"></a>Cambio del administrador de servicios de una suscripción
Solo el administrador de cuenta puede cambiar el administrador de servicios de una suscripción.

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de cuenta.
2. Seleccione la suscripción que desea cambiar.
3. En la parte derecha, haga clic en **Editar detalles de suscripción** . </br>
   
    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. En el cuadro **ADMINISTRADOR DE SERVICIOS** , escriba la dirección de correo electrónico del nuevo administrador de servicios. </br>
   
    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="how-to-change-the-account-administrator"></a>Cambio del administrador de cuenta
Para transferir la propiedad de la cuenta de Azure a otra cuenta, vea [Transferencia de la propiedad de una suscripción de Azure](billing-subscription-transfer.md).

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory/active-directory-understanding-resource-access.md)
* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)
* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asignación de roles de administrador en Azure Active Directory](active-directory/active-directory-assign-admin-roles.md)

> [!NOTE]
> Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
> 
> 




<!--HONumber=Nov16_HO3-->


