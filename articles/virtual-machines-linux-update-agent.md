<properties
	pageTitle="Actualización del agente Linux de Azure a la versión más reciente desde Github"
	description="Obtenga información acerca de cómo actualizar el agente Linux de Azure desde Github para la máquina virtual de Linux en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2015"
	ms.author="mingzhan"/>


# Actualización del agente Linux de Azure a la última versión desde Github

Para actualizar su [agente Linux de Azure](https://github.com/Azure/WALinuxAgent), debe:

1. Tener una máquina virtual que ejecuta Linux en Azure
2. Estar conectado a esa máquina virtual de Linux mediante SSH

> [AZURE.NOTE]Si va a realizar esta tarea desde un equipo con Windows, utilice Putty para SSH en el equipo Linux. Para obtener más información, consulte [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-how-to-log-on.md).

Las distribuciones de Linux aprobadas en Azure han colocado el paquete del agente Linux de Azure en sus repositorios, así que, si es posible, compruebe e instale primero la versión más reciente de ese repositorio de distribuciones.

Para Ubuntu, basta con escribir:
     
    #sudo apt-get install waagent

y en CentOS y Oracle Linux, escriba:

    #sudo yum install waagent

Normalmente, esto es todo lo que necesita, pero si por alguna razón necesita instalarlo desde https://github.com directamente, siga estos pasos.


## Instalar wget

Inicie sesión en la máquina virtual con SSH.

Instale wget \(hay algunas distribuciones que no lo instalan de forma predeterminada, como las versiones Redhat, CentOS y Oracle Linux 6.4 y 6.5\) y escriba `#sudo yum install wget` en la línea de comandos.


## Descarga de la versión más reciente

Abra [la versión del agente Linux de Azure en Github](https://github.com/Azure/WALinuxAgent/releases) en una página web y compruebe el número de versión más reciente, como 2.0.12. \(Puede buscar la versión actual escribiendo `#waagent --version`\).

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

La línea siguiente usa la versión 2.0.12 como ejemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## Conversión de waagent en ejecutable

    #chmod +x waagent

## Copia del nuevo archivo ejecutable en /usr/sbin/
    
    #sudo cp waagent /usr/sbin

Para CoreOS, use:

    #sudo cp waagent /usr/share/oem/bin/
 
## Reinicio del servicio de waagent

Para la mayoría de las distribuciones de Linux:

    #sudo service waagent restart

Para Ubuntu, use:

    #sudo service walinuxagent restart

Para CoreOS, use:

    #sudo systemctl restart waagent 

## Confirmación de la versión del agente Linux de Azure
   
    #waagent -version

Para CoreOS, es posible que no funcione el comando anterior.

Verá que la versión del agente Linux se ha actualizado a la versión nueva.

Para obtener más información sobre el agente Linux de Azure, consulte [archivo Léame del agente Linux de Azure](https://github.com/Azure/WALinuxAgent).




<!--HONumber=52-->
