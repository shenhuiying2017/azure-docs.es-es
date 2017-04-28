---
title: "Suscripción a Azure con la cuenta de Office 365 | Microsoft Docs"
description: "Obtenga información acerca de cómo crear una suscripción de Azure mediante una cuenta de Office 365"
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d62ec023605573e4b0804da00c75f65c8dea5173
ms.lasthandoff: 04/12/2017


---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrarse para obtener una suscripción de Azure con su cuenta de Office 365
Si tiene una suscripción de Office 365, use su cuenta de Office 365 para crear una suscripción de Azure. Puede iniciar sesión en [Azure Portal](https://portal.azure.com/) sin una suscripción de Azure usando su nombre de usuario de Office 365 y la contraseña. Pero si desea configurar máquinas virtuales o usar otros servicios de Azure, debe crear una suscripción de Azure para su cuenta. Si es parte de una organización que desea usar los servicios de Azure, puede agregar a otras personas de su organización a la suscripción después de crearla.  

Si ya tiene una cuenta de Office 365 y una suscripción de Azure, consulte [Asociación de un inquilino de Office 365 a una suscripción de Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-by-using-your-office-365-account"></a>Obtener una suscripción de Azure con su cuenta de Office 365

Para suscribirse a Azure, use el vínculo gratuito de Azure y suscríbase a Azure usando su nombre de usuario y contraseña de Office 365. 

1. Vaya a [Azure.com](https://azure.microsoft.com/). 
2. Haga clic en **Empiece gratis**.
3. Inicie sesión con su nombre de usuario y contraseña de Office 365. La cuenta que use no necesita tener permisos de administrador. Si tiene más de una cuenta de Office 365, asegúrese de utilizar las credenciales de la cuenta de Office 365 que desea asociar a su suscripción de Azure. 

   ![Captura de pantalla que muestra la página de registro.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

4. Escriba la información necesaria para completar el proceso de suscripción.

    ![Captura de pantalla que muestra el formulario de registro.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Si necesita agregar otras personas de su organización a la suscripción de Azure, vea [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md). 
- Si desea agregar un administrador para ayudar a administrar la suscripción, consulte [Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios](billing-add-change-azure-subscription-administrator.md).

## <a id="more-about-subs">Más información acerca de las suscripciones de Azure y Office 365</a>
Office 365 y Azure usan el servicio Azure Active Directory para administrar usuarios y suscripciones. El directorio de Azure es como un contenedor en el que puede agrupar usuarios y suscripciones. Para poder usar las mismas cuentas de usuario con sus suscripciones de Azure y Office 365, asegúrese de que las suscripciones se hayan creado en el mismo directorio. Tenga en cuenta los siguientes puntos:

* Una suscripción se crea en un directorio.
* Los usuarios pertenecen a directorios.
* Una suscripción se incluye en el directorio del usuario que la crea. Por lo que la suscripción a Office 365 está asociada a la misma cuenta que la suscripción de Azure.
* Las suscripciones de Azure son propiedad de usuarios individuales del directorio.
* Las suscripciones de Office 365 pertenecen al propio directorio. Los usuarios con los permisos adecuados en el directorio pueden administrar estas suscripciones.

![Captura de pantalla que muestra la relación entre el directorio, los usuarios y las suscripciones.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para más información, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente. 
