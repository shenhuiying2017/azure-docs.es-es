---
title: "Realización de una revisión del acceso | Microsoft Docs"
description: "Aprenda a realizar una revisión con la aplicación Privileged Identity Management de Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 49ee2feb-7d2e-4acf-82c1-40ff23062862
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8ca735f04334557f40ddbe3119f7110dbcdde2a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Revisión de acceso en Privileged Identity Management de Azure AD
Privileged Identity Management de Azure Active Directory (AD) simplifica el modo en que las empresas administra el acceso con privilegios a los recursos de Azure AD y en otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.  

Si se le ha asignado un rol administrativo, el administrador de roles con privilegios de su organización le puede pedir que revise y confirme periódicamente que aún necesita ese rol para su trabajo. Puede que reciba un correo electrónico con un vínculo o que vaya directamente al [Portal de Azure](https://portal.azure.com). Siga los pasos de este artículo para realizar una autorrevisión de los roles asignados.

Si es un administrador de roles con privilegios interesado en las revisiones de acceso, puede obtener más información en [Inicio de una revisión de acceso](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Incorporación de la aplicación Privileged Identity Management
Puede usar la aplicación Privileged Identity Management (PIM) de Azure AD en el [Portal de Azure](https://portal.azure.com/) para realizar la revisión.  Si no tiene la aplicación en el portal, siga estos pasos para comenzar:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione su nombre de usuario en la esquina superior derecha del Portal de Azure y elija el directorio donde va a trabajar.
3. Seleccione **Más servicios** y use el cuadro de texto Filtro para buscar **Azure AD Privileged Identity Management**.
4. Active **Anclar al panel** y haga clic en **Crear**. Se abrirá la aplicación Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Aprobación o denegación de acceso
Al aprobar o denegar el acceso, lo que hace es indicar al revisor si aún usa o no este rol. Elija **Aprobar** si desea permanecer en el rol o **Denegar** si no va a volver a necesitar el acceso. Su estado no cambiará inmediatamente, hay que esperar a que el revisor aplique los resultados.
Para buscar y completar la revisión de acceso, siga estos pasos:

1. En la aplicación PIM, seleccione **Revisar acceso con privilegios**. Si tienes revisiones de acceso pendientes, aparecerán en la hoja de revisiones de acceso de Azure AD.
2. Seleccione la revisión que quiere completar.
3. A no ser que haya creado la revisión, aparecerá como el único usuario en ella. Seleccione la marca de verificación junto a su nombre.
4. Elija **Aprobar** o **Denegar**. Puede que necesite incluir un motivo de su decisión en el cuadro de texto **Proporcionar una razón** .  
5. Cierre la hoja **Revisar roles de Azure AD** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
