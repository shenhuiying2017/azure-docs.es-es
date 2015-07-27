<properties 
	pageTitle="Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux" 
	description="Si no se puede conectar la máquina virtual de Azure basada en Linux, siga estos pasos para aislar la causa del problema."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux

Si no puede conectarse a máquinas virtuales Azure basadas en Linux, este artículo describe un enfoque metódico para aislar y corregir el problema.

## Paso 1: Restablecimiento de la configuración, clave o contraseña de SSH

Siga las instrucciones de [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) en la máquina virtual. Con estas instrucciones, puede:

- Restablecer la contraseña o la clave de SSH.
- Crear una nueva cuenta de usuario de sudo.
- Restablecer la configuración de SSH.

Si el cliente SSH sigue sin poder ponerse en contacto con el servicio SSH en la máquina virtual, puede ser debido a muchas causas. Presentamos a continuación los componentes implicados.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Las secciones siguientes indican los pasos para aislar y determinar las distintas causas de este problema y proporcionan soluciones alternativas.

## Paso 2: Pasos previos antes de la solución de problemas detallada

En primer lugar, compruebe el estado de la máquina virtual en el Portal de administración de Azure o en el Portal de vista previa de Azure

En el Portal de administración de Azure:

1. Haga clic en **Máquinas virtuales** > *Nombre de la máquina virtual*.
2. Haga clic en **Panel** de la máquina virtual para comprobar su estado.
3. Haga clic en **Monitor** para ver la actividad reciente de los recursos de proceso, almacenamiento y de red. 
4. Haga clic en **Extremos** para asegurarse de que hay un extremo para el tráfico de SSH.

En el Portal de vista previa de Azure:

1. Haga clic en **Examinar** > **Máquinas virtuales** > *Nombre de la máquina virtual*. Para una máquina virtual creada en el Administrador de recursos, haga clic en **Examinar** > **Máquinas virtuales (v2)** > *Nombre de máquina virtual*. El panel de estado de la máquina virtual debe mostrar **En ejecución**. Desplácese hacia abajo para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.
2. Haga clic en **Configuración** para examinar los extremos, direcciones IP y otros valores de configuración.

Para comprobar la conectividad de red, analice los extremos configurados y determine si puede llegar a la máquina virtual a través de otro protocolo, como HTTP o de otro servicio conocido.

Si es necesario, [reinicie la máquina virtual](https://msdn.microsoft.com/library/azure/dn763934.aspx) o [cambie el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Después de realizar estos pasos, vuelva a intentar la conexión a SSH.

## Paso 3: Solución de problemas detallada 

La incapacidad del cliente de SSH para ponerse en contacto con el servicio Servicios de SSH en la máquina virtual de Azure puede deberse a los siguientes problemas o errores de configuración:

- Equipo cliente de SSH
- Dispositivo perimetral de la organización
- Extremo de servicio en la nube y lista de control de acceso (ACL)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Linux

### Causa 1: Equipo cliente de SSH

Para descartar el equipo como causa de los problemas o errores de configuración, compruebe que el equipo puede establecer conexiones a SSH con otro equipo local basado en Linux.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)
 
Si no es posible, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que está bloqueando el tráfico SSH entrante o saliente (TCP 22)
- Software de proxy de cliente instalado localmente que impide las conexiones a SSH
- Software de supervisión de red instalado localmente que impide las conexiones a SSH
- Otro tipo de software de seguridad, por ejemplo para supervisar el tráfico o para permitir o impedir ciertos tipos de tráfico, que imposibilita las conexiones a SSH

En todos estos casos, intente deshabilitar temporalmente el software y pruebe a establecer una conexión a SSH con un equipo local para determinar la causa. A continuación, trabaje con el administrador de red para corregir la configuración del software para permitir conexiones a SSH.

Si utiliza la autenticación de certificados, compruebe que dispone de estos permisos a la carpeta .ssh en el directorio principal:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id_rsa (o cualquier otro archivo en el que tenga almacenadas las claves privadas)
- Chmod 644 ~/.ssh/known_hosts (contiene los hosts a los que se ha conectado a través de SSH)

### Causa 2: Dispositivo perimetral de la organización

Para descartar el dispositivo perimetral de la organización como causa de los problemas o errores de configuración, compruebe que un equipo conectado directamente a Internet puede establecer conexiones a SSH con la máquina virtual de Azure. Si tiene acceso a la máquina virtual a través de una conexión de ExpressRoute o de VPN de sitio a sitio, vaya a [Causa 4: Grupos de seguridad de red](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)
 
Si no tiene un equipo conectado directamente a Internet, puede crear fácilmente una nueva máquina virtual de Azure en su propio grupo de recursos o servicio en la nube y usarla. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-tutorial.md). Cuando acabe de realizar las pruebas, elimine el grupo de recursos o la máquina virtual y el servicio en la nube.

Si puede crear una conexión a SSH con un equipo conectado directamente a Internet, compruebe si el dispositivo perimetral de la organización tiene lo siguiente:

- Un firewall interno que bloquea el tráfico SSH a Internet
- Un servidor proxy que impide las conexiones a SSH
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones a SSH

Trabaje con el administrador de red para corregir la configuración de los dispositivos perimetrales de la organización para permitir el tráfico SSH a Internet.

### Causa 3: extremo de servicio en la nube y ACL

Para descartar el extremo de servicio en la nube y la ACL como causa de los problemas o errores de configuración de máquinas virtuales creadas en la administración del servicio, compruebe que otra máquina virtual de Azure que se encuentra en la misma red virtual pueda hacer conexiones a SSH en la máquina virtual de Azure.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Para las máquinas virtuales creadas en el Administrador de recursos, vaya a [Origen 4: Grupos de seguridad de red](#nsg).

Si no tiene otra máquina virtual en la misma red virtual, puede crear una fácilmente. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-tutorial.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual que creó.

Si puede crear una conexión a SSH con una máquina virtual en la misma red virtual, compruebe:

- La configuración del extremo para el tráfico de SSH en la máquina virtual de destino. El puerto TCP privado del extremo debe coincidir con el puerto TCP en el que escucha el servicio SSH en la máquina virtual, que de forma predeterminada es 22. Para las máquinas virtuales creadas en el Administrador de recursos mediante plantillas, compruebe el número de puerto TCP de SSH en el Portal de vista previa de Azure con **Examinar** > **Máquinas virtuales (v2)** > *Nombre de máquina virtual* > **Configuración** > **Extremos**.
- La ACL del extremo para el tráfico de SSH en la máquina virtual de destino. Las ACL permiten especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de SSH al extremo. Examine las ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [Acerca de las listas de control de acceso (ACL) de red](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Para descartar el extremo como causa del problema, quite el extremo actual y cree un nuevo extremo, especificando el nombre **SSH** (puerto TCP 22 para el número de puerto público y privado). Para obtener más información, vea [ Configuración de extremos en una máquina virtual en Azure](virtual-machines-set-up-endpoints.md).

### <a id="nsg"></a>Causa 4: Grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Examine las reglas de los grupos de seguridad de red para asegurarse de que se permite el tráfico de SSH desde Internet. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](../traffic-manager/virtual-networks-nsg.md).

### Causa 5: Máquina virtual de Azure basada en Linux 

La última causa de los posibles problemas puede residir en la propia máquina virtual de Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)
 
Si aún no lo ha hecho, siga las instrucciones de [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) en la máquina virtual.

Pruebe de nuevo la conexión desde su equipo. Si no funciona correctamente, pueden darse algunos de estos problemas:

- El servicio de SSH no se está ejecutando en la máquina virtual de destino.
- El servicio SSH no está escuchando en el puerto TCP 22. Para probar esto, instale un cliente telnet en el equipo local y ejecute "telnet *cloudServiceName*.cloudapp.net 22". Esto determinará si la máquina virtual permite la comunicación entrante y saliente al extremo de SSH.
- El firewall local en la máquina virtual de destino tiene reglas que impiden el tráfico entrante o saliente de SSH.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones a SSH.


## Paso 4: Enviar el problema a los foros de soporte técnico de Azure

Para recibir ayuda de expertos de Azure de todo el mundo, puede enviar su problema a los foros de Azure de MSDN o Stack Overflow. Para obtener más información, consulte [Foros de Microsoft Azure](http://azure.microsoft.com/support/forums/).

## Paso 5: Registrar un incidente de soporte técnico de Azure

Si siguió los pasos del 1 al 4 de este artículo y envió su problema a los foros de soporte técnico de Azure pero todavía no es capaz de crear una conexión a SSH, puede volver a crear la máquina virtual.

Si no puede volver a crear la máquina virtual, es el momento de registrar un incidente de soporte técnico de Azure.

Para registrar un incidente, vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener Ayuda**.

Para obtener información sobre el uso del soporte técnico de Azure, consulte las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Recursos adicionales

[Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)
 

<!---HONumber=July15_HO2-->