---
title: "Automatización de Azure Blueprint de FedRAMP: protección física y del entorno"
description: "Aplicaciones web para FedRAMP: protección física y del entorno"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9e9e473285f4e82e1da40b8c3d496d65733fbd45
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="physical-and-environmental-protection-pe"></a>Protección física y del entorno (PE)

> [!NOTE]
> NIST y los Estados Unidos definen estos controles. Departamento de Comercio como parte de la publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

## <a name="nist-800-53-control-pe-1"></a>NIST 800-53 Control PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Procedimientos y directivas de protección física y del entorno

**PE-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de protección física y del entorno que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; así como los procedimientos para facilitar la implementación de la directiva de protección física y del entorno y los controles de protección física y del entorno asociados; y revisa y actualiza la directiva de protección física y del entorno actual [Asignación: frecuencia definida por la organización] y los procedimientos de protección física y del entorno [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de protección física y del entorno de nivel empresarial y los procedimientos del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-pe-2a"></a>NIST 800-53 Control PE-2.a

#### <a name="physical-access-authorizations"></a>Autorizaciones de acceso físico

**PE-2.a** La organización desarrolla, aprueba y mantiene una lista de los usuarios con acceso autorizado a la instalación donde reside el sistema de información.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. El equipo de Administración de centros de datos (DCM) debe aprobar el acceso físico a un centro de datos de Microsoft mediante la herramienta de acceso a los centros de datos. Las asignaciones de acceso requieren una fecha de finalización después de la cual el acceso se quita automáticamente y se debe volver a aprobar. Además, cuando ya no se requiere el acceso, la administración o los responsables de la seguridad de los centros de datos deben solicitar manualmente la finalización del acceso. |


 ## <a name="nist-800-53-control-pe-2b"></a>NIST 800-53 Control PE-2.b

#### <a name="physical-access-authorizations"></a>Autorizaciones de acceso físico

**PE-2.b** La organización emite credenciales de autorización para el acceso a la instalación.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. La herramienta de acceso a los centros de datos es el origen de autoridad que muestra todo el personal que tiene acceso autorizado a un centro de datos específico. La herramienta está vinculada con los dispositivos de control de acceso de seguridad física del centro de datos y autoriza el acceso basado en los niveles de acceso que están aprobados por el equipo de DCM. Los niveles de acceso se asignan en la herramienta a un distintivo emitido por Microsoft del usuario o a un distintivo de acceso temporal que el supervisor de la sala de control (CRS) asigna en el centro de datos. El equipo de DCM aprueba los niveles de acceso. Además de las credenciales que se asignan a los distintivos físicos, algunas de las áreas del centro de datos requieren la inscripción de los datos biométricos del usuario (geometría manual o huella digital). |


 ## <a name="nist-800-53-control-pe-2c"></a>NIST 800-53 Control PE-2.c

#### <a name="physical-access-authorizations"></a>Autorizaciones de acceso físico

**PE-2.c** La organización revisa la lista de acceso que detalla el acceso autorizado a la instalación para los individuos [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Además de la revocación de acceso que se describe en la parte a, Microsoft Azure revisa las listas de acceso autorizado para los centros de datos de Azure cada 90 días para quitar o actualizar el acceso individual según sea necesario. |


 ## <a name="nist-800-53-control-pe-2d"></a>NIST 800-53 Control PE-2.d

#### <a name="physical-access-authorizations"></a>Autorizaciones de acceso físico

**PE-2.d** La organización quita individuos de la lista de acceso a la instalación cuando ya no se requiere el acceso.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure quita automáticamente el acceso cuando se llega a la fecha de finalización de la asignación de acceso. Cuando ya no se requiere el acceso, la administración o los responsables de la seguridad de los centros de datos solicitarán manualmente la finalización del acceso en la herramienta de acceso a los centros de datos. Además, Microsoft Azure quitará cualquier autorización de acceso que no sea necesaria que se detecte como resultado de la revisión de la lista de acceso que se describe en la parte c. |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800-53 Control PE-3.a

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.a** La organización exige autorizaciones de acceso físico en [Asignación: puntos de entrada y de salida definidos por la organización de la instalación donde reside el sistema de información] mediante la comprobación de las autorizaciones de acceso individuales antes de conceder acceso a la instalación y el control de ingreso o egreso a la instalación mediante [Selección (una o varias opciones): [Asignación: dispositivos o sistemas de control de acceso físico definidos por la organización]; restricciones].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure exige autorizaciones de acceso físico para todos los puntos de acceso físico a los centros de datos de Azure con personal ininterrumpido, alarmas, vigilancia mediante vídeo, autenticación multifactor y dispositivos del portal de trampa. |


 ## <a name="nist-800-53-control-pe-3b"></a>NIST 800-53 Control PE-3.b

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.b** La organización mantiene registros de auditoría de acceso físico para [Asignación: puntos de entrada y de salida definidos por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Todos los accesos a las instalaciones de centros de datos de Azure se registran y auditan. |


 ## <a name="nist-800-53-control-pe-3c"></a>NIST 800-53 Control PE-3.c

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.c** La organización proporciona [Asignación: medidas de seguridad definidas por la organización] para controlar el acceso a áreas dentro de la instalación que estén designadas oficialmente como accesibles públicamente.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los centros de datos de Azure no contienen áreas que esté designadas como accesibles públicamente. |


 ## <a name="nist-800-53-control-pe-3d"></a>NIST 800-53 Control PE-3.d

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.d** La organización escolta a los visitantes y supervisa su actividad [Asignación: circunstancias definidas por la organización que requieren escolta y supervisión de los visitantes].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Todos los visitantes con acceso aprobado al centro de datos (consulte PE-2) se designan como "Solo escolta" en sus distintivos o mediante otro indicador visual (por ejemplo, distintivos de color) y que deben permanecer con sus escoltas en todo momento. A los visitantes con escolta no se les concede ningún nivel de acceso y solo pueden realizar recorridos si acceden con sus escoltas. Los escoltas supervisan todas las actividades del visitante dentro del centro de datos. |


 ## <a name="nist-800-53-control-pe-3e"></a>NIST 800-53 Control PE-3.e

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.e** La organización protege las claves, las combinaciones y otros dispositivos de acceso físico.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Las claves físicas y los distintivos de acceso temporal están protegidos dentro del centro de operaciones de seguridad (SOC). El personal responsable de la seguridad está disponible de manera ininterrumpida. Las claves se extraen para el personal específico mediante la asociación del distintivo de acceso de la persona con la clave física. En cada turno se realizan inventarios de las claves y estas no pueden salir de las instalaciones. |


 ## <a name="nist-800-53-control-pe-3f"></a>NIST 800-53 Control PE-3.f

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.f** La organización realiza un inventario de [Asignación: dispositivos de acceso físico definidos por la organización] cada [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Se realiza un inventario de los dispositivos de acceso físico dentro de los centros de datos de Azure al menos de manera anual. Se realiza un inventario de las claves y los distintivos de acceso temporal varias veces al día durante cada turno. Los lectores de distintivos de acceso y dispositivos de acceso similares están vinculados con el sistema de seguridad física donde el estado se representa de manera continua. |


 ## <a name="nist-800-53-control-pe-3g"></a>NIST 800-53 Control PE-3.g

#### <a name="physical-access-control"></a>Control de acceso físico

**PE-3.g** La organización cambia las combinaciones y las claves [Asignación: frecuencia definida por la organización] o el momento en que se pierden las claves, las combinaciones se ven comprometidas o se transfiere o despide a los individuos.  

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los centros de datos de Microsoft Azure tienen procedimientos para implementar en los casos en que se pierde una clave o un distintivo de acceso o si se transfiere o despide a una persona. En el caso de un despido o una transferencia, el acceso de la persona se quita inmediatamente del sistema y también se quita su distintivo de acceso. |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800-53 Control PE-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Control de acceso físico | Acceso a la información del sistema

**PE-3 (1)** La organización exige autorizaciones de acceso físico al sistema de información además de los controles de acceso físico a la instalación en [Asignación: espacios físicos definidos por la organización que contienen uno o varios componentes del sistema de información].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure restringe aún más las áreas dentro de los centros de datos de Microsoft que contienen los sistemas críticos (por ejemplo, las ubicaciones, los entornos críticos, las salas de MDF, etc.) a través de diversos mecanismos de seguridad como el control de acceso electrónico, los dispositivos biométricos y los controles antirretorno. El acceso a las ubicaciones de Azure se concede como un nivel independiente de acceso de DCAT superior a las otras áreas de acceso del centro de datos. Además, todos los FTE de Azure y los proveedores que tienen acceso a las ubicaciones de Azure Government deben someterse formalmente al filtrado de la nube de Microsoft y a la comprobación de ciudadanía de EE. UU. antes de tener acceso autorizado al entorno. Consulte la sección PS-03 para más detalles relacionados con el filtrado de la nube para las ubicaciones de Azure Government. |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800-53 Control PE-4

#### <a name="access-control-for-transmission-medium"></a>Control de acceso para medios de transmisión

**PE-4** La organización controla el acceso físico a [Asignación: distribución del sistema de información y líneas de transmisión definidas por la organización] dentro de las instalaciones de la organización [Asignación: medidas de seguridad definidas por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure implementó el control de acceso de los medios de transmisión a través del diseño y la creación de las ubicaciones y las salas del marco de distribución principal (MDF) para proteger las líneas de transmisión y distribución del sistema de información ante daños accidentales, interrupciones y manipulaciones físicas. El acceso a las ubicaciones y las salas de MDF requiere autenticación en dos fases (distintivo de acceso y biométrica). Con esto se garantiza que el acceso está restringido solo al personal autorizado (consulte PE-2 y PE-3). Dentro del MDF, las líneas de distribución y transmisión están protegidas ante daños accidentales, interrupciones y manipulaciones físicas mediante el uso de conductos metálicos, bastidores cerrados, jaulas o bandejas de cables. |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800-53 Control PE-5

#### <a name="access-control-for-output-devices"></a>Control de acceso para los dispositivos de salida

**PE-5** La organización controla el acceso físico a los dispositivos de salida del sistema de información con el fin de impedir que individuos no autorizados obtengan la salida.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los centros de datos de Microsoft Azure no tienen dispositivos de salida (monitores, impresoras, dispositivos de audio, etc.) conectados de manera permanente a los activos de Azure o a los activos compartidos de Azure. Además de no tener dispositivos de salida, los responsables de la seguridad realizar recorridos físicos por la instalación varias veces durante un turno para comprobar que elementos como puertas estén cerrados y que los bastidores estén protegidos. El acceso a los centros de datos está limitado a los usuarios que tienen aprobadas las autorizaciones de acceso. Las ubicaciones requieren la autenticación en dos fases (distintivo de acceso y biométrica) para obtener acceso. |


 ## <a name="nist-800-53-control-pe-6a"></a>NIST 800-53 Control PE-6.a

#### <a name="monitoring-physical-access"></a>Supervisión del acceso físico

**PE-6.a** La organización supervisa el acceso físico a la instalación donde reside el sistema de información para detectar y responder los incidentes físicos de seguridad.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. El acceso físico se supervisa mediante la implementación de procesos y dispositivos de seguridad en los centros de datos. Los ejemplos incluyen la supervisión electrónica ininterrumpida del control de acceso y los sistemas de alarmas y vídeos, además de vigilancia de seguridad local ininterrumpida de las instalaciones. Un supervisor de la sala de control se encuentra en todo momento en el SOC para proporcionar la supervisión del acceso físico en el centro de datos. |


 ## <a name="nist-800-53-control-pe-6b"></a>NIST 800-53 Control PE-6.b

#### <a name="monitoring-physical-access"></a>Supervisión del acceso físico

**PE-6.b** La organización revisa los registros de acceso físico [Asignación: frecuencia definida por la organización] y ante la aparición de [Asignación: eventos o posibles indicaciones de eventos definidos por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los registros de acceso físico se revisan continuamente y se conservan para una revisión posterior con fines de investigación. |


 ## <a name="nist-800-53-control-pe-6c"></a>NIST 800-53 Control PE-6.c

#### <a name="monitoring-physical-access"></a>Supervisión del acceso físico

**PE-6.c** La organización coordina los resultados de las revisiones y las investigaciones con la funcionalidad de la respuesta ante incidentes de la organización. 

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. El equipo de seguridad documenta los eventos de seguridad que se producen dentro del centro de datos. El equipo de seguridad crea los informes que capturan los detalles de un evento de seguridad una vez que se produce el evento. <br /> En el caso de los incidentes en los que se requiera notificar al gobierno, el equipo de seguridad de Microsoft Azure coordinará con el proveedor de aplicaciones principal (por ejemplo, O365) para notificar al cliente de la agencia gubernamental, US CERT, y FedRAMP dentro de las instrucciones de US-CERT (consulte IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800-53 Control PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Supervisión del acceso físico | Equipo de vigilancia / alarmas de intrusiones

**PE-6 (1)** La organización supervisa el equipo de vigilancia y las alarmas de intrusiones físicas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Además de la seguridad ininterrumpida en las instalaciones, los centros de datos de Microsoft Azure (a concesión y completamente administrados) también usan sistemas de supervisión de alarmas y CCTV. El supervisor de la sala de control que siempre se encuentra disponible en el SOC supervisa y responde a las alarmas. Durante una situación de emergencia, el supervisor de la sala de control usa cámaras en el área del incidente que se está investigando para brindar información en tiempo real al respondedor. |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800-53 Control PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Supervisión del acceso físico | Supervisión del acceso físico a los sistemas de información

**PE-6 (4)** La organización supervisa el acceso físico al sistema de información además de la supervisión del acceso físico a la instalación como [Asignación: espacios físicos definidos por la organización que contienen uno o varios componentes del sistema de información].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure supervisa el acceso físico a las instalaciones así como los sistemas de información dentro de los centros de datos. Todo el equipo de los servicios en línea de Microsoft se encuentra en ubicaciones dentro de los centros de datos en los que se supervisa el acceso físico. Toda la ubicación y las salas de MDF están protegidas a través de control de acceso, alarmas y vídeo. |


 ## <a name="nist-800-53-control-pe-8a"></a>NIST 800-53 Control PE-8.a

#### <a name="visitor-access-records"></a>Registros de acceso de los visitantes

**PE-8.a** La organización mantiene registros de acceso de los visitantes a la instalación donde reside el sistema de información para [Asignación: período de tiempo definido por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los registros de acceso a los centros de datos se conservan en la herramienta de acceso a los centros de datos en la forma de solicitudes aprobadas. Tal como se describe en PE-3, se requiere que los visitantes vayan escoltados en todo momento. El acceso de la escolta dentro del centro de datos se registra y, si es necesario, se puede poner en correlación con el visitante para una revisión a futuro. |


 ## <a name="nist-800-53-control-pe-8b"></a>NIST 800-53 Control PE-8.b

#### <a name="visitor-access-records"></a>Registros de acceso de los visitantes

**PE-8.b** La organización revisa los registros de acceso de los visitantes [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. El registro de acceso de los visitantes con una solicitud de acceso aprobada se revisará en el momento en que la identificación se compruebe con un distintivo emitido por Microsoft o un identificador emitido por el gobierno. Tal como se describe en PE-3, los visitantes van escoltados en todo momento dentro del centro de datos. |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800-53 Control PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Registros de acceso de los visitantes | Revisión y mantenimiento automatizados de los registros

**PE-8 (1)** La organización emplea mecanismos automatizados para facilitar el mantenimiento y la revisión de los registros de acceso de los visitantes.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure mantiene los registros de acceso a los centros de datos en DCAT como solicitudes de DCAT aprobadas. Solo el equipo de DCM puede aprobar las solicitudes de DCAT. Los niveles de acceso dentro de los centros de datos se asignan y administran dentro de DCAT. El acceso a los centros de datos se revisa cada trimestre. Todo el acceso a los centros de datos de Azure se registra en DCAT y está disponible para posibles investigaciones a futuro. Tal como se describe en PE-3, se requiere que los visitantes estén escoltados en todo momento. El acceso de la escolta dentro del centro de datos se registra dentro del sistema de supervisión de alarmas y, si es necesario, se puede poner en correlación con el visitante para una revisión a futuro. El acceso de los visitantes lo revisa constantemente la escolta asignada y, además, el supervisor de la sala de control a través de CCTV y del sistema de supervisión de alarmas. Los visitantes no cuentan con acceso y deben ir acompañados de escoltas en todo momento. |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800-53 Control PE-9

#### <a name="power-equipment-and-cabling"></a>Equipamiento electrónico y cableado de alimentación

**PE-9** La organización protege el equipamiento eléctrico y el cableado de alimentación del sistema de información ante daños y destrucciones.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure proporciona espacios protegidos y el etiquetado correspondiente de los cables. El equipo de infraestructura de Microsoft Azure, por ejemplo, cables, tendidos eléctricos y generadores de reserva, se debe ubicar en entornos que se han diseñado para su protección ante riesgos del entorno, como robos, incendios, explosivos, humo, agua, polvo, vibraciones, sismos, productos químicos nocivos, interferencias eléctricas, apagones energéticos, disturbios eléctricos (alzas). Todos los activos de servicios en línea portátiles (como bastidores, servidores o dispositivos de red) deben estar bloqueados o fijados a fin de protegerlos de robos o de daños provocados por el movimiento. Los cables eléctricos y del sistema de información dentro de cualquier entorno de Microsoft Azure están correctamente etiquetados y protegidos contra cualquier interceptación o daño. Los cables eléctricos y del sistema de información están separados entre sí en todos los puntos de un entorno para evitar cualquier interferencia. |


 ## <a name="nist-800-53-control-pe-10a"></a>NIST 800-53 Control PE-10.a

#### <a name="emergency-shutoff"></a>Interrupción de emergencia

**PE-10.a** La organización proporciona la funcionalidad de interrumpir la alimentación del sistema de información o de los componentes individuales del sistema en situaciones de emergencia.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure tiene instalados botones para el apagado de emergencia (EPO) en ubicaciones dentro del centro de datos según lo estipulado en el código local para la prevención de incendios. En algunos centros de datos administrados por Microsoft Azure, el diseño de los centros de datos ya no requiere los botones de EPO. |


 ## <a name="nist-800-53-control-pe-10b"></a>NIST 800-53 Control PE-10.b

#### <a name="emergency-shutoff"></a>Interrupción de emergencia

**PE-10.b** La organización coloca dispositivos o interruptores de emergencia en [Asignación: ubicación definida por la organización por sistema de información o componente del sistema] para facilitar el acceso seguro y simple del personal.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los botones de EPO están ubicados de manera estratégica para poder activarlos en situaciones de emergencia. Los botones de EPO pueden estar colocados en las ubicaciones, se pueden activar desde los centros de operaciones de las instalaciones (FOC), o según lo que estipule el código local para la prevención de incendios. |


 ## <a name="nist-800-53-control-pe-10c"></a>NIST 800-53 Control PE-10.c

#### <a name="emergency-shutoff"></a>Interrupción de emergencia

**PE-10.c** La organización protege la funcionalidad de interrupción de alimentación de emergencia ante activaciones no autorizadas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Para evitar que se activen por accidente, los botones de EPO pueden estar dentro de una carcasa de protección, requerir activación dual o usar una sonora como advertencia antes de la activación. Además, los botones de EPO se vigilan por vídeo. |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800-53 Control PE-11

#### <a name="emergency-power"></a>Alimentación de emergencia

**PE-11** La organización proporciona una fuente de alimentación ininterrumpida a corto plazo para facilitar [Selección (una o varias opciones): un apagado en orden del sistema de información, la transición del sistema de información a una fuente de alimentación alternativa a largo plazo] en el caso de una pérdida de la fuente de energía principal.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure implementó la alimentación de emergencia a través de la protección de los circuitos y del equipo del centro de datos con un sistema de suministro de energía ininterrumpida (PS) que proporciona una fuente de alimentación a corto plazo para brindar energía hasta que los generadores puedan quedar en línea. |


 ### <a name="nist-800-53-control-pe-11-1"></a>NIST 800-53 Control PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Alimentación de emergencia | Fuente de alimentación alternativa a largo plazo: funcionalidad operativa mínima

**PE-11 (1)** La organización proporciona una fuente de alimentación alternativa a largo plazo para el sistema de información que es capaz de mantener la funcionalidad operativa mínima requerida en el caso de una extensa pérdida de la fuente de energía principal.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementó una fuente de alimentación alternativa a largo plazo para el sistema de información que es capaz de mantener una funcionalidad operativa requerida mínima cuando se produce una extensa pérdida de la fuente de energía principal. Cuando hay problemas o bajas en el suministro eléctrico debido a un nivel de voltaje inaceptable, los sistemas de suministro de energía ininterrumpida (UPS) se inician de inmediato y toman el control de la carga de energía. Esto brinda la energía suficiente para ejecutar los servidores hasta que los generadores puedan hacerse cargo. Los generadores de emergencia brindan alimentación de respaldo en caso de interrupciones intensas y para el mantenimiento planeado, y pueden operar el centro de datos con las reservas de combustible locales en caso de una catástrofe natural. Azure mantiene generadores diésel en muchos de los centros de datos. Los generadores de reserva se usan cuando sea necesario para mantener la estabilidad de la red o en el caso de alguna reparación excepcional, además de las situaciones de mantenimiento que requieren que quitemos los centros de datos de la red eléctrica. |


 ## <a name="nist-800-53-control-pe-12"></a>NIST 800-53 Control PE-12

#### <a name="emergency-lighting"></a>Iluminación de emergencia

**PE-12** La organización emplea y mantiene iluminación automática de emergencia para el sistema de información que se activa cuando se produce una interrupción o una pérdida de energía y abarca las salidas de emergencia y las rutas de evacuación dentro de la instalación.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los centros de datos de Microsoft Azure (a concesión y completamente administrados) implementan la iluminación de emergencia como luces de emergencia superiores en los circuitos dedicados respaldados por el UPS y los sistemas de generación (consulte PE-11). La iluminación automática de emergencia se implementa en todas las rutas de evacuación, en las salidas de emergencia y dentro de las ubicaciones en conformidad con lo estipulado en el Código de protección de la seguridad de la Asociación Nacional de Protección contra el fuego (NFPA). En caso de que se produzca una pérdida del suministro de electricidad de la red pública, la iluminación de emergencia cambiará automáticamente a la energía que proporciona los sistemas de generación y de UPS. Los sistemas de iluminación de emergencia dentro de los centros de datos de Microsoft Azure pasan por un mantenimiento de rutina para garantizar que funcionan correctamente. |


 ## <a name="nist-800-53-control-pe-13"></a>NIST 800-53 Control PE-13

#### <a name="fire-protection"></a>Protección contra incendios

**PE-13** La organización emplea y mantiene sistemas y dispositivos de detección y extinción de incendios para el sistema de información con el respaldo de una fuente energética independiente.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure implementó la protección contra incendios mediante la instalación de sistemas de extinción y detección de incendios en los centros de datos de Microsoft Azure. <br /> Los centros de datos de Microsoft Azure implementan mecanismos eficaces para la detección de incendios. El enfoque de Microsoft Azure para la protección contra incendios incluye el uso de detectores de humo fotoeléctricos instalados por debajo del piso y en el techo, los que están integrados con el sistema de rociadores para la protección contra incendios. Además, en cada ubicación hay sistemas VESDA (aparatos de detección de humo muy temprana) de Xtralis que supervisan la calidad del aire. Las unidades VESDA son sistemas de muestreo de aire muy sensibles que se instalan a lo largo de varios espacios de alto valor. Las unidades VESDA permiten una respuesta investigativa antes de una alarma de detección de incendios real. <br /> Hay cajas de alarmas de incendios manuales instaladas a lo largo de todos los centros de datos para realizar una notificación de alarmas de incendios manual. Hay extintores ubicados en todos los centros de datos, y cada año se revisan, se les realiza mantenimiento y se etiquetan. El personal de seguridad patrulla todas las áreas del edificio varias veces durante cada turno. El personal del centro de datos realiza un recorrido diario por el sitio para garantizar que se cumplen todos los requisitos de protección contra incendios. <br /> Las áreas que contienen los equipos eléctricos delicados (ubicaciones, MDF, etc.) están protegidas con sistemas de aspersión de acción previa (tubería seca) de interbloqueo doble. Los aspersores de tubería seca son un sistema de acción previa de dos fases que requiere la activación de los cabezales de los aspersores (debido al calor) y la detección de humo para lanzar agua. La activación de los cabezales de los aspersores libera la presión del aire en las tuberías, lo que permite que las tuberías se llenen de agua. El agua se lanza cuando también se activa un detector de humo o un detector térmico. <br /> Los sistemas de iluminación de emergencia y de extinción o detección de incendios están conectados con los sistemas de generación y del UPS del centro de datos para brindar una fuente de alimentación redundante. |


 ### <a name="nist-800-53-control-pe-13-1"></a>NIST 800-53 Control PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Protección contra incendios | Sistemas o dispositivos de detección

**PE-13 (1)** La organización emplea los sistemas o dispositivos de detección de incendios para el sistema de información que se activan automáticamente y notifican a [Asignación: los roles o el personal definidos por la organización] y [Asignación: los respondedores de emergencia definidos por la organización] en caso de incendio.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure emplea los sistemas o dispositivos de detección de incendios para el sistema de información que se activan automáticamente y notifican al personal del centro de datos junto con los respondedores de emergencia en caso de incendio. Si alguno de estos mecanismos de detección de incendios se activa en alguna ubicación, el departamento de bomberos local recibe una notificación automática mediante el sistema de alarma contra incendios. Además, los sistemas de detección y protección contra incendios están vinculados con el sistema de seguridad para notificar al personal de seguridad y la instalación local. |


 ### <a name="nist-800-53-control-pe-13-2"></a>NIST 800-53 Control PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Protección contra incendios | Sistemas o dispositivos de extinción

**PE-13 (2)** La organización emplea los sistemas o dispositivos de extinción de incendios para el sistema de información que proporcionan notificaciones automáticas de cualquier activación a [Asignación: los roles o el personal definidos por la organización] y [Asignación: los respondedores de emergencia definidos por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Si alguno de estos sistemas de extinción de incendios se activa en el centro de datos, el departamento de bomberos local recibe una notificación automática mediante el sistema de alarma contra incendios. Además, los sistemas de detección y protección contra incendios están vinculados con el sistema de seguridad para notificar al personal de seguridad y la instalación local. |


 ### <a name="nist-800-53-control-pe-13-3"></a>NIST 800-53 Control PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Protección contra incendios | Extinción automática de incendios

**PE-13 (3)** La organización emplea una funcionalidad de extinción automática de incendios para el sistema de información cuando la instalación no cuenta con personal de manera continua.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Los centros de datos de Microsoft Azure tienen un personal disponible de manera ininterrumpida. Los sistemas de extinción de incendios se activan de manera automática sin intervención manual cuando se detecta una alarma de incendios. |


 ## <a name="nist-800-53-control-pe-14a"></a>NIST 800-53 Control PE-14.a

#### <a name="temperature-and-humidity-controls"></a>Controles de temperatura y humedad

**PE-14.a** La organización mantiene los niveles de temperatura y humedad dentro de la instalación donde el sistema de información reside en [Asignación: niveles aceptables definidos por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure mantiene los niveles de temperatura y humedad en conformidad con las pautas de la Sociedad Estadounidense de Ingenieros en Calefacción, Refrigeración y Aire Acondicionado (ASHRAE). El sistema de administración de edificios (BMS) del centro de datos supervisa de manera continua los niveles de temperatura y humedad. |


 ## <a name="nist-800-53-control-pe-14b"></a>NIST 800-53 Control PE-14.b

#### <a name="temperature-and-humidity-controls"></a>Controles de temperatura y humedad

**PE-14.b** La organización supervisa los niveles de temperatura y humedad [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. En los centros de datos de Microsoft Azure, el sistema de administración de edificios (BMS) supervisa de manera continua los niveles de temperatura y humedad. El personal del centro de datos supervisa el BMS desde el FOC, por lo que pueden administrar la temperatura y la humedad dentro del centro de datos antes de que se supere cualquier punto de alarma. |


 ### <a name="nist-800-53-control-pe-14-2"></a>NIST 800-53 Control PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Controles de temperatura y humedad | Supervisión con alarmas o notificaciones

**PE-14 (2)** La organización emplea la supervisión de temperatura y humedad que proporciona una alarma o una notificación de los cambios potencialmente dañinos para el personal o el equipo.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. En los centros de datos de Microsoft Azure, el sistema de administración de edificios (BMS) supervisa de manera continua los niveles de temperatura y humedad. El personal del centro de datos supervisa el BMS desde el FOC, por lo que pueden administrar la temperatura y la humedad dentro del centro de datos antes de que se supere cualquier punto de alarma. |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800-53 Control PE-15

#### <a name="water-damage-protection"></a>Protección contra daños por el agua

**PE-15** La organización protege al sistema de información de los daños resultantes a partir de fugas de agua mediante válvulas maestras de aislamiento o de cierre a las que el personal clave conoce y a las que puede acceder y operar de manera correcta.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure proporciona la detección de agua o fugas en áreas en las que existe el riesgo de fugas de agua (por ejemplo, en las unidades de tratamiento de aire). Los sistemas de extinción de incendios también tienen alarmas de detección de fugas que se supervisan. El sistema de detección de agua o fugas está integrado con el sistema de notificación y alarmas de la instalación. Los sistemas de aspersión de los centros de datos están divididos por zonas. El personal del centro de datos está familiarizado con los procedimientos de emergencia en los que se requiere usar las válvulas de cierre del suministro de agua y sus ubicaciones. Es posible cerrar los aspersores de manera individual o como grupo, con las válvulas de paso. Todos los aspersores en un espacio crítico son tipos de aspersores de acción previa de interbloqueo doble que requieren dos formas de activación antes de que se inicie el flujo. Se supervisa la presión del sistema de aspersión, el que recibe una alarma en caso de una fuga de agua. |


 ### <a name="nist-800-53-control-pe-15-1"></a>NIST 800-53 Control PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>Protección contra daños por el agua | Compatibilidad con la automatización

**PE-15 (1)** La organización emplea mecanismos automatizados para detectar la presencia de agua en las proximidades del sistema de información y alerta a [Asignación: roles o personal definidos por la organización].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure emplea mecanismos automatizados para detectar la presencia de agua en los centros de datos y envía alertas al personal de los centros de datos. Azure proporciona la detección de agua o fugas en áreas en las que existe el riesgo de fugas de agua (por ejemplo, en las unidades de tratamiento de aire). Los sistemas de extinción de incendios también tienen alarmas de detección de fugas que se supervisan. El sistema de detección de agua o fugas está integrado con el sistema de notificación y alarmas de la instalación. Se supervisa la presión del sistema de aspersión, el que recibe una alarma en caso de una fuga de agua. |


 ## <a name="nist-800-53-control-pe-16"></a>NIST 800-53 Control PE-16

#### <a name="delivery-and-removal"></a>Entrega y eliminación

**PE-16** La organización autoriza, supervisa y controla [Asignación: los tipos de componentes del sistema de información definidos por la organización] que entran y salen de la instalación y mantiene registros de esos elementos.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure implementa este requisito en nombre de los clientes. Microsoft Azure implementa el cumplimiento estricto de los elementos que están autorizados a entrar y salir del centro de datos. Se lleva un registro de todos los activos y los componentes de sistema en la base de datos de la herramienta de administración de activos. |


 ## <a name="nist-800-53-control-pe-17a"></a>NIST 800-53 Control PE-17.a

#### <a name="alternate-work-site"></a>Lugar de trabajo alternativo

**PE-17.a** La organización emplea [Asignación: los controles de seguridad definidos por la organización] en sitios de trabajo alternativos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las disposiciones de lugares de trabajo de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-pe-17b"></a>NIST 800-53 Control PE-17.b

#### <a name="alternate-work-site"></a>Lugar de trabajo alternativo

**PE-17.b** La organización evalúa como factible la eficacia de los controles de seguridad en los lugares de trabajo alternativos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las disposiciones de lugares de trabajo de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800-53 Control PE-17.c

#### <a name="alternate-work-site"></a>Lugar de trabajo alternativo

**PE-17.c** La organización proporciona un medio para que los empleados se comuniquen con el personal de seguridad de la información en caso de problemas o incidentes de seguridad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las disposiciones de lugares de trabajo de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-pe-18"></a>NIST 800-53 Control PE-18

#### <a name="location-of-information-system-components"></a>Ubicación de los componentes del sistema de información

**PE-18** La organización coloca los componentes del sistema de información dentro de la instalación para minimizar el potencial daño a partir de [Asignación: peligros físicos y del entorno definidos por la organización] y para minimizar la oportunidad para que se produzca un acceso no autorizado.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Azure implementa un enfoque de diseño estratégico del centro de datos para cumplir con la ubicación del control de los componentes del sistema de información. Todo el equipo de los servicios en línea de Microsoft se encuentran en ubicaciones que se han diseñado para su protección ante riesgos del entorno, como robos, incendios, explosivos, humo, agua, polvo, vibraciones, sismos, productos químicos nocivos, interferencias eléctricas, apagones energéticos, disturbios eléctricos (alzas) y radiación. La instalación e infraestructura tienen soportes antisísmicos implementados como protección ante peligros ambientales. Toda la ubicación y las salas de MDF están protegidas a través de control de acceso, alarmas y vídeo. Los responsables de la seguridad patrullan de manera ininterrumpida las instalaciones. Todos los activos portátiles de Azure están bloqueados o fijados a fin de protegerlos de robos o de daños provocados por el movimiento. |


