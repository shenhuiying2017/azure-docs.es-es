---
title: "Asignación de grupos a aplicaciones de Azure AD | Microsoft Docs"
description: "Cómo implementar la asignación de grupos para aplicaciones de Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.openlocfilehash: 471099c58a7fc1dfd520e693873a76f64c0fbf0a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>Asignación de grupos de Azure Active Directory a una aplicación
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
