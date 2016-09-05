<properties
	pageTitle="Información general sobre la supervisión en Microsoft Azure | Microsoft Azure"
	description="Información general de la supervisión y los diagnósticos de Microsoft Azure, incluidas las alertas, los webhooks, el escalado automático, etc."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/03/2016"
	ms.author="robb"/>

# Información general sobre la supervisión en Microsoft Azure

Este artículo proporciona una introducción a los conceptos de supervisión de recursos y punteros en Azure para más información sobre determinados tipos de recursos. Para obtener información detallada sobre la supervisión de las aplicaciones desde el punto de vista no de Azure, consulte [Guía de supervisión y diagnóstico](../best-practices-monitoring.md).

Las aplicaciones de nube son complejas y tienen muchas partes móviles. La supervisión permite asegurarse de que la aplicación se mantiene activa y ejecutándose en un estado correcto, al tiempo que recopila métricas vitales para evitar posibles problemas o solucionar problemas pasados. También puede usar los datos de supervisión para automatizar algunas acciones que requieren una intervención manual y para obtener información detallada sobre su aplicación que le permita mejorar su rendimiento o mantenimiento.

El diagrama siguiente muestra una vista lógica de supervisión de Azure. La implementación subyacente es más compleja. La ilustración muestra el tipo de registros que puede recopilar y lo que puede hacer con esos datos.

![Modelo lógico para la supervisión y el diagnóstico de recursos que no son de proceso](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

Figura 1: Modelo lógico para la supervisión y el diagnóstico de recursos que no son de proceso

<br/>

![Modelo lógico para la supervisión y el diagnóstico de recursos de proceso](./media/monitoring-overview/monitoring-azure-resources-compute.png)

Figura 2: Modelo lógico para la supervisión y el diagnóstico de recursos de proceso


## Orígenes de supervisión
### Registros de auditoría de infraestructura (actividad)
Puede obtener los registros de auditoría (también llamados registros operativos o de actividad) del recurso tal y como se muestra en la infraestructura de Azure. Estos registros contienen información tal como el número de veces que se crean o se destruye recursos.

### Máquina virtual host
**Solo proceso**

Actualmente no se pueden recopilar métricas sobre la máquina virtual host, pero se muestran aquí para ofrecer información completa.

Algunos recursos de proceso, tales como Servicios en la nube, Máquinas virtuales y Service Fabric, tienen una máquina virtual host dedicada con la que interactúan. La máquina virtual host es el equivalente a la máquina virtual de raíz del modelo de hipervisor de Hyper-V.

Para otros servicios de Azure, no hay necesariamente una asignación 1:1 entre los recursos y una máquina virtual host específica.


### Recurso: métricas y registros de diagnóstico
Las métricas que se pueden recopilar varían según el tipo de recurso. Por ejemplo, Máquinas virtuales proporciona estadísticas acerca de la E/S de disco y el porcentaje de CPU, pero estas no tienen sentido para una cola de Bus de servicio, que proporciona métricas tales como el rendimiento de los mensajes y el tamaño de la cola.

En el caso de los recursos de proceso, también puede obtener métricas del SO invitado y de los módulos de diagnóstico tales como Diagnósticos de Azure. Diagnósticos de Azure ayuda a recopilar y enrutar los datos de diagnóstico a otras ubicaciones, incluido Almacenamiento de Azure.

### Aplicación: registros de diagnóstico, registros de aplicación y métricas
**Solo proceso**

Las aplicaciones se pueden ejecutar sobre el SO invitado en el modelo de proceso. Emiten su propio conjunto de registros y métricas.

Los tipos de métricas son:

- Contadores de rendimiento
- Registros de aplicación
- Registros de eventos de Windows
- Origen de eventos de .NET
- Registros IIS
- ETW basado en manifiesto
- Volcados de memoria
- Registros de errores personalizados


## Usos de los datos de supervisión

### Visualizar
Ver los datos de supervisión en gráficos y diagramas ayuda a encontrar tendencias mucho más rápidamente que si se busca en los propios datos. Algunas maneras de hacer esto son:

- Uso del portal de Azure
- Enrutar los datos a Azure Application Insights
- Enrutar los datos a Power BI
- Enrutar los datos a una herramienta de visualización de terceros mediante streaming en vivo o haciendo que la herramienta lea un archivo de Almacenamiento de Azure

### Archivar
Normalmente, los datos de supervisión se escriben en Almacenamiento de Azure y se conservan ahí hasta que se eliminan.

Algunas formas de usar estos datos:

- Una vez escritos, puede hacer que otras herramientas, dentro o fuera de Azure, los lean y los procesen.
- Los datos se descargan localmente para un archivo local o se cambia su directiva de retención en la nube para conservar los datos durante largos períodos de tiempo.
- Los datos se dejan en Almacenamiento de Azure indefinidamente. (Tenga en cuenta que tendrá que pagar por Almacenamiento de Azure según la cantidad de datos que tenga).

### Consultar
Puede usar la API de REST de Insights, los comandos de la interfaz de Common Language Runtime (CLI), los cmdlets de PowerShell o el SDK de .NET para acceder a los datos en el sistema o en Almacenamiento de Azure. Algunos ejemplos son:

-  Obtener datos para una aplicación de supervisión personalizada que ha escrito.
-  Crear consultas personalizadas y enviar los datos a una aplicación de terceros.

### Enrutar
Puede transmitir los datos de supervisión a otras ubicaciones en tiempo real. Algunos ejemplos son:

- Enviarlos a Application Insights para poder usar sus herramientas de visualización.
- Enviarlos a los Centros de eventos para poder enrutarlos a herramientas de terceros para realizar el análisis en tiempo real.

### Automatizar
Puede usar los datos de supervisión para desencadenar eventos o incluso procesos completos. Algunos ejemplos son:

- Usar los datos para aumentar o reducir automáticamente el número de instancias de proceso según la carga de la aplicación.
- Enviar mensajes de correo electrónico cuando una métrica cruza un umbral predeterminado.
- Llamar a una dirección URL web (webhook) para ejecutar una acción en un sistema externo a Azure.
- Iniciar un runbook en Automatización de Azure para realizar diversas tareas.



## Métodos de uso
Por lo general, se puede manipular lo que se sigue, adónde va y la recuperación de los datos con uno de los métodos siguientes. No todos los métodos están disponibles para todas las acciones.

- [Portal de Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)
- [Interfaz de biblioteca común (CLI)](insights-cli-samples.md)
- [API DE REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## Ofertas de supervisión de Azure
Azure tiene diversas ofertas disponibles para supervisar sus servicios, desde la infraestructura sin sistema operativo a la telemetría de las aplicaciones. La mejor estrategia de supervisión combina el uso de las tres para obtener información detallada y completa sobre el mantenimiento de los servicios.

- [Supervisión de Azure Insights (plataforma)](http://aka.ms/azmondocs): ofrece funciones de visualización, consulta, enrutamiento, alertas, escalado automático y automatización de los datos tanto de la infraestructura de Azure (registros de auditoría) como de cada recurso individual de Azure (registros de diagnóstico). Este artículo forma parte de la documentación de Azure Insights.
- **[Application Insights](https://azure.microsoft.com/documentation/services/application-insights/)**: ofrece detección y diagnóstico de problemas en el nivel de aplicación del servicio, bien integrado sobre los datos de Supervisión de Azure.
- **[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)**, parte de **[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)**: proporciona una solución integral de administración de TI tanto para infraestructura local como basada en la nube, e incluye búsqueda de registros y análisis de los recursos de Azure.


## Pasos siguientes
Más información acerca de

- [Azure Insights](http://aka.ms/azmondocs): explore los vínculos de la tabla de contenido situada a la izquierda y los vídeos de este vínculo.
- [Diagnósticos de Azure](../azure-diagnostics.md): si está intentando diagnosticar problemas en su aplicación de Servicio en la nube, Máquina Virtual o Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/): si está intentando diagnosticar problemas en su aplicación web de Servicio de aplicaciones.
- [Solución de problemas de Almacenamiento de Azure](../storage/storage-e2e-troubleshooting.md): si usa almacenamiento de blobs, tablas o colas.
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) y [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0824_2016-->