---
title: "Búsqueda de informes de actividad en Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo buscar informes de actividad de Azure Active Directory en Azure Portal."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1875582476c3817b9eb0082b6548cc15043cb98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Búsqueda de informes de actividad en Azure Portal

Si migra del Portal de Azure clásico a Azure Portal, los registros de actividad de Azure Active Directory (Azure AD) tendrán un nuevo aspecto. En una [entrada de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) reciente, explicamos cómo puede ver los registros de actividad en el contexto del recurso en que trabaja en Azure Portal. En este artículo se describe cómo puede buscar los informes que usaba en el Portal de Azure clásico en Azure Portal.

## <a name="whats-new"></a>Novedades

Los informes del Portal de Azure clásico están separados por categorías:

1.  Informes de seguridad
2.  Informes de actividad
3.  Informes de aplicaciones integradas

### <a name="activity-and-integrated-app-reports"></a>Informes de actividad y de aplicaciones integradas

En el caso de los informes basados en contexto en Azure Portal, los informes existentes se combinan en una sola vista. Una API subyacente única proporciona los datos a la vista.

Para ver esta vista, vaya a la hoja **Azure Active Directory** y, en **ACTIVIDAD**, seleccione **Registros de auditoría**.

![Registros de auditoría](./media/active-directory-reporting-migration/482.png "Registros de auditoría")

Los informes siguientes están consolidados en esta vista:

-   Informe de auditoría
-   Actividad de restablecimiento de contraseña
-   Actividad de registro de restablecimiento de contraseñas
-   Actividad de los grupos de autoservicio
-   Cambios de nombre de grupo de Office365
-   Actividad de aprovisionamiento de cuentas
-   Estado de la sustitución de contraseña
-   Errores de aprovisionamiento de cuentas


El informe Uso de la aplicación se mejoró y se incluyó en la vista **Inicios de sesión**. Para ver esta vista, vaya a la hoja **Azure Active Directory** y, en **ACTIVIDAD**, seleccione **Inicios de sesión**.

![Vista de inicios de sesión](./media/active-directory-reporting-migration/483.png "Vista de inicios de sesión")

La vista **Inicios de sesión** incluye todos los inicios de sesión del usuario. Puede usar esta información para obtener información sobre el uso de las aplicaciones. También puede consultar la información sobre el uso de las aplicaciones en la información general sobre **Aplicaciones empresariales** en la sección **ADMINISTRAR**.

![Aplicaciones empresariales](./media/active-directory-reporting-migration/484.png "Aplicaciones empresariales")

## <a name="access-a-specific-report"></a>Acceso a un informe específico

Si bien Azure Portal ofrece una vista única, también se pueden consultar informes específicos.

### <a name="audit-logs"></a>Registros de auditoría

En respuesta a los comentarios de los clientes, en Azure Portal puede usar el filtrado avanzado para tener acceso a los datos que desea. Un filtro que puede usar es una *categoría de actividad*, que muestra los distintos tipos de registros de actividad en Azure AD. Para restringir los resultados a lo que busca, puede seleccionar una categoría.

Por ejemplo, si solo le interesan las actividades relacionadas con los restablecimientos de contraseña de autoservicio, puede elegir la categoría **Administración de contraseñas de autoservicio**. Las categorías que ve se basan en el recurso en el que trabaja.  

![Opciones de categoría en la página Filtrar registros de auditoría](./media/active-directory-reporting-migration/06.png "Opciones de categoría en la página Filtrar registros de auditoría")

Las categorías de actividad incluyen:

- Core Directory (Directorio principal)
- Self-service Password Management (Administración de contraseñas de autorservicio)
- Self-service Group Management (Administración de grupos de autoservicio)
- Account Provisioning (Aprovisionamiento de cuentas)

### <a name="application-usage"></a>Uso de la aplicación

Para detalles sobre el uso de todas las aplicaciones o de una sola aplicación, en **ACTIVIDAD**, seleccione **Inicios de sesión**. Para restringir los resultados, puede filtrar según el nombre del usuario o el nombre de la aplicación.

![Página Filtrar eventos de inicio de sesión](./media/active-directory-reporting-migration/07.png "Página Filtrar eventos de inicio de sesión")

### <a name="security-reports"></a>Informes de seguridad

#### <a name="azure-ad-anomalous-activity-reports"></a>Informes de actividades anómalas de Azure AD

Los informes de seguridad de actividad anómala en Azure AD del Portal de Azure clásico se consolidaron para brindarle una vista una y centralizada. Esta vista muestra todos los eventos de riesgo relacionados con la seguridad que Azure AD puede detectar e informar.

En la tabla siguiente aparecen los informes de seguridad de actividad anómala de Azure AD y los tipos de eventos de riesgo correspondientes en Azure Portal.

| Informe de actividad anómala de Azure AD |  Tipo de evento de riesgo de Identity Protection|
| :--- | :--- |
| Usuarios con credenciales perdidas | Credenciales con fugas |
| Actividad de inicio de sesión irregular | Viaje imposible a ubicaciones inusuales |
| Inicios de sesión desde dispositivos posiblemente infectados | Inicios de sesión desde dispositivos infectados|
| Inicios de sesión desde orígenes desconocidos | Inicios de sesión desde direcciones IP anónimas |
| Inicios de sesión desde direcciones IP con actividad sospechosa | Inicios de sesión desde direcciones IP con actividad sospechosa |
| - | Inicios de sesión desde ubicaciones desconocidas |

Los informes de seguridad de actividad anómala de Azure AD siguientes no se incluyen como eventos de riesgo en Azure Portal:

* Inicios de sesión tras varios errores
* Inicios de sesión desde varias ubicaciones geográficas

Estos informes siguen disponibles en el Portal de Azure clásico, pero quedarán en desuso en el futuro.

Para más información, consulte [Eventos de riesgo de Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Eventos de riesgo detectados

En Azure Portal, puede tener acceso a los informes sobre eventos de riesgo detectados en la hoja **Azure Active Directory**, en **SEGURIDAD**. En los informes siguientes se hace un seguimiento de los eventos de riesgo detectados:   

- Usuarios en riesgo
- Inicios de sesión no seguros

![Informes de seguridad](./media/active-directory-reporting-migration/04.png "Informes de seguridad")

Para más información sobre los informes de seguridad, consulte:

- [Informe de seguridad de usuarios en riesgo en el portal de Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Informe de inicios de sesión poco seguros del portal de Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Informes de actividad en el Portal de Azure clásico frente a Azure Portal

La tabla de esta sección muestra los informes existentes en el Portal de Azure clásico. También describe cómo puede obtener la misma información en Azure Portal.

Para ver todos los datos de auditoría, vaya a la hoja **Azure Active Directory** y, en **ACTIVIDAD**, vaya a **Registros de auditoría**.

![Registros de auditoría](./media/active-directory-reporting-migration/61.png "Registros de auditoría")

| Portal de Azure clásico                 | Para encontrar en Azure Portal                                                         |
| ---                                  | ---                                                                        |
| Registros de auditoría                           | Para **Categoría de actividad**, seleccione **Directorio principal**.                       |
| Actividad de restablecimiento de contraseña              | Para **Categoría de actividad**, seleccione **Administración de contraseñas de autoservicio**. |
| Actividad de registro de restablecimiento de contraseñas | Para **Categoría de actividad**, seleccione **Administración de contraseñas de autoservicio**.     |
| Actividad de los grupos de autoservicio         | Para **Categoría de actividad**, seleccione **Administración de grupos de autoservicio**.        |
| Actividad de aprovisionamiento de cuentas        | Para **Categoría de actividad**, seleccione **Aprovisionamiento de usuarios de la cuenta**.         |
| Estado de la sustitución de contraseña             | En **Categoría de actividad**, seleccione **Sustitución automática de contraseñas de aplicación**.      |
| Errores de aprovisionamiento de cuentas          | Para **Categoría de actividad**, seleccione **Aprovisionamiento de usuarios de la cuenta**.        |
| Cambios de nombre de grupo de Office365         | Para **Categoría de actividad**, seleccione **Administración de contraseñas de autoservicio**. Para **Tipo de recurso de actividad**, seleccione **Grupo**. Para **Origen de la actividad**, seleccione **Grupos de O365**.|

Para ver el informe **Uso de la aplicación**, vaya a la hoja **Azure Active Directory** y, en **ADMINISTRAR**, seleccione **Aplicaciones empresariales** y, luego, seleccione **Inicios de sesión**.


![Informe Inicios de sesión de aplicaciones empresariales](./media/active-directory-reporting-migration/199.png "Informe Inicios de sesión de aplicaciones empresariales")

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre los informes, consulte [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md).
