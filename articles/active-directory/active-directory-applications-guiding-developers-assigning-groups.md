---
title: "Asignación de grupos a aplicaciones de Azure AD | Microsoft Docs"
description: "Cómo implementar la asignación de grupos para aplicaciones de Azure."
services: active-directory
documentationcenter: 
author: IHenkel
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: inhenk
translationtype: Human Translation
ms.sourcegitcommit: e66b606433f8924a509f2d04dae67ff00ded6dca
ms.openlocfilehash: 81b9b66bb16e183b8c3c206a4a49b2f4845bc201


---
# <a name="azure-ad-and-applications-assign-groups-to-an-application"></a>Azure AD y aplicaciones: asignación de grupos a una aplicación
Para poder asignar usuarios y grupos a una aplicación, debe requerir la asignación de usuarios. Para obtener información sobre cómo requerir la asignación de usuarios, consulte el artículo [Necesidad de asignación de usuario](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

En este artículo se supone que ya ha creado grupos en el Active Directory que está usando para esta aplicación.

## <a name="assigning-groups-to-an-application"></a>Asignación de grupos a una aplicación
1. Inicie sesión en el portal de Azure con una cuenta de administrador.
2. Haga clic en el elemento **Todos los elementos** del menú principal.
3. Elija el directorio que está usando para la aplicación.
4. Haga clic en la pestaña **APLICACIONES** .
5. Seleccione la aplicación en la lista de aplicaciones asociada a este directorio.
6. Haga clic en la pestaña **USUARIOS Y GRUPOS** .
7. Filtre la lista de grupos en Active Directory mediante la lista desplegable **Grupos** .
8. Seleccione el grupo.
9. Haga clic en **ASIGNAR**.
10. Haga clic en **Sí** cuando se le solicite.

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]



<!--HONumber=Feb17_HO1-->


