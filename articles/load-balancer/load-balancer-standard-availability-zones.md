---
title: Azure Load Balancer Estándar y zonas de disponibilidad | Microsoft Docs
description: Load Balancer Estándar y zonas de disponibilidad
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: kumud
ms.openlocfilehash: f5d46fda6bdb32c1a5000883c6aedb2da15e796a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322801"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Estándar y zonas de disponibilidad

La SKU Estándar de Azure Load Balancer admite escenarios de [zonas de disponibilidad](../availability-zones/az-overview.md). Hay varios conceptos nuevos disponibles con Load Balancer Estándar que permiten optimizar la disponibilidad en un escenario de un extremo a otro mediante la alineación de los recursos con las zonas y su distribución en distintas zonas.  Puede revisar el documento de [Zonas de disponibilidad](../availability-zones/az-overview.md) para obtener instrucciones sobre qué son las zonas de disponibilidad, qué regiones las admiten actualmente y otros productos y conceptos relacionados. Las zonas de disponibilidad, combinadas con Load Balancer Estándar, son un conjunto de características ampliable y flexible que posibilita la creación de diferentes escenarios.  Revise este documento para conocer estos [conceptos](#concepts) y la [guía de diseño](#design) de un escenario básico.

>[!NOTE]
>Revise las [zonas de disponibilidad](https://aka.ms/availabilityzones) para conocer otros temas relacionados. 

## <a name="concepts"></a> Conceptos de las zonas de disponibilidad aplicados a Load Balancer

No hay ninguna relación directa entre los recursos de Load Balancer y la infraestructura real; su creación no crea una instancia. Los recursos de Load Balancer son objetos mediante los cuales se puede expresar cómo debe Azure programar su infraestructura de varios inquilinos creada previamente para lograr el escenario que se desea crear.  Esto es importante en el contexto de las zonas de disponibilidad porque un único recurso de Load Balancer puede controlar la programación de la infraestructura en varias zonas de disponibilidad, mientras que un servicio con redundancia de zona aparece como un recurso desde el punto de vista del cliente.

Las funciones de un recurso de Load Balancer se expresan como un front-end, una regla, un sondeo de mantenimiento y una definición de grupo de back-end.

En el contexto de las zonas de disponibilidad, el comportamiento y las propiedades de un recurso de Load Balancer se describen como de redundancia de zona o zonal.  Redundancia de zona y zonal describen la zonalidad de una propiedad.  En el contexto de Azure Load Balancer, redundancia de zona siempre significa *todas las zonas* y zonal significa que se garantiza el servicio para una *sola zona*.

El uso de Load Balancer, tanto público como interno, admite escenarios de redundancia de zona y escenarios zonales, y ambos pueden dirigir el tráfico entre las zonas según sea necesario (*equilibrio de carga entre zonas*).

Un recurso de Load Balancer en sí mismo es regional y nunca zonal.  Y una red virtual y una subred siempre son regionales y nunca zonales.

### <a name="frontend"></a>Front-end

Un front-end de Load Balancer es una configuración de IP de front-end que hace referencia a un recurso de dirección IP pública o a una dirección IP privada dentro de la subred de un recurso de red virtual.  Conforma el punto de conexión con equilibrio de carga en el que se expone su servicio.

Un recurso de Load Balancer puede contener front-end zonales y con redundancia de zona al mismo tiempo. 

Cuando se garantiza un recurso de dirección IP pública a una zona, la zonalidad (o la ausencia de la misma) no es mutable.  Si desea cambiar u omitir la zonalidad de un front-end con dirección IP pública, debe volver a crear la dirección IP pública en la zona que corresponda.  

Para cambiar la zonalidad de un front-end de un recurso de Load Balancer interno, puede eliminar y volver a crear el front-end y cambiar u omitir la zonalidad.

Se utiliza varios front-end, hay consideraciones importantes que puede consultar en [varios front-end de Load Balancer](load-balancer-multivip-overview.md).

#### <a name="zone-redundant-by-default"></a>Redundancia de zona de forma predeterminada

En una región con zonas de disponibilidad, un front-end de Load Balancer Estándar tiene redundancia de zona de forma predeterminada.  Una dirección IP de front-end única puede sobrevivir a un error en la zona y se puede utilizar para llegar a todos los miembros del grupo de back-end con independencia de la zona. Esto no significa una ruta de acceso de datos sin incidencias, sino que los reintentos o el restablecimiento se realizarán correctamente. No son necesarios esquemas de redundancia de DNS. La dirección IP única del front-end la sirven al mismo tiempo implementaciones de infraestructura independientes en cada zona de disponibilidad.  La redundancia de zona significa que todos los flujos de entrada o de salida son atendidos por todas las zonas de disponibilidad de una región de forma simultánea con una única dirección IP.

Se puede producir un error en una o más zonas de disponibilidad y la ruta de acceso de datos sobrevive siempre que una zona de la región permanezca correcta. La configuración de redundancia de zona es el valor predeterminado y no requiere ninguna acción adicional.  Cuando una región obtiene la capacidad de admitir zonas de disponibilidad, un front-end existente tendrá automáticamente redundancia de zona.

Use el siguiente script para crear una dirección IP pública con redundancia de zona para el recurso de Load Balancer Estándar interno. Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Use el siguiente script para crear una dirección IP de front-end con redundancia de zona para el recurso de Load Balancer Estándar interno. Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

#### <a name="optional-zone-guarantee"></a>Garantía de zona opcional

Puede optar por tener un front-end garantizado para una sola zona, que se conoce como un *front-end zonal*.  Esto significa que cualquier flujo de entrada o de salida es atendido por una sola zona en una región.  El front-end comparte destino con el estado de la zona.  La ruta de acceso de datos no se ve afectada por errores en zonas distintas a la garantizada. Puede utilizar front-end zonales para exponer una dirección IP por cada zona de disponibilidad.  Además, puede usar front-end zonales directamente o, cuando el front-end se compone de direcciones IP públicas, puede integrarlos con una producto de equilibrio de carga de DNS como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) y usar un nombre de DNS único, que un cliente resolverá en varias direcciones IP zonales.  También puede utilizar esto para exponer puntos de conexión con equilibrio de carga por zona para supervisar de forma individual cada zona.  Si desea combinar estos conceptos (redundancia de zona y zonal para el mismo back-end), consulte [varios front-end en Azure Load Balancer](/load-balancer-multivip-overview.md).

Para un front-end de Load Balancer público, se agrega un parámetro de *zonas* a la dirección IP pública a la que hace referencia la configuración de IP del front-end.  

Para un front-end de Load Balancer interno, se agrega un parámetro de *zonas* a la configuración IP del front-end del recurso de Load Balancer interno. El front-end zonal hace que Load Balancer garantice una dirección IP en una subred para una zona específica.

Use el siguiente script para crear una dirección IP pública Estándar zonal en la zona de disponibilidad 1. Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Use el siguiente script para crear un front-end de Load Balancer Estándar interno en la zona de disponibilidad 1.

Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas. Asimismo, defina la propiedad **zonas** en la configuración de IP del front-end para el recurso secundario.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

### <a name="cross-zone-load-balancing"></a>Equilibrio de carga entre zonas

El equilibrio de carga entre zonas es la capacidad de Load Balancer para alcanzar un punto de conexión de back-end en cualquier zona y es independiente del front-end y su zonalidad.

Si desea alinear y garantizar la implementación en una sola zona, puede alinear recursos de front-end zonal y de back-end zonal en la misma zona. No se requiere ninguna acción adicional.

### <a name="backend"></a>Back-end

Load Balancer funciona con máquinas virtuales.  Todas las máquinas virtuales de una red virtual pueden formar parte del grupo de back-end con independencia de si están garantizadas para una zona o a qué zona están garantizadas.

Si desea alinear y garantizar el front-end y el back-end con una sola zona, basta con colocar las máquinas virtuales dentro de la misma zona en el grupo de back-end respectivo.

Si desea ubicar las máquinas virtuales a través de varias zonas, basta con colocar las máquinas virtuales de varias zonas en el mismo grupo de back-end.  Cuando se utilizan conjuntos de escalado de máquinas virtuales, puede ubicar uno o más conjuntos de escalado de máquinas virtuales en el mismo grupo de back-end.  Y cada uno de estos conjuntos de escalado de máquinas virtuales puede estar en una o varias zonas.

### <a name="outbound-connections"></a>Conexiones salientes

Las [conexiones salientes](load-balancer-outbound-connections.md) se atienden desde todas las zonas y tienen automáticamente redundancia de zona en una región con zonas de disponibilidad cuando una máquina virtual está asociada con un recurso de Load Balancer público y un front-end con redundancia de zona.  Las asignaciones de puerto SNAT de la conexiones salientes sobreviven a errores de la zona.  

En cambio, si la máquina virtual está asociada a un recurso de Load Balancer público y a un front-end zonal, las conexiones salientes tienen garantizado el servicio por una única zona.  Las conexiones salientes comparten destino con el estado de la zona correspondiente.

La asignación previa de puerto SNAT y el algoritmo son los mismos con o sin zonas.

### <a name="health-probes"></a>Sondeos de estado

Las definiciones de sondeos de mantenimiento existentes permanecen tal cual estaban sin zonas de disponibilidad.  Pero hemos ampliado el modelo de mantenimiento hasta un nivel de infraestructura. 

Cuando se usan front-end con redundancia de zona, Load Balancer expande su modelo de mantenimiento interno para sondear el alcance de una máquina virtual de cada zona de disponibilidad por separado y cerrar las rutas de acceso entre zonas que puedan haber tenido errores sin la intervención del usuario.  Si una ruta de acceso determinada no está disponible desde la infraestructura de Load Balancer de una zona para una máquina virtual de otra zona, Load Balancer puede detectar y evitar este error. Otras zonas que puedan llegar a esta máquina virtual pueden continuar sirviendo la máquina virtual desde sus front-end respectivos.  Como resultado, es posible que, durante los eventos de error, cada zona pueda tener distribuciones de flujo ligeramente diferentes al proteger el mantenimiento general de su servicio de un extremo a otro.

## <a name="design"></a> Consideraciones de diseño

Load Balancer es deliberadamente flexible en el contexto de las zonas de disponibilidad. Puede optar por alinearse con zonas y puede optar por la redundancia de zona.  Aumentar la disponibilidad puede conllevar una mayor complejidad y debe diseñar la disponibilidad para un rendimiento óptimo.  Revise algunas consideraciones de diseño importantes.

### <a name="automatic-zone-redundancy"></a>Redundancia de zona automática

Load Balancer hace sencillo tener una dirección IP única como un front-end con redundancia de zona. Una dirección IP con redundancia de zona puede servir de forma segura un recurso zonal de cualquier zona y puede sobrevivir a uno o más errores de zona mientras una zona funcione correctamente en la región. Por el contrario, un front-end zonal es una reducción del servicio a una única zona y comparte el destino con la zona correspondiente.

La redundancia de zona no implica una ruta de acceso de datos sin incidencias ni un plano de control; es expresamente un plano de datos. Los flujos con redundancia de zona pueden usar cualquier zona y los flujos de un cliente utilizarán todas las zonas correctas de una región. En caso de error de zona, el tráfico fluye por las zonas correctas que en ese momento del tiempo no se ven afectadas.  El tráfico que fluye por una zona en el momento de un error en dicha zona puede verse afectado, pero las aplicaciones se pueden recuperar y los flujos continúan en las restantes zonas correctas de la región después de la retransmisión o el restablecimiento una vez que Azure ha detectado el error en la zona.

### <a name="xzonedesign"></a> Límites entre zonas

Es importante comprender que cuando un servicio de un extremo a otro cruza zonas, comparte su destino no con una zona, sino potencialmente con varias zonas.  Como resultado, el servicio de un extremo a otro podría no obtener ninguna disponibilidad en implementaciones no zonales.

Evite la introducción de dependencias entre zonas no deseadas que anulan las mejoras de disponibilidad cuando utilice zonas de disponibilidad.  Cuando la aplicación consta de varios componentes y desea que sean resistentes a los errores de zona, debe tener cuidado para asegurar la supervivencia de los componentes fundamentales suficientes si se produce un error en la zona.  Por ejemplo, un único componente crítico de la aplicación puede afectar a toda la aplicación si solo se encuentra en una zona que no sea de las zonas supervivientes.  Además, considere también la restauración de la zona y cómo se restablecerá la aplicación. Vamos a revisar algunos puntos clave y usarlos como ideas para preguntas al pensar sobre su escenario concreto.

- Si la aplicación tiene dos componentes, como una dirección IP y una máquina virtual con un disco administrado y están garantizados en las zonas 1 y 2, cuando se produce un error la zona 1, el servicio de un extremo a otro de la zona no sobrevivirá.  No se deben cruzar zonas a menos que se comprenda totalmente que se va a crear un modo de error potencialmente perjudicial.

- Si la aplicación tiene dos componentes, como una dirección IP y una máquina virtual con un disco administrado y tienen garantizadas la redundancia de zona y la zona 1 respectivamente, el servicio de un extremo a otro sobrevivirá a errores en las zonas 2 y 3 o ambas, a menos que se produzcan errores en la zona 1.  No obstante, perderá cierta capacidad para razonar sobre el mantenimiento del servicio si todo lo que se observa es si se puede llegar al front-end.  Considere la posibilidad de desarrollar un modelo de mantenimiento y capacidad más amplio.  Es posible que pueda usar los conceptos de redundancia de zona y zonal conjuntamente para expandir la visión general y facilidad de uso.

- Si la aplicación tiene dos componentes, como un front-end de Load Balancer con redundancia de zona y un conjunto de escalado de máquinas virtuales distribuido en tres zonas, los recursos de las zonas no afectadas por el error estarán disponibles, pero el servicio de un extremo a otro puede verse degradado en términos de capacidad durante el error en la zona. Desde una perspectiva de infraestructura, la implementación puede sobrevivir a uno o más errores de zona y esto nos lleva a las siguientes preguntas:
  - ¿Conoce la lógica de la aplicación ante estos errores y ante una capacidad degradada?
  - ¿Necesita tener medidas de seguridad en el servicio para forzar la conmutación por error a otra región, si es necesario?
  - ¿Cómo serán la supervisión, la detección y la mitigación ante este escenario? Puede usar los diagnósticos de Load Balancer Estándar para aumentar la supervisión del rendimiento del servicio de un extremo a otro. Tenga en cuenta lo que está disponible y lo que puede necesitar de un aumento para tener una imagen completa.

- Las zonas pueden hacer que los errores sean más fáciles de entender y contener.  Sin embargo, un error en la zona no es diferente a otros errores en cuanto a conceptos como tiempos de espera, reintentos y algoritmos de retroceso. Aunque Azure Load Balancer proporciona rutas de acceso de redundancia de zona e intenta una rápida recuperación, en un nivel de paquete en tiempo real, las retransmisiones o los restablecimientos pueden producirse durante la aparición de un error y es importante conocer cómo controla la aplicación los errores. El esquema de equilibrio de carga sobrevivirá, pero debe planear lo siguiente:
  - Cuando se produce un error en una zona, ¿el servicio de un extremo a otro comprende esto y si se pierde el estado, cómo se recuperará?
  - Cuando vuelve la disponibilidad de una zona, ¿la aplicación sabe cómo realizar una recuperación de forma segura?

### <a name="zonalityguidance"></a> Con redundancia de zona frente a zonal

La redundancia de zona puede proporcionar una opción independiente de la zona y al mismo tiempo resistente con una única dirección IP para el servicio.  A su vez, puede reducir la complejidad.  La redundancia de zona también tiene movilidad entre zonas y se puede usar de forma segura en los recursos de cualquier zona.  Además, es una opción de futuro en regiones sin zonas de disponibilidad, lo que puede limitar los cambios necesarios una vez que una región tenga zonas de disponibilidad.  La sintaxis de configuración para una dirección IP o un front-end con redundancia de zona se ejecuta correctamente en cualquier región, incluidas aquellas sin zonas de disponibilidad.

La opción zonal puede proporcionar una garantía explícita para una zona, compartiendo destino con el estado de la zona. La asociación de una dirección IP zonal o de un front-end de Load Balancer zonal puede ser un atributo deseable o razonable, especialmente si el recurso conectado es una máquina virtual zonal de la misma zona.  O quizás la aplicación requiere un conocimiento explícito sobre la zona en la que se encuentra un recurso y desea analizar la disponibilidad en zonas independientes explícitamente.  Puede elegir exponer varios front-end zonales para un servicio de un extremo a otro distribuido en distintas zonas (es decir, varios front-end zonales por zona para varios conjuntos de escalado de máquinas virtuales zonales).  Y si los front-end zonales son direcciones IP públicas, puede utilizar estos front-end zonales para exponer el servicio con [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  O bien, puede utilizar varios front-end zonales para tener detalles de mantenimiento y rendimiento por zona mediante soluciones de supervisión de terceros y exponer el servicio global con un front-end con redundancia de zona. Debe servir únicamente recursos zonales con front-end zonales alineados con la misma zona y evitar escenarios entre zonas con riesgos potenciales para los recursos zonales.  Los recursos zonales existen en las regiones donde hay zonas de disponibilidad.

No hay una guía general para saber cuál es la mejor elección sin conocer la arquitectura del servicio.

## <a name="limitations"></a>Limitaciones

- Aunque el plano de datos tiene una redundancia de zona completa (salvo que se especifique la garantía zonal), las operaciones del plano de control no tienen una redundancia de zona completa.

## <a name="next-steps"></a>Pasos siguientes
- Aprenda más sobre [zonas de disponibilidad](../availability-zones/az-overview.md).
- Más información sobre [Load Balancer Estándar](load-balancer-standard-overview.md)
- Obtenga información sobre cómo [equilibrar la carga de las máquinas virtuales dentro de una zona con Load Balancer estándar con un front-end de zona](load-balancer-standard-public-zonal-cli.md)
- Obtenga información sobre cómo [equilibrar la carga de las máquinas virtuales en distintas zonas con Load Balancer estándar con un front-end con redundancia de zona](load-balancer-standard-public-zone-redundant-cli.md)
