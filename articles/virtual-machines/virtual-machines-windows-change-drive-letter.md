<properties
	pageTitle="Cambio de la letra de unidad del disco temporal de Windows"
	description="Describe cómo reasignar el disco temporal en una máquina virtual basada en Windows en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="kathydav"/>

#Cambio de la letra de unidad del disco temporal de Windows

Si necesita utilizar la unidad D para almacenar datos, siga estas instrucciones para usar una unidad distinta para el disco temporal. Nunca use el disco temporal para almacenar los datos que desee conservar.

Antes de comenzar, necesitará un disco de datos asociado a la máquina virtual para poder almacenar el archivo de paginación de Windows (pagefile.sys) durante este procedimiento. Consulte [Conexión de un disco de datos a una máquina virtual de Windows][Attach] si no tiene ninguno. Para obtener instrucciones sobre cómo averiguar qué discos están conectados, consulte "Buscar el disco" en [Cómo desconectar un disco de datos de una máquina virtual Windows][Detach].

Si desea usar un disco de datos existente en la unidad D, asegúrese de que también se haya cargado el VHD en la cuenta de almacenamiento. Para obtener instrucciones, consulte los pasos 3 y 4 que se encuentran en [Creación y carga de un VHD de Windows Server en Azure][VHD].

> [AZURE.WARNING]Si al modificar el tamaño de una máquina virtual se mueve la máquina virtual a otro host, el disco temporal volverá a ser la unidad D.

##Cambio de la letra de unidad

1. Inicie sesión en la máquina virtual. Para obtener información detallada, consulte [Cómo iniciar sesión en una máquina virtual que ejecuta Windows Server][Logon].

2. Mueva el archivo pagefile.sys de la unidad D a otra unidad.

3. Reinicie la máquina virtual.

4. Inicie sesión de nuevo y cambie la letra de unidad de D a E.

5. En el [Portal de Azure](http://manage.windowsazure.com), acople un disco de datos existente o un disco de datos vacío.

6.	Inicie sesión en la máquina virtual de nuevo, inicialice el disco y asigne la letra D como la letra de unidad para el disco que acaba de acoplar.

7.	Compruebe que la letra E está asignada al disco temporal.

8.	Mueva el archivo pagefile.sys de la otra unidad a la unidad E.

## Recursos adicionales
[Inicio de sesión en una máquina virtual con Windows Server][Logon]

[Desacoplamiento de un disco de datos de una máquina virtual de Windows][Detach]

[Acerca de las cuentas de almacenamiento de Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=August15_HO7-->