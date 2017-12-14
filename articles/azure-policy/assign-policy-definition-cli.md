---
title: "Uso de la CLI de Azure para crear una asignación de directiva para identificar recursos no compatibles en el entorno de Azure | Microsoft Docs"
description: "Use PowerShell para crear una asignación de Azure Policy para identificar recursos no compatibles."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 88ceb47d46b66e716c6c263098d5b9458e4aff22
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Creación de una asignación de directiva para identificar recursos no compatibles en el entorno de Azure con la CLI de Azure

El primer paso para comprender cómo funciona el cumplimiento en Azure consiste en determinar cuál es la situación de los recursos con los que ya cuenta. Este inicio rápido lo guiará por el proceso de creación de una asignación de directiva para identificar máquinas virtuales que no están usando discos administrados.

Al final de este proceso, habrá identificado correctamente qué máquinas virtuales no utilizan discos administrados y, por tanto, *no son compatibles*.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva

En este tutorial rápido, se crea una asignación de directiva y se le asigna la definición de la Auditoría de máquinas virtuales sin discos administrados. Esta definición de directiva identifica los recursos que no son compatibles con las condiciones establecidas en la definición de directiva.

Siga los pasos siguientes para crear una nueva asignación de directiva.

Vea todas las definiciones de directiva, y busque la definición de la directiva de la Auditoría de máquinas virtuales sin discos administrados:

```azurecli
az policy definition list
```

Azure Policy integra ya definiciones de directiva disponibles para usarlas. Encontrará definiciones de directiva integradas como:

- Enforce tag and its value (Forzar una etiqueta y su valor)
- Apply tag and its value (Aplicar una etiqueta y su valor)
- Require SQL Server Version 12.0 (Requerir SQL Server 12.0)

A continuación, proporcione la información siguiente y ejecute el comando siguiente para asignar la definición de directiva:

- **Nombre** para mostrar para la asignación de directiva. En este caso, vamos a usar la *Auditoría de máquinas virtuales sin discos administrados*.
- **Directiva**: se trata de la definición de directiva, según la opción utilizada para crear la asignación. En este caso, es la definición de la directiva: *Auditoría de máquinas virtuales sin discos administrados*.
- Un **ámbito**: un ámbito determina en qué recursos o agrupación de recursos se aplica la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos.

  Use la suscripción (o el grupo de recursos) que registró anteriormente. En este ejemplo se usa este identificador de suscripción, **bc75htn-a0fhsi-349b-56gh-4fghti-f84852**, y el nombre del grupo de recursos **FabrikamOMS**. No olvide cambiar estos valores por el identificador de la suscripción y el nombre del grupo de recursos con los que trabaja.

El comando debe presentar un aspecto similar a este:

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

   Si hay algún recurso existente no compatible con esta nueva asignación, aparece en la pestaña **Non-compliant resources** (Recursos incompatibles), como se mostró anteriormente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de esta colección se basan en esta guía de inicio rápido. Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, elimine la asignación que creó mediante la ejecución de este comando:

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, se asigna una definición de directiva para identificar los recursos incompatibles en el entorno de Azure.

Para obtener información sobre la asignación de directivas, para garantizar la compatibilidad de los recursos creados en el **futuro**, continúe con este tutorial para:

> [!div class="nextstepaction"]
> [Crear y administrar directivas](./create-manage-policy.md)
