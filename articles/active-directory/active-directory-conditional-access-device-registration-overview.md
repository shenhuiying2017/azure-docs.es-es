<properties
	pageTitle="Información general sobre el servicio Registro de dispositivos de Azure Active Directory | Microsoft Azure"
	description="es la base de los escenarios de acceso condicional basado en dispositivos. Cuando se registra un dispositivo, el Registro de dispositivos de Azure Active Directory aprovisiona el dispositivo con una identidad que sirve para autenticar el dispositivo cuando el usuario inicia sesión."
	services="active-directory"
	keywords="registro de dispositivos, habilitar registro de dispositivos, registro de dispositivos y MDM"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Introducción al Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de Azure Active Directory es la base de los escenarios de acceso condicional basado en dispositivos. Cuando se registra un dispositivo, el Registro de dispositivos de Azure Active Directory le proporciona una identidad que se utiliza para autenticar el dispositivo cuando el usuario inicia sesión. El dispositivo autenticado y los atributos del dispositivo pueden utilizarse para aplicar directivas de acceso condicional para las aplicaciones que se hospedan en la nube y locales.

Cuando se combina con una solución de administración de dispositivos móviles (MDM) como Intune, los atributos del dispositivo en Azure Active Directory se actualizan con información adicional sobre el mismo. Esto le permite crear reglas de acceso condicional que obligan a que el acceso desde dispositivos cumpla con las normas de seguridad y cumplimiento.

El Registro de dispositivos de Azure Active Directory está disponible en Azure Active Directory. El servicio incluye compatibilidad con dispositivos iOS, Android y Windows. Los distintos escenarios que usan el Registro de dispositivos de Azure Active Directory pueden tener requisitos y compatibilidad con la plataforma más específicos.

## Escenarios habilitados para el Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de Azure Active Directory incluye compatibilidad para dispositivos iOS, Android y Windows. Los distintos escenarios que usan el Registro de dispositivos de Azure AD pueden tener requisitos y compatibilidad con la plataforma más específicos. Estos escenarios son los siguientes:

- **Acceso condicional a las aplicaciones que están hospedadas de forma local**: puede utilizar los dispositivos registrados con directivas de acceso con las aplicaciones configuradas para usar AD FS en Windows Server 2012 R2. Para obtener más información sobre cómo configurar el acceso condicional localmente, consulte [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-on-premises-setup.md).

- **Acceso condicional para aplicaciones de Office 365 con Microsoft Intune**: los administradores de TI pueden aprovisionar directivas de dispositivo de acceso condicional para proteger los recursos corporativos y permitir que los trabajadores de la información en los dispositivos compatibles puedan acceder a los servicios. Para más información, consulte las directivas de dispositivo de acceso condicional para los servicios de Office 365.

##Configuración del Registro de dispositivos de Azure Active Directory

Debe habilitar el Registro de dispositivos de Azure AD en el Portal de Azure para que los dispositivos móviles puedan detectar el servicio mediante la búsqueda de registros DNS conocidos. Debe configurar el DNS de la empresa para que los dispositivos Windows 10, Windows 8.1, Windows 7, iOS y Android puedan detectar y usar el servicio. Puede ver y habilitar o deshabilitar dispositivos registrados mediante el Portal de administrador de Azure Active Directory.

### Habilitación del servicio Registro de dispositivos de Azure Active Directory

1. Inicie sesión como administrador en el portal de Microsoft Azure.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la pestaña **Directorio**, seleccione su directorio.
4. Seleccione la pestaña **Configurar**.
5. Desplácese hasta la sección llamada **Dispositivos**.
6. Seleccione **TODOS** en **LOS USUARIOS PUEDEN UNIR DISPOSITIVOS AL ÁREA DE TRABAJO**.
7. Seleccione el número máximo de dispositivos que quiere autorizar por usuario.

>[AZURE.NOTE]
La inscripción en Microsoft Intune o Administración de dispositivos móviles para Office 365 requiere la unión al área de trabajo. Si configuró alguno de estos servicios, se seleccionará TODOS y se deshabilitará el botón NINGUNO.

De forma predeterminada, la autenticación en dos fases no está habilitada para el servicio. Aunque se recomienda usar la autenticación en dos fases al registrar un dispositivo.

- Antes de requerir la autenticación en dos fases para este servicio, debe configurar un proveedor de este tipo de autenticación en Azure Active Directory y configurar las cuentas de usuario para la autenticación multifactor. Para ello consulte [Introducción a Azure Multi-Factor Authentication en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Si utiliza AD FS con Windows Server 2012 R2, debe configurar un módulo de autenticación en dos fases en AD FS. Para ello consulte [Introducción a Servidor Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## Configuración de la detección del Registro de dispositivos de Azure Active Directory
Los dispositivos de Windows 7 y Windows 8.1 detectarán el servicio de registro de dispositivos combinando el nombre de la cuenta de usuario con un nombre de servidor de registro de dispositivos conocido.

Debe crear un registro CNAME de DNS que apunte al registro A asociado con el servicio Registro de dispositivos de Azure Active Directory. El registro CNAME debe usar el prefijo enterpriseregistration conocido seguido del sufijo UPN que usan las cuentas de usuario en su organización. Si su organización usa varios sufijos UPN, deben crearse varios registros CNAME en DNS.

Por ejemplo, si usa dos sufijos UPN en la organización denominados @contoso.com y @region.contoso.com, cree los siguientes registros DNS.

| Entrada | Tipo | Dirección |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Visualización y administración de objetos de dispositivo en Azure Active Directory
1. En el Portal de administrador de Azure, puede ver, bloquear y desbloquear dispositivos. Un dispositivo bloqueado dejará de tener acceso a las aplicaciones configuradas para permitir solo dispositivos registrados.
2. Inicie sesión en el Portal de Microsoft Azure como administrador.
3. En el panel izquierdo, seleccione **Active Directory**.
4. Seleccione el directorio.
5. Seleccione la pestaña **Usuarios**. Luego seleccione un usuario para ver sus dispositivos.
6. Seleccione la pestaña **Dispositivos**.
7. Seleccione **Dispositivos registrados** en el menú desplegable.
8. Aquí puede ver, bloquear o desbloquear dispositivos registrados de usuarios.

## Otros temas

Puede registrar los dispositivos Windows 7 y Windows 8.1 unidos a un dominio en el Registro de dispositivos de Azure AD. En el siguiente tema se ofrece más información sobre los requisitos previos y los pasos necesarios para configurar el Registro de dispositivos en dispositivos Windows 7 y Windows 8.1.

- [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](active-directory-conditional-access-automatic-device-registration.md)
- [Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configuración del registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows 10 unidos a un dominio](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0629_2016-->