---
title: "Instalación del controlador de la serie N de Azure para Linux | Microsoft Docs"
description: "Instalación de controladores de GPU de NVIDIA para máquinas virtuales de la serie N que se ejecutan en Linux en Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bdeb4d5ca1d9ff4d7dfd0961690412dd7530572a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalación de controladores de GPU de NVIDIA en máquinas virtuales de la serie N con Linux

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan Linux, instale controladores de gráficos de NVIDIA compatibles. Este artículo proporciona pasos de instalación de controlador después de implementar una VM de la serie N. También está disponible la información de instalación del controlador para las [máquinas virtuales Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Para conocer las especificaciones de máquina virtual de la serie N, las capacidades de almacenamiento y los detalles del disco, vea [Tamaño de máquinas virtuales para GPU Linux](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>Instalación de los controladores de GRID para máquinas virtuales NV

Para instalar los controladores de NVIDIA GRID en máquinas virtuales NV, realice una conexión SSH a cada máquina virtual y siga los pasos para su distribución de Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Ejecute el comando `lspci`. Compruebe que la tarjeta o tarjetas NVIDIA M60 son visibles como dispositivos PCI.

2. Instale las actualizaciones.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Deshabilite el controlador de kernel Nouveau que es incompatible con el controlador NVIDIA. (Utilice solo el controlador NVIDIA en máquinas virtuales NV). Para ello cree un archivo en `/etc/modprobe.d `llamado `nouveau.conf` con el siguiente contenido:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Reinicie la máquina virtual y vuelva a conectar. Salga del servidor X:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Descargue e instale el controlador de GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 

6. Cuando se le pregunte si desea ejecutar la utilidad nvidia-xconfig para actualizar el archivo de configuración de X, seleccione **Sí**.

7. Una vez completada la instalación, copie /etc/nvidia/gridd.conf.template en un nuevo archivo gridd.conf en la ubicación /etc/nvidia/

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Agregue lo siguiente a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Reinicie la máquina virtual y continúe para comprobar la instalación.


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Con base en CentOS 7.3 o Red Hat Enterprise Linux 7.3


1. Actualice el kernel y DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Deshabilite el controlador de kernel Nouveau que es incompatible con el controlador NVIDIA. (Utilice solo el controlador NVIDIA en máquinas virtuales NV). Para ello cree un archivo en `/etc/modprobe.d `llamado `nouveau.conf` con el siguiente contenido:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Reinicie la máquina virtual, vuelva a conectarse e instale la versión más reciente de Linux Integration Services para Hyper-V:
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
 
  tar xvzf lis-rpms-4.2.2-2.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
4. Vuelva a conectarse a la máquina virtual y ejecute el comando `lspci`. Compruebe que la tarjeta o tarjetas NVIDIA M60 son visibles como dispositivos PCI.
 
5. Descargue e instale el controlador de GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 
6. Cuando se le pregunte si desea ejecutar la utilidad nvidia-xconfig para actualizar el archivo de configuración de X, seleccione **Sí**.

7. Una vez completada la instalación, copie /etc/nvidia/gridd.conf.template en un nuevo archivo gridd.conf en la ubicación /etc/nvidia/
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Agregue lo siguiente a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Reinicie la máquina virtual y continúe para comprobar la instalación.

### <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador


Para consultar el estado del dispositivo de GPU, acceda mediante SSH a la máquina virtual y ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador. 

Verá un resultado similar al siguiente:

![Estado del dispositivo de NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Si necesita un servidor X11 para conexiones remotas a la máquina virtual NV, [x11vnc](http://www.karlrunge.com/x11vnc/) es el recomendado porque permite la aceleración de hardware de gráficos. El BusID del dispositivo M60 tiene que agregarse manualmente al archivo xconfig (`etc/X11/xorg.conf` en Ubuntu 16.04 LTS, `/etc/X11/XF86config` en 7.3 CentOS o Red Hat Enterprise Server 7.3). Agregue una sección `"Device"` similar a la siguiente:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Además, actualice su sección `"Screen"` para usar este dispositivo.
 
Encontrará el BusID ejecutando

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
El BusID puede cambiar cuando se reasigna o reinicia una máquina virtual. Por lo tanto, puede ser conveniente utilizar un script para actualizar el BusID en la configuración de X11 cuando se reinicie una máquina virtual. Por ejemplo:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Este archivo se puede invocar como raíz en el arranque mediante la creación de una entrada para él en `/etc/rc.d/rc3.d`.


## <a name="install-cuda-drivers-for-nc-vms"></a>Instalación de controladores CUDA para VM de NC

Estos son los pasos para instalar controladores NVIDIA en máquinas virtuales Linux NC desde la versión 8.0 del kit de herramientas de NVIDIA CUDA. 

Los desarrolladores de C y C++, si lo desean, pueden instalar el kit de herramientas completo para crear aplicaciones aceleradas por GPU. Para obtener más información, consulte la [guía de instalación de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Los vínculos de descarga de controladores de CUDA que se proporcionan aquí están actualizados en el momento de la publicación. Para ver los controladores de CUDA más recientes, visite el sitio web de [NVIDIA](http://www.nvidia.com/).
>

Para instalar el kit de herramientas de CUDA, realice una conexión SSH a cada máquina virtual. Para comprobar que el sistema dispone de una GPU compatible con CUDA, ejecute el siguiente comando:

```bash
lspci | grep -i NVIDIA
```
Verá un resultado similar al siguiente ejemplo (mostrando una tarjeta NVIDIA Tesla K80):

![Resultado del comando de Ispci](./media/n-series-driver-setup/lspci.png)

Luego, ejecute los comandos de instalación específicos de su distribución.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Descargue e instale los controladores de CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  La instalación puede tardar varios minutos.

2. Para instalar (opcional) el kit de herramientas CUDA completo, escriba:

  ```bash
  sudo apt-get install cuda
  ```

3. Reinicie la máquina virtual y continúe para comprobar la instalación.

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Con base en CentOS 7.3 o Red Hat Enterprise Linux 7.3

1. Obtenga las actualizaciones. 

  ```bash
  sudo yum update

  sudo reboot
  ```
2. Vuelva a conectarse a la máquina virtual e instale la versión más reciente de Linux Integration Services para Hyper-V.

  > [!IMPORTANT]
  > Si instaló una imagen HPC basada en CentOS en una máquina virtual NC24r, vaya al paso 3. Dado que los controladores RDMA de Azure y Linux Integration Services ya están instalados en la imagen, LIS no debe actualizarse y la actualizaciones del kernel están deshabilitadas de forma predeterminada.
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.1.tar.gz
 
  tar xvzf lis-rpms-4.2.1.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Vuelva a conectarse a la VM y continúe con la instalación con los siguientes comandos:

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  La instalación puede tardar varios minutos. 

4. Para instalar (opcional) el kit de herramientas CUDA completo, escriba:

  ```bash
  sudo yum install cuda
  ```

5. Reinicie la máquina virtual y continúe para comprobar la instalación.


### <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador


Para consultar el estado del dispositivo de GPU, acceda mediante SSH a la máquina virtual y ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador. 

Verá un resultado similar al siguiente:

![Estado del dispositivo de NVIDIA](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>Actualizaciones de controladores CUDA

Se recomienda actualizar periódicamente los controladores CUDA después de la implementación.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```


#### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>Con base en CentOS 7.3 o Red Hat Enterprise Linux 7.3

```bash
sudo yum update

sudo reboot
```



## <a name="troubleshooting"></a>Solución de problemas

* Hay un problema conocido con los controladores CUDA en las máquinas virtuales serie N de Azure que ejecutan el kernel de Linux 4.4.0-75 en Ubuntu 16.04 LTS. Si va a actualizar desde una versión anterior del kernel, actualice al menos a la versión 4.4.0-77. 



## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre las GPU de NVIDIA en las máquinas virtuales de la serie N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para máquinas virtuales de Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para máquinas virtuales de Azure NV)

* Para capturar una imagen de una VM Linux con los controladores de NVIDIA instalados, vea [Procedimiento para generalizar y capturar una máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
