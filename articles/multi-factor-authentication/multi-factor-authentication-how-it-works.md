---
title: "Azure Multi-Factor Authentication - Cómo funciona"
description: "Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Cómo funciona Azure Multi-Factor Authentication
La seguridad de la autenticación mediante varias fases se basa en el enfoque por niveles. El uso de varias fases de autenticación supone un reto importante para los atacantes. Incluso si un atacante consigue descifrar la contraseña de usuario, no sirve de nada si no dispone también del dispositivo de confianza. Si el usuario pierde el dispositivo, la persona que lo encuentre no podrá usarlo a no ser que conozca la contraseña de usuario.

![Página de proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple.  Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas.

Para obtener información detallada sobre cómo funciona, vea el vídeo siguiente:

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>Métodos disponibles para la verificación en dos pasos
Cuando un usuario inicia sesión, se envía una comprobación adicional al usuario.  Lo siguiente es una lista de métodos que pueden usarse para esta segunda comprobación.

| Método de comprobación | Description |
| --- | --- |
| llamada de teléfono |Se realiza una llamada al teléfono registrado del usuario para solicitarle que verifique que ha iniciado sesión presionando el signo # o escribiendo un PIN. |
| mensaje de texto |Se enviará un mensaje de texto al teléfono móvil del usuario con un código de seis dígitos.  Escriba este código para completar el proceso de comprobación. |
| Notificación de aplicación móvil |Se envía al smartphone del usuario una solicitud de verificación en la que se le solicita que complete la verificación mediante la selección de Verify (Verificar) en la aplicación móvil. Esto ocurrirá si ha seleccionado la notificación de aplicación como su método de comprobación principal.  Si los usuarios lo reciben cuando no han iniciado sesión, pueden notificarlo como fraude. |
| Código de verificación de la aplicación móvil |La aplicación móvil, que se ejecuta en el smartphone de un usuario, muestra un código de verificación de seis dígitos que cambia cada 30 segundos. El usuario busca el código más reciente y lo escribe en la página de inicio de sesión para completar el proceso de verificación. Esto ocurrirá si ha seleccionado un código de verificación como su método de comprobación principal. |
| tokens OATH de terceros | Azure Multi-Factor Authentication puede configurarse para aceptar métodos de verificación de terceros. |

Azure Multi-Factor Authentication proporciona métodos de comprobación seleccionables tanto para la nube y como para servidor. Esto significa que puede elegir qué métodos estarán disponibles para los usuarios; llamada telefónica, texto, notificación en aplicación o códigos de aplicación. Para más información, vea [Métodos de comprobación seleccionables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las diferentes [versiones y métodos de utilización de Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

- Elija si va a implementar Azure MFA [en la nube o de forma local](multi-factor-authentication-get-started.md).
