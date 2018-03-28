---
title: Adición de un elemento de Marketplace de Azure Stack desde Azure | Microsoft Docs
description: Describe cómo agregar una imagen de máquina virtual de Windows Server basada en Azure al Marketplace de Azure Stack.
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
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Tutorial: Adición de un elemento de Marketplace de Azure Stack desde Azure

Como operador de Azure Stack, lo primero que necesita hacer para permitir a los usuarios implementar una máquina virtual es agregar una imagen de máquina virtual al Marketplace de Azure Stack. De forma predeterminada, no se publica nada en Marketplace de Azure Stack, pero puedes descargar elementos de [una lista organizada de elementos de Azure Marketplace](.\.\azure-stack-marketplace-azure-items.md) que se han probado previamente para ejecutarse en Azure Stack. Use esta opción si trabaja en un escenario conectado y ha registrado su instancia de Azure Stack en Azure.

En este tutorial, va a agregar la imagen de máquina virtual de Windows Server 2016 desde Azure Marketplace al Marketplace Azure Stack.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Agregar un elemento del Marketplace de Azure Stack de la máquina virtual de Windows Server
> * Comprobar que la imagen de máquina virtual 
> * Probar la imagen de la máquina virtual

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

- Instale los [módulos de Azure PowerShell compatibles con Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell).
- Descargue las [herramientas de Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Registre el Kit de desarrollo de Azure Stack](asdk-register.md) en la suscripción de Azure

## <a name="add-a-windows-server-vm-image"></a>Adición de una imagen de máquina virtual de Windows Server
Para agregar una imagen de Windows Server 2016 al Marketplace de Azure Stack, descargue la imagen de Azure Marketplace. Use esta opción si trabaja en un escenario conectado y ya ha [registrado su instancia de Azure Stack](asdk-register.md) en Azure.

1. Inicie sesión en el [portal de administración del Kit de desarrollo de Azure Stack](https://adminportal.local.azurestack.external).

2. Seleccione **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure). 

   ![Adición desde Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Busque la imagen **Windows Server 2016 Datacenter**.

4. Seleccione la imagen de **Windows Server 2016 Datacenter** y después **Descargar**.

   ![Descarga de la imagen desde Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Se tarda aproximadamente una hora en descargar la imagen de máquina virtual y publicarla en Marketplace de Azure Stack. 

Cuando finalice la descarga, la imagen se publicará y estará disponible en **Marketplace Management** (Administración de Marketplace). La imagen también está disponible en **Proceso** y se utiliza para crear nuevas máquinas virtuales.

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
  - **Suscripción**: use la suscripción de proveedor predeterminada.
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
> * Agregar un elemento del Marketplace de Azure Stack de la máquina virtual de Windows Server
> * Comprobar que la imagen de máquina virtual 
> * Probar la imagen de la máquina virtual

Avance al siguiente tutorial para aprender a crear una oferta y un plan de Azure Stack.

> [!div class="nextstepaction"]
> [Ofrecimiento de servicios de IaaS para Azure Stack](asdk-offer-services.md)