---
title: "Introducción a Azure Active Directory | Microsoft Docs"
description: "Obtener licencias, añadir el nombre del dominio, crear una página de inicio de sesión personalizada y añadir el autoservicio de restablecimiento de contraseña en Azure Active Directory"
keywords: 
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.prod: 
ms.service: active-directory
ms.workload: identity
ms.technology: 
ms.assetid: 
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: 9e1a7337c2477455aa0b56f2147f46f3bf5293a8
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-ad"></a>Introducción a Azure AD
La administración de identidades moderna requiere confiabilidad escalable y coherente para garantizar la disponibilidad de las aplicaciones y servicios solo para los usuarios autenticados. Para admitir adecuadamente las necesidades de administración de identidad de los usuarios, el departamento de TI necesita una manera para proporcionar acceso a aplicaciones de software como servicio (SaaS) aprobadas y públicas, una manera de hospedar aplicaciones internas de línea de negocio e incluso formas de mejorar el desarrollo y uso de aplicaciones locales. Todos estos requisitos apuntan a la necesidad de una solución de administración de identidad basada en la nube.      

Azure Active Directory (Azure AD) es el directorio basado en la nube de varios inquilinos y el servicio de administración de identidades de Microsoft. Azure AD combina servicios de directorio fundamentales, gobierno avanzado de la identidad y administración del acceso a las aplicaciones. El diseño multiempresa, distribuido geográficamente y de alta disponibilidad de Azure AD significa que puede confiar en él para sus necesidades empresariales más críticas.

Azure AD incluye un conjunto completo de funcionalidades de administración de identidades, incluidas la posibilidad de sincronizar la información de los recursos locales, una marca de empresa personalizable, una administración de licencias simple y una administración de contraseñas incluso sin intervención del administrador. Estas funcionalidades de sencilla implementación pueden ayudar a utilizar Azure AD para proteger aplicaciones basadas en la nube, optimizar los procesos de TI, reducir costos y asegurar que se alcanzan los objetivos de cumplimiento normativo corporativos.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

El resto de este artículo explica cómo empezar a trabajar con Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Suscripción a Azure Active Directory Premium
Para [empezar a trabajar con Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md), puede comprar licencias y asociarlas a su suscripción de Azure. Si crea una nueva suscripción de Azure, debe activar también el plan de licencias y el acceso al servicio de Azure AD. 

Para suscribirse a Active Directory Premium, cuenta con varias opciones: 

- Use su suscripción a Office 365 o Azure
- Use un plan de licencias de Enterprise Mobility + Security
- Use un plan de Licencias por volumen de Microsoft

### <a name="verification-step"></a>Paso de comprobación
Después de activar la suscripción, asegúrese de que puede iniciar sesión en el servicio.

## <a name="add-a-custom-domain-name"></a>Adición de un dominio personalizado
Cada directorio de Azure AD incluye un nombre de dominio inicial con el formato *nombre de dominio*.onmicrosoft.com. El nombre de dominio inicial no se puede cambiar ni eliminar, pero también se puede 	[agregar el nombre de dominio corporativo a Azure AD](add-custom-domain.md). Por ejemplo, puede que su organización tenga otros nombres de dominio que se usan para hacer negocios y usuarios que inician sesión con el nombre de dominio corporativo. La incorporación de nombres de dominio personalizados a Azure AD le permite asignar en el directorio nombres de usuario conocidos para sus usuarios, como "alice@contoso.com". en lugar de "alice@.onmicrosoft.com". El proceso es simple:

1. Incorporación de nombres de dominio personalizados al directorio
2. Incorporación de la entrada DNS para el nombre de dominio en el registrador de nombres de dominio
3. Comprobación del nombre de dominio personalizado en Azure AD

### <a name="verification-step"></a>Paso de comprobación
Después de agregar un dominio personalizado, asegúrese de que el estado **Comprobado** aparece en la hoja **Nombres de dominio personalizados** del portal de Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Adición de personalización de marca a la página de inicio de sesión 
Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. Azure Active Directory (Azure AD) ofrece esta funcionalidad, ya que permite [personalizar la apariencia de la página de inicio de sesión con el logotipo de la empresa y combinaciones de colores personalizadas](customize-branding.md). La página de inicio es la página que aparece al iniciar sesión en Office 365 u otras aplicaciones web que estén utilizando Azure AD como proveedor de identidades. Interactuará con esta página para especificar las credenciales.

### <a name="verification-step"></a>Paso de comprobación
Inicie sesión en Azure Portal y asegúrese de que la página de inicio de sesión personalizada y las personalizaciones de idioma adicionales se han configurado correctamente. 

## <a name="add-new-users"></a>Adición de nuevos usuarios
Puede [agregar nuevos usuarios a la instancia de Azure AD de su organización](add-users-azure-active-directory.md) de uno en uno mediante Azure Portal o mediante la sincronización de los datos de los recursos de Windows Server AD local. Puede agregar usuarios en la nube directamente desde el portal de Azure AD o sincronizar la información de usuarios locales.

Para habilitar la sincronización de identidades local con Azure AD, debe instalar y configurar [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) en un servidor de su organización. Esta aplicación administra la sincronización de usuarios y grupos de su origen de identidades existente con su inquilino de Azure AD.

### <a name="verification-step"></a>Paso de comprobación
Después de crear o sincronizar usuarios nuevos, asegúrese de que están visibles en Azure AD.

## <a name="assign-licenses"></a>Asignación de licencias
Aunque para configurar las funcionalidades de pago todo lo que necesita es una suscripción, debe [asignar licencias a los usuarios](license-users-groups.md) para las características de pago de Azure AD Premium. Se debe asignar una licencia a los usuarios que deban disponer de acceso o que puedan administrarse a través de una característica de pago de Azure AD. Una asignación de licencia es una asignación entre un usuario y un servicio comprado, como Azure AD Premium, Basic o Enterprise Mobility + Security.

Puede usar asignación de licencias basada en grupos para configurar reglas como en los siguientes ejemplos:

- Todos los usuarios del directorio reciben automáticamente una licencia
- Todos los usuarios con el puesto adecuado reciben una licencia
- Puede delegar la decisión en otros administradores de la organización (mediante el uso de grupos de autoservicio)

### <a name="verification-step"></a>Paso de comprobación
Revise las licencias asignadas y disponibles en **Azure Active Directory** > **Licencias** > **Todos los productos**.

## <a name="configure-self-service-password-reset"></a>Configuración del autoservicio de restablecimiento de contraseña
El [autoservicio de restablecimiento de contraseña (SSPR)](active-directory-passwords-getting-started.md) ofrece un medio sencillo con el que los administradores de TI pueden permitir que los usuarios restablezcan o desbloqueen sus cuentas o contraseñas. El sistema incluye informes detallados para realizar un seguimiento de cuándo usan los usuarios el sistema, además de notificaciones para alertar de posibles abusos o usos indebidos.

### <a name="verification-step"></a>Paso de comprobación
Revise las propiedades de SSPR habilitadas en **Azure Active Directory** > **Restablecimiento de contraseña** para asegurarse de que se han efectuado las asignaciones de grupo y usuario adecuadas. 


## <a name="next-steps"></a>pasos siguientes
[Página de servicio de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

[Página de información de precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)