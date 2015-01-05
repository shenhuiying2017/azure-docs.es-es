<properties urlDisplayName="Use root privileges" pageTitle="Uso de privilegios raíz en máquinas virtuales de Linux en Azure" metaKeywords="" description="Learn how to use root privileges on a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="szarkos" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />




# Uso de privilegios raíz en máquinas virtuales con Linux en Azure

De forma predeterminada, el usuario "root" está deshabilitado en las máquinas virtuales de Linux en Azure. Los usuarios pueden ejecutar comandos con privilegios elevados con el comando "sudo". Sin embargo, la experiencia puede variar dependiendo de la manera en que se aprovisionó el sistema.

1. **Clave SSH y contraseña o solo contraseña**: la máquina virtual se aprovisionó con un certificado (archivo ".CER") o una clave SSH, además de una contraseña, o solo un nombre de usuario y una contraseña. En este caso "sudo" solicitará la contraseña del usuario antes de ejecutar el comando.

2. **Solo clave SSH**: la máquina virtual se aprovisionó con un certificado (archivo ".cer" o ".pem") o la clave SSH, pero sin contraseña.  En este caso, "sudo" **no** solicitará la contraseña del usuario antes de ejecutar el comando.


## Clave SSH y contraseña o solo contraseña

Inicie sesión en la máquina virtual con Linux usando la autenticación de clave SSH o contraseña y, a continuación, ejecute los comandos usando "sudo", por ejemplo:

	# sudo <command>
	[sudo] password for azureuser:

En este caso, se le solicitará al usuario una contraseña. Después de escribir la contraseña, "sudo" ejecutará el comando con los privilegios "root".


## Solo clave SSH

Inicie sesión en la máquina virtual con Linux usando la autenticación de clave SSH y, a continuación, ejecute los comandos usando "sudo", por ejemplo:

	# sudo <command>

En este caso **no** se solicitará la contraseña al usuario. Después de presionar "<Entrar>", "sudo" ejecutará el comando con los privilegios "root".


<!--HONumber=35.1-->
