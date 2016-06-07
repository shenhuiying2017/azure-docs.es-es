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
	ms.date="05/26/2016"
	ms.author="curtand"/>

# Configuración de Azure Active Directory para la administración de grupos de autoservicio

La administración de grupos de autoservicio permite a los usuarios crear y administrar grupos de seguridad o grupos de Office 365 en Azure Active Directory (Azure AD) y les ofrece la posibilidad de solicitar la pertenencia a dichos grupos, que posteriormente el propietario del grupo puede aprobar o rechazar. Mediante el uso de características de administración de grupos de autoservicio, el control cotidiano de la pertenencia a grupos puede delegarse a personas que comprenden el contexto empresarial de dicha pertenencia. Las características de administración de grupos de autoservicio solo están disponibles para grupos de seguridad y de Office 365, no para grupos de seguridad habilitados para correo ni listas de distribución.

La administración de grupos de autoservicio actualmente está formada por dos escenarios esenciales: la administración de grupos delegados y la administración de grupos de autoservicio.

- **Administración de grupos delegada**: por ejemplo, un administrador que administra el acceso a una aplicación SaaS que su compañía usa. La administración de estos derechos de acceso se está volviendo compleja, por lo que este administrador solicita al propietario de la empresa la creación de un nuevo grupo. Ahora, el administrador asigna el acceso para la aplicación a un nuevo grupo que acaba de crear el propietario de la empresa, y coloca a todas las personas que actualmente tienen acceso a la aplicación en este grupo. El propietario de la empresa, a continuación, puede agregar más usuarios, y esos usuarios son aprovisionados automáticamente a la aplicación unos momentos más tarde. El propietario de la empresa no necesita esperar a que el administrador realice el trabajo, sino que él mismo puede administrar el acceso para sus usuarios. El administrador puede hacer lo mismo para un asistente administrativo para un grupo empresarial diferente, y tanto el propietario de la empresa como este asistente administrativo podrán administrar ahora el acceso para sus usuarios, sin poder tocar ni visualizar los usuarios del otro. El administrador podrá seguir viendo todos los usuarios que tienen acceso a la aplicación y bloquear los derechos de acceso si fuera necesario.

- **Administración de grupos de autoservicio**: por ejemplo, dos usuarios tienen sitios de SharePoint Online configurados de forma independiente, pero quieren que los equipos del otro puedan tener acceso a sus sitios. Para ello, pueden crear un grupo en Azure AD, y en SharePoint Online cada uno de ellos selecciona que ese mismo grupo proporcione acceso a sus sitios. Cuando alguien desea tener acceso, lo solicita en el Panel de acceso, y tras la aprobación obtiene acceso a ambos sitios de SharePoint Online automáticamente. Posteriormente, uno de ellos decide que todas las personas que tengan acceso a su sitio también deben obtener acceso a una aplicación SaaS en particular. Este solicita al administrador de su aplicación SaaS que agregue derechos de acceso para esta aplicación a su sitio. Desde ese momento, las solicitudes que apruebe darán acceso a los dos sitios de SharePoint Online y también a esta aplicación SaaS.

## Puesta a disposición de un grupo para el autoservicio del usuario final

En el [Portal de Azure clásico](https://manage.windowsazure.com), en la pestaña **Configurar**, establezca **Administración de grupos delegados** en Habilitado y luego establezca **Los usuarios pueden crear grupos de seguridad** o **Los usuarios pueden crear grupos de Office** en Habilitado.

Cuando la opción **Los usuarios pueden crear grupos de seguridad** está habilitada, todos los usuarios del directorio pueden crear nuevos grupos de seguridad y agregar miembros a estos grupos. Estos grupos nuevos también aparecerán en el Panel de acceso para todos los demás usuarios, y otros usuarios pueden crear solicitudes para unirse a ellos si la configuración de directiva del grupo lo permite. Si la opción **Los usuarios pueden crear grupos de seguridad** está deshabilitada, los usuarios no podrán crear grupos ni cambiar los grupos existentes de los que sean propietarios, pero podrán seguir administrando la pertenencia a esos grupos y aprobar las solicitudes de otros usuarios para unirse a ellos.

También puede utilizar la opción **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** para conseguir un control de acceso más detallado sobre las funciones de administración de grupos de autoservicio para los usuarios. Cuando la opción **Los usuarios pueden crear grupos** está habilitada, todos los usuarios de su directorio pueden crear nuevos grupos y agregar miembros a ellos. Además, al establecer la opción **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** en Algunos, está restringiendo la administración de grupos a solo un grupo limitado de usuarios. Cuando este modificador se establece en Algunos, se crea un grupo denominado SSGMSecurityGroupsUsers en el directorio, y los usuarios a los que haya hecho miembros de este grupo serán los únicos que puedan crear nuevos grupos de seguridad y agregar miembros a estos grupos dentro del directorio. Al establecer la opción **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** en Todos, permite a todos los usuarios del directorio crear nuevos grupos.

También puede utilizar el cuadro **Grupos que pueden usar el autoservicio para grupos de seguridad** (establecido de forma predeterminada en "SSGMSecurityGroupsUsers") para especificar su propio nombre personalizado para un grupo que contendrá todos los usuarios que tienen la posibilidad de usar el autoservicio y crear nuevos grupos en el directorio.

## Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0601_2016-->