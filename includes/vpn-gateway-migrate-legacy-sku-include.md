> [!NOTE]
> * La dirección IP pública de VPN Gateway cambiará al migrar de una SKU antigua a una nueva.
> * No se pueden migrar puertas de enlace de VPN clásicas a las nuevas SKU. Las puertas de enlace de VPN clásicas solo pueden utilizar las SKU heredadas (antiguas).
> 

No se puede cambiar el tamaño de las puertas de enlace de Azure VPN Gateway entre las familias de SKU antiguas y las nuevas. Si tiene puertas de enlace VPN Gateway en el modelo de implementación de Resource Manager que utilizan la versión antigua de la SKU, puede migrar a las SKU nuevas. Para ello, elimine la puerta de enlace VPN Gateway existente en la red virtual y después cree una nueva.

Flujo de trabajo de la migración:

1. Elimine las conexiones a la puerta de enlace de la red virtual.
2. Elimine la puerta de enlace VPN Gateway antigua.
3. Cree la puerta de enlace VPN Gateway nueva.
4. Actualice los dispositivos VPN locales con la nueva dirección IP de la puerta de enlace VPN Gateway (para las conexiones de sitio a sitio).
5. Actualice el valor de la dirección IP de puerta de enlace para las puertas de enlace de red local de red virtual a red virtual que se conectarán a esta puerta de enlace.
6. Descargue los nuevos paquetes de configuración de VPN de cliente para los clientes de P2S que se vayan a conectar a la red virtual a través de esta puerta de enlace VPN Gateway.
7. Vuelva a crear las conexiones a la puerta de enlace de la red virtual.