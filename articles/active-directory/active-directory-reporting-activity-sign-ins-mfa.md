---
title: "Referencia para los informes de la autenticación multifactor informes en Azure Portal | Microsoft Docs"
description: "Información de referencia para los informes de la autenticación multifactor informes en Azure Portal"
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
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: cb4ef0e361cb3495a03e5a7ed49d9a84a54dbdc3
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Referencia para los informes de la autenticación multifactor informes en Azure Portal

Con los [informes de Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) de [Azure Portal](https://portal.azure.com), se puede obtener toda la información necesaria para determinar cómo marcha el entorno.

El [informes de las actividades de inicio de sesión](active-directory-reporting-activity-sign-ins.md) proporcionan información acerca del uso de las aplicaciones administradas y actividades de inicio de sesión de usuario, lo que incluye información acerca del uso de la autenticación multifactor (MFA). 

Los datos MFA le proporcionan información acerca del funcionamiento de MFA en su organización, lo que le permite responder preguntas como estas: 

- ¿Supuso un problema la MFA para el inicio de sesión? 

- ¿Cómo completo el usuario la MFA? 

- ¿Por qué no pudo completar el usuario la MFA?  

Una vez que se agregan todos los datos de inicio de sesión, se puede tener conocer mejor el funcionamiento de MFA en una organización. Los datos le ayudan a responder preguntas como estas: 

- ¿En cuántos usuarios se usa MFA?  

- ¿Cuántos usuarios no pueden completar el desafío de MFA? 

- ¿Cuáles son los problemas de MFA más habituales a los que se enfrentan los usuarios finales? 


Estos datos están disponibles a través de Azure Portal y la [API de informes](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Estructura de datos


Los informes de actividades de inicio de sesión referentes a la MFA le proporcionan acceso a la siguiente información:

**MFA necesaria:** si MFA es necesaria para el inicio de sesión, o no. MFA puede se necesaria debido a la MFA por usuario, al acceso condicional o a otras razones. Los valores posibles son `Yes` o `No`.

**Método de autenticación de MFA:** el método de autenticación que el usuario ha utilizado para completar la MFA. Los valores posibles son: 

- mensaje de texto 

- Notificación en aplicación móvil 

- Llamada de teléfono (teléfono de autenticación) 

- Código de verificación de la aplicación móvil 

- Llamada de teléfono (teléfono profesional) 

- Llamada de teléfono (teléfono de autenticación alternativo) 

**Detalles de la autenticación de MFA:** versión limpia del número de teléfono, por ejemplo: + X XXXXXXXX64. 

**Resultado de MFA:** más información acerca de si la MFA se ha cumplido o denegado:

- Si se ha cumplido, esta columna proporciona más información acerca de cómo se ha hecho. 

- Si se ha denegado, esta columna proporcionaría el motivo de la denegación. Los valores posibles son `Satisfied` o `Denied`. 

En la siguiente sección se enumeran los posibles valores de cadena para el campo de resultados de MFA.

## <a name="status-strings"></a>Cadenas de estado

En esta sección se enumeran los posibles valores de la cadena de estado del resultado de la MFA.

### <a name="satisfied-status-strings"></a>Cadenas de estado satisfecho


- Azure Multi-Factor Authentication

    - completado en la nube 

    - ha expirado debido a las directivas configuradas en el inquilino 

    - registro solicitado 

    - satisfecho por notificación en el token 

    - satisfecho por notificación en el token 

    - satisfecho por notificación en el token 

    - satisfecho por notificación en el token 

    - satisfecho por notificación de proveedor externo 

    - satisfecho por autenticación segura 

    - se omite, ya que el flujo usado fue el flujo de inicio de sesión del agente de Windows 

    - se omite, ya que el flujo usado fue el flujo de inicio de sesión del agente de Windows 

    - se omite debido a la aplicación de la contraseña 

    - se omite debido a la ubicación 

    - se omite debido al dispositivo registrado 
    
    - se omite debido al dispositivo recordado 

    - completado correctamente 

- Redireccionado a un para la autenticación multifactor 

 
### <a name="denied-status-strings"></a>Cadenas de estado denegado

Azure Multi-Factor Authentication denegado; 

- autenticación en curso 

- intento de duplicación de autenticación 

- se ha escribir un código incorrecto demasiadas veces 

- autenticación no válida 

- código de verificación de aplicación móvil no válido 

- error de configuración 

- la llamada de teléfono se dirigió al correo de voz 

- el número de teléfono tiene un formato no válido 

- error del servicio 

- no se puede acceder el teléfono del usuario 

- no se puede enviar la notificación de la aplicación móvil al dispositivo 

- no se puede enviar la notificación de la aplicación móvil 

- el usuario rechazó la autenticación 

- el usuario no respondió a la notificación de la aplicación móvil 

- el usuario no tiene ningún método de comprobación registrado 

- el usuario ha escrito un código incorrecto 

- el usuario ha escrito un PIN incorrecto 

- el usuario contestó la llamada de teléfono sin la autenticación 

- el usuario está bloqueado 

- el usuario no ha escrito el código de verificación 

- el usuario no se encuentra 
 
- el código de verificación ya se ha usado una vez 



## <a name="next-steps"></a>pasos siguientes

Para más información, consulte [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md).




























