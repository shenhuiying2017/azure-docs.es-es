---
title: "Nueva implementación de Azure Stack | Microsoft Docs"
description: "Cree una nueva implementación de Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Nueva implementación de Azure Stack
Para volver a implementar Azure Stack, debe empezar desde el principio, tal y como se describe a continuación.

## <a name="steps-to-redeploy-azure-stack"></a>Pasos para volver a implementar Azure Stack
1. En el host del kit de desarrollo, abra una consola de PowerShell con privilegios elevados > vaya al script asdk-installer.ps1 > ejecútelo > haga clic en **Reiniciar**.
2. Seleccione el sistema operativo base (no **Azure Stack**) y haga clic en **Siguiente**.
3. Una vez que se reinicie el host del kit de desarrollo, elimine el archivo de CloudBuilder.vhdx que se usó como parte de la implementación anterior.
4. [Implementación del kit de desarrollo](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Pasos siguientes
[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)

