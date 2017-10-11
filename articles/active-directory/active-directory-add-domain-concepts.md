---
title: "Información general conceptual de los nombres de dominio personalizados en Azure Active Directory | Microsoft Docs"
description: "Explica la plataforma conceptual para el uso de nombres de dominio personalizado en Azure Active Directory, incluida la federación para el inicio de sesión único."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fd0c5def-0da2-43af-81bc-76f4cfe86afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.openlocfilehash: 3c591680160101a91174868714392674c9aa7178
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Información general conceptual de nombres de dominio personalizado en Azure Active Directory
Un nombre de dominio puede ser un identificador importante para muchos recursos de directorio, como parte de:

* Un nombre de usuario o una dirección de correo electrónico de un usuario
* La dirección de un grupo
* El identificador URI del id. de aplicación para una aplicación

Un recurso en Azure Active Directory (Azure AD) puede incluir un nombre de dominio cuya pertenencia al directorio que contiene el recurso ya se haya verificado. Solo los administradores globales pueden realizar tareas de administración de Azure AD.

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo. Para saber cómo administrar los nombres de dominio en el centro de administración de Azure AD, vea [Administración de los nombres de dominio personalizados en Azure Active Directory](active-directory-domains-manage-azure-portal.md).

Los nombres de dominio en Azure AD son únicos globalmente. Un nombre de dominio personalizado puede utilizarlo un único inquilino de Azure AD a la vez. Si un directorio de Azure AD ha comprobado un nombre de dominio, ningún otro directorio de Azure AD puede comprobar o usar ese mismo nombre de dominio.

## <a name="initial-and-custom-domain-names"></a>Nombres de dominio personalizados e iniciales
Cada nombre de dominio en Azure AD es un nombre de dominio inicial o un nombre de dominio personalizado.

Cada instancia de Azure AD incluye un nombre de dominio inicial en el formulario de contoso.onmicrosoft.com. Este nombre de dominio de tercer nivel, en este ejemplo "contoso.onmicrosoft.com", se estableció cuando se creó el directorio, normalmente por el administrador que lo hizo. El nombre de dominio inicial de un directorio no se puede modificar ni eliminar. El nombre de dominio inicial, aunque es totalmente funcional, está diseñado principalmente para utilizarse como mecanismo de arranque hasta que se comprueba un nombre de dominio personalizado.

En la mayoría de los entornos de producción, un directorio tiene al menos un dominio personalizado comprobado, como "contoso.com", y es ese dominio personalizado el que es visible para los usuarios finales. Un nombre de dominio personalizado es un nombre de dominio que pertenece a la organización que lo utiliza (por ejemplo, "contoso.com") para fines como el alojamiento de su sitio web. Este nombre de dominio es familiar a los empleados, ya que forma parte del nombre de usuario que usan para iniciar sesión en la red corporativa, o para enviar y recuperar el correo electrónico.

Antes de poder usarlo con Azure AD, el nombre de dominio personalizado debe agregarse al directorio y comprobarse.

## <a name="verified-and-unverified-domain-names"></a>Nombres de dominio comprobados y no comprobados
El nombre de dominio inicial para un directorio se evalúa implícitamente como comprobado por Azure AD. Cuando un administrador agrega un nombre de dominio personalizado a una instancia de Azure AD, inicialmente se encuentra en un estado no comprobado. Azure AD no permitirá a los recursos de directorio usar un nombre de dominio no comprobado. De esta forma se garantiza que solo un directorio puede utilizar un nombre de dominio particular, y que la organización que usa el nombre de dominio es efectivamente su propietaria.

Azure AD comprueba la propiedad de un nombre de dominio mediante la búsqueda de una entrada determinada en el archivo de zona de servicio de nombres de dominio (DNS) para el nombre de dominio. Para comprobar la propiedad de un nombre de dominio, un administrador obtiene la entrada DNS desde Azure AD que Azure AD buscará, y agrega esa entrada al archivo de zona de DNS para el nombre de dominio. El archivo de zona de DNS se mantiene por el registrador de nombres de dominio para ese dominio. En el artículo para [agregar un dominio personalizado a su directorio de Azure AD](active-directory-add-domain.md)se muestran los pasos para comprobar un dominio.

La adición de una entrada de DNS al archivo de zona para el nombre de dominio no afecta a otros servicios de dominio, como el correo electrónico o el hospedaje web.

## <a name="federated-and-managed-domain-names"></a>Nombres de dominio federados y administrados
Puede configurarse un nombre de dominio personalizado en Azure AD para proporcionar a los usuarios una experiencia de inicio de sesión federada entre su instancia de Active Directory en local y Azure AD. Configurar un dominio para la federación requiere actualizaciones para recursos con privilegios en Azure AD y también para su instancia de Windows Server Active Directory. La configuración de un dominio federado debe realizarse desde Azure AD Connect o con PowerShell. No se puede iniciar la federación de un dominio personalizado desde el Portal de Azure clásico. [Vea este vídeo para obtener más información acerca de la configuración de AD FS para el inicio de sesión de usuario con Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Los dominios que no están federados a veces se denominan dominios administrados. El dominio inicial para un directorio de Azure AD se evalúa implícitamente como dominio administrado.

## <a name="primary-domain-names"></a>Nombres de dominio principales
El nombre de dominio principal de un directorio es el nombre de dominio que se preselecciona como valor predeterminado para la parte "dominio" del nombre de usuario, cuando un administrador crea un usuario en [Azure Portal](https://portal.azure.com/) o en otro portal, como el portal de administración de Office 365 o el portal de Microsoft Intune. Un directorio puede tener solo un nombre de dominio principal. El administrador puede cambiar el nombre de dominio principal para que sea cualquier dominio personalizado comprobado que no esté federado o el dominio principal.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Nombres de dominio en Azure AD y otros servicios Microsoft Online Services
El nombre de dominio debe comprobarse en Azure AD antes de poder ser utilizado por otros servicios Microsoft Online Services, como Exchange Online, SharePoint Online e Intune. Estos otros servicios por lo general requieren que un administrador agregue una o más entradas de DNS que son específicas del servicio.

Una aplicación web de Azure usa su propio mecanismo para comprobar la propiedad de un dominio. El dominio debe comprobarse para su uso con Azure AD, incluso si se ha hecho anteriormente para que lo utilice una aplicación web de Azure en una suscripción que depende de dicha instancia de Azure AD. La aplicación web de Azure puede utilizar un nombre de dominio que se ha comprobado en otro directorio diferente del directorio que protege la aplicación web.

## <a name="managing-domain-names"></a>Administración de nombres de dominio
Las tareas de administración de dominio se pueden completar desde el Portal de Azure clásico y desde PowerShell. Muchas tareas se pueden realizar mediante la API Graph de Azure AD.

* [Utilización de nombres de dominio personalizados para simplificar la experiencia de inicio de sesión de los usuarios](active-directory-add-domain.md)
* [Managing custom domain names in your Azure Active Directory (Administración de nombres de dominio personalizados en Azure Active Directory)](active-directory-add-manage-domain-names.md)
* [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Operations on domains (preview) | Graph API reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

