<properties
   pageTitle="Acceso al Id. de VM"
   description="Se describe el acceso y uso del identificador único de máquina virtual de Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Acceso y uso del identificador único de máquina virtual de Azure

El identificador único de máquina virtual de Azure es un identificador de 128 bits codificado y almacenado en todas las SMBIOS de la máquina virtual IaaS de Azure; actualmente se puede leer mediante comandos BIOS de la plataforma.

El identificador único de máquina virtual de Azure es una propiedad de solo lectura. El identificador único de máquina virtual de Azure no cambiará durante el cierre de reinicio (tanto si se ha planeado como si no), durante el inicio o la detención de la desasignación, durante la recuperación del servicio o la restauración local. Sin embargo, si la máquina virtual es una instantánea y se copia para crear una instancia, se configurará un nuevo identificador de máquina virtual de Azure.

> [AZURE.NOTE] Si dispone de máquinas virtuales que se ejecutan desde que se implantó esta nueva característica (18 de septiembre de 2014), reinícielas para obtener automáticamente un identificador único de Azure.


Para obtener acceso al identificador único de la máquina virtual de Azure desde dentro de la máquina virtual:


## Crear una VM
 

Para obtener más información, consulte [Create a Virtual Machine](virtual-machines-linux-creation-choices.md) (Creación de una máquina virtual).


## Conexión a la máquina virtual
 

Para obtener más información, consulte [SSH from Linux](virtual-machines-linux-ssh-from-linux.md) (SSH desde Linux).


## Consulta de un identificador único de máquina virtual

Comando (en el ejemplo se emplea **Ubuntu**):

    sudo dmidecode | grep UUID
    
Resultados esperados del ejemplo:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Debido a la ordenación de bits de Big Endian, el identificador único real de la máquina virtual en este caso será:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
El identificador único de máquina virtual de Azure se puede usar en escenarios distintos, tanto si la máquina virtual se ejecuta en Azure como si lo hace de forma local, y puede ayudar en los requisitos de las licencias, de los informes o del seguimiento general que pueda tener en sus implementaciones IaaS de Azure. Muchos proveedores independientes de software que crean aplicaciones y las certifican en Azure podrían solicitar que se identifique una máquina virtual de Azure durante su ciclo de vida y se averigüe si la máquina virtual se ejecuta en Azure, de forma local o en otros proveedores en la nube. Con este identificador de plataforma, por ejemplo, se puede detectar si el software tiene la licencia adecuada o correlacionar los datos de la máquina virtual a su origen (por ejemplo, para ayudar a configurar las métricas correctas para la plataforma correcta, a efectuar un seguimiento y a correlacionar estas métricas, entre otros usos).

<!---HONumber=AcomDC_0323_2016-->