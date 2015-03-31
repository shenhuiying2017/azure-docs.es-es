<properties 
	pageTitle="Cómo usar Docker rápidamente con una imagen de máquina virtual de Ubuntu-Docker" 
	description="Se describe y se muestra cómo usar Docker en Ubuntu Server directamente desde la Galería de imágenes de Azure en minutos" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure" 
	ms.date="02/02/2015" 
	ms.author="rasquill"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace 

La forma más rápida de empezar a usar [Docker] es ir a Azure Marketplace y crear una máquina virtual con la plantilla de imagen **Docker en Ubuntu Server** creada por [Canonical] junto con [MSOpenTech]. Así se crea una máquina virtual de Ubuntu Server y se instala automáticamente la [extensión de máquina virtual Docker](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/) junto con el motor de Docker **más reciente** previamente instalado y ejecutándose en Azure.  

Puede conectarse inmediatamente a la máquina virtual mediante SSH y empezar a trabajar con Docker directamente sin hacer nada más. 

> [AZURE.NOTE]La máquina virtual creada con la plantilla de Azure Marketplace no aloja la API remota de Docker para la administración de un cliente remoto de docker. Para habilitar el control del host de Docker en esta máquina virtual de forma remota, consulte [Ejecución de Docker con HTTPS](https://docs.docker.com/articles/https/) o siga los pasos de [Uso de la extensión de máquina virtual Docker desde el Portal de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/) o [Uso de la extensión de la máquina virtual Docker desde azure-cli](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/). Si quiere hacer algo más avanzado, puede generar el [cliente de Windows Docker](https://github.com/ahmetalpbalkan/Docker.DotNet) desde Github y probarlo también (o simplemente cogerlo de [nuget](https://www.nuget.org/packages/Docker.DotNet/)). 

En este tema:

- [Inicio de sesión en el Portal]
- [Creación de una máquina virtual con la imagen de Docker de Canonical y MSOpenTech]
- [Conexión con SSH y a disfrutar]

## <a id='logon'></a>Inicio de sesión en el Portal

Esta parte es fácil, a menos que no disponga de una cuenta de Azure. [Obtenga una también con facilidad](http://azure.microsoft.com/pricing/free-trial/).

## <a id='createvm'></a>Creación de una máquina virtual con la imagen de Docker de Canonical y MSOpenTech

1. Ahora que ya ha iniciado sesión, haga clic en **Nueva** en la esquina inferior izquierda para crear una nueva imagen de máquina virtual. Es posible que vea la imagen adecuada en el banner inmediatamente:

	![Choose the Docker Ubuntu image in the banner](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. Pero si no es así, búsquela en la Galería de imágenes: 

	![Locate the image in the Image Gallery](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. Proporcione el nombre de usuario y la contraseña para la instancia, o un archivo **.pem** para habilitar SSH con un certificado. (En el gráfico siguiente se muestra cómo se especifica una combinación de nombre de usuario y contraseña). A continuación, presione **Crear** en la parte inferior.

	![Configure the vm instance](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Y espere hasta que esté en ejecución. No debería tardar demasiado.

	![Docker image running in portal](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'></a>Conexión con SSH y a disfrutar

Ahora empieza la diversión. Puede conectarse inmediatamente a la máquina virtual mediante SSH:

	![Connecting with SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

y empezar a emitir comandos Docker, sin olvidar que en esta máquina virtual de Azure la configuración predeterminada requiere **`sudo`**:

	![Pulling images](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Va a comenzar a usar [Docker]. 

<!--Anchors-->
[Inicio de sesión en el Portal]: #logon
[Creación de una máquina virtual con la imagen de Docker de Canonical y MSOpenTech]: #createvm
[Conexión con SSH y a disfrutar]: #havingfun
[Pasos siguientes]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Imagen con scratch de Docker]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/


<!--HONumber=47-->
