---
title: "Adición de una suscripción de Azure existente al directorio de Azure AD | Microsoft Docs"
description: "Adición de una suscripción existente al directorio de Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e063e6a46b6b99c4bbe749347e6887a930adb882
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Asociación o adición de una suscripción de Azure a Azure Active Directory

Este artículo contiene información sobre la relación entre una suscripción de Azure y Azure Active Directory (AD) y cómo agregar una suscripción existente al directorio de Azure AD. La suscripción de Azure tiene una relación de confianza con Azure AD, lo que significa que confía en el directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero cada suscripción confía solo en un único directorio. 

Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.). Si una suscripción expira, el acceso a los otros recursos asociados a la suscripción también se detiene. Sin embargo, un directorio de Azure AD permanece en Azure y puede asociar una suscripción diferente a ese directorio y administrarlo con la nueva suscripción.

Todos los usuarios tienen un único directorio de inicio que los autentica, pero también pueden ser invitados en otros directorios. En Azure AD, puede ver los directorios de los que es miembro la cuenta de usuario o invitado.

## <a name="before-you-begin"></a>Antes de empezar

* Debe iniciar sesión con la cuenta que tenga acceso de propietario de RBAC a la suscripción.
* Debe iniciar sesión con una cuenta que exista tanto en el directorio actual con el que está asociado la suscripción como en el directorio al que desea agregarla. Para más información sobre cómo obtener acceso a otro directorio, consulte [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B](active-directory-b2b-admin-add-users.md)

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Asociación de una suscripción existente al directorio de Azure AD

1. Inicie sesión y seleccione una suscripción en la [Página de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Haga clic en **Cambiar dirección**.

    ![Captura de pantalla que muestra el botón Cambio de directorio](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Revise las advertencias. Todos los usuarios del [control de acceso basado en rol (RBAC)](role-based-access-control-configure.md) con acceso asignado y todos los administradores de suscripción pierden el acceso cuando cambia el directorio de la suscripción.
4. Seleccione un directorio.

    ![Captura de pantalla que muestra la interfaz de usuario de cambio de directorio](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Haga clic en **Cambiar**.
6. ¡Éxito! Utilice el modificador del directorio para ir al directorio nuevo. Puede tardar hasta 10 minutos para que todo se muestre correctamente.

    ![Captura de pantalla que muestra la notificación correcta de cambio de directorio](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Captura de pantalla que muestra el modificador](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


El cambio del directorio de suscripción es una operación de nivel de servicio. La propiedad de facturación de suscripción no se ve afectada y el Administrador de cuenta todavía puede cambiar el Administrador de servicios mediante el [Centro de cuentas](https://account.azure.com/subscriptions). Si desea eliminar el directorio original, debe transferir la propiedad de facturación de suscripción a un nuevo administrador de cuenta. Para más información acerca de cómo transferir la propiedad de facturación, vea [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>pasos siguientes

* Para más información acerca de cómo crear un nuevo directorio de Azure AD de forma gratuita, vea [Obtención de un inquilino de Azure Active Directory](develop/active-directory-howto-tenant.md)
* Para más información acerca de cómo transferir la propiedad de facturación de una suscripción de Azure, vea [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md)
* Para más información acerca de cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md)
* Para más información sobre cómo asignar roles en Azure AD, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
