---
title: "Configuración de un dispositivo de Windows 10 con Azure AD desde Configuración | Microsoft Docs"
description: "Explica cómo pueden los usuarios unirse a Azure AD a través del menú Configuración."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: b844e1d9-ad43-4e4a-a398-5c4a43bf2f5c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70610ec2c5a056d63ed854b33d84e29951940c2f


---
# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configuración de un dispositivo de Windows 10 con Azure AD desde Configuración
Si ya usa Windows 7 o Windows 8 y el equipo o dispositivo se ha actualizado a Windows 10, puede unirse a Azure Active Directory (Azure AD) a través del menú Configuración.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Para unirse a Azure AD desde el menú Configuración
1. En el menú **Inicio**, haga clic en el acceso a **Configuración**.
2. En **Configuración**, seleccione **Sistema**->**Acerca de**->**Unirse a Azure AD**.
   
   <center>
   ![Unirse a Azure AD desde el menú Configuración](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>
3. Haga clic en **Continuar** en la ventana de mensaje de Azure AD Join.
   
   <center>
   ![Ventana de mensaje de Unirse a Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Proporcione sus credenciales de inicio de sesión. Esta experiencia de inicio de sesión incluirá todos los pasos necesarios para completar la autenticación. Si forma parte de un inquilino federado, el administrador le brindará la experiencia de federación que hospeda su organización.
   <center>
   ![Proporcionar credenciales de inicio de sesión](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Si la organización ha configurado Azure Multi-Factor Authentication para unirse a Azure AD, proporcione el segundo factor para poder continuar.
6. Haga clic en **Aceptar** en la pantalla **Permitir la administración de este dispositivo**.
7. Verá el mensaje "Su dispositivo se ha unido ahora a su organización en Azure AD".

## <a name="additional-information"></a>Información adicional
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)




<!--HONumber=Dec16_HO4-->


