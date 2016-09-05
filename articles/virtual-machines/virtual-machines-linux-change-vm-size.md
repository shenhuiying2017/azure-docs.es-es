<properties
   pageTitle="Cambio de tamaño de una máquina virtual de Linux | Microsoft Azure"
   description="Escalado o reducción en vertical de una máquina virtual de Linux cambiando el tamaño de la VM."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# Cambio de tamaño de una máquina virtual de Linux

## Información general 

Después de aprovisionar una máquina virtual (VM), puede escalarla o reducirla verticalmente cambiando su [tamaño][vm-sizes]. En algunos casos, hay que desasignarla antes. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que hospeda la VM.

Este artículo muestra cómo cambiar el tamaño de una VM de Linux mediante la [CLI de Azure][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.


## Cambio de tamaño de una VM de Linux 

Para cambiar el tamaño de una máquina virtual, realice los pasos siguientes.

1. Ejecute el siguiente comando de la CLI: Este comando muestra los tamaños de máquina virtual que están disponibles en el clúster de hardware donde se hospeda la VM.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Si se muestra el tamaño deseado, ejecute el comando siguiente para cambiar el tamaño de la VM.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    La máquina virtual se reiniciará durante este proceso. Tras reiniciar, se reasignarán los discos del SO y de datos. Se perderá todo lo que haya en el disco temporal.

    Al usar la opción `--enable-boot-diagnostics`, se habilita el [diagnóstico de arranque][boot-diagnostics], que permite registrar cualquier error relacionado con el inicio.

3. Si, por el contrario, no aparece el tamaño deseado, ejecute los siguientes comandos para desasignar la máquina virtual, cambiar su tamaño y reiniciarla.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Al desasignar la máquina virtual, también se liberan todas las direcciones IP dinámicas asignadas a ella. Esto no afecta a los discos del SO y de datos.
   
## Pasos siguientes

Para obtener una mayor escalabilidad, ejecute varias instancias de VM y escálelas horizontalmente. Para obtener más información, consulte [Escalado automático de máquinas de Linux en un conjunto de escalado de máquinas virtuales][scale-set].

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/es-ES/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0824_2016-->