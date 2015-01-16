<properties urlDisplayName="Install MySQL" pageTitle="Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure" metaKeywords="Azure, MySQL" description="Aprenda a instalar MySQL en una máquina virtual en Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure

[MySQL][MySQL] es una conocida base de datos SQL de código abierto.  Con la ayuda del [Portal de administración de Azure][AzurePortal], puede crear una máquina virtual con OpenSUSE Linux.  Después, puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial se explica lo siguiente:

- Uso del Portal de administración para crear una imagen de máquina virtual de OpenSUSE Linux a partir de una imagen disponible en Azure.
- Conectarse a la máquina virtual con SSH o PuTTY
- Instalar MySQL en la máquina virtual

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Creación de una máquina virtual que ejecuta OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Instalación y ejecución de MySQL en la máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Resumen
En este tutorial ha obtenido información sobre cómo crear una máquina virtual que ejecuta OpenSUSE Linux y conectarse a ella de manera remota mediante SSH o PuTTY.  También, aprendió a instalar y configurar MySQL en la máquina virtual de Linux.  Para obtener más información sobre MySQL, consulte la [documentasión sobre MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
