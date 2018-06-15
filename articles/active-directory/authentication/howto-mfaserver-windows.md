---
title: Autenticación de Windows y Servidor Azure MFA | Microsoft Docs
description: 'Implementación de Autenticación de Windows y Servidor Azure Multi-Factor Authentication. '
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: a72a045efe916c2aa89822984898bac5e43ea1cf
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866053"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de Autenticación de Windows y Azure Multi-Factor Authentication

Use la sección Autenticación de Windows del Servidor Azure Multi-Factor Authentication para habilitar y configurar la autenticación de Windows para las aplicaciones. Antes de configurar la autenticación de Windows, tenga en cuenta la lista siguiente:

* Después de la instalación, reinicie Azure Multi-Factor Authentication para aplicar la configuración de Terminal Services.
* Si se encuentra marcada la opción “Requerir coincidencia de usuario de Azure Multi-Factor Authentication” y usted no está en la lista de usuarios, no podrá iniciar sesión en el equipo después del reinicio.
* Las direcciones IP de confianza dependen de si la aplicación puede proporcionar la dirección IP de cliente con la autenticación. Actualmente solo se admite Terminal Services.  

> [!NOTE]
> Esta característica no se admite para proteger Terminal Services en Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para proteger una aplicación con la Autenticación de Windows, use el procedimiento siguiente.
1. En el servidor de Azure Multi-Factor Authentication, haga clic en el icono de Autenticación de Windows.
   ![Autenticación de Windows](./media/howto-mfaserver-windows/windowsauth.png)
2. Active la casilla **Enable Windows Authentication** (Habilitar autenticación de Windows). De forma predeterminada, esta casilla está desmarcada.
3. La pestaña Aplicaciones permite al administrador configurar una o más aplicaciones para la Autenticación de Windows.
4. Seleccione un servidor o una aplicación (especifique si el servidor o la aplicación está habilitado). Haga clic en **OK**.
5. Haga clic en **Agregar...**
6. La pestaña IP fiables permite omitir Azure Multi-Factor Authentication para sesiones de Windows procedentes de direcciones IP específicas. Por ejemplo, si los empleados usan la aplicación desde la oficina y desde casa, puede decidir que no desea que sus teléfonos suenen para la Azure Multi-Factor Authentication mientras se encuentra en la oficina. Para ello, debe especificar la subred de la oficina como entrada de IP de confianza.
7. Haga clic en **Agregar...**
8. Si desea omitir una única dirección IP, seleccione **IP única**.
9. Seleccione **IP Range** (Intervalo IP) si quiere omitir un intervalo IP completo. Ejemplo 10.63.193.1-10.63.193.100.
10. Seleccione **Subred** si quiere especificar un intervalo de direcciones IP mediante la notación de subred. Escriba la dirección IP inicial de la subred y seleccione la máscara de red adecuada de la lista desplegable.
11. Haga clic en **OK**.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de dispositivos de VPN de terceros para el servidor Azure MFA](howto-mfaserver-nps-vpn.md)

- [Augment your existing authentication infrastructure with the NPS extension for Azure MFA](howto-mfa-nps-extension.md) (Aumento de la infraestructura de autenticación existente con la extensión de NPS para Azure MFA)
