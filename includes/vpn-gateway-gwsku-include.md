Al crear una puerta de enlace de red virtual, debe especificar la SKU de la puerta de enlace que desea usar. Seleccione las SKU que cumplan sus requisitos en función de los tipos de cargas de trabajo, rendimientos, características y Acuerdos de Nivel de Servicio.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Cargas de trabajo  *de producción* frente a cargas de desarrollo y pruebas

Dadas las diferencias en los Acuerdos de Nivel de Servicio y los conjuntos de características, se recomiendan las siguientes SKU para la producción *frente a* desarrollo y pruebas:

| **Carga de trabajo**                       | **SKU**               |
| ---                                | ---                    |
| **Cargas de trabajo de producción, críticas** | VpnGw1, VpnGw2, VpnGw3 |
| **Desarrollo y pruebas o prueba de concepto**   | Básica                  |
|                                    |                        |

Si continúa utilizando las SKU antiguas, las recomendaciones de SKU de producción son las SKU Estándar y HighPerformance. Para información acerca de las SKU antiguas, consulte [SKU de puerta de enlace (SKU heredadas)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).

###  <a name="feature"></a>Conjuntos de características de las SKU de puerta de enlace

Las nueva SKU de puerta de enlace simplifican los conjuntos de características que se ofrecen en las puertas de enlace:

| **SKU**| **Características**|
| ---    | ---         |
|**Básico**   | **VPN basada en ruta**: 10 túneles con P2S, sin autenticación RADIUS, sin P2S, sin IKEv2 para P2S<br>**VPN basada en directivas**: (IKEv1): 1 túnel; sin P2S|
| **VpnGw1, VpnGw2 y VpnGw3** | **VPN basada en enrutamiento**: hasta 30 túneles ( * ), P2S, BGP, activo-activo, directiva de IPsec/IKE personalizada, coexistencia de VPN y ExpressRoute |
|        |             |

( * ) Se puede configurar "PolicyBasedTrafficSelectors" para conectar una puerta de enlace de VPN Gateway basada en ruta (VpnGw1, VpnGw2, VpnGw3) a varios dispositivos de firewall locales basados en directivas. Consulte [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Conexión de puertas de enlace VPN Gateway a varios dispositivos de VPN locales basados en directivas con PowerShell) para más información.

###  <a name="resize"></a>Cambio de tamaño de las SKU de puerta de enlace

1. Puede cambiar el tamaño entre las SKU de VpnGw1, VpnGw2 y VpnGw3.
2. Si trabaja con las SKU de puerta de enlace antiguas, puede cambiar el tamaño entre las SKU Básica, Estándar y HighPerformance.
2. Sin embargo **no puede** cambiar el tamaño de las SKU de Básica/Estándar/HighPerformance a las nuevas SKU de VpnGw1/VpnGw2/VpnGw3. En su lugar, debe [migrar](#migrate) a las SKU nuevas.

###  <a name="migrate"></a>Migración de las SKU antiguas a las nuevas

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
