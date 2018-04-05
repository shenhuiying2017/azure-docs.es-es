---
title: Habilitación de conexiones de LinkedIn para aplicaciones y servicios de Microsoft en Azure Active Directory | Microsoft Docs
description: Se explica cómo habilitar o deshabilitar conexiones de cuentas de LinkedIn para aplicaciones de Microsoft en Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/22/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 33e3305288edc3990ed88b39c819293a8adc2dfe
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Conexiones de cuentas de LinkedIn para aplicaciones y servicios de Microsoft
En este artículo, puede aprender a administrar conexiones de cuentas de LinkedIn para su inquilino en el centro de administración de Azure Active Directory (Azure AD). 

> [!IMPORTANT]
> La funcionalidad de conexiones de cuentas de LinkedIn se encuentra actualmente en proceso de implementación en inquilinos de Azure AD. Cuando se implemente en su inquilino, estará habilitada de forma predeterminada. No está disponible para clientes del gobierno de Estados Unidos y organizaciones con buzones de Exchange Online hospedados en Australia, Canadá, China, Francia, Alemania, India, Corea del Sur, Reino Unido, Japón y Sudáfrica. La compatibilidad con estas ubicaciones de buzón estará disponible próximamente.  Para obtener una vista actualizada de la información de implementación, consulte la página del [plan de Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc).

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Cómo le aparecen al usuario las conexiones de cuentas de LinkedIn
Las conexiones de cuentas de LinkedIn permiten a los usuarios ver la información pública del perfil de LinkedIn dentro de algunas de sus aplicaciones de Microsoft. Los usuarios de su inquilino pueden elegir conectar sus cuentas profesionales o educativas de LinkedIn y Microsoft para ver información adicional del perfil de LinkedIn. Para más información, consulte [Información y características de LinkedIn en aplicaciones y servicios de Microsoft](https://go.microsoft.com/fwlink/?linkid=850740).

Cuando los usuarios de su organización conectan sus cuentas profesionales o educativas de LinkedIn y Microsoft, tienen dos opciones: 
* Conceder permiso para compartir datos entre las dos cuentas. Esto significa otorgar permiso a su cuenta de LinkedIn para compartir datos con su cuenta profesional o educativa de Microsoft, y otorgar permiso a su cuenta profesional o educativa de Microsoft para compartir datos con su cuenta de LinkedIn. Los datos que se comparten con LinkedIn abandonan los servicios en línea. 
* Conceder permiso para compartir datos únicamente entre la cuenta de LinkedIn y la cuenta de trabajo o educativa de Microsoft.

Para más información sobre los datos que se comparten entre las cuentas de trabajo o educativas de LinkedIn y Microsoft de los usuarios, consulte [LinkedIn en las aplicaciones de Microsoft en tu trabajo o universidad](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Los usuarios pueden desconectar cuentas](https://www.linkedin.com/help/linkedin/answer/85097) y quitar permisos de uso compartido de datos en cualquier momento. 
* [Los usuarios pueden controlar el modo de visualización de su perfil de LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), por ejemplo, si su perfil se puede ver en las aplicaciones de Microsoft.

## <a name="privacy-considerations"></a>Consideraciones sobre privacidad
Habilitar las conexiones de cuentas de LinkedIn permite el acceso de las aplicaciones y los servicios de Microsoft a información de los usuarios de LinkedIn. Lea la [Declaración de privacidad de Microsoft](https://privacy.microsoft.com/privacystatement/) para más información sobre las consideraciones de privacidad al habilitar las conexiones de cuentas de LinkedIn en Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Administración de las conexiones de cuentas de LinkedIn
La funcionalidad de conexiones de cuentas de LinkedIn está activa de forma predeterminada en todo el inquilino. Puede elegir deshabilitar las conexiones de cuentas de LinkedIn en todo el inquilino o habilitarlas para usuarios determinados. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Habilitar o deshabilitar conexiones de cuentas de LinkedIn en el inquilino en Azure Portal

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com/) con una cuenta de administrador global del inquilino de Azure AD.
2. Seleccione **Usuarios**.
3. En la hoja **Usuarios**, seleccione **Configuración de usuario**.
4. En **LinkedIn account connections** (Conexiones de cuentas de LinkedIn), siga estos pasos:
  * Seleccione **Sí** para habilitar las conexiones de cuentas de LinkedIn en todos los usuarios de su inquilino.
  * Elija **Selected** (Seleccionados) para habilitar las conexiones de cuentas de LinkedIn solo en determinados usuarios del inquilino.
  * Seleccione **No** para deshabilitar las conexiones de cuentas de LinkedIn en todos los usuarios. ![Habilitación de conexiones de cuentas de LinkedIn](./media/linkedin-integration/LinkedIn-integration.png)
5. Guardar la configuración cuando haya terminado seleccionando **Guardar**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Habilitar o deshabilitar las conexiones de cuentas de LinkedIn en las aplicaciones de Office 2016 de la organización mediante directiva de grupo

1. Descargue los [archivos de plantilla de administración de Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraiga los archivos **ADMX** y cópielos en el almacén central.
3. Abra la Administración de directivas de grupo.
4. Cree un objeto de directiva de grupo con la siguiente configuración: **Configuración de usuario** > **Plantillas administrativas** > **Microsoft Office 2016** > **Varios** > **Show LinkedIn features in Office applications**(Mostrar características de LinkedIn en las aplicaciones de Office).
5. Seleccione **Habilitado** o **Deshabilitado**.
  * Cuando la directiva está **habilitada**, la configuración **Show LinkedIn features in Office applications** (Mostrar características de LinkedIn en las aplicaciones de Office) del cuadro de diálogo Opciones de Office 2016 está habilitada. Esto también significa que los usuarios de la organización pueden usar las características de LinkedIn en las aplicaciones de Office.
  * Cuando la directiva está **deshabilitada**, la configuración **Show LinkedIn features in Office applications** (Mostrar características de LinkedIn en las aplicaciones de Office) del cuadro de diálogo Opciones de Office 2016 está deshabilitada y los usuarios no pueden cambiarla. Los usuarios de la organización no podrán usar las características de LinkedIn en las aplicaciones de Office 2016. 

Esta directiva de grupo afecta solo a las aplicaciones de Office 2016 de los equipos locales. Los usuarios pueden ver las características de LinkedIn en las tarjetas de perfil de Office 365, aunque se deshabilite LinkedIn en las aplicaciones de Office 2016. 

### <a name="learn-more"></a>Más información 
* [Información y características de LinkedIn en las aplicaciones de Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ayuda de LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Pasos siguientes
Use el siguiente vínculo para ver la configuración actual de las conexiones de cuentas de LinkedIn en Azure Portal:

[Configuración de las conexiones de cuentas de LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 