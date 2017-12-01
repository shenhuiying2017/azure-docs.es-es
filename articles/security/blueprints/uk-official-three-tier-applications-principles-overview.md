---
title: "Automatización de Azure Blueprint: Introducción a los principios de seguridad en la nube del National Cyber Security Centre"
description: "Automatización de Azure Blueprint: Introducción a los principios de seguridad en la nube del National Cyber Security Centre"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: jomolesk
ms.openlocfilehash: 0be18e2c2354ea8f766eb48db793c906e565a201
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>Introducción a los principios de seguridad en la nube del National Cyber Security Centre


> [!NOTE]
> Estos principios de seguridad los ha definido el National Cyber Security Centre (NCSC) del Reino Unido. Consulte la [documentación del NCSC](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) para obtener información sobre procedimientos de comprobación e instrucciones para cada principio de seguridad.



## <a name="ncsc-cloud-security-principle-1"></a>Principio 1 de seguridad en la nube del NCSC
### <a name="data-in-transit-protection"></a>Protección de datos en tránsito
Los datos de usuario que transitan entre redes se deben proteger adecuadamente frente a la manipulación y los ataques de interceptación.

Esto se debe lograr mediante una combinación de:

- protección de red: permite impedir al atacante la posibilidad de interceptar los datos
- cifrado: permite impedir al atacante la posibilidad de leer los datos


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Azure Blueprint configura los recursos para comunicarse solo mediante protocolos seguros. El componente WAF de Application Gateway está configurado para aceptar comunicadores de usos externos a través de HTTPS/TLS y para comunicarse con el grupo de back-end solo a través de HTTPS/TLS. Los servicios de Escritorio remoto están configurados para usar conexiones seguras. Se usa una VPN para proteger el tráfico web entre AppGateway y Azure. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Azure utiliza el protocolo de Seguridad de la capa de transporte (TLS) 1.2 estándar del sector con claves de cifrado 2048-bit RSA/SHA256, tal y como recomiendan CESG/NCSC, para cifrar las comunicaciones entre el cliente y la nube, e internamente entre sistemas de Azure y centros de datos. Por ejemplo, cuando los administradores usan Microsoft Azure Portal para administrar el servicio para la organización, los datos que se transmiten entre el portal y el dispositivo del administrador se envían a través de un canal TLS cifrado. Cuando un usuario de correo electrónico se conecta a Outlook.com mediante un explorador web estándar, la conexión HTTPS proporciona un canal seguro para recibir y enviar correos electrónicos.<br /> <br /> Azure ofrece a sus clientes una gama de opciones para proteger sus propios datos y el tráfico. Las características de administración de certificados integradas en Azure proporcionan a los administradores flexibilidad para configurar certificados y claves de cifrado para los sistemas de administración, los servicios individuales, las sesiones de Secure Shell (SSH), las conexiones de red privada virtual (VPN), las conexiones de escritorio remoto (RDP) y otras funciones. <br /><br /> Los desarrolladores pueden usar los proveedores de servicios criptográficos (CSP) integrados en Microsoft .NET Framework para acceder a los algoritmos del Estándar de cifrado avanzado (AES), junto con la funcionalidad de algoritmo hash seguro (SHA-2) para controlar tareas como la validación de firmas digitales. Azure Key Vault ayuda a los clientes a proteger las claves criptográficas y los secretos almacenándolos en módulos de seguridad de hardware (HSM). |


 ## <a name="ncsc-cloud-security-principle-2"></a>Principio 2 de seguridad en la nube del NCSC
### <a name="asset-protection-and-resilience"></a>Resistencia y protección de los recursos
Los datos de usuario y los recursos que los almacenan o procesan se deben proteger frente a alteraciones físicas, pérdidas, daño o incautación.

Los aspectos a tener en cuenta son:

1. Ubicación física y jurisdicción Legal
2. Seguridad del centro de datos
3. Protección de los datos en reposo
4. Limpieza de datos
5. Eliminación de equipos
6. Disponibilidad y resistencia física


 ## <a name="ncsc-cloud-security-principle-21"></a>Principio 2.1 de seguridad en la nube del NCSC
### <a name="physical-location-and-legal-jurisdiction"></a>Ubicación física y jurisdicción Legal
Para entender las circunstancias legales bajo las que se podría acceder a los datos sin su consentimiento debe identificar las ubicaciones en las que se almacenan, procesan y administran los datos.
También debe entender cómo se aplican los controles de manejo de datos dentro del servicio en relación con la legislación del Reino Unido. La protección inadecuada de los datos del usuario podría acarrear una sanción legal o un daño en la reputación.


**Responsabilidades:** `Customer`

> [!NOTE]
> Los servicios de Azure se implementan de forma regional y los clientes pueden configurar algunos de ellos para almacenar datos de clientes solo en una única región. Microsoft Azure proporciona una lista de centros de datos disponibles globalmente para ofrecer disponibilidad y confiabilidad a escala mundial. Todos los centros de datos de Azure se han certificado con la norma ISO/IEC 27001:2013. La geoárea del Reino Unido consta de 2 regiones: Sur de Reino Unido y Oeste de Reino Unido.

|||
|---|---|
| **Cliente** | Azure Blueprint solicita al administrador que determine en qué región desea implementar los recursos de Azure. Las regiones recomendadas para la implementación son Sur de Reino Unido u Oeste de Reino Unido. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | No aplicable |


 ## <a name="ncsc-cloud-security-principle-22"></a>Principio 2.2 de seguridad en la nube del NCSC
### <a name="datacentre-security"></a>Seguridad del centro de datos
Las ubicaciones que se usan para proporcionar servicios en la nube necesitan protección física contra accesos no autorizados, manipulación, robo o reconfiguración de los sistemas. Una protección inadecuada puede provocar la revelación, alteración o pérdida de los datos.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema de los centros de datos de Azure; las medidas de protección de la seguridad de los centros de datos se implementan y administran mediante Microsoft Azure. Este principio se hereda de Microsoft Azure. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementa este principio en nombre de los clientes. Microsoft Azure se ejecuta en instalaciones de Microsoft distribuidas geográficamente, que comparten espacio y utilidades con otros servicios en línea de Microsoft. Cada instalación está diseñada para ejecutarse 24 x 7 x 365 y emplea varias medidas estándar del sector para ayudar a proteger las operaciones frente a cortes de suministro eléctrico, intrusiones físicas e interrupciones de red. Estos centros de datos cumplen con los estándares del sector (por ejemplo, ISO 27001) relativos a la seguridad física y la disponibilidad. El personal de operaciones de Microsoft administra y supervisa estos centros. <br /> <br /> Los clientes de Azure pueden estar seguros de que los controles de seguridad físicos están en vigor en todos los centros de datos ya que Azure está certificado en todos los centros de datos en la norma ISO/IEC 27001: 2013. La geoárea del Reino Unido consta de 2 regiones: Sur de Reino Unido y Oeste de Reino Unido. |


 ## <a name="ncsc-cloud-security-principle-23"></a>Principio 2.3 de seguridad en la nube del NCSC
### <a name="data-at-rest-protection"></a>Protección de los datos en reposo
Para garantizar que los datos no están disponibles para terceros no autorizados con acceso físico a la infraestructura, los datos de usuario contenidos dentro del servicio deben estar protegidos independientemente de los medios de almacenamiento en los que estén contenidos. Sin las medidas adecuadas en vigor, los datos se pueden revelar accidentalmente en elementos multimedia descartados, perdidos o robados.


**Responsabilidades:** `Shared`

> [!NOTE]
> Todas las soluciones de cifrado que Microsoft Azure ofrece a sus clientes se integran fácilmente con Azure Key Vault, lo cual permite una administración fácil de las claves de cifrado.

|||
|---|---|
| **Cliente** | La confidencialidad y la integridad de todo el almacenamiento de blobs implementado mediante esta solución de Azure Blueprint están protegidas gracias al uso del cifrado del servicio Azure Storage (SSE). SSE protege los datos en reposo dentro de las cuentas de almacenamiento de Azure con el cifrado AES de 256 bits. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Azure facilita una amplia gama de funcionalidades de cifrado, por lo que ofrece a los clientes la flexibilidad de poder elegir la solución que mejor se ajusta a sus necesidades. Azure Key Vault le permite a los clientes mantener de forma fácil y rentable el control de las claves que usan los servicios y las aplicaciones en la nube para cifrar datos. Azure Disk Encryption permite a los clientes cifrar máquinas virtuales. El cifrado del servicio Azure Storage permite cifrar todos los datos que se colocan en la cuenta de almacenamiento de un cliente. |


 ## <a name="ncsc-cloud-security-principle-24"></a>Principio 2.4 de seguridad en la nube del NCSC
### <a name="data-sanitisation"></a>Limpieza de datos
El proceso de aprovisionamiento, migración y desaprovisionamiento de recursos no debería producir accesos no autorizados a los datos del usuario.

Un saneamiento inadecuado de los datos podría hacer que:

- El proveedor del servicio retenga los datos del usuario de forma indefinida
- Los datos de usuario sean accesibles para otros usuarios del servicio ya que se reutilizan recursos
- Se pierdan o se revelen los datos del usuario en elementos multimedia desechados, perdidos o robados.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure proporciona garantías contractuales con respecto a la eliminación de datos permanente en Azure. Por tanto, este principio se hereda de Microsoft Azure. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure sigue el proceso de eliminación NIST SP800-88r1 con una clasificación de datos que se ajusta a la de la norma FIPS-199 moderada. NIST proporciona un método de eliminación segura (a través de firmware de la unidad de disco duro) para unidades compatibles. En cuanto a las unidades de disco duro que no se pueden borrar, Microsoft las destruye y hace que la recuperación de información sea imposible (por ejemplo, desintegrándolas, triturándolas, pulverizándolas o incinerándolas). El método adecuado de eliminación viene determinado por el tipo de recurso. Los registros de la destrucción se conservan. Todos los servicios de Microsoft Azure usan servicios aprobados de almacenamiento de elementos multimedia y administración de los desechos. <br />  <br /> Cuando la suscripción a un servicio expira o termina, el cliente puede ponerse en contacto con Microsoft para solicitarle que: <br /><br /> (1) Deshabilite la cuenta del cliente y, a continuación, elimine los datos del cliente, o bien que <br /> (2) Conserve los datos almacenados del servicio en línea en una cuenta de funciones limitadas durante al menos 90 días después de la expiración o término de la suscripción del cliente (el "período de retención"), para que el cliente pueda extraer los datos. Después de la expiración del período de retención, Microsoft deshabilitará la cuenta del cliente y eliminará todos los datos. Las copias de seguridad o las almacenadas en la memoria caché se purgarán en un plazo de 30 días después del final del período de retención. |


 ## <a name="ncsc-cloud-security-principle-25"></a>Principio 2.5 de seguridad en la nube del NCSC
### <a name="equipment-disposal"></a>Eliminación de equipos
Una vez que el equipo usado para ofrecer un servicio llega al final de su vida útil, debe desecharse de modo que no ponga en peligro la seguridad del servicio ni la de los datos almacenados en él.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema de los centros de datos de Azure; los procedimientos de eliminación del equipo se implementan y administran mediante Microsoft Azure. Este principio se hereda de Microsoft Azure. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure implementa este principio en nombre de los clientes. Al final del ciclo de vida de un sistema, el personal operativo de Microsoft sigue unos rigurosos procedimientos de control de los datos y de eliminación del hardware que garantizan que ningún elemento del hardware que contenga datos del cliente estará disponible para terceros que no son de confianza. Microsoft Azure sigue el proceso de eliminación NIST SP800-88r1 con una clasificación de datos que se ajusta a la de la norma FIPS-199 moderada. NIST proporciona un método de eliminación segura (a través de firmware de la unidad de disco duro) para unidades compatibles. En cuanto a las unidades de disco duro que no se pueden borrar, Microsoft las destruye y hace que la recuperación de información sea imposible (por ejemplo, desintegrándolas, triturándolas, pulverizándolas o incinerándolas). El método adecuado de eliminación viene determinado por el tipo de recurso. Los registros de la destrucción se conservan. Todos los servicios de Microsoft Azure usan servicios aprobados de almacenamiento de elementos multimedia y administración de los desechos. |


 ## <a name="ncsc-cloud-security-principle-26"></a>Principio 2.6 de seguridad en la nube del NCSC
### <a name="physical-resilience-and-availability"></a>Disponibilidad y resistencia física
Los servicios tienen distintos niveles de resistencia, lo cual afecta a su capacidad para operar con normalidad en caso de errores, incidentes o ataques. Un servicio sin garantías de disponibilidad puede estar no disponible, posiblemente durante períodos prolongados, independientemente del impacto en su negocio.


**Responsabilidades:** `Shared`

> [!NOTE]
> Si el cliente configura Microsoft Azure adecuadamente mediante la habilitación de Azure Site Recovery y el almacenamiento de datos alternativo en otro centro de datos localizado en una ubicación geográfica distinta, Microsoft Azure puede admitir el funcionamiento continuo de los recursos implementados por el cliente.

|||
|---|---|
| **Cliente** | El cliente es responsable de establecer un sitio de almacenamiento alternativo. La instrucción de implementación del control del cliente debe dirigir la capacidad de este para seguir operando en caso de incidente. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure tiene centros de datos aprobados por el National Cyber Security Centre (NCSC) del Reino Unido en distintas ubicaciones geográficas (Sur de Reino Unido y Oeste de Reino Unido) que proporcionan disponibilidad y resistencia. Será responsabilidad del cliente reservar capacidad en una región alternativa mediante el servicio Azure Site Recovery. Una vez que haya configurado Azure Site Recovery, Azure iniciará y detendrá los servicios del cliente en una transición perfecta al sitio de procesamiento alternativo. |


 ## <a name="ncsc-cloud-security-principle-3"></a>Principio 3 de seguridad en la nube del NCSC
### <a name="separation-between-users"></a>Separación entre usuarios
Un usuario malintencionado del servicio, o uno que esté en peligro, no debería influir en el servicio ni en los datos de otro.

Entre los factores que afectan a la separación entre usuarios se incluyen:

- El lugar donde se implementan los controles de separación. Estos dependen en gran medida del modelo de servicio (p. ej., IaaS, PaaS o SaaS)
- Los usuarios con los que comparte el servicio. Esto viene determinado por el modelo de implementación (por ejemplo, público, privado o nube de comunidad)
- El nivel de garantía disponible en la implementación de los controles de separación

> [!NOTE]
> En un servicio IaaS debe tener en cuenta la separación proporcionada por los componentes de proceso, almacenamiento y red. Además, los servicios SaaS y PaaS basados en IaaS pueden heredar algunas de las propiedades de separación de la infraestructura IaaS subyacente.

**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure garantiza el aislamiento de cada usuario para evitar que un usuario malintencionado o que esté en peligro pueda afectar al servicio o a los datos de otro. Por tanto, este principio se hereda de Microsoft Azure. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Puesto que los servidores en la nube del cliente son virtuales, el paradigma de separación física ya no es aplicable. Microsoft Azure se diseñó para ayudar a identificar y contrarrestar los riesgos inherentes de un entorno multiinquilino. El almacenamiento y el procesamiento de datos se segrega de forma lógica entre los usuarios de Azure mediante Active Directory y una funcionalidad específicamente desarrollada para servicios multiinquilino, que ayuda a garantizar que los datos de usuario almacenados en los centros de datos de Azure compartidos no sean accesibles por ninguna otra organización. <br /> <br /> El aislamiento que se proporciona a cada usuario para evitar que un usuario malintencionado o en peligro puede afectar al servicio o a los datos de otro usuario, resulta fundamental en cualquier arquitectura compartida en la nube. <br /> <br /> Para más información relacionada con la separación de inquilinos de Microsoft, consulte la descripción completa en [Azure Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Azure Blueprint: Principios de seguridad en la nube del NCSC: Matriz de responsabilidades del cliente). |


 ## <a name="ncsc-cloud-security-principle-4"></a>Principio 4 de seguridad en la nube del NCSC
### <a name="governance-framework"></a>Marco de regulación
El proveedor del servicio debe tener un marco de regulación que coordine y dirija la administración del servicio y la información que contiene. Cualquier control técnico implementado fuera de este marco estará fundamentalmente desprotegido.
Tener un marco de regulación eficaz garantiza que los procedimientos, el personal y los controles físicos y técnicos estarán siempre funcionando a lo largo del período de vigencia de un servicio. También debe dar respuesta a los cambios en el servicio, los desarrollos tecnológicos y la aparición de nuevas amenazas.


**Responsabilidades:** `Microsoft Azure`


|||
|---|---|
| **Cliente** | Microsoft Azure mantiene un marco de regulación de la seguridad documentado para los servicios de Azure. Por tanto, este principio se hereda de Microsoft Azure. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | El marco de cumplimiento normativo de Microsoft incluye una metodología estándar para definir dominios de cumplimiento, determinar qué objetivos son aplicables a un determinado equipo o recurso, y capturar cómo se afrontan los objetivos de control de dominio con suficiente detalle cuando se aplican a un conjunto determinado de normas estándar, regulaciones o requisitos empresariales del sector. El marco asigna controles para varias normas estándar de regulación, lo que permite a Microsoft diseñar y compilar servicios mediante un conjunto común de controles, con lo que se lo que facilita el cumplimiento normativo en las regulaciones actuales y en las del futuro. <br /> <br /> Los procesos de cumplimiento de Microsoft también facilitan a los clientes el lograr el cumplimiento en varios servicios y satisfacer sus necesidades cambiantes de forma eficaz. Combinados, la tecnología de mejora de la seguridad y los procesos de cumplimiento eficaces permiten a Microsoft mantener y expandir un amplio conjunto de certificaciones de terceros. Estas certificaciones ayudan a los clientes a demostrar a sus clientes, auditores y reguladores una buena disposición en relación con el cumplimiento normativo. <br /> <br />  Azure cumple con un amplio conjunto de normas estándar internacionales y regionales, específicas del sector, tales como ISO 27001, FedRAMP, SOC 1 y SOC 2. El cumplimiento de los estrictos controles de seguridad contenidos en estas normas estándar lo comprueban rigurosos auditores independientes que demuestran que los servicios de Azure funcionan y cumplen con las normas estándar, certificaciones y autorizaciones internacionales. <br /> <br /> Azure está diseñado con una estrategia de cumplimiento que ayuda a los clientes a abordar sus objetivos de negocio así como las normas estándar del sector y las regulaciones. El marco de cumplimiento en materia de seguridad incluye fases de prueba y auditoría, análisis de la seguridad, procedimientos recomendados de administración de riesgos y análisis comparativo de la seguridad para obtener certificaciones. <br /> <br /> Para más información relacionada con la adhesión de Microsoft a los marcos de cumplimiento, consulte la descripción completa en [Azure Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Azure Blueprint: Principios de seguridad en la nube del NCSC: Matriz de responsabilidades del cliente).|


 ## <a name="ncsc-cloud-security-principle-5"></a>Principio 5 de seguridad en la nube del NCSC
### <a name="operational-security"></a>Seguridad operativa
El servicio debe controlarse y administrarse de forma segura con el fin de impedir, detectar o evitar ataques. Una buena seguridad operativa no debería requerir procesos complejos, burocráticos, caros o que consuman demasiado tiempo.

Hay cuatro elementos que hay que tener en cuenta:

- Configuración y administración de cambios: debe asegurarse de que los cambios en el sistema se han probado y autorizado correctamente. Los cambios no deben alterar de forma inesperada propiedades de seguridad
- Administración de vulnerabilidades: debe identificar y mitigar los problemas de seguridad de los componentes constituyentes
- Supervisión de protección: debe implementar medidas para detectar ataques y actividades no autorizadas en el servicio
- Administración de incidentes: asegúrese de que puede hacer frente a incidentes y recuperar un servicio seguro y disponible




 ## <a name="ncsc-cloud-security-principle-51"></a>Principio 5.1 de seguridad en la nube del NCSC
### <a name="configuration-and-change-management"></a>Configuración y administración de cambios
Debe tener una imagen precisa de los recursos que componen el servicio, junto con sus configuraciones y dependencias.
Se deben identificar y administrar los cambios que podrían afectar a la seguridad del servicio. También se deben detectar los cambios no autorizados.
Si los cambios no se administran de forma eficaz, se pueden generar de forma involuntaria vulnerabilidades de seguridad en un servicio. E incluso en los casos en los que es consciente de una vulnerabilidad, puede que esta no se pueda mitigar por completo.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Las plantillas de Azure Resource Manager y los recursos asociados que componen este programa Azure Blueprint representan una línea de base de "configuración como código" para la arquitectura implementada. La solución se proporciona mediante GitHub, que puede utilizarse para el control de configuración. <br /> <br /> Los privilegios de la cuenta de Azure Active Directory se implementan mediante un control de acceso basado en rol mediante la asignación de usuarios a roles que proporcionan un control estricto sobre qué usuarios pueden ver y controlar recursos implementados. Los privilegios de la cuenta de Active Directory se implementan mediante un control de acceso basado en rol y la asignación de usuarios a grupos de seguridad. Estos grupos de seguridad controlan las acciones que los usuarios pueden realizar con respecto a la configuración del sistema operativo. El cliente puede extender estos esquemas basados en roles para cumplir necesidades de la misión. <br /> <br /> Para ser compatible con este principio, se necesita cierta configuración adicional por parte del cliente para su uso en producción. Por lo tanto, estas configuraciones deberán formar parte del proceso de administración de cambios del cliente. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure revisa y actualiza anualmente los valores de configuración y las configuraciones básicas de los dispositivos de hardware, software y red. Los cambios se desarrollan, prueban y aprueban antes de pasar al entorno de producción desde un entorno de desarrollo o pruebas. <br /> <br />Microsoft Azure aplica configuraciones de línea de base mediante el proceso de cambio y publicación de componentes de software de Microsoft Azure (por ejemplo, OS, Fabric, RDFE, XStore, etc.) e inicia el proceso de configuración de los componentes de dispositivos de hardware y de red introduciendo el entorno de producción de Microsoft Azure tal como se describe a continuación. <br /> <br /> El equipo de seguridad y cumplimiento de Azure se encarga de revisar las configuraciones de línea de base necesarias para los servicios basados en Azure, así como por los equipos de servicio como parte de las pruebas anteriores a la implementación del servicio de producción. |


 ## <a name="ncsc-cloud-security-principle-52"></a>Principio 5.2 de seguridad en la nube del NCSC
### <a name="vulnerability-management"></a>Administración de vulnerabilidades
Los proveedores de servicios deben tener un proceso de administración en vigor para identificar, evaluar las prioridades y mitigar vulnerabilidades. En caso contrario, los servicios serán rápidamente vulnerables a ataques mediante herramientas y métodos públicamente conocidos.


**Responsabilidades:** `Customer`


|||
|---|---|
| **Cliente** | El cliente es responsable del análisis de las vulnerabilidades en los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). La instrucción de implementación del cliente debe abordar las herramientas usadas para realizar los análisis de vulnerabilidades. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | La administración de actualizaciones de seguridad ayuda a proteger los sistemas frente a las vulnerabilidades conocidas. Azure utiliza sistemas de implementación integrados para administrar la distribución e instalación de actualizaciones de seguridad de software de Microsoft. Azure también es capaz de aprovechar los recursos de Microsoft Security Response Centre (MSRC), lo cual permite identificar, supervisar, responder y resolver incidentes de seguridad y vulnerabilidades en la nube de forma ininterrumpida. |


 ## <a name="ncsc-cloud-security-principle-53"></a>Principio 5.3 de seguridad en la nube del NCSC
### <a name="protective-monitoring"></a>Supervisión de protección
Un servicio que no supervisa eficazmente ataques, usos indebidos y funcionamientos inadecuados no podrá posiblemente detectar ataques (exitosos o frustrados). Como resultado, no podrá responder rápidamente a los posibles riesgos que ponen en peligro el entorno y los datos.


**Responsabilidades:** `Customer`


|||
|---|---|
| **Cliente** | El cliente es responsable de la supervisión de los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). La instrucción de implementación del control de cliente debe incluir los mecanismos para supervisar, detectar y responder a ataques, usos indebidos y funcionamientos inadecuados. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | El equipo de seguridad de Microsoft Azure ha definido los requisitos para una supervisión activa. Los equipos de servicio configuran herramientas de supervisión activas según estos requisitos. Entre las herramientas de supervisión activas se incluyen Monitoring Agent (MA) y System Centre Operations Manager (SCOM), que están configuradas para proporcionar alertas en tiempo real al personal de seguridad de Microsoft Azure en situaciones que requieran una acción inmediata. |


 ## <a name="ncsc-cloud-security-principle-54"></a>Principio 5.4 de seguridad en la nube del NCSC
### <a name="incident-management"></a>Administración de incidentes
A menos que haya procesos en vigor de administración de incidentes planeados previamente con cuidado, es probable que se tomen unas decisiones incorrectas cuando se produzcan los incidentes, lo cual podría ampliar posiblemente el impacto global sobre los usuarios.
No hace falta que estos procesos sean complejos, ni que requieran grandes cantidades de descripción, pero una buena administración de los incidentes contribuirá a minimizar el impacto para los usuarios de los problemas de seguridad, confiabilidad y de entorno de un servicio.


**Responsabilidades:** `Shared`

> [!NOTE]
> En los casos en los que los incidentes de seguridad del cliente puedan afectar al estado de seguridad de Microsoft Azure, el cliente es responsable de notificarlo a Microsoft Azure.

|||
|---|---|
| **Cliente** | El cliente es responsable del establecimiento de un proceso de administración de incidentes para los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). La instrucción de implementación del cliente debe abordar la notificación de incidentes y las alertas, proporcionando respuestas puntuales a los incidentes y reenviando la información al PGA y a otras organizaciones HMG como sea necesario. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft ha implementado un proceso de administración de incidentes de seguridad para facilitar una respuesta coordinada frente a incidentes, en caso de que estos se produzcan. <br /> <br /> Si Microsoft detecta cualquier acceso no autorizado a los datos de un cliente almacenados en su equipo o en sus instalaciones, o un acceso no autorizado a dichos equipos o instalaciones que provocan la pérdida, revelación o alteración de los datos del cliente, Microsoft declara que: <br /> <br />   - Notificará inmediatamente al cliente del incidente de seguridad; <br /> - Investigará rápidamente el incidente de seguridad y proporcionará al cliente información detallada sobre él y <br /> - Tomará medidas razonables e inmediatas para mitigar los efectos y minimizar el daño producido como consecuencia del incidente de seguridad.  <br />  <br /> Se ha establecido un marco de administración de incidentes con roles definidos y responsabilidades asignadas. El equipo de administración de incidentes de seguridad de Windows Azure (WASIM) es responsable de administrar los incidentes de seguridad, solicitando la implicación de equipos especialistas en los casos que sea necesario. Los responsables de operaciones son responsables de supervisar la investigación y resolución de los incidentes de seguridad y privacidad con el soporte de otras funciones. <br /> <br /> Para más información relacionada con los procesos de respuesta a incidentes de Microsoft, consulte la descripción completa en [Azure Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Azure Blueprint: Principios de seguridad en la nube del NCSC: Matriz de responsabilidades del cliente). |


 ## <a name="ncsc-cloud-security-principle-6"></a>Principio 6 de seguridad en la nube del NCSC
### <a name="personnel-security"></a>Seguridad del personal
En los casos en los que el personal del proveedor de servicios tiene acceso a los datos y sistemas, necesitará un alto grado de confianza en su honradez. Se puede reducir la probabilidad de que el personal del proveedor de servicios ponga en peligro los datos de forma accidental o malintencionada mediante una cuidadosa selección y una adecuada formación.
El proveedor de servicios debe someter al personal a un filtrado de seguridad y una formación periódica en temas de seguridad. El personal que desempeña estas funciones debe ser consciente de sus responsabilidades. Los proveedores deben dejar claro cómo seleccionan y administran al personal con roles con privilegios.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | El cliente es responsable de seleccionar a los usuarios y de proporcionar a las personas con acceso a los recursos implementados por el cliente una formación periódica en materia de seguridad. La instrucción de implementación del cliente debe abordar los criterios de filtrado para los roles y la frecuencia de la formación en temas de seguridad. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | El personal de Microsoft Azure que maneja los servicios de Azure y proporciona soporte técnico al cliente (o los subcontratistas de Microsoft que ayudan con las operaciones de plataforma, la solución de problemas y el soporte técnico) se debe someter a una comprobación estándar de sus antecedentes (o comprobación equivalente) para evaluar la formación educativa, la carrera profesional y el posible historial delictivo del empleado. Las comprobaciones de los antecedentes están ampliamente en consonancia con los requisitos BPSS/BS7858 del gobierno del Reino Unido. No incluyen específicamente una comprobación de identidad formal.  <br /> <br /> Microsoft incluye disposiciones de confidencialidad en los contratos de empleados y subcontratistas. Todos los empleados y subcontratistas adecuados de Microsoft toman parte en un programa de formación en materia de seguridad patrocinado por Microsoft Azure que les informa acerca de sus responsabilidades con respecto a la seguridad de la información. <br /> <br /> El personal de servicios de Microsoft Azure o los subcontratistas sospechosos de haber cometido infracciones de seguridad y de haber infringido la directiva de seguridad de la información se verán sometidos a un proceso de investigación, y se tomarán las medidas disciplinarias adecuadas, que pueden incluir incluso el despido. Si las circunstancias así lo requieren, Microsoft puede remitir el asunto a las autoridades judiciales para iniciar acciones legales. <br /> <br /> Para complementar este sistema de comprobaciones de antecedentes y formación en materia de seguridad, Microsoft implementa combinaciones de controles preventivos, defensivos y reactivos para ayudar en la protección frente a actividades de desarrollo y administrativas no autorizadas, incluyendo los siguientes mecanismos: <br />  <br /> - Estrictos controles de acceso a la información confidencial, incluyendo la solicitud de una autenticación en dos fases basada en tarjetas inteligentes para realizar operaciones confidenciales. <br /> - Combinaciones de controles que mejoran la detección independiente de actividades malintencionadas. <br /> - Varios niveles de supervisión, registro y elaboración de informes. |


 ## <a name="ncsc-cloud-security-principle-7"></a>Principio 7 de seguridad en la nube del NCSC
### <a name="secure-development"></a>Desarrollo seguro
Los servicios se deben diseñar y desarrollar para identificar y mitigar las amenazas a su seguridad. Aquellos servicios que no lo son, pueden ser vulnerables a problemas de seguridad que pueden poner en peligro los datos, provocar pérdidas del servicio o permitir otras actividades malintencionadas.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por esta solución de Azure Blueprint ejecutan sistemas operativos Windows. Windows proporciona la validación de la integridad de los archivos en tiempo real, la protección y la recuperación de los archivos principales del sistema que se instalan como parte de Windows o de las actualizaciones del sistema de Windows autorizadas a través de la funcionalidad Protección de recursos de Windows (WRP), que permite realizar la comprobación de integridad en tiempo real. <br /> <br /> Windows dispone de protecciones para evitar la ejecución de código en ubicaciones de memoria restringidas: No ejecución (NX), Selección aleatoria del diseño del espacio de direcciones (ASLR) y Prevención de ejecución de datos (DEP). <br /> <br /> Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante Microsoft Windows Defender. Windows Defender está configurado para actualizar automáticamente tanto las firmas de protección como el motor antimalware cuando la versión está disponible. <br /> <br /> Para ser compatible con este principio, se necesita cierta configuración adicional por parte del cliente para su uso en producción. Por lo tanto, estas configuraciones deberán formar parte del proceso de desarrollo seguro del cliente. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | El Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft proporciona un proceso de modelado de amenazas eficaz para identificar las amenazas y vulnerabilidades de software y servicios. El modelado de amenazas es un ejercicio de equipo que abarca a jefes de operaciones, jefes de proyecto o de programa, desarrolladores y evaluadores, y representa una tarea de análisis de seguridad clave que se lleva a cabo para el diseño de soluciones. Los miembros del equipo usan la herramienta de modelado de amenazas SDL para modelar todos los servicios y proyectos cuando estos se compilan y cuando se actualizan con nuevas características y funcionalidades. Los modelos de amenazas incluyen todo código expuesto en la superficie del ataque y todo código escrito por un tercero, o con licencia de este, y tiene en cuenta todos los límites de confianza. El sistema STRIDE (siglas en inglés de suplantación de identidad, manipulación, rechazo, divulgación de información, denegación de servicio y elevación de privilegios) se usa para ayudar a identificar y resolver las amenazas de seguridad al principio del proceso de diseño, antes de que puedan afectar a los clientes. |


 ## <a name="ncsc-cloud-security-principle-8"></a>Principio 8 de seguridad en la nube del NCSC
### <a name="supply-chain-security"></a>Seguridad de la cadena de suministro
El proveedor de servicios debe garantizar que su cadena de suministro cumple satisfactoriamente con todos los principios de seguridad que el servicio dice implementar.
Los servicios en la nube a menudo dependen de servicios y productos de terceros. Por ello, si no se implementa este principio, una cadena de suministro en peligro puede reducir la seguridad del servicio y afectar a la implementación de otros principios de seguridad.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | El cliente es responsable de proporcionar una documentación de cadena de suministro segura para cualquier software y sistema operativo adquirido de terceros, que se usen en su suscripción de Azure. La instrucción de implementación del cliente debe abordar los procesos a seguir en caso de se produzcan las excepciones identificadas por esta documentación de la cadena de suministro. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | El grupo de la Cadena de suministro de Microsoft Cloud (MCSC) está compuesto por seis equipos únicos cada uno de los cuales contribuye a proteger a Azure frente a las amenazas a la cadena de suministro.  <br />  <br /> - Adquisiciones <br /> - Operaciones del cliente <br /> - Calidad de implementación <br /> - Administración de relaciones con los proveedores <br /> - Repuestos <br />  <br /> Para más información relacionada con el grupo MCSC de Microsoft, consulte la descripción completa en [Azure Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Azure Blueprint: Principios de seguridad en la nube del NCSC: Matriz de responsabilidades del cliente). |


 ## <a name="ncsc-cloud-security-principle-9"></a>Principio 9 de seguridad en la nube del NCSC
### <a name="secure-user-management"></a>Administración segura de usuarios
El proveedor debe hacer que las herramientas estén disponibles para administrar de forma segura el uso de su servicio. Las interfaces y procedimientos de administración son una parte vital de la barrera de seguridad que impide el acceso no autorizado y la modificación de sus recursos, aplicaciones y datos.

Los aspectos a tener en cuenta son:

- Autenticación de usuarios en las interfaces de administración y los canales de soporte técnico
- Separación y control de acceso en las interfaces de administración



 ## <a name="ncsc-cloud-security-principle-91"></a>Principio 9.1 de seguridad en la nube del NCSC
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>Autenticación de usuarios en las interfaces de administración y en los canales de soporte técnico
Para mantener un servicio seguro, los usuarios se deben autenticar correctamente antes de que se les permita realizar actividades de administración, informar de errores o solicitar cambios en el servicio.
Estas actividades pueden realizarse a través de un portal web de administración de servicios o a través de otros canales, como el teléfono o el correo electrónico. Es probable que incluyan funciones como el aprovisionamiento de nuevos elementos de servicio, la administración de cuentas de usuario y la de los datos de usuario.
Los proveedores de servicios deben garantizar que todas las solicitudes de administración que pueden afectar a la seguridad se realizan a través de canales seguros y autenticados. Si los usuarios no se autentican de forma eficaz, un impostor podría llevar a cabo correctamente acciones con privilegios, lo cual puede perjudicar la seguridad de los datos o del servicio.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Cuando los administradores acceden a Microsoft Azure Portal para administrar los recursos de Azure de su organización, los datos transmitidos entre el portal y el dispositivo del administrador se envían a través de un canal con Seguridad de la capa de transporte (TLS) cifrado mediante las claves de cifrado RSA/SHA256 de 2048 bits, tal y como recomiendan CESG y NCSC.  <br /> <br /> Esta solución de Azure Blueprint utiliza Autenticación de Windows, Escritorio remoto y BitLocker. Estos componentes se pueden configurar para que se basen en los módulos criptográficos validados de FIPS 140. <br /> <br /> Esta solución de Azure Blueprint aplica autorizaciones de acceso lógico mediante el control de acceso basado en roles que aplica Azure Active Directory a través de la asignación de usuarios a roles y que aplica Active Directory mediante la asignación de usuarios a grupos de seguridad y controles en el nivel del sistema operativo Windows. Los roles de Azure Active Directory asignados a usuarios o grupos controlan el acceso lógico a recursos de Azure en los niveles de recurso, grupo o suscripción. Los grupos de seguridad de Active Directory controlan el acceso lógico a los recursos y funciones en el nivel del sistema operativo. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Los clientes administran sus recursos de Azure a través de Azure Portal, el cual proporciona acceso a todas las máquinas virtuales, las bases de datos, los servicios en la nube, y a otros recursos configurados para la cuenta del cliente. El acceso web a Azure Portal está protegido por conexiones estándar de Seguridad de la capa de transporte (TLS) 1.2, mediante claves de cifrado de RSA/SHA256 de 2048 bits, tal y como recomiendan CESG/NCSC. Los controles de acceso basado en rol se proporcionan para permitir a los clientes ofrecer un acceso limitado a los recursos de administración de Azure para usuarios y grupos específicos. |


 ## <a name="ncsc-cloud-security-principle-92"></a>Principio 9.2 de seguridad en la nube del NCSC
### <a name="separation-and-access-control-within-management-interfaces"></a>Separación y control de acceso en las interfaces de administración
Muchos servicios en la nube se administran a través de aplicaciones web o API. Estas interfaces son una parte fundamental de la seguridad del servicio. Si los usuarios no están separados adecuadamente dentro de las interfaces de administración, un usuario puede afectar al servicio o modificar los datos de otro.
Sus cuentas administrativas con privilegios probablemente tengan acceso a grandes volúmenes de datos. Restringir los permisos de usuarios individuales a los estrictamente necesarios puede ayudar a limitar los daños provocados por usuarios malintencionados, o por credenciales o dispositivos en peligro.
El control de acceso basado en rol proporciona un mecanismo para lograr esto y es probable que sea una funcionalidad especialmente importante para aquellos usuarios que administran las implementaciones más grandes.
Exponer las interfaces de administración a las redes menos accesibles (por ejemplo, a las redes de la comunidad en lugar de a las redes públicas) dificulta a los atacantes el poder acceder a ellas y atacarlas, ya que primero deberían acceder a una de estas redes. Se proporcionan más instrucciones sobre como evaluar los riesgos de exponer las interfaces a distintos tipos de redes en el principio 11.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa una instancia de Application Gateway y un equilibrador de carga y configura reglas de grupos de seguridad de red para controlar las conmutaciones en los límites externos y entre subredes internas. La funcionalidad del usuario está separada de la funcionalidad de administración del sistema mediante la aplicación de controles de acceso lógico y arquitectura del sistema. Las interfaces para la funcionalidad de administración del sistema son independientes de las interfaces de usuario. Toda la conectividad de administración tiene lugar a través de un host bastión (jumpbox) seguro ubicado en una subred de administración con reglas del grupo de seguridad de red para limitar el acceso a los recursos de producción según corresponda. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Tal y como se describe en la separación entre usuarios, la separación se integra en Azure en realidad. Se puede usar Azure Active Directory (Azure AD o AAD) para proporcionar a todos los usuarios que se autentican en Azure Portal acceso únicamente a los recursos que tienen derecho a ver y administrar. Como consecuencia, las cuentas de clientes diferentes se segregan estrictamente unas de otras cuando se administran a través de Azure Portal. |


 ## <a name="ncsc-cloud-security-principle-10"></a>Principio 10 de seguridad en la nube del NCSC
### <a name="identity-and-authentication"></a>Identidad y autenticación
Se deben restringir todos los accesos a las interfaces de servicio a los usuarios autenticados y autorizados.
Una autenticación débil en estas interfaces puede facilitar un acceso no autorizado a los sistemas, lo que podría provocar el robo o la modificación de los datos, y cambios en el servicio o una denegación de este.
Y lo que es más importante, la autenticación debe producirse a través de canales seguros. El correo electrónico, HTTP o el teléfono son vulnerables a ataques de interceptación y de ingeniería social.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint emplea Active Directory para la administración de cuentas. El acceso a los recursos implementados por esta instancia de Azure Blueprint está protegido contra los ataques de repetición gracias a la funcionalidad Kerberos integrada de Azure Active Directory, Active Directory y el sistema operativo Windows. En la autenticación de Kerberos, el autenticador que envía el cliente contiene datos adicionales, como una lista de IP cifrada, marcas de tiempo de clientes y la vigencia del vale. Si se reproduce un paquete, se comprueba la marca de tiempo. Si la marca de tiempo es anterior que un autenticador anterior, o la misma, el paquete se rechaza. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Azure ofrece servicios para ayudar a realizar un seguimiento de la identidad así como integrarla con almacenes de identidad que ya esté utilizando. Azure AD es un servicio completo de administración de identidades y accesos para la nube que le ayuda a proteger el acceso a los datos de las aplicaciones locales y en la nube. Azure AD también simplifica la administración de usuarios y grupos mediante la combinación de los principales servicios de directorio, la regulación de identidades avanzada, la seguridad y la administración de accesos a aplicaciones. |


 ## <a name="ncsc-cloud-security-principle-11"></a>Principio 11 de seguridad en la nube del NCSC
### <a name="external-interface-protection"></a>Protección de la interfaz externa
Todas las interfaces externas o de menor confianza del servicio se deben identificar y defender adecuadamente.
Si algunas de las interfaces expuestas son privadas (por ejemplo, las interfaces de administración), el impacto del riesgo puede ser más importante.
Puede utilizar diferentes modelos para conectarse a servicios en la nube que exponen los sistemas empresariales a distintos niveles de riesgo.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa los recursos en una arquitectura con una subred web independiente, la subred de la base de datos, la subred de Active Directory y la subred de administración. Las subredes se separan de forma lógica mediante las reglas del grupo de seguridad de red aplicadas a las subredes individuales para restringir el tráfico entre subredes unicamente a lo necesario para la funcionalidad de administración y del sistema (por ejemplo, el tráfico externo no puede acceder a la base de datos, la administración o las subredes de Active Directory).  <br /> <br /> Se implementa una instancia de Application Gateway para administrar las conexiones externas a una aplicación web implementada por el cliente. Las conexiones externas para el acceso de administración se limitan a un jumpbox (host bastión) implementado en una subred de administración con las reglas de seguridad de red aplicadas para restringir las conexiones externas a direcciones IP autorizadas. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Microsoft usa un método que se llama "Red Teaming" para mejorar los controles de seguridad de Azure y los procesos a través de pruebas de penetración regulares. El "Red Team" es un grupo de personal a tiempo completo de Microsoft que emprende ataques dirigidos y persistentes contra la infraestructura, las plataformas y las aplicaciones de Microsoft, pero no las aplicaciones ni los datos de los clientes finales. <br /> <br /> Para más información relacionada con el "Red Teaming" de Microsoft así como una descripción de los esfuerzos de "Blue Teaming", consulte la descripción completa en [Azure Blueprint - NCSC Cloud Security Principles - Customer Responsibilities Matrix](https://aka.ms/blueprintuk-gcrm) (Azure Blueprint: Principios de seguridad en la nube del NCSC: Matriz de responsabilidades del cliente).  |


 ## <a name="ncsc-cloud-security-principle-12"></a>Principio 12 de seguridad en la nube del NCSC
### <a name="secure-service-administration"></a>Administración segura del servicio
Los sistemas que se usan para la administración de un servicio en la nube tendrán acceso con privilegios elevados a ese servicio. Ponerlos en peligro conlleva un impacto significativo, lo cual incluye los medios para burlar los controles de seguridad y robar o manipular grandes volúmenes de datos.
El diseño, implementación y administración de los sistemas de administración deben seguir los procedimientos empresariales recomendados, si bien se debe reconocer su alto valor para los atacantes.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | El cliente es responsable de garantizar una estación de trabajo segura para la administración de la suscripción de Azure y de los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). La instrucción de implementación del cliente debe abordar los mecanismos empleados para mitigar el riesgo de explotación de los recursos implementados por el cliente. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Es necesario que el personal de operaciones de Microsoft Azure use estaciones de trabajo seguras para la administración (o SAW; también conocidas como estaciones de trabajo con acceso con privilegios, o PAW). El enfoque de SAW es una extensión del procedimiento recomendado ya establecido de utilizar cuentas de usuario y de administración independiente para el personal administrativo. Esta práctica utiliza una cuenta administrativa asignada individualmente que es completamente independiente de la cuenta estándar del usuario. SAW se basa en dicha práctica de separación de cuentas y proporciona una estación de trabajo de confianza para las cuentas confidenciales. |


 ## <a name="ncsc-cloud-security-principle-13"></a>Principio 13 de seguridad en la nube del NCSC
### <a name="audit-information-for-users"></a>Información de auditoría para los usuarios
Se le deben proporcionar los registros de auditoría necesarios para supervisar el acceso al servicio y a los datos contenidos en él. El tipo de información de auditoría disponible tendrá un impacto directo en la capacidad para detectar y dar respuesta a actividades no adecuadas o malintencionadas en un tiempo razonable.


**Responsabilidades:** `Shared`


|||
|---|---|
| **Cliente** | Los eventos que se auditan en esta instancia de Azure Blueprint incluyen los auditados en los registros de actividad de Azure para los recursos implementados, los registros de nivel de sistema operativo y los registros de Active Directory. Estos registros de evento incluyen la información suficiente para determinar cuándo se producen los eventos, el origen del evento, el resultado del mismo y otra información detallada que da respaldo a la investigación de los incidentes de seguridad. Los clientes pueden seleccionar eventos adicionales que se auditarán para cumplir las necesidades de la misión. Todos los recursos de Azure tienen registros de auditoría disponibles en Azure Portal. |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | Azure Log Analytics recopila los registros de los eventos que se producen en los sistemas y redes de la organización en cuanto se producen y antes de que nadie pueda alterarlos, y permite diferentes tipos de análisis correlacionando datos entre varios equipos. Azure permite que los clientes realicen la generación y recopilación de eventos de seguridad desde los roles de IaaS y PaaS de Azure hasta el almacenamiento central en sus suscripciones. Estos eventos recopilados se pueden exportar a sistemas de administración de eventos e información de seguridad (SIEM) locales para una supervisión continua. Después de que los datos se transfieren al almacenamiento, hay muchas opciones para ver los datos de diagnóstico. <br /> <br /> Los diagnósticos integrados de Azure pueden ayudar con la depuración. Para las aplicaciones que se implementan en Azure, se habilita un conjunto de eventos de seguridad del sistema operativo de forma predeterminada. Los clientes pueden agregar, quitar o modificar los eventos que se auditarán a través de la personalización de la directiva de auditoría del sistema operativo. <br /> <br /> En un nivel alto, es muy fácil comenzar a recopilar registros mediante el reenvío de eventos de Windows (WEF) o un servicio más avanzado como Azure Diagnostics cuando se implementan máquinas virtuales basadas en Windows mediante IaaS en Azure. Además, se puede configurar Azure Diagnostics para que recopile registros y eventos de las instancias de rol de PaaS. Al usar máquinas virtuales basadas en IaaS, un cliente solo tiene que configurar y habilitar los eventos de seguridad deseados del mismo modo que permiten a los servidores de Windows registrar las auditorías en su centro de datos local. Para las aplicaciones web, también es posible habilitar el registro de IIS si esa es la aplicación e implementación principal en Azure. Los clientes siempre pueden almacenar datos de seguridad en cuentas de almacenamiento en ubicaciones geográficas admitidas de su elección para cumplir con los requisitos de soberanía de datos. |


 ## <a name="ncsc-cloud-security-principle-14"></a>Principio 14 de seguridad en la nube del NCSC
### <a name="secure-use-of-the-service"></a>Uso seguro del servicio
La seguridad de los servicios en la nube y de los datos contenidos en ellos se puede debilitar si utiliza el servicio de una forma inadecuada. Por tanto, tendrá cierta responsabilidad cuando use el servicio para que sus datos estén debidamente protegidos.
El alcance de su responsabilidad variará según los modelos de implementación del servicio en la nube y el escenario en el que piensa utilizar el servicio. Se deben tener en cuenta también las características específicas de los servicios individuales. Por ejemplo, la forma en que una red de entrega de contenido protege la clave privada o el modo en que un proveedor de pagos en la nube detecta transacciones fraudulentas, son consideraciones de seguridad importantes que van más allá de las consideraciones generales cubiertas por los principios de seguridad en la nube.  
Con las ofertas de IaaS y PaaS, usted es responsable de aspectos importantes de la seguridad de los datos y las cargas de trabajo. Por ejemplo, si adquiere una instancia de proceso de IaaS, normalmente será responsable de la instalación de un sistema operativo moderno, la configuración de ese sistema operativo de forma segura, la implementación de todas las aplicaciones de forma segura y también del mantenimiento de esa instancia mediante la aplicación de revisiones o la realización de las operaciones de mantenimiento necesarias.


**Responsabilidades:** `Customer`

> [!NOTE]
> El cliente puede usar Azure Security Center para evitar y detectar amenazas, así como responder a ellas, porque aporta mayor visibilidad y control sobre la seguridad de todos los recursos de Azure. Azure Security Center proporciona funcionalidades de administración de directivas y supervisión de la seguridad integradas en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

|||
|---|---|
| **Cliente** | Las plantillas de Azure Resource Manager y los recursos que las acompañan y que componen esta solución de Azure Blueprint siguen un enfoque de defensa en profundidad en relación a la seguridad. Para ser compatible con este principio, se necesita cierta configuración adicional por parte del cliente para su uso en producción (por ejemplo, un software de administración de base de datos o una implementación de una aplicación web). |
| **Proveedor&nbsp;(Microsoft&nbsp;Azure)** | No aplicable |

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.