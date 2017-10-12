---
title: "Informes de actividad de auditoría en el portal de Azure Active Directory | Microsoft Docs"
description: "Introducción a los informes de actividad de auditoría en el portal de Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f2d0332d815c82d7d47625e020de2e9c5099deeb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Informes de actividad de auditoría en el portal de Azure Active Directory 

Con los informes de Azure Active Directory (Azure AD), puede obtener toda la información que necesita para determinar cómo marcha el entorno.

La arquitectura de los informes de Azure AD consta de los siguientes componentes:

- **Actividad** 
    - **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
    - **Registros de auditoría**: información de la actividad del sistema sobre los usuarios y la administración de grupos, sus aplicaciones administradas y actividades de directorio.
- **Seguridad** 
    - **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte Inicios de no seguros.
    - **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte la sección Usuarios marcados en riesgo.

Este tema ofrece una visión general de las actividades de auditoría.
 
## <a name="who-can-access-the-data"></a>¿Quién puede acceder a los datos?
* Usuarios de los roles de administrador o lector de seguridad
* Administradores globales
* Los usuarios individuales (no administradores) pueden ver sus propias actividades


## <a name="audit-logs"></a>Registros de auditoría

Los registros de auditoría de Azure Active Directory proporcionan registros de las actividades del sistema de cara al cumplimiento.  
El primer punto de entrada a todos los datos de auditoría es **Registros de auditoría** en la sección **Actividad** de **Azure Active Directory**.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/61.png "Registros de auditoría")

Un registro de auditoría tiene una vista de lista predeterminada que muestra:

- la fecha y hora de la repetición
- el iniciador/actor (*quién*) de una actividad 
- la actividad (*qué*) 
- el destino

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/18.png "Registros de auditoría")

Puede personalizar la vista de lista, haga clic en **Columnas** en la barra de herramientas.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/19.png "Registros de auditoría")

Esto le permite mostrar los campos adicionales o quitar los campos que ya se están mostrando.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/21.png "Registros de auditoría")


Si hace clic en un elemento de la vista de lista, puede obtener todos los detalles disponibles sobre él.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/22.png "Registros de auditoría")


## <a name="filtering-audit-logs"></a>Filtrado de registros de auditoría

Para restringir los datos del informe a un nivel que se adapte a sus necesidades, puede filtrar los datos de auditoría con los siguientes campos:

- Intervalo de fechas
- Iniciado por (actor)
- Categoría
- Tipo de recurso de actividad
- Actividad

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/23.png "Registros de auditoría")


El filtro **Intervalo de fechas** permite definir un período de tiempo para los datos devueltos.  
Los valores posibles son:

- 1 mes
- 7 días
- 24 horas
- Personalizado

Cuando se selecciona un intervalo de tiempo personalizado, puede configurar una hora de inicio y una hora de finalización.

El filtro **Iniciado por** le permite definir el nombre de un actor o su nombre principal universal (UPN).

El filtro **Categoría** le permite seleccionar uno de los filtros siguientes:

- Todo
- Core category (Categoría principal)
- Core Directory (Directorio principal)
- Self-service Password Management (Administración de contraseñas de autorservicio)
- Self-service group management (Administración de grupos de autoservicio)
- Account provisioning- Automated password rollover (Aprovisionamiento de cuentas: sustitución automática de contraseñas)
- Invited users (Usuarios invitados)
- MIM service (Servicio MIM)
- Identity Protection
- B2C

El filtro **Tipo de recurso de actividad** le permite seleccionar uno de los filtros siguientes:

- Todo 
- Grupo
- Directorio
- Usuario
- Application
- Directiva
- Dispositivo
- Otros

Cuando se selecciona **Grupo** como **Tipo de recurso de actividad**, obtendrá una categoría de filtro adicional que le permite proporcionar también un **Origen**:

- Azure AD
- O365


El filtro **Actividad** se basa en la selección de categoría y de tipo de recurso de actividad que realice. Puede seleccionar la actividad específica que desea ver o elegir todas. 

Para obtener la lista de todas las actividades de auditoría, use API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, donde $tenantdomain = el nombre de dominio, o bien consulte el artículo sobre [eventos del informe de auditoría](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Métodos abreviados de los registros de auditoría

Además de **Azure Active Directory**, Azure Portal proporciona dos puntos de entrada adicionales para auditar datos:

- Usuarios y grupos
- Aplicaciones empresariales

### <a name="users-and-groups-audit-logs"></a>Registros de auditoría de los usuarios y grupos

Con los informes de auditoría basadas en grupos y usuarios, puede obtener respuestas a preguntas como:

- ¿Qué tipos de actualizaciones se han aplicado a los usuarios?

- ¿Cuántos usuarios han cambiado?

- ¿Cuántas contraseñas han cambiado?

- ¿Qué ha hecho un administrador en un directorio?

- ¿Cuáles son los grupos que se han agregado?

- ¿Hay grupos con cambios de pertenencia?

- ¿Se han cambiado los propietarios del grupo?

- ¿Qué licencias se han asignado a un grupo o un usuario?

Si desea revisar los datos de auditoría relacionados con usuarios y grupos, puede buscar una vista filtrada en **Registros de auditoría** en la sección **Actividad** de **Usuarios y grupos**. Este punto de entrada tiene **Usuarios y grupos** como **Tipo de recurso de actividad** preseleccionado.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/93.png "Registros de auditoría")

### <a name="enterprise-applications-audit-logs"></a>Registros de auditoría de aplicaciones empresariales

Con los informes de auditoría basadas en aplicaciones, puede obtener respuestas a preguntas tales como:

* ¿Cuáles son las aplicaciones que se han agregado o actualizado?
* ¿Cuáles son las aplicaciones que se han quitado?
* ¿Ha cambiado el principal de servicio para una aplicación?
* ¿Se han cambiado los nombres de las aplicaciones?
* ¿Quién dio el consentimiento a una aplicación?

Si desea revisar los datos de auditoría relacionados con las aplicaciones, puede buscar una vista filtrada en **Registros de auditoría** en la sección **Actividad** de la hoja **Aplicaciones empresariales**. Este punto de entrada tiene **Aplicaciones empresariales** como **Tipo de recurso de actividad** preseleccionado.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/134.png "Registros de auditoría")

Puede filtrar aún más esta vista hasta simplemente **grupos** o simplemente **usuarios**.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/25.png "Registros de auditoría")


## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre los informes, consulte [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md).

