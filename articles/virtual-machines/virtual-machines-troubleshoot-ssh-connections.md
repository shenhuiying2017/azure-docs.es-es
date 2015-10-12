<properties
	pageTitle="No es posible conectarse a una VM de Azure a través de SSH | Microsoft Azure"
	description="Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure que ejecuta Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="dkshir"/>

# Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artículo trata la solución de problemas de las conexiones SSH de una máquina virtual creada con el modelo de implementación clásica o el modelo de implementación del Administrador de recursos.


Puede haber diversas causas para los errores SSH en una máquina virtual de Azure basada en Linux. Este artículo le ayudará a averiguarlas y a corregir los errores.

> [AZURE.NOTE]En este artículo solo se aplica a máquinas virtuales de Azure con Linux. Para solucionar los problemas de las conexiones a máquinas virtuales de Azure con Windows, consulte [este artículo](virtual-machines-troubleshoot-remote-desktop-connections.md).

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](http://azure.microsoft.com/support/forums/).

Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Pasos básicos

Siga estos pasos para resolver los errores de conexión de SSH más comunes:

1. Restablezca el acceso remoto desde el [portal de Azure](https://portal.azure.com). Haga clic en **Examinar todo** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Restablecer acceso remoto**.

	![Restablecer acceso remoto](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. Reinicie la máquina virtual. Haga clic en el [Portal de vista previa de Azure](https://portal.azure.com), en **Examinar todo** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Reiniciar**. En el [Portal de administración de Azure](https://manage.windowsazure.com), abra **Máquinas virtuales** > **Instancias** y haga clic en **Reiniciar**.

3. [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

4. Siga las instrucciones de [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) en la máquina virtual para:

	- Restablecer la contraseña o la clave de SSH.
	- Crear una nueva cuenta de usuario de sudo.
	- Restablecer la configuración de SSH.

## Solución de problemas detallada

Si el cliente SSH sigue sin poder ponerse en contacto con el servicio SSH en la máquina virtual, puede ser debido a muchos motivos. Presentamos a continuación los componentes implicados.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Las siguientes secciones le ayudarán a aislar la causa del error y a averiguar soluciones.

### Pasos antes de la solución de problemas

En primer lugar, compruebe el estado de la máquina virtual en el portal de Azure.

En el [portal de administración de Azure](https://manage.windowsazure.com):

1. Haga clic en **Máquinas virtuales** > *Nombre de la máquina virtual*.
2. Haga clic en el **Panel** de la máquina virtual para comprobar su estado.
3. Haga clic en **Monitor** para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.
4. Haga clic en **Extremos** para asegurarse de que hay un extremo para el tráfico de SSH.

En el [portal de vista previa de Azure](https://portal.azure.com):

1. Haga clic en **Examinar** > **Máquinas virtuales** > *Nombre de la máquina virtual*. En el caso de una máquina virtual creada en el Administrador de recursos de Azure, haga clic en **Examinar** > **Máquinas virtuales (v2)** > *Nombre de máquina virtual*. El panel de estado de la máquina virtual debe mostrar **En ejecución**. Desplácese hacia abajo para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.
2. Haga clic en **Configuración** para examinar los extremos, direcciones IP y otros valores de configuración.

Para comprobar la conectividad de red, compruebe los extremos configurados y vea si puede llegar a la máquina virtual a través de otro protocolo, como HTTP u otro servicio.

Después de realizar estos pasos, vuelva a intentar la conexión a SSH.


### Pasos para solucionar problemas

El cliente de SSH de su equipo podría generar un error al alcanzar el servicio de SSH en la máquina virtual de Azure debido a estos posibles orígenes de problemas o errores de configuración:

- Equipo cliente de SSH
- Dispositivo perimetral de la organización
- Extremo de servicio en la nube y lista de control de acceso (ACL)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Linux

#### Causa 1: Equipo cliente de SSH

Para descartar el equipo como causa del error, compruebe que puede establecer conexiones a SSH con otro equipo local basado en Linux.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Si se genera un error, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que está bloqueando el tráfico SSH entrante o saliente (TCP 22)
- Software de proxy de cliente instalado localmente que impide las conexiones a SSH
- Software de supervisión de red instalado localmente que impide las conexiones a SSH
- Otro tipo de software de seguridad que o bien, supervisar el tráfico o bien, permite o impide ciertos tipos de tráfico.

En todos estos casos, deshabilite temporalmente el software e intente una conexión a SSH con un equipo local para averiguar la causa. A continuación, trabaje con el administrador de red para corregir la configuración del software para permitir conexiones a SSH.

Si utiliza la autenticación de certificados, compruebe que dispone de estos permisos a la carpeta .ssh en el directorio principal:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (o cualquier otro archivo en el que tenga almacenadas las claves privadas)
- Chmod 644 ~/.ssh/known\_hosts (contiene los hosts a los que se ha conectado a través de SSH)

#### Causa 2: Dispositivo perimetral de la organización

Para descartar el dispositivo perimetral de la organización como causa de los errores, compruebe que un equipo conectado directamente a Internet puede establecer conexiones a SSH con la máquina virtual de Azure. Si tiene acceso a la máquina virtual a través de una conexión de ExpressRoute o de VPN de sitio a sitio, vaya a [Causa 4: Grupos de seguridad de red](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Si no tiene un equipo conectado directamente a Internet, puede crear fácilmente una nueva máquina virtual de Azure en su propio grupo de recursos o servicio en la nube y usarla. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-tutorial.md). Cuando acabe de realizar las pruebas, elimine el grupo de recursos o la máquina virtual y el servicio en la nube.

Si puede crear una conexión a SSH con un equipo conectado directamente a Internet, compruebe si el dispositivo perimetral de la organización tiene lo siguiente:

- Un firewall interno que bloquea el tráfico SSH a Internet
- Un servidor proxy que impide las conexiones a SSH
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones a SSH

Trabaje con el administrador de red para corregir la configuración de los dispositivos perimetrales de la organización para permitir el tráfico SSH a Internet.

#### Causa 3: extremo de servicio en la nube y ACL

Para eliminar el extremo de servicio en la nube y ACL como el origen del error, para las máquinas virtuales creadas con el [modelo de implementación clásica](../resource-manager-deployment-model.md), compruebe que otra máquina virtual de Azure de la misma red virtual puede realizar conexiones de SSH para la máquina virtual.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Para las máquinas virtuales creadas en el Administrador de recursos, vaya a [Causa 4: grupos de seguridad de red](#nsg).

Si no tiene otra máquina virtual en la misma red virtual, puede crear una fácilmente. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-tutorial.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual que creó.

Si puede crear una conexión a SSH con una máquina virtual en la misma red virtual, compruebe:

- La configuración del extremo para el tráfico de SSH en la máquina virtual de destino. El puerto TCP privado del extremo debe coincidir con el puerto TCP en el que escucha el servicio SSH en la máquina virtual (el predeterminado es 22). En el caso de las máquinas virtuales creadas en el modelo de implementación del Administrador de recursos mediante plantillas, compruebe el número de puerto TCP de SSH en el Portal de vista previa de Azure con **Examinar** > **Máquinas virtuales (v2)** > *Nombre de máquina virtual* > **Configuración** > **Extremos**.
- La ACL del extremo para el tráfico de SSH en la máquina virtual de destino. Las ACL permiten especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de SSH al extremo. Compruebe sus ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [Acerca de las listas de control de acceso (ACL) de red](../virtual-network/virtual-networks-acl.md).

Para descartar el extremo como causa del problema, quite el extremo actual, cree un nuevo extremo y especifique el nombre **SSH** (puerto TCP 22 para el número de puerto público y privado). Para obtener más información, vea [ Configuración de extremos en una máquina virtual en Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Causa 4: Grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Compruebe las reglas de los grupos de seguridad de red para asegurarse de que se permite el tráfico de SSH tanto a Internet como desde Internet. Para obtener más información, consulte [¿Qué es un grupo de seguridad de red?](../traffic-manager/virtual-networks-nsg.md).

#### Causa 5: Máquina virtual de Azure basada en Linux

La última causa de los posibles problemas puede residir en la propia máquina virtual de Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Si aún no lo ha hecho, siga las instrucciones [para restablecer una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) en la máquina virtual.

Pruebe de nuevo la conexión desde su equipo. Si sigue sin funcionar, pueden darse algunos de estos problemas:

- El servicio de SSH no se está ejecutando en la máquina virtual de destino.
- El servicio SSH no está escuchando en el puerto TCP 22. Para probar esto, instale un cliente telnet en el equipo local y ejecute "telnet *cloudServiceName*.cloudapp.net 22". Esto averiguará si la máquina virtual permite la comunicación entrante y saliente al extremo de SSH.
- El firewall local en la máquina virtual de destino tiene reglas que impiden el tráfico entrante o saliente de SSH.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones a SSH.


## Recursos adicionales

[Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->