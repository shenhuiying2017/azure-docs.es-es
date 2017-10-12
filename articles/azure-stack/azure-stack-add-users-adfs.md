---
title: "Incorporación de usuarios a los AD FS de Azure Stack | Microsoft Docs"
description: Aprenda a agregar usuarios a las implementaciones de Azure Stack
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 2cad56caa182a9abdca02944000b1506953af3f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-users-in-the-azure-stack-development-kit"></a>Incorporación de usuarios a Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Para agregar usuarios adicionales a la implementación del kit de desarrollo, debe agregarlos al directorio de Azure Stack Development Kit con Microsoft Management Console desde un equipo host de Azure Stack.
1.  En el equipo de Azure Stack, abra Microsoft Management Console.
2.  Haga clic en **File > Add or remove snap-in** (Archivo > Agregar o quitar complemento).
3.  Seleccione **Active Directory Users and Computers** > **AzureStack.local** > **Users** (Usuarios y equipos de Active Directory > AzureStack.local > Usuarios).
4.  Haga clic en **Action** > **New** > **User** (Acción > Nuevo > Usuario).
5.  En la ventana New Object - User (Nuevo objeto: usuario), proporcione una contraseña y confírmela.
6.  Haga clic en **Next** (Siguiente) para finalizar los valores y haga clic en Finish (Finalizar) para crear el usuario.


