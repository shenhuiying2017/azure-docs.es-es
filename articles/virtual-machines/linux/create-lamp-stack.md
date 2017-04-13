---
title: "Implementación de LAMP en una máquina virtual Linux en Azure | Microsoft Docs"
description: Aprender a instalar la pila LAMP en una VM Linux en Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 174490bec1aea20abf2c2bd465c7aa2c8590e3e2
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>Implementación de la pila LAMP en Azure
En este artículo se ofrecen instrucciones paso a paso sobre cómo implementar un servidor web Apache, MySQL y PHP (la pila LAMP) en Azure. Necesita una cuenta de Azure ([obtener una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2). También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-command-summary"></a>Resumen rápido de comandos

1. Guarde y edite el [archivo azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) según sus preferencias en la máquina local.
2. Ejecute los dos comandos siguientes para crear un grupo de recursos y luego implemente la plantilla:

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>Implementar LAMP en una máquina virtual existente
El siguiente comando actualiza paquetes y luego instala Apache, MySQL y PHP:

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Tutorial de implementación de LAMP en una nueva VM

1. Cree un grupo de recursos con [az group create](/cli/azure/group#create) para que contenga la nueva VM:

```azurecli
az group create -l westus -n myResourceGroup
```
Para crear la máquina virtual, puede usar una de las plantillas de Azure Resource Manager que se encuentran [en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

2. Guarde el [archivo azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) en la máquina local.
3. Edite el archivo **azuredeploy.parameters.json** según estime oportuno.
4. Implemente la plantilla con [az group deployment create] remitiéndose al archivo json descargado:

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

La salida es similar a la del ejemplo siguiente:

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

Ahora ha creado una máquina virtual Linux con la pila LAMP ya instalada. Si lo desea, puede comprobar la instalación pasando directamente a la sección [Comprobación de que LAMP se ha instalado correctamente](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Tutorial de implementación de LAMP en una VM existente
Si necesita ayuda con el proceso de creación de una VM Linux, puede visitar [esta página para aprender a hacerlo](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli). A continuación, necesita acceder mediante SSH a la VM Linux. Si necesita ayuda con el proceso de creación de una clave SSH, puede visitar [esta página para aprender a hacerlo en Linux o Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Si ya tiene una clave SSH, continúe y acceda mediante SSH desde la línea de comandos a la VM Linux con `ssh azureuser@mypublicdns.westus.cloudapp.azure.com`.

Ahora que ya puede entrar en la VM Linux para trabajar, le enseñaremos a instalar paso a paso la pila LAMP en distribuciones basadas en Debian. Los comandos exactos podrían ser diferentes en otras distribuciones de Linux.

#### <a name="installing-on-debianubuntu"></a>Instalación en Debian y Ubuntu
Necesitará tener instalados los siguientes paquetes: `apache2`, `mysql-server`, `php5` y `php5-mysql`. Puede instalarlos directamente o mediante Tasksel.
Antes de instalarlos, debe descargar y actualizar las listas de paquetes.

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>Paquetes individuales
Uso de apt-get:

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>Uso de tasksel
Como alternativa, puede descargar Tasksel, una herramienta de Debian y Ubuntu que instala varios paquetes relacionados como una tarea coordinada en el sistema.

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

Tras ejecutar una de las opciones anteriores, se le pedirá que instale estos paquetes y una serie de dependencias. Para establecer un contraseña administrativa para MySQL, presione 'y' seguido de 'Intro' para continuar y siga cualquier otra instrucción que aparezca. Este proceso instala las extensiones PHP mínimas necesarias para utilizar PHP con MySQL. 

![][1]

Ejecute el siguiente comando para ver otras extensiones PHP disponibles como paquetes:

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>Creación de documento info.php
Ahora podrá comprobar qué versión de Apache, MySQL y PHP tiene a través de la línea de comandos. Para ello, escriba `apache2 -v`, `mysql -v` o `php -v`.

Si desea realizar más pruebas, puede crear una página PHP de información rápida para verla en un explorador. Cree un archivo con el editor de texto Nano ejecutando este comando:

```bash
sudo nano /var/www/html/info.php
```

En el editor de texto GNU Nano, agregue las siguientes líneas:

```php
<?php
phpinfo();
?>
```

Después, guarde y salga del editor de texto.

Reinicie Apache con este comando para que todas las nuevas instalaciones surtan efecto.

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>Comprobación de que LAMP se ha instalado correctamente
Ahora puede comprobar la página de información PHP que creó abriendo un explorador y yendo a http://youruniqueDNS/info.php. Debe tener un aspecto similar a esta imagen.

![][2]

Puede comprobar la instalación de Apache visitando la página predeterminada de Apache2 en Ubuntu (http://suDNSúnica/info.php). La salida es similar a la del ejemplo siguiente:

![][3]

Enhorabuena. Ya ha instalado una pila LAMP en la máquina virtual de Azure.

## <a name="next-steps"></a>Pasos siguientes
Consulte la documentación de Ubuntu relacionada con la pila LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png

