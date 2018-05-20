---
title: Uso de aplicaciones en Azure Blockchain Workbench
description: Procedimientos para el uso de contratos de aplicación en Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b6de4f1df56d1ec80ed74c98f4e3a1db9d206612
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Uso de aplicaciones en Azure Blockchain Workbench

Puede usar Blockchain Workbench para crear y realizar acciones en contratos. También puede ver detalles del contrato como el estado y el historial de transacciones.

## <a name="prerequisites"></a>requisitos previos

* Una implementación de Blockchain Workbench. Para más información sobre la implementación, consulte [Implementación de Azure Blockchain Workbench](blockchain-workbench-deploy.md).
* Una aplicación de cadena de bloques implementada en Blockchain Workbench. Consulte [Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench]()

[Abra Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) en el explorador.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Debe iniciar sesión como miembro de Blockchain Workbench. Si no aparece ninguna aplicación, es un miembro de Blockchain Workbench, pero no es miembro de ninguna aplicación. El administrador de Blockchain Workbench puede asignar a miembros a las aplicaciones.

## <a name="create-new-contract"></a>Creación de un nuevo contrato 

Para crear un nuevo contrato, debe ser miembro del rol **AllowedInstanceRoles**. 

1. En la sección de aplicaciones de Blockchain Workbench, seleccione el icono de la aplicación que contiene el contrato que desea crear. Se muestra una lista de contratos activos.

2. Para crear un nuevo contrato, seleccione **Nuevo contrato**.

    ![Botón de nuevo contrato](media/blockchain-workbench-use/contract-list.png)

3. Se muestra el panel **Nuevo contrato**. Especifique los valores de los parámetros iniciales. Seleccione **Crear**.

    ![Panel Nuevo contrato](media/blockchain-workbench-use/new-contract.png)

    El contrato recién creado se muestra en la lista con los otros contratos activos.

    ![Lista de contratos activos](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Realización de acciones en el contrato

1. En la sección de aplicaciones de Blockchain Workbench, seleccione el icono de la aplicación que contiene el contrato en el que realizar la acción.

    ![Lista de aplicaciones](media/blockchain-workbench-use/apps-list.png)

2. Seleccione el contrato en la lista.

    ![Lista de contratos](media/blockchain-workbench-use/select-contract.png)

    Los detalles sobre el contrato se muestran en secciones diferentes. 

    ![Detalles del contrato](media/blockchain-workbench-use/contract-details.png)

    | Sección  | DESCRIPCIÓN  |
    |---------|---------|
    | Status | Muestra el progreso actual dentro de las fases de contrato |
    | Detalles | Valores actuales del contrato |
    | . | Detalles sobre la última acción |
    | Actividad | Historial de transacciones del contrato |
    
3. En la sección **Acción**, seleccione **Realizar acción**.

4. Los detalles sobre el estado actual del contrato se muestran en un panel. Elija la acción que desea realizar en la lista desplegable. 

    ![Realizar acción](media/blockchain-workbench-use/take-action.png)

5. Seleccione **Ejecutar** para realizar la acción.

## <a name="next-steps"></a>Pasos siguientes

[Procedimientos para la solución de problemas de Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)