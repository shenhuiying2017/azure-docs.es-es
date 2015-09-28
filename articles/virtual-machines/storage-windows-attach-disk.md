<properties
	pageTitle="Acoplamiento de un disco a una máquina virtual | Microsoft Azure"
	description="Aprenda como acoplar un disco de datos a una máquina virtual de Azure y a inicializarla para que esté lista para utilizarse."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="cynthn"/>

# Acoplamiento de un disco de datos a una máquina virtual de Windows

Puede acoplar discos vacíos como discos que contienen datos. En ambos casos, se trata realmente de archivos .vhd que residen en una cuenta de almacenamiento de Azure. También en ambos casos, una vez acoplado el disco, tendrá que inicializarlo para que esté listo para utilizarse.

> [AZURE.NOTE]Es recomendable utilizar uno o varios discos independientes para almacenar los datos de una máquina virtual. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo asignado a la unidad C y un disco temporal asignado a la unidad D. **No use la unidad D para almacenar datos.** Como señala su nombre, la unidad D ofrece únicamente almacenamiento temporal. No ofrece redundancia ni copias de seguridad porque no reside en Almacenamiento de Azure.

## Tutorial en vídeo

A continuación se facilita una guía detallada de los pasos de este tutorial.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Inicialización de un nuevo disco de datos en Windows Server

1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server][logon].

2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

	![Abrir Administrador de servidores](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Expanda el menú y seleccione **Discos**.

4. En la sección **Discos** se muestra el disco 0, el disco 1 y el disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es el disco temporal (que no debe usarse para almacenamiento de datos) y el disco 2 es el disco de datos que conectó a la máquina virtual. El disco de datos tiene una capacidad de 5 GB, en función de lo que se especificó al conectar el disco. Haga clic con el botón secundario en el disco 2 y seleccione **Inicializar**.

5.	Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. A continuación, haga clic con el botón secundario de nuevo en el disco 2 y seleccione **Nuevo volumen**.

6.	Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.

	![Volumen inicializado correctamente](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]El tamaño de la máquina virtual determina el número de discos que le puede asociar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

## Recursos adicionales

[Desacoplamiento de un disco de una máquina virtual de Windows](storage-windows-detach-disk.md)

[Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=Sept15_HO3-->