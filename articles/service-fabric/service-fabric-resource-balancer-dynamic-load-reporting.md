<properties
   pageTitle="Informe de carga dinámica"
   description="Información general sobre informes de carga dinámica al equilibrador de recursos"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Información general de informes de carga dinámica

Durante el tiempo de ejecución, los objetos de servicio con y sin estado pueden informar de cargas mediante el método ReportLoad (miembro de las interfaces IStatefulServicePartition y IStatelessServicePartition). Es importante crear informes de valores de carga de tiempo de ejecución porque permite un empaquetado preciso de los servicios en los nodos y ayuda a garantizar un seguimiento preciso de la utilización de recursos por parte del equilibrador de recursos de Service Fabric a medida que los servicios lo están experimentando en los nodos.

Tenga en cuenta que cuando una réplica informa de una carga, se almacena en lotes con otros informes de carga localmente y, a continuación, se envía un informe único al equilibrador de recursos. Este proceso significa que si un servicio informa de una carga de manera rápida y muy repetidamente, solo se envía el último informe al equilibrador de recursos.

Cuando se ejecuta el equilibrador de recursos de Service Fabric, examina toda la información de carga que se agrega desde todos los nodos y realiza algunas comprobaciones. Estas comprobaciones incluyen el umbral de equilibrio y los umbrales de actividad de las métricas, tal como se define en el manifiesto de clúster. Determinan si el clúster no está lo suficientemente equilibrado como para ejecutar el equilibrador de recursos y si algún nodo específico ha superado su capacidad para cualquiera de las métricas para las que tiene una capacidad definida. En el caso de superar la capacidad, en primer lugar el equilibrador de recursos solo considera los servicios del nodo o los nodos que han excedido la capacidad que comparten la métrica, que supera la capacidad. En un desequilibrio de clúster, el equilibrador de recursos considera todos los servicios relacionados por las métricas con cualquier servicio que informa de la métrica no equilibrada. Si el equilibrador de recursos de Service Fabric determina que se ha producido alguna de estas situaciones, ejecuta una simulación que intenta encontrar una mejor organización de los servicios.

Los servicios deben informar de la carga cada vez que la carga cambie y no deben realizar ninguna agregación o suavizado de los informes de carga por su cuenta.

Tenga en cuenta que cuando un servicio informa de una carga, estos informes de carga reemplazan los valores de carga principal y secundaria predeterminados que se definieron durante la creación del servicio y se convierten en los nuevos valores de carga que se usan cuando el equilibrador de recursos de Service Fabric tiene que realizar el equilibrio o tomar decisiones de selección de ubicación desde ese momento.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para obtener más información: [Arquitectura de equilibrador de recursos](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=Sept15_HO2-->