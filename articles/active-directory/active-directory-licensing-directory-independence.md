<properties
   pageTitle="Adición y administración de varios directorios de Azure AD | Microsoft Azure"
   description="Instrucciones y prácticas recomendados para agregar y administrar los directorios de Azure AD explicando los directorios como recursos de totalmente independientes"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="curtand"/>

# Adición y administración de varios directorios de Azure AD

En el directorio de Azure AD, cada directorio es un recurso totalmente independiente: un recurso del mismo nivel, con características completas y lógicamente independiente de otros directorios que administre. No hay ninguna relación de elementos primarios y secundarios entre los directorios. Esta independencia entre directorios incluye la independencia de recursos, la independencia administrativa y la independencia de sincronización.

##Independencia de recursos

Si crea o elimina un recurso en un directorio, esto no tiene efecto en ningún recurso de los demás directorios, con la excepción parcial de los usuarios externos, según se describe más adelante. Si utiliza un dominio personalizado “contoso.com” con un directorio, este no se puede utilizar con ningún otro directorio.

##Independencia administrativa

Si un usuario sin derechos administrativos de directorio "Contoso" crea un directorio de prueba "Prueba: de forma predeterminada, el usuario que crea un directorio se agrega como usuario externo en ese nuevo directorio y se asigna el rol de administrador global en ese directorio. Los administradores del directorio "Contoso" no tienen privilegios administrativos directos en el directorio "Prueba" a menos que un administrador de "Prueba" específicamente les otorgue esos privilegios. Los administradores de "Contoso" pueden controlar el acceso al directorio "Prueba" si controlan la cuenta de usuario que creó "Prueba". Si cambió (agregó o quitó) un rol de administrador para un usuario en un directorio, el cambio no afecta a ningún rol de administrador que el usuario pueda tener en otro directorio.

##Independencia de sincronización

Puede configurar cada directorio de Azure AD por separado para obtener datos sincronizados desde una única instancia: - La Herramienta de sincronización de directorios (DirSync) para sincronizar datos con un único bosque de AD. - El Conector Azure Active Directory para Forefront Identity Manager para sincronizar datos con uno o más bosques locales o fuentes de datos que no son de Azure AD.

##Adición de un directorio de Azure AD

Para agregar un directorio de Azure AD en el Portal de administración de Azure, seleccione la extensión de Active Directory de la izquierda y seleccione **Agregar**.

> [AZURE.NOTE]A diferencia de otros recursos de Azure, los directorios no son recursos secundarios de una suscripción a Azure. Si cancela su suscripción a Azure o deja que esta caduque, aún podrá tener acceso a los datos de su directorio mediante Azure PowerShell, la API Graph de Azure u otras interfaces, como el Centro de administración de Office 365. También puede asociar otra suscripción con el directorio.

Para obtener una amplia visión general de los problemas de licencias de Azure AD y prácticas recomendadas, consulte [¿Qué es la licencia de Azure Active Directory?](active-directory-licensing-what-is.md)

<!---HONumber=Oct15_HO3-->