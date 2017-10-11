---
title: Azure Portal para directivas de recursos| Microsoft Docs
description: "Describe cómo usar Azure Portal para crear y administrar directivas de Resource Manager. Las directivas pueden aplicarse a la suscripción o a los grupos de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Uso de Azure Portal para asignar y administrar directivas de recursos
Azure Portal permite asignar directivas de recursos a suscripciones y grupos de recursos. La interfaz de usuario facilita la selección de la directiva que desea asignar y especificar los valores de los parámetros de dicha directiva para personalizar la configuración de directiva. 

Para asignar una directiva a través del portal, la definición de la directiva debe existir en la suscripción. La suscripción tiene varias definiciones de directivas integradas que están listas para asignarlas a grupos de recursos o suscripciones. Se ven estas directivas integradas y todas las directivas personalizadas que ha definido al usar el portal para asignar directivas. Para obtener una introducción a las directivas y a cómo definir una directiva personalizada, consulte [Información general sobre las directivas de recursos](resource-manager-policy.md).

Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos. En este artículo, el término **ámbito** hace referencia al grupo de recursos o a la suscripción a los que se asigna la directiva. 

Las directivas se evalúan al crear y actualizar los recursos (operaciones PUT y PATCH).

## <a name="assign-a-policy"></a>Asignación de una directiva

1. Para asignar una directiva a un grupo de recursos o a una suscripción, selecciónelos. En la configuración, seleccione **Directivas**.

   ![seleccionar directivas](./media/resource-manager-policy-portal/select-policies.png)

2. Para crear una asignación de directiva para este ámbito, seleccione **Agregar asignación**.

   ![agregar asignación](./media/resource-manager-policy-portal/add-assignment.png)

3. Seleccione la directiva que desea asignar. Aunque no haya agregado ninguna definición de directiva a su suscripción, verá las directivas integradas disponibles para su asignación. Estas directivas integradas abarcan muchos escenarios comunes.

   ![seleccionar definición](./media/resource-manager-policy-portal/select-definition.png)

4. Después de seleccionar una directiva, verá su descripción y sus parámetros. Por ejemplo, la siguiente imagen muestra el parámetro **Ubicaciones permitidas**, que se requiere para la directiva que restringe las ubicaciones disponibles.

   ![mostrar parámetros](./media/resource-manager-policy-portal/show-parameters.png)

5. A través de la interfaz de usuario, seleccione los valores que se especifican para los parámetros de la directiva (como las ubicaciones que se pueden usar para la implementación).

   ![seleccionar valores de parámetros](./media/resource-manager-policy-portal/select-parameters.png)

6. Especifique los valores de los otros parámetros. El ámbito se asigna automáticamente en función de la hoja que seleccionó al iniciar la asignación de la directiva. Seleccione **Aceptar** cuando haya terminado.

   ![definir parámetros](./media/resource-manager-policy-portal/define-parameters.png)

  Ha asignado la directiva al ámbito especificado.

## <a name="view-policy-assignments"></a>Visualización de asignaciones de directiva

Después de asignar una directiva, la verá en la lista de directivas de dicho ámbito. La pestaña **Detalles** muestra un resumen de la asignación de la directiva.

![mostrar detalles](./media/resource-manager-policy-portal/show-details.png)

La pestaña **Regla de asignación** muestra el JSON de la definición de la directiva.

![mostrar regla de asignación](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Cambio de una asignación de directiva existente

Para cambiar una directiva, seleccione **Editar asignación** o **Eliminar**

![editar o eliminar asignación](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Asignación de directivas personalizadas

Si ha definido directivas personalizadas en su suscripción, estarán disponibles para su asignación a través del portal. Delante de dichas políticas aparece **[Custom]**

![directivas personalizadas](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información acerca de la sintaxis JSON para definir directivas, consulte [Información general sobre las directivas de recursos](resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* El esquema de la directiva está publicado en [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

