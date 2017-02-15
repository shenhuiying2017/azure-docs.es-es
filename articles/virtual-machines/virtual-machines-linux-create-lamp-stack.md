---
title: "Implementación de LAMP en una máquina virtual Linux | Microsoft Docs"
description: "Sepa cómo instalar la pila LAMP en una máquina virtual Linux."
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
ms.devlang: NA
ms.topic: article
ms.date: 06/07/2016
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 29b295b7e061b16e187db59cdf9b777e12d63034


---
# <a name="deploy-lamp-stack-on-azure"></a>Implementación de la pila LAMP en Azure
En este artículo se ofrecen instrucciones paso a paso sobre cómo implementar un servidor web Apache, MySQL y PHP (la pila LAMP) en Azure. Necesitará una cuenta de Azure ([consiga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y la [CLI de Azure](../xplat-cli-install.md) que esté [conectada a su cuenta de Azure](../xplat-cli-connect.md).

En este artículo explicamos dos métodos para instalar LAMP:

## <a name="quick-command-summary"></a>Resumen rápido de comandos
1) Implementar LAMP en una nueva máquina virtual

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Implementar LAMP en una máquina virtual existente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Tutorial de implementación de LAMP en una nueva máquina virtual
Para empezar, cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) que contendrá la máquina virtual:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para crear la máquina virtual, puede usar una de las plantillas de Azure Resource Manager que se encuentran [en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Debería obtener una respuesta en la que se pide introducir algunos datos más:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Ahora ha creado una máquina virtual Linux con la pila LAMP ya instalada. Si lo desea, puede comprobar la instalación pasando directamente a la sección [Comprobación de que la pila LAMP se haya instalado correctamente].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Tutorial de implementación de LAMP en una máquina virtual existente
Si necesita ayuda con el proceso de creación de una máquina virtual Linux, puede visitar [esta página para aprender a hacerlo](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A continuación, necesitará acceder mediante SSH a la máquina virtual Linux. Si necesita ayuda con el proceso de creación de una clave SSH, puede visitar [esta página para aprender a hacerlo en Linux o Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Si ya tiene una clave SSH, continúe y acceda mediante SSH a la máquina virtual Linux con `ssh username@uniqueDNS`.

Ahora que ya puede entrar en la máquina virtual Linux para trabajar, le enseñaremos a instalar paso a paso la pila LAMP en distribuciones basadas en Debian. Los comandos exactos podrían ser diferentes en otras distribuciones de Linux.

#### <a name="installing-on-debianubuntu"></a>Instalación en Debian y Ubuntu
Necesitará tener instalados los siguientes paquetes: `apache2`, `mysql-server`, `php5` y `php5-mysql`. Puede instalarlos directamente o mediante Tasksel. A continuación, se muestran las instrucciones para ambas opciones.
Antes de instalarlos, debe descargar y actualizar las listas de paquetes.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Paquetes individuales
Uso de apt-get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Uso de Tasksel
Como alternativa, puede descargar Tasksel, una herramienta de Debian y Ubuntu que instala varios paquetes relacionados como una tarea coordinada en el sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Tras ejecutar cualquiera de las dos opciones anteriores, se le pedirá que instale estos paquetes y una serie de dependencias. Pulse 'y' seguido por 'Enter' para continuar, y siga cualquier otra instrucción que aparezca para establecer un contraseña administrativa para MySQL. Esto instalará las extensiones PHP mínimas necesarias para utilizar PHP con MySQL. 

![][1]

Ejecute el siguiente comando para ver otras extensiones PHP disponibles como paquetes:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Creación de documento info.php
Ahora podrá comprobar qué versión de Apache, MySQL y PHP tiene a través de la línea de comandos. Para ello, escriba `apache2 -v`, `mysql -v` o `php -v`.

Si desea realizar más pruebas, puede crear una página PHP de información rápida para verla en un explorador. Cree un nuevo archivo con el editor de texto Nano ejecutando este comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

En el editor de texto GNU Nano, agregue las siguientes líneas:

    <?php
    phpinfo();
    ?>

Después, guarde y salga del editor de texto.

Reinicie Apache con este comando para que todas las nuevas instalaciones surtan efecto.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Comprobación de que la pila LAMP se haya instalado correctamente
Ahora puede comprobar la página PHP de información que acaba de crear en el explorador visitando http://suDNSúnica/info.php. Debería ser parecida a la siguiente.

![][2]

Puede comprobar la instalación de Apache visitando la página predeterminada de Apache2 en Ubuntu (http://suDNSúnica/info.php). Puede ver algo parecido a lo siguiente:

![][3]

Enhorabuena. Ya ha instalado una pila LAMP en la máquina virtual de Azure.

## <a name="next-steps"></a>Pasos siguientes
Consulte la documentación de Ubuntu relacionada con la pila LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png



<!--HONumber=Nov16_HO3-->


