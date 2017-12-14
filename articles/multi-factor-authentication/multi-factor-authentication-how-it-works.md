---
title: "Azure Multi-Factor Authentication - Cómo funciona"
description: "Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 0791ec4c3bf4a69e2a93b8f3dd1dcf9b649f4cff
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-azure-multi-factor-authentication-works"></a>Cómo funciona Azure Multi-Factor Authentication
La seguridad de la comprobación en pos pasos se basa en el enfoque por niveles. El uso de varias fases de autenticación supone un reto importante para los atacantes. Incluso si un atacante consigue descifrar la contraseña de usuario, no sirve de nada si no dispone también del dispositivo de confianza. 

![Página de proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple.  Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas.


## <a name="methods-available-for-two-step-verification"></a>Métodos disponibles para la verificación en dos pasos
Cuando un usuario inicia sesión, se envía una comprobación adicional al usuario.  Lo siguiente es una lista de métodos que pueden usarse para esta segunda comprobación.

| Método de comprobación | Descripción |
| --- | --- |
| llamada de teléfono |Se realiza una llamada al teléfono registrado de un usuario. El usuario escribe un PIN, si es necesario, y, a continuación, presiona la tecla #. |
| mensaje de texto |Se envía un mensaje de texto al teléfono móvil del usuario con un código de seis dígitos. El usuario escribe este código en la página de inicio de sesión. |
| Notificación en aplicación móvil |Se envía una solicitud de comprobación al smartphone de un usuario. El usuario escribe un PIN, si es necesario, y, a continuación, selecciona **Comprobar** en la aplicación móvil. |
| Código de verificación de la aplicación móvil |La aplicación móvil, que se ejecuta en el smartphone de un usuario, muestra un código de verificación que cambia cada 30 segundos. El usuario busca el código más reciente y lo escribe en la página de inicio de sesión. |
| Tokens OATH de terceros | El Servidor Azure Multi-Factor Authentication puede configurarse para aceptar métodos de verificación de terceros. |

Azure Multi-Factor Authentication proporciona métodos de comprobación seleccionables tanto para la nube y como para servidor. Puede elegir qué métodos estarán disponibles para los usuarios: llamada telefónica, mensajes de texto, notificación en aplicación o códigos de aplicación. Para más información, vea [Métodos de comprobación seleccionables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las diferentes [versiones y métodos de utilización de Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

- Elija si va a implementar Azure MFA [en la nube o de forma local](multi-factor-authentication-get-started.md).

- Obtenga respuestas para las [Preguntas más frecuentes](multi-factor-authentication-faq.md).