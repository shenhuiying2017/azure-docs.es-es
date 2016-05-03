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
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# Utilización de nombres de dominio personalizados para simplificar el inicio de sesión mediante Azure Active Directory

Cuando obtiene por primera vez su directorio en Azure Active Directory (Azure AD), una de las primeras tareas importantes que tiene que realizar es verificar un nombre de dominio personalizado que su organización utiliza, como 'contoso.com'. Realizar esta tarea le permite asignar nombres de usuario que resultan familiares a los usuarios, como ‘alice@contoso.com’. Hasta que verifique su nombre de dominio personalizado, los usuarios tienen que iniciar sesión con nombres de usuario como 'alice@contoso.onmicrosoft.com' que usan el nombre de dominio inicial para el directorio.

## Incorporación de nombres de dominio personalizados al directorio

Para incorporar un nombre de dominio personalizado a su directorio:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta de usuario que sea administrador global de su directorio de Azure AD.

2. Seleccione **Active Directory** en la barra de navegación de la izquierda y abra su directorio.

4. Seleccione la pestaña **Dominios**.

5. En la barra de comandos, haga clic en **Agregar**.

6. Escriba el nombre del dominio personalizado como, por ejemplo, ‘contoso.com’. Asegúrese de incluir .com, .net u otra extensión de nivel superior.

7. Si piensa configurar este dominio para un [inicio de sesión federado](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) con su instancia de Active Directory local, seleccione la casilla.

8. Seleccione **Agregar**.

Antes de poder asignar nombres de usuario que incluyen el nombre de dominio personalizado, Azure AD debe comprobar que su organización es propietario del nombre de dominio. Para realizar esta verificación, tiene que agregar una entrada DNS en el archivo de zona DNS para el nombre de dominio. Esta tarea se puede completar en el sitio web del registrador de nombres de dominio para el nombre de dominio.

## Obtención de las entradas DNS para el nombre de dominio

Si decidió configurar el dominio para la federación, se le dirigirá a descargar la herramienta Azure AD Connect. Ejecute la herramienta Azure AD Connect para [obtener las entradas DNS que tiene que agregar en el registrador de nombres de dominio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si no seleccionó la opción de configurar el dominio para la federación, las entradas DNS están en la segunda página del asistente para **agregar dominio**.

## Incorporación de la entrada DNS en el archivo de zona DNS

Para agregar la entrada DNS necesaria para Azure AD:

1.  Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene permisos suficientes para actualizar la entrada DNS, pida a la persona o el equipo que tenga acceso que agregue la entrada DNS.

2.  Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. Esta entrada DNS permite a Azure AD comprobar la propiedad del dominio. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web.

[Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Comprobación del nombre de dominio con Azure AD

Una vez agregada la entrada DNS, puede comprobar el nombre de dominio con Azure AD.

Si optó por federar su nombre de dominio personalizado, la herramienta Azure AD Connect realiza la comprobación automáticamente. Ejecute la herramienta una vez completados los requisitos previos. De lo contrario, compruebe el dominio en el Portal de Azure clásico. Si sigue teniendo el asistente para **agregar dominio** abierto, puede hacer clic en el botón **Comprobar** en la tercera página del asistente. Puede tardar hasta una hora para que los registros DNS se propaguen.

Si el asistente para **agregar dominio** no está aún abierto, puede comprobar el dominio en el [Portal de Azure clásico](https://manage.windowsazure.com/):

1.  Inicie sesión con una cuenta de usuario que sea un administrador global de su directorio de Azure AD.

2.  Seleccione **Active Directory** en la barra de navegación de la izquierda.

3.  Abra el directorio.

4.  Seleccione la pestaña **Dominios**.

5.  En la lista de dominios, seleccione el dominio que desee comprobar.

6.  En la barra de comandos, haga clic en **Comprobar**.

7.  Seleccione **Comprobar** en el cuadro de diálogo.

Ya puede [asignar nombres de usuario a un dominio personalizado](active-directory-add-domain-add-users.md).

## Incorporación de más nombres de dominio personalizados

Si su organización usa varios nombres de dominio personalizados, como ‘contoso.com’ y ‘contosobank.com’, puede agregarlos a su directorio de Azure AD. El máximo es de 900 nombres de dominio. Para agregar los demás nombres de dominio utilice los mismos pasos.

## Solución de problemas
Si no puede verificar un nombre de dominio personalizado, hay algunas posibles causas. Comenzaremos con las más comunes hasta llegar a las menos frecuentes.

- Intentó comprobar el nombre de dominio antes de que la entrada DNS se pudiera propagar. Espere un poco e inténtelo de nuevo.

- No se ha especificado el registro de DNS. Verifique la entrada DNS, espere a que se propague y vuelva a intentarlo.

- El nombre de dominio ya se comprobó en otro directorio. Busque el nombre de dominio y elimínelo del otro directorio y vuelva a intentarlo.

- El registro de DNS contiene un error. Corrija el error e inténtelo de nuevo.

- No tiene permisos suficientes para actualizar los registros DNS. Comparta las entradas DNS con la persona o equipo de la organización que tenga este acceso y pídale que agregue la entrada DNS.


## Pasos siguientes

-   [Asignación de usuarios a un dominio personalizado](active-directory-add-domain-add-users.md)

-   [Managing custom domain names (Administración de nombres de dominio).](active-directory-add-manage-domain-names.md)

-   [Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)

-   [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Información general conceptual de nombres de dominio personalizado en Azure Active Directory](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0427_2016-->