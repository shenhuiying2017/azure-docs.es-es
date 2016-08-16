El emparejamiento de VNET es un mecanismo para conectar dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecerán como una sola a efectos de conectividad. Lea la [información general sobre el emparejamiento de VNET](../articles/virtual-network/virtual-network-peering-overview.md) si no está familiarizado con este mecanismo.

El emparejamiento de VNET se encuentra en versión preliminar pública; para poder usarlo, es preciso registrarse mediante los siguientes comandos:

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!---HONumber=AcomDC_0810_2016-->