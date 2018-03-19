---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
Las [directivas de Azure](/azure/azure-policy/) ayudan a asegurarse de que todos los recursos de la suscripción satisfacen los estándares corporativos. El uso de directivas reduce los costos al restringir las opciones de implementación únicamente a los tipos de recursos y las SKU que están aprobadas. Se definen reglas y acciones para los recursos y esas reglas se aplican automáticamente durante la implementación. Por ejemplo, puede controlar los tipos de recursos que se implementan. O, puede restringir las ubicaciones aprobadas de los recursos. Algunas directivas deniegan una acción y otras configuran la auditoría de una acción.

La directiva es complementaria al control de acceso basado en rol (RBAC). RBAC se centra en el acceso de usuario y es, de forma predeterminada, un sistema de denegación y permiso explícito. La directiva hace hincapié en las propiedades de los recursos durante y después de la implementación. Se trata de un sistema predeterminado de permiso y denegación explícita.

Hay dos conceptos que es necesario comprender con las directivas: *definiciones de directivas* y *asignaciones de directivas*. Una definición de directiva describe las condiciones de administración que se quieren aplicar. Una asignación de directiva traslada a la práctica una definición de directiva en un ámbito determinado.

![Asignación de directivas](./media/resource-manager-governance-policy/policy-concepts.png)

Azure proporciona varias definiciones de directivas integradas que se pueden usar sin necesidad de modificar nada. Para especificar los valores que se permiten en su ámbito, se deben pasar valores de parámetros. Si la definición de directiva integrada no satisface completamente sus requisitos, puede [crear definiciones de directivas personalizadas](../articles/azure-policy/create-manage-policy.md).
