<properties
   pageTitle="Administrar máquinas virtuales con la CLI | Microsoft Azure"
   description="Automatizar la administración de las máquinas virtuales del Administrador de recursos de Azure mediante la Interfaz de la línea de comandos (CLI) de Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/07/2015"
   ms.author="danlep"/>

# Administre las máquinas virtuales del Administrador de recursos mediante la CLI de Azure para Mac, Linux y Windows


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-how-to-automate-azure-resource-manager.md).

Puede automatizar muchas de las tareas cotidianas para administrar las máquinas virtuales gracias a la Interfaz de la línea de comandos (CLI) de Azure para Mac, Linux y Windows. En este artículo se proporcionan comandos de ejemplo para las tareas más sencillas, así como vínculos a artículos que muestran los comandos para tareas más complejas.

>[AZURE.NOTE]Si aún no ha instalado la CLI de Azure, puede obtener instrucciones para hacerlo [aquí](../xplat-cli-install.md); asimismo, puede aprender a conectarse a su suscripción [aquí](../xplat-cli-connect.md). También debe configurar la CLI en el modo de Administrador de recursos de Azure (arm).

## Utilización de los comandos de ejemplo
Tendrá que reemplazar parte del texto en los comandos con texto que sea adecuado para su entorno. Los símbolos < and > indican texto que se debe reemplazar. Al reemplazar el texto, quite los símbolos pero deje las comillas en su lugar.

> [AZURE.NOTE]Si desea almacenar y manipular mediante programación la salida de los comandos de consola, puede usar una herramienta de análisis de JSON como **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** o bibliotecas de idioma adecuadas para la tarea.

## Visualización de información sobre una máquina virtual

Es una tarea básica que utilizará a menudo. Utilícelo para obtener información acerca de una máquina virtual, realizar tareas en una máquina virtual y obtener el resultado para almacenarlo en una variable.

Para obtener información acerca de la máquina virtual, ejecute este comando y reemplace todo el contenido de las comillas, incluidos los caracteres < and >:

     azure vm show -g <group name> -n <virtual machine name>

Para almacenar el resultado en una variable $vm como un documento JSON, ejecute:

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

o bien, puede canalizar el stdout a un archivo.

## Inicio de sesión en una máquina virtual Linux

Normalmente los equipos Linux están conectados a través de SSH. Para obtener más información, consulte [Uso de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key.md). Información general del Administrador de recursos de Azure
## Detención de una máquina virtual

Ejecute este comando:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Utilice este parámetro para mantener la IP virtual (VIP) del servicio de nube en caso de que sea la última máquina virtual en ese servicio en la nube. <br><br> Si utiliza el parámetro StayProvisioned, se le facturará por la máquina virtual.

## Inicio de una máquina virtual

Ejecute este comando: azure vm start de información general del Administrador de recursos de Azure <group name> <virtual machine name>

## Anexión de un disco de datos

También tendrá que decidir si desea adjuntar un disco nuevo o uno que contenga los datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta en el mismo comando.

Para adjuntar un disco nuevo, ejecute este comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Para adjuntar un disco de datos existente, ejecute este comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Creación de una máquina virtual Linux

Para crear una nueva máquina virtual basada en Linux, va a necesitar tener varios valores a mano, incluido el nombre de grupo de recursos, una ubicación, un nombre de imagen, un nombre de máquina virtual y una cuenta de almacenamiento para almacenar la imagen .vhd de respaldo. Cuando disponga de la información que desea utilizar, la CLI de Azure puede crear una sesión interactiva que le solicita esos valores escribiendo:

    azure vm create

Por supuesto, si ya dispone de esos valores, puede encontrar los modificadores adecuados para pasarlos directamente si escribe `azure help vm create`.

## Pasos siguientes

* Para conseguir más ejemplos acerca del uso de la CLI de Azure con el modo de Administrador de recursos de Azure, consulte [Usar la CLI de Microsoft Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](xplat-cli-azure-resource-manager.md).

* Para obtener más información acerca de los recursos de Azure y sus conceptos, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

<!---HONumber=Oct15_HO3-->