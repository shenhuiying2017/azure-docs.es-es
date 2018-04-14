---
title: "Autenticación de identidades sin contraseñas a través de Windows Hello para empresas y Azure AD | Microsoft Docs"
description: "Proporciona una visión general de Windows Hello para empresas e información adicional sobre la implementación de Windows Hello para empresas."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 22534cc5f7f2de235bc1f1212c63ea227083c5a4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Autenticación de identidades sin contraseñas a través de Windows Hello para empresas
Los métodos actuales de autenticación con contraseña, por sí solos, no son suficientes para proteger a los usuarios. Los usuarios reutilizan y olvidan las contraseñas. Las contraseñas se pueden violar, son susceptibles a la suplantación de identidad (phishing), son propensas al descifrado y se pueden adivinar. También son difíciles de recordar y tienden a ataques del tipo "[pass the hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>Acerca de Windows Hello para empresas
Windows Hello para empresas es un enfoque de autenticación basado en certificado o clave pública/privada dirigido a organizaciones y consumidores que no se limita a las contraseñas. Esta forma de autenticación se basa en credenciales de par de claves que pueden reemplazar a las contraseñas y que resisten las infracciones, los robos y las suplantaciones de identidad.

 Windows Hello para empresas permite a los usuarios autenticarse en una cuenta Microsoft, una cuenta de Windows Server Active Directory, una cuenta de Microsoft Azure Active Directory (Azure AD) o un servicio que no sea de Microsoft compatible con la autenticación Fast IDentity Online (FIDO). Tras una comprobación inicial en dos pasos durante la inscripción en Windows Hello para empresas, Windows Hello para empresas se instala en el dispositivo del usuario y este define un gesto, que puede ser Windows Hello o un PIN. El usuario proporciona el gesto para comprobar su identidad. Windows usa entonces Windows Hello para empresas para autenticar al usuario y ayudarle a acceder a servicios y recursos protegidos.

La clave privada queda disponible únicamente a través de un "gesto de usuario" como un PIN, biometría o un dispositivo remoto como una tarjeta inteligente que el usuario utiliza para iniciar sesión en el dispositivo. Esta información se vincula a un certificado o un par de claves asimétrico. Esta clave privada es certificada por el hardware si el dispositivo cuenta con un chip de Módulo de plataforma segura (TPM). La clave privada nunca abandona el dispositivo.

La clave pública se registra en Azure Active Directory y Windows Server Active Directory (para el entorno local). Los proveedores de identidades (IDP) validan al usuario mediante la asignación de la clave pública del usuario a la clave privada y proporcionan información de inicio de sesión mediante una contraseña de un solo uso (OTP), Phonefactor o un mecanismo de notificación diferente.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>¿Por qué las empresas deben adoptar Windows Hello para empresas?
Al habilitar Windows Hello para empresas, las empresas pueden proteger sus recursos aún mejor:

* Mediante la configuración de Windows Hello para empresas con una opción preferida de hardware. Esto significa que las claves se generarán en TPM 1.2 o TPM 2.0 cuando sea posible. Cuando TPM no esté disponible, el software generará la clave.
* La definición de la complejidad y la longitud del PIN, y la posibilidad de habilitar el uso de Hello en su organización.
* Mediante la configuración de Windows Hello para empresas para admitir escenarios de tipo tarjeta inteligente con confianza basada en certificados.

## <a name="how-windows-hello-for-business-works"></a>Funcionamiento de Windows Hello para empresas
1. Las claves se generan en el hardware por TPM o el software. Muchos dispositivos tienen un chip TPM integrado que protege el hardware mediante la integración de claves de cifrado en los dispositivos. TPM 1.2 o TPM 2.0 genera claves o certificados que se crean a partir de las claves generadas.
2. TPM certifica estas claves enlazadas al hardware.
3. Un único gesto de desbloqueo desbloquea el dispositivo. Este gesto permite el acceso a varios recursos si el dispositivo está unido a un dominio o a Azure AD.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Funcionamiento del ciclo de vida de Windows Hello empresas
![Ciclo de vida de Windows Hello para empresas](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

El diagrama anterior muestra el par de claves pública y privada y la validación por el proveedor de identidades. Cada uno de estos pasos se explica con detalle a continuación:

1. El usuario prueba su identidad a través de varios métodos de verificación integrados (gestos, tarjetas inteligentes físicas, autenticación multifactor) y envía esta información al proveedor de identidades, como Azure Active Directory o el entorno Active Directory local.
2. A continuación, el dispositivo crea la clave, la certifica, toma la parte pública de esta clave, la asocia con las instrucciones de la estación, inicia sesión y envía la clave al proveedor de identidades para registrarla.
3. En cuanto el proveedor de identidades registra la parte pública de la clave, este desafía al dispositivo a que firme con la parte privada de la clave.
4. El proveedor de identidades valida y emite entonces el token de autenticación que permite al usuario y al dispositivo tener acceso a los recursos protegidos. El proveedor de identidades puede escribir aplicaciones multiplataforma o usar la compatibilidad con el explorador (mediante las API de JavaScript/Webcrypto) para crear y usar las credenciales de Windows Hello para empresas para sus usuarios.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Requisitos de implementación de Windows Hello para empresas
### <a name="at-the-enterprise-level"></a>En el nivel de empresa
* La empresa tiene una suscripción de Azure.

### <a name="at-the-user-level"></a>En el nivel de usuario
* El equipo del usuario ejecuta Windows 10 Professional o Enterprise.

Para obtener instrucciones detalladas sobre la implementación, consulte [Habilitación de Windows Hello para empresas en la organización](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

