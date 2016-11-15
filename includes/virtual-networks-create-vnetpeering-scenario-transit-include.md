## <a name="service-chaining-transit-through-peered-vnet"></a>Encadenamiento de servicios: tránsito a través de redes virtuales emparejadas
Aunque el uso de rutas del sistema facilita el tráfico automáticamente para su implementación, hay casos en los que seguramente quiera controlar el enrutamiento de paquetes a través de una aplicación virtual.
En este escenario, hay dos redes virtuales en una suscripción, HubVNet y VNet1, tal y como se describe en el siguiente diagrama. Implementará una aplicación virtual de red (NVA) en la red virtual HubVNet. Después de establecer el emparejamiento de VNET entre HubVNet y VNet1, puede configurar rutas definidas por el usuario y especificar el siguiente salto a NVA en la red virtual HubVNet.

![Tránsito de NVA](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [!NOTE]
> Por motivos de simplicidad, se supone que todas las redes virtuales están en la misma suscripción. Sin embargo, también se puede aplicar a un escenario entre suscripciones.
> 
> 

La propiedad principal para habilitar el enrutamiento de tránsito es el parámetro "Permitir tráfico reenviado". Este parámetro permite aceptar y enviar tráfico entre la NVA y la red virtual emparejada.  



<!--HONumber=Nov16_HO2-->


