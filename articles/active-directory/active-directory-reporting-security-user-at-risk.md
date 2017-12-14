---
title: Informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory | Microsoft Docs
description: Aprenda sobre el informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory
services: active-directory
author: MarkusVi
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: dca87eef3026ef43c0548a73f1b071b56e410e90
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory

Con los informes de seguridad de Azure Active Directory (Azure AD), puede obtener información acerca de la probabilidad de que haya cuentas de usuario en peligro en su entorno. 

Azure Active Directory detecta acciones sospechosas relacionadas con las cuentas de usuario. Para cada acción detectada, se crea un registro denominado *evento de riesgo*. Para más información, consulte [Eventos de riesgo de Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Los eventos de riesgo detectados se utilizan para calcular:

- **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte [Inicios de sesión no seguros](active-directory-identityprotection.md#risky-sign-ins). 

- **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte [Usuarios marcados en riesgo](active-directory-identityprotection.md#users-flagged-for-risk).  

En Azure Portal, puede encontrar los informes de seguridad en la hoja **Azure Active Directory** de la sección **Seguridad**.  

![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>¿Qué licencia de Azure AD se necesita para acceder a un informe de seguridad?  

Todas las ediciones de Azure Active Directory le proporcionan estos informes sobre usuarios marcados en riesgo.  
Sin embargo, el nivel de granularidad del informe varía según la edición: 

- En las **ediciones Azure Active Directory Free y Basic** ya puede obtener una lista de usuarios marcados en riesgo. 

- En la edición **Azure Active Directory Premium 1** se extiende este modelo, con lo que también puede examinar algunos de los eventos de riesgo subyacentes que se han detectado para cada informe. 

- La edición **Azure Active Directory Premium 2** le proporciona la información más detallada acerca de los eventos de riesgo subyacentes y también le permite configurar directivas de seguridad que responden automáticamente a los niveles de riesgo configurados.



## <a name="azure-active-directory-free-and-basic-edition"></a>Edición gratuita y básica de Azure Active Directory

El informe de usuarios marcados en riesgo de las ediciones gratis y básica de Azure Active Directory proporciona una lista de cuentas de usuario que podrían estar en peligro. 


![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/03.png)

Al seleccionar un usuario, se abre la hoja de datos del usuario relacionada.
En el caso de usuarios que están en riesgo, puede revisar el historial de inicios de sesión del usuario y restablecer la contraseña, en caso de que sea necesario.

![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/46.png)


Este cuadro de diálogo proporciona una opción para:

- Descargar el informe

- Búsqueda de usuarios

![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Ediciones Azure Active Directory Premium

El informe de usuarios marcados en riesgo de las ediciones Azure Active Directory Premium proporciona lo siguiente:

- Una [lista de cuentas de usuario](active-directory-identityprotection.md#users-flagged-for-risk) que podrían estar en peligro 

- Información agregada sobre los [tipos de eventos de riesgo](active-directory-identity-protection-risk-events.md) que se han detectado

- Una opción para descargar el informe

- Una opción para configurar una [directiva de corrección de riesgos de usuario](active-directory-identityprotection.md#user-risk-security-policy)  


![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/71.png)

Cuando selecciona un usuario, obtiene una vista detallada del informe para este usuario que le permite:

- Abrir la vista de todos los inicios de sesión

- Restablezca la contraseña del usuario.

- Descartar todos los eventos

- Investigar los eventos de riesgo notificados para el usuario. 


![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/324.png)


Para investigar un evento de riesgo, seleccione uno de la lista para abrir la hoja **Detalles** de este evento de riesgo. En la hoja **Detalles**, tiene la opción de [cerrar manualmente un evento de riesgo](active-directory-identityprotection.md#closing-risk-events-manually) o de volver a activar un evento de riesgo cerrado manualmente. 


![Inicios de sesión no seguros](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Azure Active Directory Identity Protection, consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

