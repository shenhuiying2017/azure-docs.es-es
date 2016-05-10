<properties
	pageTitle="Incorporación de su nombre de dominio personalizado a Azure Active Directory | Microsoft Azure"
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

# Incorporación de su nombre de dominio personalizado a Azure Active Directory

Dispone de uno o varios nombres de dominio que su organización usa para su actividad empresarial, y los usuarios inician sesión en la red corporativa mediante su nombre de dominio corporativo. Ahora que usa Azure Active Directory (Azure AD), también puede agregar el nombre de dominio corporativo a Azure AD. Esto permite asignar en el directorio nombres de usuario que resultan familiares a los usuarios, como ‘alice@contoso.com’. El proceso es simple:

- Agregue el nombre de dominio en el asistente para **agregar dominio** en el Portal de Azure clásico.

- Obtenga la entrada DNS en el Portal de Azure AD clásico o la herramienta Azure AD Connect.

- Agregue la entrada DNS para el nombre de dominio al archivo de zona DNS en el sitio web del registrador DNS.

- Compruebe el nombre de dominio en el Portal de Azure AD clásico o la herramienta Azure AD Connect.


Hasta que compruebe su nombre de dominio personalizado, los usuarios tienen que iniciar sesión con nombres de usuario como 'alice@contoso.onmicrosoft.com', que usan el nombre de dominio inicial para el directorio. Si necesita varios nombres de dominio personalizados, como 'contoso.com' y 'contosobank.com', se puede agregar un máximo de 900 nombres de dominio. Siga los mismos pasos de este artículo para agregar cada nombre de dominio.

## Incorporación de nombres de dominio personalizados al directorio

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con una cuenta de usuario que sea administrador global de su directorio Azure AD.

2. En **Active Directory**, abra el directorio y seleccione la pestaña **Dominios**.

3. En la barra de comandos, seleccione **Agregar** y después escriba el nombre de dominio personalizado, como 'contoso.com'. Asegúrese de incluir .com, .net u otra extensión de nivel superior.

4. Si piensa configurar este dominio para un [inicio de sesión federado](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) con su instancia de Active Directory local, seleccione la casilla.

5. Seleccione **Agregar**.

Ahora que ha agregado el nombre de dominio, Azure AD debe comprobar que su organización posea el nombre de dominio. Para que Azure AD pueda realizar esta comprobación, tiene que agregar una entrada DNS en el archivo de zona DNS para el nombre de dominio. Esta tarea se lleva a cabo en el sitio web del registrador de nombres de dominio para el nombre de dominio.

## Obtención de las entradas DNS para el nombre de dominio

Las entradas DNS están en la segunda página del asistente para **agregar dominio**, si no va a federarse con un servicio Windows Server Active Directory local.

Si va a configurar el dominio para la federación, se le dirigirá a descargar la herramienta Azure AD Connect. Ejecute la herramienta Azure AD Connect para [obtener las entradas DNS que tiene que agregar en el registrador de nombres de dominio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Además, la herramienta Azure AD Connect comprobará el nombre de dominio para Azure AD.

## Incorporación de la entrada DNS en el archivo de zona DNS

1.  Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene permisos suficientes para actualizar la entrada DNS, pida a la persona o el equipo que tenga acceso que agregue la entrada DNS.

2.  Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. Esta entrada DNS permite a Azure AD comprobar la propiedad del dominio. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web. Puede tardar hasta una hora para que los registros DNS se propaguen.

[Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Comprobación del nombre de dominio con Azure AD

Una vez que haya agregado la entrada DNS, debe asegurarse de que se comprueba el nombre de dominio con Azure AD. Este es el paso final del proceso.

Si sigue teniendo el asistente para **agregar dominio** abierto, seleccione **Comprobar** en la tercera página del asistente. Debería esperar hasta una hora a que la entrada DNS se propague antes de comprobarlo.

Si el asistente para **agregar dominio** no sigue abierto, puede comprobar el dominio en el [Portal de Azure clásico](https://manage.windowsazure.com/):

1.  Inicie sesión con una cuenta de usuario que sea un administrador global de su directorio de Azure AD.

2.  Abra el directorio y seleccione la pestaña **Dominios**.

3.  Seleccione el dominio que desee comprobar.

4.  Seleccione **Comprobar** en la barra de comandos y después seleccione **Comprobar** en el cuadro de diálogo.

Felicidades por conseguirlo. Ya puede [asignar nombres de usuario a un dominio personalizado](active-directory-add-domain-add-users.md). Si tiene algún problema para comprobar el nombre de dominio, consulte la sección [Solución de problemas](#troubleshooting).

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
-   [Información general conceptual de nombres de dominio personalizado en Azure Active Directory](active-directory-add-domain-concepts.md)
-   [Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
-   [Utilización de PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->