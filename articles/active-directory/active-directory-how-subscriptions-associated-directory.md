---
title: "Adición de una suscripción de Azure existente al directorio de Azure AD | Microsoft Docs"
description: "Adición de una suscripción existente al directorio de Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Adición de una suscripción de Azure a Azure Active Directory
Este artículo contiene información sobre la relación entre una suscripción de Azure y Azure Active Directory (AD) y cómo agregar una suscripción existente al directorio de Azure AD. La suscripción de Azure tiene una relación de confianza con Azure AD, lo que significa que confía en el directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero cada suscripción confía solo en un único directorio. 

Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.). Si una suscripción expira, el acceso a los otros recursos asociados a la suscripción también se detiene. Sin embargo, un directorio de Azure AD permanece en Azure y puede asociar una suscripción diferente a ese directorio y administrarlo con la nueva suscripción.

Todos los usuarios tienen un único directorio de inicio que los autentica, pero también pueden ser invitados en otros directorios. En Azure AD, puede ver los directorios de los que es miembro la cuenta de usuario o invitado.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Para agregar una suscripción existente a su directorio Azure AD
Debe iniciar sesión con una cuenta que exista tanto en el directorio actual con el que está asociado la suscripción como en el directorio al que desea agregarla. 

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.azure.com/Subscriptions) con una cuenta que sea administrador de cuenta de la suscripción cuya suscripción quiere transferir.
2. Asegúrese de que el usuario que va a ser el propietario de la suscripción está en el directorio de destino.
3. Haga clic en **Transferir suscripción**.
4. Especifique el destinatario. El destinatario obtiene automáticamente un correo electrónico con un vínculo de aceptación.
5. El destinatario hace clic en el vínculo y sigue las instrucciones, incluyendo la especificación de su información de pago. Cuando el destinatario finaliza correctamente, se transfiere la suscripción. 
6. El directorio predeterminado de la suscripción se cambia al directorio donde está el usuario.

Para más información, consulte [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md).

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo cambiar los administradores de una suscripción de Azure, consulte [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md)
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md)
* Para más información sobre cómo asignar roles en Azure AD, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
