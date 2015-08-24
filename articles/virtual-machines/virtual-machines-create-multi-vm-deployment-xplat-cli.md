<properties
   pageTitle="Creación de una implementación de varias máquinas virtuales mediante la CLI de Azure | Microsoft Azure"
   description="Obtenga información acerca de cómo crear una implementación de varias máquinas virtuales con la CLI de Azure"
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst;kasing"/>

# Creación de una implementación de varias máquinas virtuales mediante la CLI de Azure

El siguiente script le mostrará cómo configurar una implementación de varios servicios en la nube y varias máquinas virtuales en una red virtual usando la interfaz de la línea de comandos de Azure (CLI de Azure).

La siguiente imagen le muestra cómo quedará la implementación una vez el script está completo:

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

El script crea una máquina virtual (**servervm**) en un servicio en la nube **servercs** con dos discos de datos adjuntos y dos máquinas virtuales (**clientvm1, clientvm2**) en el servicio en la nube **workercs**. Ambos servicios en la nube se colocan en la red virtual **samplevnet**. El servicio en la nube **servercs** también tiene un extremo configurado para la conectividad externa.

## Script de CLI para llevarlo a cabo
El código para realizar esta configuración es relativamente sencillo:

>[AZURE.NOTE]Es probable que necesite cambiar los nombres servercs y workercs del servicio en la nube para que éstos sean nombres únicos del servicio.

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-es-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small –n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-es-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-es-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

El código para anularla es igual de sencillo:

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*La opción –q suprime la confirmación interactiva de eliminación de objetos y -b limpia los discos o los blobs asociados con la máquina virtual.*

## Formas genéricas de los comandos usados

Aunque puede encontrar más información sobre cualquiera de los comandos CLI de Azure gracias a la opción –help, la forma genérica de cada comando usado anteriormente es la siguiente:

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## Pasos siguientes


* [Informática de código abierto y Linux en Azure](virtual-machines-linux-opensource.md)
* [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md)
 

<!---HONumber=August15_HO7-->