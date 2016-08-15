<properties
	pageTitle="Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure | Microsoft Azure"
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
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure

Hay varias razones por las que podrían generarse errores de Secure Shell (SSH), un error de conexión de SSH o se rechaza al intentar conectarse a una máquina virtual (VM) de Azure basada en Linux. Este artículo le ayudará a identificar esos problemas y corregirlos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](http://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Máquinas virtuales creadas con el modelo de implementación de Resource Manager

Puede restablecer las credenciales o SSHD mediante los comandos de la CLI de Azure directamente o la [extensión VMAccessForLinux Azure](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). Después de cada paso de solución de problemas, intente conectarse de nuevo a la máquina virtual.

### Requisitos previos de la CLI de Azure

Si todavía no la tiene, [instale la CLI de Azure y conéctese a su suscripción de Azure](../xplat-cli-install.md). Inicie sesión con el comando `azure login` y asegúrese de que está en modo de Resource Manager (`azure config mode arm`).

Asegúrese de que está instalado el [agente Linux de Microsoft Azure](virtual-machines-linux-agent-user-guide.md) versión 2.0.5 o posterior.

### Restablecer SSHD
La configuración de SSHD podría no ser correcta o que el servicio haya encontrado un error. Puede restablecer SSHD para asegurarse de que la configuración de SSH sea válida.

#### Azure CLI
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### Extensión de acceso a máquina virtual
Las extensiones de acceso leen un archivo json que define la acción que se lleva a cabo, por ejemplo, restablecer SSH, restablecer una clave SSH, agregar un nuevo usuario, etc. En primer lugar, cree un archivo llamado PrivateConf.json con el siguiente contenido:

```bash
{  
	"reset_ssh":"True"
}
```

Después, ejecute manualmente la extensión `VMAccessForLinux` para restablecer la conexión SSHD:

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Restablecimiento de las credenciales de SSH de un usuario
Si SSHD parece funcionar correctamente, puede restablecer la contraseña de un usuario dado.

#### Azure CLI
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

Si utiliza la autenticación de claves SSH, puede restablecer la clave SSH de un usuario determinado:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <username> -M <~/.ssh/azure_id_rsa.pub>
```

#### Extensión de acceso a máquina virtual
Cree un archivo llamado PrivateConfig.json con el siguiente contenido:

```bash
{
	"username":"Username", "password":"NewPassword"
}
```

O bien, para restablecer la clave SSH de un usuario determinado, cree un archivo llamado PrivateConf.json con el siguiente contenido:

```bash
{
	"username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

Tras realizar uno de los pasos anteriores, ejecute manualmente la extensión `VMAccessForLinux` para restablecer sus credenciales de usuario de SSH:

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Reimplementación de una máquina virtual
Puede volver a implementar una máquina virtual en otro nodo dentro de Azure, lo que podría corregir los problemas de red subyacentes. Para volver a implementar una máquina virtual mediante el Portal de Azure, seleccione **Examinar** > **Máquinas virtuales** > *su máquina virtual Linux* > **Volver a implementar**. Para más información sobre cómo hacerlo, consulte [Nueva implementación de la máquina virtual en un nuevo nodo de Azure](virtual-machines-windows-redeploy-to-new-node.md). Actualmente no se puede volver a implementar una máquina virtual mediante la CLI de Azure.

> [AZURE.NOTE] Tenga en cuenta que, cuando finalice esta operación, se perderán los datos de disco efímeros y se actualizarán las direcciones IP dinámicas que están asociadas a la máquina virtual.


## Máquinas virtuales creadas con el modelo de implementación clásica

Siga estos pasos para intentar resolver los errores de conexión SSH más habituales en las máquinas virtuales creadas con el modelo de implementación clásica: Después de cada paso, pruebe a conectarse a la máquina virtual.

- Restablezca el acceso remoto desde el [Portal de Azure](https://portal.azure.com). En el Portal de Azure, seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Linux* > **Restablecer acceso remoto...**

- Reinicie la máquina virtual. En el [Portal de Azure](https://portal.azure.com), seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Linux* > **Reiniciar**.

	O

	En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Máquinas virtuales** > **Instancias** > **Reiniciar**.

- Implemente de nuevo la máquina virtual en un nuevo nodo de Azure. Para más información sobre cómo hacerlo, consulte [Nueva implementación de la máquina virtual en un nuevo nodo de Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Tenga en cuenta que, cuando finalice esta operación, se perderán los datos de disco efímeros y se actualizarán las direcciones IP dinámicas que están asociadas a la máquina virtual.

- Siga las instrucciones de [Restablecimiento de una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-classic-reset-access.md) para:
	- Restablecer la contraseña o la clave de SSH.
	- Crear una nueva cuenta de usuario de _sudo_.
	- Restablecer la configuración de SSH.

- Compruebe el estado de los recursos de la máquina virtual para ver si hay algún problema en la plataforma.<br> Seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Linux* > **Configuración** > **Comprobar el estado**.


## Recursos adicionales

- Si sigue sin poder establecer una conexión SSH a su máquina virtual después de seguir los pasos anteriores, puede examinar [pasos más detallados de solución de problemas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) para ver configuraciones de red adicionales y otra información adicional.

- Para más información sobre cómo solucionar problemas de acceso a las aplicaciones, consulte [Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-linux-troubleshoot-app-connection.md).

- Para más información sobre cómo solucionar problemas de máquinas virtuales que se crearon mediante el modelo de implementación clásica, consulte [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-classic-reset-access.md).

<!---HONumber=AcomDC_0803_2016-->