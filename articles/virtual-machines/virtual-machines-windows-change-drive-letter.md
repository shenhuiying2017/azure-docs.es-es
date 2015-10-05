<properties
	pageTitle="Cambio de la letra de la unidad del disco temporal | Microsoft Azure"
	description="Cambie la letra de unidad de disco temporal en una máquina virtual de Windows creada con el modelo de implementación clásica."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
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
	ms.author="cynthn"/>

#Cambio de la letra de la unidad del disco temporal de Windows en una máquina virtual creada con el modelo de implementación clásica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de recursos con el modelo de implementación clásica.

Si necesita utilizar la unidad D para almacenar datos, siga estas instrucciones para usar una unidad distinta para el disco temporal. Nunca use el disco temporal para almacenar los datos que desee conservar.

Antes de comenzar, necesitará un disco de datos asociado a la máquina virtual para poder almacenar el archivo de paginación de Windows (pagefile.sys) durante este procedimiento. Consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows][Attach] si no tiene ninguno. Para obtener instrucciones sobre cómo averiguar qué discos están conectados, consulte "Buscar el disco" en [Desacoplamiento de un disco de una máquina virtual de Windows][Detach].

Si desea usar un disco de datos existente en la unidad D, asegúrese de que también se haya cargado el VHD en la cuenta de almacenamiento. Para obtener instrucciones, consulte los pasos 3 y 4 que se encuentran en [Creación y carga de un VHD de Windows Server en Azure][VHD].

> [AZURE.WARNING]Si se cambia el tamaño o elige "Detener (desasignar)" una máquina virtual, se puede desencadenar la colocación de la máquina virtual en un hipervisor nuevo. Un evento de mantenimiento planificado o no planificado también puede desencadenar esta colocación. En este escenario, el disco temporal se reasignará a la primera letra de unidad disponible. Si tiene una aplicación que requiera específicamente la unidad "D", asegúrese de que después de mover el archivo de paginación se asigne un nuevo disco persistente (y asígnele la letra D). Azure no volverá a reemplazar la letra D.

> [AZURE.WARNING]Si cambia el tamaño de una máquina virtual después de mover explícitamente el archivo de paginación, tenga en cuenta que se puede producir un error en el inicio si el disco temporal de la nueva máquina virtual no es lo suficientemente grande como para contener el archivo de paginación del tamaño original de la máquina virtual. También puede encontrar este error si no se estableció la unidad temporal en la siguiente letra de unidad disponible, provocando que Windows haga referencia a una letra de unidad no válida en la configuración del archivo de paginación mientras Azure crea la unidad temporal con la siguiente letra de unidad disponible.

##Cambio de la letra de unidad

1. Inicie sesión en la máquina virtual. Para obtener información detallada, consulte [Inicio de sesión en una máquina virtual con Windows Server][Logon].

2. Mueva el archivo pagefile.sys de la unidad D a otra unidad.

3. Reinicie la máquina virtual.

4. Inicie sesión de nuevo y cambie la letra de unidad de D a E.

5. En el [Portal de Azure](http://manage.windowsazure.com), acople un disco de datos existente o un disco de datos vacío.

6.	Inicie sesión en la máquina virtual de nuevo, inicialice el disco y asigne la letra D como la letra de unidad para el disco que acaba de acoplar.

7.	Compruebe que la letra E está asignada al disco temporal.

8.	Mueva el archivo pagefile.sys de la otra unidad a la unidad E.

9.	Reinicie la máquina virtual.



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

<!---HONumber=Sept15_HO4-->