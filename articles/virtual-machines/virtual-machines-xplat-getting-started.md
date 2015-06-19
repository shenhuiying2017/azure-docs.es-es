<properties
   pageTitle="Cómo crear una máquina virtual de Azure con la xplat-cli"
   description="En este tema se describe cómo instalar la xplat-cli en cualquier plataforma, cómo usarla para conectarse a la cuenta de Azure y cómo crear una máquina virtual a partir de la xplat-cli."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# Creación de una máquina virtual mediante la interfaz de la línea de comandos entre plataformas de Azure (xplat-cli)
La xplat-cli es una excelente manera de administrar la infraestructura de Azure desde cualquier plataforma.

El solo hecho de instalar la xplat-cli y tener una suscripción de Azure le impedirá crear una máquina virtual de inmediato, así que veamos esos pasos. Si no tiene una cuenta de Azure, [obtenga una gratis](http://azure.microsoft.com/pricing/free-trial/).

## Instalación de la xplat-cli

Siga estas instrucciones para instalar la [xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install).

## Conexión a Azure con la xplat-cli

La instalación de xplat-cli puede conectarse con una cuenta personal de Azure o con una cuenta de Azure profesional o educativa. Para comprender las diferencias y elegir, consulte [Cómo conectarse a su suscripción de Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure).

## Creación y conexión a una máquina virtual en Azure

Para crear una máquina virtual, debe comenzar por elegir (o cargar) una imagen y usar el comando  `azure vm create`.

1. Para elegir una imagen de la línea de comandos, puede mostrar las imágenes de máquina virtual disponibles mediante el comando  `azure vm image list`. Como hay tantas imágenes, querrá paginar los resultados mediante  `more` o filtrarlos con  `grep` (Linux) o con  `findstr` (Windows). Por ejemplo, si está buscando imágenes de Ubuntu en Linux, utilice un comando similar al siguiente:

        azure vm image list | grep Ubuntu

    Esto todavía generará una lista larga de imágenes, que podría restringir aún más por la versión:

        azure vm image list | grep Ubuntu-14_10

    Desde aquí, puede elegir una imagen y utilizar el comando  `show` para ver las propiedades con más detalle:

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-es-30GB

2. Creación de la máquina virtual.

    Una vez que haya elegido una imagen de máquina virtual, utilice el comando  `vm create` para crear la imagen. Este comando tiene varias opciones que puede mostrar con el comando de ayuda:

        vm create --help

    Junto con la imagen del paso 1, los argumentos clave que necesita para crear una máquina virtual son ubicación, nombre DNS y nombre de usuario.

    Para la autenticación, puede especificar una contraseña (en la línea de comandos o de manera interactiva) o autenticar mediante un certificado. Si elige una contraseña, esta debe tener al menos 8 caracteres, contener tanto mayúsculas como minúsculas y también un carácter especial (por ejemplo, uno de !@#$%^&+=). Es una buena idea poner la contraseña entre comillas y caracteres especiales de escape si la está pasando en la línea de comandos.

    Para elegir una ubicación, puede utilizar el comando  `vm location list` para seleccionar una región cercana.

  El nombre DNS que elija debe ser único (se asignará a  `dnsname.cloudapp.net`), y será el mismo que el nombre del equipo si no especifica un nombre de equipo en la línea de comandos por separado.  

    El siguiente ejemplo de Linux crea una máquina virtual en el oeste de Estados Unidos, abre el puerto SSH 22 predeterminado (el argumento -e) y crea un usuario denominado  `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-es-es-30GB "myadminuser" "myAdm1n@passwd"

## Pasos siguientes

Vamos a hacer algo con la máquina virtual. 

Dado que en el ejemplo anterior se abre el puerto SSH predeterminado, conectarse a la máquina virtual una vez que está en marcha es muy sencillo. Desde una línea de comandos de Linux:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Un lugar excelente para ver más ejemplos de uso de la xplat-cli para administrar la infraestructura de Azure es la [herramienta de línea de comandos de Azure para Mac y Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
 