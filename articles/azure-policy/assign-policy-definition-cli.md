---
title: "Uso de la CLI de Azure para crear una asignación de directiva para identificar recursos no compatibles en el entorno de Azure | Microsoft Docs"
description: "Use PowerShell para crear una asignación de Azure Policy para identificar recursos no compatibles."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 76725f3ebeaf5af4f2ab8aadb303d862fa111ecb
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Creación de una asignación de directiva para identificar recursos no compatibles en el entorno de Azure con la CLI de Azure

El primer paso para entender el cumplimiento en Azure es identificar el estado de sus recursos. Este inicio rápido lo guiará por el proceso de creación de una asignación de directiva para identificar máquinas virtuales que no están usando discos administrados.

Al finalizar este proceso, habrá identificado correctamente máquinas virtuales que no utilizan discos administrados. No *son compatibles* con la asignación de directiva.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Esta guía de inicio rápido requiere la ejecución de la versión 2.0.4 de la CLI de Azure o una versión posterior para instalar y usar la CLI en un entorno local. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva

En este tutorial rápido, se crea una asignación de directiva y se le asigna la definición de la Auditoría de máquinas virtuales sin discos administrados. Esta definición de directiva identifica los recursos que no son compatibles con las condiciones establecidas en la definición de directiva.

Siga estos pasos para crear una nueva asignación de directiva:

1. Registre el proveedor de recursos de Policy Insights para asegurarse de que la suscripción funciona con el proveedor de recursos. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación de registro para este. Esta operación está incluida en los roles Colaborador y Propietario.

    Registre el proveedor de recursos mediante la ejecución del siguiente comando:

    ```azurecli
    az provider register --namespace Microsoft.PolicyInsights
    ```

    El comando devuelve un mensaje que indica que el registro está en curso.

    No se puede anular el registro de un proveedor de recursos si dispone de tipos de recursos de ese proveedor de recursos en la suscripción. Para más información sobre el registro y la visualización de los proveedores de recursos, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md).

2. Vea todas las definiciones de directiva y busque la definición de la directiva *Auditoría de máquinas virtuales sin discos administrados*:

    ```azurecli
az policy definition list
```

    Azure Policy integra ya definiciones de directiva disponibles para usarlas. Encontrará definiciones de directiva integradas como:

    - Enforce tag and its value (Forzar una etiqueta y su valor)
    - Apply tag and its value (Aplicar una etiqueta y su valor)
    - Require SQL Server Version 12.0 (Requerir SQL Server 12.0)

3. A continuación, proporcione la información siguiente y ejecute el comando siguiente para asignar la definición de directiva:

    - **Nombre** para mostrar para la asignación de directiva. En este caso, vamos a usar la *Auditoría de máquinas virtuales sin discos administrados*.
    - **Directiva**: se trata de la definición de directiva, según la opción utilizada para crear la asignación. En este caso, es la definición de la directiva: *Auditoría de máquinas virtuales sin discos administrados*.
    - Un **ámbito**: un ámbito determina en qué recursos o agrupación de recursos se aplica la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos.

    Use la suscripción (o el grupo de recursos) que registró anteriormente. En este ejemplo se usa el identificador de suscripción **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** y el nombre del grupo de recursos **FabrikamOMS**. No olvide cambiar estos valores por el identificador de la suscripción y el nombre del grupo de recursos con los que trabaja.

    El comando debe ser similar al siguiente:

    ```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

Una asignación de directiva es una directiva que se asignó para que se lleve a cabo dentro de un ámbito específico. Este ámbito también puede abarcar desde un grupo de administración a un grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificación de recursos no compatibles

Para ver los recursos que no son compatibles con esta nueva asignación:

1. Vuelva a la página de Azure Policy.
2. Seleccione **Cumplimiento** en el panel izquierdo y busque la **asignación de directiva** que creó.

   ![Cumplimiento de directivas](media/assign-policy-definition/policy-compliance.png)

   Los recursos existentes incompatibles con la nueva asignación aparecen en la pestaña **Recursos no compatibles**. La imagen anterior muestra ejemplos de recursos no compatibles.

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de esta colección se basan en esta guía de inicio rápido. Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, elimine la asignación que creó mediante la ejecución de este comando:

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>pasos siguientes

En este inicio rápido, se asigna una definición de directiva para identificar los recursos incompatibles en el entorno de Azure.

Para obtener información sobre la asignación de directivas, para garantizar la compatibilidad de los recursos creados en el **futuro**, continúe con este tutorial para:

> [!div class="nextstepaction"]
> [Crear y administrar directivas](./create-manage-policy.md)
