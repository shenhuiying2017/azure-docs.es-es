---
title: "Nueva implementación de Azure Stack | Microsoft Docs"
description: "Cree una nueva implementación de Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Nueva implementación de Azure Stack
Si recibe un error durante la implementación de Azure Stack, puede volver a ejecutar el programa de instalación mediante el siguiente comando de PowerShell: `.\InstallAzureStackpoc.ps1 -rerun`. Este comando reiniciará el programa de instalación de Azure Stack en el momento en que se produjo el error sin necesidad de volver a empezar. Si recibe el mismo error de instalación de nuevo, le recomendamos que vuelva a realizar una implementación completa para solucionar el problema. 

Para volver a implementar Azure Stack, debe empezar desde el principio, tal y como se describe a continuación.

## <a name="steps-to-redeploy-azure-stack"></a>Pasos para volver a implementar Azure Stack
1. En el host del kit de desarrollo, abra una consola de PowerShell con privilegios elevados > vaya al script asdk-installer.ps1 > ejecútelo > haga clic en **Reiniciar**.
2. Seleccione el sistema operativo base (no **Azure Stack**) y haga clic en **Siguiente**.
3. Una vez que se reinicie el host del kit de desarrollo, elimine el archivo de CloudBuilder.vhdx que se usó como parte de la implementación anterior.
4. [Implementación del kit de desarrollo](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>pasos siguientes
[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)

