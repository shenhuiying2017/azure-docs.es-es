<properties urlDisplayName="Create a custom VM" pageTitle=" Creación de una máquina virtual personalizada en Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/en-us/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />

#Creación de una máquina virtual personalizada

Una máquina virtual *personalizada* simplemente significa una máquina virtual creada con la opción **Desde la galería** porque le ofrece más opciones de configuración que la opción **Creación rápida**. Entre estas opciones se incluyen:

- Conexión de la VM a una red virtual
- Instalación del agente de máquina virtual, por ejemplo para funciones antimalware 
- Adición de la VM a un servicio de nube existente 
- Adición de la VM a un conjunto de disponibilidad

**Importante**: si desea que su máquina virtual use una red virtual para la conexión a ella directamente mediante un nombre de host o que configure conexiones entre locales, asegúrese de que especifica la máquina virtual cuando la cree. Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener detalles acerca de redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]



<!--HONumber=35.1-->
