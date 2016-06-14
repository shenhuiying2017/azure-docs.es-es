<properties
	pageTitle="Creación de una VM de Oracle WebLogic Server y Database | Microsoft Azure"
	description="Cree una imagen de Oracle WebLogic Server 12c y Oracle Database 12c Azure que se ejecute en Windows Server 2012, con el modelo de implementación del Administrador de recursos."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rickstercdn" />

#Creación de una máquina virtual Oracle WebLogic Server 12c y Oracle Database 12c en Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

En este artículo se muestra cómo crear una base de datos Oracle WebLogic Server 12c y Oracle Database 12c en una imagen anteriormente creada con software de Oracle instalado en Windows Server 2012 en Azure.

##Para crear la base de datos hospedada en esta máquina virtual

Siga las instrucciones de [Creación de una máquina virtual de Oracle Database 12c en Azure](virtual-machines-windows-classic-create-oracle-database.md), empezando por la sección **Para crear la base de datos con la máquina virtual de Oracle Database 12c en Azure**.

##Para configurar su Oracle WebLogic Server 12c hospedado en esta máquina virtual
Siga las instrucciones de [Creación de una máquina virtual de Oracle WebLogic Server 12c en Azure](virtual-machines-windows-create-oracle-weblogic-server-12c.md), empezando por la sección **Para configurar la máquina virtual de Oracle WebLogic Server 12c en Azure**.

##Recursos adicionales
[Consideraciones variadas sobre las imágenes de máquina virtual de Oracle](virtual-machines-windows-classic-oracle-considerations.md)

[Lista de imágenes de máquinas virtuales de Oracle](virtual-machines-linux-classic-oracle-images.md)

[Conexión a la Base de datos de Oracle desde una aplicación de Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c con Linux en Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database DBA 12c versión 1 de dos días](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0601_2016-->