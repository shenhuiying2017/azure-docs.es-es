---
title: "Asignación de licencias para Azure MFA | Microsoft Docs"
description: "Aprenda cómo asignar licencias de usuario para Microsoft Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.openlocfilehash: 45522bf526c4aeab1d6ccc8891a55a0436ff9320
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Asignación de una licencia de Azure MFA, Azure AD Premium o Enterprise Mobility a usuarios
Si ha adquirido Azure MFA, Azure AD Premium o Enterprise Mobility Suite, no necesita crear un Proveedor de Multi-Factor Authentication. Una vez que asigne las licencias a los usuarios, puede empezar a habilitarlos para MFA.

## <a name="to-assign-a-license"></a>Asignación de una licencia
1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página Active Directory, haga doble clic en el directorio que tiene los usuarios que desea habilitar.
4. En la parte superior de la página de directorios, seleccione **Licencias**.
   ![Asignación de licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. En la página Licencias, seleccione **Azure Multi-Factor Authentication**, **Active Directory Premium** o **Enterprise Mobility Suite**.  Si solo tiene una, en este caso se debe seleccionar automáticamente.
6. En la parte inferior de la página, haga clic en **Asignar**.
   ![Asignación de licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. En el cuadro que aparece, haga clic junto a los usuarios o grupos a los que desea asignar licencias.  Debería ver una marca de verificación verde.
8. Haga clic en el icono de la marca de verificación para guardar los cambios.
   ![Asignación de licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. Debería ver un mensaje que indica cuántas licencias se han asignado y cuántas han producido un error.  Haga clic en **Aceptar**.
   ![Asignación de licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>Pasos siguientes

- Para más información, consulte [¿Qué es la licencia de Microsoft Azure Active Directory?](../active-directory/active-directory-licensing-what-is.md)
