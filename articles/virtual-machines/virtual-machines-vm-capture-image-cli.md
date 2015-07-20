<properties
	pageTitle="Captura de una imagen de una máquina virtual que ejecuta Linux mediante la CLI"
	description="Aprenda a capturar una imagen de una máquina virtual de Azure que ejecuta Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# Cómo capturar una máquina virtual de Linux para usar como plantilla con la CLI##



En este artículo se muestra cómo puede capturar una máquina virtual de Azure con Linux para usarla como plantilla en la creación de otras máquinas virtuales. Esta plantilla de máquina virtual incluye el disco del sistema operativo y cualquier otro disco de datos conectado a la máquina virtual. No incluye la configuración de red, por lo que deberá configurarla usted mismo cuando cree las otras máquinas virtuales que utilicen la plantilla.



Azure trata esta plantilla como una imagen y la almacena en su lista de imágenes. Aquí también están almacenadas todas las imágenes que ha cargado. Para obtener más información sobre las imágenes, consulte [Acerca de las imágenes de máquina virtual en Azure][].



##Antes de empezar##



Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no ha realizado esto todavía, siga estas instrucciones:



- [Creación de una máquina virtual personalizada][]

- [Acoplamiento de un disco de datos a una máquina virtual][]



##Capture la máquina virtual##



1. Conexión a una máquina virtual. Para obtener los detalles, consulte [Inicio de sesión en una máquina virtual con Linux][].



2. La máquina virtual solo se puede capturar si el estado es **StoppedDeallocated**. En la ventana SSH, escriba el siguiente comando para apagar la máquina virtual:



        vm shutdown [options] <vm-name>



    Tenga en cuenta que una de las opciones de `vm shutdown` es `-p`, que mantendrá el recurso de proceso al apagar. **No** habilite esta opción, ya que la máquina virtual debe tener cancelado el aprovisionamiento para capturarla.



3. Ejecute el siguiente comando para capturar la imagen de VM:



        vm capture <vm-name> <target-image-name> --deleted



    Para capturar la imagen, se debe eliminar la máquina virtual. Puede utilizar `--deleted` o `-t` para ello.



4. Para confirmar que se ha capturado la imagen, puede comprobar la lista de imágenes de máquina virtual:



        vm image list | grep <target-image-name>



    La parte `| grep <target-image-name>` es opcional, pero le ayudará a encontrar la imagen más fácilmente en la lista.



El siguiente es un ejemplo de tutorial de captura de una máquina virtual, incluida la salida de terminal:


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



Visite la [página de documentación de CLI de Azure][] para conocer más detalles y comandos adicionales.


[página de documentación de CLI de Azure]: ../virtual-machines-command-line-tools.md

[Inicio de sesión en una máquina virtual con Linux]: virtual-machines-linux-how-to-log-on.md

[Acerca de las imágenes de máquina virtual en Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[Creación de una máquina virtual personalizada]: virtual-machines-create-custom.md

[Acoplamiento de un disco de datos a una máquina virtual]: storage-windows-attach-disk.md
 

<!---HONumber=July15_HO2-->