|    | **Rendimiento de puerta de enlace VPN (1)** | **Túneles IPsec máx. de puerta de enlace VPN (2)** | **Rendimiento de puerta de enlace de ExpressRoute** | **Puerta de enlace de VPN y ExpressRoute coexisten**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU básica (3)**              |  100 Mbps | 10                         |  500 Mbps                           | No   |
| **SKU estándar (4)**           |  100 Mbps | 10                         | 1000 Mbps                           | Sí  |
| **SKU de alto rendimiento (4)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Sí  |

- (1) El rendimiento de la VPN es una estimación aproximada basada en las medidas entre redes virtuales en la misma región de Azure. No es un rendimiento garantizado para las conexiones entre locales a través de Internet. Es el valor máximo posible del rendimiento.
- (2) El número de túneles hace referencia a VPN basadas en enrutamiento. Una VPN basada en directivas solo puede admitir un túnel VPN de sitio a sitio.
- (3) BGP no es compatible con la SKU básica.
- (4) Las VPN basadas en directivas no son compatibles con esta SKU. Solo son compatibles con la SKU básica.

<!--HONumber=Oct16_HO2-->


