---
title: "Habilitación o deshabilitación de la integración de LinkedIn para las aplicaciones de Office en Azure Active Directory | Microsoft Docs"
description: "Explica cómo habilitar o deshabilitar la integración de LinkedIn para las aplicaciones de Microsoft en Azure Active Directory"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cdfb5458b020e9d3a3f33cecbeb0ee7b9a48909d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Integración de LinkedIn para las aplicaciones de Office
En este artículo se explica cómo restringir los usuarios a los que se proporciona la integración de LinkedIn en Azure Active Directory (Azure AD). La integración de LinkedIn está habilitada de forma predeterminada al agregarla al inquilino, lo que permite a los usuarios acceder a datos públicos de LinkedIn desde algunas de las aplicaciones de Microsoft. Los usuarios pueden elegir independientemente si conectan la cuenta profesional o educativa a la cuenta de LinkedIn.

> [!IMPORTANT]
> La integración de LinkedIn no se implementa en todos los inquilinos de Azure AD al mismo tiempo. Una vez que se implanta en el inquilino de Azure, se habilita de forma predeterminada. No está disponible para los inquilinos go-local, sovereign y government. Para obtener una vista actualizada de la información de implementación, consulte la página del [plan de Office 365](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc).

## <a name="linkedin-integration-from-the-user-perspective"></a>Integración de LinkedIn desde la perspectiva del usuario
Cuando los usuarios de la organización conectan la cuenta de LinkedIn a la cuenta profesional o educativa, [permiten que LinkedIn proporcione datos](https://www.linkedin.com/help/linkedin/answer/84077) para su uso en las aplicaciones y los servicios de Microsoft que proporciona la organización. [Los usuarios pueden desconectar las cuentas](https://www.linkedin.com/help/linkedin/answer/85097), lo cual quitaría el permiso a LinkedIn de compartir datos con Microsoft. La integración de LinkedIn utiliza la información de perfil público de LinkedIn disponible. [Los usuarios pueden controlar el modo de visualización de su perfil de LinkedIn](https://www.linkedin.com/help/linkedin/answer/83) mediante la configuración de privacidad de LinkedIn, incluso si su perfil se puede ver en las aplicaciones de Microsoft.

## <a name="privacy-considerations"></a>Consideraciones sobre privacidad
Habilitar la integración de LinkedIn en Azure AD permite el acceso de las aplicaciones y los servicios de Microsoft a información de los usuarios de LinkedIn. Consulte la [Declaración de privacidad de Microsoft](https://privacy.microsoft.com/privacystatement/) para más información sobre las consideraciones de privacidad al habilitar la integración de LinkedIn en Azure AD. 

## <a name="manage-linkedin-integration"></a>Administración de la integración con LinkedIn
La integración de LinkedIn para empresas está habilitada de forma predeterminada en Azure AD. Habilitar la integración de LinkedIn permite a todos los usuarios de la organización usar características de LinkedIn en los servicios de Microsoft, como ver perfiles de LinkedIn en Outlook. Al deshabilitarla se quitan las características de LinkedIn de las características y los servicios de Microsoft y se deja de compartir datos entre LinkedIn y la organización a través de los servicios de Microsoft.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Habilitación o deshabilitación de la integración de LinkedIn para la organización en Azure Portal

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com/) con una cuenta de administrador global del inquilino de Azure AD.
2. Seleccione **Usuarios**.
3. En la hoja **Usuarios**, seleccione **Configuración de usuario**.
4. En **Integración de LinkedIn**, seleccione **Sí** o **No** para habilitar o deshabilitar la integración de LinkedIn.
   ![Habilitación de la integración de LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Habilitación o deshabilitación de la integración de LinkedIn para las aplicaciones de Office 2016 de la organización mediante una directiva de grupo

1. Descargue los [archivos de plantilla de administración de Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extraiga los archivos **ADMX** y cópielos en el **repositorio central**.
3. Abra la Administración de directivas de grupo.
4. Cree un objeto de directiva de grupo con la siguiente configuración: **Configuración de usuario** > **Plantillas administrativas** > **Microsoft Office 2016** > **Varios** > **Allow LinkedIn Integration** (Permitir integración de LinkedIn).
5. Seleccione **Habilitado** o **Deshabilitado**.
  * Cuando la directiva está **habilitada**, la configuración **Show LinkedIn features in Office applications** (Mostrar características de LinkedIn en las aplicaciones de Office) del cuadro de diálogo Opciones de Office 2016 está habilitada. Esto también significa que los usuarios de la organización pueden usar las características de LinkedIn en las aplicaciones de Office.
  * Cuando la directiva está **deshabilitada**, la configuración **Show LinkedIn features in Office applications** (Mostrar características de LinkedIn en las aplicaciones de Office) del cuadro de diálogo Opciones de Office 2016 está deshabilitada y los usuarios no pueden cambiarla. Los usuarios de la organización no podrán usar las características de LinkedIn en las aplicaciones de Office 2016. 

Esta directiva de grupo afecta solo a las aplicaciones de Office 2016 de los equipos locales. Los usuarios pueden ver las características de LinkedIn en las tarjetas de perfil de Office 365, aunque se deshabilite LinkedIn en las aplicaciones de Office 2016. 

### <a name="learn-more"></a>Más información 
* [Información y características de LinkedIn en las aplicaciones de Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ayuda de LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Pasos siguientes
Utilice el siguiente vínculo para ver la configuración de integración de LinkedIn actual en Azure Portal:

[Configuración de la integración con LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 