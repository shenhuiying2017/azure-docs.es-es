---
title: Descarga de elementos de Marketplace desde Azure | Microsoft Docs
description: "Puedo descargar elementos de Marketplace desde Azure a la implementación de Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Descarga de elementos de Marketplace desde Azure a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Puesto que usted decide qué contenido desea incluir en su Marketplace de Azure Stack, debe tener en cuenta el contenido disponible en Azure Marketplace. Puede descargar de una lista protegida de elementos de Azure Marketplace que han sido previamente probados para su ejecución en Azure Stack. Puesto que se agregan nuevos elementos a esta lista con frecuencia, asegúrese de volver a consultar el nuevo contenido.

Para descargar elementos de Marketplace, primero debe [registrar Azure Stack en Azure](azure-stack-register.md). 

## <a name="download"></a>Descargar
1. Inicie sesión en el portal de administrador de Azure Stack (https://portal.local.azurestack.external).
2. Algunos elementos de Marketplace pueden ser muy grandes.  Para comprobar que dispone de espacio suficiente en el sistema, haga clic en **Proveedores de recursos** > **Storage**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace).

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Haga clic en **Agregar desde Azure** para ver una lista de elementos disponibles para su descarga. Puede hacer clic en cada elemento de la lista para ver su descripción y el tamaño de la descarga.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Seleccione el elemento que desee en la lista y, a continuación, haga clic en **Descargar**. Esto inicia la descarga de la imagen de máquina virtual para el elemento seleccionado. Los tiempos de descarga varían.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Una vez finalizada la descarga, puede implementar el nuevo elemento de Marketplace como un usuario o un operador de Azure Stack. Haga clic en **+Nuevo**, busque entre las categorías el nuevo elemento de Marketplace y, a continuación, seleccione el elemento.
7. Haga clic en **Crear** para abrir la experiencia de creación del elemento recién descargado. Siga las instrucciones paso a paso para implementar el elemento.

## <a name="next-steps"></a>Pasos siguientes

[Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)
