---
title: Instalar Visual Studio y conectarse a Azure Stack | Microsoft Docs
description: Aprenda los pasos necesarios para instalar Visual Studio y conectarse a Azure Stack.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 5487125095f05b2fbfa9489c5b4733f61c0212d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalar Visual Studio y conectarse a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Use Visual Studio para crear e implementar [plantillas](user/azure-stack-arm-templates.md) de Azure Resource Manager en Azure Stack. Puede seguir los pasos descritos en este artículo para instalar Visual Studio desde [Azure Stack Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows si se conecta a través de [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Estos pasos crean una nueva instalación de Visual Studio 2015 Community Edition. Obtenga más información sobre la [coexistencia](https://msdn.microsoft.com/library/ms246609.aspx) de otras versiones de Visual Studio.

## <a name="install-visual-studio"></a>Instalación de Visual Studio
1. Descargue y ejecute el [Instalador de plataforma web](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Busque **Visual Studio Community 2015 with Microsoft Azure SDK - 2.9.6**, haga clic en **Agregar** e **Instalar**.

    ![Captura de pantalla de los pasos de la instalación de WebPI](./media/azure-stack-install-visual-studio/image1.png) 

3. Desinstale la versión de **Microsoft Azure PowerShell** que está instalada como parte de Azure SDK.

    ![Captura de pantalla de la interfaz de agregar o quitar programas para Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png) 

4. [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)

5. Una vez finalizada la instalación, reinicie el sistema operativo.

## <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

1. Inicie Visual Studio.

2. En el menú **Ver**, seleccione **Cloud Explorer**.

3. En el nuevo panel, seleccione **Agregar cuenta** e inicie sesión con sus credenciales de Azure Active Directory.  
    ![Captura de pantalla de Cloud Explorer después de iniciar sesión en Azure Stack y conectarse](./media/azure-stack-install-visual-studio/image6.png)

Después de iniciar sesión, puede [implementar plantillas](user/azure-stack-deploy-template-visual-studio.md) o examinar los tipos y los grupos de recursos disponibles para crear sus propias plantillas.  

## <a name="next-steps"></a>Pasos siguientes

 - [Desarrollo de plantillas para Azure Stack](user/azure-stack-develop-templates.md)
