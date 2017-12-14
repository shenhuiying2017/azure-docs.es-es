---
title: "Administración de programas y controles para las revisiones de acceso de Azure AD | Microsoft Docs"
description: "Puede crear programas adicionales para cada iniciativa de cumplimiento de normas, administración de riesgos y regulación de una organización a fin de recopilar y organizar las revisiones de acceso de Azure Active Directory como controles."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f322bff427384e6432fcc0c288704f6bb605c900
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-programs-and-their-controls"></a>Administración de programas y de sus controles 

Azure Active Directory (Azure AD) incluye revisiones de acceso de los miembros del grupo y acceso a la aplicación. Estos ejemplos de controles garantizan la vigilancia de quién dispone de acceso a sus aplicaciones y pertenencias a grupos de la organización. Las organizaciones pueden usar estos controles para abordar eficazmente sus requisitos de cumplimiento de normas, administración de riesgos y regulación.

## <a name="create-and-manage-programs-and-their-controls"></a>Creación y administración de programas y de sus controles
Puede simplificar el seguimiento y la recopilación de revisiones de acceso para distintos fines organizándolos en programas. Cada revisión de acceso se puede vincular a un programa. Por lo tanto, cuando prepare los informes para un auditor, solo podrá ver las revisiones de acceso en el ámbito de una iniciativa determinada.

Para ver una lista de programas, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) y seleccione **Programas**.

El **programa predeterminado** siempre está presente. Si tiene el rol de administrador global, puede crear programas adicionales. Por ejemplo, puede elegir tener un programa cada para iniciativa de cumplimiento de normas u objetivo empresarial.

Si ya no necesita un programa y no tiene ningún control vinculado a él, puede eliminarlo.

## <a name="next-steps"></a>Pasos siguientes

[Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)

