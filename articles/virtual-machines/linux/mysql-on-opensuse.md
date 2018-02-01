---
title: "Instalación de MySQL en una VM con OpenSUSE en Azure | Microsoft Docs"
description: "Aprenda a instalar MySQL en una máquina virtual con OpenSUSE Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: cynthn
ms.openlocfilehash: 88bd895cb3a384f1ada0394fe2da206aca86b981
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure

[MySQL](http://www.mysql.com) es una conocida base de datos SQL de código abierto. En este tutorial se muestra cómo crear una máquina virtual que disponga de OpenSUSE Linux y, a continuación, instale MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, necesitará la CLI de Azure versión 2.0 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Creación de una máquina virtual que ejecuta OpenSUSE Linux

En primer lugar, cree un grupo de recursos. En este ejemplo, se asigna el nombre *mySQSUSEResourceGroup* al grupo de recursos y se crea en la región *East US*.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Cree la máquina virtual. En este ejemplo, se asigna el nombre *myVM* a la VM. Se va a usar el tamaño de VM *Standard_D2s_v3*, pero debe elegir el [Tamaño de VM](sizes.md) que crea más adecuado para la carga de trabajo.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

También debe agregar una regla al grupo de seguridad de red para permitir el tráfico a través del puerto 3306 para MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Usará SSH para conectarse a la máquina virtual. En este ejemplo, la dirección IP pública de la máquina virtual es *10.111.112.113*. Puede ver la dirección IP en la salida de cuando se creó la VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Actualizar la VM
 
Después de conectarse a la VM, instale las actualizaciones y revisiones del sistema. 
   
```bash
sudo zypper update
```

Siga las indicaciones para actualizar la VM.

## <a name="install-mysql"></a>Instalar MySQL 


Instale MySQL en la VM a través de SSH. Responda a las solicitudes según corresponda.

```bash
sudo zypper install mysql
```
 
Establezca MySQL para que se inicie con el arranque del sistema. 

```bash
sudo systemctl enable mysql
```
Compruebe que MySQL esté habilitado.

```bash
systemctl is-enabled mysql
```

Debe devolver: enabled.


## <a name="mysql-password"></a>Contraseña de MySQL

después de la instalación, la contraseña raíz de MySQL se encuentra vacía de forma predeterminada. Ejecute el script **mysql\_secure\_installation** para proteger MySQL. El script le solicitará que cambie la contraseña raíz de MySQL, quite las cuentas de usuario anónimas, deshabilite los datos de inicio de sesión raíz remotos, quite las bases de datos de prueba y vuelva a cargar la tabla de privilegios. 


```bash
mysql_secure_installation
```

## <a name="log-in-to-mysql"></a>Inicie sesión en MySQL

Ahora puede iniciar sesión e introducir el símbolo del sistema de MySQL.

```bash  
mysql -u root -p
```
Esto cambia al símbolo del sistema de MySQL, donde puede emitir instrucciones SQL para interactuar con la base de datos.

Ahora, cree un nuevo usuario de MySQL.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
El punto y coma (;) al final de la línea es fundamental para terminar el comando.


## <a name="create-a-database"></a>Creación de una base de datos


Cree una base de datos y conceda los permisos de usuario `mysqluser`.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Los nombres de usuario y contraseñas de la base de datos solo los usan los scripts que se conectan a la base de datos.  Los nombres de cuenta de usuario de la base de datos no representan necesariamente las cuentas de usuario reales en el sistema.

Habilite el inicio de sesión desde otro equipo. En este ejemplo, la dirección IP del equipo desde el que se va a iniciar sesión es *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Para salir de la utilidad administración de base de datos MySQL, escriba:

```    
quit
```


## <a name="next-steps"></a>pasos siguientes
Para consultar detalles sobre MySQL, vea la [documentación de MySQ](http://dev.mysql.com/doc/index-topic.html).




