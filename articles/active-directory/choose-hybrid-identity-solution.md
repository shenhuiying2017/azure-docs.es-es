---
title: "Elección de una solución de identidad híbrida de Azure | Microsoft Docs"
description: "Obtenga un conocimiento básico de las soluciones y recomendaciones de identidades híbridas disponibles para tomar la mejor decisión de gobierno de las identidades para la organización."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/5/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 5838e3276765f4f074bca2e3cae81b17edfa7c69
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="microsoft-hybrid-identity-solutions"></a>Soluciones de identidad híbrida de Microsoft
Las soluciones de identidad híbrida de [Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) permiten sincronizar objetos de directorio locales con Azure AD mientras administra los usuarios locales. La primera decisión que hay que tomar cuando se planea sincronizar la instancia local de Windows Server Active Directory con Azure AD es si desea usar una identidad sincronizada o identidad federada. Las identidades sincronizadas, y, opcionalmente, los hashes de contraseña, permiten a los usuarios usar la misma contraseña para acceder tanto a los recursos organizacionales locales como los basados en la nube. Para unos requisitos de escenario más avanzados, como el inicio de sesión único (SSO) o MFA local, debe implementar Servicios de federación de Active Directory (AD FS) para federar identidades. 

Hay varias opciones disponibles para configurar la identidad híbrida. En este artículo se proporciona información que le ayuda a elegir la opción más idónea para su organización en función de la facilidad de implementación y las necesidades específicas de administración de acceso e identidad. Mientras piensa en el modelo de identidad que mejor se adapte a las necesidades de su organización, debe pensar también en el tiempo, la infraestructura existente, la complejidad y el costo. Estos factores son diferentes para cada organización y pueden cambiar con el tiempo. Sin embargo, si cambian los requisitos, también cuenta con la flexibilidad para cambiar a otro modelo de identidad.

> [!TIP]
> [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) entrega todas estas opciones.

## <a name="synchronized-identity"></a>Identidad sincronizada 
La identidad sincronizada es la manera más sencilla de sincronizar objetos de directorio locales (usuarios y grupos) con Azure AD. 

![Identidad híbrida sincronizada](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Aunque la identidad sincronizada es el método más fácil y rápido, los usuarios todavía tienen que mantener una contraseña independiente para los recursos basados en la nube. Para evitar esto, también puede (opcionalmente) [sincronizar un hash de contraseñas de usuario](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) con su directorio Azure AD. La sincronización de hashes de contraseña permite a los usuarios iniciar sesión en recursos organizativos basados en la nube con el mismo nombre de usuario y contraseña que usan en local. Azure AD Connect busca periódicamente en el directorio local los cambios y mantiene sincronizado el directorio Azure AD. Cuando se cambia un atributo de usuario o una contraseña en Active Directory local, se actualiza automáticamente en Azure AD. 

Para la mayoría de las organizaciones que solo tienen que habilitar a sus usuarios para iniciar sesión en Office 365, aplicaciones SaaS y otros recursos basados en Azure AD, se recomienda la opción de sincronización de contraseña predeterminada. Si esto no funciona, deberá decidir entre la autenticación de paso a través y AD FS.

> [!TIP]
> Las contraseñas de usuario se almacenan en Windows Server Active Directory local en forma de un valor hash que representa la contraseña real del usuario. Un valor hash es el resultado de una función matemática unidireccional (el algoritmo hash). No hay ningún método para volver del resultado de una función unidireccional a la versión de texto sin formato de una contraseña. El hash de contraseña no puede usarse para iniciar sesión en la red local. Si decide sincronizar contraseñas, Azure AD Connect extrae los hashes de contraseña de la instancia local de Active Directory y aplica un procesamiento de seguridad adicional al hash de contraseña antes de sincronizarlo con Azure AD. También se puede usar la sincronización de contraseñas junto con la escritura diferida de contraseñas para habilitar el autoservicio de restablecimiento de contraseña en Azure AD. Además, puede habilitar el inicio de sesión único (SSO) para usuarios en equipos unidos a un dominio que están conectados a la red corporativa. Con el inicio de sesión único, los usuarios habilitados solo necesitan escribir un nombre de usuario para acceder a los recursos de nube de manera segura. 

## <a name="pass-through-authentication"></a>Autenticación de paso a través
La [autenticación de paso a través de Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) proporciona una sencilla solución de validación de contraseñas para servicios basados en Azure AD mediante su instancia de Active Directory local. Si las directivas de seguridad y cumplimiento de su organización no permiten el envío de contraseñas de los usuarios, incluso en un formulario con algoritmo hash, y solo hay que admitir el SSO de escritorio para dispositivos unidos a un dominio, se recomienda que lo evalúe mediante la autenticación de paso a través. La autenticación de paso a través no requiere ninguna implementación en la red perimetral, lo que simplifica la infraestructura de implementación cuando se compara con AD FS. Cuando los usuarios inician sesión con Azure AD, este método de autenticación valida las contraseñas de los usuarios directamente en la instancia de Active Directory local.

![Autenticación de paso a través](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Con la autenticación de paso a través, no es necesario una infraestructura de red compleja ni tampoco almacenar contraseñas locales en la nube. Combinada con el inicio de sesión único, la autenticación de paso a través proporciona una experiencia realmente integrada cuando se inicie sesión en Azure AD o en otros servicios en la nube.

La autenticación de paso a través se configura mediante Azure AD Connect y utiliza un agente local simple que escucha las solicitudes de validación de contraseña. El agente se puede implementar fácilmente en varios equipos para proporcionar una alta disponibilidad y equilibrio de carga. Puesto que todas las comunicaciones son exclusivamente salientes, no es necesario que el conector se instale en una red perimetral. Los requisitos del equipo servidor para el conector son los siguientes:

- Windows Server 2012 R2 o superior
- Unión a un dominio en el bosque en el que se validan los usuarios

> [!NOTE]
> La autenticación de paso a través de Azure AD está actualmente en versión preliminar y es compatible para los clientes basados en explorador web y clientes de Office que admiten la autenticación moderna. Para los clientes no compatibles, como los clientes de Office o de Exchange Active Sync heredados (incluso clientes de correo electrónico nativos en dispositivos móviles), se recomienda usar el equivalente de autenticación moderno. La autenticación moderna no solo permite la autenticación de paso a través, sino también que se apliquen directivas de acceso condicional, como la autenticación multifactor. 

Actualmente no se admite la autenticación de paso a través para clientes que usen dispositivos Windows 10 unidos a Azure AD. Sin embargo, puede utilizar la sincronización de hash de contraseña como una reserva automática para admitir Windows 10 y los clientes heredados mencionados anteriormente. Durante la versión preliminar, se habilita la sincronización de hash de contraseña de forma predeterminada cuando se selecciona la autenticación de paso a través como opción de inicio de sesión en Azure AD Connect.


## <a name="federated-identity-ad-fs"></a>Identidad federada (AD FS)
Para tener más control sobre cómo los usuarios acceden a Office 365 y otros servicios en la nube, puede configurar la sincronización de directorios con el inicio de sesión único (SSO) mediante [Servicios de federación de Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). La federación de los inicios de sesión del usuario con AD FS delega la autenticación a un servidor local que valida las credenciales del usuario. En este modelo, las credenciales de Active Directory local nunca se pasan a Azure AD.

![Identidad federada](./media/choose-hybrid-identity-solution/federated-identity.png)

También llamado federación de identidades, este método garantiza que toda la autenticación de usuario está controlada en local y permite a los administradores implementar niveles más rigurosos de control de acceso. La federación de identidades con AD FS es la opción más complicada y requiere la implementación de servidores adicionales en su entorno local. La federación de identidades también permite confirmar el soporte técnico ininterrumpido para su infraestructura de Active Directory y AD FS. Es necesario este elevado nivel de soporte técnico porque si el acceso de Internet local, el controlador de dominio o los servidores de AD FS no están disponibles, los usuarios no pueden iniciar sesión en servicios en la nube.

> [!TIP]
> Si opta por usar Federación con Servicios de federación de Active Directory (AD FS), tiene la posibilidad de configurar la sincronización de contraseña como copia de seguridad en caso de error en la infraestructura de AD FS.


## <a name="common-scenarios-and-recommendations"></a>Escenarios comunes y recomendaciones
Estos son algunos escenarios comunes de identidad híbrida y administración de acceso con recomendaciones sobre la opción (u opciones) de identidad que podría ser más adecuada.

|Necesitará:|PWS y SSO<sup>1</sup>| PTA y SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Sincronice nuevas cuentas de usuarios, contactos y grupos creadas en Active Directory local con la nube automáticamente.|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Configurar mi inquilino para escenarios híbridos de Office 365|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Permitir que mis usuarios inicien sesión en Cloud Services y accedan con su contraseña local|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementar el inicio de sesión único utilizando credenciales corporativas|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Asegurarse de que ningún hash de contraseña está almacenado en la nube| |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Habilitar soluciones locales de autenticación multifactor| | |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Admitir la autenticación de tarjeta inteligente para mis usuarios<sup>4</sup>| | |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Mostrar notificaciones de expiración de contraseña en el portal de Office y en el escritorio de Windows 10| | |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Sincronización de contraseñas con inicio de sesión único. 

> <sup>2</sup> Autenticación de paso a través e inicio de sesión único. 

> <sup>3</sup> Inicio de sesión único federado con AD FS.

> <sup>4</sup> AD FS se puede integrar con el PKI de la empresa para permitir el inicio de sesión con certificados. Estos certificados pueden ser certificados flexibles implementados a través de canales de aprovisionamiento de confianza, como certificados MDM o GPO o de tarjetas inteligentes (incluidas las tarjetas PIV/CAC) o Hello para empresas (cert-trust). Para más información sobre la compatibilidad con la autenticación de tarjetas inteligentes, consulte [este blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Pasos siguientes
[Información sobre el entorno de prueba de concepto de Azure](https://aka.ms/aad-poc)

[Instalación de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Supervisión de 'sincronización de identidades híbridas](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

