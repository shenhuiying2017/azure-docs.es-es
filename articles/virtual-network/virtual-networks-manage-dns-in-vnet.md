---
title: "Administración de servidores DNS usados por una red virtual (clásica): Portal de Azure clásico | Microsoft Docs"
description: "Obtenga información sobre cómo agregar y quitar servidores DNS en una red virtual (clásica) mediante el Portal de Azure clásico."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: b582be7d-dc78-4cfe-a766-185bd7e5dc68
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: b765fb94f881453ae6a90ec0ae6b6f06843b3aa2
ms.lasthandoff: 02/28/2017


---
# <a name="manage-dns-servers-used-by-a-virtual-network-classic-using-the-azure-portal-classic"></a>Administración de servidores DNS usados por una red virtual (clásica) mediante el Portal de Azure clásico

Puede administrar la lista de servidores DNS usados en una red virtual (VNet) en el Portal de Azure clásico o en el archivo de configuración de red. Puede agregar hasta 12 servidores DNS para cada red virtual. Al especificar servidores DNS, es importante comprobar que se enumeran los servidores DNS en el orden correcto para su entorno. Las listas de servidores DNS no funcionan con Round Robin. Se utilizan en el orden en que se especifican. Si se puede acceder al primer servidor DNS de la lista, el cliente utilizará ese servidor DNS con independencia de si el servidor DNS funciona correctamente o no. Para cambiar el orden del servidor DNS de la red virtual, quite los servidores DNS de la lista y agréguelos en el orden que desee.

> [!WARNING]
> Después de actualizar la lista de DNS, debe reiniciar las máquinas virtuales que se encuentran en la red virtual para que elijan la nueva configuración de servidor DNS. Las máquinas virtuales seguirán usando su configuración actual hasta que se reinicien.
> 
> 

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-azure-portal-classic"></a>Edición de una lista de servidores DNS para una máquina virtual mediante el Portal de Azure clásico
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En el panel de navegación, haga clic en **Redes** y en el nombre de la red virtual en la columna **Nombre**.
3. Haga clic en **Configurar**.
4. En **Servidores DNS**, se puede configurar lo siguiente:
   
   * **Para registrar (agregar) un nuevo servidor DNS** : simplemente escriba el nombre y la dirección IP en los cuadros correspondientes. De esta forma se agrega un servidor DNS a la lista de servidores DNS de la red virtual y también registra el servidor DNS con Azure.
   * **Para agregar un servidor DNS que se registró previamente** : si ha registrado un servidor DNS con Azure, puede seleccionarlo en la lista rellenada previamente.
   * **Para quitar un servidor DNS de la red virtual** : haga clic en la X junto al servidor que desea quitar. Tenga en cuenta que esto solo quita el servidor de esta lista de redes virtuales. El servidor DNS seguirá registrado en Azure para que lo usen otras redes virtuales. Para eliminar un servidor DNS de su suscripción, vaya a la página **Redes -> Servidores DNS**.
   * **Para cambiar el orden de los servidores DNS** : quite todos los servidores DNS que aparecen y, a continuación, vuelva a agregarlos en el orden que desee. Recuerde que no es una lista DNS de Round Robin.
   * **Para cambiar el nombre de un servidor DNS:** resalte el servidor DNS en la lista y después escriba el nuevo nombre. Se registrará un servidor DNS nuevo en Azure, así como se agregará a la lista de servidores DNS de la red virtual. El servidor DNS anterior y su dirección IP permanecerán registrados en Azure. Se puede eliminar en la página **Servidores DNS** si no se usan en ninguna otra red virtual.
5. Haga clic en **Guardar** en la parte inferior de la página para guardar la nueva configuración de los servidores DNS.
6. Reinicie las máquinas virtuales que se encuentran en la red virtual para que puedan adquirir la nueva configuración de DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Edición de una lista de servidores DNS con un archivo de configuración de red
Para editar una lista de servidores DNS mediante el uso de un archivo de configuración de red, primero hay que exportar los valores de configuración del Portal de administración. Después, podrá editar el archivo de configuración de red y volverlo a importar mediante el Portal de Azure clásico. A continuación se muestra una lista detallada de los pasos necesarios para completar este proceso.

1. Exporte la configuración de la red virtual a un archivo de configuración de red. Para que obtener más información y conocer los pasos necesarios para exportar las opciones de configuración de red, consulte [Exportación de la configuración de una red virtual a un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).
2. Especifique la información del servidor DNS para la red virtual. Para obtener más información acerca de cómo especificar un servidor DNS, consulte [Especificación de un servidor DNS en un archivo de configuración de red virtual](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Para más información sobre los archivos de configuración de red, consulte [Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Esquema de configuración de Azure Virtual Network) y [Configuración de una red virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).
3. Importe un archivo de configuración de red. Para obtener más información y conocer los pasos necesarios para importar el archivo de configuración de red, consulte [Importación de un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).
4. Reinicie las máquinas virtuales que se encuentran en la red virtual para que puedan adquirir la nueva configuración de DNS.


