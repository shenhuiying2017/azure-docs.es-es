---
title: 'Azure Blueprint Automation de FedRAMP: mantenimiento'
description: 'Aplicaciones web para FedRAMP: mantenimiento'
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a0546f6e10b04bbfdb5b02e5c0bbe6d907c76e72
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="maintenance-ma"></a>Mantenimiento (MA)

> [!NOTE]
> Estos controles se definen por NIST y Estados Unidos. Departamento de Comercio como parte de publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

## <a name="nist-800-53-control-ma-1"></a>NIST 800-53 Control MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Procedimientos y directiva de mantenimiento del sistema

**MA-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de mantenimiento del sistema que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; y los procedimientos para facilitar la implementación de la directiva de mantenimiento del sistema y los controles de mantenimiento del sistema asociados; y revisa y actualiza la directiva de mantenimiento del sistema actual [Asignación: frecuencia definida por la organización], y los procedimientos de mantenimiento del sistema [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de mantenimiento del sistema de nivel empresarial y los procedimientos del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-2a"></a>NIST 800-53 Control MA-2.a

#### <a name="controlled-maintenance"></a>Mantenimiento controlado

**MA-2.a** La organización programa, realiza, documenta y revisa registros de mantenimiento y reparaciones de componentes del sistema de información según las especificaciones del fabricante o del proveedor, o bien los requisitos de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable del mantenimiento controlado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800-53 Control MA-2.b

#### <a name="controlled-maintenance"></a>Mantenimiento controlado

**MA-2.b** La organización aprueba y supervisa todas las actividades de mantenimiento, independientemente de si se realizan en el sitio o de forma remota y de si los equipos se reparan en el sitio o se trasladan a otra ubicación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable del mantenimiento controlado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-2c"></a>NIST 800-53 Control MA-2.c

#### <a name="controlled-maintenance"></a>Mantenimiento controlado

**MA-2.c** La organización requiere que [Asignación: personal o roles definidos por la organización] apruebe de forma explícita la eliminación del sistema de información o de los componentes del sistema de las instalaciones de la organización para reparaciones o mantenimiento externos.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure requiere que los recursos de la propiedad (p. ej., servidor o dispositivo de red) que a su vez requieren que la transferencia externa al sitio cuente con la aprobación explícita de recursos por parte del propietario. |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800-53 Control MA-2.d

#### <a name="controlled-maintenance"></a>Mantenimiento controlado

**MA-2.d** La organización corrige los equipos para quitar toda la información de los medios asociados antes de su retirada de las instalaciones de la organización para reparaciones o mantenimiento externos.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | La protección de los recursos de Microsoft Azure estándar define las precauciones de administración de recursos necesarias para la transferencia de recursos externa al sitio. La protección de los recursos estándar requiere que los recursos de almacenamiento de datos se borren o purguen de forma coherente con NIST SP 800-88, las directrices para el saneamiento de medios, antes de abandonar el centro de datos. |


 ## <a name="nist-800-53-control-ma-2e"></a>NIST 800-53 Control MA-2.e

#### <a name="controlled-maintenance"></a>Mantenimiento controlado

**MA-2.e** La organización comprueba todos los controles de seguridad que se han podido ver afectados para verificar que los controles siguen funcionando correctamente mediante acciones de mantenimiento o reparación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable del mantenimiento controlado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-2f"></a>NIST 800-53 Control MA-2.f

#### <a name="controlled-maintenance"></a>Mantenimiento controlado

**MA-2.f** La organización incluye [Asignación: información relacionada con el mantenimiento definida por la organización] en los registros de mantenimiento de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable del mantenimiento controlado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-2-2a"></a>NIST 800-53 Control MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Mantenimiento controlado | Actividades de mantenimiento automatizadas

**MA-2 (2).a** La organización emplea mecanismos automatizados para programar, dirigir y documentar el mantenimiento y las reparaciones.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la automatización de las actividades de mantenimiento. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-2-2b"></a>NIST 800-53 Control MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Mantenimiento controlado | Actividades de mantenimiento automatizadas

**MA-2 (2).b** La organización produce registros actualizados, precisos y completos de todas las acciones de mantenimiento y reparación solicitadas, programadas, en curso y completadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la automatización de las actividades de mantenimiento. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-3"></a>NIST 800-53 Control MA-3

#### <a name="maintenance-tools"></a>Herramientas de mantenimiento

**MA-3** La organización aprueba, controla y supervisa las herramientas de mantenimiento del sistema de información.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure usa varias herramientas para completar el mantenimiento. Las herramientas de mantenimiento de software se aprueban, controlan y mantienen a través del proceso de lanzamiento y cambio de Microsoft Azure. <br /> El equipo de servicios del sitio mantiene un inventario de herramientas de mantenimiento aprobadas para su uso en el centro de datos (consulte MA-3). El personal de mantenimiento recibe instrucciones para usar las herramientas de mantenimiento proporcionadas. La aprobación de la administración de centro de datos es necesaria para usar aquellas herramientas que no ha proporcionado el centro de datos. Las herramientas manuales físicas (destornilladores, llaves inglesas, etc.) están exentas de este control. <br /> En cada una de las instalaciones se incluye un cuadro de bloqueo físico restringido o sala de acceso controlado para almacenar herramientas de mantenimiento especializadas como, por ejemplo, ámbitos Ethernet Fluke, probadores de canal de fibra Fluke, tóneres Ethernet, etc. El equipo de servicios del sitio realiza comprobaciones de inventario rutinarias para verificar el estado de todas las herramientas. Se realiza un seguimiento del acceso a la sala de almacenamiento de mantenimiento o al cuadro de bloqueo en los registros del lector de distintivos de acceso, que están disponibles en caso de investigación. |


 ### <a name="nist-800-53-control-ma-3-1"></a>NIST 800-53 Control MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Herramientas de mantenimiento | Herramientas de inspección

**MA-3 (1)** La organización inspecciona las herramientas de mantenimiento llevadas a una instalación por el personal de mantenimiento para realizar modificaciones incorrectas o no autorizadas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | El equipo de servicios del sitio de Microsoft Azure mantiene un inventario de herramientas de mantenimiento aprobadas para su uso en el centro de datos (consulte MA-3 para obtener más detalles). El personal de mantenimiento recibe instrucciones para usar las herramientas de mantenimiento proporcionadas. La aprobación de la administración de centro de datos es necesaria para usar aquellas herramientas que no ha proporcionado el centro de datos. |


 ### <a name="nist-800-53-control-ma-3-2"></a>NIST 800-53 Control MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Herramientas de mantenimiento | Medios de inspección

**MA-3 (2)** La organización comprueba los medios que contienen programas de diagnóstico y prueba para código malintencionado antes de que los medios se usen en el sistema de información.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure prohíbe el uso de medios de almacenamiento o informática móvil en el entorno de producción de los centros de datos de Microsoft Azure sin la aprobación de la dirección de los centros de datos. Se prohíbe el uso de medios de propiedad personal en el entorno de producción de los centros de datos de Microsoft Azure. <br /> Microsoft Azure ha implementado un proceso para inspeccionar portátiles antes de usarse en el entorno de producción de los centros de datos de Microsoft Azure. Los responsables de la seguridad están entrenados para desafiar al personal que usa portátiles en el entorno de producción para verificar que estos se han sometido a la inspección y la han superado. |


 ### <a name="nist-800-53-control-ma-3-3"></a>NIST 800-53 Control MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Herramientas de mantenimiento | Impedir la retirada no autorizada

**MA-3 (3)** La organización impide la retirada no autorizada de los equipos de mantenimiento que contienen información de la organización comprobando que no se incluye información de la organización alguna en los equipos, corrigiendo o destruyendo los equipos, reteniendo los equipos en la instalación u obteniendo una exención de [Asignación: personal o roles definidos por la organización], que autoriza de forma explícita la retirada de los equipos de la instalación. 

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure emplea herramientas de mantenimiento específicas del centro de datos que se retienen en la instalación y no se quitan. En cada una de las instalaciones se incluye un cuadro de bloqueo físico restringido o sala de almacenamiento que almacena herramientas de mantenimiento como, por ejemplo, ámbitos Ethernet Fluke, probadores de canal de fibra Fluke, tóneres Ethernet, etc. Se controla el acceso al cuadro de bloqueo o a la sala de almacenamiento en DCAT para prohibir el acceso no autorizado a las herramientas de almacenamiento. <br /> Los controles de MA-4 protegen la información de la organización durante el mantenimiento. Para tener acceso a la información de la organización, el usuario debe tener autenticadores y cuentas con privilegios. |


 ## <a name="nist-800-53-control-ma-4a"></a>NIST 800-53 Control MA-4.a

#### <a name="nonlocal-maintenance"></a>Mantenimiento no local

**MA-4.a** La organización aprueba y supervisa las actividades de diagnóstico y mantenimiento no locales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de llevar a cabo un mantenimiento no local en aquellos sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-4b"></a>NIST 800-53 Control MA-4.b

#### <a name="nonlocal-maintenance"></a>Mantenimiento no local

**MA-4.b** La organización permite el uso de herramientas de diagnóstico y mantenimiento no locales únicamente de acuerdo con la directiva de la organización y si se documenta en el plan de seguridad del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de llevar a cabo un mantenimiento no local en aquellos sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800-53 Control MA-4.c

#### <a name="nonlocal-maintenance"></a>Mantenimiento no local

**MA-4.c** La organización emplea autenticadores seguros en el establecimiento de sesiones de diagnóstico y mantenimiento no locales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de llevar a cabo un mantenimiento no local en aquellos sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-4d"></a>NIST 800-53 Control MA-4.d

#### <a name="nonlocal-maintenance"></a>Mantenimiento no local

**MA-4.d** La organización mantiene y registra las actividades de diagnóstico y mantenimiento no locales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de llevar a cabo un mantenimiento no local en aquellos sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-4e"></a>NIST 800-53 Control MA-4.e

#### <a name="nonlocal-maintenance"></a>Mantenimiento no local

**MA-4.e** La organización finaliza la sesión y las conexiones de red al completarse el mantenimiento no local.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de llevar a cabo un mantenimiento no local en aquellos sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-4-2"></a>NIST 800-53 Control MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Mantenimiento no local | Documentar el mantenimiento no local

**MA-4 (2)** La organización se documenta en el plan de seguridad del sistema de información, las directivas y los procedimientos para el establecimiento y uso de conexiones de diagnóstico y mantenimiento no locales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de documentar el mantenimiento no local en el plan de seguridad de los sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-4-3"></a>NIST 800-53 Control MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Mantenimiento no local | Saneamiento o seguridad comparables

**MA-4 (3)** La organización requiere que los servicios de diagnóstico y mantenimiento no locales se lleven a cabo a partir de un sistema de información que implemente una funcionalidad de seguridad comparable a la funcionalidad implementada en el sistema atendido, o bien quita el componente que se va a atender del sistema de información antes que los servicios de diagnóstico o mantenimiento no locales, corrige el componente (con respecto a la información de la organización) antes de su retirada de las instalaciones de la organización y, una vez que se lleva a cabo el servicio, inspecciona y corrige el componente (con respecto al software que puede ser malintencionado) antes de volver a conectar el componente al sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de llevar a cabo todo el mantenimiento no local de los sistemas operativos implementados por el cliente a partir de un sistema de información con una seguridad comparable. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-4-6"></a>NIST 800-53 Control MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Mantenimiento no local | Protección criptográfica

**MA-4 (6)** El sistema de información implementa mecanismos criptográficos para proteger la integridad y confidencialidad de las comunicaciones de diagnóstico y mantenimiento no locales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de implementar mecanismos criptográficos al llevar a cabo el diagnóstico y el mantenimiento no locales de los sistemas operativos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-5a"></a>NIST 800-53 Control MA-5.a

#### <a name="maintenance-personnel"></a>Personal de mantenimiento

**MA-5.a** La organización establece un proceso para la autorización del personal de mantenimiento y mantiene una lista de personal u organizaciones de mantenimiento autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procesos de escolta y autorización del personal de mantenimiento del sistema de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-5b"></a>NIST 800-53 Control MA-5.b

#### <a name="maintenance-personnel"></a>Personal de mantenimiento

**MA-5.b** La organización garantiza que el personal no escoltado que lleva a cabo el mantenimiento en el sistema de información tenga autorizaciones de acceso requeridas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procesos de escolta y autorización del personal de mantenimiento del sistema de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-5c"></a>NIST 800-53 Control MA-5.c

#### <a name="maintenance-personnel"></a>Personal de mantenimiento

**MA-5.c** La organización designa al personal de la organización con autorizaciones de acceso requeridas y competencia técnica para supervisar las actividades de mantenimiento del personal que no posee las autorizaciones de acceso requeridas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procesos de escolta y autorización del personal de mantenimiento del sistema de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-5-1a"></a>NIST 800-53 Control MA-5 (1).a

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personal de mantenimiento | Personas sin acceso adecuado

**MA-5 (1).a** La organización implementa procedimientos para el uso del personal de mantenimiento que carece de los permisos de seguridad adecuados o que no es estadounidense, incluyendo los siguientes requisitos: el personal que no ha necesitado autorizaciones de acceso, permisos o aprobaciones de acceso formales se escolta y supervisa durante la realización de actividades de diagnóstico y mantenimiento en el sistema de información. De esto se encarga el personal de la organización aprobado totalmente habilitado, técnicamente cualificado y con autorizaciones de acceso adecuadas; antes de que el personal que no ha necesitado autorizaciones de acceso, permisos o aprobaciones de acceso formales inicie las actividades de diagnóstico o mantenimiento, se corrigen todos los componentes de almacenamiento de información volátiles y se quitan o desconectan físicamente del sistema y se protegen todos los medios de almacenamiento no volátiles.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procesos de escolta y autorización del personal de mantenimiento del sistema de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ma-5-1b"></a>NIST 800-53 Control MA-5 (1).b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personal de mantenimiento | Personas sin acceso adecuado

**MA-5 (1).b** La organización desarrolla e implementa medidas de seguridad alternativas si un componente del sistema de información no puede corregirse, quitarse o desconectarse del sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procesos de escolta y autorización del personal de mantenimiento del sistema de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ma-6"></a>NIST 800-53 Control MA-6

#### <a name="timely-maintenance"></a>Mantenimiento temporal

**MA-6** La organización obtiene soporte técnico de mantenimiento o piezas de repuesto para [Asignación: componentes del sistema de información definidos por la organización] en [Asignación: período de tiempo definido por la organización] de error.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Los centros de datos de Microsoft Azure mantienen al personal de mantenimiento residente para respaldar los sistemas de infraestructura del centro de datos esenciales y las operaciones del centro de datos. Los equipos han identificado los componentes del sistema de tecnología y seguridad esenciales de los que mantienen reservas in situ. Los sistemas esenciales están diseñados en configuraciones N+1 y los servicios están diseñados para ser resistentes. Esto permite al equipo de administración del centro de datos cumplir los objetivos de recuperación en caso de una situación del plan de contingencia o de interrupción del servicio. Los servicios del sistema de información esenciales se aprovisionan desde más de un centro de datos para impedir una interrupción del servicio debido a un incidente en uno de los centros de datos. Las aplicaciones del cliente son responsables de implementarse en varios centros de datos para proporcionar redundancia y resistencia. |
