---
title: "Códigos de error de informes de actividad de inicio de sesión en el portal de Azure Active Directory | Microsoft Docs"
description: "Referencia sobre los códigos de error de los informes de actividad de inicio de sesión."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 183e11bed0d2399ffa714bc544f88eb359647b9a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Códigos de error de informes de actividad de inicio de sesión en el portal de Azure Active Directory

Con la información proporcionada por el informe de inicio de sesión del usuario, puede encontrar respuestas a preguntas tales como:

- ¿Quién ha iniciado sesión mediante Azure Active Directory?
- ¿En qué aplicaciones se inició sesión?
- ¿En qué inicios de sesión se produjeron errores y cuál fue la causa?

Este tema muestra los códigos de error y las descripciones relacionadas. 

## <a name="how-can-i-display-failed-sign-ins"></a>¿Cómo puedo mostrar los inicios de sesión en los que se produjeron errores? 

El primer punto de entrada a todos los datos de actividades de inicio de sesión es **[Inicios de sesión](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** en la sección **Actividad** de **Azure Active**.


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Actividad de inicio de sesión")


En los informes de inicios de sesión, puede mostrar todos los inicios de sesión en los que se produjeron errores seleccionando **Error** como **Estado de inicio de sesión**.


![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Actividad de inicio de sesión")

Si hace clic en un elemento de la lista mostrada, se abre la hoja **Detalles de la actividad: Inicios de sesión**. Esta vista le proporciona todos los detalles sobre los inicios de sesión de los que Azure Active Directory realiza el seguimiento incluido el **código de error del inicio de sesión** y un **motivo de error**.

![Actividad de inicio de sesión](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Actividad de inicio de sesión")


Como alternativa al uso de Azure Portal para acceder a los datos de inicios de sesión, también puede utilizar la [API de informes](active-directory-reporting-api-getting-started-azure-portal.md).


En la siguiente sección se proporciona una información general completa de todos los posibles errores y las descripciones relacionadas. 

## <a name="error-codes"></a>Códigos de error

| Error| Descripción |
| --- | --- |
| 50001| No se encontró la entidad de servicio denominada X en el inquilino denominado Y. Esto puede pasar si el administrador del inquilino no es el que ha instalado la aplicación. O bien no se encontró la entidad de servicio del recurso en el directorio o no es válida.|
| 50008| La aserción SAML falta o está mal configurada en el token.|
| 50011| La dirección de respuesta falta, está mal configurada o no coincide con las direcciones de respuesta configuradas para la aplicación.|
| 50012| El usuario notificó un fraude durante la autenticación multifactor.|
| 50053| La cuenta está bloqueada porque el usuario intentó iniciar sesión demasiadas veces con un Id. de usuario o contraseña incorrectos.|
| 50054| Se utilizó una contraseña antigua para realizar la autenticación.|
| 50055| La contraseña no es válida o ha expirado.|
| 50057| La cuenta de usuario está deshabilitada.|
| 50058| No se encontró información acerca de la identidad del usuario entre las credenciales proporcionadas, o no se encontró al usuario en el inquilino, o se envió una solicitud de inicio de sesión silenciosa pero ningún usuario inició sesión, o el servicio no pudo autenticar al usuario.|
| 50074| El usuario no superó el desafío MFA.|
| 50079| El usuario debe inscribirse para el segundo factor de autenticación.|
| 50126| Nombre de usuario o contraseña no válidos, o nombre de usuario o contraseña locales no válidos.|
| 50131| Se usa en varios errores de acceso condicional. Por ejemplo, estado de dispositivo incorrecto de Windows, solicitud bloqueada debido a actividades sospechosas, directivas de acceso y decisiones sobre directivas de seguridad.|
| 50133| La sesión no es válida debido a que ha expirado o a un cambio reciente de contraseña.|
| 50144| Ha expirado la contraseña de Active Directory del usuario.|
| 65001| La aplicación X no tiene permiso para acceder a la aplicación Y o este se ha revocado. O bien, el usuario o administrador no ha dado su consentimiento para usar la aplicación con el identificador X. Envíe una solicitud de autorización interactiva para este usuario y recurso. O bien, el usuario o administrador no ha dado su consentimiento para usar la aplicación con el identificador X. Envíe una solicitud de autorización al administrador del inquilino para que actúe en nombre de la aplicación: Y para el recurso: Z.|
| 65005| La lista de acceso a recursos requeridos de la aplicación no contiene aplicaciones reconocibles por el recurso, o la aplicación cliente ha solicitado el acceso a un recurso que no se especificó en su lista de acceso a recursos requeridos, o el servicio Graph devolvió una solicitud incorrecta o no se encontró el recurso.|
| 70001| No se encontró la aplicación denominada X en el inquilino denominado Y. Esto puede pasar si el administrador del inquilino no es el que ha instalado la aplicación o no ha recibido el consentimiento de ningún usuario del inquilino. Es posible que haya enviado la solicitud de autenticación al inquilino incorrecto.|
| 80001| El agente de autenticación no puede conectarse a Active Directory.|
| 80002| El tiempo de espera se agotó para la solicitud de validación de contraseña del agente de autenticación.|
| 80003| El agente de autenticación recibió una respuesta no válida.|
| 80004| Se usó un nombre principal de usuario (UPN) incorrecto en una solicitud de inicio de sesión.|
| 80005| Error del agente de autenticación.|
| 80007| El agente de autenticación no puede validar la contraseña del usuario.|
| 80010| El agente de autenticación no puede descifrar la contraseña.|
| 80011| El agente de autenticación no puede recuperar la clave de descifrado.|
| 81001| El vale de Kerberos del usuario es demasiado grande.|
| 81002| No se ha podido validar el vale de Kerberos del usuario.|
| 81003| No se ha podido validar el vale de Kerberos del usuario.|
| 81004| Error al intentar la autenticación Kerberos.|
| 81008| No se ha podido validar el vale de Kerberos del usuario.|
| 81009| No se ha podido validar el vale de Kerberos del usuario.|
| 81010| Error de SSO de conexión directa porque el vale de Kerberos del usuario ha expirado o no es válido.|
| 81011| No se encuentra el objeto de usuario con la información del vale de Kerberos del usuario.|
| 81012| El usuario que intenta iniciar sesión en Azure AD es distinto del que inició sesión en el dispositivo.|
| 81013| No se encuentra el objeto de usuario con la información del vale de Kerberos del usuario.|
| 90014| Se utiliza en varios casos cuando un campo esperado no está presente en la credencial.|
| 90093| Se devolvió un gráfico con un código de error prohibido para la solicitud.|




## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Introducción a los informes de actividad de inicio de sesión en el portal de Azure Active Directory](active-directory-reporting-activity-sign-ins.md).

