---
title: ¿Qué es la colaboración B2B de Azure Active Directory? | Microsoft Docs
description: La colaboración B2B de Azure Active Directory posibilita las relaciones entre empresas al permitir que los asociados comerciales tengan acceso de forma selectiva a las aplicaciones corporativas.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2bc405e6356113e0423f833868c86890c0c3d5d2
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259717"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>¿Qué es la colaboración B2B de Azure AD?

Las funcionalidades de colaboración de negocio a negocio (B2B) de Azure Active Directory (Azure AD) permiten que cualquier organización con Azure AD funcione de forma segura con usuarios de cualquier otra organización, pequeña o grande. Esas organizaciones pueden estar asociadas o no a Azure AD, y ni siquiera necesitan tener un departamento de TI.

Las organizaciones que usan Azure AD pueden proporcionar acceso a documentos, recursos y aplicaciones a sus asociados, mientras mantienen un control total sobre sus propios datos corporativos. Los desarrolladores pueden usar las API de negocio a negocio de Azure AD para escribir aplicaciones que unan a dos organizaciones de una manera más segura. Además, la navegación resulta sencilla para los usuarios finales.

El vídeo siguiente proporciona una introducción práctica.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Ventajas clave de la colaboración B2B de Azure AD

### <a name="work-with-any-user-from-any-partner"></a>Permite trabajar con cualquier usuario de cualquier asociado.

- Los asociados usan sus propias credenciales.
- Los asociados no necesitan usar Azure AD.
- No se necesita ningún directorio externo o instalación compleja.

### <a name="simple-and-secure-collaboration"></a>Colaboración sencilla y segura

- Proporciona acceso a cualquier aplicación o datos corporativos, al aplicar avanzadas directivas de autorización con tecnología de Azure AD.
- Fácil para los usuarios
- Seguridad de nivel empresarial para aplicaciones y datos.

### <a name="no-management-overhead"></a>Sin sobrecarga de administración.

- Sin administración externa de contraseñas o cuentas.
- Sin administración del ciclo de vida de cuenta manual o sincronización.
- Sin sobrecarga administrativa externa.

## <a name="easily-add-b2b-collaboration-users"></a>Incorporación sencilla de usuarios de colaboración B2B

Como administrador, puede agregar fácilmente usuarios de colaboración B2B (invitados) a su organización en [Azure Portal](https://portal.azure.com).

![agregar usuarios invitados](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Permiso a los colaboradores para que traigan su propia identidad

Los colaboradores de B2B pueden iniciar sesión con la identidad que elijan. Si el usuario no tiene una cuenta de Microsoft o de Azure AD, se crea una en su nombre en el momento del canje de la oferta.

### <a name="delegate-to-application-and-group-owners"></a>Delegación a propietarios de grupos y aplicaciones

Como propietario de aplicaciones o grupos, puede agregar usuarios de B2B directamente a cualquier aplicación que le interese, ya sea una aplicación de Microsoft o no. Los administradores pueden delegar el permiso para agregar usuarios de B2B a los usuarios no administradores. Los usuarios no administradores pueden utilizar el [panel de acceso a la aplicación de Azure AD](https://myapps.microsoft.com) para agregar los usuarios de colaboración B2B a las aplicaciones o los grupos.

![panel de acceso](media/what-is-b2b/access-panel.png)

![agregar miembro](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Protección de las directivas de autorización al contenido corporativo

Las directivas de acceso condicional, como la autenticación multifactor, se pueden aplicar:
- En el nivel de inquilino
- En el nivel de aplicación
- A usuarios específicos para proteger los datos y las aplicaciones empresariales.

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Uso de API y el código de ejemplo para crear fácilmente aplicaciones para realizar una incorporación

Incorpore a los asociados externos de manera personalizada según las necesidades de su organización.

Use las [API de invitación de colaboración B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) para personalizar sus experiencias de incorporación, incluida la creación de portales de suscripción de autoservicio. Proporcionamos código de ejemplo para un portal de autoservicio [en Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portal de suscripción](media/what-is-b2b/sign-up-portal.png)

Con la colaboración B2B de Azure AD, puede obtener toda la funcionalidad de Azure AD para proteger sus relaciones con los asociados de forma que los usuarios finales la encuentren sencilla e intuitiva.

## <a name="next-steps"></a>Pasos siguientes

- [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](add-users-administrator.md)
- [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](add-users-information-worker.md)
- [Canje de invitación de colaboración B2B](redemption-experience.md)
- [Concesión de licencias de colaboración B2B de Azure AD](licensing-guidance.md)
- Y, como siempre, conéctese con el equipo del producto para obtener comentarios, conversaciones y sugerencias a través de nuestra [Comunidad tecnológica de Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).