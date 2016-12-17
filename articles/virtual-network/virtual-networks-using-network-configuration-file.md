---
title: "Configuración de una red virtual con un archivo de configuración de red"
description: "Instrucciones para exportar e importar un archivo de configuración de red en el Portal de administración de Azure para crear o modificar redes virtuales. "
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a29f94cf0b6f8da70a5a18dde1dcbdca68bf284


---
# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configuración de una red virtual con un archivo de configuración de red
Puede configurar una red virtual (VNet) mediante el Portal de administración de Azure o mediante un archivo de configuración de red.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Creación y modificación de un archivo de configuración de red
La manera más fácil de crear un archivo de configuración de red es exportar la configuración de red desde una configuración de red virtual existente, modificar el archivo para que contenga los ajustes con los que desea configurar las redes virtuales.

Para editar el archivo de configuración de red, puede simplemente abrir el archivo, realizar los cambios adecuados y, después, guardarlo. Puede usar cualquier editor *xml* para realizar cambios en el archivo de configuración de red. 

Se deben seguir meticulosamente las instrucciones sobre los [parámetros del esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera una subred que tiene algo implementado en ella como **en uso**. Cuando una subred está en uso, no puede modificarse. Antes de la modificación, mueva todo lo que haya implementado en la subred a una subred diferente que no se esté modificando.   Consulte [Mover una máquina virtual o instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Exportación e importación de la configuración de red virtual mediante el Portal de administración
Puede importar y exportar las opciones de configuración de una red contenidas en el archivo de configuración de red mediante PowerShell o el Portal de administración. Las instrucciones siguientes le ayudarán a exportar e importar con el Portal de administración. 

### <a name="to-export-your-network-settings"></a>Para exportar la configuración de red
Al exportar, todos los ajustes de las redes virtuales en su suscripción se escribirán en un archivo .xml. 

1. Inicie sesión en el **Portal de administración**.
2. En el Portal de administración, en la parte inferior de la página **Redes**, haga clic en **Exportar**. 
3. En la ventana **Exportar configuración de red** , compruebe que seleccionó la suscripción cuya configuración de red desee exportar. A continuación, haga clic en la marca de verificación de la esquina inferior derecha. 
4. Cuando se le solicite, guarde el archivo *NetworkConfig.xml* en la ubicación que prefiera.

### <a name="to-import-your-network-settings"></a>Para importar la configuración de red
1. En el **Portal de administración**, en el panel de navegación de la parte inferior izquierda, haga clic en **Nuevo**.
2. Haga clic en **Network Services** -> **Virtual Network** -> **Importar configuración**.
3. En la página **Importar el archivo de configuración de red**, busque su archivo de configuración de red y haga clic en la flecha **Siguiente**.
4. En la página **Creando su red** , verá información en pantalla que muestra las secciones de la configuración de red que cambiarán o se crearán. Si está de acuerdo con los cambios, haga clic en la marca de verificación para continuar con la actualización o la creación de la red virtual. 




<!--HONumber=Nov16_HO3-->


