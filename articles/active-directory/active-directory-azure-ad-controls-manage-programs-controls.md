---
title: Administración de programas y controles para las revisiones de acceso de Azure AD | Microsoft Docs
description: Puede crear programas adicionales para cada iniciativa de cumplimiento de normas, administración de riesgos y regulación de una organización a fin de recopilar y organizar las revisiones de acceso de Azure Active Directory como controles.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: a3fb812623b490e27907f63c1f7c6610ae754fb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192887"
---
# <a name="manage-programs-and-their-controls"></a>Administración de programas y de sus controles 

Azure Active Directory (Azure AD) incluye revisiones de acceso de los miembros del grupo y acceso a la aplicación. Estos ejemplos de controles garantizan la vigilancia de quién dispone de acceso a sus aplicaciones y pertenencias a grupos de la organización. Las organizaciones pueden usar estos controles para abordar eficazmente sus requisitos de cumplimiento de normas, administración de riesgos y regulación.

## <a name="create-and-manage-programs-and-their-controls"></a>Creación y administración de programas y de sus controles
Puede simplificar el seguimiento y la recopilación de revisiones de acceso para distintos fines organizándolos en programas. Cada revisión de acceso se puede vincular a un programa. Por lo tanto, cuando prepare los informes para un auditor, puede centrarse en las revisiones de acceso en el ámbito de una iniciativa determinada.  Los programas y los resultados de la revisión de acceso son visibles para los usuarios que se encuentran en el rol Administrador global, Administrador de seguridad o Lector de seguridad.

Para ver una lista de programas, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) y seleccione **Programas**.

El **programa predeterminado** siempre está presente. Si tiene el rol de administrador global, puede crear programas adicionales. Por ejemplo, puede elegir tener un programa cada para iniciativa de cumplimiento de normas u objetivo empresarial.

Si ya no necesita un programa y no tiene ningún control vinculado a él, puede eliminarlo.

## <a name="next-steps"></a>Pasos siguientes

- [Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)
- [Retrieve the results of an access review](active-directory-azure-ad-controls-retrieve-access-review.md) (Recuperación de los resultados de una revisión de acceso)
