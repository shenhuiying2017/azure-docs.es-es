<properties
	pageTitle="Información general conceptual de nombres de dominio personalizado en Azure Active Directory | Microsoft Azure"
	description="Explica la plataforma conceptual para el uso de nombres de dominio personalizado en Azure Active Directory, incluida la federación para el inicio de sesión único."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Información general conceptual de nombres de dominio personalizado en Azure Active Directory

Un nombre de dominio es una parte importante del identificador para muchos recursos de directorio: forma parte de un nombre de usuario o una dirección de correo electrónico de un usuario, forma parte de la dirección para un grupo y puede formar parte del URI del identificador de una aplicación. Un recurso en Azure Active Directory (Azure AD) puede incluir un nombre de dominio que ya se haya verificado para que pertenezca al directorio que contiene el recurso. Solo los administradores globales pueden realizar tareas de administración de dominio de Azure AD.

Los nombres de dominio en Azure AD son únicos globalmente. Un nombre de dominio puede usarse por una única instancia de Azure AD. Si un directorio de Azure AD ha comprobado un nombre de dominio, ningún otro directorio de Azure AD puede comprobar o usar ese mismo nombre de dominio.

## Nombres de dominio personalizados e iniciales

Cada nombre de dominio en Azure AD es un nombre de dominio inicial o un nombre de dominio personalizado.

Cada instancia de Azure AD incluye un nombre de dominio inicial en el formulario de contoso.onmicrosoft.com. El tercer nivel del nombre de dominio, en este ejemplo "contoso.com" se estableció cuando se creó el directorio, normalmente por el administrador que creó el directorio. El nombre de dominio inicial es un directorio que no se puede modificar ni eliminar. El nombre de dominio inicial, aunque es totalmente funcional, está diseñado principalmente para utilizarse como mecanismo de arranque.

En la mayoría de los entornos de producción, un directorio tiene al menos un dominio personalizado comprobado, como "contoso.com", y es ese dominio personalizado el que es visible para los usuarios finales. Un nombre de dominio personalizado es un recurso que agrega un administrador global de un directorio. Un nombre de dominio personalizado es un dominio que pertenece a la organización, por ejemplo, "contoso.com". La mayoría de las organizaciones ya poseen un nombre de dominio que utilizan para hospedar su sitio web. Este nombre de dominio es familiar a los empleados, ya que forma parte del nombre de usuario que usan para iniciar sesión en la red corporativa, o para enviar y recuperar el correo electrónico. Antes de que lo pueda utilizar un directorio, se debe comprobar el nombre de dominio personalizado.

## Nombres de dominio comprobados y no comprobados

El nombre de dominio inicial para un directorio se evalúa implícitamente como comprobado por Azure AD. Cuando un administrador agrega un nombre de dominio personalizado a una instancia de Azure AD, inicialmente se encuentra en un estado no comprobado. Azure AD no permitirá a los recursos de directorio usar un dominio no comprobado. De esta forma se garantiza que solo un directorio puede utilizar un nombre de dominio personalizado y que la organización que posee la instancia de Azure AD es la organización que posee realmente el nombre de dominio.

Azure AD comprueba la propiedad de un nombre de dominio mediante la búsqueda de entradas determinadas en los registros del Servicio de nombres de dominio (DNS) para el dominio. Para comprobar la propiedad de un dominio, el administrador obtiene las entradas de DNS de Azure AD que buscará Azure AD y agrega esas entradas a los registros de DNS para el nombre de dominio que mantiene el registrador de nombres de dominio para ese dominio. Se muestran los pasos para comprobar un dominio en el artículo para [agregar un dominio personalizado a su directorio de Azure AD](active-directory-add-domain.md).

La adición de entradas de DNS a Azure AD para comprobar la propiedad del dominio no afecta a otros servicios de dominio, como correo electrónico u hospedaje web.

## Nombres de dominio federados y administrados

Puede configurarse un nombre de dominio personalizado en Azure AD para proporcionar a los usuarios una experiencia de inicio de sesión federada entre su instancia de Active Directory en local y Azure AD. Configurar un dominio para la federación requiere actualizaciones para recursos con privilegios en Azure AD y también para su instancia de Active Directory local. La configuración de un dominio federado debe realizarse desde Azure AD Connect o con PowerShell. No se puede iniciar la federación de un dominio personalizado desde el Portal de Azure clásico. [Vea este vídeo para más información acerca de la configuración de AD FS para el inicio de sesión de usuario con Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Los dominios que no están federados a veces se denominan dominios administrados. El dominio inicial para un directorio de Azure AD se evalúa implícitamente como dominio administrado.

## Nombres de dominio principales

El nombre de dominio principal de un directorio es el nombre de dominio que se preselecciona como valor predeterminado para la parte "dominio" del nombre de usuario, cuando un administrador crea un nuevo usuario en el [Portal de Azure clásico](https://manage.windowsazure.com/) u otro portal como el portal de administración de Office 365. Un directorio de Azure AD puede tener solo un nombre de dominio principal. El administrador puede cambiar el nombre de dominio principal para que sea cualquier dominio personalizado comprobado que no esté federado o el dominio principal.

## Nombres de dominio en Azure AD y otros servicios Microsoft Online Services

El nombre de dominio debe comprobarse en Azure AD antes de poder ser utilizado por otros servicios Microsoft Online Services, como Exchange Online, SharePoint Online e Intune. Estos otros servicios por lo general requieren que un administrador agregue una o más entradas de DNS que son específicas del servicio.

Una aplicación web de Azure usa su propio mecanismo para comprobar la propiedad de un dominio. El dominio debe comprobarse para su uso con Azure AD, incluso si se ha comprobado anteriormente para su uso por una aplicación web de Azure en una suscripción que depende de dicha instancia de Azure AD. La aplicación web de Azure puede utilizar un nombre de dominio que se ha comprobado en otro directorio de Azure AD diferente al directorio que protege la aplicación web.

## Administración de nombres de dominio

Las tareas de administración de dominio se pueden completar desde el Portal de Azure clásico y desde PowerShell. Muchas tareas se pueden realizar mediante la API Graph de Azure AD (en versión preliminar pública).

-   [Utilización de nombres de dominio personalizados para simplificar la experiencia de inicio de sesión de los usuarios](active-directory-add-domain.md)

-   [Managing custom domain names in your Azure Active Directory](active-directory-add-manage-domain-names.md) (Administración de nombres de dominio personalizados en Azure Active Directory)

-   [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Operations on domains (preview) | Graph API reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) (Operaciones sobre dominios (versión preliminar) | Referencia de la API Graph)

<!---HONumber=AcomDC_0420_2016-->