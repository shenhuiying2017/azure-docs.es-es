---
title: Informes de actividad de inicio de sesión en el portal de Azure Active Directory | Microsoft Docs
description: Introducción a los informes de actividad de inicio de sesión en el portal de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/17/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 78d3399aab8e3c3b1d98946cb3ac6ffab353d95c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257720"
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

## <a name="prerequisites"></a>requisitos previos

### <a name="who-can-access-the-data"></a>¿Quién puede acceder a los datos?
* Usuarios de los roles Administrador de seguridad, Lector de seguridad o Lector de informes
* Administradores globales
* Cualquier usuario (no administradores) puede acceder a sus propios inicios de sesión 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>¿Qué licencia de Azure AD se necesita para acceder a la actividad de inicio de sesión?
* El inquilino debe tener una licencia de Azure AD Premium asociada para ver el informe de actividades de inicio de sesión activas


## <a name="sign-in-activities"></a>Actividades de inicio de sesión

Con la información proporcionada por el informe de inicio de sesión del usuario, puede encontrar respuestas a preguntas tales como:

* ¿Cuál es el patrón de inicio de sesión de un usuario?
* ¿Cuántos usuarios han iniciado sesión en una semana?
* ¿Cuál es el estado de estos inicios de sesión?

El primer punto de entrada a todos los datos de actividades de inicio de sesión es **Inicios de sesión** en la sección Actividad de **Azure Active**.


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/61.png "Actividad de inicio de sesión")


Un registro de inicios de sesión tiene una vista de lista predeterminada que muestra:

- La fecha de inicio de sesión
- El usuario relacionado
- La aplicación en la que el usuario ha iniciado sesión
- El estado de inicio de sesión
- El estado de la detección de riesgos
- El estado del requisito de la autenticación multifactor (MFA) 

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/01.png "Actividad de inicio de sesión")

Puede personalizar la vista de lista; para ello, haga clic en **Columnas** en la barra de herramientas.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/19.png "Actividad de inicio de sesión")

Esto le permite mostrar los campos adicionales o quitar los campos que ya se están mostrando.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/02.png "Actividad de inicio de sesión")

Si hace clic en un elemento de la vista de lista, puede obtener todos los detalles disponibles sobre él en una vista horizontal.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/03.png "Actividad de inicio de sesión")


## <a name="filter-sign-in-activities"></a>Filtrado de las actividades de inicio de sesión

Para restringir los datos del informe a un nivel que se adapte a sus necesidades, puede filtrar los datos de inicio de sesión con los siguientes campos predeterminados:

- Usuario
- Application
- Estado de inicio de sesión
- Estado de la detección de riesgos
- Date


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/04.png "Actividad de inicio de sesión")

El filtro **usuario** permite especificar el nombre o el nombre principal de usuario (UPN) del usuario que le interesa. 

El filtro **aplicación** permite especificar el nombre de la aplicación que le interesa.  

El filtro **estado de inicio de sesión** le permite seleccionar:

- Todo 
- Correcto
- Error

El filtro **Riesgo detectado** le permite seleccionar:

- Todo
- Sí
- Sin  


El filtro **Fecha** le permite definir un período de tiempo para los datos devueltos.  
Los valores posibles son:

- 1 mes
- 7 días
- 24 horas
- Intervalo de tiempo personalizado

Cuando se selecciona un intervalo de tiempo personalizado, puede configurar una hora de inicio y una hora de finalización.

Si agrega campos adicionales a la vista de inicios de sesión, estos campos se agregarán automáticamente a la lista de filtros. Por ejemplo, si agrega el campo **Aplicación cliente** a la lista, también obtendrá otra opción de filtro que le permitirá establecer los siguientes filtros:

- Browser      
- Exchange ActiveSync (compatible)               
- Exchange ActiveSync (no compatible)
- Otros clientes               
    - IMAP
    - MAPI
    - Clientes antiguos de Office
    - POP
    - SMTP


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/12.png "Actividad de inicio de sesión")


> [!TIP] 
> Además de los filtros predeterminados, todos los campos adicionales que se agregan a la vista de inicios de sesión se convierten en campos de filtro.


## <a name="download-sign-in-activities"></a>Descarga de actividades de inicio de sesión

Puede descargar los datos de las actividades de inicio de sesión si desea trabajar con ellos fuera de Azure Portal. Además de un botón de descarga, Azure Portal también proporciona una opción para generar un script para descargar los datos.  

![Descargar](./media/active-directory-reporting-activity-sign-ins/71.png "Descargar")

Si hace clic en **Descargar** se crea un archivo CSV con los 5000 registros más recientes. Si necesita más flexibilidad, puede usar la solución de script. Si hace clic en **Script** se crea un script que incluye todos los filtros que ha establecido. Además de la implementación técnica, el número de registros que se puede descargar también está restringido por las [directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md).  



## <a name="sign-in-activities-shortcuts"></a>Métodos abreviados de las actividades de inicio de sesión

Además de Azure Active Directory, Azure Portal proporciona dos puntos de entrada adicionales para datos de actividades de inicio de sesión:

- Información general sobre la protección de la seguridad de la identidad
- Usuarios
- Grupos
- Aplicaciones empresariales


### <a name="users-sign-ins-activities"></a>Actividades de inicios de sesión de usuarios

Con la información proporcionada por el informe de inicio de sesión del usuario, puede encontrar respuestas a preguntas tales como:

- ¿Cuál es el patrón de inicio de sesión de un usuario?
- ¿Cuántos usuarios tienen usuarios que han iniciado sesión durante una semana?
- ¿Cuál es el estado de estos inicios de sesión?



El punto de entrada a estos datos es el gráfico de inicio de sesión del usuario de la página de información general de **protección de la seguridad de la identidad**. El gráfico de inicio de sesión de usuario muestra agregaciones semanales de inicios de sesión para todos los usuarios en un período determinado. El valor predeterminado para el período es 30 días.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/06.png "Actividad de inicio de sesión")

Al hacer clic en un día del gráfico de inicio de sesión, obtiene una vista general de las actividades de inicio de sesión de ese día.


Cada fila de la lista de actividades de inicio de sesión muestra:

* ¿Quién ha iniciado sesión?
* ¿Qué aplicación era el destino del inicio de sesión?
* ¿Cuál es el estado del inicio de sesión?
* ¿Cuál es el estado de MFA del inicio de sesión?

Si hace clic en un elemento, obtendrá más detalles sobre la operación de inicio de sesión:

- Id. de usuario
- Usuario
- Nombre de usuario
- Identificador de aplicación
- Application
- Cliente
- Ubicación
- Dirección IP
- Date
- Se requiere MFA
- Estado de inicio de sesión

 
En la página **Usuarios**, puede obtener una vista general completa de todos los inicios de sesión del usuario haciendo clic en **Inicios de sesión** en la sección **Actividad**.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/08.png "Actividad de inicio de sesión")




## <a name="usage-of-managed-applications"></a>Uso de las aplicaciones administradas

Con una vista centrada en la aplicación de los datos de inicio de sesión, puede responder a preguntas tales como:

* ¿Quién está usando mis aplicaciones?
* ¿Cuáles son las tres aplicaciones principales en su organización?
* Recientemente he implementado una aplicación. ¿Cómo sigue?

El punto de entrada a los datos son las *tres aplicaciones principales de su organización en el informe de los últimos 30 días* en la sección **Introducción** de la página **Aplicaciones empresariales**.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/10.png "Actividad de inicio de sesión")

Agregaciones semanales del gráfico de uso de la aplicación de inicios de sesión para las tres aplicaciones principales en un período determinado. El valor predeterminado para el período es 30 días.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/47.png "Actividad de inicio de sesión")

Si lo desea, puede establecer el foco en una aplicación específica.


![Informes](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Informes")

Al hacer clic en un día del gráfico de uso de la aplicación, obtendrá una lista detallada de las actividades de inicio de sesión.




La opción **Inicios de sesión** ofrece una descripción completa de todos los eventos de inicio de sesión para sus aplicaciones.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins/11.png "Actividad de inicio de sesión")



## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información acerca de los códigos de error de las actividades de inicio de sesión, consulte [Códigos de error de informes de actividad de inicio de sesión en el portal de Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

