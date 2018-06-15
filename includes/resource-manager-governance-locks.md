---
title: archivo de inclusión
description: archivo de inclusión
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
ms.locfileid: "29817031"
---
Los bloqueos de recursos impiden que los usuarios de una organización eliminen o modifiquen de forma accidental recursos críticos. A diferencia del control de acceso basado en rol, los bloqueos de recursos aplican una restricción a todos los usuarios y roles. 

Puede establecer el bloqueo de nivel en **CanNotDelete** o **ReadOnly**. En el portal, los niveles de bloqueo se muestran como **Delete** y **Read-only**, respectivamente.

* **CanNotDelete** significa que los usuarios autorizados pueden leer y modificar recursos, pero no eliminarlos. 
* **ReadOnly** significa que los usuarios autorizados solo pueden leer recursos, pero no actualizarlos ni eliminarlos. Aplicar este bloqueo es similar a restringir todos los usuarios autorizados a los permisos concedidos por el rol **Lector**. 

> [!TIP]
> Tenga cuidado al aplicar un bloqueo **ReadOnly**. Algunas operaciones que parecen de solo lectura requieren en realidad acciones adicionales. Por ejemplo, un bloqueo **ReadOnly** en una cuenta de almacenamiento impide que todos los usuarios muestren las claves. La operación de visualización claves se administra mediante solicitudes POST debido a que las claves devueltas están disponibles para las operaciones de escritura. Un bloqueo **ReadOnly** en un recurso de App Service evita que el Explorador de servidores de Visual Studio muestre los archivos del recurso, ya que esa interacción requiere acceso de escritura.

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos heredan el mismo bloqueo. Incluso los recursos que agregue posteriormente heredan el bloqueo del elemento primario. El bloqueo más restrictivo de toda la herencia tiene prioridad.

Los bloqueos de Resource Manager solo se aplican a las operaciones que se producen en el plano de la administración, que consta de las operaciones enviadas a `https://management.azure.com`. Los bloqueos no restringen cómo los recursos procesan sus propias funciones. Los cambios de recursos están restringidos, pero no así las operaciones de recursos. Por ejemplo, un bloqueo ReadOnly en una instancia de SQL Database impide que elimine o modifique la base de datos. Pero no le impide crear, actualizar o eliminar datos de la base de datos. Las transacciones de datos se permiten porque esas operaciones no se envían a `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quién puede crear o eliminar bloqueos en su organización
Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones `Microsoft.Authorization/locks/*`. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**.
