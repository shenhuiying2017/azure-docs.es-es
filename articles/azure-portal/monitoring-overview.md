---
title: Información general sobre la supervisión en Microsoft Azure | Microsoft Docs
description: Información general de la supervisión y los diagnósticos de Microsoft Azure, incluidas las alertas, los webhooks, el escalado automático, etc.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: robb

---
# Información general sobre la supervisión en Microsoft Azure
Este artículo proporciona una introducción a los conceptos de supervisión de recursos y punteros en Azure para más información sobre determinados tipos de recursos. Para obtener información detallada sobre la supervisión de las aplicaciones desde el punto de vista no de Azure, consulte [Guía de supervisión y diagnóstico](../best-practices-monitoring.md).

Las aplicaciones de nube son complejas y tienen muchas partes móviles. La supervisión proporciona datos para garantizar que la aplicación permanece en funcionamiento en un estado correcto. También ayuda a evitar posibles problemas o a solucionar los existentes. Además, puede usar datos de supervisión para obtener un conocimiento más profundo sobre su aplicación. Esta información puede ayudarle a mejorar su rendimiento o mantenimiento, o a automatizar algunas acciones que de lo contrario requerirían intervención manual.

En el siguiente diagrama se muestra una vista conceptual de la supervisión de Azure, incluido el tipo de registros que puede recopilar y lo que puede hacer con esos datos.

![Modelo lógico para la supervisión y el diagnóstico de recursos que no son de proceso](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

Figura 1: Modelo conceptual para la supervisión y el diagnóstico de recursos que no son de proceso

<br/>

![Modelo lógico para la supervisión y el diagnóstico de recursos de proceso](./media/monitoring-overview/monitoring-azure-resources-compute.png)

Figura 2: Modelo conceptual para la supervisión y el diagnóstico de recursos de proceso

## Orígenes de supervisión
### Registros de actividad de auditoría
Puede buscar registros de auditoría (también llamados registros operativos o de actividad) para obtener información sobre los recursos tal y como se ven en la infraestructura de Azure. El registro contiene información tal como el número de veces que se crean o se destruye recursos.

### Máquina virtual host
**Solo proceso**

Actualmente no se pueden recopilar métricas sobre la máquina virtual host, pero se muestran aquí para ofrecer información completa.

Algunos recursos de proceso, tales como Cloud Services, Virtual Machines y Service Fabric, tienen una máquina virtual host dedicada con la que interactúan. La máquina virtual host es el equivalente a la máquina virtual de raíz del modelo de hipervisor de Hyper-V.

Para otros servicios de Azure, no hay necesariamente una asignación 1:1 entre los recursos y una máquina virtual host específica.

### Recurso: métricas y registros de diagnóstico
Las métricas que se pueden recopilar varían según el tipo de recurso. Por ejemplo, Virtual Machines proporciona estadísticas sobre la E/S de disco y % de CPU. Pero esas estadísticas no existen para una cola de Service Bus, sino métricas como el tamaño de cola y el rendimiento de mensajes.

En el caso de los recursos de proceso, puede obtener métricas sobre el SO invitado y los módulos de diagnóstico como Diagnósticos de Diagnostics. Diagnósticos de Azure ayuda a recopilar y enrutar los datos de diagnóstico recopilados a otras ubicaciones, como el almacenamiento de Azure.

### Aplicación: registros de diagnóstico, registros de aplicación y métricas
**Solo proceso**

Las aplicaciones se pueden ejecutar sobre el SO invitado en el modelo de proceso. Emiten su propio conjunto de registros y métricas.

Los tipos de métricas son:

* Contadores de rendimiento
* Registros de aplicación
* Registros de eventos de Windows
* Origen de eventos de .NET
* Registros IIS
* ETW basado en manifiesto
* Volcados de memoria
* Registros de errores personalizados

## Usos de los datos de supervisión
### Visualizar
Ver los datos de supervisión en gráficos y diagramas ayuda a encontrar tendencias mucho más rápidamente que si se busca en los propios datos.

Algunos métodos de visualización incluyen:

* Uso del Portal de Azure
* Enrutamiento de datos a Azure Application Insights
* Enrutamiento de datos a Microsoft PowerBI
* Enrutamiento de datos a una herramienta de visualización de terceros mediante streaming en vivo o haciendo que la herramienta lea un archivo de almacenamiento de Azure

### Archivar
Normalmente, los datos de supervisión se escriben en Almacenamiento de Azure y se conservan ahí hasta que se eliminan.

Algunas formas de usar estos datos:

* Una vez escritos, puede hacer que otras herramientas, dentro o fuera de Azure, los lean y los procesen.
* Los datos se descargan localmente para un archivo local o se cambia su directiva de retención en la nube para conservar los datos durante largos períodos de tiempo.
* Los datos se dejan en Almacenamiento de Azure indefinidamente. Tenga en cuenta que tendrá que pagar por almacenamiento de Azure en función de la cantidad de datos que tenga.

### Consultar
Puede usar la API de REST de Insights, comandos de la interfaz de Common Language Runtime (CLI), cmdlets de PowerShell o el SDK de .NET para acceder a los datos en el sistema o en almacenamiento de Azure.

Algunos ejemplos son:

* Obtener datos para una aplicación de supervisión personalizada que ha escrito.
* Crear consultas personalizadas y enviar los datos a una aplicación de terceros.

### Enrutar
Puede transmitir los datos de supervisión a otras ubicaciones en tiempo real.

Algunos ejemplos son:

* Enviarlos a Application Insights para poder usar sus herramientas de visualización.
* Enviarlos a los Centros de eventos para poder enrutarlos a herramientas de terceros para realizar el análisis en tiempo real.

### Automatizar
Puede usar los datos de supervisión para desencadenar eventos o incluso procesos completos. Algunos ejemplos son:

* Usar los datos para aumentar o reducir automáticamente el número de instancias de proceso según la carga de la aplicación.
* Enviar mensajes de correo electrónico cuando una métrica cruza un umbral predeterminado.
* Llamar a una dirección URL web (webhook) para ejecutar una acción en un sistema externo a Azure.
* Iniciar un runbook en Automatización de Azure para realizar diversas tareas.

## Métodos de uso
En general, puede manipular el seguimiento, el enrutamiento y la recuperación de los datos mediante uno de los métodos siguientes. No todos los métodos están disponibles para todas las acciones o tipos de datos.

* [Portal de Azure](https://portal.azure.com)
* [PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Interfaz de línea de comandos (CLI) multiplataforma ](../monitoring-and-diagnostics/insights-cli-samples.md)
* [API DE REST](https://msdn.microsoft.com/library/dn931943.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## Ofertas de supervisión de Azure
Azure tiene diversas ofertas disponibles para supervisar sus servicios, desde la infraestructura sin sistema operativo a la telemetría de las aplicaciones. La mejor estrategia de supervisión combina el uso de las tres para obtener información detallada y completa sobre el mantenimiento de los servicios.

* [Supervisión de Azure Insights (plataforma)](http://aka.ms/azmondocs): ofrece funciones de visualización, consulta, enrutamiento, alertas, escalado automático y automatización de los datos tanto de la infraestructura de Azure (registros de actividad) como de cada recurso individual de Azure (registros de diagnóstico). Este artículo forma parte de la documentación de Azure Insights.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/): ofrece detección y diagnóstico de problemas en la capa de aplicación del servicio, y se encuentra perfectamente integrado encima de los datos de supervisión de Azure.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) parte de [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), proporciona una solución integral de administración de TI tanto para infraestructura local como basada en la nube, e incluye búsqueda de registros y análisis de recursos de Azure.

## Pasos siguientes
Más información acerca de

* [Azure Insights](http://aka.ms/azmondocs): explore los vínculos de la tabla de contenido situada a la izquierda y los vídeos de este vínculo.
* [Diagnósticos de Azure](../azure-diagnostics.md): si está intentando diagnosticar problemas en su aplicación de Cloud Services, Virtual Machines o Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/): si está intentando diagnosticar problemas en su aplicación web de Servicio de aplicaciones.
* [Solución de problemas de Azure Storage](../storage/storage-e2e-troubleshooting.md): si usa Blob Storage, Table Storage o Queue Storage.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) y [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0907_2016-->