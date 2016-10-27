<properties
    pageTitle="Azure AD Connect y la federación | Microsoft Azure"
    description="Esta página es la ubicación central para toda la documentación relacionada con las operaciones de AD FS mediante Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/14/2016"
    ms.author="anandy"/>



# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect y la federación

Azure AD Connect le permite configurar la federación con AD FS y Azure AD en un entorno local. Con el inicio de sesión federado, puede habilitar a los usuarios para que inicien sesión en los servicios basados en Azure AD con sus contraseñas locales y, mientras se encuentren en la red corporativa, sin necesidad de volver a escribir sus contraseñas. La opción de federación con AD FS le permite implementar servicios AD FS nuevos o especificar servicios AD FS existentes en la granja de Windows Server 2012 R2.

Este tema constituye la principal referencia de las funcionalidades relacionadas con la federación correspondientes a Azure AD Connect y ofrece vínculos a todos los demás temas asociados a él. Para obtener vínculos a Azure AD Connect, consulte Integración de las identidades locales con Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect: temas de federación

| Tema. | ¿Qué aspectos cubre y cuándo debe leerlo? |
|:------|:-----------|
| **Opciones para el inicio de sesión de los usuarios en Azure AD Connect** ||
| [Opciones para el inicio de sesión de los usuarios en Azure AD Connect](active-directory-aadconnect-user-signin.md) | Descripción de varias opciones de inicio de sesión de usuario y cómo afectan a la experiencia de inicio de sesión del usuario en Azure |
| **Instalación de AD FS mediante Azure AD Connect**||
| [Requisitos previos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Requisitos previos para una correcta instalación de AD FS a través de Azure AD Connect|
| [Configuración de la granja de servidores de AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Cómo instalar una nueva granja de servidores de AD FS mediante Azure AD Connect |
| **Modificación de la configuración de AD FS** | |
| [Reparación de la confianza](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Reparación de confianza actual entre locales de AD FS y Office 365 o Azure |
| [Incorporación de un nuevo servidor de AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Expansión de la granja de servidores de AD FS con servidores de AD FS adicionales posteriores a la instalación inicial |
| [Incorporación de un nuevo servidor WAP de AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Expansión de la granja de servidores de AD FS con servidores de WAP adicionales posteriores a la instalación inicial |
| [Incorporación de un nuevo dominio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Incorporación de otro dominio para la federación con Azure AD |
|**Tareas posteriores a la instalación**||
| [Incorporación de un logotipo de la compañía personalizado o una ilustración](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modificación de la experiencia de inicio de sesión especificando el logotipo personalizado que se mostrará en la página de inicio de sesión de AD FS |
| [Incorporación de la descripción de inicio de sesión](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Cambio de la descripción de inicio de sesión en la página de inicio de sesión de AD FS | 
| [Modificación de las reglas de notificaciones de AD FS](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modificación o incorporación de reglas de notificaciones de AD FS que se correspondan con la configuración de sincronización de Azure AD Connect |


## <a name="additional-resources"></a>Recursos adicionales

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
* [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)




<!--HONumber=Oct16_HO2-->


