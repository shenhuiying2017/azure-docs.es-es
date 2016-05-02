


Si no tiene acceso a una aplicación que se ejecuta en una máquina virtual de Azure, en este artículo se describe un enfoque metódico para aislar la causa del problema y corregirlo.

> [AZURE.NOTE]  Para obtener ayuda para la conexión a una máquina virtual de Azure, consulte [Solución de problemas de conexiones de escritorio remoto a una máquina virtual de Azure basada en Windows](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) o [Solución de problemas de conexiones Secure Shell (SSH) a una máquina virtual de Azure basada en Linux](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure y de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.


Hay cuatro áreas principales para solucionar el acceso de una aplicación que se ejecuta en una máquina virtual de Azure.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1.	La aplicación que se ejecuta en la máquina virtual de Azure.
2.	La máquina virtual de Azure.
3.	Puntos de conexión de Azure para el servicio en la nube que contiene la máquina virtual (para las máquinas virtuales del modelo de implementación clásica), reglas NAT de entrada (para las máquinas virtuales del modelo de implementación de Resource Manager) y grupos de seguridad de red.
4.	El dispositivo perimetral de Internet.

Para los equipos cliente que tienen acceso a la aplicación a través de una conexión ExpressRoute o de VPN de sitio a sitio, las áreas principales que pueden causar problemas son la aplicación y la máquina virtual de Azure. Para determinar el origen del problema y su corrección, siga estos pasos.

## Paso 1: ¿Puede obtener acceso a la aplicación desde la VM de destino?

Intente obtener acceso a la aplicación con el programa cliente adecuado desde la VM en la que se ejecuta. Use el nombre de host local, la dirección IP local o la dirección de bucle invertido (127.0.0.1).

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por ejemplo, si la aplicación es un servidor web, abra un explorador en la VM e intente obtener acceso a una página web hospedada en ella.

Si puede tener acceso a la aplicación, vaya al [paso 2](#step2).

Si no se puede obtener acceso a la aplicación, compruebe lo siguiente:

- La aplicación se ejecuta en la máquina virtual de destino.
- La aplicación está escuchando en los puertos TCP y UDP esperados.

En máquinas virtuales basadas en Windows y Linux, use el comando **netstat -a** para mostrar los puertos de escucha activos. Examine la salida para los puertos esperados en el que debe escuchar su aplicación. Reinicie la aplicación o configúrela para usar los puertos esperados según sea necesario.

## <a id="step2"></a>Paso 2: ¿Tiene acceso a la aplicación desde otra máquina virtual en la misma red virtual?

Intente obtener acceso a la aplicación desde otra VM (que esté en la misma red virtual) con el nombre de host de la VM o con su dirección IP de proveedor, privada o pública asignada por Azure. Para las máquinas virtuales creadas con el modelo de implementación clásica, no use la dirección IP pública del servicio en la nube.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por ejemplo, si la aplicación es un servidor web, intente obtener acceso a una página web desde un explorador en una VM diferente de la misma red virtual.

Si puede tener acceso a la aplicación, vaya al [paso 3](#step3).

Si no se puede obtener acceso a la aplicación, compruebe lo siguiente:

- El firewall del host de la VM de destino permite el tráfico de la solicitud entrante y el tráfico de la respuesta saliente.
- El software de detección de intrusiones o de supervisión de red que se ejecuta en la VM de destino permite el tráfico.
- Los grupos de seguridad de red permiten el tráfico.
- Un componente independiente que se ejecuta en la VM en la ruta de acceso entre la VM de prueba y su VM (como un equilibrador de carga o un firewall) permite el tráfico.

En una máquina virtual basada en Windows, use el Firewall de Windows con seguridad avanzada para determinar si las reglas de firewall excluyen el tráfico entrante y saliente de la aplicación.

## <a id="step3"></a>Paso 3: ¿Tiene acceso a la aplicación desde un equipo que está fuera de la red virtual, pero no conectado a la misma red que el equipo?

Intente obtener acceso a la aplicación desde un equipo situado fuera de la red virtual como la VM en la que se está ejecutando la aplicación, pero que no esté en la misma red que el equipo cliente original.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por ejemplo, si la aplicación es un servidor web, intente tener acceso a la página web desde un explorador de un equipo que no esté en la red virtual.

Si no se puede obtener acceso a la aplicación, compruebe lo siguiente:

- En las VM creadas con el modelo de implementación clásica, que la configuración del punto de conexión de la VM permita el tráfico entrante, sobre todo el protocolo (TCP o UDP) y los números de puerto público y privado. Para obtener más información, consulte [Cómo establecer extremos en una máquina virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
- En las VM creadas con el modelo de implementación clásica, que las listas de control de acceso (ACL) del punto de conexión no impidan el tráfico entrante desde Internet. Para obtener más información, consulte [Cómo establecer extremos en una máquina virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
- En las VM creadas con el modelo de implementación de Resource Manager, que la configuración de la regla NAT entrante de la VM permita el tráfico entrante, sobre todo el protocolo (TCP o UDP) y los números de puerto público y privado.
- Que los grupos de seguridad de red permitan la solicitud entrante y el tráfico de respuesta saliente. Para obtener más información, vea [¿Qué es un grupo de seguridad de red?](../articles/virtual-network/virtual-networks-nsg.md)

Si la máquina virtual o el extremo es un miembro de un conjunto con equilibrio de carga:

- Compruebe que el protocolo de sondeo (TCP o UDP) y el número de puerto son correctos.
- Si el protocolo de sondeo y el puerto es diferente del protocolo y puerto del conjunto con equilibrio de carga:
	- Compruebe que la aplicación está escuchando el protocolo de sondeo (TCP o UDP) y el número de puerto (use **netstat –a** en la VM de destino).
	- Compruebe que el firewall del host de la VM de destino permite la solicitud de sondeo entrante y el tráfico de respuesta de sondeo saliente.

Si puede tener acceso a la aplicación, asegúrese de que el dispositivo perimetral de Internet permita:

- El tráfico de solicitud saliente de aplicaciones desde el equipo cliente hasta la máquina virtual de Azure.
- El tráfico de respuesta de aplicación entrante desde la máquina virtual de Azure.

## Solucionar problemas de conectividad de punto de conexión

Si tiene problemas al conectar a un punto de conexión como puede ser el del escritorio remoto, puede intentar los siguientes pasos para solucionar varios problemas generales:

- Reiniciar la máquina virtual
- Volver a crear el punto de conexión
- Conectarse desde otra ubicación
- Cambiar el tamaño de la máquina virtual
- Volver a crear la máquina virtual

Para obtener más información, consulte [Solución de problemas con la conectividad del punto de conexión (RDP, SSH, HTTP u otros errores)](https://social.msdn.microsoft.com/Forums/azure/es-ES/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).



## Recursos adicionales

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows ](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)

<!---HONumber=AcomDC_0420_2016-->