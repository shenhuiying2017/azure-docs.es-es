---
title: "Creación de una máquina virtual de prueba en Azure Stack | Microsoft Docs"
description: "Aprenda a aprovisionar una máquina virtual de prueba en Azure Stack como un operador en la nube."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 41096f68e5e7d9e31098d1e8919101418abe4c03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Creación de una máquina virtual en Azure Stack

*Se aplica a: Kit de desarrollo de Azure Stack*

Como operador de Azure Stack, puede crear una máquina virtual de prueba para validar la implementación de [Azure Stack](azure-stack-poc.md) Development Kit.

> [!NOTE]
> Antes de poder aprovisionar las máquinas virtuales, debe [agregar la imagen de evaluación de Windows Server 2016 en Marketplace de Azure Stack](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>de una máquina virtual
1. En servidor host del Kit de desarrollo de Azure Stack, [inicie sesión en](azure-stack-connect-azure-stack.md) el portal del administrador (`https://adminportal.local.azurestack.external`) y, a continuación, haga clic en **Nuevo** > **Proceso** > **Windows Server 2016 Datacenter Eval** > **Crear**.  
2. En la hoja **Aspectos básicos**, escriba un **Nombre**, **Nombre de usuario** y **Contraseña**. Elija una **suscripción**. Cree un **grupo de recursos** o seleccione uno existente y, a continuación, haga clic en **Aceptar**.  
3. En la hoja **Elegir un tamaño**, haga clic en **A1 Estándar** y, después, en **Seleccionar**.  
4. En la hoja de **configuración**, acepte los valores predeterminados y haga clic en **Aceptar**.
5. En la hoja **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.  
6. Para ver la nueva máquina virtual, haga clic en **Todos los recursos** y, a continuación, busque la máquina virtual y haga clic en su nombre.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>pasos siguientes
[Usar los portales de administración y de usuarios en Azure Stack](azure-stack-manage-portals.md)
