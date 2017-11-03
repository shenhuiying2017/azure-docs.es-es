---
title: "Información de seguridad detallada sobre la autenticación de paso a través de Azure Active Directory | Microsoft Docs"
description: "En este artículo se describe cómo la autenticación de paso a través de Azure Active Directory (Azure AD) protege las cuentas locales."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: a5feadd851b166d0a9a77bd1d124cdd599d5d701
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Información de seguridad detallada sobre la autenticación de paso a través de Azure Active Directory

En este artículo se proporciona una descripción más detallada sobre cómo funciona la autenticación de paso a través. Se centra más en los aspectos de seguridad de la característica. Este tema será de interés para los administradores de seguridad y TI, los encargados de la conformidad y seguridad y otros profesionales de TI responsables de este último ámbito en pymes o grandes empresas.

Entre los temas tratados se incluyen:
- Información técnica detallada sobre cómo se instalan y registran los agentes de autenticación.
- Información técnica detallada sobre el cifrado de contraseñas durante el inicio de sesión de usuario.
- Seguridad de los canales entre los agentes de autenticación locales y Azure Active Directory (Azure AD).
- Información técnica detallada sobre cómo se mantiene la seguridad operativa de los agentes de autenticación.
- Otros temas relacionados con la seguridad.

## <a name="key-security-capabilities"></a>Funcionalidades de seguridad principales

Estos son los aspectos clave de seguridad de esta característica:
- Se basa en una arquitectura multiinquilino segura que proporciona aislamiento de las solicitudes de inicio de sesión entre los inquilinos.
- Las contraseñas locales nunca se almacenan en la nube.
- Los agentes de autenticación locales, que escuchan las solicitudes de validación de contraseña y responden a ellas, solo realizan conexiones salientes desde dentro de la red. No hay ningún requisito para instalar estos agentes de autenticación en una red perimetral (DMZ).
- Solo se utilizan los puertos estándar (80 y 443) para la comunicación saliente desde los agentes de autenticación a Azure AD. Ningún puerto de entrada debe estar abierto en el firewall. 
  - El puerto 443 se usa para todas las comunicaciones salientes autenticadas.
  - Solo se utiliza el puerto 80 para la descarga de las listas de revocación de certificados (CRL), a fin de garantizar que ninguno de los certificados utilizados por la característica se han revocado.
  - Vea [aquí](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) la lista completa de los requisitos de red.
- Las contraseñas proporcionadas por el usuario durante el inicio de sesión se cifran en la nube antes de que los agentes de autenticación locales las acepten para validarlas en Active Directory.
- El canal HTTPS entre Azure AD y un agente de autenticación local está protegido por la autenticación mutua.
- Se integra perfectamente con las funcionalidades de protección de la nube de Azure AD, como las directivas de acceso condicional (incluido Multi-Factor Authentication), Identity Protection y el bloqueo inteligente.

## <a name="components-involved"></a>Componentes necesarios

Para obtener información general sobre la seguridad operativa, del servicio y de los datos de Azure AD, vea el [Centro de confianza](https://azure.microsoft.com/support/trust-center/). Los siguientes componentes son necesarios al utilizar la autenticación de paso a través para el inicio de sesión de usuario:
- **STS de Azure AD**: un servicio de token de seguridad (STS) sin estado que procesa las solicitudes de inicio de sesión y genera tokens de seguridad para los clientes, servicios o exploradores de los usuarios, según proceda.
- **Azure Service Bus** proporciona comunicación habilitada para la nube con mensajería empresarial y retransmite comunicación que ayuda a conectar las soluciones locales con la nube.
- **Agente de autenticación de Azure AD Connect (agente de autenticación)**: un componente local que escucha las solicitudes de validación de contraseña y responde a ellas.
- **Azure SQL Database**: retiene información sobre los agentes de autenticación del inquilino, incluidos los metadatos y las claves de cifrado.
- **Active Directory (AD)**: el entorno local de Active Directory, donde se almacenan las cuentas de usuario y las contraseñas correspondientes.

## <a name="installation-and-registration-of-authentication-agents"></a>Instalación y registro de los agentes de autenticación

Los agentes de autenticación están instalados y registrados en Azure AD al [habilitar la autenticación de paso a través con Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) o al [agregar agentes de autenticación adicionales para garantizar la alta disponibilidad de las solicitudes de inicio de sesión](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). El funcionamiento de un agente de autenticación consta de tres fases principales:

- Instalación del agente de autenticación
- Registro del agente de autenticación
- Inicialización del agente de autenticación

Cada una de estas fases se aborda de forma detallada en los temas siguientes.

### <a name="authentication-agent-installation"></a>Instalación del agente de autenticación

Solo los administradores globales pueden instalar un agente de autenticación (con Azure AD Connect o de forma independiente) en un servidor local. La instalación agrega estas dos nuevas entradas a **Panel de control -> Programas -> Programas y características**:
- La propia aplicación del agente de autenticación. Se ejecuta con privilegios del [servicio de red](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx).
- La aplicación del actualizador utilizada para actualizar automáticamente el agente de autenticación. Se ejecuta con privilegios del [sistema local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).


### <a name="authentication-agent-registration"></a>Registro del agente de autenticación

Después de instalar el agente de autenticación, este debe registrarse en Azure AD. Azure AD realiza este paso mediante la asignación al agente de autenticación de un certificado de identidad digital exclusivo que puede usar para proteger la comunicación con Azure AD.

El procedimiento de registro también enlaza el agente de autenticación con su inquilino para que Azure AD sepa que este agente de autenticación específico es el único autorizado para controlar las solicitudes de validación de contraseña del inquilino. Este procedimiento se repite para cada nuevo agente de autenticación que se registra.

A continuación, se explica cómo los agentes de autenticación se registran en Azure AD:

![Registro del agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD primero solicita a un administrador global que inicie sesión en Azure AD con sus credenciales. Durante el inicio de sesión, el agente de autenticación adquiere un token de acceso que puede usar en nombre del administrador global.
2. El agente de autenticación después genera un par de claves: una clave pública y una clave privada.
    - Este par de claves se genera con el uso del cifrado estándar de **2048 bits de RSA**.
    - La clave privada nunca abandona el servidor local en el que se ha instalado el agente de autenticación.
3.  El agente de autenticación realiza una solicitud de "registro" a Azure AD a través de HTTPS, con los siguientes componentes incluidos en la solicitud:
    - El token de acceso adquirido en el paso 1.
    - La clave pública generada en el paso 2.
    - Una **solicitud de firma de certificado** (CSR o solicitud de certificado). Sirve para solicitar un certificado de identidad digital, con Azure AD como la entidad de certificación.
4. Azure AD valida el token de acceso en la solicitud de registro y verifica que la solicitud proviene de un administrador global.
5. Azure AD después firma y emite un certificado de identidad digital al agente de autenticación.
    - El certificado se firma mediante la **entidad de certificación raíz (CA) de Azure AD**. Tenga en cuenta que esta entidad de certificación _no_ está en el almacén de **entidades de certificación raíz de confianza** de Windows.
    - Esa entidad de certificación se usa solo con la característica de autenticación de paso a través. Solo se usa para la firmas CSR durante el registro del agente de autenticación.
    - Esta entidad de certificación no se usa ningún otro servicio en Azure AD.
    - El sujeto del certificado (**nombre distintivo o DN**) se establece en su **Id. de inquilino**. Se trata de un GUID que identifica al inquilino de forma exclusiva. Limita el uso del certificado solo con dicho inquilino.
6. Azure AD almacena la clave pública del agente de autenticación en una instancia de Azure SQL Database, a la que solo Azure AD tiene acceso.
7. El certificado (emitido en el paso 5) se almacena en el servidor local en el **almacén de certificados de Windows** (concretamente, en la ubicación **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**) y lo utilizan el agente de autenticación y las aplicaciones del actualizador.

### <a name="authentication-agent-initialization"></a>Inicialización del agente de autenticación

Cuando se inicia el agente de autenticación, ya sea por primera vez después del registro o después del reinicio de un servidor, necesita comunicarse de forma segura con el servicio Azure AD y empezar a aceptar solicitudes de validación de contraseña.

![Inicialización del agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

A continuación, se explica cómo se inicializan los agentes de autenticación:



1. El agente de autenticación se inicia mediante la realización de una solicitud de "arranque" saliente a Azure AD. 
    - Esta solicitud de arranque se realiza a través del puerto 443 y por un canal HTTPS autenticado mutuamente (con el mismo certificado emitido durante el registro del agente de autenticación).
2. Azure AD responde a esta solicitud de arranque proporcionando una **clave de acceso** a una cola de Azure Service Bus que sea única en su inquilino (identificado con su Id. de inquilino).
3. El agente de autenticación realiza una conexión HTTPS saliente persistente (a través del puerto 443) a la cola. 
    - Ahora está listo para recuperar y administrar las solicitudes de validación de contraseña.

Si tiene varios agentes de autenticación registrados en el inquilino, el procedimiento de inicialización garantiza que cada uno de ellos se conecte a la misma cola de Azure Service Bus. 

## <a name="processing-sign-in-requests"></a>Procesamiento de solicitudes de inicio de sesión 

En el diagrama siguiente se muestra cómo la autenticación de paso a través procesa las solicitudes de inicio de sesión de usuario.

![Procesamiento de solicitudes de inicio de sesión](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

La autenticación de paso a través administra una solicitud de inicio de sesión de usuario como sigue: 

1. Un usuario intenta acceder a una aplicación (por ejemplo, Outlook Web App en [https://outlook.office365.com/owa](https://outlook.office365.com/owa)).
2. Si el usuario todavía no ha iniciado sesión, la aplicación redirige el explorador a la página de inicio de sesión de Azure AD.
3. El servicio STS de Azure AD responde con la página de inicio de sesión de usuario.
4. El usuario escribe su nombre de usuario y contraseña en la página de inicio de sesión de Azure AD y hace clic en el botón "Iniciar sesión".
5. El nombre de usuario y la contraseña se envían a STS de Azure AD en una solicitud POST HTTPS.
6. STS de Azure AD recupera las claves públicas de todos los agentes de autenticación registrados en el inquilino de la instancia de Azure SQL Database y las usa para cifrar la contraseña. 
    - Genera "N" valores de contraseñas cifradas para "N" agentes de autenticación registrados en el inquilino.
7. STS de Azure AD coloca la solicitud de validación de contraseña (el nombre de usuario y los valores de las contraseñas cifradas) en la cola de Azure Service Bus específica del inquilino.
8. Puesto que los agentes de autenticación inicializados están conectados de forma persistente a la cola de Azure Service Bus, uno de los agentes de autenticación disponibles recupera la solicitud de validación de contraseña.
9. El agente de autenticación busca el valor de la contraseña cifrada específico para su clave pública (con un identificador) y lo descifra con su clave privada.
10. El agente de autenticación intenta validar el nombre de usuario y la contraseña en el entorno local de Active Directory con **[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)** (mediante el parámetro **dwLogonType** definido en **LOGON32_LOGON_NETWORK**). 
    - Se trata de la misma API que utilizan los Servicios de federación de Active Directory (AD FS) para que los usuarios inicien sesión en un escenario de inicio de sesión federado.
    - Esto se basa en el proceso de resolución estándar de Windows Server para buscar el controlador de dominio.
11. El agente de autenticación recibe el resultado de Active Directory (correcto, nombre de usuario o contraseña incorrectos, contraseña expirada, usuario bloqueado, etc.).
12. El agente de autenticación reenvía el resultado al servicio STS de Azure AD por un canal HTTPS saliente autenticado mutuamente a través del puerto 443. La autenticación mutua usa el mismo certificado emitido anteriormente al agente de autenticación durante el registro.
13. STS de Azure AD verifica que este resultado se pone en correlación con la solicitud de inicio de sesión específica del inquilino.
14. STS de Azure AD continúa con el procedimiento de inicio de sesión según la configuración. Por ejemplo, si la validación de contraseña es correcta, el usuario podría tener que someterse a Multi-Factor Authentication o se le podría redirigir de nuevo a la aplicación.

## <a name="operational-security-of-authentication-agents"></a>Seguridad operativa de los agentes de autenticación

Para garantizar la seguridad operativa de la autenticación de paso a través, Azure AD renueva periódicamente sus certificados. Estas renovaciones se desencadenan desde Azure AD y no se rigen por los propios agentes de autenticación.

![Seguridad operativa](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

A continuación, se explica cómo un agente de autenticación renueva su confianza con Azure AD:

1. El agente de autenticación hace ping periódicamente a Azure AD (cada pocas horas) para comprobar si es el momento de renovar su certificado. 
    - Esta comprobación se realiza a través de un canal HTTPS autenticado mutuamente (con el mismo certificado emitido durante el registro).
2. Si el servicio indica que es el momento de renovar, el agente de autenticación genera un nuevo par de claves: una clave pública y una clave privada.
    - Estas claves se generan con el uso del cifrado estándar de **2048 bits de RSA**.
    - La clave privada nunca abandona el servidor local.
3. El agente de autenticación realiza una solicitud de "renovación de certificado" a Azure AD a través de HTTPS, con los siguientes componentes incluidos en la solicitud:
    - El certificado existente (recuperado de la ubicación **CERT_SYSTEM_STORE_LOCAL_MACHINE** en el almacén de certificados de Windows). En este procedimiento no participa ningún administrador global, por lo que no se necesita ningún token de acceso en nombre del administrador global.
    - La clave pública generada en el paso 2.
    - Una **solicitud de firma de certificado** (CSR o solicitud de certificado). Sirve para solicitar un nuevo certificado de identidad digital, con Azure AD como la entidad de certificación.
4. Azure AD valida el certificado existente en la solicitud de renovación de certificado. Después, verifica que la solicitud proviene de un agente de autenticación registrado en el inquilino.
5. Si el certificado existente sigue siendo válido, Azure AD firma un nuevo certificado de identidad digital y lo emite de nuevo al agente de autenticación. 
6. Si ha expirado el certificado existente, Azure AD elimina el agente de autenticación de lista de los agentes de autenticación registrados en el inquilino. Después, un administrador global debe instalar y registrar manualmente un nuevo agente de autenticación.
    - El certificado se firma mediante la **entidad de certificación raíz (CA) de Azure AD**.
    - El sujeto del certificado (**nombre distintivo o DN**) se establece en su **Id. de inquilino**, un GUID que identifica al inquilino de forma exclusiva. Es decir, el certificado se limita solo al inquilino.
6. Azure AD almacena la clave pública "nueva" del agente de autenticación en una instancia de Azure SQL Database, a la que solo Azure AD tiene acceso. Además, invalida la "antigua" clave pública asociada con el agente de autenticación.
7. El nuevo certificado (que se emitió en el paso 5) se almacena en el servidor en el **almacén de certificados de Windows** (concretamente, en la ubicación **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**).
    - Puesto que el procedimiento de renovación de confianza ocurre de manera no interactiva (sin la presencia del administrador global), el agente de autenticación ya no tiene acceso para actualizar el certificado existente en la ubicación **CERT_SYSTEM_STORE_LOCAL_MACHINE**. Tenga en cuenta que el certificado de la ubicación **CERT_SYSTEM_STORE_LOCAL_MACHINE** no se elimina durante este procedimiento.
8. El nuevo certificado se utiliza para la autenticación a partir de este punto. Cada posterior renovación del certificado reemplaza el certificado de la ubicación **CERT_SYSTEM_STORE_LOCAL_MACHINE**.

## <a name="auto-update-of-authentication-agents"></a>Actualización automática de los agentes de autenticación

La aplicación del actualizador actualiza automáticamente el agente de autenticación cuando se lanza una nueva versión. No controla las solicitudes de validación de contraseña del inquilino. 

Azure AD hospeda la nueva versión del software como un **paquete de Windows Installer (MSI)** firmado. El archivo MSI se firma mediante [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) con **SHA256** como el algoritmo de resumen. 

![Actualización automática](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

A continuación, se explica cómo un agente de autenticación se actualiza automáticamente:

1. El actualizador hace ping periódicamente a Azure AD (cada hora) para comprobar si hay una nueva versión del agente de autenticación. 
    - Esta comprobación se realiza a través de un canal HTTPS autenticado mutuamente (con el mismo certificado emitido durante el registro). El agente de autenticación y el actualizador comparten el certificado almacenado en el servidor.
2. Si hay disponible una nueva versión, Azure AD devuelve el archivo MSI firmado al actualizador.
3. El actualizador verifica si el archivo MSI está firmado por Microsoft.
4. El actualizador ejecuta el archivo MSI. Esta acción realiza los pasos siguientes (tenga en cuenta que el actualizador se ejecuta con privilegios del [sistema local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)):
    - Detiene el servicio del agente de autenticación.
    - Instala la nueva versión del agente de autenticación en el servidor.
    - Reinicia el servicio del agente de autenticación.

>[!NOTE]
>Si tiene varios agentes de autenticación registrados en el inquilino, Azure AD no renueva sus certificados ni los actualiza al mismo tiempo. En su lugar, Azure AD lo hace gradualmente para garantizar la alta disponibilidad de las solicitudes de inicio de sesión.


## <a name="next-steps"></a>Pasos siguientes
- [**Limitaciones actuales**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): conozca qué escenarios son compatibles y cuáles no.
- [**Inicio rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md): desarrollo y ejecución de la autenticación de paso a través de Azure AD.
- [**Bloqueo inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configuración de la funcionalidad Bloqueo inteligente en su inquilino para proteger las cuentas de usuario.
- [**Cómo funciona**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): aprenda los conceptos básicos sobre cómo funciona la autenticación de paso a través de Azure AD.
- [**Preguntas más frecuentes**](active-directory-aadconnect-pass-through-authentication-faq.md): obtenga respuestas a las preguntas más frecuentes.
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): aprenda a resolver problemas comunes de esta característica.
- [**SSO de conexión directa de Azure AD**](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.

