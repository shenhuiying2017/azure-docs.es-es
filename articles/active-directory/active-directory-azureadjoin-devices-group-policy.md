---
title: "Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10 | Microsoft Docs"
description: "Explica cómo los administradores pueden configurar directivas de grupo para permitir que los dispositivos se unan mediante dominio a la red empresarial."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 7d141adf04cfb99e57c63ba62de4a7dad9ab8326
ms.openlocfilehash: 290645b920bc4a83c610e80266854450b6e1509a


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10
Durante los últimos 15 años o más, las organizaciones emplean el método tradicional de unirse a un dominio para que los dispositivos conectados funcionen. Esto ha hecho posible que los usuarios inicien sesión en sus dispositivos con sus cuentas profesionales o educativas de Windows Server Active Directory (Active Directory) y que los administradores de TI puedan administrar esos dispositivos por completo. Las organizaciones suelen confían en los métodos de creación de imágenes para aprovisionar los dispositivos de los usuarios y usan generalmente System Center Configuration Manager (SCCM) o la directiva de grupo para administrarlos.

Unirse a un dominio en Windows 10 proporcionará las siguientes ventajas después de haber conectado los dispositivos a Azure Active Directory (Azure AD):

* Inicio de sesión único (SSO) a los recursos de Azure AD desde cualquier lugar
* Acceso a la Tienda Windows para empresas mediante cuentas profesionales o educativas (sin necesidad de una cuenta Microsoft)
* Movilidad de las configuraciones de usuario conforme a la empresa entre dispositivos que usan cuentas profesionales o educativas (sin necesidad de una cuenta Microsoft)
* Autenticación segura y práctico inicio de sesión para cuentas profesionales o educativas con Microsoft Passport y Windows Hello
* Posibilidad de limitar el acceso únicamente a los dispositivos que cumplan con la configuración de la directiva de grupo para los dispositivos de la organización

## <a name="prerequisites"></a>Requisitos previos
Unirse a un dominio sigue resultando útil. Sin embargo, para disfrutar de los beneficios de Azure AD respecto del inicio de sesión único, la movilidad de la configuración con las cuentas profesionales o educativas y el acceso a la Tienda Windows con las cuentas profesionales o educativas, necesitará lo siguiente:

* Suscripción de Azure AD
* Azure AD Connect para ampliar el directorio local a Azure AD
* Establecimiento de la directiva para conectar los dispositivos unidos a un dominio a Azure AD
* Compilación de Windows 10 (compilación 10551 o posterior) en los dispositivos.

Para habilitar Microsoft Passport for Work y Windows Hello, necesitará además lo siguiente:

* Infraestructura de clave pública (PKI) para la emisión de certificados de usuario.
* Versión 1509 de System Center Configuration Manager para Technical Preview. Para más información, consulte [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) (Versión preliminar técnica de Microsoft System Center Configuration Manager) y [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx) (Blog del equipo de System Center Configuration Manager). Esta información es necesaria para implementar certificados de usuario según las claves de Microsoft Passport.

Como alternativa al requisito de implementación de PKI, puede hacer lo siguiente:

* Tener algunos controladores de dominio con los Servicios de dominio de Active Directory para Windows Server 2016.

Para habilitar el acceso condicional, puede crear una configuración de directiva de grupo que permita el acceso a dispositivos unidos a un dominio sin ninguna implementación adicional. Para administrar el control de acceso basado en el cumplimiento del dispositivo, necesitará lo siguiente:

* La versión 1509 de System Center Configuration Manager para Technical Preview para escenarios de Passport

## <a name="deployment-instructions"></a>Instrucciones de implementación

Para implementar, siga los pasos de [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO4-->


