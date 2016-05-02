<properties
	pageTitle="Protección del acceso con privilegios en Azure AD | Microsoft Azure"
	description="Un tema que explica los enfoques para proteger el acceso con privilegios en Azure, Azure Active Directory y Microsoft Online Services."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor="mwahl"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="kgremban"/>


# Protección del acceso con privilegios en Azure AD

La protección del acceso con privilegios es un primer paso esencial para ayudar a proteger los activos empresariales en las organizaciones modernas. La seguridad de la mayoría de los activos empresariales de una organización depende de la integridad de las cuentas con privilegios que administran los sistemas de TI. Los ciberatacantes se dirigen a estas cuentas con la finalidad de conseguir acceso a los datos y los sistemas de una organización.

Proteger el acceso administrativo frente a determinados adversarios requiere aislar estas cuentas y sistemas administrativos de los riesgos. Cada vez más usuarios están comenzando a obtener acceso con privilegios mediante los servicios en la nube. Por ejemplo, los administradores globales de Office 365, los administradores de suscripciones de Azure y los usuarios que tienen acceso administrativo en máquinas virtuales o en aplicaciones de SaaS.

Microsoft recomienda seguir este plan a la hora de [proteger el acceso con privilegios](https://technet.microsoft.com/library/mt631194.aspx).

Para los clientes que usan Azure Active Directory para administrar el acceso a Azure, Office 365 u otros servicios y aplicaciones de Microsoft, estos principios se aplican tanto si las cuentas de usuario se administran y autentican en Active Directory como en Azure Active Directory. En las secciones siguientes se proporciona más información sobre las características de Azure AD que respaldan la protección del acceso con privilegios.

## Multi-Factor Authentication

Para aumentar la seguridad de la autenticación del administrador, debe exigir autenticación multifactor (MFA) antes de conceder privilegios. MFA es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios.

Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece autenticación segura mediante una serie de opciones de comprobación sencillas, como llamadas telefónicas, mensajes de texto, notificaciones de aplicaciones móviles, código de comprobación y tokens OATH de terceros.

Para obtener información general de cómo funciona Multi-Factor Authentication, vea el siguiente vídeo.
<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

Para más información, consulte [MFA for Office 365 and MFA for Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) (MFA para Office 365 y MFA para Azure).

## Privilegios limitados en tiempo

Algunas organizaciones pueden encontrarse con que tienen demasiados usuarios en roles con privilegios elevados. Un usuario podría haberse agregado al rol de una determinada actividad, por ejemplo, registrarse en un servicio, pero luego no usar esos privilegios con frecuencia.

Para reducir el tiempo de exposición de los privilegios y aumentar la visibilidad de su uso, limite a los usuarios a tomar esos privilegios Just in Time (JIT), cuando necesiten realizar una tarea. Para Azure Active Directory y Microsoft Online Services, puede usar [Privileged Identity Management (PIM) de Azure AD](http://aka.ms/AzurePIM).


![Panel de PIM][2]


## Detección de ataques

[Azure Active Directory Identity Protection](active-directory-identityprotection.d) proporciona una vista consolidada en eventos de riesgo y puntos vulnerables que afectan a las identidades de su organización. En función de los eventos de riesgo, Identity Protection calcula un nivel de riesgo del usuario para cada usuario, lo que le permite configurar las directivas basadas en riesgos para proteger automáticamente las identidades de su organización. Estas directivas, junto con otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden bloquear automáticamente al usuario u ofrecer sugerencias que incluyen restablecimientos de contraseña y cumplimiento de la autenticación multifactor.

![Azure AD Identity Protection][3]

## Acceso condicional

Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que elige al autenticar a un usuario, antes de permitirle el acceso a una aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación.


![Configuración de reglas de acceso condicional con MFA][4]


## Modelo de rol

Para reducir los riesgos para la seguridad, debe revisar el modelo de roles y delegación para acoger a los roles administrativos de servicios en la nube e incorporar la facilidad de uso por el administrador como principio clave. Este modelo debe aprovechar las funcionalidades Just in Time.

## Pasos siguientes

- Habilitar [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Habilitar [Privileged Identity Management de Azure AD](active-directory-privileged-identity-management-configure.md)
- Habilitar [Azure AD Identity Protection](active-directory-identityprotection.md)
- Habilitar los [controles de acceso condicional](active-directory-conditional-access.md)


Para más información sobre la creación de un plan de seguridad completo, consulte la sección sobre responsabilidades del cliente y plan de acción en el documento [Microsoft Cloud Security for Enterprise Architects](http://aka.ms/securecustomer) (Seguridad de Microsoft Cloud para los arquitectos de empresa). Para más información sobre la implicación de los servicios de Microsoft para ayudar con alguno de estos temas, póngase en contacto con su representante de Microsoft o visite nuestra [página de soluciones de ciberseguridad](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-identityprotection/29.png
[4]: ./media/active-directory-conditional-access/conditionalaccess-saas-apps.png

<!---HONumber=AcomDC_0420_2016-->