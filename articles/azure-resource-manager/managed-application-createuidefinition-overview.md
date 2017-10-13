---
title: "Información sobre la creación de definiciones de interfaz de usuario para aplicaciones administradas de Azure | Microsoft Docs"
description: "Describe cómo crear definiciones de interfaz de usuario para aplicaciones administradas de Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.openlocfilehash: 077012a2fe9b4b6e6e042fdeb9be2b1d37685bc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-createuidefinition"></a>Introducción a CreateUiDefinition
Este documento presenta los conceptos básicos de CreateUiDefinition, que Azure Portal utiliza para generar la interfaz de usuario para crear una aplicación administrada.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition siempre contiene tres propiedades: 

* handler
* versión
* parameters

Para las aplicaciones administradas, handler siempre debería ser `Microsoft.Compute.MultiVm`, y la versión más reciente admitida es `0.1.2-preview`.

El esquema de la propiedad parameters depende de la combinación de los valores de handler y version especificados. Para las aplicaciones administradas, las propiedades admitidas son `basics`, `steps` y `outputs`. Las propiedades basics y steps contienen los _elementos_, como cuadros de texto y listas desplegables, que se mostrarán en Azure Portal. La propiedad outputs se utiliza para asignar los valores de salida de los elementos especificados a los parámetros de la plantilla de implementación de Azure Resource Manager.

Se recomienda incluir `$schema`, aunque es opcional. Si se especifica, el valor de `version` debe coincidir con la versión en el identificador URI de `$schema`.

## <a name="basics"></a>Aspectos básicos
El paso basics siempre es el primero del asistente que se genera cuando Azure Portal analiza una instancia de CreateUiDefinition. Además de mostrar los elementos especificados en `basics`, el portal inserta elementos para que los usuarios elijan la suscripción, el grupo de recursos y la ubicación de la implementación. Por lo general, los elementos que consultan los parámetros de toda la implementación, como el nombre de un clúster o las credenciales del administrador, deben ir en este paso.

Si el comportamiento de un elemento depende de la suscripción, el grupo de recursos o la ubicación del usuario, ese elemento no puede utilizarse en basics. Por ejemplo, **Microsoft.Compute.SizeSelector** depende de suscripción del usuario y la ubicación para determinar la lista de los tamaños disponibles. Por lo tanto, **Microsoft.Compute.SizeSelector** solo puede utilizarse en steps. Por lo general, solo los elementos del espacio de nombres **Microsoft.Common** pueden usarse en basics. Sin embargo, sí se permiten algunos elementos de otros espacios de nombres (como **Microsoft.Compute.Credentials**) que no dependen del contexto del usuario.

## <a name="steps"></a>Pasos
La propiedad steps puede contener cero o más pasos adicionales que se mostrarán después de basics, cada uno de los cuales contiene uno o más elementos. Considere la posibilidad de agregar pasos por rol o nivel de aplicación que se está implementando. Por ejemplo, agregue un paso para las entradas de los nodos principales y un paso para los nodos de trabajo de un clúster.

## <a name="outputs"></a>Salidas
Azure Portal usa la propiedad `outputs` para asignar elementos de `basics` y `steps` a los parámetros de la plantilla de implementación de Azure Resource Manager. Las claves de este diccionario son los nombres de los parámetros de plantilla, y los valores son propiedades de los objetos de salida de los elementos a los que se hace referencia.

## <a name="functions"></a>Functions
Similar a las [funciones de plantilla](resource-group-template-functions.md) de Azure Resource Manager (tanto en la sintaxis como en la funcionalidad), CreateUiDefinition proporciona funciones para trabajar con las entradas y salidas de los elementos, así como características tales como los condicionales.

## <a name="next-steps"></a>Pasos siguientes
El esquema de CreateUiDefinition en sí es simple. Su profundidad real se debe a todos los elementos y funciones admitidos, que se describen con detalle en los siguientes documentos:

- [Elementos](managed-application-createuidefinition-elements.md)
- [Funciones](managed-application-createuidefinition-functions.md)

Hay disponible un esquema JSON actual para CreateUiDefinition aquí: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Las versiones posteriores estarán disponibles en la misma ubicación. Reemplace la parte `0.1.2-preview` de la dirección URL y el valor de `version` por el identificador de versión que va a utilizar. Los identificadores de versión actualmente admitidos son `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview` y `0.1.2-preview`.