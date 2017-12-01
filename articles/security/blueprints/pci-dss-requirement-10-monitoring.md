---
title: "Plano técnico de procesamiento de pagos de Azure: requisitos de supervisión"
description: Requisito 10 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Requisitos de supervisión para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-10"></a>Requisito 10 de PCI DSS

**Realizar un seguimiento y una supervisión de todos los accesos a recursos de red y datos de los titulares de tarjetas**

> [!NOTE]
> Estos requisitos los define el [Consejo de Estándares de Seguridad para la Industria de Tarjeta de Pago (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte del [Estándar de Seguridad de Datos PCI (DSS) versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de pruebas e instrucciones para cada requisito.

Los mecanismos de registro y la capacidad de realizar un seguimiento de las actividades del usuario son fundamentales para evitar, detectar o minimizar el impacto de poner en riesgo datos. La presencia de registros en todos los entornos permite obtener análisis, alertas y seguimientos exhaustivos cuando se produce algún error. Determinar la causa de haber puesto en riesgo los datos es muy difícil, si no imposible, sin registros de actividad del sistema.

## <a name="pci-dss-requirement-101"></a>Requisito 10.1 de PCI DSS

**10.1** Implementación de pistas de auditoría para vincular todo el acceso a componentes del sistema a cada usuario individual.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure restringe el acceso a herramientas administrativas y de diagnóstico a personal autorizado con una responsabilidad de trabajo importante. Microsoft Azure restringe el acceso con privilegios a las herramientas usadas en el entorno de producción en función de los principios de privilegios mínimos. Microsoft Azure guarda y mantiene un registro de todos los accesos de usuarios individuales a los componentes del sistema de Microsoft Azure en el entorno de plataforma.<br /><br />Los componentes de la plataforma de Microsoft Azure (incluido el sistema operativo, CloudNet, Fabric, etc.) están configurados para registrar y recopilar eventos de seguridad. Se registra la actividad del administrador en la plataforma de Microsoft Azure. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore tiene un registro completo de todo el sistema, así como de la actividad del usuario (incluido el inicio de sesión de CHD). Para obtener más información, consulte [Guía de PCI: registro](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>Requisito 10.2 de PCI DSS

**10.2** Implementación de registros de auditoría automatizados para todos los componentes del sistema para reconstruir los siguientes eventos:
- **10.2.1** Todos los accesos de usuarios individuales a los datos de titulares de tarjetas
- **10.2.2** Todas las acciones realizadas por individuos con privilegios administrativos o de raíz
- **10.2.3** Acceso a todos los registros de auditoría
- **10.2.4** Intentos de acceso lógico no válidos
- **10.2.5** Cambios y uso de los mecanismos de identificación y autenticación, incluidas, entre otras acciones, la creación de cuentas nuevas y la elevación de privilegios, así como todos los cambios, adiciones o eliminaciones de cuentas con privilegios administrativos o de raíz
- **10.2.6** Inicialización, detenimiento o pausa de los registros de auditoría
- **10.2.7** Creación y eliminación de objetos del sistema

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure restringe el acceso a herramientas administrativas y de diagnóstico a personal autorizado con una responsabilidad de trabajo importante. Microsoft Azure restringe el acceso con privilegios a las herramientas usadas en el entorno de producción en función de los principios de privilegios mínimos. Microsoft Azure guarda y mantiene un registro de todos los accesos de usuarios individuales a los componentes del sistema de Microsoft Azure en el entorno de plataforma.<br /><br />Los componentes de la plataforma de Microsoft Azure (incluido el sistema operativo, CloudNet, Fabric, etc.) están configurados para registrar y recopilar eventos de seguridad. Se registra la actividad del administrador en la plataforma de Microsoft Azure. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore tiene un registro completo de todo el sistema, así como de la actividad del usuario, incluido el inicio de sesión de CHD. Para obtener más información, consulte [Guía de PCI: registro](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>Requisito 10.3 de PCI DSS

**10.3** Registrar, como mínimo, las siguientes entradas de registro de auditoría de todos los componentes del sistema para cada evento:
- **10.3.1** Identificación del usuario
- **10.3.2** Tipo de evento
- **10.3.3** Fecha y hora
- **10.3.4** Indicación de una operación correcta o errónea
- **10.3.5** Origen del evento
- **10.3.6** Identidad o nombre de los datos, del componente del sistema o del recurso que están afectados

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha establecido procedimientos para sincronizar los servidores y dispositivos de red del entorno de Microsoft Azure con los servidores de hora de capa 1 de NTP sincronizados con los satélites del sistema de posicionamiento global (GPS). La sincronización se realiza automáticamente cada cinco minutos. Microsoft Azure es responsable de garantizar que los hosts de servicio sincronicen la hora correctamente. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore registra la identificación del usuario, el tipo de evento, la marca de tiempo de fecha, eventos que no se realizaron correctamente, el origen del evento y el nombre del recurso según lo requiere el control 10.3.|



## <a name="pci-dss-requirement-104"></a>Requisito 10.4 de PCI DSS

**10.4** Al usar la tecnología de sincronización de hora, se sincronizan todos los relojes y las horas fundamentales del sistema y se garantiza que se implementa lo siguiente para adquirir, distribuir y almacenar la hora. 
> [!NOTE]
> Un ejemplo de tecnología de sincronización de hora es el protocolo de tiempo de redes (NTP).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha establecido procedimientos para sincronizar los servidores y dispositivos de red del entorno de Microsoft Azure con los servidores de hora de capa 1 de NTP sincronizados con los satélites del sistema de posicionamiento global (GPS). La sincronización se realiza automáticamente cada cinco minutos. Microsoft Azure es responsable de garantizar que los hosts de servicio sincronicen la hora correctamente. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Azure realiza la sincronización de hora para el servicio de PaaS.|



### <a name="pci-dss-requirement-1041"></a>Requisito 10.4.1 de PCI DSS

**10.4.1** Los sistemas críticos tienen la hora correcta y coherente.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Azure realiza la sincronización de hora para el servicio de PaaS.|



### <a name="pci-dss-requirement-1042"></a>Requisito 10.4.2 de PCI DSS

**10.4.2** Los datos de hora están protegidos.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Azure realiza la sincronización de hora para el servicio de PaaS.|



### <a name="pci-dss-requirement-1043"></a>Requisito 10.4.3 de PCI DSS

**10.4.3** La configuración de hora se recibe desde orígenes de hora aceptados por el sector.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Azure realiza la sincronización de hora para el servicio de PaaS.|



## <a name="pci-dss-requirement-105"></a>Requisito 10.5 de PCI DSS

**10.5** Protección de los registros de auditoría para que no se puedan modificar.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Las herramientas de FIM e identificadores se implementan dentro del entorno de Microsoft Azure. Microsoft Azure usa EWS para admitir el análisis en tiempo real de los eventos dentro de su entorno operativo. Los MA y AIM generan alertas próximas en tiempo real acerca de los eventos que podrían poner en peligro el sistema. <br /><br />El registro de eventos de seguridad, servicio y usuario (registros de servidor web, registros de servidor FTP, etc.) está habilitado y se conserva de forma centralizada. Azure restringe el acceso a los registros de auditoría para el personal autorizado según las responsabilidades de trabajo. Los registros de eventos se archivan en la infraestructura de archivo segura de Azure y se conservan durante 180 días. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona lo necesario para realizar la auditoría de todos los elementos a OMS. Las copias de seguridad de un origen externo pueden realizarse mediante [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>Requisito 10.5.1 de PCI DSS

**10.5.1** Limitar la visualización de registros de auditoría a los usuarios que tienen una necesidad relacionada con el trabajo.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona lo necesario para realizar la auditoría de todos los elementos a OMS. Las copias de seguridad de un origen externo pueden realizarse mediante [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>Requisito 10.5.2 de PCI DSS

**10.5.2** Proteger los archivos de registro de auditoría de modificaciones no autorizadas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona lo necesario para realizar la auditoría de todos los elementos a OMS. Las copias de seguridad de un origen externo pueden realizarse mediante [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>Requisito 10.5.3 de PCI DSS

**10.5.3** Realizar una copia de seguridad de forma puntual de los archivos de registro de auditoría en un soporte físico o en un servidor de registros centralizado que sea difícil de modificar.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona lo necesario para realizar la auditoría de todos los elementos a OMS. Las copias de seguridad de un origen externo pueden realizarse mediante [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>Requisito 10.5.4 de PCI DSS

**10.5.4** Escribir los registros para las tecnologías de uso externo en un dispositivo multimedia o servidor de registros interno, centralizado y seguro.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona lo necesario para realizar la auditoría de todos los elementos a OMS. Las copias de seguridad de un origen externo pueden realizarse mediante [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>Requisito 10.5.5 de PCI DSS

**10.5.5** Usar software de detección de cambios o supervisión de integridad de los archivos de registros para asegurarse de que no se pueden cambiar los datos de registro existentes sin generar alertas (aunque los datos nuevos que se agreguen no deberían causar ninguna alerta).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona lo necesario para realizar la auditoría de todos los elementos a OMS. Las copias de seguridad de un origen externo pueden realizarse mediante [Azure Backup](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>Requisito 10.6 de PCI DSS

**10.6** Revisar los registros y eventos de seguridad de todos los componentes del sistema para identificar anomalías o actividad sospechosa.
 
> [!NOTE]
> El registro de herramientas de recopilación, análisis y alerta se pueden usar para satisfacer este requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Las herramientas de FIM e identificadores se implementan dentro del entorno de Microsoft Azure. Microsoft Azure usa EWS para admitir el análisis en tiempo real de los eventos dentro de su entorno operativo. Los MA y AIM generan alertas próximas en tiempo real acerca de los eventos que podrían poner en peligro el sistema. <br /><br />El registro de eventos de seguridad, servicio y usuario (registros de servidor web, registros de servidor FTP, etc.) está habilitado y se conserva de forma centralizada. Azure restringe el acceso a los registros de auditoría para el personal autorizado según las responsabilidades de trabajo. Los registros de eventos se archivan en la infraestructura de archivo segura de Azure y se conservan durante 180 días. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Azure Security Center](https://azure.microsoft.com/services/security-center/) para supervisar y evitar las anomalías, así como para informar sobre estas. [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure.|



### <a name="pci-dss-requirement-1061"></a>Requisito 10.6.1 de PCI DSS

**10.6.1** Revise los siguientes aspectos a diario como mínimo:
- Todos los eventos de seguridad
- Registros de todos los componentes del sistema que almacenan, procesan o transmiten CHD o SAD
- Registros de todos los componentes fundamentales del sistema
- Registros de todos los servidores y componentes del sistema que realizan funciones de seguridad (por ejemplo, firewalls, sistemas de detección o prevención de intrusiones (id. o IP), servidores de autenticación, servidores de redireccionamiento de comercio electrónico, etc.).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Azure Security Center](https://azure.microsoft.com/services/security-center/) para supervisar y evitar las anomalías, así como para informar sobre estas. [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure.|



### <a name="pci-dss-requirement-1062"></a>Requisito 10.6.2 de PCI DSS

**10.6.2** Revisar los registros de todos los demás componentes del sistema periódicamente de acuerdo con la estrategia de administración de riesgos y las directivas de la organización, según lo determina la evaluación de riesgos anual de la organización.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Azure Security Center](https://azure.microsoft.com/services/security-center/) para supervisar y evitar las anomalías, así como para informar sobre estas. [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure.|



### <a name="pci-dss-requirement-1063"></a>Requisito 10.6.3 de PCI DSS

**10.6.3** Realizar un seguimiento de las excepciones y anomalías identificadas durante el proceso de revisión.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Azure Security Center](https://azure.microsoft.com/services/security-center/) para supervisar y evitar las anomalías, así como para informar sobre estas. [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure.|



## <a name="pci-dss-requirement-107"></a>Requisito 10.7 de PCI DSS

**10.7** Retener el historial de registros de auditoría durante al menos un año, y que durante un mínimo de tres meses ofrezca disponibilidad inmediata para el análisis (por ejemplo, en línea, archivado o que se pueda restaurar desde la copia de seguridad).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure conserva los registros de auditoría durante un año, con los 3 meses más recientes inmediatamente accesibles a través de su portal interno. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore tiene un registro completo de todo el sistema, así como de la actividad del usuario (incluido el inicio de sesión de CHD). Para obtener más información, consulte [Guía de PCI: registro](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>Requisito 10.8 de PCI DSS

**10.8** **Requisito adicional únicamente para proveedores de servicios:** implementar un proceso para la detección oportuna y la generación de informes de errores de sistemas de control de seguridad críticos, incluidos, entre otros:
- Firewalls
- Id. o direcciones IP
- FIM
- Antivirus
- Controles de acceso físicos
- Controles de acceso lógicos
- Mecanismos de registro de auditoría
- Controles de segmentación (en caso de que se usen) 

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018. Después, será un requisito.



**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa EWS para admitir el análisis en tiempo real de los eventos dentro de su entorno operativo. Los MA y AIM generan alertas próximas en tiempo real acerca de los eventos que podrían poner en peligro el sistema. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore tiene un registro completo de todo el sistema, así como de la actividad del usuario (incluido el inicio de sesión de CHD). Para obtener más información, consulte [Guía de PCI: registro](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>Requisito 10.8.1 de PCI DSS

**10.8.1** **Requisito adicional únicamente para proveedores de servicios:** responder a los errores de los controles de seguridad críticos de manera oportuna. Los procesos para responder a los errores de los controles de seguridad deben incluir las siguientes acciones:
- Restauración de las funciones de seguridad
- Identificación y documentación de la duración (principio a fin de fecha y hora) del error de seguridad
- Identificación y documentación de las causas del error, incluida la causa principal, y documentación de la corrección necesaria para tratar la causa principal
- Identificación y direccionamiento de los problemas de seguridad que se produjeron durante el error
- Realización de una evaluación de riesgos para determinar si son necesarias más acciones como resultado del error de seguridad
- Implementación de controles para evitar que se repita la causa del error: reanudación de la supervisión de los controles de seguridad 

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018. Después, será un requisito.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa EWS para admitir el análisis en tiempo real de los eventos dentro de su entorno operativo. Los MA y AIM generan alertas próximas en tiempo real acerca de los eventos que podrían poner en peligro el sistema. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore tiene un registro completo de todo el sistema, así como de la actividad del usuario (incluido el inicio de sesión de CHD). Para obtener más información, consulte [Guía de PCI: registro](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>Requisito 10.9 de PCI DSS

**10.9** Asegurarse de que las directivas de seguridad y los procedimientos operativos para supervisar todos los accesos a los recursos de redes y a los datos de titulares de tarjetas están documentados, en uso y que todas las partes afectadas los conocen.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona un caso de uso y una descripción sobre cómo se administra y protege el CHD.|




