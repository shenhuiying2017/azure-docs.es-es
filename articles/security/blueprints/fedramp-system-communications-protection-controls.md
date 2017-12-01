---
title: "Automatización del programa Azure Blueprint de FedRAMP: protección del sistema y de las comunicaciones"
description: "Aplicaciones web para FedRAMP: protección del sistema y de las comunicaciones"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 02e2d07eb29d0d5d436afed1cdab4fe710674a8c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-communications-protection-sc"></a>Protección del sistema y de las comunicaciones

> [!NOTE]
> Estos controles se definen por NIST y Estados Unidos. Departamento de Comercio como parte de publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 Control SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Directiva y procedimientos para la protección del sistema y de las comunicaciones

**SC-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de protección del sistema y de las comunicaciones que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de administración, la coordinación entre las entidades de la organización y el cumplimiento; y los procedimientos para facilitar la implementación de la directiva de protección del sistema y de las comunicaciones y los controles asociados de protección del sistema y de las comunicaciones; y revisa y actualiza la directiva de protección del sistema y de las comunicaciones actual [Asignación: frecuencia definida por la organización]; y los procedimientos de protección del sistema y de las comunicaciones [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva y los procedimientos empresariales de protección del sistema y de las comunicaciones del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 Control SC-2

#### <a name="application-partitioning"></a>Creación de particiones en la aplicación

**SC-2** El sistema de información separa la funcionalidad del usuario (incluidos los servicios de la interfaz de usuario) de la funcionalidad de administración del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este documento Azure Blueprint separa la funcionalidad del usuario de la funcionalidad de administración del sistema mediante la aplicación de controles de acceso lógico y arquitectura del sistema. La funcionalidad del usuario se limita a las interfaces de aplicación web implementadas por el cliente. Las interfaces para la funcionalidad de administración del sistema son independientes de las interfaces de usuario. Toda la conectividad de administración tiene lugar a través de un host bastión (jumpbox) seguro ubicado en una subred de administración con reglas del grupo de seguridad de red para limitar el acceso a los recursos de producción según corresponda. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 Control SC-3

#### <a name="security-function-isolation"></a>Aislamiento de la función de seguridad

**SC-3** El sistema de información aísla las funciones de seguridad de las funciones de otra índole.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint ejecutan sistemas operativos Windows. Windows mantiene dominios de ejecución independientes para cada proceso en ejecución mediante la asignación de un espacio privado de direcciones virtuales a cada proceso. Además, la solución implementa una arquitectura y controles de acceso diseñados para aislar la funcionalidad de seguridad donde corresponda. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 Control SC-4

#### <a name="information-in-shared-resources"></a>Información en recursos compartidos

**SC-4** El sistema de información evita la transferencia de información accidental y no autorizada a través de recursos del sistema compartidos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint ejecutan sistemas operativos Windows. El sistema operativo administra los recursos (por ejemplo, memoria, almacenamiento) de manera que la información sea accesible solo para los usuarios y los roles con los permisos adecuados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 Control SC-5

#### <a name="denial-of-service-protection"></a>Protección ante la denegación de servicio

**SC-5** El sistema de información protege contra los efectos (o los limita) de los siguientes tipos de ataques por denegación de servicio: [Asignación: tipos definidos por la organización de ataques por denegación de servicio o referencias a fuentes para tal información] empleando [Asignación: medidas de seguridad definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa una instancia de Application Gateway que incluye un firewall de aplicaciones web y funciones de equilibrio de carga. Las máquinas virtuales implementadas que brindan soporte al nivel web, el nivel de base de datos y Active Directory se implementan en un conjunto de disponibilidad escalable. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 Control SC-6

#### <a name="resource-availability"></a>Disponibilidad de recursos

**SC-6** El sistema de información protege la disponibilidad de recursos asignando [Asignación: recursos definidos por la organización] por [Selección (uno o más); prioridad; cuota; [Asignación: medidas de seguridad definidas por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint ejecutan sistemas operativos Windows. Cada proceso de Windows proporciona los recursos necesarios para ejecutar un programa. La prioridad de recursos se administra por parte del sistema operativo. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 Control SC-7.a

#### <a name="boundary-protection"></a>Protección de límites

**SC-7.a** El sistema de información supervisa y controla las comunicaciones en el límite externo del sistema y en límites internos clave dentro del sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa una instancia de Application Gateway y un equilibrador de carga y configura reglas de grupos de seguridad de red para controlar las conmutaciones en los límites externos y entre subredes internas. La instancia de Application Gateway, el equilibrador de carga y los registros de diagnóstico y evento del grupo seguridad de red se recopilan por parte de OMS Log Analytics para permitir la supervisión del cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 Control SC-7.b

#### <a name="boundary-protection"></a>Protección de límites

**SC-7.b** El sistema de información implementa subredes para componentes del sistema accesibles públicamente que están [Selección: físicamente; lógicamente] separados de las redes internas de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa los recursos en una arquitectura con una subred web independiente, la subred de la base de datos, la subred de Active Directory y la subred de administración. Las subredes se separan de forma lógica mediante las reglas del grupo de seguridad de red aplicadas a las subredes individuales para restringir el tráfico entre subredes unicamente a lo necesario para la funcionalidad de administración y del sistema (por ejemplo, el tráfico externo no puede acceder a la base de datos, la administración o las subredes de Active Directory). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 Control SC-7.c

#### <a name="boundary-protection"></a>Protección de límites

**SC-7.c** El sistema de información se conecta a redes o sistemas de información externos únicamente a través de interfaces administradas que constan de dispositivos de protección de límites organizados con arreglo a una arquitectura de seguridad de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa una instancia de Application Gateway para administrar las conexiones externas a una aplicación web implementada por el cliente. Las conexiones externas para el acceso de administración se limitan a un host bastión/jumpbox implementado en una subred de administración con las reglas de seguridad de red aplicadas para restringir las conexiones externas a direcciones IP autorizadas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 Control SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Protección de límites | Puntos de acceso

**SC-7 (3)** La organización limita el número de conexiones de red externas al sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa dos direcciones IP públicas: una asociada a la instancia de Application Gateway y la otra asociada al host bastión/jumpbox de administración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800-53 Control SC-7 (4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>Protección de límites | Servicios de telecomunicaciones externos

**SC-7 (4).a** La organización implementa una interfaz administrada para cada servicio de telecomunicaciones externo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa dos direcciones IP públicas: una asociada a la instancia de Application Gateway y la otra asociada al host bastión/jumpbox de administración. La administración de estas interfaces se habilita a través de redes definidas por software. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800-53 Control SC-7 (4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>Protección de límites | Servicios de telecomunicaciones externos

**SC-7 (4).b** La organización establece una directiva de flujo de tráfico para cada interfaz administrada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa dos direcciones IP públicas: una asociada a la instancia de Application Gateway y la otra asociada al host bastión/jumpbox de administración. La administración de estas interfaces se habilita a través de redes definidas por software. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800-53 Control SC-7 (4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>Protección de límites | Servicios de telecomunicaciones externos

**SC-7 (4).c** La organización protege la confidencialidad y la integridad de la información que se transmite a través de cada interfaz.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La puerta de enlace de la aplicación web implementada por esta solución de Azure Blueprint se configura con un agente de escucha HTTPS, lo que genera confidencialidad e integridad para las sesiones de comunicación. Las conexiones del Escritorio remoto al jumpbox también se cifran para proporcionar confidencialidad e integridad. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800-53 Control SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>Protección de límites | Servicios de telecomunicaciones externos

**SC-7 (4).d** La organización documenta cada excepción a la directiva de flujo de tráfico con una necesidad de apoyo de misión o empresa y la duración de esa necesidad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los clientes no son responsables de las operaciones del centro de datos (incluidos los servicios de telecomunicaciones). Todos los servicios de telecomunicaciones se proporcionan y administran por Microsoft Azure. Este control se hereda de Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800-53 Control SC-7 (4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>Protección de límites | Servicios de telecomunicaciones externos

**SC-7 (4).e** La organización revisa las excepciones a la directiva de flujo de tráfico [Asignación: frecuencia definida por la organización] y quita las excepciones que ya no son compatibles con una necesidad explícita de misión o empresa.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los clientes no son responsables de las operaciones del centro de datos (incluidos los servicios de telecomunicaciones). Todos los servicios de telecomunicaciones se proporcionan y administran por Microsoft Azure. Este control se hereda de Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 Control SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Protección de límites | Denegar de forma predeterminada y permitir mediante excepción

**SC-7 (5)** El sistema de información en interfaces administradas deniega el tráfico de las comunicaciones de red de forma predeterminada y permite el tráfico de las comunicaciones de red mediante excepción (es decir, deniega todo, permite mediante excepción).

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los conjuntos de reglas aplicados a los grupos de seguridad de red implementados por esta solución de Azure Blueprint se configuran mediante un esquema de denegación predeterminada. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 Control SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Protección de límites | Evitar la tunelización dividida en los dispositivos remotos

**SC-7 (7)** El sistema de información, junto con un dispositivo remoto, impide que el dispositivo establezca conexiones no remotas con el sistema y se comunique simultáneamente mediante otra conexión con recursos en redes externas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Es posible que la directiva de configuración de dispositivos remotos a nivel empresarial del cliente aborde la tunelización dividida. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 Control SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Protección de límites | Enrutar el tráfico a servidores proxy autenticados

**SC-7 (8)** El sistema de información enruta [Asignación: tráfico de comunicaciones internas definidas por la organización] a [Asignación: redes externas definidas por la organización] a través de servidores proxy autenticados en interfaces administradas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de enrutar la información definida por el cliente a través de un proxy autenticado a una red externa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 Control SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Protección de límites | Evitar la exfiltración no autorizada

**SC-7 (10)** La organización impide la exfiltración no autorizada de información a través de interfaces administradas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de impedir de exfiltración no autorizada de información a través de interfaces administradas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 Control SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Protección de límites | Protección basada en host

**SC-7 (12)** La organización implementa [Asignación: mecanismos de protección de límites basados en host definidos por la organización] en [Asignación: componentes del sistema de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint se configuran con un firewall basado en host habilitado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 Control SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Protección de límites | Aislamiento de herramientas de seguridad/mecanismos/componentes de soporte

**SC-7 (13)** La organización aísla [Asignación: herramientas de seguridad, mecanismos y componentes de soporte de información definida por la organización] de otros componentes del sistema de información interno mediante la implementación de subredes separadas físicamente con interfaces administradas para otros componentes del sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa recursos en una arquitectura con una subred de administración independiente para la implementación del cliente de herramientas y componentes de soporte para la seguridad de información. Las subredes se separan lógicamente mediante reglas del grupo de seguridad de red. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 Control SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Protección de límite | Error seguro

**SC-7 (18)** El sistema de información provoca un error seguro en caso de que se produzca una deficiencia operativa del dispositivo de protección de límites.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún dispositivo físico de protección de límites dentro del ámbito de sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa servidores de puerta de enlace y VPN de SSL redundantes y separados geográficamente. Cuando se produce una deficiencia en un sistema de puerta de enlace, este provoca un error seguro y se restringe el acceso al entorno. A fin de establecer una conexión con el entorno de Microsoft Azure, un usuario debe establecer una conexión independiente con un servidor de puerta de enlace activo administrado por Microsoft Azure. <br /> Además, si se produce un error en los dispositivos de red de Microsoft Azure (incluidos los enrutadores perimetrales, los enrutadores de acceso, los equilibradores de carga, los conmutadores de agregación y TOR), se desconecta el circuito afectado, provocando así un error seguro. Un error del dispositivo de red de Microsoft Azure no puede provocar que información ajena al sistema entre en el dispositivo, así como un error no puede permitir la liberación de información no autorizada. La redundancia incorporada permite que los activos de Microsoft Azure generen un error sin que eso afecte a la disponibilidad. |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 Control SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Protección de límites | Segregación/aislamiento dinámico

**SC-7 (20)** El sistema de información ofrece la posibilidad de aislar o segregar dinámicamente [Asignación: componentes del sistema de información definidos por la organización] de otros componentes del sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de garantizar que el sistema tiene la capacidad de aislar dinámicamente los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 Control SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Protección de límites | Aislamiento de componentes del sistema de información

**SC-7 (21)** La organización utiliza mecanismos de protección de límites para separar [Asignación: componentes del sistema de información definidos por la organización] en apoyo de [Asignación: funciones empresariales o de misión definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa los recursos en una arquitectura con una subred web independiente, la subred de la base de datos, la subred de Active Directory y la subred de administración. Las subredes se separan de forma lógica mediante las reglas del grupo de seguridad de red aplicadas a las subredes individuales para restringir el tráfico entre subredes unicamente a lo necesario para la funcionalidad de administración y del sistema. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 Control SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Integridad y confidencialidad de transmisión

**SC-8** El sistema de información protege la [Selección (una o más): confidencialidad, integridad] de la información transmitida.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La implementación de SI-8 (1) cumple con este requisito de control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 Control SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Integridad y confidencialidad de la transmisión | Protección criptográfica o protección física alternativa

**SC-8 (1)** El sistema de información implementa mecanismos criptográficos para [Selección (uno o más): impedir revelación de información no autorizada, detectar cambios en la información] durante la transmisión a menos que haya otro sistema de protección mediante [Asignación: medidas físicas alternativas definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint configura los recursos para comunicarse solo mediante protocolos seguros. El componente WAF de Application Gateway está configurado para aceptar comunicadores de usos externos a través de HTTPS/TLS y para comunicarse con el grupo de back-end solo a través de HTTPS/TLS. SQL Server está configurado para comunicarse solo a través de HTTPS/TLS. Los servicios de Escritorio remoto están configurados para usar conexiones seguras. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 Control SC-10

#### <a name="network-disconnect"></a>Desconexión de red

**SC-10** El sistema de información finaliza la conexión de red asociada a una sesión de comunicaciones al final de la sesión o después de [Asignación: período de tiempo definido por la organización] de inactividad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La autenticación para sesiones de Escritorio remoto se administra mediante Active Directory. Una vez que el acceso está deshabilitado para un usuario en Active Directory, las sesiones remotas se finalizan de inmediato. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 Control SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Establecimiento y administración de una clave criptográfica

**SC-12** La organización establece y administra las claves criptográficas para la criptografía requerida que se utiliza en el sistema de información de acuerdo con [Asignación: requisitos definidos por la organización para la generación, la distribución, el almacenamiento, el acceso y la destrucción de claves].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa una instancia de Azure Key Vault. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Azure Key Vault puede generar claves mediante una función de generación de claves del módulo de seguridad de hardware catalogada como 140-2 nivel 2 según el FIPS. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 Control SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Establecimiento y administración de una clave criptográfica | Disponibilidad

**SC-12 (1)** La organización mantiene la disponibilidad de la información en el caso de la pérdida de las claves criptográficas por los usuarios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Key Vault se usa para almacenar secretos y claves criptográficas utilizados en esta solución de Azure Blueprint. Key Vault simplifica el proceso de administración de claves para las claves que acceden a los datos y los cifran. Los siguientes autenticadores se almacenan en Key Vault: la contraseña de Azure para la cuenta de implementación, la contraseña de administrador de la máquina virtual y la contraseña de la cuenta de servicio de SQL Server. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 Control SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Establecimiento y administración de una clave criptográfica | Claves simétricas

**SC-12 (2)** La organización produce, controla y distribuye claves criptográficas simétricas mediante tecnología y procesos de administración de claves [Selección: compatibles con NIST FIPS; aprobados por NSA].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Key Vault se usa para producir, controlar y distribuir claves criptográficas. Azure Key Vault puede generar claves mediante una función de generación de claves del módulo de seguridad de hardware catalogada como 140-2 nivel 2 según el FIPS. Las claves se almacenan y administran en contenedores de cifrados de forma segura de Azure Key Vault. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 Control SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Establecimiento y administración de una clave criptográfica | Claves asimétricas

**SC-12 (3)** La organización produce, controla y distribuye claves criptográficas asimétricas mediante [Selección: tecnología y procesos de administración de claves aprobados por NSA; certificados o material de claves preposicionado aprobados de infraestructura de clave pública de clase 3; certificados y tokens de seguridad de hardware aprobados de infraestructura de clave pública de clase 3 o clase 4 que protegen la clave privada del usuario].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de producir, controlar y distribuir las claves criptográficas asimétricas (si se utilizan en los recursos implementados por el cliente). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 Control SC-13

#### <a name="cryptographic-protection"></a>Protección criptográfica

**SC-13** El sistema de información implementa [Asignación: usos criptográficos definidos por la organización y tipo de criptografía necesaria para cada uso] con arreglo a las leyes federales aplicables, las órdenes ejecutivas, las directivas, las regulaciones y las normativas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint utiliza Autenticación de Windows, Escritorio remoto y BitLocker. Estos componentes se pueden configurar para que se basen en los módulos criptográficos validados de FIPS 140. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 Control SC-15.a

#### <a name="collaborative-computing-devices"></a>Dispositivos informáticos de colaboración

**SC-15.a** El sistema de información prohíbe la activación remota de dispositivos informáticos de colaboración con las siguientes excepciones: [Asignación: excepciones definidas por la organización donde se permite la activación remota].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay dispositivos informáticos de colaboración implementados como parte de esta solución de Azure Blueprint. Nota: Hay dispositivos informáticos de colaboración físicos dentro del ámbito de sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 Control SC-15.b

#### <a name="collaborative-computing-devices"></a>Dispositivos informáticos de colaboración

**SC-15.b** El sistema de información proporciona una indicación explícita de uso a los usuarios presentes físicamente en los dispositivos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay dispositivos informáticos de colaboración implementados como parte de esta solución de Azure Blueprint. Nota: Hay dispositivos informáticos de colaboración físicos dentro del ámbito de sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 Control SC-17

#### <a name="public-key-infrastructure-certificates"></a>Certificados de infraestructura de clave pública

**SC-17** La organización emite certificados de clave pública en virtud de [Asignación: directiva de certificados definida por la organización] u obtiene certificados de clave pública de un proveedor de servicios aprobado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en una infraestructura de clave pública de nivel empresarial para la emisión de certificados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 Control SC-18.a

#### <a name="mobile-code"></a>Código móvil

**SC-18.a** La organización define tecnologías de código móvil y código móvil aceptable o no aceptable.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de protección de las comunicaciones y el sistema a nivel empresarial del cliente pueden definir código móvil aceptable o no aceptable. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 Control SC-18.b

#### <a name="mobile-code"></a>Código móvil

**SC-18.b** La organización establece las restricciones de uso y las instrucciones de implementación para las tecnologías de código móvil y el código móvil aceptable.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de protección de las comunicaciones y el sistema a nivel empresarial del cliente pueden establecer restricciones al uso de código móvil. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 Control SC-18.c

#### <a name="mobile-code"></a>Código móvil

**SC-18.c** La organización autoriza, supervisa y controla el uso de código móvil dentro del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en un proceso de nivel empresarial para la autorización, la supervisión y el control del uso de código móvil. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 Control SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voz sobre IP (VoIP)

**SC-19.a** La organización establece las restricciones de uso y las instrucciones de implementación para las tecnologías de voz sobre IP (VoIP) en función del potencial de provocar daños en el sistema de información si se utiliza de forma malintencionada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay tecnologías de voz sobre IP (VoIP) implementadas como parte de esta solución de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 Control SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voz sobre IP (VoIP)

**SC-19.b** La organización autoriza, supervisa y controla el uso de VoIP dentro del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay tecnologías de voz sobre IP (VoIP) implementadas como parte de esta solución de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 Control SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Servicio de resolución de direcciones/nombres seguro (origen de autoridad)

**SC-20.a** El sistema de información proporciona autenticación de origen de datos adicional y artefactos de verificación de integridad junto con los datos de resolución de nombres de autoridad que el sistema devuelve en respuesta a consultas externas de resolución de direcciones/nombres.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de un servicio de resolución de direcciones y nombres seguro. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 Control SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Servicio de resolución de direcciones/nombres seguro (origen de autoridad)

**SC-20.b** El sistema de información proporciona los medios para indicar el estado de seguridad de las zonas secundarias y (si el elemento secundario admite servicios de resolución segura) para habilitar la comprobación de una cadena de confianza entre dominios primarios y secundarios, cuando opera como parte de un espacio de nombres jerárquico y distribuido.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de un servicio de resolución de direcciones y nombres seguro. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 Control SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Servicio de resolución de direcciones/nombres seguro (resolución recursiva o en caché)

**SC-21** El sistema de información solicita y realiza la autenticación del origen de los datos y la verificación de la integridad de los datos en las respuestas de la resolución de direcciones/nombres que el sistema recibe de orígenes autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de configurar los recursos implementados por el cliente para solicitar y realizar la autenticación del origen de los datos y la verificación de la integridad de los datos en las respuestas de la resolución de direcciones/nombres recibidas de orígenes autorizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800-53 Control SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Arquitectura y aprovisionamiento para el servicio de resolución de direcciones/nombres

**SC-22** Los sistemas de información que proporcionan colectivamente el servicio de resolución de direcciones/nombres para una organización son tolerantes a los errores e implementan la separación de roles internos/externos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de garantizar que los sistemas que proporcionan servicios de resolución de direcciones para recursos implementados por el cliente son tolerantes a los errores e implementan la separación de roles internos/externos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 Control SC-23

#### <a name="session-authenticity"></a>Autenticidad de sesión

**SC-23** El sistema de información protege la autenticidad de las sesiones de comunicaciones.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso remoto a los recursos implementados por esta solución de Azure Blueprint, como Azure Portal, la conexión a Escritorio remoto y la puerta de enlace de aplicación web, está protegido mediante TLS. TLS proporciona autenticidad para las comunicaciones en el nivel de sesión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800-53 Control SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Autenticidad de sesión | Invalidar los identificadores de sesión en el cierre de sesión

**SC-23 (1)** El sistema de información invalida los identificadores de sesión en el cierre de sesión de usuario o en otra finalización de la sesión.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso remoto a los recursos implementados por esta solución de Azure Blueprint, como Azure Portal, la conexión a Escritorio remoto y la puerta de enlace de aplicación web, está protegido mediante TLS. Las sesiones de Azure Portal y Escritorio remoto invalidan los identificadores de sesión en el cierre de sesión. La invalidación de la sesión web se aplica a través de reglas de Azure Application Gateway y el firewall de aplicaciones web (WAF). El firewall de aplicaciones web aplica la afinidad de cookies por sesión y agota el tiempo de espera de la sesión después de 30 minutos (configurable tras la implementación a las reglas específicas de organización) de inactividad del cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 Control SC-24

#### <a name="fail-in-known-state"></a>Error en estado conocido

**SC-24** El sistema de información genera un error en un [Asignación: estado conocido definido por la organización] para [Asignación: tipos de errores definidos por la organización] preservando [Asignación: información de estado del sistema definida por la organización] en caso de error.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de garantizar que los recursos implementados por el cliente generan un error en estado conocido. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 Control SC-28

#### <a name="protection-of-information-at-rest"></a>Protección de la información en reposo

**SC-28** El sistema de información protege la [Selección (una o más): confidencialidad, integridad] de [Asignación: información en reposo definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La implementación de SC-28 (1) cumple con este requisito de control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800-53 Control SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Protección de la información en reposo | Protección criptográfica

**SC-28 (1)** El sistema de información implementa mecanismos criptográficos para evitar la divulgación y la modificación no autorizada de [Asignación: información definida por la organización] en [Asignación: componentes del sistema de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint implementan el cifrado de disco para proteger la confidencialidad y la integridad de la información en reposo. El cifrado del disco de Azure para Windows se implementa mediante la función BitLocker de Windows. SQL Server está configurado para usar el cifrado de datos transparente (TDE), que realiza el cifrado y descifrado de archivos de datos y de registro en tiempo real para proteger la información en reposo. TDE garantiza que los datos almacenados no hayan estado sujetos a un acceso no autorizado. El cliente puede optar por implementar controles adicionales a nivel de aplicación para proteger la integridad de la información almacenada. La confidencialidad y la integridad de todos los blobs de almacenamiento implementados mediante esta solución de Azure Blueprint están protegidas gracias al uso del cifrado del servicio Azure Storage (SSE). SSE protege los datos en reposo dentro de las cuentas de Azure Storage con el cifrado AES de 256 bits. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 Control SC-39

#### <a name="process-isolation"></a>Aislamiento de procesos

**SC-39** El sistema de información mantiene un dominio de ejecución independiente para cada proceso en ejecución.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint ejecutan sistemas operativos Windows. Windows mantiene dominios de ejecución independientes para cada proceso en ejecución mediante la asignación de un espacio privado de direcciones virtuales a cada proceso. |
| **Proveedor (Microsoft Azure)** | No aplicable |
