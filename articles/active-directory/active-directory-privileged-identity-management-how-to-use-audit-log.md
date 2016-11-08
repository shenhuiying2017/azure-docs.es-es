---
title: Cómo utilizar el registro de auditoría | Microsoft Docs
description: Obtenga información sobre cómo usar el registro de auditoría en la extensión de Privileged Identity Management de Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2016
ms.author: kgremban

---
# Uso del registro de auditoría en Privileged Identity Management de Azure AD
Puede utilizar el registro de auditoría de Privileged Identity Management (PIM) para ver todas las asignaciones de usuario y las activaciones comprendidas en un período de tiempo determinado. Si quiere ver el historial de auditoría completo de actividad en el inquilino, incluido el administrador, el usuario final y la actividad de sincronización, puede usar los [informes de acceso y uso de Azure Active Directory](active-directory-view-access-usage-reports.md).

## Desplazamiento al registro de auditoría
En el panel de [Azure Portal](https://portal.azure.com), seleccione la aplicación **Azure AD Privileged Identity Management**. Desde ahí, acceda al registro de auditoría; para ello, haga clic en **Administrar roles con privilegios** > **Historial de auditorías** en el panel de PIM.

## Gráfico del registro de auditoría
Puede utilizar el registro de auditoría para ver el total de activaciones, el número máximo de activaciones por día y el promedio de activaciones por día en un gráfico de líneas. También puede filtrar los datos por rol si hay más de un rol en el historial de auditoría.

Utilice los botones de **tiempo**, **acción** y **rol** para ordenar el registro.

## Lista del registro de auditoría
Las columnas de la lista del registro de auditoría son:

* **Solicitante**: el usuario que solicitó la activación o el cambio del rol. Si el valor es "Sistema de Azure", compruebe el registro de auditoría de Azure para más información.
* **Usuario**: el usuario que activa el rol o que está asignado al rol.
* **Rol**: el rol asignado al usuario o activado por el usuario.
* **Acción** : las acciones realizadas por el solicitante. Esto puede incluir asignación, desasignación, activación o desactivación.
* **Hora**: momento en que se produjo la acción.
* **Razonamiento**: si durante la activación se escribió texto en el campo de motivo, aparecerá aquí.
* **Caducidad** : solo es importante para la activación de los roles.

## Filtro del registro de auditoría
Puede filtrar la información que aparece en el registro de auditoría haciendo clic en el botón **Filtrar**. Aparecerá la hoja **Actualizar parámetros del gráfico**.

Después de configurar los filtros, haga clic en **Actualizar** para filtrar los datos del registro. Si los datos no aparecen inmediatamente, actualice la página.

### Cambio del intervalo de fechas
Utilice los botones **Hoy**, **Semana pasada**, **Mes pasado** o **Personalizado** para cambiar el intervalo de tiempo del registro de auditoría.

Si elige el botón **Personalizado**, se le ofrece un campo de fecha **Desde** y un campo de fecha **Hasta** para especificar un intervalo de fechas para el registro. Puede especificar las fechas en formato MM/DD/AAAA o hacer clic en el icono de **calendario** y elegir la fecha en un calendario.

### Cambio de los roles incluidos en el registro
Active o desactive la casilla **Rol** situada junto a cada rol que quiera incluir o excluir del registro.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0928_2016-->