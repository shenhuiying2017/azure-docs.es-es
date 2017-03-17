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
ms.sourcegitcommit: afd48a77faeeab6d5a8730934345cb7000a59831
ms.openlocfilehash: e72e49140aaad55a6e66a9f331ce7fde20b8577c
ms.lasthandoff: 02/23/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Búsqueda de informes de actividad en Azure Portal

Con la migración desde el Portal de Azure clásico a Azure Portal, se ofrece un nuevo aspecto a los registros de actividad de Azure Active Directory. Hace un par de meses se publicó una [entrada de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) donde se explica cómo se proporcionan registros de actividad en el contexto del recurso con el que trabaja. En este artículo se describe cómo se han adaptado los informes existentes en el Portal de Azure clásico al nuevo entorno de Azure Portal.

## <a name="what-is-new"></a>Novedades

Los informes del Portal de Azure clásico se dividen en varias categorías:

1.    Informes de seguridad
2.    Informes de actividad
3.    Informes de aplicaciones integradas

### <a name="activity-and-integrated-app-reports"></a>Informes de actividad y de aplicaciones integradas

Al mover los informes basados en contexto a Azure Portal, se han combinado los informes existentes en una única vista con una sola API subyacente que proporciona los datos a la vista. 

Esta vista se encuentra en **Registros de auditoría**, en la sección **Actividad** de la hoja **Azure Active Directory**.


![Registros de auditoría](./media/active-directory-reporting-migration/482.png "Registros de auditoría")








Estos son los informes que se han consolidado en esta vista.

-    Informe de auditoría

-     Actividad de restablecimiento de contraseña

-     Actividad de registro de restablecimiento de contraseñas

-     Actividad de los grupos de autoservicio

-     Cambios de nombre de grupo de Office365

-     Actividad de aprovisionamiento de cuentas

-     Estado de la sustitución de contraseña
-     Errores de aprovisionamiento de cuentas


El informe Uso de la aplicación se ha mejorado y se ha incluido en una vista denominada **Inicios de sesión**. Esta vista se encuentra en la sección **Actividad** de la hoja **Azure Active Directory**.


![Registros de auditoría](./media/active-directory-reporting-migration/483.png "Registros de auditoría")

Esta vista incluye los inicios de sesión de todos los usuarios, que, a su vez, se pueden derivar para obtener la información del uso de la aplicación. Dicha información se puede encontrar también en la información general de **Aplicaciones empresariales**.

![Registros de auditoría](./media/active-directory-reporting-migration/484.png "Registros de auditoría")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>¿Cómo puedo acceder a un informe específico en esta vista única?

### <a name="audit-logs"></a>Registros de auditoría

Una de las preguntas clave de muchos clientes ha sido la capacidad de tener varias opciones de filtro para acceder a los registros de actividad en Azure AD. En su lugar, se ha habilitado un mecanismo de filtrado avanzado para poder filtrar los datos deseados. Uno de los filtros proporcionados se llama **Categoría de actividad** y enumera los diferentes tipos de registro de actividad que Azure AD proporciona. Al elegir la categoría que desea, puede limitar los resultados a lo que busca. 

Por ejemplo, si solo le interesa obtener las actividades relacionadas con el **Autoservicio de restablecimiento de contraseña**, puede elegir la categoría **Self-service Password Management** (Administración de contraseñas de autoservicio). Las categorías que puede ver se engloban en el contexto del recurso con el que trabaja.  


![Registros de auditoría](./media/active-directory-reporting-migration/06.png "Registros de auditoría")

 
Las distintas categorías disponibles actualmente son:

- Core Directory (Directorio principal)

- Self-service Password Management (Administración de contraseñas de autorservicio)

- Self-service Group Management (Administración de grupos de autoservicio)

- Account Provisioning (Aprovisionamiento de cuentas)

### <a name="application-usage"></a>Uso de la aplicación

Puede ver el uso de todas las aplicaciones o de una sola a través de la vista **Actividades > Inicios de sesión**. Tal y como se muestra a continuación, esta vista está presente para todas las aplicaciones o para una sola aplicación. Si desea limitar los resultados, puede filtrar por **nombre de usuario** o **nombre de aplicación**.
 

![Registros de auditoría](./media/active-directory-reporting-migration/07.png "Registros de auditoría")


### <a name="security-reports"></a>Informes de seguridad

Los informes de seguridad se han consolidado para proporcionar una información general completa de una vista de todos los eventos de riesgo relacionados con la seguridad que Azure Active Directory puede detectar y notificar. Para obtener una información general completa, consulte [Eventos de riesgo de Azure Active Directory](active-directory-identity-protection-risk-events.md).  
En este tema, puede encontrará información general sobre la forma en que los informes de actividad anómala de Azure Active Directory se asignan a los eventos de riesgo en Azure AD en la sección [Informes de actividades anómalas de Azure AD](active-directory-identity-protection-risk-events.md#azure-ad-anomalous-activity-reports).

En Azure Portal, puede acceder a los informes acerca de los eventos de riesgo detectados en la sección **Seguridad** de la hoja **Azure Active Directory**. En los siguientes informes se realiza un seguimiento de los eventos de riesgo detectados:   

- Usuarios en riesgo
- Inicios de sesión no seguros 


![Registros de auditoría](./media/active-directory-reporting-migration/04.png "Registros de auditoría")

Para más información acerca de estos informes, consulte:

- [Informe de seguridad de usuarios en riesgo en el portal de Azure Active Directory: versión preliminar](active-directory-reporting-security-user-at-risk.md)
- [Informe de inicios de sesión poco seguros en el portal de Azure Active Directory: versión preliminar](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Informes de actividad en el Portal de Azure clásico frente a Azure Portal

En esta sección se enumeran los informes existentes en el Portal de Azure clásico y cómo puede obtener esta información en Azure Portal.


El punto de entrada a todos los datos de auditoría es **Registros de auditoría**, en la sección **Actividad** de la hoja **Azure Active Directory**.

![Registros de auditoría](./media/active-directory-reporting-migration/61.png "Registros de auditoría")


| Portal de Azure clásico                 | Pasos de Azure Portal                                                         |
| ---                                  | ---                                                                        |
| Registros de auditoría                           | En **Categoría de actividad**, seleccione **Core Directory** (Directorio principal).                       |
| Actividad de restablecimiento de contraseña              | En **Categoría de actividad**, seleccione **Self service Password Management** (Administración de contraseñas de autoservicio). | 
| Actividad de registro de restablecimiento de contraseñas | En **Categoría de actividad**, seleccione **Self service Password Management** (Administración de contraseñas de autoservicio).     |
| Actividad de los grupos de autoservicio         | En **Categoría de actividad**, seleccione **Administración de grupos de autoservicio**.        |
| Actividad de aprovisionamiento de cuentas        | En **Categoría de actividad**, seleccione **Account User Provisioning** (Aprovisionamiento de usuarios de la cuenta).         |
| Estado de la sustitución de contraseña             | En **Categoría de actividad**, seleccione **Automatic App Password Rollover** (Sustitución automática de contraseñas de la aplicación).      |
| Errores de aprovisionamiento de cuentas          | En **Categoría de actividad**, seleccione **Account User Provisioning** (Aprovisionamiento de usuarios de la cuenta).        |
| Cambios de nombre de grupo de Office365         | En **Categoría de actividad**, seleccione **Self service Password Management** (Administración de contraseñas de autoservicio), en **Tipo de recurso de la actividad**, seleccione **Grupo** y en**Origen de la actividad**, seleccione **Grupos de Office&365;**.|

 

El punto de entrada al informe **Uso de la aplicación** es **Azure Active Directory > Aplicaciones empresariales > Inicios de sesión**. 


![Registros de auditoría](./media/active-directory-reporting-migration/199.png "Registros de auditoría")


