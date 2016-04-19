<properties
	pageTitle="Adición de usuarios o cambio de la información del usuario en Azure Active Directory | Microsoft Azure"
	description="Explica cómo agregar usuarios o cambiar la información del usuario en Azure Active Directory, incluidos los usuarios externos e invitados."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/31/2016"
	ms.author="curtand;viviali"/>

# Adición o cambio de usuarios en Azure Active Directory

Agregue una cuenta al directorio de su inquilino para cada usuario que vaya a acceder a un servicio en la nube de Microsoft. De forma predeterminada, los usuarios agregados no tienen permisos de administrador, pero puede asignárselos en cualquier momento.

## Adición de un usuario

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) con una cuenta que sea administrador global para el directorio.
2. Haga clic en **Active Directory** y seleccione el nombre del directorio de su organización.
3. Seleccione la pestaña **Usuarios** y, después, en la barra de comandos, seleccione **Agregar usuario**.
4. En la página **Proporcione información sobre este usuario**, elija una de estas opciones en **Tipo de usuario**:

	- **Nuevo usuario de la organización**: agrega una nueva cuenta de usuario al directorio.
	- **Usuario con una cuenta de Microsoft existente**: agrega una cuenta de consumidor de Microsoft existente a su directorio (por ejemplo, una cuenta de Outlook).
	- **Usuario de otro directorio de Azure AD**: agrega a su directorio una cuenta de usuario cuyo origen es otro directorio de Azure AD. Puede seleccionar un usuario de otro directorio solo si también es un miembro de ese directorio.
	- **Usuarios en compañías asociadas**: para invitar y autorizar a usuarios de empresas asociadas a su directorio ([consulte colaboración de Azure Active Directory B2B](active-directory-b2b-what-is-azure-ad-b2b.md))


5. En función del valor de **Tipo de usuario**, escriba un nombre de usuario o una dirección de correo electrónico, o bien cargue un archivo CSV en el que se especifiquen las direcciones de correo electrónico.
6. En la página **Perfiles** del usuario, especifique el nombre, los apellidos y un nombre descriptivo, así como un rol de usuario en la lista **Roles**. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md). Especifique si se debe **habilitar la autenticación multifactor**.
7. En la página **Obtener contraseña temporal**, seleccione **Crear**.

> [AZURE.IMPORTANT] Si su organización usa más de un dominio, debe tener en cuenta los siguientes problemas al agregar una cuenta de usuario:
>
> - Para agregar cuentas de usuario con el mismo nombre principal de usuario (UPN) en todos los dominios, agregue **primero** geoffgrisso@contoso.onmicrosoft.com, por ejemplo, **seguido de** geoffgrisso@contoso.com.
> - **No** agregue geoffgrisso@contoso.com antes de geoffgrisso@contoso.onmicrosoft.com. Este orden es importante y puede ser complicado de deshacer.

## Cambiar la información de usuario

Puede cambiar los atributos de usuario, excepto el id. objeto.

1. Abra el directorio.
2. Seleccione la pestaña **Usuarios** y, después, el nombre para mostrar del usuario que quiere cambiar.
3. Termine los cambios y, a continuación, haga clic en **Guardar**.

Si el usuario que está intentando cambiar está sincronizado con el servicio de Active Directory local, no puede cambiar la información del usuario mediante este procedimiento. Para cambiar el usuario, utilice las herramientas de administración locales de Active Directory.

## Restablecimiento de la contraseña del usuario

1. Abra el directorio.
2. Seleccione la pestaña **Usuarios** y, después, el nombre para mostrar del usuario que quiere cambiar.
3. En la barra de comandos, seleccione **Restablecer contraseña**.
4. En el cuadro de diálogo Restablecer contraseña, haga clic en **Restablecer**.
5. Seleccione la marca de verificación para finalizar el restablecimiento de la contraseña.

## Adición de usuarios externos

También puede agregar usuarios de otro directorio de Azure AD al que pertenezca o desde las empresas asociadas mediante la carga de un archivo CSV. Para agregar un usuario externo, en **Tipo de usuario**, especifique **Usuario de otro directorio de Microsoft Azure AD** o **Usuarios en compañías asociadas**.

Los usuarios de ambos tipos proceden de otro directorio y se agregan como **usuarios externos**. Los usuarios externos pueden colaborar con otros usuarios en un directorio sin ningún requisito para agregar nuevas cuentas y credenciales. Los usuarios externos se autentican mediante su directorio principal cuando inician sesión, y esa autenticación funciona para todos los demás directorios a los que se han agregado.

## Limitaciones y administración de usuarios externos

Cuando se agrega un usuario desde otro directorio al suyo, ese usuario es un usuario externo en el directorio. El nombre para mostrar y el nombre de usuario se copian desde su directorio principal y se usan para el usuario externo en el directorio. Desde ese momento, las propiedades de la cuenta de usuario externo son completamente independientes. Si se realizan cambios en las propiedades del usuario en su directorio particular, esos cambios no se propagan a la cuenta de usuario externa de su directorio.

La única vinculación entre las dos cuentas es que el usuario siempre se autentica en el directorio particular o con su cuenta de Microsoft. Ese es el motivo de que no vea una opción para restablecer la contraseña o habilitar la autenticación multifactor para un usuario externo. Actualmente, la directiva de autenticación del directorio particular o la cuenta de Microsoft es la única que se evalúa cuando el usuario inicia sesión.

> [AZURE.NOTE]
Si lo desea, puede deshabilitar el usuario externo en el directorio, lo que bloqueará el acceso al directorio.

Si se elimina un usuario en su directorio principal o se cancela su cuenta de Microsoft, el usuario externo sigue existiendo en el directorio. Sin embargo, el usuario del directorio no puede tener acceso a los recursos porque no se puede autenticar con un directorio particular o una cuenta de Microsoft.

### Servicios que actualmente permiten el acceso a los usuarios externos de Azure AD

- **Portal de Azure clásico**: permite que un usuario externo que sea administrador de varios directorios administre cada uno de dichos directorios.
- **SharePoint Online**: si el uso compartido externo está habilitado, permite que un usuario externo acceda a los recursos autorizados de SharePoint Online.
- **Dynamics CRM**: si el usuario tiene licencia mediante PowerShell, permite que un usuario externo acceda a los recursos autorizados en Dynamics CRM.
- **Dynamics AX**: si el usuario tiene licencia mediante PowerShell, permite que un usuario externo acceda a los recursos autorizados en Dynamics AX. Las limitaciones de los [usuarios externos de Azure AD](#known-limitations-of-azure-ad-external-users) y los [usuarios invitados](#guest-user-management-and-limitations) se aplican también a los usuarios externos en Dynamics AX.

### Limitaciones conocidas de los usuarios externos de Azure AD

- Los usuarios externos que son administradores no pueden agregar usuarios de empresas asociadas a directorios (colaboración B2B) fuera de su directorio principal.
- Los usuarios externos no pueden dar su consentimiento a aplicaciones multiinquilino en directorios fuera de su directorio principal.
- PowerBI no admite actualmente el acceso a usuarios externos
- El Portal de Office no admite licencias de usuarios externos

## Limitaciones y administración de usuarios invitados

Las cuentas de invitado son usuarios de otros directorios que se invitaron a su directorio para que tuvieran acceso a documentos de SharePoint, aplicaciones u otros recursos de Azure. Una cuenta de invitado en su directorio tiene el atributo UserType establecido en "Guest" (Invitado). Los usuarios normales (específicamente, los miembros del directorio) tienen el valor de UserType "Miembro".

Los invitados tienen un conjunto limitado de derechos en el directorio. Estos derechos limitan la capacidad de los invitados para obtener información acerca de otros usuarios en el directorio. Sin embargo, los usuarios invitados todavía pueden interactuar con los usuarios y los grupos asociados a los recursos en los que trabajan. Los usuarios invitados pueden:

- Ver otros usuarios y grupos asociados a una suscripción de Azure a la que están asignados
- Ver los miembros de grupos a los que pertenecen
- Buscar otros usuarios en el directorio, si conocen su dirección de correo electrónico completa
- Ver solo un conjunto limitado de atributos de los usuarios que buscan: el nombre para mostrar, la dirección de correo electrónico, el nombre principal de usuario (UPN) y la foto en miniatura.
- Obtener una lista de los dominios verificados del directorio del inquilino
- Dar su consentimiento a aplicaciones, concediéndoles el mismo acceso que tienen los miembros en su directorio.

## Establecimiento de las directivas de acceso del usuario

La pestaña **Configurar** de un directorio incluye opciones para controlar el acceso de los usuarios externos. Un administrador global de directorios solo puede cambiar estas opciones en el Portal de Azure clásico. Actualmente no hay ningún método de PowerShell o de API.

Para abrir la pestaña **Configurar** del Portal de Azure clásico, seleccione **Active Directory** y luego el nombre del directorio.

![Configuración de Azure Active Directory][1]

A continuación, puede editar las opciones para controlar el acceso de usuarios externos.

![][2]


## Pasos siguientes

- [Administración de Azure AD](active-directory-administer.md)
- [Administración de contraseñas en Azure AD](active-directory-manage-passwords.md)
- [Administración de grupos en Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0413_2016-->