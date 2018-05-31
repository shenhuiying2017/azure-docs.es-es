---
title: Realización de una revisión de acceso en Privileged Identity Management para recursos de Azure | Microsoft Docs
description: En este documento se describe cómo realizar una revisión de acceso en PIM para recursos de Azure, de acuerdo con un rol de recursos.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 47d981ce8034d725fe2b119392334a156852698d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201596"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Realización de una revisión de acceso de PIM, según el rol de recurso
Privileged Identity Management (PIM) para recursos de Azure simplifica el modo en que las empresas administran el acceso con privilegios a los recursos de Azure. 

Si se le ha asignado un rol administrativo, el administrador de roles con privilegios de su organización le puede pedir que revise y confirme periódicamente que aún necesita ese rol para su trabajo. Puede que reciba un correo electrónico con un vínculo o que vaya directamente al [Portal de Azure](https://portal.azure.com). Siga los pasos de este artículo para realizar una autorrevisión de los roles asignados.

Si es un administrador de roles con privilegios interesado en las revisiones de acceso, puede obtener más información en [Inicio de una revisión de acceso](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Incorporación de la aplicación Privileged Identity Management
Puede usar la aplicación PIM de Azure Active Directory (Azure AD) en [Azure Portal](https://portal.azure.com/) para realizar la revisión. Si no tiene la aplicación en el portal, siga estos pasos para comenzar.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Seleccione su nombre de usuario en la esquina superior derecha de Azure Portal y seleccione el directorio donde va a trabajar.
3. Seleccione **Todos los servicios** y use el cuadro de texto **Filtro** para buscar *Azure AD Privileged Identity Management*.
4. Seleccione **Anclar al panel** y, después, seleccione **Crear**. Se abre la aplicación PIM.

## <a name="approve-or-deny-access"></a>Aprobación o denegación de acceso
Al aprobar o denegar el acceso, lo que hace es indicar al revisor si aún usa o no este rol. Elija **Aprobar** si desea permanecer en el rol o **Denegar** si no va a volver a necesitar el acceso. El estado cambia solo cuando el revisor aplica a los resultados.

Para buscar y completar la revisión de acceso, siga estos pasos:
1. Vaya a la aplicación PIM de Azure AD.
2. Seleccione la hoja **Revisar acceso**.

   ![Captura de pantalla de la aplicación PIM, con la hoja Revisar acceso seleccionada](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Seleccione la revisión que quiere completar. 
4. Elija **Aprobar** o **Denegar**. En el cuadro de texto **Proporcionar un motivo**, puede que necesite incluir un motivo de su decisión.

   ![Captura de pantalla de la página de detalles de la revisión](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
