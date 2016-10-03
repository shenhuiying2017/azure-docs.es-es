##Configuración de TCP para máquinas virtuales de Azure

Las máquinas virtuales de Azure se comunican con la red pública de Internet mediante [NAT][nat] \(traducción de direcciones de red). Los dispositivos NAT asignan un puerto y una dirección IP pública a una máquina virtual de Azure, lo que le permite establecer un socket para comunicarse con otros dispositivos. Si, transcurrido un tiempo específico, dejan de pasar paquetes por ese socket, el dispositivo NAT elimina la asignación y el socket estará disponible para que lo usen otras máquinas virtuales.

Se trata de un comportamiento común de NAT que puede causar problemas de comunicación en aplicaciones basadas en TCP que esperan un socket que se mantenga más allá de un período de tiempo de espera. Hay dos opciones de tiempo de espera de inactividad que hay que tener en cuenta en las sesiones que tienen el estado de *conexión establecida*:

- **entrante** a través de [Azure Load Balancer][azure-lb-timeout]. Este tiempo de espera predeterminado es de 4 minutos, pero se puede ajustar hasta 30 minutos.
- **saliente** con [SNAT][snat] \(NAT de origen). Este tiempo de espera está establecido en 4 minutos y no se puede ajustar.

Para garantizar que no se pierden conexiones más allá del límite de tiempo de espera, debe asegurarse de que su aplicación mantiene la sesión activa, o bien configurar el sistema operativo subyacente para ello. Las opciones de configuración que se usarán son diferentes en los sistemas Linux y Windows, tal y como se muestra aquí.

En [Linux][linux], debe cambiar estas variables de kernel: net.ipv4.tcp\_keepalive\_time = 120 net.ipv4.tcp\_keepalive\_intvl = 30 net.ipv4.tcp\_keepalive\_probes = 8
 
En [Windows][windows], debe cambiar los siguientes valores del Registro. KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


Con estas configuraciones se garantiza que un paquete que se mantiene activo se envía transcurridos 2 minutos (120 segundos) de tiempo de inactividad y, a partir de ahí, se envía cada 30 segundos. Y, si se produce un error en 8 de esos paquetes, se interrumpe la sesión.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md