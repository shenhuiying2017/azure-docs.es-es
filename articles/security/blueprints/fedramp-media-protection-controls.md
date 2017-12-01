---
title: "Automatización de Azure Blueprint de FedRAMP - Protección de medios"
description: "Aplicaciones web para FedRAMP - Protección de medios"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9cb33abc7ab88aaa54a77308c7863f8128f2e6c7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="media-protection-mp"></a>Protección de medios (MP)

> [!NOTE]
> Estos controles se definen por NIST y Estados Unidos. Departamento de Comercio como parte de publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

## <a name="nist-800-53-control-mp-1"></a>NIST 800-53 Control MP-1

#### <a name="media-protection-policy-and-procedures"></a>Procedimientos y directivas de protección de medios

**MP-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de protección de medios que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; así como los procedimientos para facilitar la implementación de la directiva de protección de medios y los controles de protección de medios asociados; y revisa y actualiza la directiva de protección de medios actual [Asignación: frecuencia definida por la organización], y los procedimientos de protección de medios [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos y la directiva de protección de medios a nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-mp-2"></a>NIST 800-53 Control MP-2

#### <a name="media-access"></a>Acceso a medios

**MP-2** La organización restringe el acceso a [Asignación: tipos definidos por la organización de medios digitales o no digitales] a [Asignación: personal o roles definidos por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure ha implementado el acceso a los medios a través de la implementación de la directiva de seguridad de Microsoft. El acceso lógico a los medios digitales se controla mediante objetos de la directiva de grupo de Active Directory (AD GPO) y grupos de seguridad. El acceso físico a todos los medios está restringido por el proceso de acceso al centro de datos. El acceso está restringido a las personas que tienen un propósito de negocio legítimo para acceder a los datos. Consulte la sección PE-3, Control de acceso físico, para obtener más información sobre los controles de acceso al centro de datos establecidos. El estándar de protección de recursos define las medidas necesarias para proteger la confidencialidad, integridad y disponibilidad de los recursos de información en los centros de datos de Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>NIST 800-53 Control MP-3.a

#### <a name="media-marking"></a>Marcado de medios

**MP-3.a** La organización marca los medios del sistema de información mediante la indicación de las limitaciones de distribución, advertencias de tratamiento y el marcado de seguridad aplicable (si lo hubiera) de la información.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure marca los recursos dentro de los centros de datos de Microsoft con una designación HBI, MBI o LBI (Impacto de negocio alto, moderado o bajo) que requiere diferentes niveles de seguridad y precauciones de tratamiento. Se requiere que los propietarios de recursos clasifiquen sus recursos almacenados en un centro de datos de Microsoft. Para obtener más información, consulte el estándar de clasificación de recursos y estándar de protección de recursos. |


 ## <a name="nist-800-53-control-mp-3b"></a>NIST 800-53 Control MP-3.b

#### <a name="media-marking"></a>Marcado de medios

**MP-3.b** La organización exime a [Asignación: tipos de medios del sistema de información definidos por la organización] del marcado mientras los medios permanezcan dentro de [Asignación: áreas controladas definidas por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure requiere que los propietarios de recursos asignen sus recursos con una clasificación de recursos y que ningún recurso esté exento de este requisito. En el entorno del centro de datos de Microsoft, los recursos hacen referencia a servidores, dispositivos de red y cintas magnéticas. No se utilizan otros medios digitales como memorias USB flash o dispositivo de memoria USB, discos duros externos o extraíbles o CD/DVD. Los medios no digitales no se utilizan en el centro de datos. |


 ## <a name="nist-800-53-control-mp-4a"></a>NIST 800-53 Control MP-4.a

#### <a name="media-storage"></a>Almacenamiento de medios

**MP 4.a** La organización controla físicamente y almacena de forma segura [Asignación: tipos de medios digitales o no digitales definidos por la organización] dentro de [Asignación: áreas controladas definidas por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Los recursos de medios digitales de Microsoft Azure se almacenan de forma física y con seguridad en las salas de ubicación del centro de datos. Los centros de datos de Microsoft tienen múltiples capas de controles de acceso físico (credenciales de acceso, datos biométricos; véase PE-3 para más detalles sobre controles de acceso físico) y videovigilancia para proporcionar almacenamiento seguro. Los medios digitales incluyen servidores, dispositivos de red y cintas magnéticas utilizadas para copias de seguridad. Los medios no digitales no se utilizan en el entorno del centro de datos. |


 ## <a name="nist-800-53-control-mp-4b"></a>NIST 800-53 Control MP-4.b

#### <a name="media-storage"></a>Almacenamiento de medios

**Módulo de administración 4.b** La organización protege los medios del sistema de información hasta que son destruidos o saneados utilizando equipo, técnicas y procedimientos aprobados.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Los recursos de medios digitales de Microsoft Azure están protegidos en las ubicaciones de los centros de datos de Microsoft mediante controles de acceso físico (PE-3) y controles de acceso lógico (IA-2) durante la vida útil del recurso. Los recursos de Microsoft Azure se limpian, purgan o destruyen con métodos coherentes con NIST SP 800-88 antes de su eliminación. Para la destrucción de recursos, Microsoft Azure utiliza los servicios de destrucción de recursos in situ. |


 ## <a name="nist-800-53-control-mp-5a"></a>NIST 800-53 Control MP-5.a

#### <a name="media-transport"></a>Transporte de medios

**MP-5.a** La organización protege y controla [Asignación: tipos de medios del sistema de información definidos por la organización] durante el transporte fuera de las áreas controladas mediante [Asignación: medidas de seguridad definidas por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Los medios digitales de Microsoft Azure en los centros de datos de Microsoft consisten en servidores, dispositivos de red y cintas y discos de respaldo magnéticos, cuando corresponda. Los centros de datos de Microsoft no utilizan medios no digitales. Microsoft utiliza tres métodos para proteger los medios que se transportan fuera del centro de datos: 1) Transporte seguro, 2) Cifrado y 3) Limpieza, purga o destrucción. |


 ## <a name="nist-800-53-control-mp-5b"></a>NIST 800-53 Control MP-5.b

#### <a name="media-transport"></a>Transporte de medios

**Módulo de administración 5.b** La organización es responsable de los medios del sistema de información durante el transporte fuera de las zonas controladas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure mantiene la responsabilidad por los recursos que salen del centro de datos mediante el uso de la guía de NIST SP 800-88: limpieza y purga coherentes, destrucción de recursos, cifrado, realización de inventarios precisos, seguimiento y protección de la cadena de custodia durante el transporte. |


 ## <a name="nist-800-53-control-mp-5c"></a>NIST 800-53 Control MP-5.c

#### <a name="media-transport"></a>Transporte de medios

**MP-5.c** La organización documenta las actividades asociadas con el transporte de medios del sistema de información.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure mantiene registros de inventario antes del transporte, seguimiento y protección de la cadena de custodia durante el transporte, limpieza y purga de recursos, destrucción de recursos, recepción de recursos y validación de inventarios después del transporte. |


 ## <a name="nist-800-53-control-mp-5d"></a>NIST 800-53 Control MP-5.d

#### <a name="media-transport"></a>Transporte de medios

**Módulo de administración 5.c** La organización restringe las actividades asociadas con el transporte de medios del sistema de información al personal autorizado.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure restringe las actividades de transporte de recursos al personal autorizado mediante la protección de la cadena de custodia. El uso de cerraduras, sellos a prueba de manipulaciones y la exigencia de validación de los inventarios de recursos asegura que solo el personal autorizado está involucrado en el transporte de recursos. |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800-53 Control MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Transporte de medios | Protección criptográfica

**MP-5 (4)** El sistema de información aplica mecanismos criptográficos para proteger la confidencialidad e integridad de la información almacenada en medios digitales durante el transporte fuera de las zonas controladas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure emplea el servicio de protección de datos (DPS) para administrar claves criptográficas utilizando un módulo de cifrado validado por FIPS 140-2 Nivel 3 (cert #1694) y HSM (cert #1178) para proteger los datos cifrados de 256 bits AES en las cintas magnéticas. |


 ## <a name="nist-800-53-control-mp-6a"></a>NIST 800-53 Control MP-6.a

#### <a name="media-sanitization"></a>Saneamiento de medios

**MP-6.a** La organización sanea [Asignación: medios del sistema de información definidos por la organización] antes de desecharlos, los libera fuera del control de la organización o los libera para su reutilización mediante [Asignación: técnicas y procedimientos de saneamiento definidos por la organización] de conformidad con las normas y directivas federales y organizacionales aplicables.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure requiere que los medios digitales en el entorno del centro de datos de Microsoft Azure se limpien o purguen mediante herramientas aprobadas por Microsoft Azure y de una manera coherente con NIST SP 800-88, Directrices para el saneamiento de los medios, antes de reutilizarlos o desecharlos. Microsoft Azure no utiliza medios no digitales en el entorno del centro de datos. |


 ## <a name="nist-800-53-control-mp-6b"></a>NIST 800-53 Control MP-6.b

#### <a name="media-sanitization"></a>Saneamiento de medios

**MP-6.b** La organización emplea mecanismos de saneamiento con la fuerza e integridad acordes con la categoría de seguridad o clasificación de la información.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure utiliza unidades y procesos de borrado de datos para limpiar o purgar los datos de forma coherente con NIST SP 800-88 y que sean acordes con la clasificación de recursos de Microsoft Azure del recurso. Para los recursos que requieren destrucción, Microsoft Azure utiliza servicios de destrucción de recursos in situ. |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800-53 Control MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Saneamiento de medios | Revisar / Aprobar / Realizar el seguimiento / Documentar / Comprobar

**MP-6 (1)** La organización revisa, aprueba, realiza el seguimiento, documenta y comprueba las acciones de saneamiento y eliminación de los medios.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure ha implementado procedimientos de saneamiento de medios de acuerdo con la guía de NIST SP 800-88 para la norma de clasificación de recursos y la norma de protección de recursos. Todos los medios magnéticos o electrónicos se limpian o purgan siguiendo las especificaciones de NIST SP 800-88 de acuerdo con la clasificación de recursos de Azure. Azure utiliza unidades de borrado de datos de Extreme Protocol Solutions (EPS). El software EPS es compatible con los requisitos de NIST SP 800-88 para la limpieza y purga o borrado seguro. |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800-53 Control MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Saneamiento de medios | Pruebas de equipos

**MP-6 (2)** La organización prueba los equipos y procedimientos de saneamiento [Asignación: frecuencia definida por la organización] para comprobar que se está logrando el saneamiento deseado.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure utiliza unidades y procesos de borrado de datos para limpiar o purgar los datos de manera coherente con NIST SP 800-88. Cada 180 días, las operaciones de DCS prueban las unidades de borrado de datos de Microsoft Azure y el proceso de borrado. En la prueba, las operaciones de DCS comprueban que el saneamiento pretendido se está consiguiendo a través de un análisis forense de los discos duros probados para confirmar que las unidades de borrado de datos han saneado los datos. |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800-53 Control MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Saneamiento de medios | Técnicas no destructivas

**MP-6 (3)** La organización aplica técnicas de saneamiento no destructivas a los dispositivos de almacenamiento portátiles antes de conectarlos al sistema de información en las siguientes circunstancias: [Asignación: circunstancias definidas por la organización que requieren saneamiento de los dispositivos de almacenamiento portátiles].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure garantiza que los centros de datos de Azure siguen el procedimiento de seguridad de herramientas y medios extraíbles en el runbook de servicios del centro de datos para evitar la infección del entorno gubernamental por malware en dispositivos de almacenamiento portátiles. El procedimiento especifica que se tomen las siguientes medidas con las unidades USB antes de utilizarlas en el entorno gubernamental: <br /> (1) Dar formato a las unidades USB cuando al comprarse al fabricante o vendedor, antes del uso inicial o cuando se vuelvan a utilizar para una herramienta diferente. <br /> (2) Examinar cualquier unidad USB que se vaya a utilizar en un área designada por el gobierno en busca de malware antes de llevar la unidad al área. <br /> (3) Después de usar una unidad dentro de un área designada por el gobierno, de formato a la unidad antes de salir del área. <br /> El procedimiento de seguridad de herramientas y medios extraíbles también requiere que todos los dispositivos de memoria USB perdidos, desechados, robados o extraviados nunca se vuelvan a introducir en los centros de datos de Azure, sino que se cataloguen y destruyan. |


 ## <a name="nist-800-53-control-mp-7"></a>NIST 800-53 Control MP-7

#### <a name="media-use"></a>Uso de los medios

**MP-7** La organización [Selección: restringe, prohíbe] el uso de [Asignación: tipos de medios de sistema de información definidos por la organización] en [Asignación: sistemas de información definidos por la organización o componentes del sistema] mediante [Asignación: medidas de seguridad definidas por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure requiere que los propietarios de recursos asignen sus activos con una clasificación de recursos, y ningún recurso está exento de este requisito. En el entorno del centro de datos de Microsoft Azure, los recursos hacen referencia a servidores y dispositivos de red. Otros medios digitales, como las memorias USB flash o dispositivos de memoria USB, se administran mediante directivas y procedimientos específicos que rigen la administración de dichos dispositivos. No se utilizan las unidades CD/DVD. Los medios no digitales no se utilizan en el centro de datos. El uso de medios digitales en los entornos del centro de datos de Microsoft Azure se supervisa ininterrumpidamente mediante la cobertura CCTV. Para obtener más información, consulte PE-06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800-53 Control MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Uso de medios | Prohibir el uso sin propietario

**MP-7 (1)** La organización prohíbe el uso de dispositivos de almacenamiento portátiles en los sistemas de información de la organización cuando dichos dispositivos no tienen un propietario identificable.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún medio controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft restringe el uso de medios extraíbles y grabables a los medios que se han aprobado explícitamente por la administración del centro de datos mediante el procedimiento de herramientas y medios extraíbles de DCS. Los medios que son propiedad personal o no tienen ningún propietario identificable están prohibidos en cualquier área de producción, tal como se indica en el documento sobre reglas y reglamentos de trabajo del centro de datos de Microsoft. |
