---
title: Administración de servicios de Azure Search en Azure Portal
description: Administrar Azure Search, un servicio hospedado de búsqueda en la nube de Microsoft Azure, mediante Azure Portal.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 896a12db1ac196b6de1e57dde9b5910e11dcc8c7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797056"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administración de servicios de Azure Search en Azure Portal
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search es un servicio basado en la nube totalmente administrado que se utiliza para la creación de una experiencia de búsqueda enriquecida en aplicaciones personalizadas. En este artículo se tratan las tareas de *administración de servicios* que se pueden realizar en [Azure Portal](https://portal.azure.com) para un servicio de búsqueda que ya se haya aprovisionado. *Service administration* es ligera, ya que se limita a las siguientes tareas:

* Administrar y proteger el acceso a las *claves de API* que se usan para el acceso de lectura o escritura a su servicio.
* Ajustar la capacidad de servicio, mediante el cambio de la asignación de particiones y réplicas.
* Supervisar el uso de recursos, en relación con los límites máximos de su nivel de servicio.

Tenga en cuenta que *actualizar* no aparece como una tarea administrativa. Dado que los recursos se asignan cuando se aprovisiona el servicio, mover a otro nivel requiere un nuevo servicio. Consulte [Creación de un servicio Azure Search](search-create-service-portal.md) para más información.

> [!Tip]
> ¿Busca ayuda acerca de cómo analizar el rendimiento de consultas o el tráfico de búsqueda? Obtenga información acerca del volumen de las consultas, qué términos buscan los usuarios y la eficacia de los resultados de búsqueda a la hora de guiar a los clientes a determinados documentos del índice. Para obtener instrucciones, consulte [Análisis de tráfico de búsqueda](search-traffic-analytics.md), [Supervisión de un servicio de Azure Search](search-monitor-usage.md) y [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Derechos de administrador
El aprovisionamiento o la retirada del propio servicio los puede realizar un administrador o coadministrador de una suscripción de Azure.

Dentro de un servicio, cualquier persona con acceso a la dirección URL del servicio y una clave de API de administración tiene acceso de lectura y escritura al servicio. El acceso de lectura y escritura ofrece capacidad para agregar, eliminar o modificar objetos de servidor, incluidos claves de API, índices, indizadores, orígenes de datos, programaciones y asignaciones de roles, tal y como se implementa a través de [roles definidos por RBAC](search-security-rbac.md).

Toda la interacción del usuario con Azure Search se encuadra en de uno de estos modos: acceso de lectura y escritura al servicio (derechos de administrador) o acceso de solo lectura al servicio (derechos de consulta). Para más información, consulte [Administración de las claves de API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Registro e información del sistema
Azure Search no expone los archivos de registro de un servicio individual ni a través del portal ni de interfaces de programación. En el nivel básico, y en los superiores, Microsoft supervisa que todos los servicios de Azure Search tengan una disponibilidad del 99,9 % por acuerdo de nivel de servicio (SLA). Si el servicio es lento o el rendimiento de las solicitudes no llega a los umbrales del SLA, los equipos de soporte técnico revisan los archivos de registro a los que pueden acceder y solucionan el problema.

En términos de información general acerca del servicio, hay varias formas de obtener información:

* En el portal, en el panel del servicio, a través de notificaciones, propiedades y mensajes de estado.
* Usando [PowerShell](search-manage-powershell.md) o la [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/) para [obtener propiedades del servicio](https://docs.microsoft.com/rest/api/searchmanagement/services) o el estado del uso de recursos del índice.
* A través del [análisis de tráfico de búsqueda](search-traffic-analytics.md), como se ha indicado anteriormente.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Supervisar el uso de recursos
En el panel, la supervisión de recursos se limita a la información que se muestra en el panel de servicios y algunas métricas que puede obtener al consultar el servicio. En el panel de servicios, en la sección Uso, podrá determinar rápidamente si los niveles de recursos de partición son adecuados para su aplicación.

Al usar la API de REST del servicio de búsqueda, podrá obtener una recuento de los documentos e índices mediante programación: 

* [Obtención de estadísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Documentos de recuento](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Interrupciones de servicio y recuperación ante desastres

Aunque podemos recuperar los datos, Azure Search no proporciona conmutación por error instantánea del servicio si se produce una interrupción en centro de datos o el clúster. Si se produce un error en un clúster del centro de datos, el equipo de operaciones lo detectará y trabajará para restaurar el servicio. Experimentará un tiempo de inactividad durante la restauración del servicio. Puede solicitar créditos del servicio para compensar la falta de disponibilidad de servicio según el [Acuerdo de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Si se requiera continuidad del servicio, en caso de errores catastróficos fuera de control de Microsoft, debe [aprovisionar un servicio adicional](search-create-service-portal.md) en una región diferente e implementar una estrategia de replicación geográfica para asegurarse de que los índices sean totalmente redundantes en todos los servicios.

Los clientes que usen [indizadores](search-indexer-overview.md) para rellenar y actualizar índices pueden controlar la recuperación ante desastres mediante indizadores geográficos que aprovechan el mismo origen de datos. Dos servicios en regiones diferentes y que cada uno ejecute un indizador pueden indexar desde el mismo origen de datos para lograr redundancia geográfica. Si indexa desde orígenes de datos que también tienen redundancia geográfica, tenga en cuenta que los indizadores de Azure Search solo pueden realizar una indización incremental a partir de las réplicas principales. En un evento de conmutación por error, asegúrese de que el indizador apunte de nuevo a la nueva réplica principal. 

Si no utiliza indizadores, usaría el código de aplicación para enviar objetos y datos a distintos servicios de búsqueda en paralelo. Para más información, consulte [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Dado que Azure Search no es una solución de almacenamiento de datos principal, no se proporcionan un mecanismo formal para realizar procesos de copias de seguridad y restauración automáticos. El código de aplicación que se usa para crear y rellenar un índice es la opción de restauración de facto si elimina por error un índice. 

Para volver a generar un índice, elimínelo (si existe), vuelva a crear el índice en el servicio y cárguelo de nuevo recuperando los datos del almacén de datos principal. Como alternativa, puede ponerse en contacto con el equipo de [asistencia técnica al cliente]() para proteger los índices si se produce una interrupción regional del sistema.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Escalado o reducción vertical
Cada uno de los servicios de búsqueda se inicia con una cantidad mínima de una réplica y una partición. Si ha iniciado sesión en un [nivel que proporciona recursos dedicados](search-limits-quotas-capacity.md), haga clic en el icono **ESCALA** del panel de servicios para ajustar el uso de recursos.

Al agregar una capacidad mediante cualquiera de los recursos, el servicio los usa automáticamente. No es necesario que haga nada más, pero hay un ligero retraso antes de materializarse el impacto del nuevo recurso. El aprovisionamiento de los recursos adicionales puede tardar 15 minutos, o más.

 ![][10]

### <a name="add-replicas"></a>Adición de réplicas
La adición de réplicas permiten un aumento de las consultas por segundo (QPS) o la consecución de una alta disponibilidad. Cada réplica tiene una copia de un índice, por lo que la adición de una réplica más se traduce en que hay un índice más disponible para controlar solicitudes de consulta del servicio. Para lograr una alta disponibilidad, se requieren un mínimo de tres réplicas (para más información, consulte [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Azure Search](search-capacity-planning.md) ).

Un servicio de búsqueda con más réplicas puede equilibrar la carga de solicitudes de consulta sobre un número de índices mayor. Una vez especificado un nivel del volumen de consultas, el rendimiento de las consultas va a ser más rápido cuando haya más copias del índice disponible para ofrecer la solicitud. En caso de experimentar una latencia de consulta, puede esperar que se produzca un impacto positivo en el rendimiento una vez que las réplicas adicionales estén en línea.

Aunque el rendimiento de las consultas aumente a medida que agrega réplicas, esto no lo dobla ni triplica precisamente a medida que agrega réplicas a su servicio. Todas las aplicaciones de búsqueda están sujetas a factores externos que pueden afectar al rendimiento de las consultas. Las consultas complejas y la latencia de red son dos factores que contribuyen a la existencia de variaciones en los tiempos de respuesta de las consultas.

### <a name="add-partitions"></a>Adición de particiones
La mayoría de las aplicaciones de servicio tienen una necesidad integrada de más réplicas, en lugar de particiones. En los casos donde se requiere un mayor número de documentos, puede agregar particiones si se registró en un servicio Estándar. El nivel Básico no proporciona particiones adicionales.

En el nivel Estándar, las particiones se agregan en múltiplos de 12 (concretamente 1, 2, 3, 4, 6 o 12). Se trata de un artefacto de particionamiento. Un índice se crea en 12 particiones de base de datos, que pueden almacenarse en su totalidad en una partición o dividirse equitativamente en 2, 3, 4, 6 o 12 particiones (una partición de base de datos por partición).

### <a name="remove-replicas"></a>Eliminación de réplicas
Tras períodos de elevados volúmenes de consultas, puede reducir las réplicas después de que se hayan normalizado las cargas de consultas de búsqueda (por ejemplo, tras finalizar las ventas navideñas).

Para ello, vuelva a mover el control deslizante de la réplica a un número inferior. No es necesario que haga nada más. Al reducir el recuento de réplicas, las máquinas virtuales se abandonan en el centro de datos. Ahora, sus operaciones de ingesta de consultas y datos se ejecutarán en menos VM que antes. El límite mínimo es una réplica.

### <a name="remove-partitions"></a>Eliminación de particiones
A diferencia de la eliminación de réplicas, que no requiere que haga nada más, es posible que tenga que hacer algo si usa más almacenamiento del que se puede reducir. Por ejemplo, si su solución usa tres particiones, la reducción de su tamaño a una o dos particiones generará un error si el nuevo espacio de almacenamiento es inferior al requerido. Como cabría esperar, las opciones con las que cuenta son eliminar índices o documentos en un índice asociado para liberar espacio o mantener la configuración actual.

No existe un método de detección que indique qué particiones de índice se almacenan en particiones concretas. Cada partición proporciona un espacio de almacenamiento de aproximadamente 25 MB, de modo que será necesario reducirlo a un tamaño al que pueda ajustarse su número de particiones. Si quiere volver a una partición, las 12 particiones deberán ajustarse.

Para ayudar en una futura planificación, es posible que quiera comprobar el espacio de almacenamiento (usando [Obtención de estadísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver cuánto usó en realidad. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Prácticas recomendadas de escalado e implementación
En este vídeo de 30 minutos se analizan las prácticas recomendadas para escenarios de implementación avanzada, entre los que se incluyen las cargas de trabajo distribuidas geográficamente. También puede consultar [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md) , donde encontrará páginas de ayuda que tratan los mismos puntos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Pasos siguientes
Una vez que comprenda los conceptos de administración del servicio, considere la posibilidad de utilizar [PowerShell](search-manage-powershell.md) para automatizar las tareas.

También se recomienda revisar el [artículo sobre rendimiento y optimización](search-performance-optimization.md).

Otra recomendación es ver el vídeo que se indica en la sección anterior. Proporciona cobertura más detallada de las técnicas mencionadas en esta sección.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



