---
title: "Uso de la solución Service Map de Operations Management Suite | Microsoft Docs"
description: "Mapa de servicio es una solución de Operations Management Suite que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. En este artículo se proporciona información para implementar la solución Mapa de servicio en su entorno y utilizarla en distintos escenarios."
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
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: 2e5475a0563549ddfaa2c146e4acf94c019841ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-map-solution-in-operations-management-suite"></a>Uso de la solución Service Map de Operations Management Suite
Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Con Service Map puede ver los servidores en la forma en que piensa en ellos: como sistemas interconectados que ofrecen servicios críticos. Service Map muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin necesidad de ninguna configuración más allá de la instalación de un agente.

En este artículo se describen todos los detalles sobre cómo utilizar la característica Mapa de servicio. Para más información sobre cómo configurar Service Map y los agentes integrados, vea [Configuración de la solución Mapa de servicio de Operations Management Suite (OMS)](operations-management-suite-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de uso: Haga que sus procesos de TI tengan en cuenta la dependencia

### <a name="discovery"></a>Detección
Mapa de servicio crea automáticamente una asignación de referencias comunes de dependencias entre servidores, procesos y servicios de terceros. Detecta y asigna todas las dependencias TCP al identificar las conexiones sorpresa, los sistemas remotos de terceros de los que depende y las dependencias con áreas oscuras tradicionales de la red, como Active Directory. Service Map detecta las conexiones de red con errores que los sistemas administrados están intentando realizar, lo que ayuda a identificar posibles errores de configuración del servidor, interrupciones de servicio y problemas de red.

### <a name="incident-management"></a>Administración de incidentes
Mapa de servicio ayuda a eliminar las suposiciones de aislamiento de problemas mostrando cómo se conectan los sistemas y afectan al resto. Además de identificar las conexiones con errores, ayuda a identificar equilibradores de carga configurados incorrectamente, cargas sorprendentes o excesivas en servicios críticos y clientes no autorizados, como equipos de desarrollador que se comunican con sistemas de producción. Al usar flujos de trabajo integrados con Operations Management Suite Change Tracking, también puede ver si un evento de cambio en un servicio o equipo back-end explica la causa raíz de un incidente.

### <a name="migration-assurance"></a>Garantía de migración
El empleo de Service Map permite planear, acelerar y validar de forma eficaz las migraciones de Azure, lo que ayuda a garantizar que nada se quede atrás y que no se produzcan interrupciones por sorpresa. Puede detectar todos los sistemas interdependientes que tienen que migrarse juntos, evaluar la capacidad y la configuración del sistema e identificar si un sistema en ejecución sigue ofreciendo servicio a los usuarios o si es un candidato para la retirada en lugar de la migración. Después de realizar la migración, puede ver la carga y la identidad del cliente para comprobar que los sistemas de prueba y los clientes se están conectando. Si las definiciones de firewall y planeación de la subred tienen problemas, los errores de conexión en las asignaciones de Service Map harán referencia a los sistemas que necesitan conectividad.

### <a name="business-continuity"></a>Continuidad del negocio
Si utiliza Azure Site Recovery y necesita ayuda para definir la secuencia de recuperación para su entorno de aplicaciones, Mapa de servicio puede automáticamente mostrarle cómo los sistemas dependen entre sí para asegurarse de que su plan de recuperación es confiable. Al elegir un servidor o grupo crítico y ver sus clientes, puede identificar los sistemas front-end que deben recuperarse después de que el servidor esté restaurado y disponible. A la inversa, al examinar las dependencias de back-end de los servidores críticos, puede identificar aquellos sistemas que deben recuperarse antes de restaurar los sistemas de foco.

### <a name="patch-management"></a>Administración de revisiones
Service Map mejora el uso de Operations Management Suite System Update Assessment al mostrar qué otros equipos y servidores dependen del servicio para poder notificarles por adelantado una interrupción de los sistemas para la aplicación de una revisión. Service Map también mejora la administración de revisiones en Operations Management Suite al mostrar si los servicios están disponibles y conectados correctamente después de haber sido revisados y reiniciados.


## <a name="mapping-overview"></a>Información general de asignación
Los agentes de Service Map recopilan información acerca de todos los procesos de conexión TCP en el servidor donde están instalados, así como los detalles de las conexiones entrantes y salientes para cada proceso. En la lista del panel izquierdo, puede seleccionar equipos o grupos con agentes de Service Map para visualizar sus dependencias durante un intervalo de tiempo especificado. Las asignaciones de dependencias de equipos se centran en un equipo concreto y muestran todos los equipos que son clientes directos de TCP o servidores de ese equipo.  Las asignaciones de grupos de equipos muestran conjuntos de servidores y sus dependencias.

![Introducción a Mapa de servicio](media/oms-service-map/service-map-overview.png)

Las máquinas se pueden expandir en la asignación para mostrar los procesos en ejecución con conexiones de red activas durante el intervalo de tiempo seleccionado. Cuando un equipo remoto con un agente de Service Map se expande para mostrar los detalles del proceso, se muestran solo aquellos procesos que se comunican con el equipo de foco. Se indica el recuento de equipos front-end sin agente que se conectan al equipo de foco en el lado izquierdo de los procesos a los que se conectan. Si el equipo de foco establece una conexión con un equipo back-end sin agente, el servidor back-end se incluye en un grupo de puertos de servidor, junto con otras conexiones al mismo número de puerto.

De forma predeterminada, las asignaciones de Service Map muestran los 30 últimos minutos de la información de dependencia. Mediante los controles de tiempo de la parte superior izquierda, se pueden consultar las asignaciones de intervalos de tiempo históricos de hasta una hora para mostrar el aspecto de las dependencias en el pasado (por ejemplo, durante un incidente o antes de un cambio). Los datos de Mapa de servicio se almacenan durante 30 días en áreas de trabajo pagadas y durante 7 días en áreas de trabajo disponibles.

## <a name="status-badges-and-border-coloring"></a>Notificaciones de estado y colores en el borde
En la parte inferior de cada servidor en el mapa puede haber una lista de notificaciones de estado que expresan información de estado acerca del servidor. Las notificaciones indican que hay cierta información relevante para el servidor de una de las integraciones de la solución Operations Management Suite. Al hacer clic en una notificación se pasa directamente a los detalles del estado en el panel derecho. Las notificaciones de estado actualmente disponibles incluyen alertas, departamento de servicios, cambios, seguridad y actualizaciones.

Según la gravedad de las notificaciones de estado, los bordes del nodo de la máquina pueden ser rojos (crítico), amarillos (advertencia) o azules (informativo). El color representa el estado más grave de cualquiera de las notificaciones de estado. Un borde gris indica que un nodo no tiene indicadores de estado.

![Notificaciones de estado](media/oms-service-map/status-badges.png)

## <a name="machine-groups"></a>Grupos de equipos
Los grupos de equipos permiten ver asignaciones centradas en torno a un conjunto de servidores, no solo uno, para que pueda ver todos los miembros de una aplicación de varios niveles o un clúster de servidores de una asignación.

Los usuarios seleccionan qué servidores pertenecen a un grupo y eligen un nombre para el grupo.  Luego pueden ver el grupo con todos sus procesos y conexiones o verlo únicamente con los procesos y las conexiones que se relacionan directamente con los demás miembros del grupo.

![Grupo de equipos](media/oms-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Creación de un grupo de equipos
Para crear un grupo, seleccione el equipo o equipos que quiera en la lista Equipos y haga clic en **Agregar al grupo**.

![Crear grupo](media/oms-service-map/machine-groups-create.png)

Ahí puede elegir **Crear nuevo** y proporcionarle un nombre al grupo.

![Dar nombre al grupo](media/oms-service-map/machine-groups-name.png)

>[!NOTE]
>Actualmente los grupos de equipos están limitados a diez servidores, aunque se planea aumentar este límite pronto.

### <a name="viewing-a-group"></a>Visualización de un grupo
Una vez que se han creado algunos grupos, puede verlos si elige la pestaña grupos.

![Pestaña Grupos](media/oms-service-map/machine-groups-tab.png)

Luego seleccione el nombre del grupo para ver la asignación de ese grupo de equipos.
![Grupo de equipos](media/oms-service-map/machine-group.png) Los equipos que pertenecen al grupo tienen un contorno blanco en la asignación.

Al expandir el grupo se muestran los equipos que componen el grupo de equipos.

![Equipos del grupo de equipos](media/oms-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrar por procesos
Puede alternar la vista de la asignación entre mostrar todos los procesos y las conexiones del grupo y solo aquellos que se relacionan directamente con el grupo de equipos.  La vista predeterminada es mostrar todos los procesos.  Puede cambiar la vista si hace clic en el icono de filtro situado encima de la asignación.

![Filtrar grupo](media/oms-service-map/machine-groups-filter.png)

Cuando **Todos los procesos** está seleccionado, la asignación incluye todos los procesos y las conexiones de cada uno de los equipos del grupo.

![Todos los procesos del grupo de equipos](media/oms-service-map/machine-groups-all.png)

Si cambia la vista para mostrar solo **procesos conectados al grupo**, la asignación se restringe solo a los procesos y las conexiones que están conectados directamente a otros equipos del grupo, con lo que se crea una vista simplificada.

![Procesos filtrados del grupo de equipos](media/oms-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Adición de equipos a un grupo
Para agregar equipos a un grupo existente, active las casillas situadas junto a los equipos que quiere y luego haga clic en **Agregar al grupo**.  Luego elija el grupo al que quiere agregar los equipos.
 
### <a name="removing-machines-from-a-group"></a>Eliminación de equipos de un grupo
En la lista de grupos, expanda el nombre del grupo para ver los equipos del grupo de equipos.  Luego haga clic en el menú de puntos suspensivos situado junto al equipo que quiere quitar y elija **Quitar**.

![Quitar equipo del grupo](media/oms-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Eliminación o cambio del nombre de un grupo
Haga clic en el menú de puntos suspensivos situado junto al nombre del grupo en la lista de grupos.

![Menú Grupo de equipos](media/oms-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Iconos de rol
Ciertos procesos cumplen roles determinados en los equipos: servidores web, servidores de aplicaciones, base de datos, etc. Service Map marca las casillas de proceso y máquina con iconos de rol, lo que facilita la identificación rápida del rol que desempeña un proceso o un servidor.

| Icono de rol | Descripción |
|:--|:--|
| ![Servidor Web](media/oms-service-map/role-web-server.png) | Servidor Web |
| ![Servidor de aplicaciones](media/oms-service-map/role-application-server.png) | Servidor de aplicaciones |
| ![Servidor de bases de datos](media/oms-service-map/role-database.png) | Servidor de bases de datos |
| ![Servidor LDAP](media/oms-service-map/role-ldap.png) | Servidor LDAP |
| ![Servidor SMB](media/oms-service-map/role-smb.png) | Servidor SMB |

![Iconos de rol](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Conexiones con errores
En Service Map se muestran las conexiones con errores de procesos y equipos con una línea roja discontinua que indica que un sistema cliente no alcanza un proceso o puerto. Se informa de las conexiones erróneas de cualquier sistema con un agente de Mapa de servicio implementado si ese sistema es el que intenta establecer la conexión con errores. Service Map mide este proceso mediante la observación de los sockets TCP que no pueden establecer una conexión. Este error puede deberse a un firewall, una configuración incorrecta en el cliente o servidor o un servicio remoto que no está disponible.

![Conexiones con errores](media/oms-service-map/failed-connections.png)

La comprensión de las conexiones con errores pueden ayudar a solucionar problemas, a la validación de la migración, el análisis de seguridad y al entendimiento general de la arquitectura. A veces las conexiones con errores son inofensivas, pero a menudo señalan directamente un problema, como un entorno de conmutación por error que de repente se convierte en inalcanzable o dos niveles de aplicación que no pueden comunicarse después de una migración a la nube.

## <a name="client-groups"></a>Grupos de clientes
Los grupos de clientes son cuadros en el mapa que representan los equipos cliente que no tienen agentes de dependencia. Un único grupo de clientes representa a los clientes de un proceso o equipo individual.

![Grupos de clientes](media/oms-service-map/client-groups.png)

Para ver las direcciones IP de los servidores de un grupo de clientes, seleccione el grupo. El contenido del grupo se muestra en el panel de **propiedades del grupo de clientes**.

![Propiedades del grupo de clientes](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de puertos de servidor
Los grupos de puertos de servidor son cuadros que representan los puertos del servidor en servidores que no tienen agentes de dependencia. El cuadro contiene el puerto del servidor y un recuento del número de servidores con conexiones a ese puerto. Expanda el cuadro para ver los servidores y las conexiones individuales. Si hay un solo servidor en el cuadro, se muestra el nombre o la dirección IP.

![Grupos de puertos de servidor](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menú contextual
Al hacer clic en los puntos suspensivos (...) de la parte superior derecha de cualquier servidor aparece el menú contextual de ese servidor.

![Conexiones con errores](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Cargar mapa del servidor
Al hacer clic en **Cargar mapa del servidor** se le remite a una nueva asignación con el servidor seleccionado como nuevo equipo de foco.

### <a name="show-self-links"></a>Mostrar autovínculos
La opción **Mostrar autovínculos** vuelve a dibujar el nodo de servidor, incluidos los autovínculos, que son conexiones TCP que empiezan y acaban en procesos del servidor. Si se muestran los autovínculos, el comando de menú cambia a **Ocultar autovínculos** para que pueda desactivarlos.

## <a name="computer-summary"></a>Resumen del equipo
El panel de **resumen del equipo** incluye una visión general del sistema operativo de un servidor, recuentos de dependencias y datos de otras soluciones de Operations Management Suite. Estos datos incluyen métricas de rendimiento, incidencias del departamento de servicios, seguimiento de cambios, seguridad y actualizaciones.

![Panel de resumen del equipo](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propiedades de proceso y de equipo
Al navegar por una asignación de Service Map, puede seleccionar equipos y procesos para obtener contexto adicional sobre sus propiedades. Los equipos proporcionan información sobre el nombre DNS, las direcciones IPv4, la capacidad de CPU y memoria, el tipo de máquina virtual, el sistema operativo y la versión, la hora del último reinicio y los identificadores de sus agentes de Operations Management Suite y Service Map.

![Panel Propiedades de la máquina](media/oms-service-map/machine-properties.png)

Puede recopilar detalles del proceso de los metadatos del sistema operativo sobre procesos en ejecución, como el nombre del proceso, la descripción del proceso, el nombre de usuario y el dominio (en Windows), el nombre de la compañía, el nombre del producto, la versión del producto, el directorio de trabajo, la línea de comandos y la hora de inicio del proceso.

![Panel Propiedades del proceso](media/oms-service-map/process-properties.png)

El panel **Resumen de proceso** proporciona información adicional sobre la conectividad del proceso, incluidos sus puertos enlazados, las conexiones entrantes y salientes y las conexiones con errores.

![Panel Resumen de proceso](media/oms-service-map/process-summary.png)

## <a name="operations-management-suite-alerts-integration"></a>Integración de Operations Management Suite Alerts
Service Map se integra con Operations Management Suite Alerts para mostrar las alertas activadas del servidor seleccionado en el intervalo de tiempo seleccionado. El servidor muestra un icono si hay alertas actuales y el panel de **alertas del equipo** enumera las alertas.

![Panel de alertas del equipo](media/oms-service-map/machine-alerts.png)

Para que Service Map muestre las alertas relevantes, cree una regla de alerta que se active para un equipo determinado. Para crear alertas apropiadas, realice estos pasos:
- Incluya una cláusula para agrupar por equipo (por ejemplo, **by Computer interval 1minute**).
- Elija que se envíen alertas según las unidades métricas.

![Configuración de alertas](media/oms-service-map/alert-configuration.png)


## <a name="operations-management-suite-log-events-integration"></a>Integración de eventos de registro de Operations Management Suite
Mapa de servicio se integra con Búsqueda de registros para mostrar un recuento de todos los eventos de registro disponibles para el servidor seleccionado durante el intervalo de tiempo elegido. Puede hacer clic en cualquier fila de la lista de recuentos de eventos para acceder a Búsqueda de registros y ver los eventos de registro individuales.

![Panel de eventos de registro del equipo](media/oms-service-map/log-events.png)

## <a name="operations-management-suite-service-desk-integration"></a>Integración de Operations Management Suite Service Desk
La integración de Service Map con IT Service Management Connector es automática si ambas soluciones están habilitadas y configuradas en el área de trabajo de Operations Management Suite. La integración en Service Map se denomina "Departamento de servicios". Para más información, vea [Administración centralizada de los elementos de trabajo ITSM con IT Service Management Connector (versión preliminar)](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

El panel de **departamento de servicios del equipo** muestra todos los eventos de IT Service Management del servidor seleccionado en el intervalo de tiempo seleccionado. El servidor muestra un icono si hay elementos actuales y el panel de departamento de servicios del equipo los enumera.

![Panel de departamento de servicios del equipo](media/oms-service-map/service-desk.png)

Para abrir el elemento en la solución ITSM conectada, haga clic en **Ver elemento de trabajo**.

Para ver los detalles del elemento en la búsqueda de registros, haga clic en **Mostrar en la búsqueda de registro**.


## <a name="operations-management-suite-change-tracking-integration"></a>Integración de Operations Management Suite Change Tracking
La integración de Service Map con Change Tracking es automática si ambas soluciones están habilitadas y configuradas en el área de trabajo de Operations Management Suite.

El panel de **seguimiento de cambios del equipo** muestra todos los cambios, con el más reciente en primer lugar, además de un vínculo para profundizar en la búsqueda de registros a fin de obtener más detalles.

![Panel de seguimiento de cambios del equipo](media/oms-service-map/change-tracking.png)

La imagen siguiente es una vista detallada de un evento ConfigurationChange que se puede ver después de seleccionar **Mostrar en Log Analytics**.

![Evento ConfigurationChange](media/oms-service-map/configuration-change-event.png)


## <a name="operations-management-suite-performance-integration"></a>Integración de rendimiento de Operations Management Suite
En el panel **Rendimiento de la máquina** se muestran las métricas de rendimiento estándar del servidor seleccionado. Las métricas incluyen uso de la CPU, uso de la memoria, bytes de red enviados y recibidos y una lista de los principales procesos por bytes de red enviados y recibidos. Para obtener los datos de rendimiento de red, también debe haber habilitado la solución Wire Data 2.0 en Operations Management Suite.

![Panel Rendimiento de la máquina](media/oms-service-map/machine-performance.png)


## <a name="operations-management-suite-security-integration"></a>Integración de Operations Management Suite Security
La integración de Service Map con Security and Audit es automática si ambas soluciones están habilitadas y configuradas en el área de trabajo de Operations Management Suite.

En el panel de **seguridad del equipo** se muestran datos de la solución Operations Management Suite Security and Audit del servidor seleccionado. El panel muestra un resumen de los problemas de seguridad pendientes del servidor durante el intervalo de tiempo seleccionado. Al hacer clic en cualquiera de los problemas de seguridad, se profundiza en una búsqueda de registros para obtener detalles sobre ellos.

![Panel de seguridad del equipo](media/oms-service-map/machine-security.png)


## <a name="operations-management-suite-updates-integration"></a>Integración de Operations Management Suite Updates
La integración de Service Map con Update Management es automática si ambas soluciones están habilitadas y configuradas en el área de trabajo de Operations Management Suite.

El panel de **actualizaciones del equipo** muestra datos de la solución Operations Management Suite Update Management del servidor seleccionado. El panel muestra un resumen de las actualizaciones que faltan en el servidor durante el intervalo de tiempo seleccionado.

![Panel de seguimiento de cambios del equipo](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registros de Log Analytics
Los datos de inventario de equipos y procesos de Service Map están disponibles para [buscar](../log-analytics/log-analytics-log-searches.md) en Log Analytics. Estos datos se pueden aplicar a escenarios que incluyen la planeación de la migración, el análisis de la capacidad, la detección y la solución de problemas de rendimiento a petición.

Se genera un registro por hora para cada equipo y proceso únicos, además de los registros generados cuando un proceso o equipo se inicia o se integra en Service Map. Estos registros tienen las propiedades de las tablas siguientes. Los campos y valores de los eventos ServiceMapComputer_CL se asignan a los campos del recurso Equipo en la API ServiceMap de Azure Resource Manager. Los campos y valores de los eventos ServiceMapProcess_CL se asignan a los campos del recurso Proceso en la API ServiceMap de Azure Resource Manager. El campo ResourceName_s coincide con el campo de nombre del recurso correspondiente de Resource Manager. 

>[!NOTE]
>A medida que aumentan las características de Service Map, estos campos están sujetos a cambios.

Hay propiedades generadas internamente que puede usar para identificar los equipos y procesos únicos:

- Equipo: use ResourceId o ResourceName_s para identificar de forma exclusiva un equipo en un área de trabajo de Operations Management Suite.
- Proceso: use ResourceId para identificar de forma exclusiva un proceso en un área de trabajo de Operations Management Suite. ResourceName_s es único dentro del contexto de la máquina en la que se está ejecutando el proceso (MachineResourceName_s) 

Puesto que pueden existir varios registros para un proceso y equipo especificados en un intervalo de tiempo concreto, las consultas pueden devolver más de un registro para el mismo proceso o equipo. Para incluir solo el registro más reciente agregue "| dedup ResourceId" a la consulta.

### <a name="servicemapcomputercl-records"></a>Registros de ServiceMapComputer_CL
Los registros con un tipo de *ServiceMapComputer_CL* tienen datos de inventario de servidores con agentes de Mapa de servicio. Estos registros tienen las propiedades de la tabla siguiente:

| Propiedad | Descripción |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Identificador único de una máquina en el área de trabajo |
| ResourceName_s | Identificador único de una máquina en el área de trabajo |
| ComputerName_s | FQDN del equipo |
| Ipv4Addresses_s | Lista de las direcciones IPv4 del servidor |
| Ipv6Addresses_s | Lista de las direcciones IPv6 del servidor |
| DnsNames_s | Matriz de nombres DNS |
| OperatingSystemFamily_s | Windows o Linux |
| OperatingSystemFullName_s | Nombre completo del sistema operativo  |
| Bitness_s | Valor de bits del equipo (32 o 64 bits)  |
| PhysicalMemory_d | Memoria física en MB |
| Cpus_d | Número de CPU |
| CpuSpeed_d | Velocidad de la CPU en MHz|
| VirtualizationState_s | *unknown*, *physical*, *virtual*, *hypervisor* |
| VirtualMachineType_s | *hyperv*, *vmware*, etc. |
| VirtualMachineNativeMachineId_g | Identificador de máquina virtual asignado por su hipervisor |
| VirtualMachineName_s | Nombre de la máquina virtual |
| BootTime_t | Tiempo de arranque |



### <a name="servicemapprocesscl-type-records"></a>Registros con un tipo ServiceMapProcess_CL
Los registros con un tipo de *ServiceMapProcess_CL* tienen datos de inventario para procesos con conexión TCP en servidores con agentes de Mapa de servicio. Estos registros tienen las propiedades de la tabla siguiente:

| Propiedad | Descripción |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Identificador único de un proceso en el área de trabajo |
| ResourceName_s | Identificador único de un proceso en el equipo en el que se está ejecutando|
| MachineResourceName_s | Nombre de recurso del equipo |
| ExecutableName_s | Nombre del archivo ejecutable del proceso |
| StartTime_t | Hora de inicio del grupo de procesos |
| FirstPid_d | Primer PID del grupo de procesos |
| Description_s | Descripción del proceso |
| CompanyName_s | Nombre de la compañía |
| InternalName_s | Nombre interno |
| ProductName_s | Nombre del producto |
| ProductVersion_s | Versión del producto |
| FileVersion_s | Versión del archivo |
| CommandLine_s | Línea de comandos |
| ExecutablePath _s | Ruta de acceso al archivo ejecutable |
| WorkingDirectory_s | Directorio de trabajo |
| UserName | Cuenta en la que se está ejecutando el proceso |
| UserDomain | Dominio en el que se está ejecutando el proceso |


## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo

### <a name="list-all-known-machines"></a>Enumerar todas las máquinas conocidas
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Enumerar la capacidad de memoria física de todos los equipos administrados.
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>Enumerar el nombre de equipo, DNS, IP y SO.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Buscar todos los procesos con "sql" en la línea de comandos
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Buscar una máquina (registro más reciente) por el nombre de recurso
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Buscar un equipo (registro más reciente) por dirección IP
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Enumerar todos los procesos conocidos en un equipo determinado
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>Enumerar todos los equipos que ejecutan SQL
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Enumerar todas las versiones de producto únicas de curl en mi centro de datos
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Crear un grupo de equipos de todos los equipos con CentOS
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>API de REST
Todos los datos de servidores, procesos y dependencias de Service Map están disponibles a través de la [API de REST de Service Map](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y uso
Microsoft recopila automáticamente datos de uso y rendimiento a través del servicio Mapa de servicio. Microsoft usa estos datos para proporcionar calidad, seguridad e integridad en el servicio Service Map y para mejorarlas. Con el fin de proporcionar funcionalidades de solución de problemas precisas y eficientes, los datos incluyen información sobre la configuración del software, como sistema operativo y versión, dirección IP, nombre DNS y nombre de la estación de trabajo. Microsoft no recopila nombres, direcciones ni otra información de contacto.

Para más información sobre el uso y la recopilación de datos, vea la [Declaración de privacidad de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Pasos siguientes
Más información sobre las [búsquedas de registros](../log-analytics/log-analytics-log-searches.md) de Log Analytics para recuperar datos recopilados por Service Map.


## <a name="troubleshooting"></a>Solución de problemas
Consulte la [sección de solución de problemas del documento de configuración de Service Map](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Comentarios
¿Quiere hacernos llegar algún comentario acerca de Mapa de servicio o esta documentación?  Visite la [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map) para sugerir características o votar sugerencias existentes.
