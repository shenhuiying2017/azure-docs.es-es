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
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 0ed8d3432a988c468260589cfe12090529c403d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Introducción a Azure Load Balancer Standard (versión preliminar)

La SKU estándar de Azure Load Balancer y la SKU estándar de IP pública juntas permiten crear arquitecturas de gran escalabilidad y confiabilidad.  Las aplicaciones que usan Load Balancer estándar pueden aprovechar las nuevas funcionalidades, además de una baja latencia, un alto rendimiento y el escalado de millones de flujos de todas las aplicaciones TCP y UDP.

>[!NOTE]
> La SKU estándar de Load Balancer se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use la [SKU básica de Load Balancer](load-balancer-overview.md) disponible con carácter general con sus servicios de producción.  Las características asociadas con esta versión preliminar, las [Zonas de disponibilidad](https://aka.ms/availabilityzones) y los [Puertos HA](https://aka.ms/haports), requieren un inicio de sesión independiente en este momento. Siga las instrucciones correspondientes para el registro adicional en Load Balancer [estándar (versión preliminar)](#preview-sign-up).

## <a name="why-use-load-balancer-standard"></a>¿Por qué usar Load Balancer estándar?

Use Load Balancer estándar en la gama completa de centros de datos virtuales, desde implementaciones a pequeña escala hasta arquitecturas de varias zonas grandes y complejas, para aprovechar sus siguientes funcionalidades:

- Se puede conseguir una [escala empresarial](#enterprisescale) con Load Balancer estándar.  Posibilidad de usarse con cualquier instancia de máquina virtual de una red virtual y hasta 1000 instancias de máquina virtual.

- Hay [nueva información de diagnóstico](#diagnosticinsights) disponible para que pueda entender, administrar y solucionar problemas de este componente esencial de su centro de datos virtual. Use Azure Monitor (versión preliminar) para mostrar, filtrar y agrupar las nuevas métricas multidimensionales para mediciones continuas del estado de la ruta de acceso a los datos desde el front-end hasta la máquina virtual, por sondeos de estado de punto de conexión, intentos de conexión TCP y conexiones salientes.

- Los [grupos de seguridad de red](#nsg) son ahora necesarios para cualquier instancia de máquina virtual asociada a las SKU estándar de Load Balancer o IP pública y proporcionan seguridad mejorada.

- Los [puertos HA proporcionan alta confiabilidad](#highreliability) y escalado para aplicaciones virtuales de red y otros escenarios de aplicación. Los puertos HA equilibran la carga en todos los puertos entre un front-end de Load Balancer interno y un grupo de instancias de máquina virtual.

- Las [conexiones salientes](#outboundconnections) usan ahora un nuevo modelo de asignación de puertos de SNAT que proporciona mayor resistencia y escalabilidad.

- [Load Balancer estándar con zonas de disponibilidad](#availabilityzones) se puede usar para construir arquitecturas zonales y con redundancia de zona, ambas con equilibrio de carga entre zonas. Puede lograr redundancia de zona sin dependencia de registros de DNS: simplemente una única dirección IP tiene redundancia de zona de forma predeterminada y puede llegar a cualquier máquina virtual de una red virtual de una región en todas las zonas de disponibilidad.


Puede usar Load Balancer estándar en una configuración pública o interna que siga admitiendo los siguientes escenarios fundamentales:

- equilibrio de carga del tráfico entrante entre instancias de back-end con un estado correcto.
- reenvío de puerto del tráfico entrante a una única instancia de back-end.
- conversión del tráfico saliente de una dirección IP privada de la red virtual a una dirección IP pública.

### <a name = "enterprisescale"></a>Escala empresarial

 Use Load Balancer estándar en el diseño de su centro de datos virtual de alto rendimiento. Puede usar instancias de máquina virtual independientes o conjuntos de escalado de hasta 1000 instancias de máquina virtual en un grupo de back-end, y obtener compatibilidad con cualquier aplicación de TCP o UDP. Con Load Balancer estándar, la aplicación puede seguir aprovechando las ventajas de latencia baja de reenvío, alto rendimiento y escalado a millones de flujos en un servicio de Azure completamente administrado.
 
Load Balancer estándar puede reenviar el tráfico a cualquier instancia de máquina virtual de una red virtual de una región. Los tamaños de grupo de back-end pueden tener hasta 1000 instancias con cualquier combinación de:

- máquinas virtuales independiente sin conjuntos de disponibilidad
- máquinas virtuales independientes con conjuntos de disponibilidad
- conjuntos de escalado de máquinas virtuales (conjunto de escalado de máquinas virtuales) con hasta 1000 instancias
- varios conjuntos de escalado de máquinas virtuales
- mezcla de máquinas virtuales y conjuntos de escalado de máquinas virtuales.

Ya no existe el requisito de tener que usar conjuntos de disponibilidad, pero puede seguir haciéndolo para beneficiarse de otras formas.

### <a name = "diagnosticinsights"></a>Información de diagnóstico

Load Balancer estándar proporciona nuevas funcionalidades de diagnóstico multidimensionales para configuraciones de Load Balancer público e interno. Estas nuevas métricas se proporcionan a través de Azure Monitor (versión preliminar) y usan todas las funcionalidades relacionadas, como la posibilidad de integración con varios consumidores de nivel inferior.

| Métrica | Descripción |
| --- | --- |
| Disponibilidad de VIP | Load Balancer estándar usa continuamente la ruta de acceso a los datos desde una región hasta el front-end de Load Balancer y, finalmente, hasta la pila de SDN que respalda la máquina virtual. Siempre que permanezcan las instancias correctas, la medida sigue la misma ruta de acceso que el tráfico con equilibrio de carga de las aplicaciones, y valida la ruta de acceso a los datos que podrían estar usando los clientes. La medida es invisible para la aplicación y no interfiere.|
| Disponibilidad de DIP | Load Balancer estándar usa un servicio de sondeo de estado distribuido que supervisa el estado del punto de conexión de la aplicación de acuerdo a lo que ha configurado.  Esta métrica proporciona una vista agregada o filtrada por punto de conexión de cada punto de conexión de instancia individual del grupo de Load Balancer.  Puede ver cómo Load Balancer observa el estado de su aplicación según se indica en la configuración de sondeo de estado.
| Paquetes SYN | Load Balancer estándar no termina las conexiones TCP ni interactúa con los flujos de paquetes TCP o UDP; los flujos y sus protocolos de enlace siempre son entre el origen y la instancia de máquina virtual. Para solucionar mejor todos los escenarios de protocolo TCP, puede hacer uso de esta métrica para comprender el número de intentos de conexión TCP realizados. Esta métrica informa del número de paquetes TCP SYN que se han recibido y puede reflejar los clientes que intentan establecer conexiones a su servicio.|
| Conexiones SNAT | Load Balancer estándar informa del número de conexiones salientes enmascaradas al front-end de dirección IP pública.  Los puertos SNAT son un recurso agotable y esta métrica puede proporcionar una indicación de la dependencia que su aplicación tiene de SNAT en las conexiones salientes originadas.|
| Contadores de bytes | Load Balancer estándar informa de los datos procesados por front-end.|
| Contadores de paquetes | Load Balancer estándar informa de los paquetes procesados por front-end.|

### <a name = "highreliability"></a>Alta confiabilidad

Configure reglas de equilibrio de carga para asegurarse de que su aplicación se escala y es enormemente confiable.  Puede configurar reglas para puertos individuales o puede usar nuevos puertos HA para equilibrar todo el tráfico con independencia del número de puerto TCP o UDP.  

Puede usar la nueva característica Puertos HA para desbloquear diversos escenarios, como alta disponibilidad y escalabilidad para aplicaciones virtuales de red internas y otros casos en los que especificar puertos individuales resulta poco práctico o inapropiado. Los puertos HA proporcionan redundancia y escalado al permitir todas las instancias que necesita, en lugar de estar limitado a escenarios activos o pasivos. Las configuraciones de sondeo de estado protegen el servicio dado que el tráfico solo se reenvía a instancias con un estado correcto.

Los proveedores de aplicaciones virtuales de red pueden proporcionar escenarios resistentes y completamente compatibles con el proveedor al eliminar un único punto de error para los clientes y permitir el escalado de varias instancias activas. Se pueden escalar hasta dos o más instancias si su dispositivo permite tales configuraciones. El proveedor de aplicaciones virtuales de red debe proporcionar instrucciones adicionales en estos casos.

### <a name = "availabilityzones"></a>Zonas de disponibilidad

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Aumente la resistencia de la aplicación con el uso de zonas de disponibilidad en regiones admitidas. Las zonas de disponibilidad se encuentran actualmente en versión preliminar en regiones específicas y requieren alguna suscripción adicional.

### <a name="automatic-zone-redundancy"></a>Redundancia de zona automática

Puede elegir si Load Balancer debe proporcionar un front-end con redundancia de zona o zonal para cada una de las aplicaciones.  Con Load Balancer estándar, es fácil crear redundancia de zona.  Una única dirección IP de front-end tiene automáticamente redundancia de zona.  Todas las zonas de disponibilidad de una región atienden a la vez a un front-end con redundancia de zona. Como consecuencia, se crea una ruta de acceso a datos con redundancia de zona para las conexiones entrantes y salientes. La redundancia de zona en Azure no requiere varias direcciones IP y registros DNS. 

Esta redundancia de zona está disponible para servidores front-end públicos o internos. La dirección IP pública, así como la IP privada del front-end de Load Balancer pueden tener redundancia de zona.

Cree una dirección IP pública con redundancia de zona con lo siguiente (agregue "sku" a cualquier plantilla de Resource Manager existente):

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

Cree una configuración de IP de front-end con redundancia de zona de Load Balancer con lo siguiente (agregue "sku" a cualquier plantilla de Resource Manager existente):

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

Si el front-end de IP pública tiene redundancia de zona, las conexiones salientes realizadas desde instancias de máquina virtual tendrán automáticamente redundancia de zona y estarán protegidas de los errores de zona.  La asignación del puerto SNAT sobrevive a los errores de zona.

#### <a name="cross-zone-load-balancing"></a>Equilibrio de carga entre zonas

El equilibrio de carga entre zonas está disponible dentro de una región del grupo de back-end, lo que permite máxima flexibilidad en las instancias de máquina virtual.  Un front-end puede proporcionar flujos a cualquier máquina virtual de la red virtual con independencia de la zona de disponibilidad de la instancia de máquina virtual.

Además, puede elegir también especificar una zona determinada para sus instancias de front-end y back-end a fin de alinear la ruta de acceso a los datos y los recursos con una zona específica.

Dado que las redes virtuales y subredes no tienen nunca limitación de zona, todo lo que debe hacer es definir un grupo de back-end con las instancias de máquina virtual deseadas y ya estará lista la configuración.

#### <a name="zonal-deployments"></a>Implementaciones zonal

Opcionalmente, puede alinear también el front-end con una zona específica mediante la definición de un front-end zonal.  La zona de disponibilidad única designada atiende únicamente al front-end zonal y, cuando se combina con instancias de máquina virtual zonales, se pueden alinear recursos a zonas específicas.

Una dirección IP pública que se cree en una zona específica siempre existirá solo en esa zona.  No es posible cambiar la zona de una dirección IP pública.  Si desea contar con una dirección IP pública que se pueda adjuntar a recursos en varias zonas, lo que debe crear es una dirección IP pública con redundancia de zona.

Cree una dirección IP pública zonal en la zona de disponibilidad 1 con lo siguiente (agregue "zones" y "sku" a las plantillas de Resource Manager existentes):

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

Cree un front-end de Load Balancer interno en la zona de disponibilidad 1 con lo siguiente (agregue "sku" a las plantillas de Resource Manager existentes y coloque "zones" en el recurso secundario de configuración de la IP de front-end):

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

Además, puede usar el equilibrio de carga entre zonas con su back-end; para ello, coloque sus instancias de máquina virtual de una red virtual en un grupo de back-end.

El propio recurso Load Balancer estándar siempre es regional y tiene redundancia de zona donde se admiten zonas de disponibilidad. Una dirección IP pública o un front-end de Load Balancer interno que no se ha asignado a una zona, se puede implementar en cualquier región con independencia de la compatibilidad con las zonas de disponibilidad. Si una región obtiene zonas de disponibilidad más adelante, la dirección IP pública ya implementada o el front-end de Load Balancer interno pasan a tener redundancia de zona automáticamente. La ruta de acceso a datos con redundancia de zona no implica que no se produzcan pérdidas de paquetes.

### <a name = "nsg"></a>Grupos de seguridad de red

Load Balancer estándar e IP pública estándar se incorporan completamente a la red virtual y ahora se requieren grupos de seguridad de red (NSG). NSG permite incluir en la lista de permitidos los flujos y permite configuraciones en las que los clientes tienen el control completo sobre el paso del tráfico a su implementación mediante NSG, sin necesidad de esperar a que finalicen otras configuraciones.

Asocie un NSG con subredes o tarjetas NIC de instancias de máquina virtual del grupo de back-end.  Esto se aplica a Load Balancer estándar e IP pública estándar cuando se usa como una IP pública a nivel de instancia. Debe incluir explícitamente en la lista de permitidos el flujo de tráfico que quiere permitir con el NSG.

Para aprender más sobre los grupos de seguridad de red y cómo aplicarlos en su caso, consulte [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Conexiones salientes

 Load Balancer estándar proporciona conexiones salientes para máquinas virtuales dentro de la red virtual cuando se asocia con un equilibrio de carga que usa la traducción de direcciones de red de origen (SNAT) de enmascaramiento de puertos.

Cuando un recurso público Load Balancer público está asociado con instancias de máquina virtual, el origen de cada conexión saliente se reescribe desde el espacio de la dirección IP privada de la red virtual hasta la dirección IP pública del front-end de Load Balancer.  Load Balancer estándar usa un nuevo algoritmo de traducción de direcciones de red de origen (SNAT) de enmascaramiento de puertos para una mayor robustez y escalabilidad.  

Además, cuando se usa con un front-end con redundancia de zona, las conexiones salientes también tienen redundancia de zona y las asignaciones de puerto SNAT sobreviven a los errores de zona.

El nuevo algoritmo de Load Balancer estándar asigna previamente puertos SNAT a la interfaz de red de cada máquina virtual en el momento en que se agregan al grupo según los siguientes niveles:

| Tamaño de grupo de back-end | Puertos SNAT asignados previamente |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Los puertos SNAT no se convierten directamente al número de conexiones. Un puerto SNAT se puede reutilizar con varios destinos únicos.  Revise el artículo sobre [conexiones salientes](load-balancer-outbound-connections.md) para más información.

Si aumenta el grupo de back-end y pasa de un nivel de tamaño al siguiente tamaño más grande, la mitad de los puertos asignados se reclamarán. Toda conexión asociada con un puerto reclamado agotará el tiempo de espera y será necesario restablecerla. Los nuevos intentos de conexión tendrán éxito inmediatamente. Si se reduce el grupo de back-end de un nivel de tamaño al siguiente tamaño más pequeño, los puertos SNAT disponibles aumentan y las conexiones existentes no resultan afectadas.

Load Balancer estándar también tiene una opción de configuración adicional basada en reglas para proporcionar a los clientes control sobre qué front-end usar para SNAT de enmascaramiento de puertos cuando están disponibles varios servidores front-end.

Por último, cuando Load Balancer estándar solo atiende instancias de máquina virtual, las conexiones SNAT salientes no están disponibles. Esta capacidad se puede restaurar explícitamente asignando también las instancias de máquina virtual a una instancia de Load Balancer público o asignando las IP públicas como una IP pública a nivel de instancia a cada instancia de máquina virtual. Esta operación puede resultar necesaria con algunos sistemas operativos o aplicaciones. 

### <a name="port-forwarding"></a>Enrutamiento de puerto

 Los equilibradores de carga básicos y estándar ofrecen la posibilidad de configurar reglas NAT entrantes para asignar un puerto de front-end a una instancia de back-end individual.  Esta capacidad tiene muchos usos, como la exposición de puntos de conexión del Protocolo de escritorio remoto, los puntos de conexión SSH u otros diversos escenarios de aplicación.

Load Balancer estándar sigue proporcionando la funcionalidad de reenvío de puertos mediante reglas NAT entrantes.  Cuando se usa con servidores front-end con redundancia de zona, las reglas NAT entrantes pasan a tener redundancia de zona y sobreviven a los errores de zona.

### <a name="multiple-frontends"></a>Varios servidores front-end

Configure varios servidores front-end para un diseño flexible donde las aplicaciones deban exponer varias direcciones IP (como sitios web TLS o puntos de conexión de grupo de disponibilidad SQL AlwaysOn).  Se pueden encontrar más detalles [aquí](load-balancer-multivip-overview.md).

Load Balancer estándar continúa proporcionando varios servidores front-end cuando es necesario exponer un punto de conexión de la aplicación específico en una única dirección IP.

 Se pueden encontrar más detalles [aquí](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Acerca de las SKU

Las SKU solo están disponibles en el modelo de implementación de Azure Resource Manager.  Esta versión preliminar presenta dos SKU (básica y estándar) para los recursos Load Balancer e IP pública.  Las SKU difieren en cuanto a sus capacidades, características de rendimiento, limitaciones y algunos comportamientos intrínsecos. Las máquinas virtuales se pueden usar con cualquier SKU. En los recursos Load Balancer e IP pública, las SKU son atributos opcionales y, si se omiten, adoptan el modo Básico de forma predeterminada.

>[!IMPORTANT]
>La SKU de un recurso no es mutable.  No se puede cambiar la SKU de un recurso existente.  

### <a name="load-balancer"></a>Load Balancer

El [recurso existente Load Balancer](load-balancer-overview.md) se convierte en la SKU básica y permanece disponible con carácter general y sin cambios.

La SKU estándar de Load Balancer es una nueva oferta actualmente en versión preliminar. La versión de API 2017-08-01 para Microsoft.Network/loadBalancers introduce las SKU en el recurso.

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
Cuando se usa en una región que también ofrece zonas de disponibilidad, Load Balancer estándar es automáticamente resistente al uso de zonas a no ser que se haya declarado zonal.

### <a name="public-ip"></a>Dirección IP pública

El [recurso existente IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) se convierte en la SKU básica y permanece disponible con carácter general con todas sus capacidades, características de rendimiento y limitaciones.

La SKU estándar de IP pública es una nueva oferta y se encuentra actualmente en versión preliminar. La versión de API 2017-08-01 para los recursos Microsoft.Network/publicIPAddresses introduce las SKU.

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

A diferencia de la SKU básica de IP pública que ofrece varios métodos de asignación, en la SKU estándar de IP pública la asignación es siempre estática.

Cuando se usa en una región que también ofrece zonas de disponibilidad, la SKU estándar de IP pública es automáticamente resistente al uso de zonas a menos que se haya declarado zonal.  No es posible cambiar una IP pública zonal de una zona a otra.

## <a name="migration-between-skus"></a>Migración entre SKU

Las SKU no son mutables.  Si desea moverse de una SKU de recurso a otra, siga estos pasos:

### <a name="migrating-from-basic-to-standard-sku"></a>Migración de una SKU básica a una estándar

1. Cree un nuevo recurso estándar (Load Balancer y direcciones IP públicas cuando sea necesario) y vuelva a crear sus reglas y definiciones de sondeo.
2. Quite recursos de SKU básica (IP pública y LB) de todas las instancias de máquina virtual (esto incluye todas las instancias de un conjunto de disponibilidad).
3. Asocie todas las instancias de máquina virtual a los nuevos recursos de SKU estándar.

### <a name="migrating-from-standard-to-basic-sku"></a>Migración de SKU estándar a básica:

1. Cree un nuevo recurso básico (Load Balancer y direcciones IP públicas cuando sea necesario) y vuelva a crear sus reglas y definiciones de sondeo. 
2. Quite recursos de SKU estándar (IP pública y LB) de todas las instancias de máquina virtual (esto incluye todas las instancias de un conjunto de disponibilidad).
3. Asocie todas las instancias de máquina virtual a los nuevos recursos de SKU básica.

  >[!NOTE]
  >Los puertos HA y el diagnóstico de la SKU estándar solo están disponibles en la SKU estándar. No se puede migrar de estándar a básica y conservar esta funcionalidad.

En los recursos Load Balancer e IP pública se deben usar SKU coincidentes.  No es posible mezclar recursos de SKU básica y estándar ni asociar una máquina virtual, las máquinas virtuales de un conjunto de disponibilidad o un conjunto de escalado de máquinas virtuales a ambas a la vez.

## <a name="region-availability"></a>Disponibilidad en regiones

Load Balancer estándar está disponible actualmente en estas regiones:
- Este de EE. UU. 2
- Central EE. UU.:
- Europa del Norte
- Centro occidental de EE.UU.
- Europa occidental
- Sudeste asiático

## <a name="service-limits--abilities-comparison"></a>Comparación de límites y capacidades de servicio

Los [límites de servicio de red](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits) se aplican por región y suscripción. 

En la tabla siguiente se proporciona una comparación de límites y capacidades entre las SKU estándar y básica de Load Balancer:

| Load Balancer | Básica | Estándar |
| --- | --- | --- |
| Tamaño de grupo de back-end | hasta 100 | hasta 1000 |
| Límite de grupo de back-end | Conjunto de disponibilidad | Red virtual, región |
| Diseño de grupo de back-end | Máquinas virtuales de un conjunto de disponibilidad o de un conjunto de escalado de máquinas virtuales de un conjunto de disponibilidad | Cualquier instancia de máquina virtual de la red virtual |
| Puertos HA | No compatible | Disponible |
| Diagnóstico | Limitado, solo público | Disponible |
| Disponibilidad de VIP  | No compatible | Disponible |
| Movilidad rápida de IP | No compatible | Disponible |
|Escenarios de zonas de disponibilidad | Solo zonal | Zonal, redundancia de zona, equilibrio de carga entre zonas |
| Algoritmo de SNAT saliente | A petición | Asignado previamente |
| Selección de front-end de SNAT saliente | No se puede configurar, varios candidatos | Configuración opcional para reducir los candidatos |
| Grupo de seguridad de red (NSG) | Opcional en la NIC/subred | Obligatorio |

En la tabla siguiente se proporciona una comparación de los límites y las capacidades entre las SKU básica y estándar para IP pública:

| Dirección IP pública | Básica | Estándar |
| --- | --- | --- |
| Escenarios de zonas de disponibilidad | Solo zonal | Con redundancia de zona (predeterminado), zonal (opcional) | 
| Movilidad rápida de IP | No compatible | Disponible |
| Disponibilidad de VIP | No compatible | Disponible |
| Counters | No compatible | Disponible |
| Grupo de seguridad de red (NSG) | Opcional en la NIC | Obligatorio |


## <a name="preview-sign-up"></a>Registro en versión preliminar

Para participar en la versión preliminar de la SKU estándar de Load Balancer y su SKU estándar complementaria de IP pública, registre su suscripción para obtener acceso mediante PowerShell o la CLI de Azure 2.0.

- Registro con PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- Registro con la CLI de Azure 2.0

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>El registro de la característica Estándar de Load Balancer puede tardar hasta una hora.

>[!NOTE]
>Si desea usar Load Balancer estándar con [Zonas de disponibilidad](https://aka.ms/availabilityzones) y [Puertos HA](https://aka.ms/haports), se requiere un registro independiente para estas versiones preliminares.  Siga las instrucciones correspondientes.

## <a name="pricing"></a>Precios

La SKU estándar de Load Balancer se factura en función de las reglas configuradas y los datos procesados.  Durante el periodo de versión preliminar no se le realizará ningún cargo.  Revise las páginas de precios de [Load Balancer](https://aka.ms/lbpreviewpricing) e [IP pública](https://aka.ms/lbpreviewpippricing) para más información.

Los clientes seguirán disfrutando de la SKU básica de Load Balancer sin ningún cargo.

## <a name="limitations"></a>Limitaciones

Las siguientes limitaciones se aplican al periodo de versión preliminar y están sujetas a cambios:

- Por el momento, las instancias de back-end de Load Balancer no se pueden encontrar en redes virtuales emparejadas. Todas las instancias de back-end deben estar en la misma región.
- Las SKU no son mutables. No se puede cambiar la SKU de un recurso existente.
- Puede usar SKU básica o estándar con una máquina virtual independiente, todas las instancias de máquina virtual de un conjunto de disponibilidad o el conjunto de escalado de máquinas virtuales, pero no las dos a la vez. No se permite la combinación de SKU.
- Cuando se usa Load Balancer estándar interno con una instancia de máquina virtual (o cualquier parte de un conjunto de disponibilidad) se deshabilitan las [conexiones salientes de SNAT predeterminadas](load-balancer-outbound-connections.md).  Puede restaurar esta capacidad en una máquina virtual independiente, un conjunto de disponibilidad de instancias de máquina virtual o un conjunto de escalado de máquinas virtuales y realizar conexiones salientes mediante la asignación simultánea de un recurso público Load Balancer estándar o IP pública estándar como IP pública a nivel de instancia a la misma instancia de máquina virtual. Una vez realizada, se proporciona de nuevo SNAT de enmascaramiento de puertos a una dirección IP pública.
- Puede que sea necesario agrupar las instancias de máquina virtual en conjuntos de disponibilidad para conseguir el escalado completo del grupo de back-end. Es posible colocar hasta 150 conjuntos de disponibilidad y máquinas virtuales independientes en un único grupo de back-end.
- No se admite IPv6.
- En el contexto de zonas de disponibilidad, un front-end no es mutable de zonal a con redundancia de zona y viceversa. Una vez creado como con redundancia de zona, permanecerá así. Una vez creado como zonal, permanecerá así.
- En el contexto de zonas de disponibilidad, una dirección IP pública zonal no se puede mover de una zona a otra.


## <a name="next-steps"></a>Pasos siguientes

- Aprenda más sobre [Load Balancer básico](load-balancer-overview.md).
- Aprenda más sobre [zonas de disponibilidad](../availability-zones/az-overview.md).
- Información acerca de las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.

