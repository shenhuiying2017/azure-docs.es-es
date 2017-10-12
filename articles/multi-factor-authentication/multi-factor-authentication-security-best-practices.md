---
title: "Prácticas recomendadas de seguridad para usar Azure Multi-Factor Authentication con cuentas de Azure AD | Microsoft Docs"
description: "Este documento proporciona las prácticas recomendadas de uso de Azure MFA con cuentas de Azure"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: f43f6e33976325920da9cf0f6aef6decae5bde26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Prácticas recomendadas de seguridad para usar Azure Multi-Factor Authentication con cuentas de Azure AD

Para la mayoría de las organizaciones que desean mejorar su proceso de autenticación, la opción preferida es la verificación en dos pasos. Azure Multi-Factor Authentication (MFA) ayuda a las empresas a cumplir sus requisitos de seguridad y cumplimiento al tiempo que proporciona una experiencia de inicio de sesión sencilla para sus usuarios. En este artículo se abordan algunas sugerencias que conviene tener en cuenta al planear la adopción de Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Implementación de Azure MFA en la nube

Existen dos maneras de habilitar Azure MFA para todos los usuarios.

* Comprar licencias para cada usuario (Azure MFA, Azure AD Premium o Enterprise Mobility + Security)
* Crear un Proveedor de Multi-Factor Auth y pagar por usuario o por autenticación

### <a name="licenses"></a>Licencias
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Si dispone de licencias de Azure AD Premium o Enterprise Mobility + Security, ya tiene Azure MFA. Su organización no necesita nada más para extender la funcionalidad de verificación en dos pasos a todos los usuarios. Solo tiene que asignar una licencia a un usuario y, luego, puede activar MFA.

Al configurar Multi-Factor Authentication, tenga en cuenta las siguientes sugerencias:

* No cree un Proveedor de Multi-Factor Auth por autenticación. Si lo hace, puede acabar pagando por solicitudes de comprobación de usuarios que ya tienen licencias.
* Si no tiene suficientes licencias para todos los usuarios, puede crear un Proveedor de Multi-Factor Auth por usuario para cubrir el resto de su organización. 
* Azure AD Connect solo es un requisito si va a sincronizar su entorno de Active Directory local con un directorio de Azure AD. Si usa un directorio de Azure AD que no está sincronizado con una instancia local de Active Directory, no necesita Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Proveedor de Multi-Factor Authentication
![Proveedor de Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si no tiene licencias que incluyan Azure MFA, puede crear un proveedor de autenticación MFA. 

Al crear el proveedor de autenticación, debe seleccionar un directorio y tener en cuenta los siguientes detalles:

* No es necesario un directorio de Azure AD para crear un proveedor de autenticación multifactor, pero obtendrá una mayor funcionalidad con uno. Cuando asocia el proveedor de autenticación con un directorio de Azure AD, están habilitadas las siguientes características:  
  * Ampliar la verificación en dos pasos a todos los usuarios  
  * Ofrezca a los administradores globales otras características tales como el portal de administración, saludos personalizados e informes.
* Se necesita DirSync o Sincronización de AAD si va sincroniza el entorno de Active Directory local con un directorio de Azure AD. Si utiliza un directorio de Azure AD que no está sincronizado con una instancia local de Active Directory, no necesita DirSync ni Sincronización de AAD.
* Elija el modelo de consumo que mejor se adapte a su negocio. Una vez seleccionado el modelo de uso, no podrá cambiarlo. Los dos modelos son:
  * Por autenticación: se le cobra por cada comprobación. Use este modelo si quiere verificación en dos pasos para cualquier persona que acceda a una aplicación determinada, pero no para usuarios específicos.
  * Por usuario habilitado: le cobra por cada usuario que se habilita para Azure MFA. Use este modelo si tiene algunos usuarios con licencias de Enterprise Mobility Suite o Azure AD Premium y otros sin ellas.

### <a name="supportability"></a>Compatibilidad
Puesto que la mayoría de los usuarios están acostumbrados a usar solo las contraseñas para autenticar, es importante que su compañía de a conocer este proceso a todos los usuarios. Este conocimiento puede reducir la probabilidad de que los usuarios llamen al departamento de soporte técnico para problemas poco importantes relacionados con MFA. Sin embargo, hay algunos escenarios en los que es necesario deshabilitar temporalmente MFA. Use las instrucciones siguientes para entender cómo administrar estos escenarios:

* Forme al personal de soporte técnico para administrar escenarios en los que el usuario no pueda iniciar sesión porque la aplicación móvil o el teléfono no reciben una notificación o una llamada de teléfono. El soporte técnico puede [habilitar la opción de omisión por única vez](multi-factor-authentication-whats-next.md#one-time-bypass) para permitir a un usuario autenticarse una sola vez omitiendo la verificación en dos pasos. La omisión es temporal y expira una vez que ha pasado el número especificado de segundos.
* Considere la [funcionalidad de IP de confianza](multi-factor-authentication-whats-next.md#trusted-ips) en Azure MFA como una forma de reducir la verificación en dos pasos. Esta característica ofrece a los administradores de un inquilino administrado o federado la posibilidad de omitir la verificación en dos pasos para usuarios que inicien sesión desde la intranet local de la compañía. Las características están disponibles para los inquilinos de Azure AD que tienen licencias de Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Procedimientos recomendados para una implementación local
Si la compañía ha decidido aprovechar su propia infraestructura para habilitar MFA, es necesario implementar una instancia local de Servidor Azure Multi-Factor Authentication. En el diagrama siguiente se muestran los componentes del servidor MFA:

![Componentes de servidor MFA predeterminados: consola, motor de sincronización, portal de administración, servicio en la nube](./media/multi-factor-authentication-security-best-practices/server.png)\*No se instalan de forma predeterminada \**Están instalados pero no habilitados de forma predeterminada

El servidor Azure Multi-Factor Authentication puede proteger los recursos en la nube y los recursos locales mediante la federación. Debe disponer de AD FS y federarlo con el inquilino de Azure AD.
Al configurar el servidor de Multi-Factor Authentication, tenga en cuenta los siguientes detalles:

* Si va a proteger recursos de Azure AD mediante Servicios de federación de Active Directory (AD FS), el primer paso de la verificación se realiza localmente mediante AD FS. El segundo paso es realiza localmente respondiendo a la notificación.
* No es necesario instalar Servidor Azure Multi-Factor Authentication en el servicio de federación de AD FS. Pero el adaptador de Multi-Factor Authentication para AD FS debe estar instalado en un equipo con Windows Server 2012 R2 en el que se ejecute AD FS. Puede instalar el servidor en un equipo diferente, siempre y cuando sea una versión compatible, e instalar el adaptador de AD FS por separado en el servidor de federación de AD FS. 
* El asistente para la instalación del adaptador de AD FS de Multi-Factor Authentication crea un grupo de seguridad denominado PhoneFactor Admins en la instancia de Active Directory y luego agrega la cuenta de servicio de AD FS a este grupo. Compruebe que el grupo PhoneFactor Admins se haya creado en el controlador de dominio y que la cuenta de servicio de AD FS sea miembro de este grupo. Si es necesario, agregue la cuenta de servicio de AD FS manualmente al grupo PhoneFactor Admins en el controlador de dominio.

### <a name="user-portal"></a>Portal de usuario
El portal de usuario permite capacidades de autoservicio y proporciona un conjunto completo de capacidades de administración de usuarios. Se ejecuta en un sitio web de Internet Information Server (IIS). Use las instrucciones siguientes para configurar este componente:

* Use IIS 6 o superior.
* Instale y registre ASP.NET v2.0.507207.
* Asegúrese de que este servidor se pueda implementar en una red perimetral.

### <a name="app-passwords"></a>Contraseñas de aplicación
Si su organización está federada para SSO con Azure AD y que va a usar Azure MFA, tenga en cuenta los siguientes detalles:

* Azure AD comprueba la contraseña de aplicación y, por tanto, omite la federación. La federación solo se usa activamente al configurar contraseñas de aplicación.
* Para los usuarios federados (SSO), las contraseñas se almacenan en el identificador de organización. Si el usuario abandona la empresa, esta información tiene que fluir al identificador de la organización con DirSync. La deshabilitación o eliminación de la cuenta puede tardar hasta tres horas en sincronizarse, lo que retrasa la deshabilitación o eliminación de las contraseñas de aplicación en Azure AD.
* La configuración del control de acceso de cliente local no admite la contraseña de aplicación
* No hay ningún registro o funcionalidad de auditoría en la autenticación local que esté disponible para contraseñas de aplicación.
* Puede que algunos diseños de arquitectura avanzada requieran el uso de una combinación de nombre de usuario y contraseñas de la organización y de contraseñas de aplicación cuando se usa la verificación de dos pasos con clientes, en función de dónde se autentiquen. Para los clientes que se autentican en una infraestructura local, usaría un nombre de usuario y una contraseña de la organización. Para los clientes que se autentican en Azure AD, usaría la contraseña de aplicación.
* De forma predeterminada, los usuarios no pueden crear contraseñas de aplicación. Si quiere permitir que los usuarios creen contraseñas de aplicación, seleccione la opción **Permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador**.

## <a name="additional-considerations"></a>Consideraciones adicionales
Use esta lista para conocer algunas consideraciones adicionales y orientaciones para cada componente que se implementa de forma local:

- Instalación de Azure Multi-Factor Authentication con el [Servicio de federación de Active Directory](multi-factor-authentication-get-started-adfs.md).
- Instalación y configuración del servidor de Azure MFA con [Autenticación RADIUS](multi-factor-authentication-get-started-server-radius.md).
- Instalación y configuración del servidor de Azure MFA con [Autenticación de IIS](multi-factor-authentication-get-started-server-iis.md).
- Instalación y configuración del servidor de Azure MFA con [Autenticación de Windows](multi-factor-authentication-get-started-server-windows.md).
- Instalación y configuración del servidor de Azure MFA con [Autenticación de LDAP](multi-factor-authentication-get-started-server-ldap.md).
- Instalación y configuración del servidor de Azure MFA con [Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS](multi-factor-authentication-get-started-server-rdg.md).
- Instalación y configuración de la sincronización entre el servidor de Azure MFA y [Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md).
- [Implementación del servicio web de aplicaciones móviles del Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md).
- [Configuración avanzada de VPN con Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) para aparatos VPN de Cisco ASA, Citrix Netscaler, y Juniper/Pulse Secure mediante LDAP o RADIUS.

## <a name="next-steps"></a>Pasos siguientes
Si bien este artículo resalta algunas prácticas recomendadas para Azure MFA, existen otros recursos que también puede usar al planear la implementación de MFA. La lista siguiente tiene algunos artículos clave que pueden ayudarle durante este proceso:

* [Informes en Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
* [La experiencia de registro de verificación en dos pasos](multi-factor-authentication-end-user-first-time.md)
* [P+F sobre Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

