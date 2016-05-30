<properties
	pageTitle="Solución de problemas de conexiones SSH a una máquina virtual de Azure | Microsoft Azure"
	description="Solucionar problemas y corregir errores de SSH como, por ejemplo, errores en la conexión SSH o conexiones SSH rechazadas para una máquina virtual de Azure con Linux."
	keywords="conexión ssh rechazada, error de ssh azure ssh, error de conexión ssh"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Solución de problemas de conexiones de Secure Shell en una máquina virtual de Azure basada en Linux

Hay varias razones por las que podrían generarse errores de Secure Shell (SSH) al intentar conectarse a una máquina virtual de Azure basada en Linux. Este artículo le ayudará a identificar esos problemas y corregirlos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](http://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Solución de errores comunes de SSH

En esta sección se indica cómo corregir rápidamente los problemas de conexión comunes de SSH.

### Solución de problemas de máquinas virtuales creadas con el modelo de implementación clásica

Siga estos pasos para intentar resolver los errores de conexión SSH más habituales en las máquinas virtuales creadas con el modelo de implementación clásica: Después de cada paso, pruebe a volver a conectarse a la máquina virtual.

- Restablezca el acceso remoto desde el [Portal de Azure](https://portal.azure.com). En el Portal de Azure, seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Linux* > **Reset Remote...** (Restablecer acceso remoto...).

- Reinicie la máquina virtual. En el [Portal de Azure](https://portal.azure.com), seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual de Linux* > **Reiniciar**.

	O

	En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Máquinas virtuales** > **Instancias** > **Reiniciar**.

- Vuelva a implementar la máquina virtual en un nuevo nodo de Azure. Para obtener información sobre cómo hacerlo, consulte [Redeploy virtual machine to new Azure node](virtual-machines-windows-redeploy-to-new-node.md) (Nueva implementación de la máquina virtual en un nuevo nodo de Azure).

	Tenga en cuenta que, cuando finalice esta operación, se perderán los datos de disco efímeros y se actualizarán las direcciones IP dinámicas que están asociadas a la máquina virtual.

- Siga las instrucciones de [Restablecimiento de una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-classic-reset-access.md) para:
	- Restablecer la contraseña o la clave de SSH.
	- Crear una nueva cuenta de usuario de _sudo_.
	- Restablecer la configuración de SSH.

- Compruebe el estado de los recursos de la VM para ver si hay algún problema en la plataforma.<br> Seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Linux* > **Configuración** > **Comprobar el estado**.


### Solución de problemas de máquinas virtuales creadas con el modelo de implementación de Azure Resource Manager

Siga estos pasos para intentar resolver los errores de SSH más habituales en las máquinas virtuales creadas con el modelo de implementación de Azure Resource Manager.

#### Restablecimiento de la conexión de SSH
Use la CLI de Azure para asegurarse de que la versión 2.0.5 o posterior del [agente Linux de Microsoft Azure](virtual-machines-linux-agent-user-guide.md) está instalada.

Si todavía no la tiene, [instale la CLI de Azure y conéctese a su suscripción de Azure](../xplat-cli-install.md). Inicie sesión con el comando `azure login`. Asegúrese de que está en modo Resource Manager: ```
	azure config mode arm
	```

Restablezca la conexión SSH con cualquiera de los métodos siguientes:

**Método 1:** use el comando `vm reset-access` como en el siguiente ejemplo. Esto instalará la extensión `VMAccessForLinux` en la máquina virtual:

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```



**Método 2:** cree un archivo llamado PrivateConfig.json con el siguiente contenido:

	```
	{  
		"reset_ssh":"True"
	}
	```

Después, ejecute manualmente la extensión `VMAccessForLinux` para restablecer la conexión SSH:

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

#### Restablecimiento de las credenciales SSH

**Método 1:** ejecute el comando `vm reset-access` para establecer cualquiera de las credenciales de SSH.

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

Para obtener más información, escriba `azure vm reset-access -h` en la línea de comandos.

**Método 2:** cree un archivo llamado PrivateConfig.json con el siguiente contenido:

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

Después, ejecute la extensión de Linux mediante el nombre de archivo que acabamos de describir (PrivateConf.json):

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

Si quiere probar otros enfoque para la solución de problemas, puede seguir pasos similares a los que aparecen en [Restablecer una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-classic-reset-access.md). Recuerde que debe modificar las instrucciones de CLI de Azure en el modo Resource Manager.


## Solucionar errores SSH con más detalle

Hay muchas razones posibles por las que el cliente SSH podría aún no ser capaz de ponerse en contacto con el servicio SSH en la máquina virtual. El siguiente diagrama muestra los componentes que intervienen.

![Diagrama que muestra los componentes del servicio SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot1.png)

Las siguientes secciones le servirán para aislar la causa del error y averiguar las soluciones.

### Pasos previos

En primer lugar, compruebe el estado de la máquina virtual en el portal.

En el [Portal de Azure clásico](https://manage.windowsazure.com), para las máquinas virtuales creadas con el modelo de implementación clásica:

1. Seleccione **Máquinas virtuales** > *Nombre de la VM*.
2. Seleccione el **Panel** de la máquina virtual para comprobar su estado.
3. Seleccione **Monitor** para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.
4. Seleccione **Extremos** para asegurarse de que hay un punto de conexión para el tráfico de SSH.

En el [Portal de Azure](https://portal.azure.com):

1. En el caso de una máquina virtual creada en el modelo de implementación clásica, seleccione **Examinar** > **Máquinas virtuales (clásico)** > *Nombre de VM*.

	O

	En el caso de una máquina virtual creada en el modelo de implementación de Resource Manager, seleccione **Examinar** > **Máquinas virtuales (clásico)** > *Nombre de VM*.

	El panel de estado de la máquina virtual debe mostrar **En ejecución**. Desplácese hacia abajo para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.

2. Seleccione **Configuración** para examinar los puntos de conexión, las direcciones IP y otros valores de configuración.

	Para identificar los puntos de conexión de las máquinas virtuales que se crearon mediante Resource Manager, compruebe que se haya definido un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md). Compruebe también que se hayan aplicado reglas al grupo de seguridad de red y que se les haga referencia en la subred.

Para comprobar la conectividad de red, compruebe los puntos de conexión configurados y vea si puede llegar a la máquina virtual a través de otro protocolo, como HTTP u otro servicio.

Después de realizar estos pasos, vuelva a intentar la conexión a SSH.


### Averiguar el origen del problema

El cliente de SSH de su equipo podría generar un error al establecer conexión con el servicio de SSH en la máquina virtual de Azure debido a problemas o errores de configuración en:

- Equipo cliente de SSH
- Dispositivo perimetral de la organización
- Punto de conexión de servicio en la nube y lista de control de acceso (ACL)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Linux

#### Causa 1: Equipo cliente de SSH

Para descartar el equipo como causa del error, compruebe que puede establecer conexiones a SSH con otro equipo local basado en Linux.

![Diagrama que resalta los componentes del equipo cliente de SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot2.png)

Si se genera un error, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que está bloqueando el tráfico SSH entrante o saliente (TCP 22)
- Software de proxy de cliente instalado localmente que impide las conexiones a SSH
- Software de supervisión de red instalado localmente que impide las conexiones a SSH
- Otro tipo de software de seguridad que o bien, supervisar el tráfico o bien, permite o impide ciertos tipos de tráfico.

Si alguna de estas condiciones es aplicable, deshabilite el software temporalmente e intente una conexión SSH a un equipo local para averiguar el motivo de que la conexión esté bloqueada en su equipo. Después, colabore con el administrador de red para corregir la configuración del software a fin de permitir conexiones SSH.

Si utiliza la autenticación de certificados, compruebe que dispone de estos permisos a la carpeta .ssh en el directorio principal:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (o cualquier otro archivo en el que tenga almacenadas las claves privadas)
- Chmod 644 ~/.ssh/known\_hosts (contiene los hosts a los que se ha conectado a través de SSH)

#### Causa 2: Dispositivo perimetral de la organización

Para descartar el dispositivo perimetral de la organización como causa de los errores, compruebe que un equipo conectado directamente a Internet puede establecer conexiones a SSH con la máquina virtual de Azure. Si tiene acceso a la máquina virtual a través de una conexión de Azure ExpressRoute o de una VPN de sitio a sitio, vaya a [Causa 4: grupos de seguridad de red](#nsg).

![Diagrama que resalta el dispositivo perimetral de la organización](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot3.png)

Si no tiene un equipo conectado directamente a Internet, puede crear fácilmente una nueva máquina virtual de Azure en su propio grupo de recursos o servicio en la nube y usarla. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-quick-create-cli.md). Cuando acabe de realizar las pruebas, elimine el grupo de recursos o la máquina virtual y el servicio en la nube.

Si puede crear una conexión SSH con un equipo que esté conectado directamente a Internet, compruebe si el dispositivo perimetral de la organización tiene lo siguiente:

- Un firewall interno que bloquea el tráfico SSH a Internet
- Un servidor proxy que impide las conexiones SSH
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones SSH

Trabaje con el administrador de red para corregir la configuración de los dispositivos perimetrales de la organización para permitir el tráfico SSH a Internet.

#### Causa 3: punto de conexión de servicio en la nube y ACL

> [AZURE.NOTE] Esta causa se aplica solo a las máquinas virtuales creadas con el modelo de implementación clásica. Para las máquinas virtuales creadas en Resource Manager, vaya a [Causa 4: grupos de seguridad de red](#nsg).

Para descartar el punto de conexión de servicio en la nube y ACL como el origen del error, compruebe si otra máquina virtual de Azure de la misma red virtual puede realizar conexiones SSH a la máquina virtual.

![Diagrama que resalta el punto de conexión del servicio en la nube y ACL](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot4.png)

Si no tiene otra máquina virtual en la misma red virtual, puede crear una fácilmente. Para obtener información sobre cómo hacerlo, consulte [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual que creó.

Si puede crear una conexión SSH con una máquina virtual en la misma red virtual, compruebe lo siguiente:

- **La configuración del punto de conexión para el tráfico de SSH en la máquina virtual de destino.** El puerto TCP privado del punto de conexión debe coincidir con el puerto TCP en el que escucha el servicio SSH en la máquina virtual. (El puerto predeterminado es 22). En el caso de las máquinas virtuales creadas en el modelo de implementación de Resource Manager, compruebe el número de puerto TCP de SSH en el Portal de Azure con **Examinar** > **Máquinas virtuales (v2)** > *Nombre de VM* > **Configuración** > **Puntos de conexión**.

- **La ACL del punto de conexión para el tráfico de SSH en la máquina virtual de destino.** Una ACL permite especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de SSH al punto de conexión. Compruebe las ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [Acerca de las listas de control de acceso (ACL) de red](../virtual-network/virtual-networks-acl.md).

Para descartar el punto de conexión como causa del problema, quite el punto de conexión actual, cree uno nuevo y especifique el nombre SSH (puerto TCP 22 para el número de puerto público y privado). Para obtener más información, vea [ Configuración de extremos en una máquina virtual en Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
#### Causa 4: Grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Compruebe las reglas de los grupos de seguridad de red para asegurarse de que se permite el tráfico de SSH tanto a Internet como desde Internet. Para obtener más información, consulte [Acerca de los grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

#### Causa 5: Máquina virtual de Azure basada en Linux

La última causa de los posibles problemas puede residir en la propia máquina virtual de Azure.

![Diagrama que resalta la máquina virtual de Azure basada en Linux](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot5.png)

Si aún no lo ha hecho, siga las instrucciones [para restablecer una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-classic-reset-access.md).

Pruebe de nuevo la conexión desde su equipo. Si sigue sin funcionar, pueden darse algunos de estos problemas:

- El servicio de SSH no se está ejecutando en la máquina virtual de destino.
- El servicio SSH no está escuchando en el puerto TCP 22. Para probar esto, instale un cliente telnet en el equipo local y ejecute "telnet *cloudServiceName*.cloudapp.net 22". Esto determinará si la máquina virtual permite la comunicación entrante y saliente al punto de conexión SSH.
- El firewall local en la máquina virtual de destino tiene reglas que impiden el tráfico entrante o saliente de SSH.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones SSH.


## Recursos adicionales

- Para obtener más información sobre cómo solucionar problemas de máquinas virtuales que se crearon mediante el modelo de implementación clásica, consulte [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-classic-reset-access.md).

- Para obtener más información sobre la solución de problemas con máquinas virtuales que se crearon mediante el modelo de implementación de Resource Manager, consulte:
	- [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows ](virtual-machines-windows-troubleshoot-rdp-connection.md)
	-	[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0518_2016-->