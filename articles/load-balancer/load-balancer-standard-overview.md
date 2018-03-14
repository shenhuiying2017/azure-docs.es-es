---
title: "Introducción a Azure Load Balancer Standard | Microsoft Docs"
description: "Introducción a las características de Azure Load Balancer Standard"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: ddbfb415f062396f022f0f58cb975f6e3a5f1807
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Introducción a Azure Load Balancer estándar (versión preliminar)

La SKU estándar de Azure Load Balancer y la SKU estándar de IP pública juntas permiten crear arquitecturas de gran escalabilidad y confiabilidad. Las aplicaciones que usan Load Balancer estándar pueden sacar partido de las nuevas funcionalidades. La baja latencia, el alto rendimiento y escalación están disponibles para millones de flujos para todas las aplicaciones de TCP y UDP.

>[!NOTE]
> La SKU estándar de Load Balancer se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use la [SKU básica de Load Balancer](load-balancer-overview.md) disponible con carácter general con sus servicios de producción. Para usar la [versión preliminar de las zonas de disponibilidad](https://aka.ms/availabilityzones) con esta versión preliminar, deberá [registrarse por separado](https://aka.ms/availabilityzones), además de registrarse en la [versión preliminar de ](#preview-sign-up)Load Balancer estándar.

## <a name="why-use-load-balancer-standard"></a>¿Por qué usar Load Balancer estándar?

Puede usar Load Balancer estándar para toda la gama de centros de datos virtual. Use Load Balancer estándar, desde implementaciones a pequeña escala hasta arquitecturas de varias zonas grandes y complejas, para aprovechar las siguientes funcionalidades:

- Se puede conseguir una [escala empresarial](#enterprisescale) con Load Balancer estándar. Esta característica puede usarse con cualquier instancia de máquina virtual (VM) de una red virtual, hasta 1000 instancias de VM.

- Hay [nueva información de diagnóstico](#diagnosticinsights) disponible para ayudarle a entender, administrar y solucionar problemas de este componente esencial de su centro de datos virtual. Use Azure Monitor (versión preliminar) para mostrar, filtrar y agrupar nuevas métricas multidimensionales para las medidas de mantenimiento de ruta de datos continuas. Supervise los datos de servidor front-end a VM, sondeos de estado de los puntos de conexión, para intentos de conexión de TCP y para conexiones salientes.

- Los [grupos de seguridad de red](#nsg) son ahora necesarios para cualquier instancia de VM asociada a las SKU estándar de Load Balancer o SKU estándar de dirección IP pública. Los grupos de seguridad de red (NSG) proporcionan una seguridad mejorada para su escenario.

- Los [puertos de alta disponibilidad (HA) proporcionan alta confiabilidad](#highreliability) y escalado para aplicaciones virtuales de red (NVA) y otros escenarios de aplicación. Los puertos HA equilibran la carga en todos los puertos entre un servidor front-end de Azure Internal Load Balancer (ILB) y un grupo de instancias de máquina virtual.

- Las [conexiones salientes](#outboundconnections) usan ahora un nuevo modelo de asignación de puertos de traducción de direcciones de red de origen (SNAT) que proporciona mayor resistencia y escalabilidad.

- [Load Balancer estándar con zonas de disponibilidad](#availabilityzones) se puede usar para construir arquitecturas zonales y con redundancia de zona. Ambas arquitecturas pueden incluir el equilibrio de carga entre zonas. Puede lograr la redundancia de zona sin dependencia en los registros DNS. Una única dirección IP tiene redundancia de zona de forma predeterminada.  Una única dirección IP puede llegar a cualquier VM en una red virtual dentro de una región en todas las zonas de disponibilidad.


Puede usar Load Balancer estándar en una configuración pública o interna para admitir los siguientes escenarios fundamentales:

- Tráfico de entrada de equilibrio de carga para instancias de servidor back-end correctas.
- Tráfico de entrada de reenvío de puerto a una instancia de servidor back-end única.
- Conversión del tráfico saliente de una dirección IP privada de la red virtual a una dirección IP pública.

### <a name = "enterprisescale"></a>Escala empresarial

 Utilice Load Balancer estándar para diseñar su centro de datos virtual de alto rendimiento y admitir cualquier aplicación de TCP o UDP. Utilice instancias de VM independientes o hasta 1000 instancias de conjuntos de escalas de máquina virtual en un grupo de servidores back-end. Siga usando la latencia baja de reenvío, el alto rendimiento y el escalado a millones de flujos en un servicio de Azure completamente administrado.
 
Load Balancer estándar puede reenviar el tráfico a cualquier instancia de máquina virtual de una red virtual de una región. Los tamaños de grupo de servidores back-end pueden tener hasta 1,000 instancias con cualquier combinación de los siguientes escenarios de VM:

- Máquinas virtuales independiente sin conjuntos de disponibilidad
- Máquinas virtuales independientes con conjuntos de disponibilidad
- Conjuntos de escalado de máquinas virtuales, hasta 1000 instancias
- Varios conjuntos de escalado de máquinas virtuales
- Mezclas de máquinas virtuales y conjuntos de escalado de máquinas virtuales

Ya no hay un requisito para conjuntos de disponibilidad. Puede usar conjuntos de disponibilidad para las otras ventajas que proporcionan.

### <a name = "diagnosticinsights"></a>Información de diagnóstico

Load Balancer estándar proporciona nuevas funcionalidades de diagnóstico multidimensionales para configuraciones de Load Balancer público e interno. Estas nuevas métricas se proporcionan a través de Azure Monitor (versión preliminar) y usan todas las funcionalidades relacionadas, como la posibilidad de integración con consumidores de nivel inferior.

| Métrica | DESCRIPCIÓN |
| --- | --- |
| Disponibilidad de VIP | Load Balancer estándar usa continuamente la ruta de acceso a los datos desde una región hasta el servidor front-end de Load Balancer y, finalmente, hasta la pila de SDN que respalda la máquina virtual. Siempre que permanezcan las instancias correctas, la medida sigue la misma ruta de acceso que el tráfico con equilibrio de carga de las aplicaciones. También se valida la ruta de acceso a los datos usada por los clientes. La medida es invisible para la aplicación y no interfiere con otras operaciones.|
| Disponibilidad de DIP | Load Balancer estándar usa un servicio de sondeo de estado distribuido que supervisa el estado del punto de conexión de la aplicación de acuerdo con lo que se ha configurado. Esta métrica proporciona una vista agregada o filtrada por punto de conexión de cada punto de conexión de instancia individual del grupo de Load Balancer.  Puede ver cómo Load Balancer observa el estado de su aplicación según se indica en la configuración de sondeo de estado.
| Paquetes SYN | Load Balancer estándar no finaliza las conexiones TCP ni interactúa con los flujos de paquetes TCP o UDP. Los flujos y los protocolos de enlace son siempre entre el origen y la instancia de máquina virtual. Para solucionar mejor los escenarios de protocolo TCP, puede hacer uso de estos contadores de paquetes SYN para saber el número de intentos de conexión TCP realizados. La métrica indica el número de paquetes TCP SYN recibidos.|
| Conexiones SNAT | Load Balancer Estándar informa del número de flujos salientes enmascarados en el servidor front-end de dirección IP pública. Los puertos SNAT son un recurso agotable. Esta métrica puede proporcionar una indicación de la dependencia que su aplicación tiene de SNAT en los flujos salientes originados.  Los contadores de los flujos de salida de SNAT que se realizaron con éxito y los que tuvieron algún error se notifican y se pueden utilizar para solucionar problemas y comprender el estado de los flujos de salida.|
| Contadores de bytes | Load Balancer estándar informa de los datos procesados por servidor front-end.|
| Contadores de paquetes | Load Balancer estándar informa de los paquetes procesados por servidor front-end.|

### <a name = "highreliability"></a>Alta confiabilidad

Configure reglas de equilibrio de carga para asegurarse de que su aplicación se escala y es enormemente confiable. Puede configurar reglas para puertos individuales o puede usar puertos HA para equilibrar todo el tráfico con independencia del número de puerto TCP o UDP.  

Puede utilizar la nueva característica de puertos HA para desbloquear una variedad de escenarios, incluida la alta disponibilidad y escalado para dispositivos virtuales de red disponibles internos. La característica es útil para otros escenarios donde no es práctico o no deseable especificar los puertos individuales. Los puertos de alta disponibilidad ofrecen redundancia y escalado, y permiten el número de instancias que necesite. La configuración ya no está restringida a los escenarios activos/pasivos. Las configuraciones de sondeo de estado protegen el servicio dado que el tráfico solo se reenvía a instancias con un estado correcto.

Los fabricantes de NVA pueden proporcionar escenarios resistentes y totalmente compatibles con los proveedores para sus clientes. Se quita el punto de error único y se admiten varias instancias activas para el escalado. Se puede escalar a dos o más instancias, según las funcionalidades del dispositivo. Para obtener instrucciones adicionales para estos escenarios, póngase en contacto con su proveedor de NVA.

### <a name = "availabilityzones"></a>Zonas de disponibilidad

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Aumente la resistencia de la aplicación con el uso de zonas de disponibilidad en regiones admitidas. Las zonas de disponibilidad se encuentran actualmente en versión preliminar en regiones específicas y requieren alguna suscripción adicional.

### <a name="automatic-zone-redundancy"></a>Redundancia de zona automática

Puede elegir si Load Balancer debe proporcionar un servidor front-end con redundancia de zona o zonal para cada una de las aplicaciones. Con Load Balancer estándar, es fácil crear redundancia de zona. Una única dirección IP de servidor front-end tiene automáticamente redundancia de zona. Todas las zonas de disponibilidad de una región atienden a la vez a un servidor front-end con redundancia de zona. Se crea una ruta de acceso a datos con redundancia de zona para las conexiones entrantes y salientes. La redundancia de zona en Azure no requiere varias direcciones IP y registros DNS. 

La redundancia de zona está disponible para servidores front-end públicos o internos. La dirección IP pública y la dirección IP privada del servidor front-end para Load Balancer interno pueden tener redundancia de zona.

Use el siguiente script para crear una dirección IP pública con redundancia de zona para el Load Balancer interno. Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas.

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

Use el siguiente script para crear una dirección IP de servidor front-end con redundancia de zona para el Load Balancer interno. Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas.

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

Si el servidor front-end de dirección IP pública tiene redundancia de zona, las conexiones salientes realizadas desde instancias de máquina virtual tendrán automáticamente redundancia de zona. El servidor front-end estará protegido de los errores de zona. La asignación del puerto SNAT también sobrevive a los errores de zona.

#### <a name="cross-zone-load-balancing"></a>Equilibrio de carga entre zonas

El equilibrio de carga entre zonas está disponible dentro de una región del grupo de servidores back-end y ofrece la máxima flexibilidad en las instancias de máquina virtual. Un servidor front-end proporciona flujos a cualquier máquina virtual de la red virtual con independencia de la zona de disponibilidad de la instancia de máquina virtual.

También puede especificar una zona determinada para sus instancias de servidor front-end y back-end para alinear la ruta de acceso a los datos y los recursos con una zona específica.

Las redes virtuales y subredes nunca están restringidas por una zona. Solo defina un grupo de servidores back-end con sus instancias de VM deseadas y su configuración estará completa.

#### <a name="zonal-deployments"></a>Implementaciones zonal

Como opción, puede alinear el servidor front-end de Load Balancer para una zona específica mediante la definición de un servidor front-end de zona. Un servidor front-end de zona se proporciona solo mediante la zona de disponibilidad única designada. Cuando el servidor front-end se combina con instancias de VM de zona, puede alinear recursos con zonas específicas.

Una dirección IP pública que se cree en una zona específica siempre existirá solo en esa zona. No es posible cambiar la zona de una dirección IP pública. Para obtener una dirección IP pública que pueda adjuntarse a recursos en varias zonas, cree una dirección IP pública con redundancia de zona.

Use el siguiente script para crear una dirección IP pública de zona en la zona de disponibilidad 1. Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas.

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

Use el siguiente script para crear un servidor front-end de Load Balancer interno en la zona de disponibilidad 1.

Si está usando las plantillas de Resource Manager existentes en la configuración, agregue la sección **sku** a estas plantillas. Asimismo, defina la propiedad **zones** en la configuración de dirección IP de servidor front-end para el recurso secundario.

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

Agregue equilibrio de carga entre zonas para el grupo de servidores back-end colocando las instancias de máquina virtual que se encuentran en una red virtual en el grupo.

El recurso Load Balancer estándar siempre es regional y tiene redundancia de zona donde se admiten zonas de disponibilidad. Puede implementar una dirección IP pública o un servidor front-end de Load Balancer interno que no tenga una zona asignada en cualquier región. La compatibilidad de zonas de disponibilidad no afecta a la funcionalidad de implementación. Si una región obtiene zonas de disponibilidad más adelante, las dirección IP públicas implementadas anteriormente o los servidores front-end de Load Balancer internos pasan a tener redundancia de zona automáticamente. Una ruta de acceso a datos con redundancia de zona no implica que no se produzcan pérdidas de paquetes.

### <a name = "nsg"></a>Grupos de seguridad de red

Load Balancer estándar e IP pública estándar se incorporan completamente a la red virtual, que requiere el uso de grupos de seguridad de red (NSG). Los NSG permiten el flujo tráfico de lista blanca. Puede usar NSG para obtener un control total sobre el tráfico en la implementación. Ya no tendrá que esperar hasta que los demás flujos de tráfico se completen.

Asocie NSG con subredes o las interfaces de red (NIC) de instancias de máquina virtual en el grupo de servidores back-end. Utilice esta configuración con Load Balancer estándar y dirección IP pública estándar cuando se use como una dirección IP pública a nivel de instancia. El NSG debe explícitamente poner en la lista blanca el tráfico que desea permitir para que el tráfico fluya.

Para aprender más sobre los NSG y cómo aplicarlos en su caso, vea [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Conexiones salientes

Load Balancer estándar proporciona conexiones salientes para las VM que están dentro de la red virtual cuando un equilibrador de carga utiliza SNAT de simulación de puerto. El algoritmo SNAT de simulación de puerto proporciona escalado y mayor solidez.

Cuando un recurso de Load Balancer público está asociado con instancias de máquina virtual, se reescribe cada origen de conexión de salida. El origen se reescribe desde el espacio de direcciones IP privadas de red virtual a la dirección IP pública de servidor front-end del equilibrador de carga.

Cuando se usan conexiones de salida con un servidor front-end con redundancia de zona, las conexiones también tienen redundancia de zona y las asignaciones de puerto SNAT sobreviven a los errores de zona.

El nuevo algoritmo en Load Balancer estándar asigna previamente los puertos SNAT a la NIC de cada máquina virtual. Cuando una NIC se agrega al grupo, los puertos SNAT se asignan previamente según el tamaño del grupo. En la tabla siguiente se muestran las asignaciones previas de puerto para los seis niveles de tamaños de grupo de servidores back-end:

| Tamaño del grupo (instancias de máquina virtual) | Número de puertos SNAT asignado previamente |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 -1000 | 32 |

Los puertos SNAT no se convierten directamente al número de conexiones salientes. Un puerto SNAT se puede reutilizar con varios destinos únicos. Para más información, revise el artículo sobre [conexiones salientes](load-balancer-outbound-connections.md).

Si el tamaño del grupo de servidores back-end aumenta y pasa a un nivel superior, se recupera la mitad de los puertos asignados. Las conexiones están asociadas a un tiempo de espera del puerto reclamado y deben restablecerse. Los nuevos intentos de conexión se realizarán correctamente de inmediato. Si el tamaño del grupo de servidores back-end se reduce y pasa a un nivel inferior, aumenta el número de puertos SNAT disponibles. En este caso, las conexiones existentes no se ven afectadas.

Load Balancer estándar tiene una opción de configuración adicional que puede usarse según la regla. Puede controlar el servidor front-end que se utiliza para SNAT de simulación de puerto cuando hay varios servidores front-end disponibles.

Cuando Load Balancer estándar solo atiende instancias de máquina virtual, las conexiones SNAT salientes no están disponibles. Puede restaurar esta capacidad explícitamente mediante la asignación también de las instancias de máquina virtual a un equilibrador de carga público. También puede asignar directamente direcciones IP públicas como direcciones IP públicas de nivel de instancia a cada instancia de máquina virtual. Esta opción de configuración puede ser necesaria con algunos escenarios de aplicaciones y sistemas operativos. 

### <a name="port-forwarding"></a>Enrutamiento de puerto

Los equilibradores de carga básicos y estándar ofrecen la posibilidad de configurar reglas NAT entrantes para asignar un puerto de servidor front-end a una instancia de servidor back-end individual. Al configurar estas reglas, puede exponer los puntos de conexión del protocolo de escritorio remoto y los puntos de conexión SSH, o realizar otros escenarios de aplicación.

Load Balancer estándar sigue proporcionando la funcionalidad de reenvío de puertos mediante reglas NAT entrantes. Cuando se usa con servidores front-end con redundancia de zona, las reglas NAT entrantes pasan a tener redundancia de zona y sobreviven a los errores de zona.

### <a name="multiple-front-ends"></a>Varios servidores front-end

Configure varios servidores front-end para un diseño flexible donde las aplicaciones deban exponer varias direcciones IP, como sitios web TLS o puntos de conexión de grupo de disponibilidad SQL AlwaysOn. 

Load Balancer estándar continúa proporcionando varios servidores front-end cuando es necesario exponer un punto de conexión de la aplicación específico en una única dirección IP.

Para más información sobre la configuración de varias direcciones IP de servidor front-end, vea [Varias IP virtuales para Azure Load Balancer](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Acerca de las SKU

Las SKU solo están disponibles en el modelo de implementación de Azure Resource Manager. Esta versión preliminar presenta dos SKU para los recursos Load Balancer y dirección IP pública: básica y estándar. Las SKU difieren en cuanto a sus capacidades, características de rendimiento, limitaciones y algunos comportamientos intrínsecos. Las máquinas virtuales se pueden usar con cualquier SKU. En los recursos Load Balancer y dirección IP pública, las SKU son atributos opcionales. Si las SKU se omiten en una definición de escenario, adoptan la configuración de SKU básica de forma predeterminada.

>[!IMPORTANT]
>La SKU de un recurso no es mutable. No se puede cambiar la SKU de un recurso existente.  

### <a name="load-balancer"></a>Load Balancer

El [recurso existente Load Balancer](load-balancer-overview.md) se convierte en la SKU básica y permanece disponible con carácter general y sin cambios.

La SKU estándar de Load Balancer es nueva y se encuentra actualmente en versión preliminar. El 1 de agosto de 2017, la versión de la API para Microsoft.Network/loadBalancers agrega la propiedad **sku** a la definición de recursos:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer estándar es resistente en la zona automáticamente en regiones que ofrecen zonas de disponibilidad. Si Load Balancer se ha declarado de zona, no es resistente en la zona automáticamente.

### <a name="public-ip"></a>Dirección IP pública

El [recurso existente de dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) se convierte en la SKU básica y permanece disponible con carácter general con todas sus capacidades, características de rendimiento y limitaciones.

La SKU estándar de dirección IP pública es nueva y se encuentra actualmente en versión preliminar. El 1 de agosto de 2017, la versión de la API para Microsoft.Network/publicIPAddresses agrega la propiedad **sku** a la definición de recursos:

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

A diferencia de la dirección IP pública básica que ofrece varios métodos de asignación, en la dirección IP pública estándar la asignación es siempre estática.

La dirección IP estándar pública es resistente en la zona automáticamente en regiones que ofrecen zonas de disponibilidad. Si la dirección IP pública se ha declarado de zona, no es resistente en la zona automáticamente. No es posible cambiar una dirección IP pública zonal de una zona a otra.

## <a name="migration-between-skus"></a>Migración entre SKU

Las SKU no son mutables. Siga los pasos de esta sección para cambiar de una SKU de recurso a otra.

### <a name="migrate-from-basic-to-standard-sku"></a>Migración de una SKU básica a una estándar

1. Cree un nuevo recurso estándar (Load Balancer y direcciones IP públicas cuando sea necesario). Vuelva a crear sus reglas y definiciones de sondeo.

2. Cree un grupo de seguridad de red nuevo o actualice uno existente en la NIC o la subred para incluir en la lista de permitidos el tráfico con equilibrio de carga  y cualquier otro tráfico que desee permitir.

3. Quite los recursos de SKU básica (Load Balancer y direcciones IP públicas, según corresponda) de todas las instancias de máquina virtual. Asegúrese de quitar también todas las instancias de máquina virtual de un conjunto de disponibilidad.

4. Asocie todas las instancias de máquina virtual a los nuevos recursos de SKU estándar.

### <a name="migrate-from-standard-to-basic-sku"></a>Migración de SKU estándar a básica

1. Cree un nuevo recurso básico (Load Balancer y direcciones IP públicas cuando sea necesario). Vuelva a crear sus reglas y definiciones de sondeo. 

2. Quite los recursos de SKU estándar (Load Balancer y direcciones IP públicas, según corresponda) de todas las instancias de máquina virtual. Asegúrese de quitar también todas las instancias de máquina virtual de un conjunto de disponibilidad.

3. Asocie todas las instancias de máquina virtual a los nuevos recursos de SKU básica.

>[!IMPORTANT]
>
>Existen limitaciones sobre uso de las SKU básica y estándar.
>
>Los puertos HA y el diagnóstico de la SKU estándar solo están disponibles en la SKU estándar. No puede migrar desde la SKU estándar a la SKU básica y también conservan estas características.
>
>En los recursos Load Balancer e IP pública se deben usar SKU coincidentes. No puede tener una combinación de recursos de SKU básica y recursos de SKU estándar. No puede conectar una VM, las máquinas virtuales en un conjunto de disponibilidad o una escala de máquina virtual establecido en ambas SKU de forma simultánea.
>

## <a name="region-availability"></a>Disponibilidad en regiones

Load Balancer estándar está disponible actualmente en todas las regiones de la nube pública.

>[!IMPORTANT]
> Durante un breve período de tiempo, el acceso a regiones no incluidas en el lanzamiento inicial (Este de EE. UU. 2, Centro de EE. UU., Europa del Norte, Centro Occidental de EE. UU., Europa Occidental, Sudeste Asiático) requerirá el registro de características de suscripción adicionales (AllowLBPreviewWave2 y AllowLBPreviewWave3).  [Siga los pasos que se indican a continuación](#additionalpreviewregions). Ejecute todas las características, incluso si ya se registró en AllowLBPreview.
> Este paso dejará de ser un requisito en las próximas semanas.

## <a name="sku-service-limits-and-abilities"></a>Capacidades y límites de servicio de la SKU

Los [límites de servicio de red](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits) se aplican por región y suscripción. 

En la tabla siguiente se comparan los límites y capacidades de los SKU de Load Balancer básico y estándar:

| Load Balancer | Básica | Estándar |
| --- | --- | --- |
| Tamaño del grupo de servidores back-end | hasta 100 | hasta 1000 |
| Límite de grupo de servidores back-end | Conjunto de disponibilidad | red virtual, región |
| Diseño de grupo de servidores back-end | Máquinas virtuales de un conjunto de disponibilidad, conjunto de escalado de máquinas virtuales de un conjunto de disponibilidad | Cualquier instancia de máquina virtual en la red virtual |
| Puertos HA | No compatible | Disponible |
| Diagnóstico | Limitado, solo público | Disponible |
| Disponibilidad de VIP  | No compatible | Disponible |
| Movilidad rápida de IP | No compatible | Disponible |
|Escenarios de zonas de disponibilidad | Solo zonal | Zonal, redundancia de zona, equilibrio de carga entre zonas |
| Algoritmo de SNAT saliente | A petición | Asignado previamente |
| Selección de servidor front-end de SNAT saliente | No se puede configurar, varios candidatos | Configuración opcional para reducir los candidatos |
| Grupo de seguridad de red (NSG) | Opcional en la NIC/subred | Obligatorio |

En la tabla siguiente se comparan los límites y capacidades de SKU básica y estándar de la dirección IP pública:

| Dirección IP pública | Básica | Estándar |
| --- | --- | --- |
| Escenarios de zonas de disponibilidad | Solo zonal | Con redundancia de zona (predeterminado), zonal (opcional) | 
| Movilidad rápida de IP | No compatible | Disponible |
| Disponibilidad de VIP | No compatible | Disponible |
| Counters | No compatible | Disponible |
| Grupo de seguridad de red (NSG) | Opcional en la NIC | Obligatorio |


## <a name="preview-sign-up"></a>Registro en versión preliminar

Para participar en la versión preliminar de la SKU estándar de Load Balancer y su SKU estándar complementaria de dirección IP pública, registre su suscripción.  Si registra su suscripción, dispondrá de acceso desde PowerShell o la CLI de Azure 2.0. Para registrase, realice los siguientes pasos:

>[!NOTE]
>El registro de la característica Load Balancer estándar puede tardar hasta una hora en hacerse efectiva globalmente. Si desea usar Load Balancer estándar con las [zonas de disponibilidad](https://aka.ms/availabilityzones), necesitará [un registro independiente](https://aka.ms/availabilityzones) para la versión preliminar de AZ.

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> Durante un breve período de tiempo, el acceso a regiones no incluidas en el lanzamiento inicial (Este de EE. UU. 2, Centro de EE. UU., Europa del Norte, Centro Occidental de EE.UU., Europa Occidental, Sudeste Asiático) requerirá el registro de características de suscripción adicionales (AllowLBPreviewWave2 y AllowLBPreviewWave3).  Los pasos que se indican a continuación se han modificado para habilitar características de suscripción adicionales. Ejecute todas las características, incluso si ya se registró en AllowLBPreview. Este paso dejará de ser un requisito en las próximas semanas.


### <a name="sign-up-by-using-azure-cli-20"></a>Registro mediante la CLI de Azure 2.0

1. Registro de la característica con el proveedor:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. La operación puede tardar hasta 10 minutos en completarse. Puede comprobar el estado de la operación con el siguiente comando:

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    Continúe con el siguiente paso cuando el estado de registro de la característica devuelva el valor "Registrado" para cada una de las características anteriores. Ejemplo:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. Complete el registro de la versión preliminar volviendo a registrar la suscripción con el proveedor de recursos:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>Registro mediante PowerShell

1. Registro de la característica con el proveedor:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. La operación puede tardar hasta 10 minutos en completarse. Puede comprobar el estado de la operación con el siguiente comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  Continúe con el siguiente paso cuando el estado de registro de la característica devuelva el valor "Registrado" para cada una de las características anteriores. Ejemplo:

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Complete el registro de la versión preliminar volviendo a registrar la suscripción con el proveedor de recursos:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Precios

La facturación de la SKU de Load Balancer estándar se basa en las reglas configuradas y en los datos procesados. Durante el periodo de versión preliminar no se le realizará ningún cargo. Para más información, revise las páginas de precios de [Load Balancer](https://aka.ms/lbpreviewpricing) e [IP pública](https://aka.ms/lbpreviewpippricing).

Los clientes seguirán disfrutando de la SKU básica de Load Balancer sin ningún cargo.

## <a name="limitations"></a>Limitaciones

Las siguientes limitaciones se aplican al periodo de versión preliminar y están sujetas a cambios:

- Por el momento, las instancias de servidor back-end de Load Balancer no se pueden encontrar en redes virtuales emparejadas. Todas las instancias de servidor back-end deben estar en la misma región.
- Las SKU no son mutables. No se puede cambiar la SKU de un recurso existente.
- Ambas SKU pueden usarse con una VM independiente, instancias de VM en un conjunto de disponibilidad o un conjunto de escalado de máquina virtual. Las combinaciones de VM pueden no usarse con ambas SKU simultáneamente. No se permite una configuración con una mezcla de SKU.
- Cuando se usa Load Balancer estándar interno con una instancia de máquina virtual (o cualquier parte de un conjunto de disponibilidad) se deshabilitan las [conexiones salientes de SNAT predeterminadas](load-balancer-outbound-connections.md). Puede restablecer estar capacidad para una VM independiente, instancias de VM en un conjunto de disponibilidad o un conjunto de escalado de máquina virtual. También puede restaurar la capacidad de realizar conexiones salientes. Para restaurar estas capacidades, asigne simultáneamente un Load Balancer estándar público o una dirección IP estándar pública como dirección IP pública de nivel de instancia a la misma instancia de VM. Una vez que la asignación se ha completado, se proporciona de nuevo SNAT de enmascaramiento de puertos a una dirección IP pública.
- Puede que sea necesario agrupar las instancias de máquina virtual en conjuntos de disponibilidad para conseguir el escalado completo del grupo de servidores back-end. Es posible colocar hasta 150 conjuntos de disponibilidad y máquinas virtuales independientes en un único grupo de servidores back-end.
- No se admite IPv6.
- En el contexto de zonas de disponibilidad, un servidor front-end no es mutable de zonal a con redundancia de zona y viceversa. Una vez un servidor front-end se crea como redundancia de zona, permanece con redundancia de zona. Una vez un servidor front-end se crea como de zona, permanece como de zona.
- En el contexto de zonas de disponibilidad, una dirección IP pública zonal no se puede mover de una zona a otra.
- [Las alertas de Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-alerts.md) no son compatibles en estos momentos.
- El portal aún no es compatible con las regiones de versión preliminar ampliadas.  Para solucionar el problema, use herramientas cliente, como, por ejemplo, plantillas, la CLI 2.0 de Azure o PowerShell.
- No es posible mover las operaciones de suscripción.


## <a name="next-steps"></a>Pasos siguientes

- Aprenda más sobre [Load Balancer básico](load-balancer-overview.md).
- Aprenda más sobre [zonas de disponibilidad](../availability-zones/az-overview.md).
- Más información sobre los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).
- Aprenda sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave en Azure.
- Obtenga información sobre las [métricas que se utilizan](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) en [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
