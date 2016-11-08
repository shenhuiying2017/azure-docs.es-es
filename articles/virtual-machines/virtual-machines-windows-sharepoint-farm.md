---
title: Creación de granjas de servidores de SharePoint | Microsoft Docs
description: Cree rápidamente una nueva granja de servidores de SharePoint 2013 o de SharePoint 2016 en Azure.
services: virtual-machines-windows
documentationcenter: ''
author: JoeDavies-MSFT
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: josephd

---
# Creación de granjas de servidores de SharePoint
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

modelo clásico.

## Granjas de servidores de SharePoint 2013
Con Marketplace del Portal de Microsoft Azure, puede crear rápidamente granjas de SharePoint Server 2013 preconfiguradas. Esto puede suponer un importante ahorro de tiempo si necesita una granja de SharePoint básica o de alta disponibilidad para un entorno de desarrollo y pruebas, o si va a evaluar SharePoint Server 2013 como solución de colaboración para su organización.

> [!NOTE]
> Se quitó el elemento **Granja de servidores SharePoint** en Azure Marketplace del Portal de Azure. Se reemplazó por los elementos **Granja de SharePoint 2013 sin alta disponibilidad** y **Granja de SharePoint 2013 de alta disponibilidad**.
> 
> 

La granja de SharePoint básica consta de tres máquinas virtuales con esta configuración.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Utilice esta configuración de granja simplificada para el desarrollo de aplicaciones de SharePoint en su primera evaluación de SharePoint 2013.

Si desea crear la granja básica de SharePoint (de tres servidores), siga estos pasos:

1. Haga clic [aquí](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Haga clic en **Implementar**.
3. En el panel **Granja de SharePoint 2013 sin alta disponibilidad**, haga clic en **Crear**.
4. Especifique la configuración en los pasos del panel **Crear granja de SharePoint 2013 sin alta disponibilidad** y, luego, haga clic en **Crear**.

La granja de servidores de SharePoint de alta disponibilidad consta de nueve máquinas virtuales con esta configuración.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Use esta configuración de granja para probar cargas de cliente más altas, la alta disponibilidad del sitio externo de SharePoint y los grupos de disponibilidad AlwaysOn de SQL Server para una granja de SharePoint. También puede usar esta configuración para el desarrollo de aplicaciones de SharePoint en un entorno de alta disponibilidad.

Para crear la granja de SharePoint de alta disponibilidad (de nueve servidores):

1. Haga clic [aquí](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Haga clic en **Implementar**.
3. En el panel **Granja de SharePoint 2013 de alta disponibilidad**, haga clic en **Crear**.
4. Especifique la configuración en los 7 pasos del panel **Crear granja de SharePoint 2013 de alta disponibilidad** y, luego, haga clic en **Crear**.

> [!NOTE]
> No se puede crear la **Granja de SharePoint 2013 sin alta disponibilidad** o la **Granja de SharePoint 2013 de alta disponibilidad** con una evaluación gratuita de Azure.
> 
> 

El Portal de Azure crea los dos tipos de granja en una red virtual que se encuentra solo en la nube y que tiene presencia web orientada a Internet. No hay una conexión VPN de sitio a sitio o conexión ExpressRoute con la red de su organización.

## Granjas de servidores de SharePoint 2016
Consulte [este artículo](https://technet.microsoft.com/library/mt723354.aspx) las instrucciones sobre cómo crear la siguiente granja de servidores de SharePoint 2016 de un único servidor.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## Administración de las granjas de SharePoint
Puede administrar los servidores de estas granjas a través de conexiones de Escritorio remoto. Para obtener más información, vea [Inicio de sesión en la máquina virtual](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

En el sitio de Administración central de SharePoint, puede configurar Mis sitios, aplicaciones de SharePoint y más funcionalidad. Para obtener más información, consulte [Configuración de SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## Pasos siguientes
* Descubra más configuraciones de [SharePoint](https://technet.microsoft.com/library/dn635309.aspx) en los servicios de infraestructura de Azure.

<!---HONumber=AcomDC_0810_2016-->