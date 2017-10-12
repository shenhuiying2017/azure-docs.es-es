---
title: Cuentas de almacenamiento en Azure Stack | Microsoft Azure
description: Aprenda a crear una cuenta de almacenamiento en Azure Stack.
services: azure-stack
documentationcenter: 
author: vhorne
manager: byronr
editor: 
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/1/2017
ms.author: victorh
ms.openlocfilehash: 41c9ee37c43d4ad41c51ea2ed023d3b47d460dd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="storage-accounts-in-azure-stack"></a>Cuentas de almacenamiento en Azure Stack
Las cuentas de almacenamiento incluyen los servicios BLOB y Tabla y el espacio de nombres único para los objetos de datos de almacenamiento. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta de almacenamiento.

1. En el equipo de la prueba de concepto de Azure Stack, inicie sesión en `https://adminportal.local.azurestack.external` como [un administrador](azure-stack-connect-azure-stack.md) y, a continuación, haga clic en **Nuevo** > **Datos y almacenamiento**  >  **Cuenta de almacenamiento**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. En la hoja **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento. Cree un nuevo **grupo de recursos**, o seleccione uno existente y, a continuación, haga clic en **Crear** para crear la cuenta de almacenamiento.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Para ver la nueva cuenta de almacenamiento, haga clic en **Todos los recursos** y, a continuación, busque la cuenta de almacenamiento y haga clic en su nombre.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Pasos siguientes
[Uso de plantillas de Azure Resource Manager](user/azure-stack-arm-templates.md)

[Más información acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md)

[Descargue la guía de validación de almacenamiento coherente con Azure para Azure Stack](http://aka.ms/azurestacktp1doc)
