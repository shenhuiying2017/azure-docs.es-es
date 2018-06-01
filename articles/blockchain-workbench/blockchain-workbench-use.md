---
title: Uso de aplicaciones en Azure Blockchain Workbench
description: Procedimientos para el uso de contratos de aplicación en Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258046"
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

Para crear un contrato, debe ser un miembro especificado como un **iniciador** de contratos. Para obtener información sobre la definición de roles de aplicación e iniciadores para el contrato, vea los [flujos de trabajo de la información general de configuración](blockchain-workbench-configuration-overview.md#workflows). Para obtener información sobre la asignación de miembros a los roles de aplicación, vea cómo [agregar un miembro a la aplicación](blockchain-workbench-manage-users.md#add-member-to-application).

1. En la sección de aplicaciones de Blockchain Workbench, seleccione el icono de la aplicación que contiene el contrato que desea crear. Se muestra una lista de contratos activos.

2. Para crear un nuevo contrato, seleccione **Nuevo contrato**.

    ![Botón de nuevo contrato](media/blockchain-workbench-use/contract-list.png)

3. Se muestra el panel **Nuevo contrato**. Especifique los valores de los parámetros iniciales. Seleccione **Crear**.

    ![Panel Nuevo contrato](media/blockchain-workbench-use/new-contract.png)

    El contrato recién creado se muestra en la lista con los otros contratos activos.

    ![Lista de contratos activos](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Realización de acciones en el contrato

Según el estado que tenga el contrato, los miembros pueden realizar acciones para realizar la transición al siguiente estado del contrato. Las acciones se definen como [transiciones](blockchain-workbench-configuration-overview.md#transitions) dentro de un [estado](blockchain-workbench-configuration-overview.md#states). Los miembros que pertenecen a un rol de aplicación o instancia permitido para la transición pueden realizar la acción. 

1. En la sección de aplicaciones de Blockchain Workbench, seleccione el icono de la aplicación que contiene el contrato en el que realizar la acción.
2. Seleccione el contrato en la lista. Los detalles sobre el contrato se muestran en secciones diferentes. 

    ![Detalles del contrato](media/blockchain-workbench-use/contract-details.png)

    | Sección  | DESCRIPCIÓN  |
    |---------|---------|
    | Status | Muestra el progreso actual dentro de las fases de contrato |
    | Detalles | Valores actuales del contrato |
    | . | Detalles sobre la última acción |
    | Actividad | Historial de transacciones del contrato |
    
3. En la sección **Acción**, seleccione **Realizar acción**.

4. Los detalles sobre el estado actual del contrato se muestran en un panel. Elija la acción que desea realizar en la lista desplegable. 

    ![Elegir acción](media/blockchain-workbench-use/choose-action.png)

5. Seleccione **Realizar acción** para iniciar la acción.
6. Si se requieren parámetros para la acción, especifique los valores pertinentes.

    ![Realizar acción](media/blockchain-workbench-use/take-action.png)

7. Seleccione **Realizar acción** para ejecutar la acción.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Procedimientos para la solución de problemas de Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)