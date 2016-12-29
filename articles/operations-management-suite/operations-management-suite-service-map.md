---
title: "Solución Mapa de servicio de Operations Management Suite (OMS) | Microsoft Docs"
description: "Mapa de servicio es una solución de Operations Management Suite (OMS) que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios.  En este artículo se proporciona información para implementar la solución Mapa de servicio en su entorno y utilizarla en distintos escenarios."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cbed591d15daf8060f13d0e9b009d65c85d256aa
ms.openlocfilehash: c5cd77dc53c36c4ad6c41eb876e24b4077d2007c


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>Uso de la solución Mapa de servicio de Operations Management Suite (OMS)
![Icono Administración de alertas](media/oms-service-map/icon.png) Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos.  Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente.

En este artículo se describen todos los detalles sobre cómo utilizar la característica Mapa de servicio.  Para obtener información sobre cómo configurar Mapa de servicio y los agentes integrados, consulte [Configuración de la solución Mapa de servicio de Operations Management Suite (OMS)](operations-management-suite-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de uso: Haga que sus procesos de TI tengan en cuenta la dependencia

### <a name="discovery"></a>Detección
Mapa de servicio crea automáticamente una asignación de referencias comunes de dependencias entre servidores, procesos y servicios de terceros.  Detecta y asigna todas las dependencias TCP, identificando las conexiones sorpresa, los sistemas de terceros remotos de los que depende y las dependencias para las áreas oscuras tradicionales de la red como DNS y AD.  Mapa de servicio detecta las conexiones de red con errores que los sistemas administrados están intentando realizar, lo que lo ayudará a identificar posibles errores de configuración del servidor, interrupciones de servicio y problemas de red.

### <a name="incident-management"></a>Administración de incidentes
Mapa de servicio ayuda a eliminar las suposiciones de aislamiento de problemas mostrando cómo se conectan los sistemas y afectan al resto.  Además de las conexiones con error, la información sobre los clientes conectados ayuda a identificar equilibradores de carga configurados incorrectamente, una carga sorprendente o excesiva en los servicios críticos y clientes no autorizados como equipos de desarrollador que tratan los sistemas de producción.  Los flujos de trabajo integrados con el seguimiento de cambios de OMS también permiten ver si un evento de cambio en un servicio o equipo back-end explica la causa raíz de un incidente.

### <a name="migration-assurance"></a>Garantía de migración
Mapa de servicio permite planear, acelerar y validar de forma eficaz las migraciones de Azure garantizando que nada se queda atrás y no hay interrupciones por sorpresa.  Puede detectar todos los sistemas interdependientes que tienen que migrarse juntos, evaluar la capacidad y la configuración del sistema e identificar si un sistema en ejecución sigue ofreciendo servicio a los usuarios o si es un candidato para la retirada en lugar de la migración.  Después de realizar el traslado, puede comprobar la carga del cliente y la identidad para comprobar que se están conectando los sistemas de prueba y los clientes.  Si las definiciones de firewall y planeación de la subred tienen problemas, los errores de conexión en las asignaciones de Mapa de servicio harán referencia a los sistemas que necesitan conectividad.

### <a name="business-continuity"></a>Continuidad del negocio
Si utiliza Azure Site Recovery y necesita ayuda para definir la secuencia de recuperación para su entorno de aplicaciones, Mapa de servicio puede automáticamente mostrarle cómo los sistemas dependen entre sí para asegurarse de que su plan de recuperación es confiable.  Al elegir un servidor crítico y ver sus clientes, puede identificar los sistemas front-end que deben recuperarse solo después de que ese servidor crítico esté restaurado y esté disponible.  Por el contrario, al examinar las dependencias de back-end de un servidor crítico, puede identificar aquellos sistemas que deben recuperarse antes de restaurar el sistema de foco.

### <a name="patch-management"></a>Administración de revisiones
Mapa de servicio mejora el uso de la evaluación de actualizaciones del sistema de OMS mostrando qué otros equipos y servidores dependen del servicio, por lo que informará con antelación antes de que afecte a los sistemas para la revisión.  Esta solución también mejora la administración de revisiones en OMS mostrando si los servicios están disponibles y conectados correctamente después de que se revisen y se reinicien.


## <a name="mapping-overview"></a>Información general de asignación
Los agentes de Mapa de servicio recopilan información sobre todos los procesos de conexión TCP en el servidor donde están instalados, así como detalles sobre las conexiones entrantes y salientes para cada proceso.  Mediante la lista de equipos en el lado izquierdo de la solución Mapa de servicio, se pueden seleccionar máquinas con agentes de Mapa de servicio para visualizar sus dependencias en un intervalo de tiempo seleccionado.  La dependencia de máquina asigna el foco en un equipo concreto y muestra todos los equipos que son clientes directos de TCP o servidores de la máquina.

![Introducción a Mapa de servicio](media/oms-service-map/service-map-overview.png)

Las máquinas se pueden expandir en la asignación para mostrar los procesos en ejecución con conexiones de red activas durante el intervalo de tiempo seleccionado.  Cuando un equipo remoto con un agente de Mapa de servicio se expande para mostrar los detalles del proceso, se muestran solo los procesos que se comunican con el equipo de foco.  Se indica el número de máquinas de front-end sin agente que se conecta al equipo de foco en el lado izquierdo de los procesos a los que se conectan.  Si el equipo de foco realiza una conexión a una máquina de back-end sin un agente, el back-end se representa con un nodo en el mapa y el nodo se puede expandir para mostrar los puertos individuales y los servicios que se comunican con la máquina de foco.

De forma predeterminada, las asignaciones de Mapa de servicio muestran los últimos 10 minutos de la información de dependencia.  Con los controles de tiempo en la parte superior izquierda, se pueden consultar las asignaciones para intervalos de tiempo históricos hasta durante una hora, para mostrar cómo se buscan dependencias en el pasado, por ejemplo, durante un incidente o antes de que se produzca un cambio.    Los datos de Mapa de servicio se almacenan durante 30 días en áreas de trabajo pagadas y durante 7 días en áreas de trabajo disponibles.

![Mapa de la máquina con propiedades de la máquina seleccionada](media/oms-service-map/machine-map.png)

## <a name="failed-connections"></a>Conexiones con errores
Las conexiones erróneas se muestran en las asignaciones de Mapa de servicio para procesos y equipos, con una línea roja discontinua que muestra si no se puede llegar a un proceso o un puerto de un sistema cliente.  Se informa de las conexiones erróneas de cualquier sistema con un agente de Mapa de servicio implementado si ese sistema es el que intenta establecer la conexión con errores.  Mapa de servicio mide esto mediante la observación de los sockets TCP que no pudieron establecer una conexión.  Esto puede deberse a un firewall, una configuración incorrecta en el cliente o a un servidor o un servicio remoto que no está disponible.

![Conexiones con errores](media/oms-service-map/failed-connections.png)

La comprensión de las conexiones con errores pueden ayudar a solucionar problemas, a la validación de la migración, el análisis de seguridad y al entendimiento general de la arquitectura.  A veces las conexiones con errores son inofensivas, pero a menudo indican directamente un problema, como un entorno de conmutación por error que de repente se convierte en inalcanzable,.. .o dos niveles de aplicación que no pueden comunicarse después de una migración a la nube.

## <a name="computer-and-process-properties"></a>Propiedades de proceso y de equipo
Al navegar por una asignación de Mapa de servicio, puede seleccionar equipos y procesos para obtener contexto adicional sobre sus propiedades.  Las máquinas proporcionan información sobre el nombre DNS, las direcciones IPv4, la capacidad de CPU y memoria, el tipo de VM, la versión del sistema operativo, el reinicio de última hora y los identificadores de sus agentes de OMS y Mapa de servicio.

![Propiedades de la máquina](media/oms-service-map/machine-properties.png)

Los detalles de proceso se recopilan de los metadatos del sistema operativo sobre procesos de ejecución, como el nombre del proceso, la descripción del proceso, el nombre de usuario y dominio (en Windows), el nombre de la compañía, el nombre del producto, la versión del producto, el directorio de trabajo, la línea de comandos y la hora de inicio del proceso.

![Propiedades del proceso](media/oms-service-map/process-properties.png)

El panel de resumen de procesos proporciona información adicional acerca de la conectividad de ese proceso, incluidos sus puertos de enlace, las conexiones entrantes y salientes y las conexiones con error.

![Resumen del proceso](media/oms-service-map/process-summary.png)

## <a name="computer-summary"></a>Resumen del equipo
El panel de resumen de la máquina incluye información general del sistema operativo de un servidor y el número de dependencias, además de diversos datos de otras soluciones de OMS, como Métricas de rendimiento, Seguimiento de cambios, Seguridad, Actualizaciones, etc.

![Resumen de la máquina](media/oms-service-map/machine-summary.png)

## <a name="oms-change-tracking-integration"></a>Integración del seguimiento de cambios de OMS
La integración de Mapa de servicio con Seguimiento de cambios es automática cuando ambas soluciones están habilitadas y configuradas en el área de trabajo de OMS.

El panel de seguimiento de cambios de la máquina muestra una lista de todos los cambios, la más reciente en primer lugar, junto con un vínculo para explorar en búsqueda de registro para obtener más detalles.
![Panel de seguimiento de cambios del equipo](media/oms-service-map/change-tracking.png)

A continuación se muestra una vista detallada del evento de cambio de configuración después de seleccionar **Mostrar en Log Analytics**.
![Evento de cambio de configuración](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>Integración de Rendimiento de OMS
En el panel de rendimiento de la máquina se muestran las métricas de rendimiento estándar del servidor seleccionado.  Algunas de las métricas son: Uso de la CPU, Uso de la memoria, Bytes de red enviados y recibidos, y una lista de los principales procesos que envía y recibe Bytes de red.
![Panel de seguimiento de cambios del equipo](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>Integración de Seguridad de OMS
La integración de Mapa de servicio con Seguridad y auditoría es automática cuando ambas soluciones están habilitadas y configuradas en el área de trabajo de OMS.

En el panel de seguridad de la máquina se muestran datos de la solución Seguridad y auditoría de OMS del servidor seleccionado.  El panel mostrará un resumen de los problemas de seguridad pendientes del servidor durante el intervalo de tiempo seleccionado.  Al hacer clic en cualquiera de los problemas de seguridad, se desglosará en una búsqueda de registros para obtener más información sobre los problemas de seguridad.
![Panel de seguimiento de cambios del equipo](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>Integración de Actualizaciones de OMS
La solución Integración de Mapa de servicio con Administración de actualizaciones es automática cuando ambas soluciones están habilitadas y configuradas en el área de trabajo de OMS.

En el panel de actualizaciones de la máquina se muestran datos de la solución Administración de actualizaciones de OMS del servidor seleccionado.  El panel mostrará un resumen de las actualizaciones que faltan en el servidor durante el intervalo de tiempo seleccionado.
![Panel de seguimiento de cambios del equipo](media/oms-service-map/machine-updates.png)


## <a name="oms-alert-integration"></a>Integración de Alerta de OMS
Mapa de servicio se integra con Alertas de OMS para mostrar las alertas activadas del servidor seleccionado en el intervalo de tiempo seleccionado.  El servidor mostrará un icono si hay alertas actuales, y el panel de alertas de la máquina enumerará las alertas

![Panel de alertas de la máquina](media/oms-service-map/machine-alerts.png)

Tenga en cuenta que para que Mapa de servicio pueda mostrar las alertas pertinentes, debe crearse la regla de alerta para que se desencadene en un equipo específico.  Para crear alertas apropiadas, realice estos pasos:
- Incluya una cláusula para realizar agrupaciones por equipo: "by Computer interval 1minute".
- Elija que se envíen alertas según Unidades métricas.

![Configuración de alertas](media/oms-service-map/alert-configuration.png)


## <a name="log-analytics-records"></a>Registros de Log Analytics
Los datos de inventario de equipos y procesos de Mapa de servicio están disponibles para realizar [búsquedas](../log-analytics/log-analytics-log-searches.md) en Log Analytics.  Esto se puede aplicar a escenarios como la planeación de la migración, el análisis de la capacidad, la detección y la solución de problemas de rendimiento ad hoc.

Se genera un registro por hora para cada equipo y proceso únicos, además de los registros generados cuando ese proceso o equipo se inicia o está integrado en Mapa de servicio.  Estos registros tienen las propiedades de las tablas siguientes.

Hay propiedades generadas internamente que puede usar para identificar los equipos y procesos únicos:

- PersistentKey_s se define de forma única mediante la configuración del proceso, por ejemplo, el identificador de usuario y la línea de comandos.  Es único para un equipo determinado, pero se puede repetir en varios equipos.
- ProcessId_s y ComputerId_s son globalmente únicos en el modelo de Mapa de servicio.



### <a name="servicemapcomputercl-records"></a>Registros de ServiceMapComputer_CL
Los registros con un tipo de **ServiceMapComputer_CL** tienen datos de inventario de servidores con agentes de Mapa de servicio.  Estos registros tienen las propiedades de la tabla siguiente:

| Propiedad | Descripción |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nombre de equipo de Windows o Linux |
| CPUSpeed_d | Velocidad de la CPU en MHz |
| DnsNames_s | Lista de todos los nombres DNS de este equipo |
| IPv4s_s | Lista de todas las direcciones IPv4 en uso en este equipo |
| IPv6s_s | Lista de todas las direcciones IPv6 en uso en este equipo.  (Mapa de servicio identifica las direcciones IPv6, pero no detecta las dependencias de IPv6). |
| Is64Bit_b | true o false según el tipo de sistema operativo |
| MachineId_s | Un GUID interno, único en un área de trabajo OMS  |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemVersion_s | Cadena de versión del sistema operativo larga |
| TimeGenerated | Fecha y hora en que se creó el registro. |
| TotalCPUs_d | Número de núcleos de la CPU |
| TotalPhysicalMemory_d | Capacidad de memoria en MB |
| VirtualMachine_b | true or false en función de si el sistema operativo es un invitado de VM |
| VirtualMachineID_g | Id. de VM de Hyper-V |
| VirtualMachineName_g | Nombre de VM de Hyper-V |
| VirtualMachineType_s | Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |


### <a name="servicemapprocesscl-type-records"></a>Registros con un tipo ServiceMapProcess_CL
Los registros con un tipo de **ServiceMapProcess_CL** tienen datos de inventario para procesos con conexión TCP en servidores con agentes de Mapa de servicio.  Estos registros tienen las propiedades de la tabla siguiente:

| Propiedad | Descripción |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Línea de comandos completa del proceso |
| CompanyName_s | Nombre de la empresa (desde Windows PE o Linux RPM) |
| Description_s | Descripción del proceso largo (desde Windows PE o Linux RPM) |
| FileVersion_s | Versión del archivo ejecutable (desde Windows PE, solo Windows) |
| FirstPid_d | Id. de proceso del sistema operativo |
| InternalName_s | Nombre interno del archivo ejecutable (desde Windows PE, solo Windows) |
| MachineId_s | Un GUID interno y único en un área de trabajo OMS  |
| Name_s | El nombre del archivo ejecutable de proceso |
| Path_s | Ruta de sistema de archivos del archivo ejecutable de proceso |
| PersistentKey_s | GUID interno único dentro de este equipo |
| PoolId_d | Identificador interno para la agregación de procesos basados en las líneas de comandos similares. |
| ProcessId_s | Un GUID interno y único en un área de trabajo OMS  |
| ProductName_s | Cadena de nombre de producto (desde Windows PE o Linux RPM) |
| ProductVersion_s | Cadena de versión de producto (desde Windows PE o Linux RPM) |
| StartTime_t | Hora de inicio de proceso en el reloj del equipo local |
| TimeGenerated | Fecha y hora en que se creó el registro. |
| UserDomain_s | Dominio del propietario del proceso (solo Windows) |
| UserName_s | Nombre del propietario del proceso (solo Windows) |
| WorkingDirectory_s | Directorio de trabajo del proceso |


## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Enumerar la capacidad de memoria física de todos los equipos administrados.
Type=ServiceMapComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

### <a name="list-computer-name-dns-ip-and-os-version"></a>Enumerar el nombre de equipo, DNS, IP y versión del sistema operativo.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Buscar todos los procesos con "sql" en la línea de comandos
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Después de ver los datos del evento para un proceso determinado, use su identificador de equipo para recuperar el nombre del equipo
Type=ServiceMapComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

### <a name="list-all-computers-running-sql"></a>Enumerar todos los equipos que ejecutan SQL
Type=ServiceMapComputer_CL MachineId_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Enumerar todas las versiones de producto únicas de curl en mi centro de datos
Type=ServiceMapProcess_CL Name_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Crear un grupo de equipos de todos los equipos que ejecutan CentOS
Type=ServiceMapComputer_CL OperatingSystemVersion_s = "CentOS" | Distinct ComputerName_s



## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y uso
Microsoft recopila automáticamente datos de uso y rendimiento a través del servicio Mapa de servicio. Microsoft usa estos datos para proporcionar y mejorar la calidad, la seguridad y la integridad del servicio Mapa de servicio. Los datos incluyen información sobre la configuración del software como sistema operativo y la versión, y también incluyen la dirección IP, el nombre DNS y el nombre de la estación de trabajo con el fin de proporcionar funcionalidades de solución de problemas precisas y eficientes. No recopilamos los nombres, las direcciones ni otra información de contacto.

Para obtener más información sobre el uso y la recopilación de datos, consulte la [Declaración de privacidad de Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre las [búsquedas de registro](../log-analytics/log-analytics-log-searches.md) en Log Analytics para recuperar los datos que recopila Mapa de servicio.



<!--HONumber=Nov16_HO4-->


