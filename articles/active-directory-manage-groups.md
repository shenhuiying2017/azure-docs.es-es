<properties 
	pageTitle="Administración de grupos en Azure AD" 
	description="Un tema que explica cómo administrar grupos en Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Administración de grupos en Azure AD

Un grupo es una colección de usuarios y grupos que se pueden administrar como una sola unidad. Los usuarios y grupos que pertenecen a un grupo determinado se conocen como miembros del grupo. Usar grupos puede simplificar la administración mediante la asignación de un conjunto común de permisos y derechos a varias cuentas a la vez, en lugar de asignar permisos y derechos a cada cuenta individual.

Ahora en Azure AD, solo puede crear grupos de seguridad.

Puede utilizar los grupos como una manera cómoda de asignar acceso a las aplicaciones para los usuarios en los casos en los que necesite asignar muchos usuarios a la misma aplicación. También puede utilizar grupos al configurar la administración de acceso de otros en línea que controlan el acceso a los recursos, por ejemplo, SharePoint Online.

Si ha configurado la sincronización de directorios, puede ver los grupos que se han sincronizado desde sus instalaciones locales Windows Server Active Directory, que tiene el valor 'Active Directory Local' en la propiedad 'Con origen en'. Debe continuar administrando estos grupos en su Active Directory local; estos grupos no se pueden administrar ni eliminar en el Portal de administración de Azure.

Si dispone de Office 365, puede ver los grupos de distribución y grupos de seguridad habilitados para correo electrónico que se crean y se administran en el Centro de administración de Exchange dentro de Office 365. Estos grupos tienen el valor 'Office 365' en la propiedad 'Con origen en' y deben continuar para que se administren en el Centro de administración de Exchange.

También puede crear grupos unificados a través del Panel de acceso. En la ficha Configurar, en Administración de grupo, establezca el widget **Los usuarios pueden crear grupos de O365** a **Sí**. Si tiene grupos unificados de Office 365 que se crearon en el Panel de acceso o en Office 365, estos grupos tendrán la propiedad 'Con origen en' establecida en 'Azure Active Directory' y se pueden administrar a través del Panel de acceso.

Si ha habilitado la administración de grupos de autoservicio para los usuarios (para obtener más información, vea Administración de grupos de autoservicio para usuarios en Azure AD, como administrador de inquilinos también puede administrar estos grupos mediante el Portal de administración de Azure. Puede agregar y quitar miembros del grupo, agregar y quitar propietarios del grupo, editar propiedades del grupo y ver informes de actividad de historial de los grupos que muestra la acción realizada dentro del grupo, quién realizó esa acción, y en qué momento.

> [AZURE.NOTE]Para poder asignar un grupo a una aplicación, debe utilizar Azure AD Premium. Si dispone de Azure AD Premium, también puede utilizar grupos para asignar el acceso a aplicaciones de SaaS que se integran con Azure AD. Para obtener más información, consulte Asignación de acceso de un grupo a la aplicación SaaS en Azure AD.

## Pasos siguientes

- [Administración de Azure AD](active-directory-administer.md)
- [Creación o edición de usuarios en Azure AD](active-directory-create-users.md)
- [Administración de contraseñas en Azure AD](active-directory-manage-passwords.md)

<!---HONumber=58-->