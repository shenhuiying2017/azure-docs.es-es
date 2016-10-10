<properties
	pageTitle="Desconexión de un disco de una máquina virtual de Windows | Microsoft Azure"
	description="Aprenda a desconectar un disco de datos de una máquina virtual de Azure creada mediante el modelo de implementación de Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>



# Desacoplamiento de un disco de datos de una máquina virtual de Windows


Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento.

> [AZURE.WARNING] Si se desconecta un disco, no se elimina automáticamente. Si se ha suscrito a Almacenamiento premium, seguirá acumulando cargos de almacenamiento por el disco. Para más información, consulte [Precios y facturación](../storage/storage-premium-storage.md#pricing-and-billing) cuando se utiliza Almacenamiento premium.

Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).


## Desconexión de un disco de datos mediante el portal

1. En el centro del portal, seleccione **Máquinas virtuales**.

2. Seleccione la máquina virtual que tiene el disco de datos que quiere desconectar y haga clic en **Todas las configuraciones**.

3. En la hoja **Configuración**, seleccione **Discos**.

4. En la hoja **Discos**, seleccione el disco de datos que desearía desconectar.

5. En la hoja del disco de datos, haga clic en **Separar**.


	![Captura de pantalla que muestra el botón Desacoplar.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.


## Desconexión de un disco de datos con PowerShell

En este ejemplo, el primer comando obtiene la máquina virtual denominada **MyVM07** en el grupo de recursos **RG11** con el cmdlet Get-AzureRmVM. El comando almacena la máquina virtual en la variable **$VirtualMachine**.

El segundo comando quita el disco de datos denominado DataDisk3 de la máquina virtual.

El último comando actualiza el estado de la máquina virtual para completar el proceso de quitar el disco de datos.

	$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
	Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
	Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Para más información, consulte [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## Pasos siguientes

Si desea reutilizar el disco de datos, basta con que lo [conecte a otra máquina virtual](virtual-machines-windows-attach-disk-portal.md)

<!---HONumber=AcomDC_0928_2016-->