<properties pageTitle="Uso de privilegios raíz en máquinas virtuales de Linux en Azure" description="Aprenda a usar privilegios raíz en una máquina virtual de Linux en Azure." services="virtual-machines" documentationCenter="" authors="szarkos" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark"/>




# Uso de privilegios raíz en máquinas virtuales con Linux en Azure

De forma predeterminada, el usuario  `root` está deshabilitado en las máquinas virtuales de Linux en Azure. Los usuarios pueden ejecutar comandos con privilegios elevados mediante el comando  `sudo`. Sin embargo, la experiencia puede variar dependiendo de la manera en que se aprovisionó el sistema.

1. **Clave SSH y contraseña o solo contraseña** - la máquina virtual se aprovisionó con un certificado (archivo ".CER") o clave SSH, así como con una contraseña o simplemente un nombre de usuario y una contraseña. En este caso  `sudo` solicitará la contraseña del usuario antes de ejecutar el comando.

2. **Solo clave SSH** - La máquina virtual se aprovisionó con un certificado (".cer" o archivo  `.pem`) o clave SSH, pero no hay ninguna contraseña.  En este caso  `sudo`** no** solicitará la contraseña del usuario antes de ejecutar el comando.


## Clave SSH y contraseña o solo contraseña

Inicie sesión en la máquina virtual con Linux usando la autenticación de clave SSH o contraseña y, a continuación, ejecute los comandos usando  `sudo`, por ejemplo:

	# sudo <command>
	[sudo] password for azureuser:

En este caso, se le solicitará al usuario una contraseña. Después de escribir la contraseña,  `sudo` ejecutará el comando con los privilegios  `root`.


## Solo clave SSH

Inicie sesión en la máquina virtual con Linux usando la autenticación de clave SSH y, a continuación, ejecute los comandos usando  `sudo`, por ejemplo:

	# sudo <command>

En este caso **no** se solicitará la contraseña al usuario. Después de pulsar, `<, enter>` `sudo` ejecutará el comando con los privilegios  `root`.





<!--HONumber=42-->
