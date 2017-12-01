---
title: "Azure Blueprint Automation de FedRAMP: Auditoría y rendición de cuentas"
description: "Aplicaciones web para FedRAMP: Auditoría y rendición de cuentas"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>Auditoría y rendición de cuentas (AU)

> [!NOTE]
> NIST y los Estados Unidos definen estos controles. Departamento de Comercio como parte de publicación especial de NIST 800-53 Revisión 4. Consulte el informe NIST 800-53 Rev. 4 para obtener información sobre la guía y los procedimientos de prueba de cada control.

## <a name="nist-800-53-control-au-1"></a>NIST 800-53 Control AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Procedimientos y directivas de auditoría y rendición de cuentas

**AU-1** La organización desarrolla, documenta y difunde a [Asignación: roles o personal definidos por la organización] una directiva de auditoría y rendición de cuentas que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; y los procedimientos para facilitar la implementación de la directiva de auditoría y rendición de cuentas y los controles de auditoría y rendición de cuentas asociados; y revisa y actualiza la directiva de auditoría y rendición de cuentas actual [Asignación: frecuencia definida por la organización], y los procedimientos de auditoría y rendición de cuentas [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de auditoría y rendición de cuentas de nivel empresarial y los procedimientos del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-2a"></a>NIST 800-53 Control AU-2.a

#### <a name="audit-events"></a>Auditoría de eventos

**AU-2.a** La organización determina que el sistema de información es capaz de auditar los eventos siguientes: [Asignación: eventos auditables definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Monitor y el servicio de Log Analytics en OMS proporcionan la funcionalidad de auditoría para esta instancia de Azure Blueprint. Azure Monitor proporciona registros de auditoría detallados sobre la actividad asociada con los recursos implementados. Log Analytics recopila estos registros y los registros en el nivel de sistema operativo y los almacena en el repositorio de OMS. Log Analytics pone en correlación los datos de auditoría entre los recursos que implementa esta solución y que se pueden extender a la aplicación web implementada por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-2b"></a>NIST 800-53 Control AU-2.b

#### <a name="audit-events"></a>Auditoría de eventos

**AU-2.b** La organización coordina la función de auditoría de seguridad con las demás entidades organizativas que requieren información relacionada con la auditoría para mejorar el respaldo mutuo y ayudar a guiar la selección de los eventos auditables.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede depender de un proceso establecido de nivel empresarial que determina los eventos auditables. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-2c"></a>NIST 800-53 Control AU-2.c

#### <a name="audit-events"></a>Auditoría de eventos

**AU-2.c** La organización proporciona un fundamento de por qué los eventos auditables se consideran como adecuados para respaldar las investigaciones posteriores al hecho de los incidentes de seguridad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los eventos que se auditan en esta instancia de Azure Blueprint incluyen la información suficiente para determinar cuándo se producen los eventos, el origen del evento, el resultado del mismo y otra información detallada que da respaldo a la investigación de los incidentes de seguridad. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-2d"></a>NIST 800-53 Control AU-2.d

#### <a name="audit-events"></a>Auditoría de eventos

**AU-2.d** La organización determina que los eventos siguientes se van a auditar dentro del sistema de información: [Asignación: eventos auditados definidos por la organización (el subconjunto de los eventos auditables que se definen en AU-2 a.) junto con la frecuencia de (o la situación que requiere) la auditoría de cada evento identificado].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los eventos que se auditan en esta instancia de Azure Blueprint incluyen los auditados en los registros de actividad de Azure para los recursos implementados, los registros en el nivel de sistema operativo, los registros de Active Directory y los registros de SQL Server. Los clientes pueden seleccionar eventos adicionales que se auditarán para cumplir las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 Control AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Eventos de auditoría | Revisiones y actualizaciones

**AU-2 (3)** La organización revisa y actualiza los eventos auditados [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede depender de un proceso periódico de revisión y actualización establecido de nivel empresarial para el conjunto de eventos auditados definido. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-3"></a>NIST 800-53 Control AU-3

#### <a name="content-of-audit-records"></a>Contenido de los registros de auditoría

**AU-3** El sistema de información genera los registros de auditoría que contienen la información que establece el tipo de evento que se produjo, cuándo se produjo el evento, dónde se produjo, el origen del evento, el resultado del mismo y la identidad de cualquier individuo o sujeto asociado con el evento.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint se basa en las funcionalidades de auditoría integradas de Azure, Windows Server y SQL Server. Estas soluciones de auditoría capturan los registros de auditoría con el detalle suficiente para cumplir los requisitos de este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 Control AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Contenido de registros de auditoría | Información adicional de auditoría

**AU-3 (1)** El sistema de información genera registros de auditoría que contienen la información adicional siguiente: [Asignación: información adicional más detallada definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los eventos de Azure Activity Log usan un esquema detallado que contiene campos para más de 20 tipos de información de auditoría. Además de Activity Log, esta instancia de Azure Blueprint implementa la solución de Log Analytics en OMS que admite un conjunto diverso de orígenes de datos, incluidos registros de Windows, registros de Linux, registros de Azure Diagnostics y registros de cliente.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 Control AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Contenido de los registros de auditoría | Administración centralizada del contenido de los registros de auditoría planeada

**AU-3 (2)** El sistema de información proporciona la configuración y administración centralizada del contenido que se va a capturar en los registros de auditoría generados por [Asignación: componentes del sistema de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todas las máquinas virtuales que se implementan en esta instancia de Azure Blueprint están unidas al dominio implementado de Active Directory. Todas las máquinas virtuales unidas a un dominio implementan una directiva de grupo que se pueden configurar para administrar de manera centralizada la configuración del sistema de auditoría en el nivel de sistema operativo. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-4"></a>NIST 800-53 Control AU-4

#### <a name="audit-storage-capacity"></a>Capacidad de almacenamiento de auditoría

**AU-4** La organización asigna la capacidad de almacenamiento de los registros de auditoría en conformidad con [Asignación: requisitos de almacenamiento de registros de auditoría definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint asigna capacidad de almacenamiento suficiente para conservar los registros de auditoría durante un período de un año. Log Analytics, que está configurado para una retención de un año, recopila todos los registros de auditoría. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-5a"></a>NIST 800-53 Control AU-5.a

#### <a name="response-to-audit-processing-failures"></a>Respuesta a errores de procesamiento de auditoría

**AU-5.a** El sistema de información envía una alerta a [Asignación: roles o personal definidos por la organización] en caso de un error de procesamiento de la auditoría.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El estado del servicio de Azure Monitor y Log Analytics está disponible en el sitio web de estado de Azure y en la hoja de estado del servicio en Azure Portal. Las alertas se pueden configurar a través de Log Analytics para proporcionar notificaciones de otros tipos de errores de procesamiento de auditoría. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-5b"></a>NIST 800-53 Control AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Respuesta a errores de procesamiento de auditoría

**AU-5.b** El sistema de información realiza las acciones adicionales siguientes: [Asignación: acciones definidas por la organización que se van a realizar (por ejemplo, apagar el sistema de información, sobrescribir los registros de auditoría anteriores, dejar de generar los registros de auditoría)].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Log Analytics recopila todos los registros de auditoría generados por los recursos que se implementan en esta instancia de Azure Blueprint y se mantienen durante el período de un año. La asignación de almacenamiento para este almacenamiento de los registros de auditoría se realiza de manera dinámica para garantizar que hay capacidad suficiente disponible. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 Control AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Respuesta a errores de procesamiento de auditoría | Capacidad de almacenamiento de auditoría

**AU-5 (1)** El sistema de información proporciona una advertencia a [Asignación: ubicaciones, roles o personal definidos por la organización] dentro de [Asignación: período de tiempo definido por la organización] cuando el volumen de almacenamiento de los registros de auditoría alcanza [Asignación: porcentaje definido por la organización] de la capacidad máxima de almacenamiento de los registros de auditoría del repositorio.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Log Analytics recopila todos los registros de auditoría generados por los recursos que se implementan en esta instancia de Azure Blueprint y se mantienen durante el período de un año. La asignación de almacenamiento para este almacenamiento de los registros de auditoría se realiza de manera dinámica para garantizar que hay capacidad suficiente disponible. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 Control AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Respuesta de errores de procesamiento de auditoría | Alertas en tiempo real

**AU-5 (2)** El sistema de información proporciona una alerta en [Asignación: período en tiempo real definido por la organización] a [Asignación: ubicaciones, roles o personal definidos por la organización] cuando se producen los eventos de error de auditoría siguientes: [Asignación: eventos de error de auditoría definidos por la organización que necesitan alarmas en tiempo real].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El estado del servicio de Azure está disponible en la hoja de estado del servicio en Azure Portal. Las alertas se pueden configurar a través de Log Analytics para proporcionar notificaciones de otros tipos de errores de procesamiento de auditoría. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-6a"></a>NIST 800-53 Control AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Revisión, análisis e informes de auditoría

**AU-6.a** La organización revisa y analiza los registros de auditoría del sistema de información [Asignación: frecuencia definida por la organización] en las indicaciones de [Asignación: actividad no habitual o inadecuada definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar y analizar los registros de auditoría de los recursos implementados por el cliente (incluidas las aplicaciones, los sistemas operativos, las bases de datos y el software). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800-53 Control AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Revisión, análisis e informes de auditoría

**AU-6.b** La organización informa las conclusiones a [Asignación: roles o personal definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de informar las conclusiones de la actividad no habitual o inadecuada (que se define en AU-06.a) en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 Control AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Revisión, análisis e informes de auditoría | Integración de procesos

**AU-6 (1)** La organización emplea mecanismos automatizados para integrar los procesos de revisión, análisis e informes de auditoría para admitir los procesos organizacionales para investigar y responder a las actividades sospechosas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede depender del planeamiento de la funcionalidad centralizada de revisión, análisis e informe de auditoría de nivel empresarial. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 Control AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Revisión, análisis e informes de auditoría | Correlación de los repositorios de auditoría

**AU-6 (3)** La organización analiza y pone en correlación los registros de auditoría en distintos repositorios para obtener conocimiento sobre la situación en toda la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa la solución de Log Analytics en OMS para centralizar los datos de auditoría en todos los recursos implementados, con lo que se admite el conocimiento sobre la situación en toda la organización. Los clientes pueden elegir seguir integrando Log Analytics con otros sistemas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 Control AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Revisión, análisis e informes de auditoría | Revisión y análisis central

**AU-6 (4)** El sistema de información proporciona la funcionalidad de revisar y analizar de manera central los registros de auditoría de varios componentes dentro del sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa la solución de Log Analytics en OMS para centralizar los datos de auditoría en todos los recursos implementados, con lo que se admite la revisión, el análisis y los informes centralizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 Control AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Revisión, análisis e informes de auditoría | Integración y funcionalidades de examen y supervisión

**AU-6 (5)** La organización integra el análisis de los registros de auditoría con el análisis de [Selección (una o varias opciones): información de examen de vulnerabilidades; datos de rendimiento; información de supervisión del sistema de información; [Asignación: Información y datos definidos por la organización recopilados de otros orígenes]] para mejorar aún más la capacidad de identificar actividad no habitual o inadecuada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa la solución OMS Security and Audit. Esta solución proporciona una visión completa de la actitud frente a la seguridad. El panel Security and Audit proporciona un punto de vista de alto nivel sobre el estado de seguridad de los recursos implementados con los datos disponibles a través de las soluciones de OMS implementadas, integrando los datos de registro y los datos de vulnerabilidad desde la línea de base y la valoración de revisiones. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 Control AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Revisión, análisis e informes de auditoría | Correlación con la supervisión física

**AU-6 (6)** La organización pone en correlación la información proveniente de los registros de auditoría con la información que se obtiene de la supervisión del acceso físico para seguir mejorando la capacidad de identificar actividad sospechosa, inadecuada, no habitual o malintencionada.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | El equipo SIM de Microsoft Azure usa los datos de supervisión física relacionados y los pone en correlación con los registros de auditoría para determinar si se produjo alguna infracción de seguridad lógica o un comportamiento sospechoso asociado cuando se detectan incidentes físicos. |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 Control AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Revisión, análisis e informes de auditoría | Acciones permitidas

**AU-6 (7)** La organización especifica las acciones permitidas para cada [Selección (una o varias opciones): usuario, rol, proceso del sistema de información] que esté asociado con la revisión, el análisis y los informes de la información de auditoría.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales Windows que se implementan en esta instancia de Azure Blueprint implementan permisos en el nivel de sistema operativo que restringen las acciones que un usuario puede llevar a cabo con respecto a la información de auditoría. Dentro de Azure, los usuarios o grupos de usuarios se pueden asignar a roles (por ejemplo, propietario, colaborador, lector o un rol personalizado) para restringir las acciones disponibles respecto de cualquier recurso o solución implementada, incluido Log Analytics.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 Control AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Revisión, análisis e informes de auditoría | Ajuste en el nivel de auditoría

**AU-6 (10)** La organización ajusta el nivel de la revisión, el análisis y los informes de auditoría dentro del sistema de información cuando se produce un cambio en el riesgo basado en la información de autoridades judiciales, la información de inteligencia u otros orígenes de información confiables.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de ajustar el nivel de revisión, análisis e informes de auditoría de los recursos implementados por el cliente (incluidas aplicaciones, sistemas operativos, bases de datos y software) cuando se produce un cambio en el riesgo basado en la información proporcionada por orígenes judiciales, de inteligencia u otros orígenes confiables. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-7a"></a>NIST 800-53 Control AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>Reducción de auditoría y generación de informes

**AU-7.a** El sistema de información proporciona una funcionalidad de reducción de auditoría y generación de informes que admite los requisitos de revisión, análisis e informes de auditoría a petición y las investigaciones de los incidentes de seguridad después del hecho.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa la solución de Log Analytics en OMS. Log Analytics proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. Una vez recopilados, los datos están disponibles para las alertas, el análisis y la exportación. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-7b"></a>NIST 800-53 Control AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>Reducción de auditoría y generación de informes

**AU-7.b** El sistema de información proporciona una funcionalidad de reducción de auditoría y generación de informes que no altera el contenido original ni el orden cronológico de los registros de auditoría.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa la solución de Log Analytics en OMS. Log Analytics proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. El contenido y el orden cronológico de los registros de auditoría no se ven alterados cuando Log Analytics los recopila. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 Control AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Reducción de auditoría y generación de informes | Procesamiento automático

**AU-7 (1)** El sistema de información proporciona la funcionalidad de procesar los registros de auditoría para eventos de interés en función de [Asignación: campos de auditoría definidos por la organización dentro de los registros de auditoría].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa la solución de Log Analytics en OMS. Log Analytics proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. Una vez recopilados, los datos están disponibles para las alertas, el análisis y la exportación. Log Analytics incluye un lenguaje de consulta eficaz para extraer los datos almacenados en el repositorio. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-8a"></a>NIST 800-53 Control AU-8.a

#### <a name="time-stamps"></a>Marcas de tiempo

**AU-8.a** El sistema de información usa relojes internos del sistema para generar marcas de tiempo para los registros de auditoría.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos que implementa esta instancia de Azure Blueprint usan relojes internos del sistema para generar marcas de tiempo para los registros de auditoría. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-8b"></a>NIST 800-53 Control AU-8.b

#### <a name="time-stamps"></a>Marcas de tiempo

**AU-8.b** El sistema de información registra las marcas de tiempo de los registros de auditoría que se pueden asignar a la Hora universal coordinada (UTC) o a la Hora de Greenwich (GMT) [Asignación: granularidad de la medición de tiempo definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos que implementa esta instancia de Azure Blueprint usan relojes internos del sistema para generar marcas de tiempo para los registros de auditoría. Las marcas de tiempo se registran en UTC. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-8-1a"></a>NIST 800-53 Control AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Marcas de tiempo | Sincronización con el origen autorizado de la hora

**AU-8 (1).a** El sistema de información compara los relojes internos del sistema de información [Asignación: frecuencia definida por la organización] con [Asignación: origen autorizado de la hora definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos que implementa esta instancia de Azure Blueprint usan relojes internos del sistema para generar marcas de tiempo para los registros de auditoría. Los relojes internos del sistema están configurados para sincronizarse con un origen autorizado de la hora. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800-53 Control AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Marcas de tiempo | Sincronización con el origen autorizado de la hora

**AU-8 (1).b** El sistema de información sincroniza los relojes internos del sistema con el origen autorizado de la hora cuando la diferencia horaria es mayor que [Asignación: período de tiempo definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos que implementa esta instancia de Azure Blueprint usan relojes internos del sistema para generar marcas de tiempo para los registros de auditoría. Los relojes internos del sistema están configurados para sincronizarse con un origen autorizado de la hora. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-9"></a>NIST 800-53 Control AU-9

#### <a name="protection-of-audit-information"></a>Protección de la información de auditoría

**AU-9** El sistema de información protege las herramientas de auditoría y la información de auditoría del acceso, la modificación y la eliminación no autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Para proteger las herramientas y la información de auditoría dentro de esta instancia de Azure Blueprint frente a la eliminación, la modificación y el acceso no autorizados, se usan controles de acceso lógicos. Azure Active Directory exige la aprobación del acceso lógico mediante la pertenencia a grupos basada en roles. La capacidad para ver información de auditoría y usar las herramientas de auditoría puede estar limitada a los usuarios que requieran dichos permisos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 Control AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Protección de la información de auditoría | Audit Backup on Separate Physical Systems / Copia de seguridad de la auditoría de componentes o sistemas físicos independientes

**AU-9 (2)** El sistema de información crea copias de seguridad de los registros de auditoría [Asignación: frecuencia definida por la organización] en un componente de sistema o un sistema físico distinto del sistema o componente que se audita.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio de Log Analytics en OMS. Las cuentas de almacenamiento de Azure Diagnostics y las máquinas virtuales implementadas son orígenes conectados a Log Analytics y se mantienen independientemente de su origen. OMS recopila los datos prácticamente en tiempo real. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 Control AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Protección de la información de auditoría | Protección criptográfica

**AU-9 (3)** El sistema de información implementa mecanismos de criptografía para proteger la integridad de la información de auditoría y las herramientas de auditoría.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio de Log Analytics en OMS. Log Analytics asegura que los datos entrantes provienen de una fuente de confianza mediante la validación de certificados y la integridad de los datos con la autenticación de Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 Control AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Protección de la información de auditoría | Acceso por subconjunto de usuarios con privilegios

**AU-9 (4)**La organización autoriza el acceso a la administración de la funcionalidad de auditoría solo a [Asignación: subconjunto de usuarios con privilegios definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Para proteger las herramientas y la información de auditoría dentro de esta instancia de Azure Blueprint frente a la eliminación, la modificación y el acceso no autorizados, se usan controles de acceso lógicos. Azure Active Directory exige la aprobación del acceso lógico mediante la pertenencia a grupos basada en roles. La capacidad para ver información de auditoría y usar las herramientas de auditoría puede estar limitada a los usuarios que requieran dichos permisos.
 |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-10"></a>NIST 800-53 Control AU-10

#### <a name="non-repudiation"></a>No rechazo

**AU-10** El sistema de información se protege contra un individuo (o un proceso que actúa en nombre de un individuo) al denegar falsamente haber realizado [Asignación: acciones definidas por la organización que cubrirá el no rechazo].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Monitor y el servicio de Log Analytics en OMS proporcionan la funcionalidad de auditoría para esta instancia de Azure Blueprint. Azure Monitor proporciona registros de auditoría detallados sobre la actividad asociada con los recursos implementados. Log Analytics recopila estos registros y los registros en el nivel de sistema operativo y los almacena en el repositorio de OMS. Estos registros contienen registros detallados de los eventos del sistema de información y pueden ayudar a proteger contra el no rechazo. Además, el acceso a los datos de registros está restringido al uso del control de acceso basado en rol para evitar la modificación o eliminación no autorizada de los datos de registros. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-11"></a>NIST 800-53 Control AU-11

#### <a name="audit-record-retention"></a>Retención de los registros de auditoría

**AU-11** La organización mantiene los registros de auditoría durante [Asignación: período de tiempo definido por la organización coherente con la directiva de retención de los registros] para permitir las investigaciones posteriores al hecho de los incidentes de seguridad y para cumplir los requisitos de retención de la información reglamentarios y organizacionales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio de Log Analytics en OMS. Log Analytics proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. Una vez que se recopilan los datos, se mantienen durante un año según la configuración de Log Analytics. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-12a"></a>NIST 800-53 Control AU-12.a

#### <a name="audit-generation"></a>Generación de auditoría

**AU-12.a** El sistema de información proporciona la funcionalidad de generación de registros de auditoría para los eventos auditables definidos en AU-2 a. en [Asignación: componentes del sistema de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los eventos que se auditan en esta instancia de Azure Blueprint incluyen los auditados en los registros de actividad de Azure para los recursos implementados, los registros en el nivel de sistema operativo, los registros de Active Directory y los registros de SQL Server. Los clientes pueden seleccionar eventos adicionales que se auditarán para cumplir las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-12b"></a>NIST 800-53 Control AU-12.b

#### <a name="audit-generation"></a>Generación de auditoría

**AU-12.b** El sistema de información permite que [Asignación: roles o personal definidos por la organización] seleccione los eventos auditables que serán auditados por componentes específicos del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso a las funciones de auditoría se restringe mediante el control de acceso basado en rol dentro de Azure y en el nivel de sistema operativo de la máquina virtual. Los usuarios con la autorización basada en rol correspondiente pueden establecer la configuración de los eventos seleccionados para ser auditados por los recursos que implementa esta instancia de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-au-12c"></a>NIST 800-53 Control AU-12.c

#### <a name="audit-generation"></a>Generación de auditoría

**AU-12.c** El sistema de información genera registros de auditoría para los eventos definidos en AU-2.d. con el contenido definido en AU-3.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los eventos que se auditan en esta instancia de Azure Blueprint incluyen los auditados en los registros de actividad de Azure para los recursos implementados, los registros en el nivel de sistema operativo, los registros de Active Directory y los registros de SQL Server. Los clientes pueden seleccionar eventos adicionales que se auditarán para cumplir las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 Control AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Generación de auditoría | Pista de auditoría con correlación de tiempo en todo el sistema

**AU-12 (1)** El sistema de información compila los registros de auditoría desde [Asignación: componentes del sistema de información definidos por la organización] en una pista de auditoría (lógica o física) de todo el sistema que tiene correlación de tiempo dentro de [Asignación: nivel de tolerancia definido por la organización para la relación entre las marcas de tiempo de los registros individuales de la pista de auditoría].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio de Log Analytics en OMS. Log Analytics proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. Las marcas de tiempo del registro de auditoría no se modifican, por lo que la pista de auditoría está correlacionada con el tiempo. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 Control AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Generación de auditoría | Cambios por individuos autorizados

**AU-12 (3)** El sistema de información proporciona la funcionalidad para que [Asignación: roles o individuos definidos por la organización] cambien la auditoría que se realizará en [Asignación: componentes del sistema de información definidos por la organización] en función de [Asignación: criterios de eventos seleccionables definidos por la organización] dentro de [Asignación: umbrales de tiempo definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso a las funciones de auditoría se restringe mediante el control de acceso basado en rol dentro de Azure y en el nivel de sistema operativo de la máquina virtual. Los usuarios con la autorización basada en rol correspondiente pueden establecer la configuración de los eventos seleccionados para ser auditados por los recursos que implementa esta instancia de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |
