---
title: "Elección entre la nube o el servidor Azure MFA | Microsoft Docs"
description: "Para elegir la solución de seguridad para la autenticación multifactor que sea más adecuada para su situación, conteste a dos preguntas: qué estoy tratando de asegurar y dónde están ubicados mis usuarios.  A continuación, elija la nube, Servidor MFA o AD FS."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: a93a676c5553b36316c1f20cc796e50352687fed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Elección de la solución de Azure Multi-Factor Authentication más adecuada
Puesto que existen varios modelos de Azure Multi-Factor Authentication (MFA), se debe responder primero a algunas preguntas para descubrir cuál es el más adecuado para usar.  Estas preguntas son:

* [Qué es lo que quiero proteger](#what-am-i-trying-to-secure)
* [Dónde se encuentran los usuarios](#where-are-the-users-located)
* [¿Qué características necesito?](#what-features-do-i-need)

Las siguientes secciones proporcionan instrucciones para determinar cada una de estas respuestas.

## <a name="what-am-i-trying-to-secure"></a>¿Qué es lo que quiero proteger?
Para determinar la solución correcta de verificación en dos pasos, en primer lugar, hay que responder a la pregunta de qué es lo que está intentando proteger con un segundo método de autenticación.  ¿Es una aplicación que está en Azure?  ¿O un sistema de acceso remoto?  Mediante la determinación de lo que estamos intentando proteger, encontraremos la respuesta a la pregunta de dónde hay que habilitar Multi-Factor Authentication.  

| ¿Qué intenta proteger? | MFA en la nube | Servidor MFA |
| --- |:---:|:---:|
| Aplicaciones propias de Microsoft |● |● |
| Aplicaciones de SaaS en la galería de aplicaciones |● |  |
| Aplicaciones web que se publican a través del proxy de aplicación de Azure AD |● |  |
| Aplicaciones de IIS que no se publican a través del proxy de aplicación de Azure AD | |● |
| Acceso remoto como VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Dónde se encuentran los usuarios
A continuación, en función del lugar en que se encuentren los usuarios, podemos determinar la solución correcta que se debe utilizar, ya sea en la nube o de forma local mediante Servidor MFA.

| Ubicación del usuario | MFA en la nube | Servidor MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD y AD local mediante la federación con AD FS |● |● |
| Azure AD y AD local a través de DirSync, Azure AD Sync, Azure AD Connect - sin sincronización de contraseñas |● |● |
| Azure AD y AD local a través de DirSync, Azure AD Sync, Azure AD Connect - con sincronización de contraseñas |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>¿Qué características necesito?
En la tabla siguiente se comparan las características disponibles en Multi-Factor Authentication en la nube con las de Servidor Multi-Factor Authentication.

| Característica | MFA en la nube | Servidor MFA |
| --- |:---:|:---:|
| Notificación de aplicación móvil como segundo factor | ● | ● |
| Código de verificación de aplicación móvil como segundo factor | ● | ● |
| Llamada de teléfono como segundo factor | ● | ● |
| SMS unidireccional como segundo factor | ● | ● |
| SMS bidireccional como segundo factor | | ●  (En desuso)| 
| Tokens de hardware como segundo factor | | ● |
| Contraseñas de aplicaciones para clientes de Office 365 que no admiten MFA | ● | |
| Control de administración sobre los métodos de autenticación | ● | ● |
| Modo de PIN | | ● |
| Alerta de fraude |● | ● |
| Informes de MFA |● | ● |
| Omisión por única vez | | ● |
| Saludos personalizados para las llamadas de teléfono | ● | ● |
| Identificador de llamada personalizable para llamadas telefónicas | ● | ● |
| IP de confianza | ● | ● |
| Recordar MFA para dispositivos de confianza | ● | |
| Acceso condicional | ● | ● |
| Memoria caché |  | ● |

## <a name="next-steps"></a>Pasos siguientes

Ahora que comprende la diferencia entre Azure Multi-Factor Authentication en la nube o el servidor MFA local, es momento de configurar y usar Azure Multi-Factor Authentication. **Seleccione el icono que represente su escenario**

<center>

[![MFA en la nube](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Servidor MFA](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</center>
