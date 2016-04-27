<properties
	pageTitle="Utilización de nombres de dominio personalizados para simplificar el inicio de sesión mediante Azure Active Directory | Microsoft Azure"
	description="Cómo agregar nombres de dominio de la empresa a Azure Active Directory y comprobar el nombre de dominio."
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
	ms.topic="get-started-article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Utilización de nombres de dominio personalizados para simplificar el inicio de sesión mediante Azure Active Directory

Puede utilizar sus propios nombres de dominio personalizado para mejorar y simplificar el inicio de sesión y otras experiencias del usuario en Azure Active Directory (Azure AD). Por ejemplo, si la organización posee el nombre de dominio ‘contoso.com’, los usuarios pueden entonces iniciar sesión con nombres de usuario con los que están familiarizados, como ‘joe@contoso.com'. Debe tener acceso a una cuenta de usuario que sea un administrador global de su directorio de Azure AD.

Cuando obtiene por primera vez el directorio del inquilino en Azure Active Directory, los usuarios inician sesión con nombres de usuario como ‘alice@contoso.onmicrosoft.com’. En este ejemplo, contoso.onmicrosoft.com es un nombre de dominio inicial integrado que puede usar hasta que compruebe su nombre de dominio personalizado. Uno de los pasos siguientes sería agregar un nombre de dominio personalizado que tenga su organización como, por ejemplo, ‘contoso.com’. Esto le permite asignar nombres que resultan familiares a los usuarios, como ‘alice@contoso.com’.

Para más información sobre cómo se usan los nombres de dominio en Azure AD, consulte [Conceptual overview of custom domain names in Azure Active Directory](active-directory-add-domain-concepts.md) (Introducción conceptual de nombres de dominio personalizados en Azure Active Directory). La mayoría de los administradores realizan tareas de administración de nombres de dominio en Azure AD mediante el Portal de Azure clásico. Sin embargo, si lo prefiere, puede usar [PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o la [versión preliminar de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) para realizar las tareas de administración.

## Incorporación de nombres de dominio personalizados al directorio

Para incorporar un nombre de dominio personalizado a su directorio:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta de usuario que sea un administrador global de su directorio de Azure AD.

2. Seleccione **Active Directory** en la barra de navegación izquierda.

3. Abra el directorio.

4. Seleccione la pestaña **Dominios**.

5. En la barra de comandos, haga clic en **Agregar**.

6. Escriba el nombre del dominio personalizado como, por ejemplo, ‘contoso.com’. Asegúrese de incluir .com, .net u otra extensión de nivel superior.

7. Si piensa configurar este dominio para un [inicio de sesión federado](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) con su instancia de Active Directory local, seleccione la casilla.

8. Seleccione **Agregar**.

Antes de poder asignar nombres de usuario que incluyen el nombre de dominio personalizado, Azure AD debe comprobar que su organización es propietario del nombre de dominio. Para realizar esta comprobación, debe actualizar las entradas DNS en el registrador de nombres de dominio para el dominio.

## Obtención de las entradas DNS que utilizará Azure AD para comprobar nombres de dominio

Si ha seleccionado la opción para configurar el dominio para la federación cuando agregó el dominio, verá instrucciones para descargar la herramienta Azure AD Connect. Ejecute la herramienta Azure AD Connect para [obtener las entradas DNS que debe agregar en el registrador de nombres de dominio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si no seleccionó la opción de inicio de sesión federado con Windows Server AD, verá las entradas DNS en la segunda página del asistente para **agregar dominio**.

## Incorporación de las entradas DNS al archivo de zona DNS para el dominio

Para agregar las entradas DNS necesarias para Azure AD:

1.  Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene permisos suficientes para actualizar el archivo de zona DNS para el dominio, comparta las entradas DNS con la persona o el equipo de su organización que cuente con este acceso y solicítele que las actualice.

2.  Actualice el archivo de zona DNS para el dominio. Para ello, agregue las entradas DNS proporcionadas por Azure AD. Estas entradas DNS permitirán a Azure AD comprobar la propiedad del dominio. No cambiarán los comportamientos, como el enrutamiento de correo o el hospedaje web.

[Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Comprobación del dominio con Azure AD

Una vez agregadas las entradas DNS, puede comprobar el dominio con Azure AD.

Si ha seleccionado la opción para federar su nombre de dominio personalizado, la herramienta Azure AD Connect realizará automáticamente la comprobación. Ejecute la herramienta una vez completados los requisitos previos. De lo contrario, compruebe el dominio en el Portal de Azure clásico. Si sigue teniendo el asistente para **agregar dominio** abierto, puede hacer clic en el botón de comprobación en la tercera página del asistente. Los registros de DNS pueden tardar unos minutos en propagarse.

Si el asistente para **agregar dominio** no está aún abierto, puede comprobar el dominio en el [Portal de Azure clásico](https://manage.windowsazure.com/):

1.  Inicie sesión con una cuenta de usuario que sea un administrador global de su directorio de Azure AD.

2.  Seleccione **Active Directory** en la barra de navegación izquierda.

3.  Abra el directorio.

4.  Seleccione la pestaña **Dominios**.

5.  En la lista de dominios, seleccione el dominio que desee comprobar.

6.  En la barra de comandos, haga clic en **Comprobar**.

7.  Seleccione **Comprobar** en el cuadro de diálogo.

Ya puede asignar nombres de usuario que incluyan su nombre de dominio personalizado.

## Incorporación de más nombres de dominio personalizados

Si su organización usa más de un nombre de dominio personalizado, como ‘contoso.com’ y ‘contosobank.com’, puede agregar cada uno de ellos a su directorio de Azure AD, hasta un máximo de 900 dominios. Utilice los pasos enumerados anteriormente para agregar los nombres de dominio siguientes.

Pasos siguientes

-   [Asignación de usuarios a un dominio personalizado](active-directory-add-domain-add-users.md)

-   [Managing custom domain names in your Azure Active Directory](active-directory-add-manage-domain-names.md) (Administración de nombres de dominio personalizados en Azure Active Directory)

-   [Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)

-   [Conceptual overview of custom domain names in Azure Active Directory](active-directory-add-domain-concepts.md) (Introducción conceptual de nombres de dominio personalizados en Azure Active Directory).

<!---HONumber=AcomDC_0420_2016-->