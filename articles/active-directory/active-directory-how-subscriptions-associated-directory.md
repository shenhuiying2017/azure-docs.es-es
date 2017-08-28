---
title: "Asociación de las suscripciones de Azure con Azure Active Directory | Microsoft Docs"
description: "Inicio de sesión en Microsoft Azure y temas relacionados, como la relación entre una suscripción de Azure y Azure Active Directory."
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
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2f8cc774b6cc245a4000e5c6924a8bb817707bc
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Asociación de las suscripciones de Azure con Azure Active Directory
Este artículo contiene información sobre la relación entre una suscripción de Azure y Azure Active Directory (Azure AD).

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relación de la suscripción de Azure con Azure AD
La suscripción de Azure tiene una relación de confianza con Azure AD, lo que significa que confía en el directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero cada suscripción confía solo en un único directorio. 

Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.). Si una suscripción expira, el acceso a los otros recursos asociados a la suscripción también se detiene. Sin embargo, el directorio permanece en Azure y puede asociar una suscripción diferente a ese directorio y continuar con la administración de los usuarios del directorio.

![Diagrama de asociación de suscripciones](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Azure AD no funciona igual que los demás servicios en la suscripción de Azure. Otros servicios de Azure están subordinados a la suscripción de Azure. Pero lo que se ve en Azure AD no varía en función de suscripción. Permite el acceso a directorios basándose en el usuario que inició sesión.

Todos los usuarios tienen un único directorio de inicio que los autentica, pero también pueden ser invitados en otros directorios. En Azure AD, puede ver solo los directorios de los que es miembro la cuenta de usuario. También se puede sincronizar un directorio con la instancia local de Active Directory.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD y suscripciones de servicios en la nube
Azure AD proporciona las principales capacidades de administración de identidades y directorios de la mayoría de los servicios en la nube de Microsoft, incluyendo:

* Las tablas de Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Al suscribirse en cualquiera de estos servicios en la nube de Microsoft, obtiene el servicio gratuito de Azure AD. Si desea agregar una suscripción de Azure adicional a un directorio de Azure AD, puede hacerlo solo si ha iniciado sesión con una cuenta de Microsoft. Por ejemplo, si se registra en Azure con una cuenta de Microsoft y también se registra en un servicio en la nube de Microsoft distinto con una cuenta profesional o educativa, tiene dos instancias de Azure AD:
1. El directorio predeterminado de la suscripción de Azure. Puede agregar otra suscripción de Azure a este directorio u otros que haya creado si ha iniciado sesión con una cuenta de Microsoft porque se puede autenticar en Azure.
2. El directorio de inicio de la cuenta profesional o educativa. Si ha iniciado sesión en Azure con una cuenta profesional o educativa, no puede agregar una suscripción de Azure a un directorio existente porque su cuenta profesional o educativa no se puede autenticar directamente en Azure. 
 
Para más información sobre cómo cambiar los administradores de una suscripción de Azure, consulte [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Sugerencias para administrar una suscripción y un directorio
Los roles administrativos de una suscripción de Azure administran los recursos vinculados a la suscripción de Azure. En esta sección se explican las diferencias entre los administradores de suscripciones de Azure y los administradores de directorios de Azure AD. Los roles administrativos y otras sugerencias para administrar la suscripción se tratan en [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles.md).

De forma predeterminada, se le asigna el rol de administrador de servicios cuando se registra. Si otros usuarios necesitan iniciar sesión y acceder a los servicios con la misma suscripción, se pueden agregar como coadministradores. 

Azure AD tiene un conjunto diferente de roles administrativos para gestionar las características relacionadas con la identidad y el directorio. Por ejemplo, el administrador global de un directorio puede agregar usuarios y grupos al directorio o requerir la autenticación multifactor para los usuarios. Los usuarios que crean un directorio se asignan al rol de administrador global y pueden asignar roles administrativos a otros usuarios. Otros servicios, como Office 365 y Microsoft Intune, también usan los roles administrativos de Azure AD. 

Los administradores de suscripciones de Azure y los administradores de directorios de Azure AD son dos roles diferentes. 
* Los administradores de suscripciones de Azure pueden administrar recursos de Azure y pueden usar Azure AD en Azure Portal (ya que Azure Portal es un recurso de Azure). 
* Los administradores de directorios solo pueden administrar propiedades en el directorio de Azure AD.

Una persona puede tener ambos roles, pero no es obligatorio. Un usuario se puede asignar al rol de administrador global de directorios, pero no como administrador de servicios ni coadministrador de una suscripción de Azure. Si no es un administrador de la suscripción, el usuario puede iniciar sesión en Azure Portal, pero no puede administrar los directorios de esa suscripción en el portal. Este usuario puede administrar los directorios con otras herramientas como PowerShell de Azure AD o el centro de administración de Office 365.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo cambiar los administradores de una suscripción de Azure, consulte [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md)
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md)
* Para más información sobre cómo asignar roles en Azure AD, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

