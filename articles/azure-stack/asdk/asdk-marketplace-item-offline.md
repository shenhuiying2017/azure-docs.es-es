---
title: Adición de un elemento de Marketplace de Azure Stack desde un origen local | Microsoft Docs
description: Describe cómo agregar una imagen del sistema operativo local a Marketplace de Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 815dc055e19a38a61cbb3e927c3d7e92393b17dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Tutorial: Adición de un elemento de Marketplace de Azure Stack desde un origen local

Como operador de Azure Stack, lo primero que necesita hacer para permitir a los usuarios implementar una máquina virtual es agregar una imagen de máquina virtual al Marketplace de Azure Stack. De forma predeterminada, no se publica nada en Marketplace de Azure Stack, pero puede cargar imágenes ISO de la máquina virtual para que estén disponibles para los usuarios. Use esta opción si ha implementado Azure Stack en un escenario sin conexión o en escenarios con conectividad limitada.

En este tutorial, va a descargar una imagen de la máquina virtual de Windows Server 2016 desde la página de evaluaciones de Windows Server y la va a cargar en Marketplace de Azure Stack.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Agregar una imagen de máquina virtual de Windows Server 2016
> * Comprobar que la imagen de máquina virtual 
> * Probar la imagen de la máquina virtual

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

- Instale los [módulos de Azure PowerShell compatibles con Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell)
- Descargue las [herramientas de Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools)
- Descargue [la imagen ISO de la máquina virtual de Windows Server 2106](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) en la página de evaluaciones de Windows Server

## <a name="add-a-windows-server-vm-image"></a>Adición de una imagen de máquina virtual de Windows Server
Para publicar la imagen de Windows Server 2016 en Marketplace de Azure Stack, agregue una imagen ISO descargada previamente mediante PowerShell. 

Use esta opción si ha implementado Azure Stack en un escenario sin conexión o en escenarios con conectividad limitada.

1. Importe los módulos de PowerShell `Connect` y `ComputeAdmin` de Azure Stack incluidos en el directorio de herramientas de Azure Stack mediante los comandos siguientes:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Ejecute uno de los siguientes scripts en el equipo host del Kit de desarrollo de Azure Stack, según si implementó su entorno de Azure Stack mediante Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS):

  - Comandos para **implementaciones de Azure AD**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - Comandos para **implementaciones de AD FS**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Agregue la imagen de Windows Server 2016 a Marketplace de Azure Stack. (Reemplace *fully_qualified_path_to_ISO* por la ruta de acceso a la instancia de Windows Server 2016 ISO que ha descargado).

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>Comprobación de que el elemento de Marketplace está disponible
Utilice estos pasos para comprobar que la nueva imagen de máquina virtual está disponible en Marketplace de Azure Stack.

1. Inicie sesión en el [portal de administración del Kit de desarrollo de Azure Stack](https://adminportal.local.azurestack.external).

2. Seleccione **Más servicios** > **Marketplace Management** (Administración de Marketplace). 

3. Compruebe que la imagen de la máquina virtual de Windows Server 2016 Datacenter se ha agregado correctamente.

   ![Imagen descargada desde Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Probar la imagen de la máquina virtual
Como operador de Azure Stack, puede utilizar el [portal de administración](https://adminportal.local.azurestack.external) para crear una máquina virtual de prueba para validar que la imagen esté disponible correctamente. 

1. Inicie sesión en el [portal de administración del Kit de desarrollo de Azure Stack](https://adminportal.local.azurestack.external).

2. Haga clic en **Nuevo** > **Proceso** > **Windows Server 2016 Datacenter** > **Crear**.  
 ![Creación de una máquina virtual a partir de una imagen de Marketplace](media/asdk-marketplace-item/new-compute.png)

3. En la hoja **Básico**, especifique la siguiente información y haga clic en **Aceptar**:
  - **Nombre**: test-vm-1
  - **Nombre de usuario**: AdminTestUser
  - **Contraseña**: AzS-TestVM01
  - **Suscripción**: use la suscripción de proveedor predeterminada
  - **Grupo de recursos**: test-vm-rg
  - **Ubicación**: local

4. En la hoja **Elegir un tamaño**, haga clic en **A1 Estándar** y, después, en **Seleccionar**.  

5. En la hoja de **configuración**, acepte los valores predeterminados y haga clic en **Aceptar**.

6. En la hoja **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.  
> [!NOTE]
> La implementación de la máquina virtual tarda unos minutos en completarse.

7. Para ver la nueva máquina virtual, haga clic en **Máquinas virtuales**, busque **test-vm-1** y haga clic en su nombre.
    ![Primera imagen de máquina virtual de prueba mostrada](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
Después de haber iniciado sesión en la máquina virtual y comprobado que la imagen de prueba funciona correctamente, debe eliminar el grupo de recursos de prueba. Esto liberará los recursos limitados disponibles para las instalaciones del Kit de desarrollo de Azure Stack de nodo único.

Cuando ya no los necesite, quite el grupo de recursos, la máquina virtual y todos los recursos relacionados siguiendo los pasos a continuación:

1. Inicie sesión en el [portal de administración del Kit de desarrollo de Azure Stack](https://adminportal.local.azurestack.external).
2. Haga clic en **Grupos de recursos** > **test-vm-rg** > **Eliminar grupo de recursos**.
3. Escriba **test-vm-rg** como nombre del grupo de recursos y, después, haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Agregar una imagen de máquina virtual de Windows Server 2016
> * Comprobar que la imagen de máquina virtual 
> * Probar la imagen de la máquina virtual

Avance al siguiente tutorial para aprender a crear una oferta y un plan de Azure Stack.

> [!div class="nextstepaction"]
> [Ofrecimiento de servicios de IaaS para Azure Stack](asdk-offer-services.md)
