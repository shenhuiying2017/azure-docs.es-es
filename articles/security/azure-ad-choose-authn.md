---
title: Selección del método de autenticación adecuado para la solución de identidad híbrida de Azure AD | Microsoft Docs
description: Esta guía está pensada para ayudar a los Consejeros delegados (CEO), Directores de información (CIO), Directores de seguridad de la información (CISO), Arquitectos jefe de identidad, Arquitectos de empresa y responsables de la toma de decisiones de TI a elegir un método de autenticación para su solución de identidad híbrida de Azure en organizaciones de tamaño medio y grande.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: eb5fb008e602c2026e57d3436875ec485b350af8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895527"
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Selección del método de autenticación adecuado para su solución de identidad híbrida de Azure Active Directory 

Este artículo es el primero de una serie de artículos destinados a ayudar a las organizaciones a implementar una solución de identidad híbrida de Azure AD completa. Una solución de identidad híbrida de Azure AD completa se ha descrito como el Marco de transformación digital de identidad híbrida y abarca los resultados y objetivos empresariales en los que deben centrarse las organizaciones para tener la seguridad de que han implementado una solución de identidad híbrida sólida y segura. Los primeros resultados empresariales del marco señalan los requisitos de las organizaciones de proteger el proceso de autenticación cuando los usuarios acceden a las aplicaciones en la nube. El primer objetivo empresarial del resultado empresarial de autenticación segura es la posibilidad de que los usuarios inicien sesión en las aplicaciones en la nube mediante sus nombres de usuario y contraseñas locales. Este proceso de inicio de sesión y cómo los usuarios se autentican abre un mundo de posibilidades en la nube.

La elección del método de autenticación correcto es la primera preocupación para las organizaciones que desean mover sus aplicaciones a la nube. Esta decisión no debe tomarse a la ligera por las razones siguientes:

1. Es la primera decisión de una organización que quiere moverse a la nube. 

2. El método de autenticación es un componente esencial de la presencia de una organización en la nube, al controlar el acceso a todos los datos y recursos en ella.

3. Es la base de todas las demás características avanzadas de seguridad y experiencia de usuario en Azure AD.

4. Dificultad para cambiar el método de autenticación una vez implementado.

Al ser la identidad el nuevo plano de control de la seguridad de TI, la autenticación se convierte en el guardián de acceso de una organización al nuevo mundo de la nube. Las organizaciones deben asegurarse de que el plano de control de identidad refuerza su seguridad y mantiene a sus aplicaciones en la nube protegidas frente a los intrusos.

### <a name="out-of-scope"></a>Fuera de ámbito

Las organizaciones que no tienen una superficie de directorio local existente no entran en el ámbito de este artículo. Por lo general, esas empresas crean identidades solo en la nube, lo que no requiere una solución de identidad híbrida. Las identidades solo en la nube existen únicamente en la nube y no están asociadas a identidades locales correspondientes.  

## <a name="choosing-the-right-authentication-method"></a>Selección del método de autenticación adecuado

Con la solución de identidad híbrida de Azure AD como el nuevo plano de control, la autenticación es la base del acceso a la nube. La elección del método de autenticación correcto es una primera decisión fundamental en la configuración de una solución de identidad híbrida de Azure AD. La implementación del método de autenticación se configura mediante Azure AD Connect, que también aprovisiona a los usuarios en la nube. 

Para elegir un método de autenticación, es necesario tener en cuenta el tiempo, la infraestructura existente, la complejidad y el costo de implementar cada opción. Estos factores varían con cada organización y pueden cambiar con el tiempo. 

Azure AD admite los siguientes métodos de autenticación en soluciones de identidad híbrida:

### <a name="cloud-authentication"></a>Autenticación en la nube
Cuando se elige este método de autenticación, Azure AD administra el proceso de inicio de sesión de los usuarios. Junto con el inicio de sesión único (SSO) completo, los usuarios pueden iniciar sesión en las aplicaciones en la nube sin tener que volver a escribir sus credenciales. Con la autenticación en la nube puede elegir entre dos opciones: 

**Sincronización de hash de contraseñas (PBS)**: la manera más sencilla de habilitar la autenticación para los objetos de directorio local en Azure AD. La sincronización de hash de contraseñas permite a los usuarios usar el mismo nombre de usuario y contraseña que en el entorno local sin tener que implementar infraestructura adicional. Algunas características premium de Azure AD, como Identity Protection, requieren la sincronización de hash de contraseñas con independencia del método de autenticación seleccionado.

> [!NOTE] 
> Las contraseñas nunca se almacenan en texto no cifrado o cifradas con un algoritmo reversible en Azure AD. Para más información sobre el proceso real de sincronización de hash de contraseñas, consulte [Implementación de la sincronización de hash de contraseñas con la sincronización de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Autenticación de paso a través (PTA)**: esta autenticación proporciona una validación sencilla de contraseñas para servicios de autenticación de Azure AD que se vale de un agente de software que se ejecuta en uno o varios servidores locales para validar a los usuarios directamente con su instancia local de Active Directory, lo que garantiza que la validación de contraseñas no sucede en la nube. Las empresas con un requisito de seguridad para aplicar de inmediato los estados de cuenta de los usuarios locales, las directivas de contraseña y las horas de inicio de sesión usarían este método de autenticación. Para obtener más información sobre el proceso real de autenticación de paso a través, consulte [Inicio de sesión del usuario con la autenticación de paso a través de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Autenticación federada
Cuando se elige este método de autenticación, Azure AD deja el proceso de autenticación en manos de un sistema de autenticación de confianza, por ejemplo, en una instancia local de Servicios de federación de Active Directory (AD FS) para validar la contraseña del usuario. El sistema de autenticación puede proporcionar requisitos de autenticación adicionales, como la autenticación basada en tarjeta inteligente o una autenticación multifactor de terceros. Para más información, consulte [Implementación de Servicios de federación de Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

La siguiente sección le ayuda a decidir qué método de autenticación es más adecuado en su caso, mediante un árbol de decisión. Este árbol le ayuda a determinar si se debe implementar la autenticación en la nube o la federada para la solución híbrida de Azure AD.

## <a name="azure-ad-authentication-decision-tree"></a>Árbol de decisión de autenticación de Azure AD

![imagen1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Consideraciones detalladas sobre los métodos de autenticación

### <a name="cloud-authentication-password-hash-sync"></a>Autenticación en la nube: sincronización de hash de contraseñas 

* **Esfuerzo:** la sincronizados de hash de contraseñas requiere el menor esfuerzo con respecto a la implementación, el mantenimiento y la infraestructura en organizaciones que solo necesitan permitir que sus usuarios inicien sesión en Office 365, aplicaciones SaaS y otros recursos basados en Azure AD. Cuando se habilita, este método de autenticación forma parte del proceso de sincronización de Azure AD Connect y se ejecuta cada dos minutos. 

* **Experiencia del usuario:** se recomienda que las organizaciones implementen el inicio de sesión único (SSO) completo con la sincronización de hash de contraseñas con el fin de mejorar la experiencia de inicio de sesión del usuario, ya que se evitan mensajes innecesarios una vez que han iniciado la sesión.

* **Escenarios avanzados:** si las organizaciones lo eligen, es posible usar información detallada de identidades con informes de Azure AD Identity Protection, por ejemplo, el informe de credenciales filtradas. Windows Hello para empresas es otra opción que tiene [requisitos específicos cuando se usa la sincronización de hash de contraseñas](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). Las organizaciones que requieren la autenticación multifactor con sincronización de hash de contraseñas deben usar la autenticación multifactor de Azure AD y no pueden usar métodos de autenticación multifactor locales o de terceros.

* **Continuidad empresarial:** la sincronización de hash de contraseñas presenta una alta disponibilidad intrínseca como un servicio en la nube que se escala a todos los centros de datos de Microsoft. Se recomienda implementar un segundo servidor de Azure AD Connect en modo de almacenamiento provisional en una configuración de espera con fines de recuperación de desastres.

* **Consideraciones:** actualmente, la sincronización de hash de contraseñas no aplica inmediatamente los cambios en los estados de la cuenta local. En esta situación, un usuario tendrá acceso a las aplicaciones en la nube hasta que el estado de la cuenta del usuario se sincronice con Azure AD. Si las organizaciones desean superar esta limitación, se recomienda activar un nuevo ciclo de sincronización después de que los administradores realicen actualizaciones masivas en los estados de las cuentas de los usuarios locales, como deshabilitar las cuentas. 

> [!NOTE] 
> Los estados de contraseña expirada y cuenta bloqueada no se sincronizan actualmente con Azure AD mediante Azure AD Connect. 

Para ver los pasos de implementación, consulte [Implementación de la sincronización de hash de contraseñas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization).

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticación en la nube: autenticación de paso a través  

* **Esfuerzo:** en la autenticación de paso a través, es necesario tener instalados uno o varios (se recomiendan tres) agentes ligeros en los servidores existentes con acceso a Active Directory Domain Services, incluido el acceso a sus controladores de dominio locales de AD. Estos agentes necesitan acceso saliente a Internet y tener acceso a los controladores de dominio. Por esta razón, no se admite implementar los agentes en una red perimetral, porque se requiere acceso de red sin restricciones a los controladores de dominio. Todo el tráfico de red se cifra y se limita a las solicitudes de autenticación. Para obtener más información sobre este proceso, consulte [Información de seguridad detallada sobre la autenticación de paso a través](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive).

* **Experiencia del usuario:** se recomienda que las organizaciones implementen el inicio de sesión único de conexión directa con la autenticación de paso a través a fin de mejorar la experiencia de inicio de sesión del usuario, ya que se evitan mensajes innecesarios una vez que han iniciado la sesión.

* **Escenarios avanzados:** la autenticación de paso a través aplica la directiva de cuenta local al iniciar sesión. Por ejemplo, se deniega el acceso cuando el estado de la cuenta de un usuario local es deshabilitada, bloqueada, [contraseña expirada](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication ) o se encuentra fuera de las horas de inicio de sesión del usuario.  Las organizaciones que requieren la autenticación multifactor con autenticación de paso a través deben usar Azure Multi-Factor Authentication (MFA) y no pueden usar métodos de autenticación multifactor locales o de terceros. Características avanzadas, como el informe de credenciales filtradas de Identity Protection requieren que se implemente la sincronización de hash de contraseñas con independencia de si se elige la autenticación de paso a través.

* **Continuidad empresarial:** se recomienda implementar dos agentes de autenticación de paso a través adicionales, además del primer agente, en el servidor de Azure AD Connect para garantizar la alta disponibilidad de las solicitudes de autenticación. Cuando hay tres agentes implementados, un agente puede dejar de funcionar cuando otro agente está inactivo por mantenimiento. Otra ventaja de implementar la sincronización de hash de contraseñas además de la autenticación de paso a través, es que puede funcionar como método de autenticación de reserva cuando el método de autenticación principal deje de estar disponible; por ejemplo, cuando los servidores locales no están disponibles.

* **Consideraciones:** si usa la sincronizados de hash de contraseñas como método de autenticación de reserva para la autenticación de paso a través y los agentes no pueden validar las credenciales del usuario, entonces la conmutación por error a la sincronización de hash de contraseñas no sucede automáticamente. Deberá cambiar el método de inicio de sesión manualmente con Azure AD Connect. La autenticación de paso a través solo es compatible con las aplicaciones en la nube que usan la autenticación moderna y protocolos específicos de Exchange Online como ActiveSync, POP3 e IMAP4. Por ejemplo, Microsoft Office 2013 y posterior admite la autenticación moderna, pero no versiones anteriores. Para más información sobre la compatibilidad de aplicaciones de Office, consulte esta [entrada de blog](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Para otras consideraciones sobre la autenticación de paso a través, incluida la compatibilidad con identificadores alternativos, consulte las [preguntas frecuentes](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Para obtener información sobre los pasos de implementación, consulte [Implementación de la autenticación de paso a través](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Federated Authentication

* **Esfuerzo:** el uso de un sistema de autenticación federada se basa en un sistema externo para autenticar a los usuarios. Algunas empresas desean reutilizar sus inversiones existentes en sistemas federados con su solución de identidad híbrida de Azure AD. El mantenimiento y la administración del sistema federado no entra en el control de Azure AD. Es responsabilidad de la organización que usa el sistema federado asegurarse de que se implementa de forma segura y puede administrar la carga de autenticación. 

* **Experiencia del usuario:** la experiencia de usuario de la autenticación federada depende de la implementación de las características, la topología y la configuración de la granja de servidores de federación. Algunas organizaciones requieren esta flexibilidad para adaptar y configurar el acceso a la granja de servidores de federación a fin de satisfacer sus requisitos de seguridad. Por ejemplo, es posible configurar usuarios y dispositivos conectados internamente para iniciar la sesión de los usuarios automáticamente, sin pedirles las credenciales, porque ya han iniciado sesión en sus dispositivos. Por otro lado, si es necesario, algunas características de seguridad avanzadas pueden dificultar el proceso de inicio de sesión del usuario.

* **Escenarios avanzados:** normalmente, la solución de autenticación federada no es necesaria cuando los clientes tienen un requisito de autenticación que no se admite de forma nativa en Azure AD. Se puede encontrar información detallada [aquí](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/), pero los requisitos comunes incluyen:

    * Autenticación que requiere tarjetas inteligentes o certificados.
    * Uso del servidor MFA local o un proveedor de autenticación multifactor de terceros.
    * Autenticación con una solución de autenticación de terceros. Consulte la [lista de compatibilidad de federación de AD Azure](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Los usuarios deben iniciar sesión con su sAMAccountName, por ejemplo, DOMAIN\username, en lugar de usar un nombre principal de usuario (UPN), por ejemplo, user@domain.com.

* **Continuidad empresarial:** normalmente, los sistemas federados requieren una matriz de servidores de carga equilibrada, lo que también se conoce como granja de servidores, configurados en una topología de red interna y red perimetral para garantizar la alta disponibilidad de las solicitudes de autenticación. La sincronización de hash de contraseñas se puede implementar en combinación con la autenticación federada como método de autenticación de reserva cuando el método de autenticación principal ya no está disponible; por ejemplo, cuando los servidores locales no están disponibles. Algunas organizaciones empresariales de gran tamaño requieren una solución de federación para admitir varios puntos de entrada a Internet con DNS geográfico para solicitudes de autenticación de baja latencia.

* **Consideraciones:** normalmente, los sistemas federados requieren una inversión más importante en infraestructura local. La mayoría de las organizaciones eligen esta opción si ya tienen una inversión en federación local y es un requisito empresarial importante para usar un único proveedor de identidades. Es más difícil usar la federación y solucionar sus problemas en comparación con las soluciones de autenticación en la nube. El uso de identificadores de usuario con un dominio no enrutable que no se puede verificar en Azure AD para iniciar sesión requiere la implementación de configuración adicional. Este requisito se conoce como compatibilidad con identificadores de inicio de sesión alternativos. Para conocer los requisitos y las limitaciones, consulte [Configurar un identificador de inicio de sesión alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). Si decide usar un proveedor de autenticación multifactor de terceros con federación, asegúrese de que el proveedor admite WS-Trust para permitir que los dispositivos se unan a Azure AD.

Para conocer los pasos de implementación, consulte [Implementación de los servicios de federación](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers).

> [!NOTE] 
> Al implementar la solución de identidad híbrida de Azure AD, debe asegurarse de implementar una de las topologías admitidas de Azure AD Connect. Aprenda más sobre las configuraciones admitidas y no admitidas en [Topologías de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramas de arquitectura

En el siguiente diagrama se describen los componentes de arquitectura de alto nivel que requiere cada método de autenticación que se puede usar con la solución de identidad híbrida de Azure AD. Se proporciona información general para comparar las diferencias entre las soluciones.

En el siguiente diagrama se muestra la simplicidad de una solución de sincronización de hash de contraseñas:

![PHS](media/azure-ad/azure-ad-authn-image2.png)

En el siguiente diagrama, se describen los requisitos del agente de la autenticación de paso a través:

![PTA](media/azure-ad/azure-ad-authn-image3.png)

En el siguiente diagramase describen los componentes necesarios para la federación en la red perimetral e interna de su organización:

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>Comparación de métodos de autenticación

|Consideración|Sincronización de hash de contraseña + SSO de conexión directa|Autenticación de paso a través + SSO de conexión directa|Federación con AD FS|
|:-----|:-----|:-----|:-----|
|¿Dónde se realiza la autenticación?|En la nube|En la nube, después de un intercambio de comprobación de contraseña segura con el agente de autenticación local|Local|
|¿Cuáles son los requisitos de servidor local más allá del sistema de aprovisionamiento (Azure AD Connect)?|None|1 servidor para cada agente de autenticación adicional|2 o más servidores de AD FS<br>2 o más servidores WAP en la red perimetral|
|¿Cuáles son los requisitos de redes e Internet local más allá del sistema de aprovisionamiento?|None|[Acceso saliente a Internet](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) desde los servidores que ejecutan los agentes de autenticación|[Acceso entrante a Internet](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) para los servidores WAP del perímetro<br>Acceso entrante de red a servidores de AD FS desde los servidores WAP del perímetro<br>Equilibrio de carga de red|
|¿Hay algún requisito de certificado SSL?|Sin |Sin |Sí|
|¿Hay alguna solución de supervisión de estado?|No se requiere|El estado del agente lo proporciona el [Centro de administración de Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|¿Los usuarios realizan el inicio de sesión único en los recursos de nube desde dispositivos unidos a un dominio de la red de la empresa?|Sí, con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Sí, con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Sí|
|¿Qué tipos de inicio de sesión se admiten?|UserPrincipalName + contraseña<br>Autenticación integrada de Windows con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Identificador de inicio de sesión alternativo](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + contraseña<br>Autenticación integrada de Windows con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Identificador de inicio de sesión alternativo](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + contraseña<br>sAMAccountName + contraseña<br>Autenticación integrada de Windows<br>[Autenticación de certificados y tarjetas inteligentes](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[Identificador de inicio de sesión alternativo](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|¿Se admite Windows Hello para empresas?|[Modelo de confianza de clave](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Modelo de confianza de certificado con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modelo de confianza de clave](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Modelo de confianza de certificado con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modelo de confianza de clave](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Modelo de confianza de certificado](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|¿Cuáles son las opciones de autenticación multifactor?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Servidor de Azure MFA](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[MFA de terceros](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|¿Qué estados de cuenta de usuario se admiten?|Cuentas deshabilitadas<br>(retraso de hasta 30 minutos)|Cuentas deshabilitadas<br>Cuenta bloqueada<br>Contraseña expirada<br>Horas de inicio de sesión|Cuentas deshabilitadas<br>Cuenta bloqueada<br>Contraseña expirada<br>Horas de inicio de sesión|
|¿Cuáles son las opciones de acceso condicional?|[Acceso condicional de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acceso condicional de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acceso condicional de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[Reglas de notificaciones de AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|¿Se admite el bloqueo de protocolos heredados?|Sin |Sin |[Sí](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|¿Se puede personalizar el logotipo, la imagen y la descripción en las páginas de inicio de sesión?|[Sí, con Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Sí, con Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Sí](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|¿Qué escenarios avanzados se admiten?|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[Informes de credenciales filtradas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Sistema de autenticación multisitio de baja latencia<br>[Bloqueo de extranet de AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[Integración con sistemas de identidad de terceros](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Recomendaciones y consideraciones de Azure AD

El sistema de identidad garantiza que los usuarios tienen acceso a aplicaciones en la nube y aplicaciones de línea de negocio que se migran y están disponibles en la nube. La autenticación controla el acceso a las aplicaciones, a fin de mantener a los usuarios autorizados productivos y a los usuarios malintencionados alejados de los datos confidenciales de la organización. Por este motivo, tenga en cuenta las recomendaciones siguientes al elegir el método de autenticación adecuado para su organización. 

Use o habilite la sincronización de hash de contraseñas con independencia del método de autenticación que elija, por las razones siguientes:

1. **Alta disponibilidad y recuperación ante desastres:** la autenticación de paso a través y la federación se basan en la infraestructura local. En la autenticación de paso a través, la superficie local incluye las redes y el hardware del servidor que requieren los agentes de autenticación de paso a través. En la federación, la superficie local es incluso mayor, porque se requieren servidores en la red perimetral para las solicitudes de autenticación de proxy y los servidores de federación internos. Para evitar únicos puntos de error, su organización debe implementar servidores redundantes para garantizar que las solicitudes de autenticación siempre se atiendan en caso de que alguno de los componentes deje de funcionar. Tanto la autenticación de paso a través como la federación están sujetas a los controladores de dominio para responder a las solicitudes de autenticación, que también pueden producir un error. Muchos de estos componentes necesitan mantenimiento para funcionar correctamente y es más probable que haya interrupciones cuando el mantenimiento no se planea ni implementa correctamente. Para evitar interrupciones, se puede usar la sincronización de hash de contraseñas porque el servicio de autenticación en la nube de Azure AD de Microsoft se escala globalmente y siempre está disponible.

2. **Supervivencia a una interrupción local:** las consecuencias de una interrupción local debida a un ataque cibernético o a un desastre pueden ser de envergadura; desde daños en la reputación de la marca hasta la paralización de organizaciones que se ven incapaces de hacer frente al ataque. En el último año, muchas organizaciones fueron víctimas de ataques de malware, como ransomware dirigido, que provocaron la inactivación de sus servidores locales. A la hora de ayudar a los clientes a tratar con estos tipos de ataques, Microsoft observó dos categorías de organizaciones:

   a. Las organizaciones que anteriormente habilitaron la sincronización de hash de contraseñas estaban de nuevo en línea en cuestión de horas al cambiar su método de autenticación por este otro. Con el acceso al correo electrónico a través de Office 365, pudieron trabajar para resolver los problemas y tener acceso a otras cargas de trabajo basadas en la nube.

   b. Las organizaciones que anteriormente no habilitaron la sincronización de hash de contraseñas tuvieron que recurrir a sistemas de correo electrónico de consumo externos para las comunicaciones y la resolución de problemas. En esos casos, tardaron varias semanas en estar de nuevo funcionando.

3. **Identity Protection:** una de las mejores maneras de proteger a los usuarios de la nube es Azure AD Identity Protection. Microsoft examina continuamente Internet en busca de listas de usuarios y contraseñas, que usuarios malintencionados venden y permiten que estén disponibles en la Internet oscura. Azure AD puede usar esta información para comprobar si algunos de los nombres de usuario y contraseñas de su organización están en peligro. Por lo tanto, es fundamental habilitar la sincronización de hash de contraseñas, con independencia de qué método de autenticación se use, ya sea autenticación federada o de paso a través. Las credenciales filtradas se presentan como un informe y se pueden usar para bloquear a un usuario o forzarle a que cambie su contraseña cuando intente iniciar sesión con una contraseña filtrada.

Por último, de acuerdo con [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft cuenta con el conjunto más completo de funciones de identidad y administración de acceso. Microsoft controla [450 mil millones de solicitudes de autenticación](https://www.microsoft.com/en-us/security/intelligence-report) cada mes para proporcionar acceso a miles de aplicaciones de SaaS como Office 365 desde prácticamente cualquier dispositivo. 

## <a name="conclusion"></a>Conclusión

En este artículo se describen distintas opciones de autenticación que las organizaciones pueden configurar e implementar para admitir el acceso a las aplicaciones en la nube. Para satisfacer los diversos requisitos empresariales, técnicos y de seguridad, las organizaciones pueden elegir entre la sincronización de hash de contraseña, la autenticación de paso a través y la federación. Con cada método de autenticación, su organización puede decidir si el trabajo de implementar la solución y la experiencia de usuario del proceso de inicio de sesión satisface sus requisitos empresariales. También debe evaluar si su organización necesita las características de escenarios avanzados y continuidad empresarial de cada método de autenticación. Por último, es necesario evaluar las consideraciones de cada método de autenticación para ver si alguno de ellos le impide implementar su opción.

## <a name="next-steps"></a>Pasos siguientes

En la actualidad, las amenazas están presentes 24 horas al día y proceden de cualquier lugar. La implementación del método de autenticación correcto le ayudará a mitigar los riesgos de seguridad y proteger las identidades. 

[Comience a trabajar](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) con Azure AD e implemente la solución de autenticación adecuada para su organización.

Si va a migrar de autenticación federada a autenticación en la nube, consulte [cómo cambiar el método de inicio de sesión](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Para ayudarle a planear e implementar la migración, puede usar [estos planes de proyecto para ayudarle](http://aka.ms/deploymentplans).
