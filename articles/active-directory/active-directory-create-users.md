<properties
	pageTitle="Creación o edición de usuarios en Azure Active Directory | Microsoft Azure"
	description="Un tema que explica cómo crear o editar cuentas de usuario en Azure Active Directory."
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
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="curtand;viviali"/>

# Creación o edición de usuarios en Azure AD

Hay que crear una cuenta para cada usuario que vaya a tener acceso a un servicio de nube de Microsoft. También puede cambiar las cuentas de usuario o eliminarlas cuando ya no sean necesarias. De forma predeterminada, los usuarios no tienen permisos de administrador, pero puede asignárselos si lo desea.

## Creación de un usuario

1. Haga clic en **Active Directory** y seleccione el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en **Agregar usuario**.
3. En la página **Proporcione información sobre este usuario**, elija una de estas opciones en **Tipo de usuario**:

	- **Nuevo usuario de la organización**: para crear una nueva cuenta de usuario en el directorio
	- **Usuario con una cuenta Microsoft existente**: para agregar una cuenta de consumidor de Microsoft existente a su directorio (por ejemplo, una cuenta de Outlook)
	- **Usuario en otro directorio de Azure AD**: para agregar una cuenta de usuario al directorio cuyo origen es otro directorio de Azure AD (Nota: debe ser un miembro del otro directorio para seleccionar un usuario en él)
	- **Usuarios en compañías asociadas**: para invitar y autorizar a usuarios de empresas asociadas a su directorio ([consulte colaboración de Azure Active Directory B2B](active-directory-b2b-what-is-azure-ad-b2b.md))


4. Según la opción seleccionada, escriba un nombre de usuario o una dirección de correo electrónico, o bien cargue un archivo CSV para usuarios asociados.
5. En la página **Perfiles** del usuario, especifique el nombre, los apellidos y un nombre descriptivo. Elija también un rol en el menú desplegable Roles. Para obtener más información acerca de los roles del usuario y el administrador, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md). Especifique si se debe **habilitar la autenticación multifactor**.
6. En la página **Obtener contraseña temporal**, haga clic en **Crear**.

Si su organización usa más de un dominio, debe tener en cuenta los siguientes problemas al crear una cuenta de usuario:

- Puede crear cuentas de usuario con el mismo nombre principal de usuario (UPN) en los dominios si crea en primer lugar, por ejemplo, geoffgrisso@contoso.onmicrosoft.com seguido de geoffgrisso@contoso.com.
- No se puede crear geoffgrisso@contoso.com seguido de geoffgrisso@contoso.onmicrosoft.com.

## Edición de un usuario

Para editar un usuario en el Portal de Azure clásico:

1. Haga clic en **Active Directory** y después haga clic en el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en el nombre para mostrar del usuario que desee editar.
3. Termine los cambios y, a continuación, haga clic en **Guardar**.

Si el usuario que está intentando editar está sincronizado con el servicio de Active Directory local, aparece un mensaje de error y no será posible editar el usuario mediante este procedimiento. Para editar el usuario, utilice las herramientas de administración locales de Active Directory.

## Restablecimiento de la contraseña del usuario

1. Haga clic en **Active Directory** y después haga clic en el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en el nombre para mostrar del usuario que desee editar.
3. En la parte inferior del portal, haga clic en **Restablecer contraseña**.
4. En el cuadro de diálogo Restablecer contraseña, haga clic en **Restablecer**.
5. Haga clic en la marca de verificación para confirmar que se ha restablecido la contraseña.

## Creación de usuarios externos

En Azure AD, también puede agregar usuarios a un directorio de Azure AD con una cuenta de Microsoft desde otro directorio de Azure AD al que pertenecen o desde empresas asociadas; para ello, se carga un archivo CSV. Para crear un usuario externo, cree un usuario en el portal y, en **Tipo de usuario**, seleccione **Usuario en otro directorio de Azure AD** o **Usuarios en compañías asociadas**.

Los usuarios creados de las dos formas proceden de otro directorio y se crean como **usuarios externos**. Los usuarios externos pueden colaborar con los usuarios que ya existen en un directorio con su única cuenta sin necesidad de crear credenciales y cuentas nuevas. Los usuarios externos se autentican mediante su directorio principal cuando inician sesión, y esta autenticación funciona para todos los demás directorios de los que sean miembros.

## Limitaciones y administración de usuarios externos

Cuando se agrega un usuario desde otro directorio al suyo, ese usuario es un usuario externo en el directorio. Inicialmente, se copian el nombre para mostrar y el nombre de usuario desde el directorio principal del usuario y se pegan en el usuario externo en su directorio. A partir de ese momento, esas y otras propiedades del objeto de usuario externo son totalmente independientes; es decir, si se realiza cambios en el usuario en el directorio principal (por ejemplo, cambiar el nombre de usuario, agregar un puesto de trabajo, etc.), esos cambios no se propagan al objeto de usuario externo en su directorio.

La única vinculación entre los dos objetos es que el usuario siempre se autentica en el directorio principal o con su cuenta de Microsoft. Por eso, no se muestra una opción para restablecer la contraseña ni habilitar la autenticación multifactor para una cuenta de usuario externo actualmente, la directiva de autenticación del directorio principal o la cuenta de Microsoft es la única que se evalúa cuando el usuario inicia sesión.

> [AZURE.NOTE]
Si lo desea, puede deshabilitar el usuario externo en el directorio y esta acción bloqueará el acceso al directorio.

Si se elimina un usuario en su directorio principal o se cancela su cuenta de Microsoft, el usuario externo sigue existiendo en el directorio. Sin embargo, el usuario no tiene acceso a los recursos de su directorio porque ya no puede autenticarse en su directorio principal ni en la cuenta de Microsoft.

Estos son los servicios que actualmente permiten el acceso a los usuarios externos de Azure AD:

- Portal de Azure clásico: un usuario que sea administrador de varios directorios puede administrar cada uno de dichos directorios
- SharePoint Online: permite a un usuario externo acceder a los recursos de SharePoint Online autorizados si el uso compartido externo está habilitado
- Dynamics CRM: permite a un usuario externo acceder a los recursos autorizados en Dynamics CRM si el usuario tiene licencia mediante PowerShell

Estas son las limitaciones conocidas de los usuarios externos de Azure AD:

- los usuarios externos que son administradores no pueden agregar usuarios de empresas asociadas a directorios (B2B) fuera de su directorio principal
- los usuarios externos no pueden dar su consentimiento a las aplicaciones multiinquilino de directorios fuera de su directorio principal
- Actualmente, Visual Studio Online no permite el acceso a usuarios externos
- Actualmente, PowerBI no permite el acceso a usuarios externos
- Portal de Office no admite licencias de usuarios externos

## Invitados

Un **invitado** es un usuario en el directorio que tiene el atributo UserType establecido como "Guest" (Invitado). Los usuarios normales tienen un UserType "Member" (Miembro) para indicar que son miembros del directorio. Los invitados representan a los usuarios de otros directorios a los que se ha invitado a su directorio para que accedan a un recurso específico, como un documento de SharePoint, una aplicación o un recurso de Azure.

Los invitados tienen un conjunto limitado de derechos en el directorio. Estos derechos limitan la posibilidad de que los invitados descubran información sobre otros usuarios del directorio, aunque pueden interactuar con los usuarios y grupos asociados a los recursos en los que están trabajando. Los invitados tienen las siguientes funcionalidades:

- ver otros usuarios y grupos asociados con una suscripción de Azure a la que están asignados
- ver los miembros de los grupos a los que pertenecen
- buscar otros usuarios en el directorio, siempre que conozcan su dirección de correo electrónico completa
- ver solo un conjunto limitado de atributos de los usuarios que buscan; limitado al nombre para mostrar, la dirección de correo electrónico, el nombre principal de usuario (UPN) y la foto en miniatura
- obtener una lista de los dominios verificados del inquilino
- dar su consentimiento a aplicaciones, para los que les concede el mismo acceso que tienen en su directorio

## Configuración de las directivas de acceso del usuario

La pestaña **Configurar** de un directorio incluye opciones para controlar el acceso de los usuarios externos. Estas opciones solo se pueden cambiar en la interfaz de usuario (no hay ningún método de Windows PowerShell o API) en el Portal de Azure clásico y solo puede hacerlo un administrador global de directorio. Para abrir la pestaña **Configurar** del Portal de Azure clásico, haga clic en **Active Directory** y luego haga clic en el nombre del directorio.

![][1]

A continuación, puede editar las opciones para controlar el acceso de usuarios externos.

![][2]


## Pasos siguientes

- [Administración de Azure AD](active-directory-administer.md)
- [Administración de contraseñas en Azure AD](active-directory-manage-passwords.md)
- [Administración de grupos en Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0211_2016-->