---
title: "Informes de actividad de inicio de sesión en el portal de Azure Active Directory | Microsoft Docs"
description: "Introducción a los informes de actividad de inicio de sesión en el portal de Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b9e61950654ba427b09dd608d354589a0804aaa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Informes de actividad de inicio de sesión en el portal de Azure Active Directory

Con los informes de Azure Active Directory (Azure AD) que encontrará en [Azure Portal](https://portal.azure.com), puede obtener toda la información que necesita para determinar cómo marcha el entorno.

La arquitectura de los informes de Azure Active Directory consta de los siguientes componentes:

- **Actividad** 
    - **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
    - **Registros de auditoría**: información de la actividad del sistema sobre los usuarios y la administración de grupos, sus aplicaciones administradas y actividades de directorio.
- **Seguridad** 
    - **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte Inicios de no seguros.
    - **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte la sección Usuarios marcados en riesgo.

Este tema ofrece una visión general de las actividades de inicio de sesión.

## <a name="pre-requisite"></a>Requisito previo

### <a name="who-can-access-the-data"></a>¿Quién puede acceder a los datos?
* Usuarios de los roles de administrador o lector de seguridad
* Administradores globales
* Cualquier usuario (no administradores) puede acceder a sus propios inicios de sesión 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>¿Qué licencia de Azure AD se necesita para acceder a la actividad de inicio de sesión?
* El inquilino debe tener una licencia de Azure AD Premium asociada para ver el informe de actividades de inicio de sesión activas


## <a name="signs-in-activities"></a>Actividades de inicio de sesión

Con la información proporcionada por el informe de inicio de sesión del usuario, puede encontrar respuestas a preguntas tales como:

* ¿Cuál es el patrón de inicio de sesión de un usuario?
* ¿Cuántos usuarios tienen usuarios que han iniciado sesión durante una semana?
* ¿Cuál es el estado de estos inicios de sesión?

El primer punto de entrada a todos los datos de actividades de inicio de sesión es **Inicios de sesión** en la sección Actividad de **Azure Active**.


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/61.png "Actividad de inicio de sesión")


Un registro de auditoría tiene una vista de lista predeterminada que muestra:

- el usuario relacionado,
- la aplicación en que el usuario ha iniciado sesión,
- el estado de inicio de sesión,
- la hora de inicio de sesión.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/41.png "Actividad de inicio de sesión")

Puede personalizar la vista de lista; para ello, haga clic en **Columnas** en la barra de herramientas.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/19.png "Actividad de inicio de sesión")

Esto le permite mostrar los campos adicionales o quitar los campos que ya se están mostrando.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/42.png "Actividad de inicio de sesión")

Si hace clic en un elemento de la vista de lista, puede obtener todos los detalles disponibles sobre él.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/43.png "Actividad de inicio de sesión")


## <a name="filtering-sign-in-activities"></a>Filtrado de actividades de inicio de sesión

Para restringir los datos del informe a un nivel que se adapte a sus necesidades, puede filtrar los datos de inicio de sesión con los siguientes campos:

- Intervalo de tiempo
- Usuario
- Application
- Cliente
- Estado de inicio de sesión

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/44.png "Actividad de inicio de sesión")


El filtro **Intervalo de tiempo** permite definir un período de tiempo para los datos devueltos.  
Los valores posibles son:

- 1 mes
- 7 días
- 24 horas
- Personalizado

Cuando se selecciona un intervalo de tiempo personalizado, puede configurar una hora de inicio y una hora de finalización.

El filtro **usuario** permite especificar el nombre o el nombre principal de usuario (UPN) del usuario que le interesa.

El filtro **aplicación** permite especificar el nombre de la aplicación que le interesa.

El filtro **cliente** permite especificar información sobre el dispositivo que le interesa.

El filtro **estado de inicio de sesión** permite seleccionar uno de los filtros siguientes:

- Todo
- Correcto
- Error


## <a name="sign-in-activities-shortcuts"></a>Métodos abreviados de las actividades de inicio de sesión

Además de Azure Active Directory, Azure Portal proporciona dos puntos de entrada adicionales para datos de actividades de inicio de sesión:

- Usuarios y grupos
- Aplicaciones empresariales


### <a name="users-and-groups-sign-ins-activities"></a>Actividades de inicios de sesión de usuarios y grupos

Con la información proporcionada por el informe de inicio de sesión del usuario, puede encontrar respuestas a preguntas tales como:

- ¿Cuál es el patrón de inicio de sesión de un usuario?
- ¿Cuántos usuarios tienen usuarios que han iniciado sesión durante una semana?
- ¿Cuál es el estado de estos inicios de sesión?



El punto de entrada a los datos es el gráfico de inicio de sesión del usuario en la sección **Introducción** de **Usuarios y grupos**.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/45.png "Actividad de inicio de sesión")

El gráfico de inicio de sesión de usuario muestra agregaciones semanales de inicios de sesión para todos los usuarios en un período determinado. El valor predeterminado para el período es 30 días.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/46.png "Actividad de inicio de sesión")

Al hacer clic en un día en el gráfico de inicio de sesión, obtiene una lista detallada de las actividades de inicio de sesión de ese día.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/41.png "Actividad de inicio de sesión")

Cada fila de la lista de actividades de inicio de sesión le ofrece la información detallada acerca del inicio de sesión seleccionado como:

* ¿Quién ha iniciado sesión?
* ¿Cuál era el UPN relacionado?
* ¿Qué aplicación era el destino del inicio de sesión?
* ¿Cuál es la dirección IP del inicio de sesión?
* ¿Cuál es el estado del inicio de sesión?

La opción **Inicios de sesión** ofrece información general completa sobre todos los inicios de sesión de usuarios.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/51.png "Actividad de inicio de sesión")



## <a name="usage-of-managed-applications"></a>Uso de las aplicaciones administradas

Con una vista centrada en la aplicación de los datos de inicio de sesión, puede responder a preguntas tales como:

* ¿Quién está usando mis aplicaciones?
* ¿Cuáles son las tres aplicaciones principales en su organización?
* Recientemente he implementado una aplicación. ¿Cómo sigue?

El punto de entrada a los datos son las tres aplicaciones principales de su organización en el informe de los últimos 30 días en la sección **Introducción** en **Aplicaciones empresariales**.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/64.png "Actividad de inicio de sesión")

Agregaciones semanales del gráfico de uso de la aplicación de inicios de sesión para las tres aplicaciones principales en un período determinado. El valor predeterminado para el período es 30 días.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/47.png "Actividad de inicio de sesión")

Si lo desea, puede establecer el foco en una aplicación específica.


![Informes](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Informes")

Al hacer clic en un día del gráfico de uso de la aplicación, obtendrá una lista detallada de las actividades de inicio de sesión.


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/48.png "Actividad de inicio de sesión")


La opción **Inicios de sesión** ofrece una descripción completa de todos los eventos de inicio de sesión para sus aplicaciones.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/49.png "Actividad de inicio de sesión")



## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información acerca de los códigos de error de las actividades de inicio de sesión, consulte [Códigos de error de informes de actividad de inicio de sesión en el portal de Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

