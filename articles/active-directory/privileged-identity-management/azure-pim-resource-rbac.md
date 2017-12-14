---
title: "Introducción al control de acceso basado en rol para los recursos de Azure PIM | Microsoft Docs"
description: "Introducción a la característica de control de acceso basado en rol en PIM, terminología y las notificaciones incluidas"
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.openlocfilehash: 19715f800e7d8d40336d8e9fa3bf8073795dce5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="pim-for-azure-resources-preview"></a>PIM para recursos de Azure (versión preliminar)

Con Privileged Identity Management (PIM) (versión preliminar) de Azure Active Directory, puede administrar, controlar y supervisar el acceso a los recursos de Azure dentro de su organización. Esto incluye las suscripciones, los grupos de recursos e incluso las máquinas virtuales. Cualquier recurso de Azure Portal que aproveche la funcionalidad de control de acceso basado en rol (RBAC) de Azure puede aprovechar las excelentes funcionalidades de seguridad y administración del ciclo de vida que ofrece PIM de Azure AD, además de algunas características nuevas muy útiles que tenemos previsto incorporar pronto a los roles de Azure AD. 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>PIM para recursos de Azure (versión preliminar) ayuda a los administradores de recursos

- Consulte qué usuarios y grupos son roles asignados a los recursos de Azure que administra
- Habilite el acceso "Just-In-Time", a petición, para administrar recursos como las suscripciones, los grupos de recursos, etc.
- Anule el acceso de los usuarios /grupos de recursos asignados automáticamente con una nueva configuración de asignación de tiempo
- Asigne acceso temporal a los recursos para tareas rápidas u horarios de guardia
- Establezca Multi-Factor Authentication para los recursos en cualquier rol incorporado o personalizado 
- Obtenga informes sobre la actividad de los recursos relativa al acceso durante una sesión de usuario activa
- Reciba alertas cuando se asigne acceso a los recursos para nuevos usuarios o grupos, así como cuando activen asignaciones aptas

PIM de Azure AD puede administrar los roles incorporados de los recursos de Azure, así como los roles personalizados (RBAC), entre ellos, los siguientes:

- Propietario
- Administrador de acceso de usuario
- Colaborador
- Administrador de seguridad
- Y muchos otros

>[!NOTE]
Los usuarios o los miembros de un grupo asignados a los roles Propietario o Administrador de acceso de usuario y los administradores globales que permiten la administración de suscripciones de Azure AD son administradores de recursos. Estos administradores pueden asignar roles, configurar opciones de rol y revisar el acceso con PIM a los recursos de Azure. Consulte la lista de [roles integrados en los recursos de Azure](../role-based-access-built-in-roles.md).

## <a name="tasks"></a>Tareas

PIM ofrece un acceso cómodo para activar los roles, y ver las activaciones/solicitudes pendientes, las aprobaciones pendientes (para los [roles de directorio de Azure AD](azure-ad-pim-approval-workflow.md)) y las revisiones pendientes de respuesta en la sección Tareas del menú de navegación izquierdo.

Cuando se accede a cualquiera de los elementos del menú Tareas del punto de entrada de Información general, la vista resultante contiene los resultados de los roles de directorio de Azure AD y los roles de los recursos de Azure (versión preliminar). 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Mis roles contiene una lista de las asignaciones de rol activas y válidas para los roles de directorio de Azure AD, así como roles de los recursos de Azure (versión preliminar).

## <a name="activate-roles"></a>Activación de roles

La activación de roles para los recursos de Azure (versión preliminar) presenta una nueva experiencia que permite a los miembros del rol aptos programar la activación de fechas y horas futuras, y seleccionar una duración de activación específica dentro de los límites (que configuren los administradores). Aprenda a [activar roles de Azure AD aquí](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

En el menú Activaciones, escriba la fecha de inicio y la hora deseadas para activar el rol. Si lo desea, reduzca la duración de la activación (el período de tiempo que el rol permanece activo) y escriba una justificación (si procede); haga clic en Activar.

Si no se modifica la fecha de inicio y la hora, el rol se activa en unos segundos. Verá un rol en cola para el mensaje del banner de activación en la página Mis roles. Haga clic en el botón Actualizar para borrar este mensaje.

![](media/azure-pim-resource-rbac/my-roles.png)

Si la activación está programada para una fecha y hora futuras, la solicitud pendiente aparecerá en la pestaña Solicitudes pendientes del menú de navegación izquierdo. En caso de que la activación del rol ya no sea necesaria, el usuario puede cancelar la solicitud; para ello, debe hacer clic en el botón Cancelar del lado derecho de la página.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Búsqueda y administración de recursos de Azure

Para buscar y administrar roles para un recurso de Azure, seleccione Recursos de Azure (versión preliminar) en la pestaña Administrar en el menú de navegación izquierdo. Utilice los filtros o la barra de búsqueda de la parte superior de la página para buscar un recurso.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Paneles de recursos

El panel Vista de administrador consta de cuatro elementos principales. Una representación gráfica de las activaciones de rol de recursos de los últimos siete días. Estos datos se limitan al recurso seleccionado y muestran las activaciones de los roles más comunes (Propietario, Colaborador, Administrador de acceso de usuario) y de la combinación de todos los roles.

A la derecha del grafo de activaciones hay dos gráficos que muestran la distribución de las asignaciones de roles por tipo de asignación, tanto de usuario como de grupo. Al seleccionar un segmento del gráfico, el valor cambia a porcentaje (o viceversa).

![](media/azure-pim-resource-rbac/admin-view.png)

Por debajo de los gráficos, verá el número de usuarios y grupos con nuevas asignaciones de roles de los últimos 30 días (a la izquierda) y una lista de los roles ordenada por asignaciones totales (descendente).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Administración de asignaciones de roles

Los administradores pueden administrar las asignaciones de roles mediante la selección de Roles o Miembros en el panel de navegación izquierdo. La selección de Roles permite a los administradores definir el ámbito de sus tareas de administración a un rol específico, mientras que en Miembros se muestran todas las asignaciones de roles de usuario y de grupo para el recurso.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Si tiene un rol pendiente de activación, se muestra un banner de notificación en la parte superior de la página al ver la pertenencia.

## <a name="assign-roles"></a>Asignación de roles

Para asignar un usuario o grupo a un rol, seleccione el rol (si está en Roles) o haga clic en Agregar en la barra de acción (si se encuentra en la vista de Miembros).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Si va a agregar un usuario o grupo desde la pestaña Miembros, debe seleccionar un rol del menú Agregar para poder seleccionar un usuario o grupo.

![](media/azure-pim-resource-rbac/select-role.png)

Elija un usuario o grupo del directorio.

![](media/azure-pim-resource-rbac/choose.png)

Elija el tipo de asignación correspondiente del menú desplegable. 

**Asignación Just-In-Time:** proporciona acceso apto pero no persistente al rol durante un período determinado de tiempo o indefinidamente (si se ha configurado en Roles) al usuario o los miembros del grupo. 

**Asignación directa:** no requiere que el usuario o los miembros del grupo activen la asignación del rol (esto se conoce como acceso persistente). Microsoft recomienda la asignación directa para usos breves, como los turnos de guardia o actividades temporales, que, una vez terminados, ya no necesitan acceso.

![](media/azure-pim-resource-rbac/membership-settings.png)

Una casilla debajo de la lista desplegable Tipo de asignación permite especificar si la asignación debe ser permanente (siempre apta para la activación Just-In-Time/siempre activa para la asignación directa). Para especificar la duración de una asignación específica, anule la selección de la casilla y modifique los campos de fecha y hora de inicio o finalización.

>[!NOTE]
Si otro administrador ha especificado la duración máxima para cada tipo de asignación en la configuración de los roles, la casilla no admite modificación.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Consulta de la activación y la actividad de los recursos de Azure

Si necesita ver las acciones que realizó un usuario específico en varios recursos, puede revisar la actividad de los recursos de Azure asociada a un período de activación específico (para usuarios aptos). Primero, seleccione el usuario de la vista Miembros o de la lista de miembros de un rol específico. Como resultado se muestra una gráfica de las acciones del usuario en los recursos de Azure por fecha y las activaciones de rol recientes durante ese período.

![](media/azure-pim-resource-rbac/user-details.png)

Al seleccionar la activación de un rol específico se muestran los detalles de la activación del rol y la actividad de los recursos de Azure correspondiente al tiempo de actividad de ese usuario.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Modificación de las asignaciones existentes

Para modificar las asignaciones existentes desde la vista de detalles del usuario o grupo, seleccione Cambiar configuración en la barra de acción de la parte superior de la página. Cambie el tipo de asignación a Just-In-Time o directa.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Comprobación de quién tiene acceso a una suscripción

Para revisar las asignaciones de rol en la suscripción, seleccione la pestaña Miembros en el panel de navegación izquierdo o Roles y elija un rol específico para revisar los miembros. 

Seleccione Revisar en la barra de acción para ver las revisiones de acceso existentes y seleccione Agregar para crear una nueva.

![](media/azure-pim-resource-rbac/owner.png)

[Más información sobre las revisiones de acceso](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Actualmente, solo se admiten revisiones para los tipos de recursos de suscripción.

## <a name="configure-role-settings"></a>Configuración de los roles

En la configuración de los roles se definen los valores predeterminados que se aplican a las asignaciones en el entorno de PIM. Para definirlos para un recurso, seleccione la pestaña Configuración de rol desde el panel izquierdo o el botón de configuración del rol de la barra de acción de cualquier rol para ver las opciones actuales.

Haga clic en Editar de la barra de acción de la parte superior de la página para modificar las opciones de configuración.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Los cambios de configuración se registran en la página de configuración del rol, la fecha y la hora de actualización más recientes y el administrador que realizó el cambio incluidos.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Auditoría de recursos

Auditoría de recursos proporciona una vista de todas las actividades de rol del recurso. Puede filtrar la información con una fecha predefinida o un intervalo personalizado.
![](media/azure-pim-resource-rbac/last-day.png) Auditoría de recursos también proporciona acceso directo a los detalles de la actividad de un usuario. En la vista, todas las acciones de "Activar rol" son vínculos a la actividad de recursos específicos para el usuario que los solicita.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Just Enough Administration

PIM para recursos de Azure facilita la aplicación de los procedimientos recomendados de JEA (Just Enough Administration) en las asignaciones de roles para recursos. Los usuarios y los miembros del grupo con asignaciones a suscripciones o grupos de recursos de Azure pueden activar su asignación de rol existente en un ámbito menor. 

En la página de búsqueda, encuentre el recurso subordinado que necesite administrar.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Seleccione Mis roles en el menú de navegación izquierdo y elija el rol que desee activar. Observe que el tipo de asignación es Heredado, puesto que el rol se asignó a la suscripción, en lugar de al grupo de recursos, tal y como se muestra a continuación.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Pasos siguientes

- [Roles integrados en los recursos de Azure](../role-based-access-built-in-roles.md)
- Obtenga información acerca de la [activación de roles de Azure AD aquí](../active-directory-privileged-identity-management-how-to-activate-role.md).
- [Flujos de trabajo de aprobación de PIM](azure-ad-pim-approval-workflow.md)
