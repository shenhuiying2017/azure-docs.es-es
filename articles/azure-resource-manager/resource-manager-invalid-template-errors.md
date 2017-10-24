---
title: "Errores de plantilla no válida de Azure | Microsoft Docs"
description: "Describe cómo resolver errores de plantilla no válida."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-invalid-template"></a>Resolución de errores de plantilla no válida

Ese artículo describe cómo resolver errores de plantilla no válida.

## <a name="symptom"></a>Síntoma

Al implementar una plantilla, recibe un error que indica:

```
Code=InvalidTemplate
Message=<varies>
```

El mensaje de error depende del tipo de error.

## <a name="cause"></a>Causa

Este error puede tener distintos orígenes. Normalmente implican un error estructural o de sintaxis en la plantilla.

## <a name="solution"></a>Solución

### <a name="solution-1---syntax-error"></a>Solución 1: error de sintaxis

Si recibe un mensaje de error que indica que la plantilla no superó la validación, puede que esta tenga un problema de sintaxis.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Es fácil cometer este error porque las expresiones de plantilla pueden ser complejas. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Si no proporciona toda la sintaxis de coincidencia, la plantilla generará un valor que será diferente del que esperaba.

Si recibe este tipo de error, revise cuidadosamente la sintaxis de las expresiones. Considere la posibilidad de utilizar un editor JSON como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) que puede avisarlo de los errores de sintaxis.

### <a name="solution-2---incorrect-segment-lengths"></a>Solución 2: longitudes de segmentos incorrectas

Cuando el nombre del recurso no tiene el formato correcto, se produce otro error de plantilla no válida.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Un recurso de nivel de raíz debe tener un segmento menos en el nombre que en el tipo de recurso. Cada segmento se distingue por una barra diagonal. En el ejemplo siguiente, el tipo tiene 2 segmentos y el nombre, 1, por lo que es un **nombre válido**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Pero el ejemplo siguiente **no es un nombre válido** porque el nombre tiene el mismo número de segmentos que el tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Para los recursos secundarios, el tipo y el nombre deben tener el mismo número de segmentos. Este número de segmentos tiene sentido, ya que el tipo y el nombre completo del elemento secundario incluyen el tipo y el nombre del elemento primario. Por lo tanto, el nombre completo sigue teniendo un segmento menos que el tipo completo.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Obtener los segmentos correctos puede resultar complicado con los tipos de Resource Manager que se aplican en los proveedores de recursos. Por ejemplo, para aplicar un bloqueo de recurso a un sitio web, el tipo debe tener 4 segmentos. Por lo tanto, el nombre tiene 3 segmentos:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>Solución 3: el parámetro no es válido

Si la plantilla especifica los valores permitidos de un parámetro y proporciona un valor que no es uno de esos, recibirá un mensaje similar al siguiente error:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Compruebe los valores permitidos de la plantilla y proporcione uno durante la implementación.

### <a name="solution-4---circular-dependency-detected"></a>Solución 4: se detectó una dependencia circular

Este error se recibe cuando los recursos dependen unos de otros de una forma que impide que se inicie la implementación. Una combinación de interdependencias hace que dos o más recursos esperen otros recursos que también están esperando. Por ejemplo, resource1 depende de resource3, resource2 depende de resource1 y resource3 depende de resource2. Para resolver este problema, normalmente se eliminan las dependencias innecesarias.
