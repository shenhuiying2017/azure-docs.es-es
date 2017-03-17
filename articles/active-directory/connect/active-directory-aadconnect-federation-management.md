---
title: "Servicios de federación de Active Directory y personalización con Azure AD Connect | Microsoft Docs"
description: "Administración de AD FS con Azure AD Connect y personalización del inicio de sesión de AD FS del usuario con Azure AD Connect y PowerShell."
keywords: "AD FS, ADFS, administración de AD FS, AAD Connect, Connect, inicio de sesión, personalización de AD FS, reparación de la confianza, Office&365;, federación, usuario de confianza"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: da3b8a375d380a0e2f1c2e243a475f2f3ff94bfc
ms.lasthandoff: 03/07/2017


---
# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Servicios de federación de Active Directory y personalización con Azure AD Connect
En este artículo se describen las tareas relacionadas con los Servicios de federación de Active Directory (AD FS) que se pueden realizar con Microsoft Azure Active Directory Connect y otras tareas comunes de AD FS que pueden ser necesarias para la configuración completa de una granja de servidores de AD FS.

| Tema. | Qué trata |
|:--- |:--- |
| **Administración de AD FS** | |
| [Reparación de la confianza](#repairthetrust) |Reparación de la confianza de federación con Office 365 |
| [Adición de un servidor de AD FS](#addadfsserver) |Expansión de la granja de servidores de AD FS con un servidor de AD FS adicional |
| [Adición de un servidor proxy de aplicación web de AD FS](#addwapserver) |Expansión de la granja de servidores de AD FS con un servidor WAP adicional |
| [Adición de un dominio federado](#addfeddomain) |Adición de un dominio federado |
| [Actualizar el certificado SSL](active-directory-aadconnectfed-ssl-update.md)| Actualizar el certificado SSL para una granja de Servicios de federación de Active Directory (AD FS)|
| **Personalización de AD FS** | |
| [Adición de un logotipo de la compañía personalizado o una ilustración](#customlogo) |Personalización de una página de inicio de sesión de AD FS con el logotipo de la empresa e ilustración |
| [Adición de la descripción de inicio de sesión](#addsignindescription) |Adición de la descripción de inicio de sesión |
| [Modificación de las reglas de notificaciones de AD FS](#modclaims) |Modificación de las notificaciones de AD FS en diversos escenarios de federación |

## <a name="ad-fs-management"></a>Administración de AD FS
Azure AD Connect proporciona diversas tareas relacionadas con AD FS que se pueden realizar con el Asistente para Azure AD Connect con mínima intervención por parte del usuario. Una vez finalizada la instalación de Azure AD Connect con el asistente, puede ejecutarlo de nuevo para realizar tareas adicionales.

## Reparación de la confianza <a name=repairthetrust></a>
Azure AD Connect puede comprobar el estado actual de la confianza de AD FS y de Azure Active Directory, y tomar las medidas adecuadas para repararla. Siga estos pasos para reparar la confianza de Azure AD y AD FS.

1. Seleccione **Reparar AAD y confianza de ADFS** en la lista de tareas disponibles.
   ![Reparar AAD y confianza de ADFS](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)
2. En la página **Conectar con Azure AD**, proporcione las credenciales de administrador global de Azure AD y haga clic en **Siguiente**.
   ![Conectarse a Azure](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)
3. En la página **Credenciales de acceso remoto** , proporcione las credenciales del administrador de dominio.
   ![Credenciales de acceso remoto](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Cuando haga clic en **Siguiente**, Azure AD Connect comprobará el estado del certificado y mostrará los problemas que existan.

    ![Estado de los certificados](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    La página **Listo para configurar** mostrará la lista de acciones que se llevarán a cabo para reparar la confianza.

    ![Listo para configurar](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)
4. Haga clic en **Instalar** para reparar la confianza.

> [!NOTE]
> Azure AD Connect solo puede reparar o actuar en los certificados autofirmados. Azure AD Connect no puede reparar certificados de terceros.
>
>

## Adición de un servidor de AD FS <a name=addadfsserver></a>

> [!NOTE]
> Azure AD Connect requiere el archivo de certificado PFX para agregar un servidor de AD FS. Por lo tanto, puede realizar esta operación solamente si ha configurado la granja de servidores de AD FS con Azure AD Connect.
>
>

1. Seleccione **Implementar un servidor de federación adicional** y haga clic en **Siguiente**.
   ![Servidor de federación adicional](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)
2. En la página **Conectar con Azure AD**, proporcione las credenciales de administrador global de Azure AD y haga clic en **Siguiente**.
   ![Conectarse a Azure](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)
3. Proporcione las credenciales del administrador de dominio.
   ![Credenciales del administrador de dominio](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)
4. Azure AD Connect le pedirá la contraseña del archivo PFX que proporcionó al configurar la nueva granja de servidores de AD FS con Azure AD Connect. Haga clic en **Escribir contraseña** para proporcionar la contraseña del archivo PFX.
   ![Contraseña del certificado](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Especificar certificado SSL](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)
5. En la página **Servidores de AD FS** , escriba el nombre del servidor o la dirección IP que se agregarán a la granja de servidores de AD FS.
   ![AD FS servers](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)
6. Haga clic en **Siguiente** y recorra la página **Configurar** final. Una vez que Azure AD Connect haya terminado de agregar los servidores a la granja de servidores de AD FS, se le ofrecerá la opción de comprobar la conectividad.
   ![Listo para configurar](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Instalación completada](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## Adición de un servidor proxy de aplicación web de AD FS <a name=addwapserver></a>

> [!NOTE]
> Azure AD Connect requiere el archivo de certificado PFX para agregar un servidor proxy de aplicación web. Por lo tanto, podrá realizar esta operación solamente si ha configurado la granja de servidores de AD FS con Azure AD Connect.
>
>

1. Seleccione **Implementar proxy de aplicación web** en la lista de tareas disponibles.
   ![Deploy web application proxy](media/active-directory-aadconnect-federation-management/WapServer1.PNG)
2. Proporcione las credenciales del administrador global de Azure.
   ![Conectar con Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)
3. En la página **Especificar el certificado SSL**, indique la contraseña para el archivo PFX que proporcionó durante la configuración de la granja de servidores de AD FS con Azure AD Connect.
   ![Contraseña del certificado](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Especificar certificado SSL](media/active-directory-aadconnect-federation-management/WapServer4.PNG)
4. Incluya el servidor que se agregará como proxy de aplicación web. Como el servidor proxy de aplicación web puede no estar unido al dominio, se le pedirán credenciales administrativas para el servidor que se va a agregar.
   ![Credenciales administrativas del servidor](media/active-directory-aadconnect-federation-management/WapServer5.PNG)
5. En la página **Credenciales de confianza del proxy**, proporcione credenciales administrativas para configurar la confianza del proxy y tener acceso al servidor principal en la granja de servidores de AD FS.
   ![Credenciales de confianza del proxy](media/active-directory-aadconnect-federation-management/WapServer6.PNG)
6. En la página **Listo para configurar**, el asistente muestra la lista de acciones que se realizarán.
   ![Listo para configurar](media/active-directory-aadconnect-federation-management/WapServer7.PNG)
7. Haga clic en **Instalar** para finalizar la configuración. Una vez completada la configuración, se le ofrece la opción de comprobar la conectividad con los servidores. Haga clic en **Comprobar** para validar la conectividad.
   ![Instalación completada](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## Adición de un dominio federado <a name=addfeddomain></a>

Es fácil agregar un dominio para la federación con Azure AD mediante Azure AD Connect. Azure AD Connect agrega el dominio para la federación y modifica las reglas de notificaciones para reflejar correctamente el emisor cuando existen varios dominios federados con Azure AD.

1. Para agregar un dominio federado, seleccione la tarea **Agregar un dominio de Azure AD adicional**.
   ![Dominio de Azure AD adicional](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)
2. En la siguiente página del asistente, proporcione las credenciales de administrador global para Azure AD.
   ![Conectar con Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)
3. En la página **Credenciales de acceso remoto** , proporcione las credenciales del administrador del dominio.
   ![Credenciales de acceso remoto](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)
4. En la siguiente página del asistente, aparecerá una lista de dominios de Azure AD con los que puede federar su directorio local. Elija el dominio en la lista.
   ![Dominio de Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Después de elegir el dominio, se le proporcionará información adecuada sobre otras acciones que el asistente vaya a llevar a cabo y cómo afectarán a la configuración. En algunos casos, si selecciona un dominio que aún no esté comprobado en Azure AD, se le proporcionará información para comprobarlo. Consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](../active-directory-add-domain.md) para más información.
5. Haga clic en **Siguiente** y en la página **Listo para configurar** se mostrará la lista de acciones que realizará Azure AD Connect. Haga clic en **Instalar** para finalizar la configuración.
   ![Listo para configurar](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalización de AD FS
En las secciones siguientes, se proporcionan detalles sobre algunas de las tareas comunes que probablemente deba realizar para personalizar la página de inicio de sesión de AD FS.

## Adición de un logotipo de la compañía personalizado o una ilustración <a name=customlogo></a>
Para cambiar el logotipo de la compañía que se muestra en la página de **inicio de sesión** , use el siguiente cmdlet de Windows PowerShell y la sintaxis.

> [!NOTE]
> Las dimensiones recomendadas para el logotipo son 260 × 35 a 96 ppp con un tamaño de archivo no superior a 10 kB.
>
>

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Se requiere el parámetro *TargetName* . El tema predeterminado que se incluyó con AD FS se llama Default.
>
>

## Adición de la descripción de inicio de sesión <a name=addsignindescription></a>
Para agregar una descripción de la **página de inicio de sesión**a dicha página, use el siguiente cmdlet de Windows PowerShell y la sintaxis.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## Modificación de las reglas de notificaciones de AD FS <a name=modclaims></a>
AD FS admite un amplio lenguaje de notificaciones que sirve para crear reglas de notificaciones personalizadas. Para más información, consulte [El papel de lenguaje de reglas de notificación](https://technet.microsoft.com/library/dd807118.aspx).

En las secciones siguientes, se describe cómo escribir reglas personalizadas para algunos escenarios relacionados con la federación de Azure AD y AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Identificador inmutable dependiente de si hay un valor presente en el atributo
Azure AD Connect permite especificar un atributo que se usará como delimitador de origen cuando los objetos se sincronicen con Azure AD. Si el valor del atributo personalizado no está vacío, puede emitir una notificación de identificador inmutable. Por ejemplo, puede seleccionar **ms-ds-consistencyguid** como atributo del delimitador de origen y emitir **ImmutableID** como **ms-ds-consistencyguid** por si el atributo tiene un valor para él. Si no hay ningún valor para el atributo, emita **objectGuid** como identificador inmutable.  Puede construir el conjunto de reglas de notificaciones personalizadas, tal como se describe en la sección siguiente.

**Regla 1: Consultar atributos**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

En esta regla simplemente se consultan los valores de **ms-ds-consistencyguid** y **objectGuid** para el usuario desde Active Directory. Cambie el nombre del almacén por un nombre adecuado en la implementación de AD FS. Cambie también el tipo de notificaciones a un tipo correcto para la federación, tal como se define para los atributos **objectGuid** y **ms-ds-consistencyguid**.

Además, al usar **add** y no **issue**, no se tiene que agregar una emisión de salida para la entidad y solo se usan los valores como valores intermedios. Se emitirá la notificación en una regla posterior después de establecerse el valor que se usará como identificador inmutable.

**Regla 2: comprobar si ms-ds-consistencyguid existe para el usuario**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regla define un marcador temporal **idflag**, que se establece en **useguid** si no hay ningún atributo **ms-ds-consistencyguid** rellenado para el usuario. Esto se debe a que ADFS no admite notificaciones vacías. Portanto, cuando se agrega http://contoso.com/ws/2016/02/identity/claims/objectguid and http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid in Rule 1, terminará con la notificación **msdsconsistencyguid** solo si el valor está rellenado para el usuario. Si no se rellena, AD FS ve que tiene un valor vacío y lo coloca inmediatamente. Todos los objetos tendrán el atributo **objectGuid**, así que esa notificación seguirá estando después de que se ejecute la regla 1.

**Regla 3: Emitir el atributo ms-ds-consistencyguid como identificador inmutable si está presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Se trata de una comprobación **Exist** implícita. Si el valor de la notificación existe, emítalo como identificador inmutable. En el ejemplo anterior se utiliza la notificación **nameidentifier** . Tendrá que cambiar este valor al tipo de notificación adecuado para un identificador inmutable en su entorno.

**Regla 4: Emitir el atributo objectGUID como identificador inmutable si ms-ds-consistencyguid no está presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

En esta regla, simplemente va a comprobar el atributo **idflag**temporal. Decida si va a emitir la notificación basándose en su valor.

> [!NOTE]
> La secuencia de estas reglas es importante.
>
>

### <a name="sso-with-a-subdomain-upn"></a>SSO con un UPN de subdominio
Puede agregar más de un dominio para federarlo mediante Azure AD Connect, tal como se describe en [Incorporación de un nuevo dominio federado](active-directory-aadconnect-federation-management.md#addfeddomain). Debe modificar la notificación de UPN para que el identificador de emisor se corresponda con el dominio raíz y no con el subdominio, ya que el dominio raíz federado cubre también el elemento secundario.

De forma predeterminada, la regla de notificaciones para el identificador de emisor se establece como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Notificación del identificador de emisor predeterminado](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

La regla predeterminada simplemente toma el sufijo UPN y lo utiliza en la notificación de identificador del emisor. Por ejemplo, John es un usuario de sub.contoso.com y contoso.com está federado con Azure AD. John entra en john@sub.contoso.com como nombre de usuario al iniciar sesión en Azure AD y, a continuación, la regla predeterminada de notificaciones de identificador de emisor en AD FS lo controla de la siguiente manera.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valor de notificación:** http://sub.contoso.com/adfs/services/trust/

Para tener solo el dominio raíz en el valor de notificación del emisor, cambie la regla de notificaciones para que coincida con lo siguiente.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las [opciones de inicio de sesión del usuario](active-directory-aadconnect-user-signin.md).

