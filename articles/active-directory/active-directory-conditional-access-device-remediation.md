---
title: "Solución de problemas de acceso de Azure Active Directory | Microsoft Docs"
description: "Conozca los pasos para resolver problemas de acceso a recursos en línea de su organización."
services: active-directory
keywords: acceso condicional basado en dispositivo, registro de dispositivo, habilitar registro de dispositivo, registro de dispositivo y MDM
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2076f22c6048fda83d6da3b069e2805afb453f


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Solución de problemas de acceso de Azure Active Directory
Al intentar obtener acceso a la intranet de SharePoint Online de su organización, recibe un mensaje de error de "acceso denegado". ¿Qué puede hacer?


En este artículo se explican los pasos para resolver problemas de acceso a recursos en línea de su organización.

Para obtener ayuda para problemas de acceso de Azure Active Directory (Azure AD), vaya a la sección del artículo en la que se trata la plataforma de su dispositivo:

* Dispositivo Windows
* Dispositivo iOS (pronto se ofrecerá asistencia para iPhone y iPad)
* Dispositivo Android (pronto se ofrecerá asistencia para teléfonos y tabletas Android)

## <a name="access-from-a-windows-device"></a>Acceso desde un dispositivo Windows
Si su dispositivo utiliza una de las siguientes plataformas, busque en las siguientes secciones el mensaje de error que aparece cuando intenta acceder a una aplicación o servicio:

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>El dispositivo no está registrado
Si el dispositivo no está registrado en Azure AD y la aplicación está protegida mediante una directiva basada en dispositivo, puede ver una página con uno de los siguientes errores:

![Mensajes de tipo "No se puede llegar allí desde aquí" para dispositivos no registrados](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Si el dispositivo está unido al dominio a la instancia de Active Directory de su organización, pruebe lo siguiente:

1. Asegúrese de que ha iniciado sesión en Windows con su cuenta de trabajo (su cuenta de Active Directory).
2. Conéctese a su red corporativa a través de una red privada virtual (VPN) o DirectAccess.
3. Una vez conectado, bloquee la sesión de Windows presionando a la vez la tecla Windows y la tecla L.
4. Especifique las credenciales de su cuenta profesional para desbloquear la sesión de Windows.
5. Espere un minuto e intente de nuevo acceder a la aplicación o servicio.
6. Si aparece la misma página, haga clic en **Más detalles** y póngase en contacto con el administrador para informarle de los detalles.

Si el dispositivo no está unido al dominio y ejecuta Windows 10, tiene dos opciones:

* Ejecutar Azure AD Join
* Agregar su cuenta profesional o educativa a Windows

Para obtener información acerca de las diferencias entre estas opciones, consulte [Uso de dispositivos de Windows 10 en el área de trabajo](active-directory-azureadjoin-windows10-devices.md).

Para ejecutar Azure AD Join, complete los siguientes pasos para la plataforma que utiliza su dispositivo. (Azure AD Join no está disponible en teléfonos Windows).

**Actualización de aniversario de Windows 10**

1. Abra la aplicación de **configuración** .
2. Haga clic en **Cuentas** > **Access work or school** (Acceder a la cuenta profesional o educativa).
3. Haga clic en **Conectar**.
4. Haga clic en **Join this device to Azure AD** (Conectar este dispositivo a Azure AD).
5. Autentique su organización, proporcione Multi-Factor Authentication, si fuera necesario, y siga los pasos que se muestran.
6. Cierre la sesión y vuelva a iniciarla con su cuenta profesional.
7. Intente volver a acceder a la aplicación.

**Actualización de Windows de 10 de noviembre de 2015**

1. Abra la aplicación de **configuración** .
2. Haga clic en **Sistema** > **About** (Acerca de).
3. Haga clic en **Unirse a Azure AD**.
4. Autentique su organización, proporcione Multi-Factor Authentication, si fuera necesario, y siga los pasos que se muestran.
5. Cierre la sesión y vuelva a iniciarla con su cuenta profesional (cuenta de Azure AD).
6. Intente volver a acceder a la aplicación.

Para agregar su cuenta profesional o educativa, siga estos pasos:

**Actualización de aniversario de Windows 10**

1. Abra la aplicación de **configuración** .
2. Haga clic en **Cuentas** > **Access work or school** (Acceder a la cuenta profesional o educativa).
3. Haga clic en **Conectar**.
4. Autentique su organización, proporcione Multi-Factor Authentication, si fuera necesario, y siga los pasos que se muestran.
5. Intente volver a acceder a la aplicación.

**Actualización de Windows de 10 de noviembre de 2015**

1. Abra la aplicación de **configuración** .
2. Haga clic en **Cuentas** > **Your accounts** (Sus cuentas).
3. Haga clic en **Add work or school account**(Agregar cuenta profesional o educativa).
4. Autentique su organización, proporcione Multi-Factor Authentication, si fuera necesario, y siga los pasos que se muestran.
5. Intente volver a acceder a la aplicación.

Si el dispositivo no está unido a un dominio y ejecuta Windows 8.1, para unirse al lugar de trabajo e inscribirse en Microsoft Intune, siga estos pasos:

1. Abra **Configuración de PC**.
2. Haga clic en **Red** > **Área de trabajo**.
3. Haga clic en **Unir**.
4. Autentique su organización, proporcione Multi-Factor Authentication, si fuera necesario, y siga los pasos que se muestran.
5. Haga clic en **Activar**.
6. Intente volver a acceder a la aplicación.

### <a name="browser-is-not-supported"></a>El explorador no es compatible.
Es posible que se le deniegue el acceso si intenta acceder a una aplicación o servicio con uno de los siguientes exploradores:

* Chrome, Firefox o cualquier otro explorador que no sea Microsoft Edge ni Microsoft Internet Explorer en Windows 10 o Windows Server 2016.
* Firefox en Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

Se mostrará una página de error como la siguiente:

![Mensaje "No se puede llegar allí desde aquí" para exploradores no admitidos](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

La única solución es utilizar un explorador compatible con la aplicación de la plataforma del dispositivo.

## <a name="next-steps"></a>Pasos siguientes
[Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO2-->


