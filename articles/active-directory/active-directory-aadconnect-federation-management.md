<properties
	pageTitle="Servicios de federación de Active Directory y personalización con Azure AD Connect | Microsoft Azure"
	description="Administración de AD FS con Azure AD Connect y personalización del inicio de sesión de AD FS del usuario con Azure AD Connect y PowerShell."
	keywords="AD FS, ADFS, administración de AD FS, AAD Connect, Connect, inicio de sesión, personalización de AD FS, reparación de la confianza, Office 365, federación, usuario de confianza"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

# Servicios de federación de Active Directory y personalización con Azure AD Connect

En este artículo se describen diversas tareas relacionadas con los Servicios de federación de Active Directory (AD FS) que se pueden realizar con Azure AD Connect y otras tareas comunes de AD FS que pueden ser necesarias para la configuración completa de una granja de servidores de AD FS.

| Tema. | Qué trata |
|:------|:-------------|
|**Administración de AD FS**|
|[Reparación de la confianza](#repairthetrust)| Reparación de la confianza de federación con Office 365 |
|[Incorporación de un nuevo servidor de AD FS](#addadfsserver) | Expansión de la granja de servidores de AD FS con un servidor de AD FS adicional|
|[Incorporación de un nuevo servidor proxy de aplicación web de AD FS](#addwapserver) | Expansión de la granja de servidores de AD FS con un servidor WAP adicional|
|[Incorporación de un nuevo dominio federado](#addfeddomain)| Adición de un dominio federado|
| **Personalización de AD FS**|
|[Incorporación de un logotipo de la compañía personalizado o una ilustración](#customlogo)| Personalización de una página de inicio de sesión de FS con el logotipo de la empresa e ilustración |
|[Incorporación de la descripción de inicio de sesión](#addsignindescription) | Adición de la descripción de la página de inicio de sesión |
|[Modificación de las reglas de notificaciones de AD FS](#modclaims) | Modificación de las notificaciones de AD FS en diversos escenarios de federación |

## Administración de AD FS

Azure AD Connect proporciona diversas tareas relacionadas con AD FS que se pueden realizar con el Asistente para Azure AD Connect con mínima intervención por parte del usuario. Una vez finalizada la instalación de Azure AD Connect con el asistente, puede ejecutarlo de nuevo para realizar tareas adicionales.

### Reparación de la confianza <a name=repairthetrust></a>

Azure AD Connect puede comprobar el estado actual de la confianza de AD FS y Azure AD, y tomar las medidas adecuadas para repararla. Siga estos pasos para reparar la confianza de Azure AD y AD FS.

Seleccione **Reparar AAD y confianza de ADFS** en la lista de tareas disponibles.

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

En la página **Conectar con Azure AD**, proporcione las credenciales de administrador global de Azure AD y haga clic en Siguiente.

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

En la página **Credenciales de acceso remoto**, proporcione las credenciales del administrador de dominio.

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

Cuando haga clic en Siguiente, Azure AD Connect comprobará el estado del certificado y mostrará los problemas que existan.

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

La página **Listo para configurar** mostrará la lista de acciones que se llevarán a cabo para reparar la confianza.

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

Haga clic en Instalar para continuar y reparar la confianza.

>[AZURE.NOTE] Azure AD Connect solo puede reparar o tomar medidas para los certificados autofirmados. No se pueden reparar certificados de terceros mediante Azure AD Connect.

### Adición de un nuevo servidor de AD FS <a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect requiere el archivo de certificado PFX para agregar un servidor de AD FS. Por lo tanto, podrá realizar esta operación solamente si ha configurado la granja de servidores de AD FS con Azure AD Connect.

Seleccione **Implementar un servidor de federación adicional** y haga clic en Siguiente.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

En la página **Conectar con Azure AD**, proporcione las credenciales de administrador global de Azure AD y haga clic en Siguiente.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

Proporcione las credenciales del administrador de dominio en la página siguiente.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

En la página siguiente, Azure AD Connect le pedirá la contraseña del archivo PFX que proporcionó al configurar la nueva granja de servidores de AD FS con Azure AD Connect. Haga clic en Escribir contraseña para proporcionar la contraseña del archivo PFX.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

En la siguiente página, proporcione el nombre o la dirección IP de los demás servidores que desee agregar a la granja de servidores de AD FS.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

Haga clic en Siguiente y recorra la página de configuración final. Una vez que Azure AD Connect haya terminado de agregar los servidores a la granja de servidores de AD FS, se le ofrecerá la opción de comprobar la conectividad.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Adición de un nuevo servidor proxy de aplicación web de AD FS <a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect requiere el archivo de certificado PFX para agregar un servidor proxy de aplicación web. Por lo tanto, podrá realizar esta operación solamente si ha configurado la granja de servidores de AD FS con Azure AD Connect.

Seleccione **Implementar proxy de aplicación web** en la lista de tareas disponibles.

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

En la siguiente página, proporcione las credenciales del administrador global de Azure.

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

A continuación, verá la página **Especificar el certificado SSL**, donde debe indicar la contraseña para el archivo PFX que proporcionó durante la configuración de la granja de servidores de AD FS con Azure AD Connect.

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

En la siguiente página, incluya el servidor que se agregará como proxy de aplicación web. Como el servidor proxy de aplicación web puede estar unido al dominio o no, se le pedirán credenciales administrativas para el servidor que se va a agregar.

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

En la página **Credenciales de confianza del proxy**, proporcione credenciales administrativas para configurar la confianza del proxy y tener acceso al servidor principal en la granja de servidores de AD FS.

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

En la página **Listo para configurar**, se muestra la lista de acciones que se realizarán.

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

Haga clic en Instalar para finalizar la configuración. Una vez completada la configuración, se le ofrece la opción de comprobar la conectividad con los servidores. Haga clic en Comprobar para comprobar la conectividad.

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Incorporación de un nuevo dominio federado <a name=addfeddomain></a>

Es fácil agregar un nuevo dominio para la federación con Azure AD mediante Azure AD Connect. Azure AD Connect, no solo agrega el nuevo dominio para la federación, sino que modifica las reglas de notificación para reflejar correctamente el emisor cuando existen varios dominios federados con Azure AD.

Para agregar un nuevo dominio federado, seleccione la tarea **Agregar un dominio de Azure AD adicional**.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

En la siguiente página del asistente, proporcione las credenciales de administrador global para Azure AD.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

En la página Credenciales de acceso remoto, proporcione las credenciales del administrador del dominio.

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

En la siguiente página del asistente, aparecerá una lista de dominios de Azure AD con los que federar su directorio local. Elija el dominio de la lista.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

Después de elegir el dominio, se le proporcionará información adecuada sobre otras acciones que el asistente vaya a llevar a cabo y cómo afectarán a la configuración. En algunos casos, si selecciona un dominio que aún no esté comprobado en Azure AD, se le proporcionará información para ayudarlo a comprobarlo. Consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](active-directory-add-domain.md) para más información sobre cómo comprobar el dominio.

Haga clic en Siguiente y en la página **Listo para configurar** se mostrará la lista de acciones que realizará Azure AD Connect. Haga clic en Instalar para finalizar la configuración.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## Personalización de AD FS

En las secciones siguientes, se proporcionan detalles sobre cómo realizar algunas de las tareas comunes que probablemente deba llevar a cabo para personalizar la página de inicio de sesión de AD FS.

### Adición de un logotipo de la compañía personalizado o una ilustración <a name=customlogo></a>

Para cambiar el logotipo de la compañía que se muestra en la página de inicio de sesión, use el siguiente cmdlet de Windows PowerShell y la sintaxis.

> [AZURE.NOTE] Las dimensiones recomendadas para el logotipo son 260 × 35 a 96 ppp con un tamaño de archivo no superior a 10 kB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Se requiere el parámetro TargetName. El tema predeterminado que se incluyó con AD FS se llama default.
 

### Adición de la descripción de inicio de sesión <a name=addsignindescription></a>

Para agregar una descripción de la página de inicio de sesión a dicha página, use el siguiente cmdlet de Windows PowerShell y la sintaxis.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificación de las reglas de notificaciones de AD FS <a name=modclaims></a>

AD FS ofrece la opción de especificar reglas personalizadas para emitir notificaciones. Admite un amplio lenguaje de notificaciones que sirve para crear reglas de notificaciones personalizadas. Para más información, puede consultar [este](https://technet.microsoft.com/library/dd807118.aspx) artículo.

En las secciones siguientes, se explica cómo escribir reglas personalizadas para algunos escenarios relacionados con la federación de Azure AD y AD FS.

#### Identificador inmutable dependiente de si hay un valor presente en el atributo

Azure AD Connect permite especificar un atributo que se usará como delimitador de origen cuando los objetos se sincronicen con Azure AD. Es posible que desea emitir una notificación de identificador inmutable dependiente de si el valor del atributo personalizado no está vacío. Por ejemplo, considere que ha seleccionado ms-ds-consistencyguid como atributo del delimitador de origen y desea emitir ImmutableID como ms-ds-consistencyguid por si el atributo tiene un valor para él; de lo contrario, emita objectGuid como identificador inmutable. Puede construir el conjunto de reglas de notificación personalizadas tal como se describe a continuación:

**Regla 1 (consultar atributos)**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

En esta regla simplemente se consultan los valores de ms-ds-consistencyguid y objectGuid para el usuario desde Active Directory. Cambie el nombre del almacén al adecuado que esté disponible en su implementación de ADFS y el tipo de notificaciones al adecuado que esté definido en su federación para objectGuid y ms-ds-consistencyguid. He definido tipos de notificaciones personalizados en mi entorno de prueba.

Además, al usar "add" y no "issue", no se tiene que agregar una emisión de salida para la entidad y solo se usan los valores como valores intermedios. Se emitirá la notificación en una regla posterior después de establecerse el valor que se usará como identificador inmutable.

**Regla 2: (comprobar si ms-ds-consistencyguid existe para el usuario)**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regla simplemente define un marcador temporal "idflag", que se establece en "useguid" si no hay ningún atributo ms-ds-consistencyguid rellenado para el usuario. Esto se debe a que ADFS no admite notificaciones vacías. Por tanto, cuando se agregan las notificaciones http://contoso.com/ws/2016/02/identity/claims/objectguid y http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid en la regla 1, solo terminará con la notificación msdsconsistencyguid si se rellena el valor para el usuario. Si no se rellena, ADFS considera que aparecerá como valor vacío y lo elimina directamente. Como sabe, todos los objetos tendrán el atributo objectGuid, así que esa notificación seguirá estando después de que se ejecute la regla 1.

**Regla 3: emitir ms-ds-consistencyguid como identificador inmutable si está presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Se trata de una comprobación EXIST implícita. Si el valor de la notificación existe, emítalo como identificador inmutable. Observe que estoy emitiendo la notificación nameidentifier. Tendrá que cambiar este valor al tipo de notificación adecuado para un identificador inmutable en su entorno.

**Regla 4: emitir el GUID de objeto como identificador inmutable si ms-ds-consistencyguid no está presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

En esta regla simplemente se va a comprobar la marca temporal "idflag" y, según el valor, se decidirá si emitir o no la notificación.

> [AZURE.NOTE] La secuencia de estas reglas es importante.

#### SSO con un UPN de subdominio

Puede agregar más de un dominio para federarlo mediante Azure AD Connect ([Incorporación de un nuevo dominio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain)). La notificación de UPN deberá modificarse para que el identificador de emisor se corresponda con el dominio raíz y no con el subdominio ya que el dominio raíz federado cubre también el elemento secundario.

De forma predeterminada, la regla de notificaciones para el identificador de emisor se establece como:

	c:[Type 
	== “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Notificación del identificador de emisor predeterminado](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

La regla predeterminada simplemente toma el sufijo UPN y lo utiliza en la notificación de identificador del emisor. Por ejemplo, John es un usuario de sub.contoso.com y contoso.com está federado con Azure AD. John entra en john@sub.contoso.com como nombre de usuario al iniciar sesión en Azure AD y, a continuación, la regla predeterminada de notificaciones de identificador de emisor en AD FS lo controlará de la siguiente manera:

c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valor de notificación:** http://sub.contoso.com/adfs/services/trust/

Para tener solo el dominio raíz en el valor de notificación del emisor, cambie la regla de notificaciones a:

	c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## Pasos siguientes

Obtenga más información sobre las [opciones de inicio de sesión del usuario](active-directory-aadconnect-user-signin.md)

<!---HONumber=AcomDC_0803_2016-->