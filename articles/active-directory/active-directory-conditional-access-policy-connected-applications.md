<properties
	pageTitle="Establecimiento de una directiva de acceso condicional basado en dispositivos de Azure Active Directory para el control de acceso a aplicaciones conectadas a Azure Active Directory"
	description="Explica cómo los administradores de TI pueden establecer directivas de acceso condicional basado en dispositivos para aplicaciones de conectadas a Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="markvi"/>


# Establecimiento de una directiva de acceso condicional basado en dispositivos de Azure Active Directory para el control de acceso a aplicaciones conectadas a Azure Active Directory 


El acceso condicional basado en dispositivos de Azure Active Directory es la capacidad que dispone para proteger los recursos de la organización desde lo siguiente:

- Acceso realizado desde dispositivos desconocidos o no administrados
- Dispositivos que no cumplen las directivas de seguridad, tal como define la organización.

Para una introducción general al acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).

Puede establecer directivas de acceso condicional basado en dispositivos para proteger lo siguiente:

- Office 365 SharePoint Online para proteger documentos y sitios de la organización.

- Office 365 Exchange Online para proteger el correo electrónico de la organización.

- Aplicaciones de SaaS conectadas a Azure AD para su autenticación.

- Aplicaciones locales publicadas mediante el proxy de aplicación de Azure AD.


En el Portal de administración de Azure, puede establecer esta directiva accediendo a la aplicación específica en el directorio.

 
  ![Aplicaciones](./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicaciones")


Después de seleccionar la aplicación, haga clic en la pestaña **Configurar** para establecer la directiva de acceso condicional.


  ![Reglas de acceso basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/02.png "Reglas de acceso basadas en el dispositivo")


 

Para habilitar una directiva de acceso condicional basado en dispositivos, en la sección **Reglas de acceso basadas en dispositivos**, para **Habilitar reglas de acceso**, seleccione **Activado**.

Esta directiva consta de tres componentes:

1. **Aplicar a**: el ámbito de los usuarios al que se aplica esta directiva cuando se accede a la aplicación.

2. **Reglas del dispositivo**: las condiciones necesarias que los dispositivos deben cumplir antes de acceder a la aplicación.

3. **Cumplimiento de la aplicación**: las aplicaciones cliente (nativas y de explorador) para las que se debe evaluar la directiva.

  ![Reglas de acceso basadas en el dispositivo](./media/active-directory-conditional-access-policy-connected-applications/03.png "Reglas de acceso basadas en el dispositivo")
 

## Selección de los usuarios a los que se aplica la directiva 

En la sección **Aplicar a**, puede seleccionar el ámbito de los usuarios a los que se aplica esta directiva.

Tiene dos opciones para el ámbito:

- **Todos los usuarios**: para todos los que acceden a la aplicación

- **Grupos**: para limitar el ámbito a los usuarios que son miembros de un grupo o grupos.

![Se aplica a](./media/active-directory-conditional-access-policy-connected-applications/11.png "Se aplica a")


Al seleccionar **Excepto**, puede impedir a usuarios de esta directiva tener acceso a la aplicación. Esto es útil cuando necesita permitir que usuarios específicos accedan temporalmente a la aplicación. Seleccione esta opción, por ejemplo, si algunos de los usuarios tienen dispositivos que no están preparados para el acceso condicional (todavía no se han registrado, que salen de un cumplimiento, etc.).
 

## Selección de las condiciones que debe cumplir los dispositivos 

Con **Reglas del dispositivo**, establece las condiciones para que los dispositivos puedan acceder a la aplicación.

Para el acceso condicional basado en dispositivos, se admiten los siguientes dispositivos:

- Actualización de aniversario de Windows 10, Windows 7 y Windows 8.1.

- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2

- Dispositivos iOS (iPad, iPhone)

- Dispositivos Android

La compatibilidad para Mac se agregará próximamente.

  ![Dispositivos](./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicaciones")



 >[AZURE.NOTE] Para más información sobre las diferencias entre unión a un dominio y Azure AD Join, consulte [Uso de dispositivos de Windows 10 en el área de trabajo](active-directory-azureadjoin-windows10-devices.md).


Tiene dos opciones para las reglas de dispositivos:

- **Todos los dispositivos deben ser compatibles**: esto requiere que todas las plataformas de dispositivo que acceden a la aplicación sean compatibles. Para las plataformas que no son compatibles con el acceso condicional basado en dispositivos, a los dispositivos se le deniega el acceso.

- **Solo los dispositivos seleccionados deben ser compatibles**: esto requiere que solo las plataformas de dispositivos seleccionados sean compatibles. Las plataformas no seleccionadas u otras plataformas que acceden a la aplicación tienen permiso de acceso".

  ![Aplicaciones](./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicaciones")



Los dispositivos unidos a Azure AD son compatibles si se marcan como **compatibles** en el directorio por Microsoft Intune o un sistema de Mobile Device Management (MDM) de terceros que se integra con Azure AD.

Los dispositivos unidos a un dominio son compatibles en una de dos maneras:

- Si se registran con Azure AD, por el hecho de que están unidos a un dominio muchas organizaciones los tratan como dispositivos de confianza.

- Si se marcan como "compatibles" en Azure AD mediante System Center Configuration Manager 2016.

 ![Reglas de dispositivo](./media/active-directory-conditional-access-policy-connected-applications/06.png "Reglas de dispositivo")
 

Los dispositivos personales de Windows son compatibles si se marcan como **compatibles** en el directorio por Microsoft Intune o un sistema de Mobile Device Management (MDM) de terceros que se integra con Azure AD.

Los dispositivos que no son de Windows son compatibles si se marcan como **compatibles** en el directorio por Microsoft Intune.

 >[AZURE.NOTE] Para más información sobre cómo configurar Azure AD para el cumplimiento de dispositivos mediante el sistema de administración, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).


Al seleccionar las plataformas de dispositivo específicas, puede seleccionar una o varias opciones, incluidas Android, iOS, Windows Mobile (teléfonos y tabletas Windows 8.1) y Windows (todos los demás dispositivos Windows incluidos todos los dispositivos Windows 10). Con esta opción, la evaluación de directivas solo se produce en las plataformas seleccionadas. Si se ha intentado acceder a un dispositivo que no forma parte de la selección, no se evalúa ninguna directiva de dispositivo y el dispositivo se permite si se permite el usuario.

![Reglas de dispositivo](./media/active-directory-conditional-access-policy-connected-applications/07.png "Reglas de dispositivo")
  

## Selección del tipo de aplicaciones cliente en las que se evaluará la directiva 

En la sección **Cumplimiento de la aplicación**, establezca el tipo de aplicaciones para las que debe evaluarse la directiva.


Tiene dos opciones para las aplicaciones:

- Para aplicaciones nativas y de explorador
- Solo para aplicaciones nativas.


![Aplicaciones](./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicaciones")


Si se selecciona **Para aplicaciones nativas y de explorador**, se aplica la directiva sobre el acceso a las aplicaciones mediante lo siguiente:

- Exploradores (por ejemplo, Edge en Windows 10, Safari en iOS, etc.)
- Aplicaciones que utilizan la biblioteca de aplicaciones de Active Directory (ADAL) en cualquier plataforma o la API Web Account Manager (WAM) en Windows 10

>[AZURE.NOTE] Para más información sobre la compatibilidad con exploradores y otras consideraciones para el usuario final que accede a aplicaciones protegidas por entidad emisora de certificados basadas en dispositivos, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md).

 

## Protección del acceso de correo electrónico de aplicaciones basadas en Exchange Active Sync 

En las aplicaciones de Office 365 Exchange Online, tendrá una sección adicional denominada **Exchange Activesync**. Esta sección le permite bloquear el acceso de correo electrónico a las aplicaciones de correo electrónico basadas en Exchange Active Sync.

![Aplicaciones](./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicaciones")
 
![Aplicaciones](./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicaciones")

 
## Otros temas

- [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0914_2016-->