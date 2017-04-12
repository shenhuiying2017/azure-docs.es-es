---
title: "Instalación de MongoDB en una máquina virtual Linux mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Aprenda a instalar y configurar MongoDB en una máquina virtual Linux en Azure con el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/20/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ec6b94eec9364f25a630f290316048c8ac838b62
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Procedimiento de instalación y configuración de MongoDB en una máquina virtual Linux con la CLI de Azure 1.0
[MongoDB](http://www.mongodb.org) es una conocida base de datos NoSQL de código abierto y alto rendimiento. En este artículo, se muestra cómo instalar y configurar MongoDB en una máquina virtual Linux en Azure con el modelo de implementación de Resource Manager. Se muestran algunos ejemplos detallados de:

* [Instalación y configuración manuales de una instancia básica de MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Creación de una instancia básica de MongoDB mediante una plantilla de Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Creación de un clúster complejo con particiones de MongoDB con conjuntos de réplicas mediante una plantilla de Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- CLI de Azure 1.0: la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI de última generación para el modelo de implementación de administración de recursos


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalación y configuración manuales de MongoDB en una máquina virtual
MongoDB [proporciona instrucciones de instalación](https://docs.mongodb.com/manual/administration/install-on-linux/) para distribuciones de Linux, incluidos Red Hat/CentOS, SUSE, Ubuntu y Debian. En el ejemplo siguiente, se crea una `CentOS`máquina virtual mediante una clave SSH almacenada en `~/.ssh/id_rsa.pub`. Responda a los mensajes que solicitan el nombre de la cuenta de almacenamiento, el nombre DNS y las credenciales de administrador:

```azurecli
azure vm quick-create --ssh-publickey-file ~/.ssh/id_rsa.pub --image-urn CentOS
```

Inicie sesión en la máquina virtual con la dirección IP pública que se muestra al final del anterior paso de creación de la máquina virtual:

```bash
ssh azureuser@40.78.23.145
```

Para agregar los orígenes de instalación para MongoDB, cree un archivo de repositorio `yum` como sigue:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Abra el archivo de repositorio de MongoDB para editarlo. Agregue las siguientes líneas:

```sh
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Instale MongoDB mediante `yum` como se indica a continuación:

```bash
sudo yum install -y mongodb-org
```

De forma predeterminada, SELinux está aplicado en las imágenes de CentOS, lo que impide el acceso de MongoDB. Instale herramientas de administración de directivas y configure SELinux para permitir que MongoDB funcione en su puerto TCP predeterminado, 27017, como sigue. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie el servicio de MongoDB como se muestra a continuación:

```bash
sudo service mongod start
```

Para comprobar la instalación de MongoDB, conéctese por medio del cliente `mongo` local:

```bash
mongo
```

Ahora pruebe la instancia de MongoDB agregando algunos datos y después buscando:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Si lo desea, configure MongoDB para que se inicie automáticamente durante un reinicio del sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Creación de una instancia básica de MongoDB en CentOS mediante una plantilla
Puede crear una instancia básica de MongoDB una sola máquina virtual CentOS con la siguiente plantilla de inicio rápido de Azure en GitHub. Esta plantilla usa la extensión de script personalizado para Linux para agregar un repositorio `yum` a la máquina Virtual CentOS recién creada y después instalar MongoDB.

* [Instancia básica de MongoDB en CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la región `WestUS`. Escriba sus propios valores, como se indica a continuación:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> La CLI de Azure regresa a un símbolo del sistema a los pocos segundos de crearse la implementación, pero la instalación y la configuración tardan unos minutos en completarse. Para comprobar el estado de la implementación con `azure group deployment show myResourceGroup`, escriba el nombre de su grupo de recursos según corresponda. Espere a que `ProvisioningState` muestre "Succeeded" (Correcto) antes de intentar conectarse mediante SSH a la máquina virtual.
> 
> 

Una vez finalizada la implementación, conéctese mediante SSH a la máquina virtual. Obtenga la dirección IP de la máquina virtual con el comando `azure vm show`, como en el ejemplo siguiente:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

Hacia el final de la salida, se muestra `Public IP address`. Conéctese mediante SSH a la máquina virtual con la dirección IP de su máquina virtual:

```bash
ssh azureuser@138.91.149.74
```

Para comprobar la instalación de MongoDB, conéctese por medio del cliente `mongo` local como sigue:

```bash
mongo
```

Ahora pruebe la instancia agregando algunos datos y después buscando:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Creación de un clúster complejo con particiones de MongoDB en CentOS mediante una plantilla
Puede crear un clúster complejo con particiones de MongoDB con la siguiente plantilla de inicio rápido de Azure en GitHub. Esta plantilla sigue los [procedimientos recomendados para clústeres con particiones de MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para proporcionar alta disponibilidad y redundancia. La plantilla crea dos particiones, con tres nodos en cada conjunto de réplicas. También se crean un conjunto de réplicas de servidor de configuración con tres nodos, más dos servidores de ruta `mongos` para proporcionar coherencia a las aplicaciones entre las particiones.

* [Clúster con particiones de MongoDB en CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Para implementar este clúster complejo con particiones de MongoDB, se requieren más de 20 núcleos, lo que suele ser el número predeterminado por región para una suscripción. Abra una solicitud de soporte técnico de Azure para aumentar el número de núcleos.
> 
> 

En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la región `WestUS`. Escriba sus propios valores, como se indica a continuación:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> La CLI de Azure regresa a un símbolo del sistema a los pocos segundos de crearse la implementación, pero la instalación y la configuración pueden tardar más de una hora en completarse. Para comprobar el estado de la implementación con `azure group deployment show myResourceGroup`, ajuste el nombre de su grupo de recursos según corresponda. Espere a que `ProvisioningState` muestre "Succeeded" (Correcto) antes de intentar conectarse a las máquinas virtuales.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
En estos ejemplos, puede conectarse a la instancia de MongoDB localmente desde la máquina virtual. Si desea conectarse a la instancia de MongoDB desde otra máquina virtual o una red, asegúrese de que [se creen las reglas de grupo de seguridad de red](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) correspondientes.

Para más información sobre la creación de plantillas, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Las plantillas de Azure Resource Manager usan la extensión de script personalizado para descargar y ejecutar scripts en las máquinas virtuales. Para más información, consulte [Uso de la extensión de script personalizado de Azure con máquinas virtuales Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


