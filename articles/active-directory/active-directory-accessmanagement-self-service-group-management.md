<properties
	pageTitle="Configuración de Azure Active Directory para la administración del acceso a la aplicación de autoservicio | Microsoft Azure"
	description="La administración de grupos de autoservicio permite a los usuarios crear y administrar grupos de seguridad o grupos de Office 365 en Azure Active Directory y les ofrece la posibilidad de solicitar la pertenencia a dichos grupos."
	services="active-directory"
	documentationCenter=""
  authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/14/2016"
	ms.author="curtand"/>

# Configuración de Azure Active Directory para la administración de grupos de autoservicio

La administración de grupos de autoservicio permite a los usuarios crear y administrar grupos de seguridad o grupos de Office 365 en Azure Active Directory (Azure AD). Los usuarios también pueden solicitar la pertenencia a grupos de seguridad o a grupos de Office 365; a continuación, el propietario del grupo puede aprobarla o rechazarla. De esta forma, el control diario de la pertenencia a grupos se puede delegar a personas que conocen el contexto empresarial de dicha pertenencia. Las características de administración de grupos de autoservicio solo están disponibles para grupos de seguridad y de Office 365, no para grupos de seguridad universal habilitados para correo electrónico ni listas de distribución.

La administración de grupos de autoservicio actualmente está formada por dos escenarios esenciales: la administración de grupos delegados y la administración de grupos de autoservicio.

- **Administración de grupos delegados** Por ejemplo, un administrador que administra el acceso a una aplicación SaaS que su compañía usa. La administración de estos derechos de acceso se está volviendo compleja, por lo que este administrador solicita al propietario de la empresa la creación de un nuevo grupo. El administrador asigna acceso a la aplicación al grupo nuevo y agrega a dicho grupo todas las personas que ya acceden a la aplicación. Luego, el propietario de la empresa puede agregar más usuarios, y dichos usuarios se aprovisionan automáticamente en la aplicación. No es preciso que el propietario espere a que el administrador administre el acceso de los usuarios. Si el administrador concede el mismo permiso a un administrador de otro grupo de negocios, dicha persona también puede administrar el acceso de sus propios usuarios. Ni el propietario de una empresa ni el administrador pueden ver o administrar los usuarios del otro. El administrador podrá seguir viendo todos los usuarios que tienen acceso a la aplicación y bloquear los derechos de acceso si fuera necesario.

- **Administración de grupos de autoservicio** Por ejemplo, dos usuarios tienen sitios de SharePoint Online configurados de forma independiente. Ellos desean que los equipos del otro puedan acceder a sus sitios. Para ello, pueden crear un grupo en Azure AD, y en SharePoint Online cada uno de ellos selecciona dicho grupo para proporcionar acceso a sus sitios. Cuando alguien desea tener acceso, lo solicita en el Panel de acceso, y tras la aprobación obtiene acceso a ambos sitios de SharePoint Online automáticamente. Posteriormente, uno de ellos decide que todas las personas que accedan a su sitio también deben obtener acceso a una aplicación SaaS concreta. El administrador de la aplicación SaaS puede agregar derechos de acceso a la aplicación en el sitio de SharePoint Online. Desde ese momento, todas las solicitudes aprobadas darán acceso tanto a los dos sitios de SharePoint Online como a la aplicación SaaS.

## Puesta a disposición de un grupo para el autoservicio del usuario final

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), abra el directorio de Azure AD.

2. En la pestaña **Configurar**, en **Administración de grupos delegados** seleccione Habilitado.

3. En **Los usuarios pueden crear grupos de seguridad** o **Los usuarios pueden crear grupos de Office** seleccione Habilitado.

Si la opción **Los usuarios pueden crear grupos de seguridad** está habilitada, todos los usuarios del directorio pueden crear nuevos grupos de seguridad y agregar miembros a dichos grupos. Estos grupos nuevos también se muestran en el Panel de acceso para los restantes usuarios. Si la configuración de la directiva en el grupo lo permite, otros usuarios pueden crear solicitudes para unirse a dichos grupos. Si la opción **Los usuarios pueden crear grupos de seguridad** está deshabilitada, los usuarios no podrán crear grupos ni podrán cambiar los grupos existentes de los que sean propietarios. Sin embargo, pueden administrar la pertenencia a dichos grupos y aprobar las solicitudes de otros usuarios para unirse a ellos.

También puede utilizar la opción **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** para conseguir un control de acceso más específico sobre la administración de grupos de autoservicio para los usuarios. Cuando la opción **Los usuarios pueden crear grupos** esté habilitada, todos los usuarios de su directorio podrán crear nuevos grupos y agregar miembros a ellos. Además, si en **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** se selecciona Algunos, se restringe la administración de los grupos a solo un grupo limitado de usuarios. Si este modificador se establece en Algunos, debe agregar usuarios al grupo SSGMSecurityGroupsUsers antes de poder crear grupos nuevos y agregar miembros a dichos grupos. Si en **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** se selecciona Todos, todos los usuarios del directorio podrán crear nuevos grupos.

También puede utilizar el cuadro **Grupos que pueden utilizar el autoservicio para grupos de seguridad** para especificar un nombre personalizado para un grupo cuyos miembros puedan utilizar el autoservicio.

## Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0615_2016-->