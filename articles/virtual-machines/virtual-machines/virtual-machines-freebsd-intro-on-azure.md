<properties
   pageTitle="Introducción a FreeBSD en Azure | Microsoft Azure"
   description="Aprenda a utilizar máquinas virtuales de FreeBSD en Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

#Introducción a FreeBSD en Azure
Este tema proporciona una visión general de la ejecución de una máquina virtual de FreeBSD en Azure.

##Información general
Microsoft Corporation proporciona FreeBSD para Microsoft Azure. FreeBSD es un sistema operativo avanzado que se utiliza para servidores modernos, equipos de escritorio y plataformas insertadas. En este momento, la imagen de FreeBSD 10.3 está disponible en Azure. La imagen se basa en la versión 10.3 de FreeBSD. Y está instalada la versión [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) del agente invitado de máquina virtual de Azure. El agente es responsable de la comunicación entre la máquina virtual de FreeBSD y el tejido de Azure para operaciones como el aprovisionamiento de la máquina virtual en el primer uso (nombre de usuario, contraseña, nombre de host, etc.) y la habilitación de la funcionalidad para las extensiones de máquina virtual selectivas. En cuanto a futuras versiones de FreeBSD, la estrategia es mantenerse al día y que las últimas versiones estén disponibles al poco tiempo de que el equipo de ingeniería de versión de FreeBSD las publique. La próxima versión es [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

##Cómo implementar una máquina Virtual de FreeBSD
La implementación de una máquina virtual con FreeBSD es un proceso sencillo cuando se usa una imagen de [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

##Extensión de máquina virtual para FreeBSD
Las extensiones de máquina virtual admitidas en la máquina virtual de FreeBSD son las siguientes.

• [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

La extensión VMAccess puede:

- Restablecer la contraseña del usuario de sudo original
- Crear un nuevo usuario de sudo con la contraseña especificada
- Establecer la clave pública del host con la clave dada
- Restablecer la clave pública del host proporcionada durante el aprovisionamiento de máquina virtual si no se proporciona clave de host
- Abrir el puerto(22) SSH y restaurar sshd\_config si reset\_ssh está establecido en true
- Quitar el usuario existente
- Comprobar discos
- Reparar disco agregado

• [CustomSript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)

La extensión CustomScript puede:

- Si se proporciona, descargar los scripts desde Azure Storage o desde un almacenamiento público externo (por ejemplo, Github)
- Ejecutar el script de punto de entrada
- Admitir comandos en línea
- Convertir la nueva línea de estilo de Windows en scripts de Shell y Python automáticamente
- Quitar la marca BOM en scripts de Shell y Python automáticamente
- Proteger la información confidencial en commandToExecute

##Autenticación: nombres de usuario, contraseñas y claves SSH
Al crear una máquina virtual FreeBSD con el Portal de Azure clásico, se le pedirá que facilite un nombre de usuario, una contraseña o una clave pública SSH. La elección de un nombre de usuario para implementar una máquina virtual FreeBSD en Azure está sujeta a la siguiente limitación: no se admiten los nombres de cuentas del sistema (UID <100) ya existentes en la máquina virtual, como por ejemplo, "root". Hasta ahora se admite solo la clave RSA SSH. La clave SSH de varias líneas tiene que empezar con---- BEGIN SSH2 PUBLIC KEY ---- y terminar con ---- END SSH2 PUBLIC KEY ----.

##Cómo obtener privilegios de superusuario
La cuenta de usuario especificada durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. Se instaló el paquete de "sudo" en la imagen de FreeBSD publicada. Después de iniciar sesión con esta cuenta de usuario, puede ejecutar comandos como root con usando la sintaxis de comando.
  
    # sudo <COMMAND>

También puede obtener un shell root con sudo -s.

##Pasos siguientes
- Acuda a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) para crear una máquina virtual de FreeBSD.
- Si desea incorporar su propio FreeBSD a Azure, consulte [Creación y carga de un VHD de FreeBSD en Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

<!---HONumber=AcomDC_0907_2016-->