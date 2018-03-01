---
title: "Incorporación de usuarios a los AD FS de Azure Stack | Microsoft Docs"
description: Aprenda a agregar usuarios a las implementaciones de Azure Stack
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 5774750edc5b7380275d4f20aee3be47f2f62b4d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="add-users-in-the-azure-stack-development-kit"></a>Incorporación de usuarios al Kit de desarrollo de Azure Stack

*Se aplica a: Kit de desarrollo de Azure Stack*

Para agregar usuarios adicionales a la implementación del Kit de desarrollo, debe agregarlos al directorio del Kit de desarrollo de Azure Stack con Microsoft Management Console desde un equipo host de Azure Stack.
1.  En el equipo de Azure Stack, abra Microsoft Management Console.
2.  Haga clic en **File > Add or remove snap-in** (Archivo > Agregar o quitar complemento).
3.  Seleccione **Active Directory Users and Computers** > **AzureStack.local** > **Users** (Usuarios y equipos de Active Directory > AzureStack.local > Usuarios).
4.  Haga clic en **Action** > **New** > **User** (Acción > Nuevo > Usuario).
5.  En la ventana New Object - User (Nuevo objeto: usuario), proporcione una contraseña y confírmela.
6.  Haga clic en **Next** (Siguiente) para finalizar los valores y haga clic en Finish (Finalizar) para crear el usuario.


