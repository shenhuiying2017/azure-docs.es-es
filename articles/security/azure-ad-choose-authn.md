---
title: Selección del método de autenticación adecuado para la solución de identidad híbrida de Azure AD | Microsoft Docs
description: Esta guía está pensada para ayudar a los Consejeros delegados (CEO), Directores de información (CIO), Directores de seguridad de la información (CISO), Arquitectos jefe de identidad, Arquitectos de empresa y responsables de la toma de decisiones de TI a elegir un método de autenticación para su solución de identidad híbrida de Azure AD en organizaciones de tamaño medio y grande.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 6d107b9264a80c7b280ffed9a50b7bb0ffe354be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365532"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Seleccione el método de autenticación adecuado para su solución de identidad híbrida de Azure Active Directory 

Este artículo es el primero de una serie de artículos destinados a ayudar a las organizaciones a implementar una solución de identidad híbrida de Azure Active Directory (Azure AD) completa. Esta solución se puede considerar como el marco de transformación digital de la identidad híbrida. Asimismo, cubre los resultados comerciales y objetivos en los que las organizaciones pueden centrarse para implementar una solución de identidad híbrida sólida y segura. 

Los primeros resultados empresariales del marco señalan los requisitos de las organizaciones de proteger el proceso de autenticación cuando los usuarios acceden a las aplicaciones en la nube. El primer objetivo empresarial del resultado empresarial de autenticación segura es la posibilidad de que los usuarios inicien sesión en las aplicaciones en la nube mediante sus nombres de usuario y contraseñas locales. Gracias a este proceso de inicio de sesión y a la manera de autenticarse de los usuarios, se abre todo un mundo de posibilidades en la nube.

La elección del método de autenticación correcto es la primera preocupación para las organizaciones que desean mover sus aplicaciones a la nube. Esta decisión no debe tomarse a la ligera por las razones siguientes:

1. Es la primera decisión de una organización que quiere trasladarse a la nube. 

2. El método de autenticación es un componente esencial de la presencia de una organización en la nube. Esto es debido a que controla el acceso a todos los datos y recursos que hay en ella.

3. Es la base de todas las demás características avanzadas de seguridad y experiencia de usuario en Azure AD.

4. Además, el método de autenticación es difícil de modificar una vez implementado.

La identidad es el nuevo plano de control de seguridad de TI. Por lo tanto, la autenticación es la forma de proteger el acceso de una organización al nuevo mundo de la nube. Las organizaciones necesitan un plano de control de identidad que fortalezca su seguridad y mantenga las aplicaciones en la nube a salvo de intrusos.

### <a name="out-of-scope"></a>Fuera de ámbito
Las organizaciones que no tienen una superficie de directorio local existente no entran en el ámbito de este artículo. Por lo general, esas empresas crean identidades solo en la nube, lo que no requiere una solución de identidad híbrida. Las identidades que solo se usan en la nube existen únicamente en la nube y no están asociadas a sus identidades locales correspondientes.

## <a name="authentication-methods"></a>Métodos de autenticación
Cuando la solución de identidad híbrida de Azure AD sea el nuevo plano de control, la autenticación será la base del acceso a la nube. La elección del método de autenticación correcto es una primera decisión fundamental en la configuración de una solución de identidad híbrida de Azure AD. Implemente el método de autenticación que se configura mediante Azure AD Connect, y que también aprovisionará a los usuarios en la nube.

Para elegir un método de autenticación, es necesario tener en cuenta el tiempo, la infraestructura existente, la complejidad y el costo de implementar cada opción. Estos factores varían con cada organización y pueden cambiar con el tiempo. 

Azure AD admite los siguientes métodos de autenticación en soluciones de identidad híbrida.

### <a name="cloud-authentication"></a>Autenticación en la nube
Cuando se elige este método de autenticación, Azure AD administra el proceso de inicio de sesión de los usuarios. Junto con el inicio de sesión único (SSO) completo, los usuarios pueden iniciar sesión en las aplicaciones en la nube sin tener que volver a escribir sus credenciales. Con la autenticación en la nube puede elegir entre dos opciones: 

**Sincronización de hash de contraseñas de Azure AD**. Es la manera más sencilla de habilitar la autenticación para los objetos de directorio local en Azure AD. Gracias a ella, los usuarios pueden usar el mismo nombre de usuario y contraseña que en el entorno local sin tener que implementar ninguna infraestructura adicional. Algunas características premium de Azure AD, como Identity Protection, requieren la sincronización de hash de contraseñas con independencia del método de autenticación seleccionado.

> [!NOTE] 
> Las contraseñas nunca se almacenan en texto no cifrado o cifradas con un algoritmo reversible en Azure AD. Para obtener más información sobre el proceso real de la sincronización de hash de contraseñas, consulte [Implementación de la sincronización de hash de contraseñas con la sincronización de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Autenticación de paso a través de Azure AD**. Proporciona una validación de contraseñas simple para los servicios de autenticación de Azure AD mediante un agente de software que se ejecuta en uno o más servidores locales. Los servidores validan a los usuarios directamente con la instancia local de Active Directory, lo que garantiza que la validación de la contraseña no se realiza en la nube. 

Las empresas con un requisito de seguridad para aplicar de inmediato los estados de cuenta de los usuarios locales, las directivas de contraseña y las horas de inicio de sesión pueden usar este método de autenticación. Para obtener más información sobre el proceso real de autenticación de paso a través, consulte [Inicio de sesión del usuario con la autenticación de paso a través de Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Autenticación federada
Cuando se elige este método de autenticación, Azure AD deja el proceso de autenticación en manos de un sistema de autenticación de confianza como, por ejemplo, una instancia local de Servicios de federación de Active Directory (AD FS) para validar la contraseña del usuario.

El sistema de autenticación puede proporcionar requisitos adicionales de autenticación avanzada. Algunos ejemplos son la autenticación basada en tarjetas inteligentes o la autenticación multifactor de terceros. Para más información, consulte [Implementación de Servicios de federación de Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

En la siguiente sección encontrará un árbol de decisión que le ayudará a decidir qué método de autenticación es más adecuado en su caso. Gracias a este árbol podrá determinar si debe implementar la autenticación en la nube o la autenticación federada para la solución híbrida de Azure AD.

## <a name="decision-tree"></a>Árbol de decisión

![Árbol de decisión de autenticación de Azure AD](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Consideraciones detalladas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticación en la nube: sincronización de hash de contraseñas

* **Esfuerzo**. La sincronización de hash de contraseñas requiere un esfuerzo mínimo en cuanto a la implementación, el mantenimiento y la infraestructura.  Este nivel de esfuerzo se aplica generalmente a organizaciones que solo necesitan que sus usuarios inicien sesión en Office 365, aplicaciones de SaaS y otros recursos basados en Azure AD. Cuando se habilita, la sincronización de hash de contraseñas forma parte del proceso de sincronización de Azure AD Connect y se ejecuta cada dos minutos.

* **Experiencia del usuario**. Para mejorar la experiencia de inicio de sesión de los usuarios, puede implementar un SSO de conexión directa gracias a la sincronización de hash de contraseñas. El SSO de conexión directa elimina las solicitudes innecesarias cuando los usuarios inician sesión.

* **Escenarios avanzados**. Si las organizaciones lo eligen, es posible usar información detallada de identidades con informes de Azure AD Identity Protection. Por ejemplo, el informe de credenciales filtradas. Windows Hello para empresas es otra opción que tiene [requisitos específicos cuando se usa la sincronización de hash de contraseñas](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    Las organizaciones que requieren una autenticación multifactor con sincronización de hash de contraseñas deben usar la autenticación multifactor de Azure AD. Esas organizaciones no pueden usar métodos de autenticación multifactor de terceros o locales.

* **Continuidad del negocio**. La sincronización de hash de contraseñas tiene una alta disponibilidad como servicio en la nube que se puede escalar a todos los centros de datos de Microsoft. Para asegurarse de que la sincronización de hash de contraseñas no deja de funcionar durante períodos prolongados, implemente un segundo servidor de Azure AD Connect de modo provisional en una configuración en espera.

* **Consideraciones**. Actualmente, la sincronización de hash de contraseñas no aplica inmediatamente los cambios en los estados de la cuenta local. En esta situación, un usuario tendrá acceso a las aplicaciones en la nube hasta que el estado de la cuenta del usuario se sincronice con Azure AD. Si las organizaciones quieren superar esta limitación, es recomendable ejecutar un nuevo ciclo de sincronización después de que los administradores realicen actualizaciones masivas en los estados de las cuentas de los usuarios locales. Por ejemplo, pueden deshabilitar las cuentas.

> [!NOTE]
> Los estados de contraseña expirada y cuenta bloqueada no se sincronizan actualmente con Azure AD mediante Azure AD Connect. 

Para ver los pasos de implementación, consulte [Implementación de la sincronización de hash de contraseñas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization).

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticación en la nube: autenticación de paso a través  

* **Esfuerzo**. Para realizar la autenticación de paso a través, necesita uno o más (recomendamos tres) agentes livianos instalados en los servidores existentes. Estos agentes deben tener acceso a la instancia local de Active Directory Domain Services, incluidos los controladores de dominio locales de AD. Estos agentes necesitan acceso saliente a Internet y tener acceso a los controladores de dominio. Por esta razón, no se pueden implementar los agentes en una red perimetral. 

    Este tipo de autenticación de paso a través necesita que los controladores de dominio le proporcionen acceso a la red sin restricciones. Todo el tráfico de red se cifra y se limita a las solicitudes de autenticación. Para obtener más información sobre este proceso, consulte [Información de seguridad detallada sobre la autenticación de paso a través](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive).

* **Experiencia del usuario**. Para mejorar la experiencia de inicio de sesión de los usuarios, puede implementar un SSO de conexión directa con la autenticación de paso a través. El SSO de conexión directa elimina las solicitudes innecesarias cuando los usuarios inician sesión.

* **Escenarios avanzados**. La autenticación de paso a través aplica la directiva de cuenta local al iniciar sesión. Por ejemplo, se deniega el acceso cuando el estado de la cuenta de un usuario local indica que está deshabilitada, bloqueada, con la [contraseña expirada](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) o que se encuentra fuera de las horas de inicio de sesión del usuario. 

    Las organizaciones que requieren una autenticación multifactor con una autenticación de paso a través deben usar Azure Multi-Factor Authentication (MFA). Esas organizaciones no pueden usar métodos de autenticación multifactor de terceros o locales. Las características avanzadas requieren que se implemente la sincronización de hash de contraseñas sin importar si elige o no la autenticación de paso a través. Por ejemplo, esto sucede con el informe de credenciales filtradas de Identity Protection.

* **Continuidad del negocio**. Es recomendable que implemente dos agentes de autenticación de paso a través adicionales. Estos extras son adicionales al primer agente en el servidor de Azure AD Connect. Esta implementación adicional garantiza una alta disponibilidad de solicitudes de autenticación. Cuando hay tres agentes implementados, un agente puede dejar de funcionar cuando otro agente está inactivo por mantenimiento. 

    Existe otro beneficio al implementar la sincronización de hash de contraseñas además de la autenticación de paso a través. Actúa como un método de autenticación de respaldo cuando el método de autenticación principal ya no está disponible.

* **Consideraciones**. Puede utilizar la sincronización de hash de contraseñas como método de autenticación de respaldo para la autenticación de paso a través, y si los agentes no pueden validar las credenciales de un usuario. Debido a esto, la conmutación por error a la sincronización de hash de contraseñas no ocurre automáticamente. Deberá cambiar el método de inicio de sesión manualmente con Azure AD Connect. 

    La autenticación de paso a través solo es compatible con las aplicaciones en la nube que usan la autenticación moderna y protocolos específicos de Exchange Online. Algunos protocolos son ActiveSync, POP3 e IMAP4. Por ejemplo, Microsoft Office 2013 y sus versiones posteriores admiten la autenticación moderna, pero las versiones anteriores no. Para obtener más información sobre la compatibilidad con aplicaciones de Office, consulte [Updated Office 365 modern authentication](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticación moderna actualizada en Office 365). Para ver más detalles sobre la autenticación de paso a través, incluida la compatibilidad con identificadores alternativos, consulte las [preguntas más frecuentes](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Para obtener información sobre los pasos de implementación, consulte [Implementación de la autenticación de paso a través](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Autenticación federada

* **Esfuerzo**. El uso de un sistema de autenticación federada se basa en un sistema externo de confianza para autenticar a los usuarios. Algunas empresas desean reutilizar sus inversiones existentes en sistemas federados con su solución de identidad híbrida de Azure AD. El mantenimiento y la administración del sistema federado no entra en el control de Azure AD. Es responsabilidad de la organización que usa el sistema federado asegurarse de que se implementa de forma segura y de que puede administrar la carga de autenticación. 

* **Experiencia del usuario**. La experiencia de usuario de la autenticación federada depende de la implementación de las características, la topología y la configuración de la granja de servidores de federación. Algunas organizaciones necesitan esta flexibilidad para adaptar y configurar el acceso a la granja de servidores de federación a fin de satisfacer sus requisitos de seguridad. Por ejemplo, es posible configurar usuarios y dispositivos conectados internamente para iniciar la sesión de los usuarios automáticamente, sin pedirles las credenciales. Esta configuración funciona porque ya han iniciado sesión en sus dispositivos. Por otro lado, si es necesario, algunas características de seguridad avanzadas pueden dificultar el proceso de inicio de sesión del usuario.

* **Escenarios avanzados**. Generalmente, se requiere una solución de autenticación federada cuando los clientes tienen un requisito de autenticación que Azure AD no admite de forma nativa. Puede consultar información detallada que le ayudará a [elegir la opción de inicio de sesión correcta](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Considere los siguientes requisitos comunes:

    * Un método de autenticación que requiere tarjetas inteligentes o certificados.
    * Servidores MFA locales o proveedores de autenticación multifactor de terceros.
    * Un método de autenticación con una solución de autenticación de terceros. Consulte la [lista de compatibilidad de federación de AD Azure](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Un inicio de sesión que requiera un elemento sAMAccountName, como DOMAIN\username, en lugar de usar el nombre principal de usuario (UPN); por ejemplo, user@domain.com.

* **Continuidad del negocio**. Los sistemas federados generalmente requieren una matriz de servidores de carga equilibrada, conocida como granja de servidores. Esta granja de servidores está configurada en una red interna y en una topología de red perimetral para garantizar una alta disponibilidad de las solicitudes de autenticación.

    La sincronización de hash de contraseñas se puede implementar en combinación con la autenticación federada como método de autenticación de reserva cuando el método de autenticación principal ya no esté disponible. Por ejemplo, cuando los servidores locales no están disponibles. Algunas organizaciones empresariales de gran tamaño requieren una solución de federación para admitir varios puntos de entrada a Internet con DNS geográfico para solicitudes de autenticación de baja latencia.

* **Consideraciones**. Normalmente, los sistemas federados requieren una inversión más importante en infraestructura local. La mayoría de las organizaciones eligen esta opción si ya han invertido en una federación local. También la escogerán si es un requisito de negocios importante usar un proveedor de identidades únicas. Es más difícil usar la federación y solucionar sus problemas en comparación con las soluciones de autenticación en la nube.

Si hay un dominio no enrutable que no se puede verificar en Azure AD, debe realizar una configuración adicional para implementar el inicio de sesión de un id. de usuario. Este requisito se conoce como compatibilidad con identificadores de inicio de sesión alternativos. Para conocer los requisitos y las limitaciones, consulte [Configurar un identificador de inicio de sesión alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). Si decide usar un proveedor de autenticación multifactor de terceros con federación, asegúrese de que el proveedor admite WS-Trust para permitir que los dispositivos se unan a Azure AD.

Para conocer los pasos de implementación, consulte [Deploying Federation Servers](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (Implementación de los servidores de federación).

> [!NOTE] 
> Al implementar la solución de identidad híbrida de Azure AD, debe asegurarse de implementar una de las topologías admitidas de Azure AD Connect. Aprenda más sobre las configuraciones admitidas y no admitidas en [Topologías de Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramas de arquitectura

En los siguientes diagramas se describen los componentes de arquitectura de alto nivel que requiere cada método de autenticación que se puede usar con la solución de identidad híbrida de Azure AD. Proporcionan información general para comparar las diferencias entre las soluciones.

* Simplicidad de una solución de sincronización de hash de contraseñas:

    ![Identidad híbrida de Azure AD con sincronización de hash de contraseñas](media/azure-ad/azure-ad-authn-image2.png)

* Requisitos del agente para la autenticación de paso a través:

    ![Identidad híbrida de Azure AD con autenticación de paso a través](media/azure-ad/azure-ad-authn-image3.png)

* Componentes necesarios para la federación en la red perimetral e interna de la organización:

    ![Identidad híbrida de Azure AD con autenticación federada](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Comparación de métodos

|Consideración|Sincronización de hash de contraseñas + SSO de conexión directa|Autenticación de paso a través + SSO de conexión directa|Federación con AD FS|
|:-----|:-----|:-----|:-----|
|¿Dónde se realiza la autenticación?|En la nube|En la nube, después de un intercambio de comprobación de contraseña segura con el agente de autenticación local|Local|
|¿Cuáles son los requisitos de servidor local más allá del sistema de aprovisionamiento (Azure AD Connect)?|None|Un servidor para cada agente de autenticación adicional|Dos o más servidores de AD FS<br><br>Dos o más servidores WAP en la red perimetral o DMZ|
|¿Cuáles son los requisitos de redes e Internet locales más allá del sistema de aprovisionamiento?|None|[Acceso saliente a Internet](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) desde los servidores que ejecutan los agentes de autenticación|[Acceso entrante a Internet](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) para los servidores WAP del perímetro<br><br>Acceso entrante de red para los servidores de AD FS desde los servidores WAP del perímetro<br><br>Equilibrio de carga de red|
|¿Hay algún requisito de certificado SSL?|Sin |Sin |Sí|
|¿Hay alguna solución de supervisión de estado?|No se requiere|El estado del agente lo proporciona el [Centro de administración de Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|¿Los usuarios realizan el inicio de sesión único en los recursos de nube desde dispositivos unidos a un dominio de la red de la empresa?|Sí, con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Sí, con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Sí|
|¿Qué tipos de inicio de sesión se admiten?|UserPrincipalName + contraseña<br><br>Autenticación integrada de Windows con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Identificador de inicio de sesión alternativo](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + contraseña<br><br>Autenticación integrada de Windows con [SSO de conexión directa](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Identificador de inicio de sesión alternativo](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + contraseña<br><br>sAMAccountName + contraseña<br><br>Autenticación integrada de Windows<br><br>[Autenticación de certificados y tarjetas inteligentes](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Identificador de inicio de sesión alternativo](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|¿Se admite Windows Hello para empresas?|[Modelo de confianza de clave](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confianza de certificado con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modelo de confianza de clave](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confianza de certificado con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modelo de confianza de clave](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confianza de certificado](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|¿Cuáles son las opciones de autenticación multifactor?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br><br>[Servidor de Azure MFA](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA de terceros](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|¿Qué estados de cuenta de usuario se admiten?|Cuentas deshabilitadas<br>(retraso de hasta 30 minutos)|Cuentas deshabilitadas<br><br>Cuenta bloqueada<br><br>Contraseña expirada<br><br>Horas de inicio de sesión|Cuentas deshabilitadas<br><br>Cuenta bloqueada<br><br>Contraseña expirada<br><br>Horas de inicio de sesión|
|¿Cuáles son las opciones de acceso condicional?|[Acceso condicional de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acceso condicional de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acceso condicional de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Reglas de notificaciones de AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|¿Se admite el bloqueo de protocolos heredados?|Sin |Sin |[Sí](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|¿Se puede personalizar el logotipo, la imagen y la descripción en las páginas de inicio de sesión?|[Sí, con Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Sí, con Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Sí](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|¿Qué escenarios avanzados se admiten?|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords) (Bloqueo inteligente de contraseñas)<br><br>[Informes de credenciales filtradas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout) (Bloqueo inteligente de contraseñas)|Sistema de autenticación multisitio de baja latencia<br><br>[Bloqueo de extranet de AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integración con sistemas de identidad de terceros](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Recomendaciones
El sistema de identidad garantiza que los usuarios tengan acceso a aplicaciones en la nube y a aplicaciones de línea de negocio que se migran y están disponibles en la nube. La autenticación controla el acceso a las aplicaciones, a fin de mantener la productividad de los usuarios autorizados, y a los usuarios malintencionados alejados de los datos confidenciales de la organización.

Use o habilite la sincronización de hash de contraseñas con independencia del método de autenticación que elija, por las razones siguientes:

1. **Alta disponibilidad y recuperación ante desastres**. La autenticación de paso a través y la federación se basan en la infraestructura local. En la autenticación de paso a través, la superficie local incluye las redes y el hardware del servidor que requieren los agentes de autenticación de paso a través. En el caso de la federación, la superficie local es aún mayor. Esto es debido a que se requieren servidores en la red perimetral para las solicitudes de autenticación de proxy y los servidores de federación internos. 

    Para evitar los únicos puntos de errores, implemente servidores redundantes. A continuación, las solicitudes de autenticación siempre se atenderán si se produce un error en alguno de los componentes. Tanto la autenticación de paso a través como la de federación están sujetas a los controladores de dominio para responder a las solicitudes de autenticación, que también pueden producir un error. Muchos de estos componentes deben mantenerse para permanecer en buen estado. Las interrupciones son más probables cuando el mantenimiento no se planifica ni se implementa correctamente. Para evitar interrupciones, se puede usar la sincronización de hash de contraseñas porque el servicio de autenticación en la nube de Microsoft Azure AD se escala globalmente y siempre está disponible.

2. **Subsistencia a interrupciones locales**.  Las consecuencias de una interrupción local debida a un ataque cibernético o a un desastre pueden ser de gran envergadura; desde daños en la reputación de la marca hasta la paralización de organizaciones que se ven incapaces de hacer frente al ataque. Últimamente muchas organizaciones han sido víctimas de ataques de malware, como ransomware dirigido, que provocaron que sus servidores locales se desconectaran. A la hora de ayudar a los clientes a tratar con estos tipos de ataques, Microsoft observó dos categorías de organizaciones:

   * Las organizaciones que activaron previamente la sincronización de hash de contraseñas, cambiaron su método de autenticación para usar la sincronización de hash de contraseñas. Gracias a esto, volvieron a estar en línea en cuestión de horas. Al usar el acceso al correo electrónico a través de Office 365, pudieron trabajar para resolver los problemas y tener acceso a otras cargas de trabajo basadas en la nube.

   * Las organizaciones que no habilitaron previamente la sincronización de hash de contraseñas, tuvieron que recurrir a sistemas de correo electrónico de consumidor externos para las comunicaciones y la resolución de problemas. En esos casos, tardaron varias semanas en estar de nuevo funcionando.

3. **Identity Protection**. Una de las mejores maneras de proteger a los usuarios en la nube es Azure AD Identity Protection. Microsoft examina continuamente Internet en busca de listas de usuarios y contraseñas que los usuarios malintencionados venden y proporcionan en la Internet oscura. Azure AD puede usar esta información para comprobar si algunos de los nombres de usuario y contraseñas de su organización están en peligro. Por lo tanto, es fundamental habilitar la sincronización de hash de contraseñas, con independencia de qué método de autenticación se use, ya sea autenticación federada o de paso a través. Las credenciales filtradas se presentan como un informe. Use esta información para bloquear o exigir que los usuarios cambien sus contraseñas cuando intenten iniciar sesión con contraseñas filtradas.

Por último, de acuerdo con [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft cuenta con el conjunto más completo de funciones de identidad y administración de acceso. Microsoft controla [450 mil millones de solicitudes de autenticación](https://www.microsoft.com/en-us/security/intelligence-report) cada mes para proporcionar acceso a miles de aplicaciones de SaaS como Office 365 desde prácticamente cualquier dispositivo. 

## <a name="conclusion"></a>Conclusión

En este artículo se describen distintas opciones de autenticación que las organizaciones pueden configurar e implementar para admitir el acceso a las aplicaciones en la nube. Para satisfacer los diversos requisitos de negocios, técnicos y de seguridad, las organizaciones pueden elegir entre la sincronización de hash de contraseñas, la autenticación de paso a través y la federación. 

Revise con atención cada método de autenticación interno. ¿Los esfuerzos para implementar la solución, así como la experiencia del usuario en el proceso de inicio de sesión responden a los requisitos de su negocio? También debe valorar si su organización necesita las características de escenarios avanzados y continuidad empresarial de cada método de autenticación. Por último, sopese los detalles de cada método de autenticación. ¿Alguno de ellos le impide implementar su elección?

## <a name="next-steps"></a>Pasos siguientes

En la actualidad, las amenazas están presentes 24 horas al día y proceden de cualquier lugar. La implementación del método de autenticación correcto le ayudará a mitigar los riesgos de seguridad y a proteger las identidades.

[Comience a trabajar](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) con Azure AD e implemente la solución de autenticación adecuada para su organización.

Si está pensando cambiar de la autenticación federada a la autenticación en la nube, consulte la información acerca de [cómo cambiar el método de inicio de sesión](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Para ayudarle a planear e implementar la migración, puede usar [estos planes de implementación de proyectos](http://aka.ms/deploymentplans).
