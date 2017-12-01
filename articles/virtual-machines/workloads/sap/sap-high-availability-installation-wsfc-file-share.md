---
title: "Instalación de alta disponibilidad para SAP NetWeaver en un clúster de conmutación por error de Windows y un recurso compartido de archivos para instancias de SAP ASCS/SCS en Azure | Microsoft Docs"
description: "Instalación de alta disponibilidad para SAP NetWeaver en un clúster de conmutación por error de Windows y un recurso compartido de archivos para instancias de SAP ASCS/SCS"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc957ece0250d233db9cec4f1fdd8b063c13a136
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="install-sap-netweaver-high-availability-on-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances-on-azure"></a>Instalación de alta disponibilidad para SAP NetWeaver en un clúster de conmutación por error de Windows y un recurso compartido de archivos para instancias de SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[sap-powershell-scrips]:https://github.com/Azure-Samples/sap-powershell

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

En este artículo se describe cómo instalar y configurar un sistema SAP de alta disponibilidad en Azure con el clúster de conmutación por error de Windows Server (WSFC) y el servidor de archivos de escalabilidad horizontal como opción para la agrupación en clústeres de instancias de SAP ASCS/SCS.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar la instalación, consulte los artículos siguientes:

* [Guía de arquitectura: Agrupación de una instancia de ASCS/SCS de SAP en un clúster de conmutación por error de Windows con un recurso compartido de archivos en Azure][sap-high-availability-guide-wsfc-file-share]

* [Preparación de la infraestructura de Azure para la alta disponibilidad de SAP con un clúster de conmutación por error de Windows y el recurso compartido de archivos para instancias de SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]

Necesitará los siguientes archivos ejecutables y DLL de SAP:
* Versión de la herramienta de instalación SAP Software Provisioning Manager (SWPM) SPS21 (o posterior).
* Descargue el archivo NTCLUST.SAR más reciente y el nuevo archivo DLL de recurso de clúster de SAP. Los nuevos archivos DLL de clúster de SAP admiten alta disponibilidad de SAP ASCS/SCS con el recurso compartido de archivos en el clúster de conmutación por error de Windows Server.

  Para más información sobre el nuevo archivo DLL de recurso de clúster de SAP, consulte este blog: [New SAP cluster resource DLL is available!][sap-blog-new-sap-cluster-resource-dll] (Ya disponible el nuevo archivo DLL de recurso de clúster de SAP).

No se describe la configuración del sistema de administración de bases de datos (DBMS),ya que las configuraciones varían en función del sistema DBMS que se use. Sin embargo, se da por supuesto que las inquietudes con respecto a la alta disponibilidad con DBMS se abordan con las funcionalidades que admiten diversos proveedores de DBMS para Azure. Tales funcionalidades incluyen AlwaysOn o creación de reflejo de base de datos para SQL Server y Oracle Data Guard para bases de datos de Oracle. En el escenario de este artículo, no se agregó más protección a DBMS.

No hay ninguna consideración especial cuando distintos servicios de DBMS interactúan con esta variante de configuración de SAP ASCS/SCS en clúster en Azure.

> [!NOTE]
> Los procedimientos de instalación de sistemas ABAP de SAP NetWeaver, sistemas Java y sistemas ABAP+Java son casi idénticos. La diferencia más significativa es que un sistema ABAP de SAP tiene una instancia de ASCS. El sistema Java de SAP tiene una instancia de SCS. El sistema ABAP+Java de SAP tiene una instancia de ASCS y una instancia de SCS en ejecución en el mismo grupo de clústeres de conmutación por error de Microsoft. Cualquier diferencia de instalación de cada pila de instalación de SAP NetWeaver se menciona explícitamente. Puede asumir que todos los demás componentes son iguales.  
>
>

## <a name="install-an-ascsscs-instance-on-an-ascsscs-cluster"></a>Instalación de una instancia de ASCS/SCS en un clúster de ASCS/SCS

> [!IMPORTANT]
>
> Actualmente, la herramienta de instalación SAP SWPM no admite una configuración de alta disponibilidad con una configuración de recurso compartido de archivos. Por lo tanto, es necesario realizar algunas acciones manuales para instalar un sistema SAP (por ejemplo, instalar y agrupar en clúster una instancia de SAP ASCS/SCS y configurar un host global de SAP independiente).  
>
> No se produce ningún otro cambio en los pasos de instalación (y agrupación en clúster) de una instancia de DBMS y los servidores de aplicaciones de SAP.
>

### <a name="install-an-ascsscs-instance-on-your-local-drive"></a>Instalación de una instancia de ASCS/SCS en la unidad local

Instale una instancia de SAP ASCS/SCS en *ambos* nodos del clúster de ASCS/SCS. Hágalo en la unidad local. En nuestro ejemplo, la unidad local es C:\\, pero puede elegir cualquier otra unidad local.  

Para instalar la instancia, en la herramienta de instalación SAP SWPM, vaya a:

**\<Product (Producto) >** > **\<DBMS >** > **Installation (Instalación)**  > **Application Server ABAP** (o **Java**) > **Distributed System** > **ASCS/SCS instance (Instancia de ASCS/SCS)**

> [!IMPORTANT]
> Actualmente, el escenario de recurso compartido de archivos no es compatible con la herramienta de instalación SAP SWPM. *No se puede usar* la siguiente ruta de acceso de instalación:
>
> **\<Product (Producto) >** > **\<DBMS >** > **Installation (Instalación)** > **Application Server ABAP** (o **Java**) > **High-Availability System** (Sistema de alta disponibilidad) > …
>

### <a name="remove-sapmnt-and-create-an-saploc-file-share"></a>Eliminación de SAPMNT y creación de un recurso compartido de archivos SAPLOC

SWMP creó un recurso compartido local SAPMNT en la carpeta C:\\usr\\sap.

Quite el recurso compartido de archivos SAPMNT en *ambos* nodos del clúster de ASCS/SCS.

Ejecute el siguiente script de PowerShell:

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Si no existe el recurso compartido SAPLOC, cree uno en *ambos* nodos del clúster de ASCS/SCS.

Ejecute el siguiente script de PowerShell:

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-an-sap-global-host-on-the-sofs-cluster"></a>Preparación de un host global de SAP en el clúster de SOFS

Cree el volumen y el recurso compartido de archivos siguientes en el clúster de SOFS:

* Estructura del archivo SAP GLOBALHOST C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS\ en el volumen compartido del clúster (CSV) de SOFS

* Recurso compartido de archivos SAPMNT

* Establezca la seguridad en la carpeta y el recurso compartido de archivos SAPMNT con control total para:
    * El grupo de usuarios \<DOMAIN>\SAP_\<SID>_GlobalAdmin
    * Los objetos de equipo de nodo de clúster de SAP ASCS/SCS \<DOMAIN>\ClusterNode1$ y \<DOMAIN>\ClusterNode2$

Para crear un nuevo volumen CSV con resistencia reflejada, ejecute el siguiente cmdlet de PowerShell en uno de los nodos del clúster de SOFS:


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
Para crear SAPMNT y establecer la seguridad de la carpeta y el recurso compartido, ejecute el siguiente script de PowerShell en uno de los nodos del clúster de SOFS:

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create a SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add a file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add  a security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add a security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascsscs-instances-and-sap-services"></a>Detención de instancias de ASCS/SCS y servicios SAP

Ejecute los pasos siguientes:
1. Detenga las instancias de SAP ASCS/SCS en ambos nodos del clúster de ASCS/SCS.
2. Detenga los servicios de Windows de SAP ASCS/SCS **SAP\<SID>_\<InstanceNumber>** en ambos nodos del clúster.

## <a name="move-the-sys-folder-to-the-sofs-cluster"></a>Traslado de la carpeta \SYS\. al clúster de SOFS

Ejecute los pasos siguientes:
1. Copie la carpeta SYS (por ejemplo, C:\usr\sap\\<SID>\SYS) de uno de los nodos del clúster de ASCS/SCS en el clúster de SOFS (por ejemplo, en C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS).
2. Elimine la carpeta C:\usr\sap\\<SID>\SYS de ambos nodos del clúster de ASCS/SCS.

## <a name="update-the-cluster-security-setting-on-the-sap-ascsscs-cluster"></a>Actualización de la configuración de seguridad del clúster de SAP ASCS/SCS

Ejecute el siguiente script de PowerShell en uno de los nodos del clúster de SAP ASCS/SCS:

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to the cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for the <DOMAIN>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Creación de un nombre de host virtual para la instancia de SAP ASCS/SCS en clúster

Cree un nombre de red en clúster de SAP ASCS/SCS (por ejemplo, **pr1-ascs [10.0.6.7]**), como se describe en [Creación de un nombre de host virtual para la instancia de SAP ASCS/SCS en clúster][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host].

## <a name="update-the-default-and-sap-ascsscs-instance-profile"></a>Actualización del perfil de instancia de SAP ASCS/SCS y del predeterminado

Para usar el nuevo nombre de host virtual de SAP ASCS/SCS y el nombre de host global de SAP, debe actualizar el perfil de instancia predeterminado y el de SAP ASCS/SCS \<SID>_ASCS/SCS\<n >_<Host>.


| Valores anteriores |  |
| --- | --- |
| Nombre de host de SAP ASCS/SCS = host global de SAP | ascs-1 |
| Nombre de perfil de instancia de SAP ASCS/SCS | PR1_ASCS00_ascs-1 |

| Nuevos valores |  |
| --- | --- |
| Nombre de host de SAP ASCS/SCS | **pr1-ascs** |
| Host global de SAP | **sapglobal** |
| Nombre de perfil de instancia de SAP ASCS/SCS | PR1\_ASCS00\_**pr1-ascs** |

### <a name="update-sap-default-profile"></a>Actualización del perfil predeterminado de SAP


| Nombre de parámetro | Valor del parámetro |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| enque/serverhost | **pr1-ascs** |

### <a name="update-the-sap-ascsscs-instance-profile"></a>Actualización del perfil de instancia de SAP ASCS/SCS

| Nombre de parámetro | Valor del parámetro |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>Puede usar el cmdlet de PowerShell **Update-SAPASCSSCSProfile** para automatizar la actualización del perfil.
>
>El cmdlet de PowerShell admite instancias de SAP ABAP ASCS y SAP Java SCS.
>

Copie [**SAPScripts.psm1**][sap-powershell-scrips] en la unidad local C:\tmp y ejecute el siguiente cmdlet de PowerShell:

```PowerShell
Import-Module C:\tmp\SAPScripts.psm1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![Figura 1: Salida de SAPScripts.psm1][sap-ha-guide-figure-8012]

_**Figura 1:** Salida de SAPScripts.psm1_

## <a name="update-the-sidadm-user-environment-variable"></a>Actualización de la variable de entorno \<sid >adm user

1. Actualice la \<ruta de acceso UNC de GLOBALHOST de la nueva variable de entorno sid>adm user en *ambos* nodos del clúster de ASCS/SCS.
2. Inicie sesión como \<sid>adm user y luego inicie la herramienta Regedit.exe.
3. Vaya a **HKEY_CURRENT_USER** > **Entorno** y actualice las variables con el nuevo valor:

| Variable | Valor |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-a-new-saprcdll-file"></a>Instalación de un nuevo archivo saprc.dll

1. Instale una nueva versión del recurso de clúster de SAP que admita el escenario de recurso compartido de archivos.

2. Descargue el paquete NTCLUST.SAR más reciente desde SAP Service Marketplace.

3. Desempaquete NTCLUS.SAR en uno de los nodos del clúster de ASCS/SCS y, luego, ejecute el siguiente comando desde el símbolo del sistema para instalar el nuevo archivo saprc.dll:

```
.\NTCLUST\insaprct.exe -yes -install
```

El nuevo archivo saprc.dll se instala en ambos nodos del clúster de ASCS/SCS.

Para más información, consulte [SAP Note 1596496 - How to update SAP Resource Type DLLs for Cluster Resource Monitor][1596496] (Nota de SAP 1596496: Actualización de los archivos DLL de tipos de recursos SAP para el Monitor de recursos de clúster).

## <a name="create-a-sap-sid-cluster-group-network-name-and-ip"></a>Creación de un grupo de clústeres SAP <SID>, un nombre de red y una IP

Para crear un grupo de clústeres de SAP \<SID>, un nombre de red de ASCS/SCS y una dirección IP correspondiente, ejecute el siguiente cmdlet de PowerShell:

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create an SAP ASCS instance virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create an SAP ASCS virtual IP address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set a static IP address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create a corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set a network DNS name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start an SAP <SID> cluster group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-the-sap-start-service-on-both-nodes"></a>Registro del servicio de inicio de SAP en ambos nodos

Vuelva a registrar el servicio de inicio de SAP ASCS/SCS para apuntar al nuevo perfil y a la ruta de acceso del perfil.

Debe ejecutar este nuevo registro en *ambos* nodos del clúster de ASCS/SCS.

En un símbolo del sistema con privilegios elevados, ejecute el siguiente comando:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![Figura 2: Reinstalar el servicio SAP][sap-ha-guide-figure-8013]

_**Figura 2:** Reinstalación del servicio SAP_

Asegúrese de que los parámetros sean correctos y, a continuación, seleccione **Manual** como el **tipo de inicio**.

## <a name="stop-the-ascsscs-service"></a>Detención del servicio ASCS/SCS

Detenga el servicio SAP ASCS/SCS SAP\<SID>_\<InstanceNumber> en ambos nodos del clúster de ASCS/SCS.

## <a name="create-a-new-sap-service-and-sap-instance-resources"></a>Creación de un nuevo servicio SAP y recursos de instancias de SAP

Para finalizar la creación de recursos del grupo de recursos de SAP SAP\<SID >, cree los siguientes recursos:

* Servicio SAP \<SID> \<InstanceNumber>
* Instancia de SAP \<SID> \<InstanceNumber>

Ejecute el siguiente cmdlet de PowerShell:

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Adición de un puerto de sondeo

Configure un recurso de clúster de SAP y el puerto de sondeo SAP-SID-IP mediante PowerShell. Ejecute esta configuración en uno de los nodos del clúster de SAP ASCS/SCS, como se describe [en este artículo][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-an-ers-instance-on-both-cluster-nodes"></a>Instalación de instancias de ERS en ambos nodos del clúster

Instale una instancia de Enqueue Replication Server (ERS) en *ambos* nodos del clúster de ASCS/SCS. En el menú SWPM, siga esta ruta de acceso de instalación:

**\<Product (Producto) >** > **\<DBMS>** > **Installation (Instalación)** > **Additional SAP System instances (Instancias adicionales del sistema SAP)** > **Enqueue Replication Server Instance** (Instancia de Enqueue Replication Server)

## <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalación de instancias de DBMS y servidores de aplicaciones SAP

Finalice la instalación del sistema SAP con la instalación de los siguientes elementos:
* Una instancia de DBMS
* Un servidor principal de aplicaciones SAP
* Un servidor adicional de aplicaciones SAP

## <a name="next-steps"></a>Pasos siguientes

* [Install an ASCS/SCS instance on a failover cluster with no shared disks (Instalación de una instancia de ASCS/SCS en un clúster de conmutación por error sin discos compartidos): instrucciones oficiales de SAP para recursos compartidos de archivos de alta disponibilidad][sap-official-ha-file-share-document]

* [Espacios de almacenamiento directo en Windows Server 2016][s2d-in-win-2016]

* [Introducción al servidor de archivos de escalabilidad horizontal para datos de aplicaciones][sofs-overview]

* [Novedades de Storage en Windows Server 2016][new-in-win-2016-storage]
