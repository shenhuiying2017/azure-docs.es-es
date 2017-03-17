---
title: "Configuración de una Azure Virtual Network (clásica): archivo de configuración de red | Microsoft Docs"
description: "Obtenga información sobre cómo modificar redes virtuales (clásicas) mediante la exportación, la modificación y la importación de un archivo de configuración de red mediante el Portal de Azure clásico."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
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
ms.openlocfilehash: 3827dc0958c51fa0c4ecb1a2e8e3b7bbed42a75a
ms.lasthandoff: 02/28/2017


---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configuración de una red virtual (clásica) mediante un archivo de configuración de red
Puede configurar una red virtual (clásica) mediante el Portal de Azure clásico o con un archivo de configuración de red. No puede crear o modificar una red virtual mediante el modelo de implementación de Azure Resource Manager con un archivo de configuración de red. Tampoco puede usar Azure Portal para crear o modificar una red virtual (clásica).

## <a name="creating-and-modifying-a-network-configuration-file"></a>Creación y modificación de un archivo de configuración de red
La manera más fácil de crear un archivo de configuración de red es exportar la configuración de red desde una configuración de red virtual existente (clásica), modificar el archivo para que contenga los ajustes con los que desea configurar las redes virtuales.

Para editar el archivo de configuración de red, puede simplemente abrir el archivo, realizar los cambios adecuados y, después, guardarlo. Puede usar cualquier editor *xml* para realizar cambios en el archivo de configuración de red. 

Se deben seguir meticulosamente las instrucciones sobre los [parámetros del esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera una subred que tiene algo implementado en ella como **en uso**. Cuando una subred está en uso, no puede modificarse. Antes de la modificación, mueva todo lo que haya implementado en la subred a una subred diferente que no se esté modificando.   Consulte [Mover una máquina virtual o instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-azure-portal-classic"></a>Exportación e importación de la configuración de red virtual mediante el Portal de Azure clásico
Puede importar y exportar las opciones de configuración de una red contenidas en el archivo de configuración de red mediante PowerShell o el Portal de administración. Las instrucciones siguientes le ayudarán a exportar e importar con el Portal de administración. 

### <a name="to-export-your-network-settings"></a>Para exportar la configuración de red
Al exportar, todos los ajustes de las redes virtuales en su suscripción se escribirán en un archivo .xml. 

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. En el portal, en la parte inferior de la página **Redes**, haga clic en **Exportar**. 
3. En la ventana **Exportar configuración de red** , compruebe que seleccionó la suscripción cuya configuración de red desee exportar. A continuación, haga clic en la marca de verificación de la esquina inferior derecha. 
4. Cuando se le solicite, guarde el archivo *NetworkConfig.xml* en la ubicación que prefiera.

### <a name="to-import-your-network-settings"></a>Para importar la configuración de red
1. En la parte inferior izquierda del panel de navegación de portal, haga clic en **Nuevo**.
2. Haga clic en **Network Services** -> **Virtual Network** -> **Importar configuración**.
3. En la página **Importar el archivo de configuración de red**, busque su archivo de configuración de red y haga clic en la flecha **Siguiente**.
4. En la página **Creando su red** , verá información en pantalla que muestra las secciones de la configuración de red que cambiarán o se crearán. Si está de acuerdo con los cambios, haga clic en la marca de verificación para continuar con la actualización o la creación de la red virtual. 


