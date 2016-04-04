<properties
	pageTitle="Conectar un disco de datos | Microsoft Azure"
	description="Cómo conectar un disco de datos nuevo o existente a una máquina virtual en el Portal de Azure con el modelo de implementación del Administrador de recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Cómo conectar un disco de datos en el Portal de Azure

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>Inicialización de un nuevo disco de datos en Windows Server

1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual que ejecuta Windows Server](virtual-machines-windows-log-on.md).

2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

	![Abrir Administrador de servidores](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Expanda el menú y seleccione **Discos**.

4. En la sección **Discos** aparecen todos los discos. En la mayoría de los casos, habrá un disco 0, un disco 1 y un disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es el disco temporal y el disco 2 es el disco de datos que acaba de conectar a la VM. El nuevo disco de datos mostrará la partición como **Desconocida**. Haga clic con el botón derecho en el disco y seleccione **Inicializar**.

5.	Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Una vez que se complete el proceso, la partición aparecerá como **GPT**. Vuelva a hacer clic con el botón derecho en el disco y seleccione **Nuevo volumen**.

6.	Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.


	![Volumen inicializado correctamente](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] El tamaño de la VM determina el número de discos que le puede asociar. Para obtener más información, vea [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).


## Pasos siguientes

Si la aplicación debe usar la unidad D: para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows].

<!---HONumber=AcomDC_0323_2016-->