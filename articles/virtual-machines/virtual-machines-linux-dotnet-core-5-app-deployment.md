<properties
   pageTitle="Automatización de la implementación de aplicaciones con extensiones de máquina virtual | Microsoft Azure"
   description="Tutorial de DotNet Core para máquinas virtuales de Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# Implementación de aplicaciones con plantillas de Azure Resource Manager

Una vez identificados todos los requisitos de infraestructura de Azure y traducidos en una plantilla de implementación, debe abordar la implementación de aplicaciones reales. Por implementación de la aplicación, se entiende la instalación de los archivos binarios de aplicación reales en recursos de Azure. En el ejemplo de Music Store, es necesario instalar y configurar .NET Core, NGINX y Supervisor en cada máquina virtual. Los archivos binarios de Music Store deben instalarse en la máquina virtual y la base de datos de Music Store debe crearse previamente.

En este documento se detalla cómo las extensiones de máquina virtual pueden automatizar la implementación y configuración de aplicaciones en máquinas virtuales de Azure. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, realice una implementación previa de una instancia de la solución en su suscripción de Azure y trabaje con la plantilla de Azure Resource Manager. La plantilla completa se puede encontrar aquí: [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) (Implementación de Music Store en Ubuntu).

## Script de configuración

Las extensiones de máquina virtual son programas especializados que se ejecutan en máquinas virtuales para automatizar la configuración. Hay extensiones disponibles para muchos fines específicos, como antivirus, configuración de registro y configuración de Docker. Se puede usar una extensión de script personalizado para ejecutar cualquier script en una máquina virtual. En el ejemplo de Music Store, la configuración de las máquinas virtuales de Ubuntu y la instalación de la aplicación Music Store depende de la extensión de script personalizado.

Antes de detallar cómo se declaran las extensiones de máquina virtual en una plantilla de Azure Resource Manager, examine el script que se ejecuta. Este script configura la máquina virtual de Ubuntu para hospedar la aplicación Music Store. Cuando se ejecuta, el script instala todo el software necesario, instala la aplicación Music Store desde el control de código fuente y prepara la base de datos.

Para más información acerca de cómo hospedar una aplicación de .Net Core en Linux, consulte [Publish to a Linux production environment](https://docs.asp.net/en/latest/publishing/linuxproduction.html) (Publicación en un entorno de producción de Linux).


```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## Extensión de script de máquina virtual

Para poder ejecutar extensiones de máquina virtual en una máquina virtual en tiempo de compilación, incluya los recursos de extensión en la plantilla de Azure Resource Manager. La extensión se puede agregar con el asistente Agregar nuevo recurso de Visual Studio, o insertando un recurso JSON válido en la plantilla. El recurso de extensión de script se anida dentro del recurso de máquina virtual. Lo podemos ver en el ejemplo siguiente.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [VM Script Extension](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359) (Extensión de script de máquina virtual).

En el siguiente código JSON, observe que el script se almacena en GitHub. Este script también podría almacenarse en Azure Blob Storage. Además, plantillas de Azure Resource Manager permiten construir la cadena de ejecución de script de modo que los valores de los parámetros de la plantilla se puedan usar como parámetros para la ejecución del script. En este caso, los datos se proporcionan al implementar las plantillas, y estos valores pueden usarse después al ejecutar el script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Para más información sobre el uso de la extensión de script personalizado, consulte [Uso de la extensión de script personalizado para máquinas virtuales de Linux con plantillas de Azure Resource Manager](./virtual-machines-linux-extensions-customscript.md).

## Paso siguiente

<hr>

[Más plantillas de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)

<!---HONumber=AcomDC_0928_2016-->