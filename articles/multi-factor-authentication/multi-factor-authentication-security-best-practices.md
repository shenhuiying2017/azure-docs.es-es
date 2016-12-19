---
title: "Prácticas recomendadas de seguridad para usar Azure Multi-Factor Authentication con cuentas de Azure AD | Microsoft Docs"
description: "Este documento proporciona las prácticas recomendadas de uso de Azure MFA con cuentas de Azure"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2471a8daa91113c1865507239361d093f8695e30


---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Prácticas recomendadas de seguridad para usar Azure Multi-Factor Authentication con cuentas de Azure AD
Cuando se trata de mejorar el proceso de autenticación, la verificación en dos pasos es la opción preferida por la mayoría de las organizaciones. Azure Multi-Factor Authentication (MFA) permite a las empresas cumplir sus requisitos de seguridad y cumplimiento al tiempo que proporciona una experiencia de inicio de sesión simple para sus usuarios. En este artículo se abordan algunos procedimientos recomendados que conviene tener en cuenta al planear la adopción de Azure MFA.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Prácticas recomendadas para Azure Multi-Factor Authentication en la nube
Para proporcionar a todos los usuarios la verificación en dos pasos y aprovechar las ventajas de las características extendidas que ofrece Azure MFA, debe habilitar Azure MFA para todos los usuarios.  Esto se logra con una de las siguientes implementaciones:

* Azure AD Premium o Enterprise Mobility Suite
* Proveedor de Multi-Factor Authentication

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure AD Premium/Enterprise Mobility Suite
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

El primer paso para adoptar Azure MFA en la nube con Azure AD Premium o Enterprise Mobility Suite es asignar licencias a los usuarios.  Azure Multi-Factor Authentication forma parte de estos conjuntos de aplicaciones y, como tal, su organización no necesita nada adicional para ampliar la funcionalidad de verificación en dos pasos a todos los usuarios. Solo tiene que asignar una licencia a un usuario y, luego, puede activar MFA.

Al configurar Multi-Factor Authentication, tenga en cuenta lo siguiente:

* No necesita crear un Proveedor de Multi-Factor Authentication.  Tanto Azure AD Premium como Enterprise Mobility Suite incluyen Azure Multi-Factor Authentication.  
* Azure AD Connect solo es un requisito si va a sincronizar su entorno de Active Directory local con un directorio de Azure AD. Si usa un directorio de Azure AD que no está sincronizado con una instancia local de Active Directory, no necesita Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Proveedor de Multi-Factor Authentication
![Proveedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si no tiene Azure AD Premium o Enterprise Mobility Suite, el primer paso para adoptar Azure MFA en la nube es crear un Proveedor de Multi-Factor Authentication. Aunque MFA está disponible de forma predeterminada para los administradores globales con Azure Active Directory, al implementar MFA para su organización debe ampliar la funcionalidad de verificación en dos pasos a todos los usuarios. Para ello, necesita un Proveedor de Multi-Factor Authentication.
Al seleccionar el Proveedor de Multi-Factor Authentication, debe seleccionar un directorio y tener en cuenta lo siguiente:

* No es necesario tener un directorio de Azure AD para crear un Proveedor de autenticación multifactor. Pero debe asociar el Proveedor de Multi-Factor Authentication a un directorio de Azure AD si quiere disponer de la siguiente funcionalidad:  
  * Ampliar la verificación en dos pasos a todos los usuarios  
  * Ofrezca a los administradores globales otras características tales como el portal de administración, saludos personalizados e informes.
* DirSync o Sincronización de AAD solo se necesitan si va a sincronizar su entorno local de Active Directory con un directorio de Azure AD. Si utiliza un directorio de Azure AD que no está sincronizado con una instancia local de Active Directory, no necesita DirSync ni Sincronización de AAD.
* Asegúrese de elegir el modelo de uso correcto para su empresa (por autenticación o por usuario habilitado). Una vez seleccionado el modelo de uso, no podrá cambiarlo.
  * Por autenticación: le cobra por cada verificación. Use este modelo si quiere verificación en dos pasos para cualquier persona que acceda a una aplicación determinada, pero no para usuarios específicos.
  * Por usuario habilitado: le cobra por cada usuario que se habilita para Azure MFA. Use este modelo si tiene algunos usuarios con licencias de Enterprise Mobility Suite o Azure AD Premium y otros sin ellas.

### <a name="user-account"></a>Cuenta de usuario
Al habilitar MFA para los usuarios, las cuentas de usuario pueden estar en uno de tres estados principales: deshabilitado, habilitado o forzado.
Use las instrucciones siguientes para asegurarse de usar la opción más adecuada para su implementación:

* Cuando el estado del usuario se establece en **deshabilitado**, dicho usuario no ejecuta la verificación de dos pasos. Este es el estado predeterminado.
* Cuando el estado del usuario se establece en **habilitado**, significa que el usuario está habilitado, pero no ha completado el proceso de registro. Se le pedirá que complete el proceso en el inicio de sesión siguiente. Esta configuración no afecta a las aplicaciones que no son de explorador. Todas las aplicaciones siguen funcionando hasta que se complete el proceso de registro.
* Cuando el estado del usuario se establece en **forzado**, significa que el usuario puede haber completado el registro o no. Si ha completado el proceso de registro, ejecuta la verificación en dos pasos. De lo contrario, se le pedirá que complete el proceso de registro en el inicio de sesión siguiente. Esta configuración sí afecta a las aplicaciones que no son de explorador. Estas aplicaciones no funcionarán hasta que se creen y usen contraseñas de aplicación.

Use la plantilla User Notification disponible en el artículo [Introducción a Azure Multi-Factor Authentication en la nube](multi-factor-authentication-get-started-cloud.md) para enviar un correo electrónico a los usuarios en relación a la adopción de MFA.

### <a name="supportability"></a>Compatibilidad
Puesto que la mayoría de los usuarios están acostumbrados a usar solo las contraseñas para autenticar, es importante que su compañía de a conocer este proceso a todos los usuarios. Este conocimiento puede reducir la probabilidad de que los usuarios llamen al departamento de soporte técnico para problemas poco importantes relacionados con MFA.
Sin embargo, hay algunos escenarios en los que es necesario deshabilitar temporalmente MFA. Use las instrucciones siguientes para entender cómo administrar estos escenarios:

* Asegúrese de que el personal de soporte técnico está capacitado para administrar escenarios en los que la aplicación móvil o el teléfono no reciben una notificación o una llamada de teléfono y por ese motivo el usuario no puede iniciar sesión. Soporte técnico puede habilitar la opción de omisión por única vez para permitir a un usuario autenticarse una sola vez omitiendo la verificación de dos pasos. La omisión es temporal y expira una vez que ha pasado el número especificado de segundos.
* Si es necesario, puede aprovechar la capacidad de direcciones IP de confianza de Azure MFA. Esta característica ofrece a los administradores de un inquilino administrado o federado la posibilidad de omitir la verificación de dos pasos para usuarios que inicien sesión desde la intranet local de la compañía. Las características están disponibles para los inquilinos de Azure AD que tienen licencias de Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.

## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Prácticas recomendadas para Azure Multi-Factor Authentication local
Si la compañía ha decidido aprovechar su propia infraestructura para habilitar MFA, es necesario implementar un servidor local de Azure Multi-Factor Authentication. En el diagrama siguiente se muestran los componentes del servidor MFA:

![Proveedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/server.png) 
\*No se instala de forma predeterminada \**Instalado pero no habilitado de forma predeterminada

El servidor de Azure Multi-Factor Authentication puede usarse para proteger recursos en la nube y locales a los que se tiene acceso mediante las cuentas de Azure AD. Pero esto solo puede lograrse mediante la federación.  Es decir, debe tener AD FS y tenerlo federado con el inquilino de Azure AD.
Al configurar el servidor de Multi-Factor Authentication, tenga en cuenta lo siguiente:

* Si está protegiendo recursos de Azure AD mediante Servicios de federación de Active Directory (AD FS), el primer factor de autenticación se realiza localmente mediante AD FS. El segundo factor se realiza localmente respondiendo a la notificación.
* No es necesario que el Servidor Azure Multi-Factor Authentication esté instalado en el servidor de federación de AD FS. Pero el adaptador de Multi-Factor Authentication para AD FS debe estar instalado en un equipo con Windows Server 2012 R2 en el que se ejecute AD FS. Puede instalar el servidor en un equipo diferente, siempre y cuando sea una versión compatible, e instalar el adaptador de AD FS por separado en el servidor de federación de AD FS. Consulte el procedimiento siguiente para obtener instrucciones acerca de cómo instalar el adaptador por separado.
* El asistente para la instalación del adaptador de AD FS de Multi-Factor Authentication crea un grupo de seguridad denominado PhoneFactor Admins en la instancia de Active Directory y luego agrega la cuenta de servicio de AD FS a este grupo. Se recomienda que compruebe en el controlador de dominio que se ha creado realmente el grupo PhoneFactor Admins y que la cuenta de servicio de AD FS es miembro de este grupo. Si es necesario, agregue la cuenta de servicio de AD FS manualmente al grupo PhoneFactor Admins en el controlador de dominio.

### <a name="user-portal"></a>Portal de usuario
Este portal se ejecuta en un sitio web de Internet Information Server (IIS), que permite capacidades de autoservicio y proporciona un conjunto completo de capacidades de administración de usuarios. Use las instrucciones siguientes para configurar este componente:

* Se requiere IIS 6 o superior
* ASP.NET v2.0.507207 debe estar instalado y registrado
* Este servidor se puede implementar en una red perimetral.

### <a name="app-passwords"></a>Contraseñas de aplicación
Si su organización está federada mediante SSO con Azure AD y va a usar Azure MFA, tenga en cuenta lo siguiente al usar las contraseñas de aplicación (recuerde que esto solo se aplica cuando se usa la federación (SSO)):

* La contraseña de aplicación se comprueba mediante Azure AD y, por tanto, omite la federación. La federación solo se usa activamente al configurar la contraseña de aplicación.
* Para los usuarios federados (SSO), las contraseñas se almacenarán en el identificador de organización. Si el usuario abandona la empresa, esta información tiene que fluir al identificador de la organización usando DirSync en tiempo real. La deshabilitación o eliminación de la cuenta puede tardar hasta tres horas en sincronizarse, retrasando la deshabilitación o eliminación de contraseña de aplicación en Azure AD.
* La configuración del control de acceso de cliente local no admite la contraseña de aplicación
* No hay ningún registro o capacidad de auditoría en la autenticación local que esté disponible para la contraseña de aplicación
* Es necesaria una  mayor formación del usuario final para el cliente de Microsoft Lync 2013.
* Puede que algunos diseños de arquitectura avanzada requieran el uso de una combinación de nombre de usuario y contraseñas de la organización y de contraseñas de aplicación cuando se usa la verificación de dos pasos con clientes, en función de dónde se autentiquen. Para los clientes que se autentican en una infraestructura local, usaría un nombre de usuario y una contraseña de la organización. Para los clientes que se autentican en Azure AD, usaría la contraseña de aplicación.
* De forma predeterminada, los usuarios no pueden crear contraseñas de aplicación. Si su compañía lo requiere o si necesita que los usuarios puedan crear una contraseña de aplicación en algunos escenarios, asegúrese de que está seleccionada la opción **Permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador**.

## <a name="additional-considerations"></a>Consideraciones adicionales
Use la lista siguiente para conocer algunas consideraciones adicionales y procedimientos recomendados para cada componente que se implementará de forma local:

| Método | Description |
|:--- |:--- |
| [Servicio de federación de Active Directory](multi-factor-authentication-get-started-adfs.md) |Información sobre cómo configurar la Azure Multi-Factor Authentication con AD FS. |
| [Autenticación RADIUS](multi-factor-authentication-get-started-server-radius.md) |Información sobre la instalación y configuración del servidor de MFA de Azure con RADIUS. |
| [Autenticación de IIS](multi-factor-authentication-get-started-server-iis.md) |Información sobre la instalación y configuración del servidor de MFA de Azure con IIS. |
| [Autenticación de Windows](multi-factor-authentication-get-started-server-windows.md) |Información sobre la instalación y configuración del servidor de MFA de Azure con la autenticación de Windows. |
| [Autenticación LDAP](multi-factor-authentication-get-started-server-ldap.md) |Información sobre la instalación y configuración del servidor de MFA de Azure con la autenticación LDAP. |
| [Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS](multi-factor-authentication-get-started-server-rdg.md) |Información sobre la instalación y configuración del servidor de MFA de Azure con la puerta de enlace de escritorio remoto mediante RADIUS. |
| [Sincronización con Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md) |Información sobre la instalación y configuración de la sincronización entre Active Directory y el servidor de MFA de Azure. |
| [Implementación del servicio web móvil de la aplicación móvil del servidor de Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md) |Información sobre la instalación y configuración del servicio web del servidor de Azure MFA. |
| [Configuración avanzada de VPN con Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) |Información acerca de la configuración de dispositivos Cisco ASA, Citrix Netscaler y Juniper/Pulse Secure VPN mediante LDAP o RADIUS. |

## <a name="additional-resources"></a>Recursos adicionales
Si bien este artículo resalta algunas prácticas recomendadas para Azure MFA, existen otros recursos que también puede usar al planear la implementación de MFA. La lista siguiente tiene algunos artículos clave que pueden ayudarle durante este proceso:

* [Informes en Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
* [La experiencia de configuración para Azure Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
* [P+F sobre Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)




<!--HONumber=Nov16_HO3-->


