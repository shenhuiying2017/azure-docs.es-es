---
title: "Automatización de Azure Blueprint para FedRAMP - Identificación y autenticación"
description: "Aplicaciones web para FedRAMP - Identificación y autenticación"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5ceac04f3f5c58ac5810dda6c99b40ebc8ad736f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="identification-and-authentication-ia"></a>Identificación y autenticación (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 Control IA-1

> [!NOTE]
> Estos controles se definen por NIST y Estados Unidos. Departamento de Comercio como parte de la publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Procedimientos y directivas de identificación y autenticación

**IA-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de identificación y autenticación que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; así como los procedimientos para facilitar la implementación de la directiva de identificación y autenticación asociada, y revisa y actualiza la directiva de identificación y autenticación actual [Asignación: frecuencia definida por la organización], y los procedimientos de directiva de identificación y autenticación [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos y la directiva de identificación y autenticación a nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 Control IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identificación y autenticación (usuarios de la organización)

**IA-2** El sistema de información identifica y autentica de forma unívoca a los usuarios de la organización (o procesos que actúan en nombre de los usuarios de la organización).

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las cuentas creadas por esta instancia de Azure Blueprint tienen identificadores únicos. Las cuentas integradas con identificadores no únicos se deshabilitan o se eliminan. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 Control IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificación y autenticación (usuarios de la organización) | Acceso de red a cuentas con privilegios

**IA-2 (1)** El sistema de información implementa la autenticación multifactor para el acceso de red a cuentas con privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de implementar la autenticación multifactor para el acceso de red a cuentas con privilegios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 Control IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificación y autenticación (usuarios de la organización) | Acceso de red a cuentas sin privilegios

**IA-2 (2)** El sistema de información implementa la autenticación multifactor para el acceso de red a cuentas sin privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de implementar la autenticación multifactor para el acceso de red a cuentas sin privilegios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 Control IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identificación y autenticación (usuarios de la organización) | Acceso local a cuentas con privilegios

**IA-2 (3)** El sistema de información implementa la autenticación multifactor para el acceso local a cuentas con privilegios.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso local a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no permite el acceso local a menos que se requiera acceso físico. El acceso de administrador local solo debe utilizarse para solucionar problemas en casos en los que el servidor de miembros tenga problemas de red y la autenticación del dominio no funcione. <br /> Azure implementa la autenticación multifactor para el acceso local a través de mecanismos de control de acceso necesarios para el acceso físico al entorno. Las salas dentro de los centros de datos de Azure que contienen todos los sistemas de infraestructura de Azure dentro del límite del sistema están restringidas a través de diversos mecanismos de seguridad física, incluyendo el requisito de acceso a tarjetas inteligentes corporativas y dispositivos biométricos. Ambas formas de autenticación son necesarias para el acceso físico a las ubicaciones del centro de datos Azure en el punto de entrada. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 Control IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identificación y autenticación (usuarios de la organización) | Acceso local a cuentas sin privilegios

**IA-2 (4)** El sistema de información implementa la autenticación multifactor para el acceso local a cuentas sin privilegios.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso local a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure considera que todas las cuentas de Microsoft Azure Government utilizadas por el personal de Microsoft Azure Government son cuentas con privilegio. La autenticación multifactor se implementa en todas las cuentas del personal de Microsoft Azure Government mediante el uso de tarjetas inteligentes y pines, que incluyen acceso local. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 Control IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identificación y autenticación (usuarios de la organización) | Autenticación de grupos

**IA-2 (5)** La organización requiere que las personas se autentiquen con un autenticador individual cuando se emplea un autenticador de grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No se habilita ninguna cuenta compartida o de grupo en los recursos implementados por esta instancia de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 Control IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identificación y autenticación (usuarios de la organización) | Acceso de red a cuentas con privilegios - Resistente a la reproducción

**IA-2 (8)** El sistema de información implementa mecanismos de autenticación resistente a la reproducción para el acceso de red a cuentas con privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso a los recursos implementados por esta instancia de Azure Blueprint está protegido contra los ataques de repetición gracias a la funcionalidad Kerberos integrada de Azure Active Directory, Active Directory y el sistema operativo Windows. En la autenticación de Kerberos, el autenticador que envía el cliente contiene datos adicionales, como una lista de IP cifrada, marcas de tiempo de clientes y la vigencia del vale. Si se reproduce un paquete, se comprueba la marca de tiempo. Si la marca de tiempo es anterior a un autenticador anterior, o la misma, el paquete se rechaza. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 Control IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identificación y autenticación (usuarios de la organización) | Acceso de red a cuentas sin privilegios - Resistente a la reproducción

**IA-2 (9)** El sistema de información implementa mecanismos de autenticación resistente a la reproducción para el acceso de red a cuentas sin privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso a los recursos implementados por esta instancia de Azure Blueprint está protegido contra los ataques de repetición gracias a la funcionalidad Kerberos integrada de Azure Active Directory, Active Directory y el sistema operativo Windows. En la autenticación de Kerberos, el autenticador que envía el cliente contiene datos adicionales, como una lista de IP cifrada, marcas de tiempo de clientes y la vigencia del vale. Si se reproduce un paquete, se comprueba la marca de tiempo. Si la marca de tiempo es anterior a un autenticador anterior, o la misma, el paquete se rechaza. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 Control IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identificación y autenticación (usuarios de la organización) | Acceso remoto - Dispositivo independiente

**IA-2 (11)** El sistema de información implementa la autenticación multifactor para el acceso remoto a cuentas con privilegios y sin privilegios de tal manera que uno de los factores lo proporciona un dispositivo independiente del sistema que obtiene acceso y el dispositivo cumple con [Asignación: requisitos de seguridad del mecanismo definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de implementar la autenticación multifactor para el acceso de manera remota a los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 Control IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identificación y autenticación (usuarios de la organización) | Aceptación de credenciales PIV

**IA-2 (12)** El sistema de información acepta y comprueba electrónicamente las credenciales de verificación de identidad personal (PIV).

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de aceptar y comprobar las credenciales de la comprobación de identidad personal (PIV). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 Control IA-3

#### <a name="device-identification-and-authentication"></a>Identificación y autenticación de dispositivos

**IA-3** El sistema de información identifica y autentica de forma unívoca [Asignación: dispositivos específicos definidos por la organización o tipos de dispositivos] antes de establecer una conexión [Selección (uno o más): local, remota, red].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de implementar la identificación y autenticación del dispositivo. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 Control IA-4.a

#### <a name="identifier-management"></a>Administración de identificadores

**IA 4.a** La organización administra los identificadores del sistema de información al recibir autorización de [Asignación: personal o roles definidas por la organización] para asignar un identificador de persona, grupo, rol o dispositivo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar los identificadores (es decir, personas, grupos, roles y dispositivos) de los recursos del cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 Control IA-4.b

#### <a name="identifier-management"></a>Administración de identificadores

**IA 4.b** La organización administra los identificadores del sistema de información mediante la selección de un identificador que identifica a una persona, grupo, rol o dispositivo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint solicita durante la implementación identificadores especificados por el cliente para cuentas individuales.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 Control IA-4.c

#### <a name="identifier-management"></a>Administración de identificadores

**IA 4.c** La organización administra los identificadores del sistema de información mediante la asignación del identificador a la persona, grupo, rol o dispositivo deseado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar los identificadores (es decir, personas, grupos, roles y dispositivos) de los recursos del cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 Control IA-4.d

#### <a name="identifier-management"></a>Administración de identificadores

**IA-4.d** La organización administra los identificadores del sistema de información al impedir la reutilización de identificadores para [Asignación: período definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A Active Directory y a las cuentas locales del sistema operativo Windows se les asigna un identificador de seguridad único (SID). A las cuentas de Azure Active Directory se les asigna un ID de objeto único global. Estos identificadores únicos no están sujetos a reutilización. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 Control IA-4.e

#### <a name="identifier-management"></a>Administración de identificadores

**IA-4.e** La organización administra los identificadores del sistema de información al deshabilitar el identificador después de [Asignación: período de inactividad definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa una tarea programada para que Active Directory deshabilite automáticamente las cuentas después de 35 días de inactividad. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 Control IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Administración de identificadores | Identificar estado de usuario

**IA-4 (4)** La organización administra identificadores individuales al identificar unívocamente a cada persona como [Asignación: característica definida por la organización que identifica el estado individual].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory y Active Directory admiten la señalización de contratistas, proveedores y otros tipos de usuarios mediante las convenciones de nomenclatura aplicadas a sus identificadores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 Control IA-5.a

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.a** La organización administra los autenticadores del sistema de información mediante la comprobación, como parte de la distribución inicial del autenticador, de la identidad del individuo, grupo, rol o dispositivo que recibe al autenticador.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar los autenticadores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 Control IA-5.b

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.b** La organización administra los autenticadores del sistema al establecer el contenido inicial del autenticador para los autenticadores definidos por la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todo el contenido inicial del autenticador para las cuentas creadas por esta instancia de Azure Blueprint cumple con los requisitos establecidos en IA-5 (1) comprobado cuando lo especifique el cliente durante la implementación.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 Control IA-5.c

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.c** La organización administra los autenticadores de los sistemas de información al asegurar que los autenticadores tienen suficiente seguridad del mecanismo para el uso previsto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los autenticadores utilizados por esta instancia de Azure Blueprint cumplen con los requisitos de seguridad requeridos por FedRAMP. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 Control IA-5.d

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.d** La organización administra los autenticadores del sistema de información mediante el establecimiento e implementación de procedimientos administrativos para la distribución inicial del autenticador, para los autenticadores perdidos o comprometidos o dañados, y para revocar autenticadores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar los autenticadores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 Control IA-5.e

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.e** La organización administra los autenticadores del sistema de información al cambiar el contenido predeterminado de los autenticadores antes de la instalación del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todos los autenticadores para los componentes de esta instancia de Azure Blueprint se han cambiado de sus valores predeterminados. Los autenticadores los especifica el cliente durante la implementación de esta solución. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 Control IA-5.f

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.f** La organización administra los autenticadores del sistema de información al establecer las restricciones de duración mínimas y máximas, así como las condiciones de reutilización para los autenticadores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar los autenticadores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 Control IA-5.g

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.g** La organización administra los autenticadores del sistema de información al cambiar o actualizar los autenticadores [Asignación: período definido por la organización por tipo de autenticador].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Se establece una directiva de grupo y se configura para implementar restricciones de vigencia de la contraseña (60 días). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 Control IA-5.h

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.h** La organización administra los autenticadores del sistema de información al proteger el contenido del autenticador de la divulgación y modificación no autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio Key Vault para proteger el contenido del autenticador de la divulgación y modificación no autorizadas. Los siguientes autenticadores se almacenan en una instancia de Key Vault: la contraseña de Azure para la cuenta de implementación, la contraseña de administrador de la máquina virtual, la contraseña de la cuenta de servicio de SQL Server. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 Control IA-5.i

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.i** La organización administra los autenticadores del sistema de información al exigir a las personas que tomen medidas de seguridad específicas, y las implementen en los dispositivos, para proteger los autenticadores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio Key Vault para proteger el contenido del autenticador de la divulgación y modificación no autorizadas. Los siguientes autenticadores se almacenan en una instancia de Key Vault: la contraseña de Azure para la cuenta de implementación, la contraseña de administrador de la máquina virtual, la contraseña de la cuenta de servicio de SQL Server. Key Vault cifra claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos y contraseñas) a través del uso de claves que están protegidas por módulos de seguridad de hardware (HSM). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 Control IA-5.j

#### <a name="authenticator-management"></a>Administración de autenticadores

**IA-5.j** La organización administra los autenticadores del sistema de información mediante el cambio de autenticadores para las cuentas de grupo o rol cuando cambia la pertenencia a esas cuentas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No se habilita ninguna cuenta compartida o de grupo en los recursos implementados por esta instancia de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800-53 Control IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>Administración de autenticadores | Autenticación basada en contraseña

**IA-5 (1).a** El sistema de información, para la autenticación basada en contraseña, impone una complejidad mínima de la contraseña de [Asignación: requisitos definidos por la organización para la distinción de mayúsculas y minúsculas, número de caracteres, combinación de letras mayúsculas y minúsculas, números y caracteres especiales, incluidos los requisitos mínimos para cada tipo].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Se establece y configura una directiva de grupo para aplicar los requisitos de complejidad de contraseñas para las cuentas locales de máquinas virtuales y las cuentas de AD.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800-53 Control IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>Administración de autenticadores | Autenticación basada en contraseña

**IA-5 (1).b** El sistema de información, para la autenticación basada en contraseña, impone al menos el siguiente número de caracteres modificados cuando se crean nuevas contraseñas: [Asignación: número definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear la autenticación basada en contraseña en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800-53 Control IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>Administración de autenticadores | Autenticación basada en contraseña

**IA-5 (1).c** El sistema de información, para la autenticación basada en contraseña, almacena y transmite solamente contraseñas protegidas criptográficamente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory se utiliza para garantizar que todas las contraseñas se protegidas mediante criptografía cuando se almacenan y se transmiten. A las contraseñas almacenadas por Active Directory y localmente en máquinas virtuales Windows implementadas se les aplica automáticamente un algoritmo hash como parte de la funcionalidad de seguridad integrada. Las sesiones de autenticación de Escritorio remoto se protegen mediante TLS para asegurar que los autenticadores estén protegidos cuando se transmiten. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800-53 Control IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>Administración de autenticadores | Autenticación basada en contraseña

**IA-5 (1).d** El sistema de información, para la autenticación basada en contraseña, impone restricciones de la duración mínima y máxima de la contraseña de [Asignación: números definidos por la organización para la duración mínima y máxima].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Se establece y se configura una directiva de grupo para aplicar restricciones a las contraseñas que impongan restricciones de duración mínima (1 día) y máxima (60 días) para las cuentas locales y las cuentas de AD. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800-53 Control IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>Administración de autenticadores | Autenticación basada en contraseña

**IA-5 (1).e** El sistema de información, para la autenticación basada en contraseña, prohíbe la reutilización de las contraseñas para las generaciones de [Asignación: número definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Se establece una directiva de grupo y se configura para aplicar restricciones a las condiciones de reutilización (24 contraseñas) para cuentas locales y cuentas de AD. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800-53 Control IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>Administración de autenticadores | Autenticación basada en contraseña

**IA-5 (1).f** El sistema de información, para la autenticación basada en contraseña, permite el uso de una contraseña temporal para los inicios de sesión del sistema con un cambio inmediato a una contraseña permanente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory se utiliza para administrar el acceso de control al sistema de información. Cada vez que se crea una cuenta o se genera una contraseña temporal, Azure Active Directory se emplea para requerir que el usuario cambie la contraseña en el siguiente inicio de sesión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 Control IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>Administración de autenticadores | Autenticación basada en PKI

**IA-5 (2).a** El sistema de información, para la autenticación basada en PKI, valida las certificaciones mediante la construcción y comprobación de una ruta de certificación hacia un marcador de confianza aceptado, que incluye la comprobación de la información sobre el estado del certificado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear la autenticación basada en PKI en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800-53 Control IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>Administración de autenticadores | Autenticación basada en PKI

**IA-5 (2).b** El sistema de información, para la autenticación basada en PKI, impone el acceso autorizado a la clave privada correspondiente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear la autenticación basada en PKI en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 Control IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>Administración de autenticadores | Autenticación basada en PKI

**IA-5 (2).c** El sistema de información, para la autenticación basada en PKI, asigna la identidad autenticada a la cuenta del individuo o grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear la autenticación basada en PKI en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800-53 Control IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>Administración de autenticadores | Autenticación basada en PKI

**IA-5 (2).d** El sistema de información, para la autenticación basada en PKI, implementa una caché local de datos de revocación para admitir la detección y validación de rutas de acceso en caso de imposibilidad de acceder a la información de revocación a través de la red.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear la autenticación basada en PKI en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 Control IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Administración de autenticadores | Registro de terceros en persona o de confianza

**IA-5 (3)** La organización requiere que el proceso de registro que reciba [Asignación: tipos de autenticación definidos por la organización o autenticadores específicos] se lleve a cabo [Selección: en persona; por un tercero de confianza] antes de [Asignación: autoridad de registro definida por la organización] con la autorización de [Asignación: personal o roles definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de registrar los autenticadores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 Control IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Administración de autenticadores | Compatibilidad automatizada para la determinación de la seguridad de la contraseña

**IA-5 (4)** La organización emplea herramientas automatizadas para determinar si los autenticadores de contraseñas son lo suficientemente seguros como para satisfacer los [Asignación: requisitos definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las cuentas de usuario implementadas con esta instancia de Azure Blueprint incluyen cuentas de usuario locales y de AD. Ambas proporcionan mecanismos que obligan al cumplimiento de los requisitos de contraseña establecidos para crear una contraseña inicial y durante los cambios de contraseña. Azure Active Directory es la herramienta automatizada que se emplea para determinar si los autenticadores de contraseña son suficientemente seguros como para satisfacer las restricciones de longitud, complejidad, rotación y duración de contraseña establecidas en IA-5 (1). Azure Active Directory garantiza que la seguridad del autenticador de contraseñas durante la creación cumple estos estándares. Las contraseñas especificadas por el cliente que se utilizan para implementar esta solución se comprueban para cumplir con los requisitos de seguridad de las contraseñas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 Control IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Administración de autenticadores | Protección de autenticadores

**IA-5 (6)** La organización protege a los autenticadores de acuerdo con la categoría de seguridad de la información a la que el uso del autenticador permite el acceso.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de proteger los autenticadores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 Control IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Administración de autenticadores | Autenticadores estáticos no integrados sin cifrar

**IA-5 (7)** La organización se asegura de que los autenticadores estáticos no cifrados no se inserten en aplicaciones o en scripts de acceso ni se almacenen en las teclas de función.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay uso de autenticadores estáticos no cifrados insertados en aplicaciones, scripts de acceso o teclas de función implementados por esta instancia de Azure Blueprint. Cualquier script o aplicación que utilice un autenticador realiza una llamada a un contenedor de Azure Key Vault antes de cada uso. El acceso a los contenedores de almacén Azure Key Vault se audita, lo que permite la detección de infracciones de esta prohibición si se usa una cuenta de servicio para acceder a un sistema sin una llamada correspondiente al contenedor de Azure Key Vault. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 Control IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Administración de autenticadores | Varias cuentas del sistema de información

**IA-5 (8)** La organización implementa [Asignación: medidas de seguridad definidas por la organización] para administrar el riesgo de pérdida de confidencialidad debido a que las personas tienen cuentas en varios sistemas de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en las medidas de seguridad de nivel empresarial para administrar el riesgo asociado con las personas que tienen cuentas en varios sistemas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 Control IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Administración de autenticadores | Autenticación basada en token de hardware

**IA-5 (11)** El sistema de información, para la autenticación basada en token de hardware, emplea mecanismos que satisfacen los [Asignación: requisitos de calidad de token definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear mecanismos para satisfacer los requisitos de calidad de autenticación basados en token de hardware. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 Control IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Administración de autenticadores | Expiración de autenticadores almacenados en caché

**IA-5 (13)** El sistema de información prohíbe el uso de autenticadores almacenados en caché después de [Asignación: período definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Ningún recurso implementado por esta instancia de Azure Blueprint está configurado para permitir el uso de autenticadores almacenados en caché. La autenticación para las máquinas virtuales implementadas requiere que se introduzca un autenticador en el momento de la autenticación. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 Control IA-6

#### <a name="authenticator-feedback"></a>Comentarios de autenticador

**IA-6** El sistema de información oculta los comentarios de la información de autenticación durante el proceso de autenticación para proteger la información de posibles ataques a vulnerabilidades o uso por personas no autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso a los recursos implementados por esta instancia de Azure Blueprint es a través del Escritorio remoto y se basa en la autenticación de Windows. El comportamiento predeterminado de las sesiones de autenticación de Windows enmascara las contraseñas cuando se introducen durante una sesión de autenticación.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 Control IA-7

#### <a name="cryptographic-module-authentication"></a>Autenticación del módulo criptográfico

**IA-7** El sistema de información implementa mecanismos para la autenticación a un módulo criptográfico que cumple con los requisitos de las leyes federales aplicables, órdenes ejecutivas, directivas, políticas, reglamentos, normas y guías para dicha autenticación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint utiliza Autenticación de Windows, Escritorio remoto y BitLocker. Estos componentes se pueden configurar para que se basen en los módulos criptográficos validados de FIPS 140. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 Control IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identificación y autenticación (usuarios que no pertenecen a la organización)

**IA-8** El sistema de información identifica y autentica de forma unívoca a los usuarios que no pertenecen a la organización (o procesos que actúan en nombre de usuarios que no pertenecen a la organización).

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de identificar y autenticar a los usuarios que no pertenecen a la organización que acceden a los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 Control IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identificación y autenticación (usuarios que no pertenecen a la organización) | Aceptación de credenciales PIV de otras agencias

**IA-8 (1)** El sistema de información acepta y comprueba electrónicamente las credenciales de verificación de identidad personal (PIV) de otras agencias federales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de aceptar y comprobar las credenciales de la comprobación de identidad personal (PIV) emitidas por otras agencias federales. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 Control IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identificación y autenticación (usuarios que no pertenecen a la organización) | Aceptación de credenciales de terceros

**IA-8 (2)** El sistema de información solo acepta credenciales de terceros aprobadas por el FICAM.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de aceptar únicamente las credenciales de terceros que hayan aprobado la iniciativa de las soluciones de marco de confianza de Federal Identity, Credential, and Access Management (FICAM). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 Control IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identificación y autenticación (usuarios que no pertenecen a la organización) | Uso de productos aprobados por FICAM

**IA-8 (3)** La organización emplea solo componentes del sistema de información aprobados por FICAM en [Asignación: sistemas de información definidos por la organización] para aceptar credenciales de terceros.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear solo los recursos aprobados por la iniciativa de las soluciones de marco de confianza de Federal Identity, Credential and Access Management (FICAM) para aceptar credenciales de terceros. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 Control IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identificación y autenticación (usuarios que no pertenecen a la organización) | Uso de perfiles emitidos por FICAM

**IA-8 (4)** El sistema de información se ajusta a los perfiles emitidos por FICAM.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de cumplir con los perfiles emitidos por la iniciativa de las soluciones de marco de confianza de Federal Identity, Credential, and Access Management (FICAM). |
| **Proveedor (Microsoft Azure)** | No aplicable |
