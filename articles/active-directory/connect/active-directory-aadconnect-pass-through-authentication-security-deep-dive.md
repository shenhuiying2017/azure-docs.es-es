---
title: "Información de seguridad detallada sobre la autenticación de paso a través de Azure Active Directory | Microsoft Docs"
description: "En este artículo se describe la manera en que la autenticación de paso a través de Azure Active Directory (Azure AD) protege las cuentas locales."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: 84a5ef23739635ba4d2f0adc688c1b506f643a36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Información de seguridad detallada sobre la autenticación de paso a través de Azure Active Directory

En este artículo se proporciona una descripción más detallada sobre cómo funciona la autenticación de paso a través de Azure Active Directory (Azure AD). Se centra más en los aspectos de seguridad de la característica. Este tema puede ser de interés para los administradores de seguridad y TI, los encargados del cumplimiento de la normativa y la seguridad, y otros profesionales de TI responsables de este último ámbito en pymes o grandes empresas.

Entre los temas a tratar se incluyen:
- Información técnica detallada sobre cómo se instalan y registran los agentes de autenticación.
- Información técnica detallada sobre el cifrado de contraseñas durante el inicio de sesión del usuario.
- La seguridad de los canales entre los agentes de autenticación locales y Azure AD.
- Información técnica detallada sobre cómo se mantiene la seguridad operativa de los agentes de autenticación.
- Otros temas relacionados con la seguridad.

## <a name="key-security-capabilities"></a>Funcionalidades de seguridad principales

Estos son los aspectos clave de seguridad de esta característica:
- Se basa en una arquitectura segura de varios inquilinos que proporciona aislamiento a las solicitudes de inicio de sesión entre los inquilinos.
- Las contraseñas locales nunca se almacenan en la nube.
- Los agentes de autenticación locales que escuchan las solicitudes de validación de contraseña y responden a ellas solo realizan conexiones salientes desde la red. No hay ningún requisito para instalar estos agentes de autenticación en una red perimetral (DMZ).
- Solo se usan puertos estándar (80 y 443) para la comunicación saliente de los agentes de autenticación a Azure AD. No es necesario abrir puertos entrantes en el firewall. 
  - El puerto 443 se usa en todas las comunicaciones salientes autenticadas.
  - Solo se usa el puerto 80 para descargar las listas de revocación de certificados (CRL) a fin de garantizar que ninguno de los certificados que use la característica hayan sido revocados.
  - Para obtener la lista completa de los requisitos de red, consulte [Autenticación de paso a través de Azure Active Directory: inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Las contraseñas que proporciona el usuario durante el inicio de sesión se cifran en la nube antes de que los agentes de autenticación locales las acepten para validarlas en Active Directory.
- El canal HTTPS entre Azure AD y el agente de autenticación local está protegido gracias a la autenticación mutua.
- Esta característica se integra perfectamente con las funcionalidades de protección en la nube de Azure AD, como las directivas de acceso condicional (incluyendo Azure Multi-Factor Authentication), Identity Protection y el bloqueo inteligente.

## <a name="components-involved"></a>Componentes necesarios

Para obtener información general sobre la seguridad operativa, del servicio y de los datos de Azure AD, consulte el [Centro de confianza](https://azure.microsoft.com/support/trust-center/). Los siguientes componentes son necesarios al usar la autenticación de paso a través del inicio de sesión de usuario:
- **STS de Azure AD**: un servicio de token de seguridad (STS) sin estado que procesa las solicitudes de inicio de sesión y genera tokens de seguridad para los clientes, servicios o exploradores de los usuarios, según proceda.
- **Azure Service Bus** proporciona comunicación habilitada para la nube con mensajería empresarial y retransmite comunicación que ayuda a conectar las soluciones locales con la nube.
- **Agente de autenticación de Azure AD Connect** : es un componente local que escucha las solicitudes de validación de contraseña y responde a ellas.
- **Azure SQL Database**: retiene información sobre los agentes de autenticación del inquilino, incluidos los metadatos y las claves de cifrado.
- **Active Directory**: es el entorno local de Active Directory, donde se almacenan las cuentas de usuario y las contraseñas correspondientes.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalación y registro de los agentes de autenticación

Los agentes de autenticación se instalan y registran con Azure AD al:
   - [Habilitar la autenticación de paso a través mediante Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Agregar más agentes de autenticación para garantizar la alta disponibilidad de las solicitudes de inicio de sesión](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
El funcionamiento de un agente de autenticación consta de tres fases principales:

1. Instalación del agente de autenticación
2. Registro del agente de autenticación
3. Inicialización del agente de autenticación

En las secciones siguientes se tratan estas fases en detalle.

### <a name="authentication-agent-installation"></a>Instalación del agente de autenticación

Solo los administradores globales pueden instalar un agente de autenticación (con Azure AD Connect o de forma independiente) en un servidor local. La instalación agrega dos nuevas entradas a la lista de **Panel de control** > **Programas** > **Programas y características**:
- La propia aplicación del agente de autenticación. Esta aplicación se ejecuta con privilegios [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx).
- La aplicación del actualizador que se usa para actualizar automáticamente el agente de autenticación. Esta aplicación se ejecuta con privilegios [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).

### <a name="authentication-agent-registration"></a>Registro del agente de autenticación

Después de instalar el agente de autenticación, este debe registrarse en Azure AD. Azure AD asigna a cada agente de autenticación un certificado de identidad digital exclusivo que se puede usar para proteger las comunicaciones con Azure AD.

El procedimiento de registro también enlaza al agente de autenticación con el inquilino. Gracias a esto, Azure AD sabrá que este agente de autenticación específico es el único autorizado para controlar las solicitudes de validación de contraseña del inquilino. Este procedimiento se repite para cada nuevo agente de autenticación que se registra.

Los agentes de autenticación realizan los siguientes pasos para registrarse con Azure AD:

![Registro de agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD primero solicita a un administrador global que inicie sesión en Azure AD con sus credenciales. Durante el inicio de sesión, el agente de autenticación adquiere un token de acceso que puede usar en nombre del administrador global.
2. A continuación, el agente de autenticación genera un par de claves: una clave pública y otra privada.
    - Este par de claves se genera mediante el cifrado estándar de 2048 bits de RSA.
    - La clave privada permanece en el servidor local donde reside el agente de autenticación.
3. El agente de autenticación realiza una solicitud de "registro" a Azure AD a través de HTTPS, con los siguientes componentes incluidos en la solicitud:
    - El token de acceso adquirido en el paso 1.
    - La clave pública generada en el paso 2.
    - Una solicitud de firma de certificado (CSR o solicitud de certificado). Esto sirve para solicitar un certificado de identidad digital que tenga Azure AD como entidad de certificación.
4. Azure AD valida el token de acceso en la solicitud de registro y verifica que la solicitud provenga de un administrador global.
5. A continuación, Azure AD firma y emite un certificado de identidad digital al agente de autenticación.
    - La entidad de certificación raíz en Azure AD se usa para firmar el certificado. 

     >[!NOTE]
     > Tenga en cuenta que esta entidad de certificación _no_ está en el almacén de entidades de certificación raíz de confianza de Windows.
    - Esa entidad de certificación se usa solo con la característica de autenticación de paso a través. La entidad de certificación solo se usa para firmar CSR durante el registro del agente de autenticación.
    -  Ninguno de los demás servicios de Azure AD usa esta entidad de certificación.
    - El sujeto del certificado (nombre distintivo o DN) se establece en su id. de inquilino. Este DN es un GUID que identifica al inquilino de forma exclusiva. Asimismo, limita el uso del certificado a dicho inquilino.
6. Azure AD almacena la clave pública del agente de autenticación en una instancia de Azure SQL Database, a la que solo Azure AD tiene acceso.
7. El nuevo certificado (que se emitió en el paso 5) se almacena en el servidor local del almacén de certificados de Windows (concretamente, en la ubicación [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)). Tenga en cuenta que este certificado lo usan tanto el agente de autenticación como las aplicaciones de actualización.

### <a name="authentication-agent-initialization"></a>Inicialización del agente de autenticación

Cuando se inicia el agente de autenticación, ya sea por primera vez después del registro o después de reiniciar un servidor, este necesita comunicarse de forma segura con el servicio Azure AD y así poder empezar a aceptar solicitudes de validación de contraseña.

![Inicialización del agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

A continuación, se explica cómo se inicializan los agentes de autenticación:

1. El agente de autenticación emite una solicitud de arranque saliente a Azure AD. 
    - Esta solicitud se realiza a través del puerto 443 y se encuentra en un canal HTTPS autenticado mutuamente. La solicitud usa el mismo certificado que se emitió durante el registro del agente de autenticación.
2. Para responder a esta solicitud, Azure AD proporciona una clave de acceso a una cola de Azure Service Bus que sea única en el inquilino y que esté identificada mediante el id. de inquilino.
3. El agente de autenticación realiza una conexión HTTPS saliente persistente (a través del puerto 443) a la cola. 
    - El agente de autenticación ya está listo para recuperar y administrar las solicitudes de validación de contraseñas.

Si tiene varios agentes de autenticación registrados en el inquilino, el procedimiento de inicialización garantiza que cada uno de ellos se conecte a la misma cola de Service Bus. 

## <a name="process-sign-in-requests"></a>Procesar las solicitudes de inicio de sesión 

En el diagrama siguiente se muestra cómo la autenticación de paso a través procesa las solicitudes de inicio de sesión de los usuarios.

![Inicio de sesión del proceso](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

La autenticación de paso a través administra una solicitud de inicio de sesión de usuario como sigue: 

1. Un usuario intenta acceder a una aplicación (por ejemplo, [Outlook Web App](https://outlook.office365.com/owa)).
2. Si el usuario todavía no ha iniciado sesión, la aplicación redirige el explorador a la página de inicio de sesión de Azure AD.
3. El servicio STS de Azure AD responde con la página de **inicio de sesión de usuario**.
4. El usuario escribe su nombre de usuario y contraseña en la página de **inicio de sesión del usuario** y hace clic en el botón **Iniciar sesión**.
5. El nombre de usuario y la contraseña se envían al STS de Azure AD en una solicitud POST HTTPS.
6. El STS de Azure AD recupera las claves públicas de todos los agentes de autenticación registrados en el inquilino de la instancia de Azure SQL Database y las usa para cifrar la contraseña. 
    - Genera "N" valores de contraseñas cifradas para "N" agentes de autenticación registrados en el inquilino.
7. El STS de Azure AD coloca la solicitud de validación de contraseña (que se compone del nombre de usuario y los valores de las contraseñas cifradas) en la cola de Service Bus específica del inquilino.
8. Puesto que los agentes de autenticación inicializados están conectados de forma persistente a la cola de Service Bus, uno de los agentes de autenticación disponibles recupera la solicitud de validación de contraseña.
9. El agente de autenticación busca el valor de la contraseña cifrada específico para su clave pública (mediante un identificador) y lo descifra con su clave privada.
10. El agente de autenticación intenta validar el nombre de usuario y la contraseña en el entorno local de Active Directory con [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) mediante el parámetro **dwLogonType** definido en **LOGON32_LOGON_NETWORK**. 
    - Se trata de la misma API que usa Servicios de federación de Active Directory (AD FS) para que los usuarios inicien sesión en un escenario de inicio de sesión federado.
    - Esta API se basa en el proceso de resolución estándar de Windows Server para buscar el controlador de dominio.
11. El agente de autenticación recibe un resultado de Active Directory como, por ejemplo: correcto, nombre de usuario o contraseña incorrectos o contraseña expirada.
12. El agente de autenticación reenvía el resultado al servicio STS de Azure AD por un canal HTTPS saliente autenticado mutuamente a través del puerto 443. La autenticación mutua usa el mismo certificado emitido anteriormente al agente de autenticación durante el registro.
13. STS de Azure AD verifica que este resultado se pone en correlación con la solicitud de inicio de sesión específica del inquilino.
14. STS de Azure AD continúa con el procedimiento de inicio de sesión según la configuración. Por ejemplo, si la validación de contraseña es correcta, el usuario podría tener que someterse a Multi-Factor Authentication o se le podría redirigir de nuevo a la aplicación.

## <a name="operational-security-of-the-authentication-agents"></a>Seguridad operativa de los agentes de autenticación

Para garantizar la seguridad operativa de la autenticación de paso a través, Azure AD renueva periódicamente los certificados de los agentes de autenticación. Azure AD desencadena las renovaciones. Estas renovaciones no se rigen por los propios agentes de autenticación.

![Seguridad operativa](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Para renovar la confianza de un agente de autenticación con Azure AD:

1. El agente de autenticación hace ping periódicamente a Azure AD (cada pocas horas) para comprobar si es el momento de renovar su certificado. 
    - Esta comprobación se realiza a través de un canal HTTPS autenticado mutuamente y usa el mismo certificado que se emitió durante el registro.
2. Si el servicio indica que es el momento de renovar, el agente de autenticación genera un nuevo par de claves: una clave pública y una clave privada.
    - Estas claves se generan con el cifrado estándar de 2048 bits de RSA.
    - La clave privada nunca abandona el servidor local.
3. El agente de autenticación realiza una solicitud de "renovación de certificado" a Azure AD a través de HTTPS, con los siguientes componentes incluidos en la solicitud:
    - El certificado existente que se recupera de la ubicación CERT_SYSTEM_STORE_LOCAL_MACHINE en el almacén de certificados de Windows. En este procedimiento no participa ningún administrador global, por lo que no se necesita ningún token de acceso en nombre del administrador global.
    - La clave pública generada en el paso 2.
    - Una solicitud de firma de certificado (CSR o solicitud de certificado). Esto sirve para solicitar un nuevo certificado de identidad digital que tenga Azure AD como entidad de certificación.
4. Azure AD valida el certificado existente en la solicitud de renovación de certificado. Después, verifica que la solicitud proviene de un agente de autenticación registrado en el inquilino.
5. Si el certificado existente sigue siendo válido, Azure AD firma un nuevo certificado de identidad digital y lo emite de nuevo al agente de autenticación. 
6. Si ha expirado el certificado existente, Azure AD elimina el agente de autenticación de lista de los agentes de autenticación registrados en el inquilino. A continuación, un administrador global debe instalar y registrar manualmente un nuevo agente de autenticación.
    - Use la CA raíz de Azure AD para firmar el certificado.
    - El sujeto del certificado (nombre distintivo o DN) se establece en su id. de inquilino, que es un GUID que identifica al inquilino de forma exclusiva. Es decir, el DN limita el certificado solo al inquilino.
6. Azure AD almacena la nueva clave pública del agente de autenticación en una instancia de Azure SQL Database, a la que solo Azure AD tiene acceso. Además, invalida la antigua clave pública asociada con el agente de autenticación.
7. El nuevo certificado (que se emitió en el paso 5) se almacena en el servidor en el almacén de certificados de Windows (concretamente, en la ubicación [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)).
    - Puesto que el procedimiento de renovación de confianza ocurre de manera no interactiva (sin la presencia del administrador global), el agente de autenticación ya no tiene acceso para actualizar el certificado existente en la ubicación CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Tenga en cuenta que el certificado de la ubicación CERT_SYSTEM_STORE_LOCAL_MACHINE no se elimina durante este procedimiento.
8. El nuevo certificado se utiliza para la autenticación a partir de este punto. Cada renovación posterior del certificado reemplaza al certificado de la ubicación CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Actualización automática de los agentes de autenticación

La aplicación del actualizador actualiza automáticamente el agente de autenticación cuando se lanza una nueva versión. Esta aplicación no controla las solicitudes de validación de contraseña del inquilino. 

Azure AD hospeda la nueva versión del software como un **paquete de Windows Installer (MSI)** firmado. El archivo MSI se firma mediante [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) con SHA256 como algoritmo de resumen. 

![Actualización automática](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Para actualizar automáticamente un agente de autenticación:

1. La aplicación actualizadora hace ping cada hora a Azure AD para comprobar si hay una nueva versión del agente de autenticación. 
    - Esta comprobación se realiza a través de un canal HTTPS autenticado mutuamente y usa el mismo certificado que se emitió durante el registro. El agente de autenticación y el actualizador comparten el certificado almacenado en el servidor.
2. Si hay disponible una nueva versión, Azure AD devuelve el archivo MSI firmado al actualizador.
3. El actualizador verifica si el archivo MSI está firmado por Microsoft.
4. El actualizador ejecuta el archivo MSI. Esta acción implica los pasos siguientes:

 > [!NOTE]
 > El actualizador funciona con privilegios del [sistema local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).

    - Detiene el servicio del agente de autenticación.
    - Instala la nueva versión del agente de autenticación en el servidor.
    - Reinicia el servicio del agente de autenticación.

>[!NOTE]
>Si tiene varios agentes de autenticación registrados en el inquilino, Azure AD no renueva sus certificados ni los actualiza al mismo tiempo. En su lugar, Azure AD lo hace gradualmente para garantizar la alta disponibilidad de las solicitudes de inicio de sesión.
>


## <a name="next-steps"></a>Pasos siguientes
- [Limitaciones actuales](active-directory-aadconnect-pass-through-authentication-current-limitations.md): conozca qué escenarios son compatibles y cuáles no.
- [Inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): poner en marcha la autenticación de paso a través de Azure AD.
- [Bloqueo inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configurar la funcionalidad de bloqueo inteligente en el inquilino para proteger las cuentas de usuario.
- [Cómo funciona](active-directory-aadconnect-pass-through-authentication-how-it-works.md): aprenda los conceptos básicos sobre cómo funciona la autenticación de paso a través de Azure AD.
- [Preguntas más frecuentes](active-directory-aadconnect-pass-through-authentication-faq.md): encuentre respuestas a las preguntas más frecuentes.
- [Solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): obtenga información acerca de cómo resolver problemas comunes relacionados con la característica de autenticación de paso a través.
- [SSO de conexión directa de Azure AD](active-directory-aadconnect-sso.md): obtenga más información sobre esta característica complementaria.
