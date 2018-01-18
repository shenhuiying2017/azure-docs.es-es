---
title: Informes de Azure Active Directory | Microsoft Docs
description: "Proporciona una visión general sobre los informes de Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 66c53b84e1e820178083b3ae703e0a1feada187f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-reporting"></a>Informes de Azure Active Directory

Con los informes de Azure Active Directory, puede obtener información sobre el funcionamiento de su entorno.  
Los datos proporcionados le permiten:

- Determinar cómo utilizan los usuarios las aplicaciones y servicios
- Detectar posibles riesgos que afectan al estado de su entorno
- Solucionar problemas que impiden a los usuarios finalizar su trabajo  

La arquitectura de informes se basa en dos pilares principales:

- Informes de seguridad
- Informes de actividad

![Informes](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Informes de seguridad

Los informes de seguridad de Azure Active Directory le ayudan a proteger las identidades de su organización.  
Hay dos tipos de informes de seguridad en Azure Active Directory:

- **Usuarios marcados en riesgo**: en el [informe de seguridad de usuarios marcados en riesgo](active-directory-reporting-security-user-at-risk.md) puede obtener una visión general de las cuentas de usuario que pueden haber estado en peligro.

- **Inicios de sesión de riesgo**: en el [informe de seguridad de inicios de sesión de riesgo](active-directory-reporting-security-risky-sign-ins.md) puede obtener un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. 

**¿Qué licencia de Azure AD se necesita para acceder a un informe de seguridad?**  
Todas las ediciones de Azure Active Directory le proporcionan estos informes sobre usuarios marcados en riesgo e inicios de sesión de riesgo.  
Sin embargo, el nivel de granularidad del informe varía según la edición: 

- En las **ediciones Azure Active Directory Free y Basic** ya puede obtener una lista de usuarios marcados en riesgo y de inicios de sesión de riesgo. 

- En la edición **Azure Active Directory Premium 1** se extiende este modelo, con lo que también puede examinar algunos de los eventos de riesgo subyacentes que se han detectado para cada informe. 

- La edición **Azure Active Directory Premium 2** le proporciona la información más detallada acerca de los eventos de riesgo subyacentes y también le permite configurar directivas de seguridad que responden automáticamente a los niveles de riesgo configurados.


## <a name="activity-reports"></a>Informes de actividad

Hay dos tipos de informes de actividad en Azure Active Directory:

- **Registros de auditoría**: el [informe de actividad de registros de auditoría](active-directory-reporting-activity-audit-logs.md) le proporciona acceso al historial de todas las tareas llevadas a cabo en el inquilino.

- **Inicios de sesión**: con el [informe de actividad de inicios de sesión](active-directory-reporting-activity-sign-ins.md), puede determinar quién ha realizado las tareas notificadas en el informe de registros de auditoría.



Los **informes de registros de auditoría** le proporcionan registros de las actividades del sistema en relación con el cumplimiento normativo.
Entre otras cosas, los datos proporcionados le permiten abordar cuestiones comunes como:

- Algún usuario de mi inquilino obtuvo acceso a un grupo de administración. ¿Quién les dio acceso? 

- Quiero conocer la lista de usuarios que inician sesión en una aplicación específica ya que la puse en marcha recientemente y quiero conocer si está funcionando correctamente.

- Quiero saber cuántos restablecimientos de contraseña se producen en mi inquilino


**¿Qué licencia de Azure AD se necesita para acceder al informe de registros de auditoría?**  
El informe de registros de auditoría está disponible para aquellas características para las que dispone de licencia. Si tiene una licencia para una característica determinada, también tiene acceso a la información del registro de auditoría de dicha característica.

Para más información, consulte la **comparación de características disponibles con carácter general de las ediciones Free, Basic y Premium** en [Características y funcionalidad de Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   



El **informe de actividad de inicios de sesión** le permite encontrar respuestas a preguntas como:

- ¿Cuál es el patrón de inicio de sesión de un usuario?
- ¿Cuántos usuarios tienen usuarios que han iniciado sesión durante una semana?
- ¿Cuál es el estado de estos inicios de sesión?


**¿Qué licencia de Azure AD se necesita para acceder a los informes de actividades de inicio de sesión?**  
El inquilino debe tener una licencia de Azure AD Premium asociada para acceder al informe de actividades de inicio de sesión.


## <a name="programmatic-access"></a>Acceso mediante programación

Además de a través de la interfaz de usuario, los informes de Azure Active Directory también le proporcionan [acceso mediante programación](active-directory-reporting-api-getting-started-azure-portal.md) a los datos de los informes. Los datos de estos informes pueden ser muy útiles para las aplicaciones, como sistemas SIEM, auditorías y herramientas de inteligencia empresarial. Las API de generación de informes de Azure AD proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas. 


## <a name="next-steps"></a>pasos siguientes

Si desea obtener más información sobre los distintos tipos de informes en Azure Active Directory, consulte:

- [Informe de usuarios marcados en riesgo](active-directory-reporting-security-user-at-risk.md)
- [Informe de inicios de sesión de riesgo](active-directory-reporting-security-risky-sign-ins.md)
- [Informe de registros de auditoría](active-directory-reporting-activity-audit-logs.md)
- [Informe de registros de inicios de sesión](active-directory-reporting-activity-sign-ins.md)

Si desea obtener más información acerca del acceso a los datos de los informes mediante la API de generación de informes, consulte: 

- [Introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png