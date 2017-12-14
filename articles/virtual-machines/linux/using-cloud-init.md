---
title: "Información general de la compatibilidad con cloud-init para máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Información general de las funcionalidades de cloud-init en Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: ce238a3093e29c3091f979bbd9e80f28495307da
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Compatibilidad con cloud-init para máquinas virtuales en Azure
En este artículo se explica la compatibilidad que existe para [cloud-init](https://cloudinit.readthedocs.io) para configurar una máquina virtual (VM) o conjuntos de escalado de máquinas virtuales (VMSS) en el momento del aprovisionamiento en Azure. Estos scripts cloud-init se ejecutan durante el primer arranque una vez que Azure ha aprovisionado los recursos.  

## <a name="cloud-init-overview"></a>Introducción a cloud-init
[cloud-init](https://cloudinit.readthedocs.io) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Dado que se llama a cloud-init durante el proceso de arranque inicial, no hay pasos adicionales o agentes requeridos que aplicar a la configuración.  Para más información sobre cómo dar formato correctamente a sus archivos `#cloud-config`, consulte el [sitio de documentación de cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Los archivos `#cloud-config` son archivos de texto codificados en base64.

cloud-init también funciona entre distribuciones. Por ejemplo, no use **apt-get install** o **yum install** para instalar un paquete. En su lugar, puede definir una lista de paquetes que se van a instalar. Cloud-init usará automáticamente la herramienta de administración de paquetes nativos para la distribución de Linux (distro) que seleccione.

 Estamos trabajando activamente con nuestros asociados de distribuciones de Linux certificados para disponer de imágenes con cloud-init habilitado en Azure Marketplace. Estas imágenes harán que las implementaciones y configuraciones de cloud-init funcionen perfectamente con las máquinas virtuales y los conjuntos de escalado de máquinas virtuales (VMSS). En la tabla siguiente se describe la disponibilidad actual de imágenes con cloud-init habilitado en la plataforma de Azure:

| Publicador | Oferta | SKU | Versión | Preparado para cloud-init
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |más reciente |yes | 
|Canonical |UbuntuServer |14.04.5-LTS |más reciente |yes |
|CoreOS |CoreOS |Stable |más reciente |yes |
|OpenLogic |CentOS |7-CI |más reciente |Vista previa |
|Redhat |RHEL |7-RAW-CI |más reciente |Vista previa |

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>¿Cuál es la diferencia entre cloud-init y el agente de Linux (WALA)?
WALA es un agente específico de la plataforma de Azure que se usa para aprovisionar y configurar VM y controlar extensiones de Azure. Estamos mejorando la tarea de configuración de VM para usar cloud-init en lugar del agente de Linux para permitir que los clientes existentes de cloud-init usen sus scripts actuales de cloud-init.  Si tiene inversiones existentes en scripts de cloud-init para configurar sistemas Linux, **no se necesitan configuraciones adicionales** para habilitarlos. 

Si no incluye el modificador de la línea de comandos de Azure `--custom-data` en el momento del aprovisionamiento, WALA toma los parámetros de aprovisionamiento de VM mínimos necesarios para aprovisionar la VM y completar la implementación con los valores predeterminados.  Si hace referencia al modificador `--custom-data` de cloud-init, lo que se encuentra en los datos personalizados (configuraciones individuales o script completo) invalida los valores predeterminados definidos de WALA. 

Las configuraciones de WALA de VM tienen limitación de tiempo para trabajar dentro del tiempo máximo de aprovisionamiento de VM.  Configuraciones de cloud-init que se aplican a VM no tienen restricciones de tiempo y no producirán un error de implementación porque se cumpla el tiempo de espera. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implementación de una máquina Virtual con cloud-init habilitado
La implementación de una máquina virtual con cloud-init habilitado es tan simple como hacer referencia a una distribución con cloud-init habilitado durante la implementación.  Los encargados de mantener la distribución de Linux tienen que elegir habilitar e integrar cloud-init en sus imágenes básicas publicadas de Azure. Una vez que haya confirmado que la imagen que desea implementar tiene habilitado cloud- init, puede usar la CLI de Azure para implementar la imagen. 

El primer paso para implementar esta imagen es crear un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
El paso siguiente es crear un archivo en el shell actual, denominado *cloud-init.txt* y pegar la siguiente configuración. Para este ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. Escriba `sensible-editor cloud-init.txt` para crear el archivo y ver una lista de editores disponibles. Elija el número 1 para utilizar el editor **nano**. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea:

```yaml
#cloud-config
package_upgrade: true
packages:
  -httpd
```
Presione `ctrl-X` para salir del archivo, escriba `y` para guardar el archivo y presione `enter` para confirmar el nombre del archivo al salir.

El paso final es crear la máquina virtual con el comando [az vm create](/cli/azure/vm#az_vm_create). 

En el ejemplo siguiente, se crea una máquina virtual denominada *centos74* y las claves SSH si aún no existen en una ubicación de claves predeterminada. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  Use el parámetro `--custom-data` para pasar su archivo de configuración cloud-init. Proporcione la ruta de acceso completa a la configuración de *cloud-init.txt* si guardó el archivo fuera de su directorio de trabajo actual. En el ejemplo siguiente se crea una VM denominada *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Cuando se haya creado la VM, la CLI de Azure mostrará información específica a la implementación. Anote el valor de `publicIpAddress`. Esta dirección se utiliza para tener acceso a la máquina virtual.  Transcurre algo de tiempo hasta que la máquina virtual se crea, los paquetes se instalan y la aplicación se inicia. Hay tareas en segundo plano que continúan ejecutándose después de que la CLI de Azure vuelve a abrir el símbolo del sistema. Puede usar SSH en la VM y usar los pasos descritos en la sección de solución de problemas para ver los registros de cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Solución de problemas de cloud-init
Una vez que se ha aprovisionado la VM, cloud-init se ejecuta en todos los módulos y el script definido en `--custom-data` con el fin de configurar dicha VM.  Si tiene que solucionar algún error u omisión de la configuración, debe buscar el nombre del módulo (`disk_setup` o `runcmd`,0 por ejemplo) en el registro de cloud-init, ubicado en **/var/log/cloud-init.log**.

> [!NOTE]
> No todos los errores de módulo dan como resultado un error irrecuperable en la configuración global de cloud-init. Por ejemplo, si se usa el módulo `runcmd` y se produce un error en el script, cloud-init seguirá notificando un aprovisionamiento correcto porque se ejecutó el módulo runcmd.

Para más detalles sobre el registro de cloud-init, consulte la [documentación de cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

## <a name="next-steps"></a>Pasos siguientes
Para ejemplos de cloud-init de cambios de configuración, vea los siguientes documentos:
 
- [Incorporación de otro usuario de Linux a una máquina virtual](cloudinit-add-user.md)
- [Ejecución de un administrador de paquetes para actualizar los existentes durante el primer arranque](cloudinit-update-vm.md)
- [Cambio del nombre de host de la máquina virtual local](cloudinit-update-vm-hostname.md) 
- [Instalación de un paquete de aplicación, actualización de los archivos de configuración e inserción de claves](tutorial-automate-vm-deployment.md)