Azure ofrece las siguientes SKU de VPN Gateway:

|**SKU**   | **Túneles<br>S2S/entre redes virtuales** | **Conexiones<br>P2S** | **Rendimiento<br>agregado** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Máx. 30                         | Máx. 128               | 500 Mbps                    |
|**VpnGw2**| Máx. 30                         | Máx. 128               | 1 Gbps                      |
|**VpnGw3**| Máx. 30                         | Máx. 128               | 1,25 Gbps                   |
|**Básica** | Máx. 10                         | Máx. 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- El rendimiento se basa en las mediciones de varios túneles agregados a través de una sola puerta de enlace. No es un rendimiento garantizado debido a las condiciones del tráfico de Internet y a los comportamientos de las aplicaciones.

- Puede encontrar más información sobre los precios en la página de [precios](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- La información del SLA (Acuerdo de Nivel de Servicio) puede encontrarse en la página [SLA](https://azure.microsoft.com/en-us/support/legal/sla/vpn-gateway/).