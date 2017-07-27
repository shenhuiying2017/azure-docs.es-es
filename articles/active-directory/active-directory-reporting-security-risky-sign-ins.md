---
title: "Informe de inicios de sesión poco seguros del portal de Azure Active Directory | Microsoft Docs"
description: "Obtenga información acerca del informe de inicios de sesión poco seguros del portal de Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: e21b7733bc0f6b8625fbdcec3529ad603ce306c1
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Informe de inicios de sesión poco seguros del portal de Azure Active Directory

Con los informes de seguridad de Azure Active Directory (Azure AD), puede obtener información acerca de la probabilidad de que haya cuentas de usuario en peligro en su entorno. 

Azure AD detecta acciones sospechosas relacionadas con las cuentas de usuario. Para cada acción detectada, se crea un registro denominado *evento de riesgo*. Para más información, consulte [Azure Active Directory risk events](active-directory-identity-protection-risk-events.md) (Eventos de riesgo de Azure Active Directory). 

Los eventos de riesgo detectados se utilizan para calcular:

- **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte la sección sobre los [inicios de sesión peligrosos](active-directory-identityprotection.md#risky-sign-ins). 

- **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte la sección sobre los [usuarios marcados en riesgo](active-directory-identityprotection.md#users-flagged-for-risk).  

En [Azure Portal](https://portal.azure.com), puede encontrar los informes de seguridad en la hoja **Azure Active Directory** de la sección **Seguridad**. 

![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="azure-active-directory-free-and-basic-edition"></a>Edición gratuita y básica de Azure Active Directory

Las ediciones gratuita y básica de Azure Active Directory proporcionan una lista de inicios de sesión peligrosos que se han detectado para los usuarios. En el informe de eventos de riesgo se especifican:

- **Usuario**: el nombre del usuario que se utilizó durante la operación de inicio de sesión
- **IP**: la dirección IP del dispositivo que se usó para conectarse a Azure Active Directory
- **Ubicación**: la ubicación utilizada para conectarse a Azure Active Directory
- **Hora de inicio de sesión**: la hora en que se realizó el inicio de sesión
- **Estado**: el estado del inicio de sesión

Este informe proporciona una opción para descargar los datos del informe.

![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Con los datos derivados de la investigación del inicio de sesión no seguro, puede proporcionar información a Azure Active Directory en forma de las siguientes acciones:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reactivar

![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Para más información, consulte [Cierre manual de eventos de riesgo](active-directory-identityprotection.md#closing-risk-events-manually).

## <a name="azure-active-directory-premium-editions"></a>Ediciones Azure Active Directory Premium

El informe de inicios de sesión poco seguros de las ediciones Azure Active Directory Premium proporciona lo siguiente:

- Información agregada sobre los [tipos de eventos de riesgo](active-directory-identity-protection-risk-events.md) que se han detectado

- Una opción para descargar el informe


![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/456.png)


Cuando selecciona un evento de riesgo, obtiene una vista detallada del informe para este evento de riesgo que le permite:

- Una opción para configurar una [directiva de corrección de riesgos de usuario](active-directory-identityprotection.md#user-risk-security-policy)  

- Revisar la escala de tiempo de detección del evento de riesgo  

- Revisar una lista de usuarios para los que se ha detectado este evento de riesgo

- [Cerrar manualmente los eventos de riesgo](active-directory-identityprotection.md#closing-risk-events-manually) o reactivar un evento de riesgo cerrado manualmente. 


![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/457.png)

Cuando selecciona un usuario, obtiene una vista detallada del informe para este usuario que le permite:

- Abrir la vista de todos los inicios de sesión

- Restablezca la contraseña del usuario.

- Descartar todos los eventos

- Investigar los eventos de riesgo notificados para el usuario. 


![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Para investigar un evento de riesgo, seleccione uno de la lista.  
Se abre la hoja **Detalles** de este evento de riesgo. En la hoja **Detalles**, tiene la opción de [cerrar manualmente un evento de riesgo](active-directory-identityprotection.md#closing-risk-events-manually) o de volver a activar un evento de riesgo cerrado manualmente. 


![Inicios de sesión no seguros](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Azure Active Directory Identity Protection, consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


