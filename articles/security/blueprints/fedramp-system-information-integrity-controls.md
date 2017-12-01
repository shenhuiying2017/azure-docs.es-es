---
title: "Automatización del programa Azure Blueprint de FedRAMP: la integridad de la información y del sistema"
description: "Aplicaciones web para FedRAMP: la integridad de la información y del sistema"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 1dc6805a5a1f610f06ce58bd4bd644346436294e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-information-integrity-si"></a>Integridad de la información y del sistema

> [!NOTE]
> NIST y los Estados Unidos definen estos controles. Departamento de Comercio como parte de la publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

## <a name="nist-800-53-control-si-1"></a>NIST 800-53 Control SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Procedimientos y directiva de integridad de la información y el sistema

**SI-1** La organización desarrolla, documenta y difunde al [Asignación: personal o los roles definidos por la organización] una directiva de integridad de la información y el sistema que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; y los procedimientos para facilitar la implementación de la directiva de integridad de información y el sistema y los controles asociados de integridad e información y el sistema; y revisa y actualiza la directiva de integridad de la información y el sistema actual [Asignación: frecuencia definida por la organización], y los procedimientos de la directiva de integridad de la información y el sistema [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva y los procedimientos empresariales de integridad de la información y el sistema del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800-53 Control SI-2.a

#### <a name="flaw-remediation"></a>Corrección de errores

**SI-2.a** identifica la organización, notifica y corrige los errores del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Blueprint implementa la solución OMS Automation and Control para realizar un seguimiento del estado de las actualizaciones para las máquinas virtuales Windows implementadas en esta arquitectura. En el panel de OMS, el icono Update Management muestra el estado de corrección de errores para todos los servidores Windows implementados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800-53 Control SI-2.b

#### <a name="flaw-remediation"></a>Corrección de errores

**SI-2.b** La actualización prueba las actualizaciones del firmware y el software en relación con la corrección de errores para determinar la eficacia y los posibles efectos secundarios antes de la instalación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de probar las actualizaciones relacionadas con la corrección de errores con respecto a la eficacia y los posibles efectos secundarios antes de la instalación en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800-53 Control SI-2.c

#### <a name="flaw-remediation"></a>Corrección de errores

**SI-2.c** La organización instala las actualizaciones de software y firmware relativas a la seguridad dentro del [Asignación: período de tiempo definido por la organización] del lanzamiento de las actualizaciones.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales Windows implementadas por este programa Azure Blueprint se configuran de forma predeterminada para recibir actualizaciones automáticas de Windows Update Service. Esta solución también implementa la solución OMS Automation and Control a través de la cual se pueden crear implementaciones de actualizaciones para implementar revisiones en servidores Windows cuando sea necesario. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800-53 Control SI-2.d

#### <a name="flaw-remediation"></a>Corrección de errores

**SI-2.d** La organización incorpora corrección de errores en el proceso de administración de configuración de organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de incluir la corrección de errores en la administración de configuración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800-53 Control SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Corrección de errores | Administración central

**SI-2 (1)** La organización administra de forma centralizada el proceso de corrección de errores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa la solución OMS Automation and Control para realizar un seguimiento del estado de las actualizaciones para las máquinas virtuales Windows implementadas en esta arquitectura. En el panel de OMS, el icono Update Management muestra el estado de corrección de errores para todos los servidores Windows implementados. Se pueden crear implementaciones de actualizaciones para implementar revisiones de servidores Windows cuando sea necesario. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800-53 Control SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Corrección de errores | Estado de la corrección de errores automatizada

**SI-2 (2)** La organización emplea mecanismos automatizados [Asignación: frecuencia definida por la organización] para determinar el estado de los componentes del sistema de información con respecto a la corrección de errores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa la solución OMS Automation and Control para realizar un seguimiento del estado de las actualizaciones para las máquinas virtuales Windows implementadas en esta arquitectura. Para cada equipo Windows administrado, se realiza un examen dos veces al día. Cada 15 minutos, se llama a la API de Windows para consultar la hora de la última actualización y determinar si ha cambiado el estado y, de ser así, se inicia un examen de cumplimiento. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800-53 Control SI-2 (3).a

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Corrección de errores | Tiempo para corregir los errores o pruebas comparativas realizadas para aplicar medidas correctivas

**SI-2 (3).a** La organización mide el tiempo transcurrido entre la identificación de un error y su corrección.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de corregir los errores en los recursos implementados por él. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800-53 Control SI-2 (3).b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Corrección de errores | Tiempo para corregir los errores o pruebas comparativas realizadas para aplicar medidas correctivas

**SI-2 (3).b** La organización establece [Asignación: pruebas comparativas definidas por la organización] para adoptar medidas correctivas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los clientes pueden confiar en las pruebas comparativas empresariales para los procesos de corrección de errores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-3a"></a>NIST 800-53 Control SI-3.a

#### <a name="malicious-code-protection"></a>Protección frente a código malintencionado

**SI-3.a** La organización emplea mecanismos de protección frente a código malintencionado en los puntos de entrada y salida del sistema de información para detectar y erradicar el código malintencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante la extensión de máquina virtual Microsoft Antimalware. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-3b"></a>NIST 800-53 Control SI-3.b

#### <a name="malicious-code-protection"></a>Protección frente a código malintencionado

**SI-3.b** La organización actualiza los mecanismos de protección frente a código malintencionado cada vez que hay disponibles nuevas versiones con arreglo a los procedimientos y la directiva de administración de configuración de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante la extensión de máquina virtual Microsoft Antimalware. Esta extensión está configurada para actualizar automáticamente tanto las firmas de protección como el motor de antimalware cuando la versión está disponible. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-3c"></a>NIST 800-53 Control SI-3.c

#### <a name="malicious-code-protection"></a>Protección frente a código malintencionado

**SI-3.c** La organización configura mecanismos de protección frente a código malintencionado para realizar análisis periódicos del sistema de información [Asignación: frecuencia definida por la organización] y exploraciones en tiempo real de los archivos de orígenes externos en [Selección (uno o varios); punto de conexión; puntos de entrada y salida de red] a medida que los archivos se descargan, se abren o se ejecutan de conformidad con la directiva de seguridad de la organización; y [Selección (uno o varios): se bloquea el código malintencionado; se pone en cuarentena el código malintencionado; se envían alertas al administrador; [Asignación: acción definida por la organización]] en respuesta a la detección de código malintencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante la extensión de máquina virtual Microsoft Antimalware. Esta extensión se configura para realizar exámenes periódicos (semanalmente) y en tiempo real, actualizar de forma automática tanto el motor antimalware como las firmas de protección y realizar acciones de corrección automática. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800-53 Control SI-3.d

#### <a name="malicious-code-protection"></a>Protección frente a código malintencionado

**SI-3.d** La organización trata la recepción de falsos positivos durante la detección y erradicación del código malintencionado, y el posible impacto resultante en la disponibilidad del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es el responsable de la protección frente a código malintencionado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800-53 Control SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Protección frente a código malintencionado | Administración central

**SI-3 (1)** La organización administra de forma centralizada los mecanismos de protección frente a código malintencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante la extensión de máquina virtual Microsoft Antimalware. Azure OMS proporciona una funcionalidad centralizada para revisar el estado actual de la solución antimalware. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800-53 Control SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Protección frente a código malintencionado | Actualizaciones automáticas

**SI-3 (2)** El sistema de información actualiza automáticamente los mecanismos de protección frente a código malintencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante la extensión de máquina virtual Microsoft Antimalware. Esta extensión está configurada para actualizar automáticamente tanto las firmas de protección como el motor antimalware cuando la versión está disponible. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800-53 Control SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Protección frente a código malintencionado | Detección sin firma

**SI-3 (7)** El sistema de información implementa los mecanismos de detección sin firma de código malintencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa protecciones antimalware para todas las máquinas virtuales Windows implementadas mediante la extensión de máquina virtual Microsoft Antimalware. Esta extensión está configurada para realizar una detección heurística. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800-53 Control SI-4.a

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.a** La organización supervisa el sistema de información para detectar ataques e indicadores de posibles ataques de acuerdo con los [Asignación: objetivos de supervisión definidos por la organización]; y las conexiones locales no autorizadas, de red y remotas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa Log Analytics y la solución OMS Security and Audit. Esta solución proporciona una vista completa de la actitud frente a la seguridad, los ataques y los indicadores de posibles ataques. El panel Security and Audit proporciona un punto de vista de alto nivel sobre el estado de seguridad de los recursos implementados con los datos disponibles a través de las soluciones de OMS implementadas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800-53 Control SI-4.b

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.b** La organización identifica el uso no autorizado del sistema de información a través de [Asignación: métodos y técnicas definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa la solución OMS Security and Audit. El dominio Identify and Access (Identificación y acceso) proporciona un panel con información general sobre el estado de identificación del sistema de información, incluido el número de intentos con errores de inicio de sesión y el número actual de cuentas con sesiones iniciadas. La información disponible en este panel puede ayudarle de inmediato a identificar una posible actividad sospechosa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800-53 Control SI-4.c

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.c** La organización implementa los dispositivos de supervisión estratégicamente dentro del sistema de información para recopilar información esencial determinada por la organización; y en ubicaciones ad hoc dentro del sistema para realizar el seguimiento de determinados tipos de transacciones de interés para la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa Log Analytics y la solución OMS Security and Audit. El panel Security and Audit proporciona un punto de vista de alto nivel sobre el estado de seguridad de los recursos implementados con los datos disponibles a través de las soluciones de OMS implementadas, incluido el punto de vista sobre los datos de supervisión del sistema operativo de la máquina virtual. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800-53 Control SI-4.d

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.d** La organización protege la información obtenida de las herramientas de supervisión de la intrusión frente al acceso, la modificación y la eliminación no autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Para proteger la información de supervisión dentro de este programa Azure Blueprint frente a la eliminación, la modificación y el acceso no autorizados, se usan controles de acceso lógico. Azure Active Directory exige la aprobación del acceso lógico mediante la pertenencia a grupos basada en roles. La capacidad para ver información de supervisión y utilizar herramientas de supervisión queda limitada a los usuarios que requieran dichos permisos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4e"></a>NIST 800-53 Control SI-4.e

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.e** La organización aumenta el nivel de la actividad de supervisión del sistema de información siempre que haya una indicación de mayor riesgo para las operaciones de la organización y los recursos, las personas, otras organizaciones o la nación según la información de cumplimiento de las leyes, información de inteligencia u otros orígenes confiables de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar los recursos implementados por él. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800-53 Control SI-4.f

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.f** La organización obtiene opinión jurídica en relación con las actividades de supervisión del sistema de información de conformidad con las leyes federales, los decretos, las directivas y otra normativa aplicable.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar los recursos implementados por él. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800-53 Control SI-4.g

#### <a name="information-system-monitoring"></a>Supervisión del sistema de información

**SI-4.g** La organización proporciona la [Asignación: información de supervisión del sistema de información definida por la organización] para [Asignación: el personal o los roles definidos por la organización] [Selección (una o varias): según sea necesario; [Asignación: frecuencia definida por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar los recursos implementados por él. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800-53 Control SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Supervisión del sistema de información | Detección de intrusos en todo el sistema

**SI-4 (1)** La organización se conecta con las herramientas de detección de intrusiones individuales y las configura en un sistema de detección de intrusiones que abarca todo el entorno.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar los recursos implementados por él. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800-53 Control SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Supervisión del sistema de información | Herramientas automatizadas para el análisis en tiempo real

**SI-4 (2)** La organización emplea herramientas automatizadas para permitir el análisis casi en tiempo real de los eventos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa Log Analytics y varias soluciones de OMS, incluida Security and Audit. Log Analytics proporciona el análisis casi en tiempo real de los eventos a través de los recursos implementados. Las soluciones de OMS proporcionan una visión completa de la actitud frente a la seguridad entre los dominios de la solución. OMS proporciona un punto de vista sobre el estado de seguridad de los recursos implementados con los datos disponibles a través de las soluciones de OMS implementadas. OMS puede configurarse para generar alertas basadas en criterios definidos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 Control SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Supervisión del sistema de información | Tráfico de las comunicaciones entrantes y salientes

**SI-4 (4)** El sistema de información supervisa el tráfico de las comunicaciones entrantes y salientes [Asignación: frecuencia definida por la organización] para las condiciones o actividades inusuales o no autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar los recursos implementados por él. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800-53 Control SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Supervisión del sistema de información | Alertas generadas por el sistema

**SI-4 (5)** Las alertas del sistema de información [Asignación: personal o roles definidos por la organización] cuando se producen las siguientes indicaciones de riesgo o posible riesgo: [Asignación: indicadores de riesgo definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa varias soluciones de OMS, incluida Security and Audit. Log Analytics proporciona el análisis casi en tiempo real de los eventos a través de los recursos implementados. Las soluciones de OMS proporcionan una visión completa de la actitud frente a la seguridad entre los dominios de la solución. OMS puede configurarse para generar alertas basadas en criterios definidos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800-53 Control SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Supervisión del sistema de información | Análisis de las anomalías del tráfico de las comunicaciones

**SI-4 (11)** La organización analiza el tráfico de las comunicaciones salientes en el límite externo del sistema de información y los [Asignación: puntos interiores definidos por la organización dentro del sistema (p. ej., subredes y subsistemas)] seleccionados para la detección de anomalías.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de analizar las anomalías en el tráfico de las comunicaciones para los recursos que él implementa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800-53 Control SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Supervisión del sistema de información | Detección inalámbrica de intrusos

**SI-4 (14)** La organización emplea un sistema inalámbrico para la detección de intrusos a fin de identificar los dispositivos inalámbricos no autorizados y los intentos de ataques y posibles infracciones o brechas de seguridad en el sistema de información.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | No se permite ningún hardware controlado por el cliente, incluidos los dispositivos inalámbricos, en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure supervisa periódicamente las señales inalámbricas no autorizadas de forma trimestral, como se describe en AC-18. <br /> Microsoft Azure implementa este control en el nombre de los clientes PaaS e IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800-53 Control SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Supervisión del sistema de información | Correlación de la información de supervisión

**SI-4 (16)** La organización pone en correlación la información de las herramientas de supervisión empleadas en todo el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa Log Analytics y varias soluciones de OMS, incluida Security and Audit. OMS proporciona un punto de vista sobre el estado de seguridad de los recursos implementados con los datos disponibles a través de las soluciones de OMS implementadas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800-53 Control SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Supervisión del sistema de información | Análisis del tráfico o la exfiltración encubierta

**SI-4 (18)** La organización analiza el tráfico de las comunicaciones salientes en el límite externo del sistema de información (es decir, su perímetro) y en los [Asignación: puntos interiores definidos por la organización dentro del sistema (p. ej., subredes y subsistemas)] para detectar la exfiltración encubierta de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de analizar el tráfico de las comunicaciones para los recursos que implementa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800-53 Control SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Supervisión del sistema de información | Personas que suponen mayor riesgo

**SI-4 (19)** La organización implementa la [Asignación: supervisión adicional definida por la organización] de las personas que han sido identificadas por [asignación: los orígenes definidos por la organización] como que suponen un mayor nivel de riesgo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar las personas que suponen un riesgo mayor. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800-53 Control SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Supervisión del sistema de información | Usuarios con privilegios

**SI-4 (20)** La organización implementa una [Asignación: supervisión adicional definida por la organización] de los usuarios con privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar los usuarios con privilegios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800-53 Control SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Supervisión del sistema de información | Servicios de red no autorizados

**SI-4 (22)** El sistema de información detecta servicios de red no autorizados o no aprobados por [Asignación: procesos de autorización o aprobación definidos por la organización] y [Selección (uno o varios): auditorías; alertas [Asignación: personal o roles definidos por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de detectar los servicios de red no autorizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800-53 Control SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Supervisión del sistema de información | Dispositivos basados en host

**SI-4 (23)** La organización implementa [Asignación: mecanismos de supervisión basados en host definidos por la organización] en [Asignación: componentes del sistema de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint recopila los datos de supervisión de los recursos implementados, incluidos los de las funcionalidades de supervisión basadas en host. Microsoft Monitoring Agent está instalado en todas las máquinas virtuales Windows para recopilar datos de supervisión que Log Analytics y otras soluciones de OMS usan. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800-53 Control SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Supervisión del sistema de información | Indicadores de riesgo

**SI-4 (24)** El sistema de información detecta, recopila, distribuye y utiliza indicadores de riesgo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de detectar, recopilar, distribuir y usar indicadores de riesgo para los recursos que implementa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800-53 Control SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de seguridad, avisos y directivas

**SI-5.a** La organización recibe las alertas de seguridad del sistema de información, los avisos y las directivas de [Asignación: organizaciones externas definidas por la organización] de forma continuada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar las alertas de seguridad, los avisos y las directivas de los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800-53 Control SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de seguridad, avisos y directivas

**SI-5.b** La organización genera alertas de seguridad internas, avisos y directivas según sea necesario.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar las alertas de seguridad, los avisos y las directivas de los recursos que implemente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800-53 Control SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de seguridad, avisos y directivas

**SI-5.c** La organización distribuye las alertas de seguridad, los avisos y las directivas para: [Selección (uno o varios): [Asignación: el personal o los roles definidos por la organización]; [Asignación: los elementos definidos por la organización dentro de esta]; [Asignación: las organizaciones externas definidas por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar las alertas de seguridad, los avisos y las directivas de los recursos que implemente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800-53 Control SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de seguridad, avisos y directivas

**SI-5.d** La organización implementa directivas de seguridad con arreglo a los marcos de tiempo establecidos o notifica a la organización emisora el grado de incumplimiento.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar las alertas de seguridad, los avisos y las directivas de los recursos que implemente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800-53 Control SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alertas de seguridad, avisos y directivas | Alertas y avisos automatizados

**SI-5 (1)** La organización emplea mecanismos automatizados para que la información de asesoramiento y alertas de seguridad esté disponible en toda la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de administrar las alertas de seguridad, los avisos y las directivas de los recursos que implemente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800-53 Control SI-6.a

#### <a name="security-function-verification"></a>Comprobación de la función de seguridad

**SI-6.a** El sistema de información comprueba el funcionamiento correcto de [Asignación: las funciones de seguridad definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la comprobación de las funciones de seguridad para los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 Control SI-6.b

#### <a name="security-function-verification"></a>Comprobación de la función de seguridad

**SI-6.b** El sistema de información realiza esta comprobación [Selección (uno o varios): [Asignación: estados de transición del sistema definidos por la organización]; el comando del usuario con los privilegios adecuados; [Asignación: la frecuencia definida por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la comprobación de las funciones de seguridad de los recursos que él implementa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 Control SI-6.c

#### <a name="security-function-verification"></a>Comprobación de la función de seguridad

**SI-6.c** El sistema de información notifica [Asignación: al personal o los roles definidos por la organización] las pruebas de comprobación de seguridad que dan error.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la comprobación de las funciones de seguridad de los recursos que él implementa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800-53 Control SI-6.d

#### <a name="security-function-verification"></a>Comprobación de la función de seguridad

**SI-6.d** El sistema de información [Selección (uno o varios): cierra el sistema de información; lo reinicia; [Asignación: las acciones alternativas definidas por la organización]] cuando se detectan anomalías.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la comprobación de las funciones de seguridad de los recursos que él implementa. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-7"></a>NIST 800-53 Control SI-7

#### <a name="software-firmware-and-information-integrity"></a>Integridad de la información, el firmware y el software

**SI-7** La organización utiliza herramientas de comprobación de integridad para detectar cambios no autorizados en [Asignación: el software, el firmware y la información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por este programa Azure Blueprint ejecutan sistemas operativos Windows. Windows proporciona la validación de la integridad de los archivos en tiempo real, la protección y la recuperación de los archivos principales del sistema que se instalan como parte de Windows o de las actualizaciones del sistema de Windows autorizadas a través de la funcionalidad Protección de recursos de Windows (WRP). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800-53 Control SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Integridad de la información, el firmware y el software | Comprobaciones de integridad

**SI-7 (1)** El sistema de información realiza una comprobación de integridad del [Asignación: software, el firmware y la información definidos por la organización] [selección (uno o varios): en el inicio; en [Asignación: los estados de transición o los eventos relativos a la seguridad definidos por la organización]; [Asignación: la frecuencia definida por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por este programa Azure Blueprint ejecutan sistemas operativos Windows. Windows proporciona la validación de la integridad de los archivos en tiempo real, la protección y la recuperación de los archivos principales del sistema que se instalan como parte de Windows o de las actualizaciones del sistema de Windows autorizadas a través de la funcionalidad Protección de recursos de Windows (WRP). WRP habilita la comprobación de la integridad en tiempo real. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800-53 Control SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Integridad de la información, el firmware y el software | Notificaciones automatizadas de las infracciones de integridad

**SI-7 (2)**  La organización emplea herramientas automatizadas que proporcionan notificaciones para [Asignación: el personal o los roles definidos por la organización] tras la detección de discrepancias durante la comprobación de integridad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por este programa Azure Blueprint ejecutan sistemas operativos Windows. Windows proporciona la validación de la integridad de los archivos en tiempo real, la protección y la recuperación de los archivos principales del sistema que se instalan como parte de Windows o de las actualizaciones del sistema de Windows autorizadas a través de la funcionalidad Protección de recursos de Windows (WRP).  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800-53 Control SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Integridad de la información, el firmware y el software | Respuesta automatizada a las infracciones de la integridad

**SI-7.d (5)** El sistema de información [Selección (uno o varios): cierra el sistema de información; lo reinicia; implementa [Asignación: las medidas de protección alternativas definidas por la organización]] cuando se detectan infracciones de la integridad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de responder de forma automática a las infracciones de la integridad en los recursos que implemente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800-53 Control SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Integridad de la información, el firmware y el software | Integración de la detección y la respuesta

**SI-7 (7)** La organización incorpora la detección de los [Asignación: cambios relativos a la seguridad en el sistema de información definidos por la organización] no autorizados en la funcionalidad de respuesta a los incidentes organizativos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de proteger la integridad de la información y el software para los recursos que implemente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800-53 Control SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Integridad de la información, el firmware y el software | Código ejecutable binario o de máquina

**SI-7 (14)** La organización prohíbe el uso de código ejecutable de máquina o binario de orígenes con acceso limitado o sin ninguna garantía y sin la provisión del código fuente; y proporciona excepciones en el requisito de código fuente solo respecto a los requisitos operativos o con fines de persuasión, y con la aprobación de la entidad de autorización competente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de integridad de la información y del sistema empresarial del cliente pueden establecer requisitos para obtener el código fuente del código ejecutable binario o máquina de algunos orígenes. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-8a"></a>NIST 800-53 Control SI-8.a

#### <a name="spam-protection"></a>Protección de correo no deseado

**SI-8.a** La organización emplea mecanismos de protección frente a correo no deseado en los puntos de entrada y salida del sistema de información para detectarlo y adoptar acciones en los mensajes no solicitados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay ningún servidor de correo electrónico implementado como parte de este programa Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800-53 Control SI-8.b

#### <a name="spam-protection"></a>Protección de correo no deseado

**SI-8.b** La organización actualiza los mecanismos de protección frente a correo no deseado cada vez que hay disponibles nuevas versiones con arreglo a los procedimientos y la directiva de administración de configuración de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay ningún servidor de correo electrónico implementado como parte de este programa Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800-53 Control SI-8 (1)

#### <a name="spam-protection--central-management"></a>Protección frente a correo no deseado | Administración central

**SI-8 (1)** La organización administra de forma centralizada los mecanismos de protección frente al correo no deseado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay ningún servidor de correo implementado como parte de este programa Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800-53 Control SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Protección frente a correo no deseado | Actualizaciones automáticas

**SI-8 (2)** El sistema de información actualiza automáticamente los mecanismos de protección frente a correo no deseado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay ningún servidor de correo implementado como parte de este programa Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-10"></a>NIST 800-53 Control SI-10

#### <a name="information-input-validation"></a>Validación de la entrada de información

**SI-10** El sistema de información comprueba la validez de [Asignación: las entradas de información definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la comprobación de las entradas de información para los recursos que implemente (incluidos las aplicaciones, los sistemas operativos, las bases de datos y el software). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-11a"></a>NIST 800-53 Control SI-11.a

#### <a name="error-handling"></a>Control de errores

**SI-11.a** El sistema de información genera mensajes de error que proporcionan la información necesaria para aplicar medidas correctivas sin revelar datos que podrían ser aprovechados por adversarios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos implementados por este programa Azure Blueprint usan aplicaciones de software y sistemas operativos comerciales. Este software utiliza los procedimientos recomendados del sector para garantizar que no se revele información confidencial en los mensajes de error. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-11b"></a>NIST 800-53 Control SI-11.b

#### <a name="error-handling"></a>Control de errores

**SI-11.b** El sistema de información revela los mensajes de error solo [Asignación: al personal o los roles definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos implementados por este programa Azure Blueprint usan aplicaciones de software y sistemas operativos comerciales. Este software utiliza los procedimientos recomendados del sector para proporcionar mensajes de error adecuados en el contexto de los usos que recibe el mensaje. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-12"></a>NIST 800-53 Control SI-12

#### <a name="information-handling-and-retention"></a>Retención y tratamiento de la información

**SI-12** La organización trata y conserva la información dentro del sistema de información y la información devuelta por el sistema de acuerdo con las leyes federales aplicables, los decretos, las normativas, las regulaciones, los estándares y los requisitos operativos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de tratar y conservar la información dentro de los recursos que implemente (incluyendo las aplicaciones, los sistemas operativos, las bases de datos y el software) y la información de salida de esos recursos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-si-16"></a>NIST 800-53 Control SI-16

#### <a name="memory-protection"></a>Protección de la memoria

**SI-16** El sistema de información implementa [asignación: las medidas de seguridad definidas por la organización] para impedir la ejecución de código no autorizado en su memoria.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por este programa Azure Blueprint ejecutan sistemas operativos Windows. Windows dispone de protecciones para evitar la ejecución de código en ubicaciones de memoria restringidas: No ejecución (NX), Selección aleatoria del diseño del espacio de direcciones (ASLR) y Prevención de ejecución de datos (DEP). |
| **Proveedor (Microsoft Azure)** | No aplicable |
