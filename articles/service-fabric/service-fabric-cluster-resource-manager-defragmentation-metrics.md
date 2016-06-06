<properties
   pageTitle="Desfragmentación de métricas en Azure Service Fabric | Microsoft Azure"
   description="Información general del uso de la desfragmentación o el empaquetado como estrategia para métricas en Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Desfragmentación de métricas y carga en Service Fabric
Cluster Resource Manager de Service Fabric se ocupa principalmente del equilibrio en cuanto a distribuir la carga, de forma que se tiene la seguridad de que todos los nodos del clúster se utilizan por igual. Este es habitualmente el diseño más seguro e inteligente en términos de superación de errores, ya que asegura que cualquier error no se lleve gran parte de una carga de trabajo. Cluster Resource Manager de Service Fabric admite también una estrategia diferente: la desfragmentación. Este término generalmente significa que, en lugar de intentar distribuir el uso de una métrica en el clúster, lo que deberíamos intentar realmente es consolidarla. Esto tampoco resultaba excesivamente difícil de implementar; en lugar de aumentar la valoración de una solución (minimizando la desviación estándar media de la carga métrica de una métrica determinada), la disminuimos. Dicho de otra forma: empezamos a optimizar los incrementos en la desviación, en vez de intentar minimizarlos. Pero, ¿para qué sirve esta estrategia?

Si ha repartido la carga equitativamente entre los nodos del clúster, entonces ha consumido algunos de los recursos que ofrecen los nodos. Esto no acostumbra a ser un problema, pero a veces algunas cargas de trabajo crean servicios que son excepcionalmente grandes y consumen la inmensa mayoría de un nodo (digamos que entre un 75 y un 95 % de los recursos de un nodo pueden terminar dedicándose a un solo servicio). Esto no supone un problema; Resource Manager detectará durante la creación que necesita reorganizar el clúster para dejarle margen a esta gran carga de trabajo y configurarlo para ello, pero hasta entonces la carga de trabajo deberá esperar.

La programación de nuevas cargas de trabajo suele ser un poco sensible a la latencia; por ello, si no modificamos nada, algunas veces esos SLA no se verán afectados si hay muchos servicios y estados para desplazar. De hecho, cuando medimos los tiempos de creación en las simulaciones basadas en datos reales de clúster, vimos que, si los servicios eran suficientemente grandes y el clúster se usaba de forma equitativa, la creación de esos servicios de gran tamaño se ralentizaba. También detectamos que podíamos mejorar esto si introducíamos la noción de las métricas de desfragmentación.

La creación o el acceso a archivos, por ejemplo, puede ralentizarse si el disco duro de un equipo se fragmenta, y puede acelerarse mediante la desfragmentación de la unidad. De una forma similar, puede configurar métricas de desfragmentación para que Resource Manager intente condensar la carga de los servicios de forma proactiva en menos nodos, de forma que haya (casi) siempre espacio para los servicios grandes y que estos puedan crearse rápidamente. La mayoría de usuarios no necesitarán este procedimiento, porque los servicios acostumbran a ser de tamaño pequeño y, por consiguiente, no es difícil encontrarles espacio. No obstante, si tiene servicios grandes y necesita que se creen rápidamente (y está dispuesto a aceptar otras soluciones intermedias), las métricas de desfragmentación son justo lo que está buscando.

El diagrama siguiente ofrece una representación visual de dos clústeres diferentes, uno que está desfragmentado y otro que no. En el caso del clúster equilibrado, piense en los movimientos que serían necesarios para colocar uno de los objetos de servicio de mayor tamaño si se fuera a crear uno nuevo, en contraste con el clúster desfragmentado, donde podría colocarse inmediatamente en los nodos 4 o 5.

![Comparación de clústeres equilibrados y desfragmentados][Image1]

## Ventajas y desventajas de la desfragmentación
¿Cuáles son esas otras soluciones intermedias conceptuales? Le recomendamos que mida de forma exhaustiva sus cargas de trabajo antes de activar las métricas de desfragmentación. A continuación tiene una tabla de referencia rápida con los aspectos que debe tener en cuenta:

| Ventajas de la desfragmentación | Inconvenientes de la desfragmentación |
|----------------------|----------------------|
|Permite la creación rápida de servicios de gran tamaño. |	Concentra la carga en menos nodos, de modo que aumenta la contención.
|Permite reducir el movimiento de datos durante la creación. | Los errores pueden afectar a más servicios y provocar más renovaciones.
|Permite la descripción enriquecida de requisitos y la reclamación de espacio. |	La configuración general de administración de recursos es más compleja.

Puede combinar métricas de desfragmentación y normales en el mismo clúster; Resource Manager hará todo lo que pueda para asegurarse de que obtenga una distribución que consolide la mayor cantidad posible de métricas de desfragmentación, a la vez que intenta distribuir el resto. Los resultados exactos que obtendrá dependerán del número de métricas equilibradas en comparación con el número de métricas de desfragmentación, su peso, etc.

## Configuración de métricas de desfragmentación
Configurar las métricas de desfragmentación es una decisión global en el clúster, y pueden seleccionarse métricas individuales para la desfragmentación:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## Pasos siguientes
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte el artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

<!---HONumber=AcomDC_0525_2016-->