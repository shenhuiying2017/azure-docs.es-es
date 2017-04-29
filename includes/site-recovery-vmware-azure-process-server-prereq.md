En este artículo se supone que

1. Ya se ha establecido una **VPN de sitio a sitio** o una conexión de **Express Route** entre su red local y Azure Virtual Network.
2. Su cuenta de usuario tiene permisos para crear una nueva máquina virtual en la suscripción de Azure en la que las máquinas virtuales se han conmutado por error.
3. La suscripción tiene un mínimo de 4 núcleos disponibles para poner en marcha una nueva máquina virtual de servidor de procesos.
4. Dispone de la **frase de contraseña del servidor de configuración**.

> [!TIP]
> Asegúrese de que se puede conectar al puerto 443 del servidor de configuración (que se ejecuta de forma local) desde la instancia de Azure Virtual Network en la que las máquinas virtuales se han conmutado por error.
