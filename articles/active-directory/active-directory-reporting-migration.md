---
title: "Búsqueda de informes de actividad en Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo buscar informes de actividad en Azure Portal"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 800397efdf2e3e3cb95c77bc90f45bb8852e1e42
ms.openlocfilehash: d05cf7dc62518ea5519f319a2502e1d261b148f3
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Búsqueda de informes de actividad en Azure Portal

Con la migración desde el Portal de Azure clásico a Azure Portal, se ofrece un nuevo aspecto a los registros de actividad de Azure Active Directory. Hace un par de meses se publicó una [entrada de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) donde se explica cómo se proporcionan registros de actividad en el contexto del recurso con el que trabaja. En este artículo se describe cómo se han adaptado los informes existentes en el Portal de Azure clásico al nuevo entorno de Azure Portal.

## <a name="what-is-new"></a>Novedades

Los informes del Portal de Azure clásico se dividen en varias categorías:

1.    Informes de seguridad
2.    Informes de actividad
3.    Informes de aplicaciones integradas

### <a name="activity-and-integrated-app-reports"></a>Informes de actividad y de aplicaciones integradas

Al mover los informes basados en contexto a Azure Portal, se han combinado los informes existentes en una única vista con una sola API subyacente que proporciona los datos a la vista. Puede encontrar esta vista en "*Actividades*" > Registros de auditoría en Azure Portal (panel de navegación izquierdo). Estos son los informes que se han consolidado en esta vista.

-    Informe de auditoría

-     Actividad de restablecimiento de contraseña

-     Actividad de registro de restablecimiento de contraseñas

-     Actividad de los grupos de autoservicio

-     Cambios de nombre de grupo de Office365

-     Actividad de aprovisionamiento de cuentas

-     Estado de la sustitución de contraseña
-     Errores de aprovisionamiento de cuentas


El informe de uso de aplicaciones se ha mejorado y se ha incluido en una vista denominada "*Actividades*"-> Inicios de sesión (panel de navegación izquierdo) e incluye todos los inicios de sesión de los usuarios que, a su vez, se pueden derivar para obtener la información de uso de aplicaciones. Puede encontrar información de uso de aplicaciones a través de la pantalla de información general "Aplicaciones empresariales".

## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>¿Cómo puedo acceder a un informe específico en esta vista única?

### <a name="audit-logs"></a>Registros de auditoría

Una de las preguntas clave de muchos clientes ha sido la capacidad de tener varias opciones de filtro para acceder a los registros de actividad en Azure AD. En su lugar, se ha habilitado un mecanismo de filtrado avanzado para poder filtrar los datos deseados. Uno de los filtros proporcionados se llama “*Categoría de actividad*”, que enumera los diferentes tipos de actividad de los registros de actividad que Azure AD ofrece. Si elige la categoría que desee, puede limitar los resultados de los registros de actividad a aquellos que le interesa ver. 

Por ejemplo, si solo le interesa obtener las actividades relacionadas con el **Autoservicio de restablecimiento de contraseña**, puede elegir la categoría **Self-service Password Management** (Administración de contraseñas de autoservicio). Las categorías que puede ver se engloban en el contexto del recurso con el que trabaja.  


![Registros de auditoría](./media/active-directory-reporting-migration/06.png "Registros de auditoría")

 
Las distintas categorías disponibles actualmente son:

- Core Directory (Directorio principal)

- Self-service Password Management (Administración de contraseñas de autorservicio)

- Self-service Group Management (Administración de grupos de autoservicio)

- Account Provisioning (Aprovisionamiento de cuentas)

### <a name="application-usage"></a>Uso de la aplicación

Puede consultar el uso de todas las aplicaciones o de una sola aplicación a través de la vista Actividades -> Inicios de sesión. Tal y como se muestra a continuación, esta vista está presente para todas las aplicaciones o para una sola aplicación. Puede filtrar por el nombre de usuario o el nombre de aplicación si desea restringir los resultados.
 

![Registros de auditoría](./media/active-directory-reporting-migration/07.png "Registros de auditoría")


### <a name="security-reports"></a>Informes de seguridad

Los informes de seguridad se han consolidado para proporcionar una vista de todas las señales de riesgo que Azure Active Directory puede detectar y notificar.

Esta vista consolidada proporciona datos sobre:

- Usuarios en riesgo
- Inicios de sesión no seguros 


![Registros de auditoría](./media/active-directory-reporting-migration/04.png "Registros de auditoría")



## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Informes de actividad en el Portal de Azure clásico frente a Azure Portal

En esta sección se enumeran los informes existentes en el Portal de Azure clásico y cómo puede obtener esta información en Azure Portal.

**Registros de auditoría:**

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Directorio principal**. 

**Actividad de restablecimiento de contraseña:**

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Self service Password Management** (Administración de contraseñas de autoservicio). 

**Actividad de registro de restablecimiento de contraseñas:**    

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Self service Password Management** (Administración de contraseñas de autoservicio).

**Actividad de los grupos de autoservicio:**    

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Administración de grupos de autoservicio**.

**Cambios de nombre de grupo de Office365:**

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Self service Password Management** (Administración de contraseñas de autoservicio).
3. Como **Activity Resource Type** (Tipo de recurso de actividad), seleccione **Group** (Grupo). 
4. Como **Origen de la actividad**, seleccione **O365 groups** (Grupos de Office&365;). 

**Actividad de aprovisionamiento de cuentas:**    

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Account User Provisioning** (Aprovisionamiento de usuarios de la cuenta).

**Estado de la sustitución de contraseña:**    

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Automatic App Password Rollover** (Sustitución automática de contraseñas de la aplicación).

**Errores de aprovisionamiento de cuentas:**

1. En el panel de navegación izquierdo, haga clic en **Actividades** y, luego, en **Registros de auditoría**.
2. Como **Categoría de actividad**, seleccione **Account User Provisioning** (Aprovisionamiento de usuarios de la cuenta).

**Uso de la aplicación:**

- En el panel de navegación izquierdo, haga clic en **Aplicaciones empresariales** y, luego, haga clic en **Inicios de sesión**



