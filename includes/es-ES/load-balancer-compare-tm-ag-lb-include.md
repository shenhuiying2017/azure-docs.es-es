## <a name="load-balancer-differences"></a>Diferencias del equilibrador de carga

Existen diversas opciones para distribuir el tráfico de red con Microsoft Azure. Estas opciones funcionan de manera diferente, ya que disponen de un conjunto de características distintas y son compatibles con escenarios distintos. Cada una se puede usar por separado, pero también se pueden combinar.

* **Azure Load Balancer** actúa como capa de transporte (nivel 4 en la pila de referencia de red de OSI). Proporciona distribución del tráfico en el nivel de red entre las instancias de una aplicación que se ejecutan en el mismo centro de datos de Azure.
* **Application Gateway** funciona en el nivel de aplicación (nivel 7 en la pila de referencia de red de OSI). Actúa como un servicio de proxy inverso, que termina la conexión de cliente y reenvía las solicitudes de vuelta a los puntos de conexión de back-end.
* **Traffic Manager** funciona en el nivel de DNS.  Usa las respuestas DNS para dirigir el tráfico de usuario final a los puntos de conexión distribuidos globalmente. A continuación, los clientes se conectan a esos puntos de conexión directamente.

En la tabla siguiente se resumen las características ofrecidas por cada servicio:

| Servicio | Azure Load Balancer | Application Gateway | Traffic Manager |
| --- | --- | --- | --- |
| Technology |Nivel de transporte (nivel 4) |Nivel de aplicación (nivel 7) |Nivel de DNS |
| Protocolos de aplicación admitidos |Cualquiera |HTTP, HTTPS y WebSockets |Cualquiera (es necesario un punto de conexión HTTP para la supervisión del punto de conexión) |
| Extremos |Instancias de rol de máquinas virtuales de Azure y servicios en la nube |Cualquier dirección IP interna de Azure, dirección IP de Internet pública, máquina virtual de Azure, o servicio en la nube de Azure |Azure Virtual Machines, Cloud Services, Azure Web Apps y puntos de conexión externos |
| Compatibilidad de redes virtuales |Puede usarse para aplicaciones accesibles desde Internet e internas (red virtual) |Puede usarse para aplicaciones accesibles desde Internet e internas (red virtual) |Solo es compatible con aplicaciones accesibles desde Internet |
| Supervisión de puntos de conexión |Se admite a través de sondeos |Se admite a través de sondeos |Se admite a través de HTTP/HTTPS GET |

Azure Load Balancer o Application Gateway enrutan el tráfico de red a los puntos de conexión, pero los escenarios de uso difieren en cuanto al tráfico que pueden administrar. La tabla siguiente le ayuda a entender la diferencia entre los dos equilibradores de carga:

| Tipo | Azure Load Balancer | Application Gateway |
| --- | --- | --- |
| Protocolos |UDP/TCP |HTTP, HTTPS y WebSockets |
| Reserva de IP |Compatible |No compatible |
| Modo de equilibrio de carga |5 tuplas (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo) |Round Robin<br>Enrutamiento basado en URL |
| Modo de equilibrio de carga (IP de origen / sesiones temporales) |2 tuplas (IP de origen e IP de destino), 3 tuplas (IP de origen, IP de destino y puerto). Se puede escalar o reducir verticalmente según el número de máquinas virtuales |Afinidad basada en cookies<br>Enrutamiento basado en URL |
| Sondeos de estado |Intervalo de sondeo predeterminado: 15 segundos. Se excluye de la rotación tras dos errores continuos. Admite sondeos definidos por el usuario |Intervalo de sondeo inactivo: 30 segundos. Se excluye tras cinco errores consecutivos de tráfico real o tras un solo error de sondeo en modo inactivo. Admite sondeos definidos por el usuario |
| Descarga de SSL |No compatible |Compatible |
| Enrutamiento basado en dirección URL | No compatible | Compatible|
| Directiva SSL | No compatible | Compatible|
