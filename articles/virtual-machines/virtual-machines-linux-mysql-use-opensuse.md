<properties
	pageTitle="Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure"
	description="Aprenda a instalar MySQL en una máquina virtual en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="mysql"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2015"
	ms.author="kathydav"/>

# Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure

[MySQL][MySQL] es una conocida base de datos SQL de código abierto. En este tutorial se explica lo siguiente:

- Uso del [Portal de administración de Azure][AzurePortal] para crear una imagen de máquina virtual de OpenSUSE Linux a partir de una imagen disponible en Azure.
- Conectarse a la máquina virtual con SSH o PuTTY
- Instalar MySQL en la máquina virtual

[AZURE.INCLUDE [antares-iaas-signup-iaas](../../includes/antares-iaas-signup-iaas.md)]

## Creación de una máquina virtual que ejecuta OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Instalación y ejecución de MySQL en la máquina virtual

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Resumen
En este tutorial ha obtenido información sobre cómo crear una máquina virtual que ejecuta OpenSUSE Linux y conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MySQL en la máquina virtual de Linux. Para obtener más información sobre MySQL, consulte la [Documentación de MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->