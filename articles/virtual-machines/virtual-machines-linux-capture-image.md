---
title: "Captura de una máquina virtual Linux para usarla como plantilla | Microsoft Docs"
description: "Obtenga información sobre cómo capturar una imagen de una máquina virtual de Azure basada en Linux creada con el modelo de implementación de Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 93119596f5a9fb3b6cf405f6de5d2ecccd45f298


---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Captura de una máquina virtual Linux que se ejecuta en Azure
Siga los pasos de este artículo para generalizar y capturar la máquina virtual (VM) Linux de Azure en el modelo de implementación de Resource Manager. Cuando se generaliza la máquina virtual, se quita la información de la cuenta personal y se prepara la máquina virtual para usarla como imagen. A continuación, se captura la imagen de un disco duro virtual (VHD) generalizado para el SO, discos duros virtuales para los discos de datos asociados y un [plantilla de Resource Manager](../azure-resource-manager/resource-group-overview.md) para las implementaciones de nuevas máquinas virtuales. 

Para crear máquinas virtuales mediante la imagen, configure los recursos de red de cada máquina virtual nueva y use la plantilla (un archivo de notación de objetos JavaScript o JSON) para implementarla a partir de las imágenes de VHD capturadas. De esta forma se puede replicar una máquina virtual con su configuración de software actual, de forma parecida a como se usan las imágenes en Azure Marketplace.

> [!TIP]
> Si desea crear una copia de la máquina virtual de Linux existente con su estado especializado para copia de seguridad o depuración, consulte [Creación de una copia de una máquina virtual Linux que se ejecuta en Azure](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Y si desea cargar un VHD de Linux desde una máquina virtual local, consulte [Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de que se cumplen los siguientes requisitos previos:

* **VM de Azure creada en el modelo de implementación de Resource Manager**: si no ha creado una VM de Linux, puede usar el [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), la [CLI de Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [plantillas de Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
  
    Configure la máquina virtual como considere necesario. Por ejemplo, [agregue discos de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), aplique actualizaciones e instale aplicaciones. 
* **CLI de Azure**: instale la [CLI de Azure](../xplat-cli-install.md) en un equipo local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Paso 1: Quitar el agente Linux de Azure
En primer lugar, ejecute el comando **waagent** con el parámetro **deprovision** en la máquina virtual Linux. Este comando elimina los archivos y los datos para que la máquina virtual esté lista para la generalización. Para más información, consulte [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Conexión a una máquina virtual Linux de Azure mediante un cliente SSH.
2. En la ventana de SSH, escriba el siguiente comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. No garantiza que se haya borrado toda información sensible de la imagen o que sea adecuada para su redistribución.
 
3. Escriba **y** para continuar. Puede agregar el parámetro **-force** para evitar este paso de confirmación.
4. Una vez que se complete el comando, escriba **exit**. Este paso cierra el cliente de SSH.

## <a name="step-2-capture-the-vm"></a>Paso 2: Capturar la máquina virtual
Use la CLI de Azure para generalizar y capturar la máquina virtual. En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen **myResourceGroup**, **myVnet** y **myVM**.

1. En el equipo local, abra la CLI de Azure e [inicie sesión en su suscripción de Azure](../xplat-cli-connect.md). 
2. Asegúrese de que está en modo de Resource Manager.
   
    ```azurecli
    azure config mode arm
    ```
3. Detenga la máquina virtual que ya desaprovisionó con el comando siguiente:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalice la máquina virtual con el comando siguiente:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Ahora ejecute el comando **azure vm capture**, que captura la máquina virtual. En el ejemplo siguiente, los discos duros virtuales de la imagen se capturan con nombres empiezan por **MyVHDNamePrefix** y la opción **-t** especifica una ruta de acceso a la plantilla **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > De forma predeterminada, se crean los archivos del disco duro virtual de imagen en la misma cuenta de almacenamiento que usa la máquina virtual original. Use la *misma cuenta de almacenamiento* para almacenar los discos duros virtuales de las máquinas virtuales nuevas cree a partir de la imagen. 

6. Para buscar la ubicación de una imagen capturada, abra la plantilla de JSON en un editor de texto. En **storageProfile**, busque el **identificador URI** de la **imagen** ubicado en el contenedor **system**. Por ejemplo, el identificador URI de la imagen del disco del sistema operativo es similar a `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Paso 3: Crear una máquina virtual a partir de la imagen capturada
Ahora use la imagen con una plantilla para crear una máquina virtual Linux. Estos pasos muestran cómo usar la CLI de Azure y la plantilla del archivo JSON que capturó para crear la máquina virtual en una nueva red virtual.

### <a name="create-network-resources"></a>Crear recursos de red
Para usar la plantilla, deberá configurar primero una red virtual y la tarjeta de interfaz de red para la nueva máquina virtual. Se recomienda crear un grupo de recursos para estos recursos en la ubicación en que se almacena la imagen de la máquina virtual. Ejecute comandos similares a los siguientes, sustituyendo los nombres por sus recursos y por una ubicación de Azure adecuada ("centralus" en estos comandos):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Obtención del identificador de la NIC
Para implementar una máquina virtual desde la imagen mediante el JSON que guardó durante la captura, se necesita el identificador de la NIC. Para obtenerla, ejecute el siguiente comando:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

El **identificador** de la salida es similar a `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Creación de una VM
Ahora ejecute el siguiente comando para crear la máquina virtual desde la imagen capturada de la máquina virtual. Use el parámetro **-f** para especificar la ruta de acceso al archivo JSON de la plantilla que guardó.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

En la salida del comando, se le solicita que especifique un nuevo nombre de la máquina virtual, el nombre de usuario de administrador y la contraseña, así como el identificador de la NIC que creó.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

El ejemplo siguiente muestra lo que se ve en una implementación correcta:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Comprobar la implementación
Ahora conéctese por SSH a la máquina virtual que creó para comprobar la implementación y empezar a usar la nueva máquina virtual. Para conectarse mediante SSH, busque la dirección IP de la máquina virtual que creó mediante la ejecución del comando siguiente:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

La dirección IP pública aparece en la salida del comando. De forma predeterminada, se conecta a la máquina virtual de Linux mediante SSH en el puerto 22.

## <a name="create-additional-vms"></a>Creación de máquinas virtuales adicionales
Use la imagen capturada y la plantilla para implementar máquinas virtuales adicionales siguiendo los pasos de la sección anterior. Otras opciones para crear máquinas virtuales a partir de la imagen incluyen el uso de una plantilla de inicio rápido o la ejecución del comando **azure vm create**.

### <a name="use-the-captured-template"></a>Uso de la plantilla capturada
Para usar la imagen capturada y la plantilla, siga estos pasos (que se detallan en la sección anterior):

* Asegúrese de que la imagen de la máquina virtual se encuentra en la misma cuenta de almacenamiento que hospeda el disco duro virtual de la máquina virtual.
* Copie el archivo JSON de la plantilla y especifique un nombre único para el disco del sistema operativo del nuevo disco duro virtual (o discos duros virtuales) de la máquina virtual. Por ejemplo, en **storageProfile**, en **vhd**, en **uri**, especifique un nombre único para el disco duro virtual **osDisk**, similar a `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Cree una NIC en la misma red virtual o en otra.
* Con el archivo JSON de la plantilla modificada, cree una implementación del grupo de recursos en el que configuró la red virtual.

### <a name="use-a-quickstart-template"></a>Uso de una plantilla de inicio rápido
Si desea que la red se configure automáticamente al crear una máquina virtual a partir de la imagen, puede especificar los recursos en una plantilla. Por ejemplo, vea la [plantilla 101-vm-from-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) de GitHub. Esta plantilla crea una máquina virtual desde la imagen personalizada, así como la red virtual necesaria, la dirección IP pública y la tarjeta de interfaz de red de recursos. Para ver un tutorial acerca del uso de la plantilla en el Portal de Azure, consulte [Crear una máquina virtual desde una imagen personalizada mediante una plantilla de Resource Manager](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Uso del comando azure vm create
Normalmente, la forma más fácil de crear una máquina virtual a partir de la imagen es usar una plantilla de Resource Manager. Sin embargo, la máquina virtual se puede crear *imperativamente* con el comando **azure vm create** con el parámetro **-Q** (**--image-urn**). Si usa este método, también puede usar el parámetro **-d** (**--os-disk-vhd**) para especificar la ubicación del archivo .vhd del sistema operativo de la nueva máquina virtual. Este archivo debe estar en el contenedor de discos duros virtuales de la cuenta de almacenamiento donde se almacena el archivo del disco duro virtual de la imagen. El comando copia el disco duro virtual de la nueva máquina virtual automáticamente en el contenedor de **discos duros virtuales**.

Antes de ejecutar **azure vm create** con la imagen, siga estos pasos:

1. Cree un grupo de recursos o identifique un grupo de recursos existente para la implementación.
2. Cree un recurso de direcciones IP públicas y un recurso de tarjeta de interfaz de red para la nueva máquina virtual. Para conocer los pasos para crear una red virtual, la dirección IP pública y la tarjeta de interfaz de red con la CLI, consulte más arriba este artículo. (**azure vm create** también puede crear una tarjeta de interfaz de red, es preciso usar parámetros adicionales para una red virtual y una subred).

Después, ejecute un comando que pase los identificadores URI tanto al nuevo archivo del disco duro virtual del sistema operativo como a la imagen existente. En este ejemplo, se crea una máquina virtual de tamaño Standard_A1 en la región este de EE. UU.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Para obtener opciones de comando adicionales, ejecute `azure help vm create`.

## <a name="next-steps"></a>Pasos siguientes
Para administrar las máquinas virtuales con la CLI, consulte las tareas de [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Nov16_HO3-->


