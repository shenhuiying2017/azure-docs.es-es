---
title: Inicio de una revisión de acceso en PIM para Azure Resources | Microsoft Docs
description: Se explica cómo iniciar una revisión de acceso en Privileged Identity Management para Azure Resources.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management: inicio de la revisión de acceso de roles de recursos
Las asignaciones de roles se convierten en "obsoletas" cuando los usuarios tienen acceso con privilegios que ya no necesitan. Con el fin de reducir el riesgo asociado a estas asignaciones de roles obsoletos, los administradores de roles con privilegios deben revisar regularmente los roles que se han concedido a los usuarios. En este documento se describen los pasos para iniciar una revisión de acceso en Privileged Identity Management (PIM) para Azure Resources.

Desde la página principal de la aplicación de PIM, desplácese hasta:

* **Revisiones de acceso** > **Agregar**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Al hacer clic en el botón **Agregar**, aparece la hoja **Create an access review** (Crear una revisión de acceso). En esta hoja, va a configurar la revisión con un nombre y un límite de tiempo, elija un rol para revisar y decida quién llevará a cabo la revisión.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configuración de la revisión
Para crear una revisión de acceso, necesitará nombrarla y establecer una fecha de inicio y finalización.

![Configuración de una revisión: captura de pantalla](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Asegúrese de que la longitud de la revisión sea lo suficientemente larga para que los usuarios la completen. Si finaliza antes de la fecha de finalización, siempre puede detener pronto la revisión.

### <a name="choose-a-role-to-review"></a>Elija un rol para su revisión
Cada revisión se centra solo en un rol. A menos que iniciara la revisión de acceso desde una hoja de rol específica, deberá elegir un rol ahora.

1. Vaya a **Revisar la pertenencia al rol**
   
    ![Revisión de pertenencia al rol: captura de pantalla](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Elija un rol de la lista.

### <a name="decide-who-will-perform-the-review"></a>Decida quién llevará a cabo la revisión
Hay tres opciones para realizar una revisión. Puede asignar la revisión a otra persona para que la complete, puede hacerlo usted mismo o hacer que cada usuario revise su propio acceso.

1. Elija una de las opciones:
   
   * **Usuarios seleccionados**: use esta opción cuando no sepa quién necesita acceso. Con esta opción, puede asignar la revisión a un propietario de recursos o al administrador de grupos.
   * **Asignado (autoasignado)**: use esta opción para hacer que los usuarios revisen sus propias asignaciones de roles.
   
2. Vaya a **Seleccionar revisores**
   
    ![Selección de revisores: captura de pantalla](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Inicio de la revisión
Por último, tiene la opción de requerir que los usuarios proporcionen un motivo si aprueban su acceso. Agregue una descripción de la revisión si lo desea y seleccione **Iniciar**.

Asegúrese de que permiten a los usuarios saber que hay una revisión de acceso esperando para ellos y [cómo realizar una revisión de acceso](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Administración de la revisión de acceso
Puede seguir el progreso de las revisiones a medida que los revisores las realizan en el panel de Azure Resources de PIM, en la sección de revisiones de acceso. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](pim-resource-roles-complete-access-review.md).

Hasta que termine el período de revisión, puede recordar a los usuarios completar su revisión o detener la revisión antes desde la sección de revisiones de acceso.

