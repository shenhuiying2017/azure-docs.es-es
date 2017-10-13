---
title: "Administración de programas y controles para las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Puede crear programas adicionales para cada iniciativa de cumplimiento de normas, administración de riesgos y regulación de una organización a fin de recopilar y organizar las revisiones de acceso de Azure Active Directory como controles."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f6d7c2a1a3e3bf78b568f2f6b079476e403b9911
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-programs-and-their-controls"></a>Administración de programas y de sus controles 

Azure AD incluye las revisiones del acceso de los miembros del grupo y del acceso a las aplicaciones, que son ejemplos de controles que garantizan la vigilancia de quién tiene acceso. Estos controles permiten a las organizaciones abordar eficazmente sus requisitos de cumplimiento de normas, administración de riesgos y regulación.  

## <a name="how-to-manage-programs-and-their-controls"></a>Administración de programas y de sus controles
Puede simplificar el seguimiento y la recopilación de revisiones de acceso para distintos fines organizándolos en programas.  Cada revisión de acceso se puede vincular a un programa, para que, cuando se preparen los informes para un auditor, solos sean visibles esas revisiones de acceso en el ámbito de una iniciativa determinada.

Para ver una lista de programas, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) y cambie a la pestaña **Programas**.

Hay un programa siempre presente: "Programa predeterminado".  Si tiene el rol de administrador global, puede crear programas adicionales.  Por ejemplo, puede elegir tener un programa cada para iniciativa de cumplimiento de normas u objetivo empresarial.

Si ya no necesita un programa y no tiene ningún control vinculado a él, puede eliminarlo.

## <a name="next-steps"></a>Pasos siguientes

- [Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)

