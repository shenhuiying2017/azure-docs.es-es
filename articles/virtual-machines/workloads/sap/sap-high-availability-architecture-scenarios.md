---
title: Escenarios y arquitectura de alta disponibilidad de Azure Virtual Machines para SAP NetWeaver | Microsoft Docs
description: Escenarios y arquitectura de alta disponibilidad para SAP NetWeaver en Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 867fe2835418a48e4e616d8137ba9fa4182c8fb7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Escenarios y arquitectura de alta disponibilidad para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="definition-of-terminologies"></a>Definición de terminología

El término **alta disponibilidad** se asocia a un conjunto de tecnologías que minimiza las interrupciones de TI al proporcionar una continuidad empresarial de los servicios de TI mediante componentes redundantes, tolerantes a errores o protegidos mediante conmutación por error dentro del **mismo** centro de datos. En nuestro caso, dentro de una sola región de Azure.

La **recuperación ante desastres** también tiene el objetivo de minimizar las interrupciones en los servicios de TI y recuperarlos, pero en **distintos** centros de datos que se encuentran a cientos de kilómetros de distancia. En nuestro caso, suelen estar repartidos entre distintas regiones de Azure dentro de la misma región geopolítica o según lo haya establecido usted como cliente.


## <a name="overview-of-high-availability"></a>Información general sobre la alta disponibilidad
Podemos descomponer la explicación sobre la alta disponibilidad para SAP en Azure en tres partes:

* **Alta disponibilidad de la infraestructura de Azure**, por ejemplo, la alta disponibilidad del proceso (VM), la red, el almacenamiento, etc., y sus beneficios para aumentar la disponibilidad de las aplicaciones de SAP.

* **Utilización del reinicio de VM de la infraestructura de Azure para lograr una "mayor disponibilidad" de las aplicaciones de SAP**

  Si decide no utilizar funcionalidades como los clústeres de conmutación por error de Windows Server (WSFC) o Pacemaker en Linux, el reinicio de máquina virtual de Azure se utiliza para proteger un sistema SAP contra el tiempo de inactividad, ya esté planeado o no, de la infraestructura de servidores físicos de Azure y la plataforma Azure global subyacente.


* **Alta disponibilidad de las aplicaciones de SAP**

  Para lograr alta disponibilidad total del sistema SAP, es necesario proteger todos los componentes críticos de este, por ejemplo:
  * los **servidores de aplicaciones de SAP** redundantes y
  * los componentes únicos (por ejemplo el **único punto de error (SPOF)**), como
    * las **instancias de SAP (A)SCS** y
    *  el **sistema de administración de bases de datos**.


La alta disponibilidad de SAP en Azure presenta algunas diferencias con respecto a la alta disponibilidad de SAP en un entorno físico o virtual local. En el documento [SAP NetWeaver High Availability and Business Continuity in Virtual Environments with VMware and Hyper-V on Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] (Alta disponibilidad y continuidad empresarial para SAP NetWeaver en entornos virtuales con VMware e Hyper-V en Microsoft Windows) se describen as configuraciones de alta disponibilidad para SAP estándares en entornos virtualizados en Windows.

No hay ninguna configuración de alta disponibilidad de SAP integrada en SAPInst para Linux como la que existe para Windows. Con respecto a la alta disponibilidad para SAP local en Linux, más información en [High Availability Partner Information][sap-ha-partner-information] (Información sobre la alta disponibilidad para asociados).

## <a name="azure-infrastructure-high-availability"></a>Alta disponibilidad de la infraestructura de Azure

### <a name="single-instance-virtual-machine-sla"></a>Acuerdo de Nivel de Servicio de instancia única

Actualmente hay un Acuerdo de Nivel de Servicio del 99,9 % para una única máquina virtual con Premium Storage. Para tener una idea de cómo sería la disponibilidad de una sola máquina virtual, puede compilar el producto de los distintos [Acuerdos de Nivel de Servicio de Azure][azure-sla] disponibles.

La base para el cálculo es de 30 días al mes o 43 200 minutos. Por lo tanto, un tiempo de inactividad del 0,05 % corresponde a 21,6 minutos. Como de costumbre, la disponibilidad de los distintos servicios se multiplica de la siguiente manera:

(Servicio de disponibilidad n.º 1/100) * (servicio de disponibilidad n.º 2/100) * (servicio de disponibilidad n.º 3/100) \*…

Como, por ejemplo:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 o una disponibilidad general del 99,75 %.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Varias instancias de Virtual Machines en el mismo conjunto de disponibilidad
Cuando en Virtual Machines haya dos o más instancias implementadas en el mismo **conjunto de disponibilidad**, se garantiza la conectividad de las máquinas virtuales a una instancia como mínimo al menos durante el 99,95 % del tiempo.

Cuando dos o más máquinas virtuales forman parte del mismo conjunto de disponibilidad, a cada una la plataforma Azure subyacente le asigna un **dominio de actualización** y un **dominio de error**.

Los **dominios de error** garantizan que las máquinas virtuales se implementan en un hardware diferente que no comparte interruptor de red ni fuente de alimentación. El tiempo de inactividad imprevisto de los servidores, el interruptor de red o la fuente de alimentación solo afecta a una máquina virtual.

Los **dominios de actualización** garantizan que no se reinician diferentes máquinas virtuales al mismo tiempo durante el mantenimiento planificado de infraestructura de Azure, sino que las máquinas virtuales se reinician de una en una.

Para más información, consulte [Administración de la disponibilidad de las máquinas virtuales Windows en Azure][azure-virtual-machines-manage-availability].

El conjunto de disponibilidad se utiliza para lograr alta disponibilidad de:

* Los servidores de aplicaciones de SAP redundantes  

* Los clústeres con dos o más nodos (por ejemplo, las máquinas virtuales) que protegen los únicos puntos de error como las instancias de SAP (A)SCS y el sistema de administración de bases de datos

### <a name="virtual-machine-vm-planned-and-unplanned-maintenance"></a>Mantenimiento planeado y no planeado de las máquinas virtuales

Existen dos tipos de eventos de plataforma Azure que pueden afectar a la disponibilidad de sus máquinas virtuales: mantenimiento planeado y mantenimiento no planeado.

* Los eventos de **mantenimiento planeado** son actualizaciones periódicas realizadas por Microsoft en la plataforma Azure subyacente para mejorar en general la confiabilidad, el rendimiento y la seguridad de la infraestructura de la plataforma sobre la que funcionan las máquinas virtuales.

* Los eventos de **mantenimiento no planeado** se producen cuando el hardware o la infraestructura física subyacente a su máquina virtual presentan algún tipo de error. Entre estos podemos encontrar errores de la red local, errores de los discos locales u otras errores a nivel de bastidor. Cuando se detecta un error de este tipo, la plataforma Azure migra automáticamente la máquina virtual del servidor físico en mal estado que hospeda la máquina virtual a uno con un estado correcto. Estos eventos no son habituales, pero pueden hacer que su máquina virtual se reinicie.

Para más información, consulte [Administración de la disponibilidad de las máquinas virtuales Windows en Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundancia de almacenamiento de Azure
Los datos de la cuenta de Microsoft Azure Storage se replican siempre para garantizar su durabilidad y alta disponibilidad, en cumplimiento con el contrato de nivel de servicio de Azure Storage, incluso en caso de errores transitorios del hardware.

Dado que Azure Storage mantiene de forma predeterminada tres imágenes de los datos, no se precisan RAID5 o RAID1 en varios discos de Azure.

Para más información, consulte [Replicación de Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Los discos Managed Disks son un tipo de recurso nuevo de Azure Resource Manager que puede usarse en lugar de discos duros virtuales almacenados en cuentas de Azure Storage. Los discos Managed Disks se alinean automáticamente con el conjunto de disponibilidad de la máquina virtual a la que están conectados y, por tanto, aumentan la disponibilidad de la máquina virtual y los servicios que se ejecutan en la máquina virtual.
Para más información, consulte [Introducción a Azure Managed Disks][azure-storage-managed-disks-overview].

Se recomienda usar discos Managed Disks, porque simplifican la implementación y administración de las máquinas virtuales.
**SAP actualmente solo admite Managed Disks de tipo Premium**. Para más información, lea la nota de SAP [1928533].

## <a name="utilizing-azure-infrastructure-ha-to-achieve-sap-application-higher-availability"></a>Utilización de la alta disponibilidad de la infraestructura de Azure para lograr una "mayor" disponibilidad de las aplicaciones de SAP

Si decide no utilizar funcionalidades como los clústeres de conmutación por error de Windows Server (WSFC) o Pacemaker en Linux (compatible actualmente solo para SLES 12 y versiones posteriores), el reinicio de VM de Azure se utiliza para proteger un sistema SAP contra el tiempo de inactividad, ya esté planeado o no, de la infraestructura de servidores físicos de Azure y la plataforma Azure global subyacente.

Este enfoque se describe más en el documento [Utilizing Azure Infrastructure VM Restart to Achieve “Higher Availability” of SAP System][sap-higher-availability] (Uso del reinicio de máquina virtual de la infraestructura de Azure para logar "mayor disponibilidad" del sistema SAP).

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Alta disponibilidad de las aplicaciones de SAP en IaaS de Azure

Para lograr alta disponibilidad total del sistema SAP, es necesario proteger todos los componentes críticos de este, por ejemplo:

* los **servidores de aplicaciones de SAP** redundantes y

* los componentes únicos (por ejemplo el **único punto de error (SPOF)**), como
  * las **instancias de SAP (A)SCS** y
  *  el **sistema de administración de bases de datos**.

Iremos analizando en detalle más adelante cómo lograr alta disponibilidad para los tres componentes críticos del sistema SAP.

### <a name="high-availability-for-sap-application-servers"></a>Alta disponibilidad en los servidores de aplicaciones de SAP

> Este capítulo es aplicable tanto para:
>
> ![Windows][Logo_Windows] Windows como para ![Linux][Logo_Linux] Linux
>

Por lo general, no se necesita una solución de alta disponibilidad específica para las instancias de diálogo y servidores de aplicaciones de SAP. La alta disponibilidad se obtiene mediante redundancia y se configuran varias instancias de diálogo en distintas instancias de Azure Virtual Machines. Debe tener, como mínimo, 2 instancias de aplicaciones de SAP instaladas en 2 instancias de Azure Virtual Machines.

![Figura 1: alta disponibilidad en los servidores de aplicaciones de SAP][sap-ha-guide-figure-2000]

_**Figura 1:** alta disponibilidad en los servidores de aplicaciones de SAP_

Debe colocar todas las máquinas virtuales que hospedan instancias de servidores de aplicaciones de SAP en el mismo conjunto de disponibilidad de Azure. Un conjunto de disponibilidad de Azure garantiza lo siguiente:

* Todas las máquinas virtuales forman parte del mismo **dominio de actualización**. Por ejemplo, un dominio de actualización garantiza que las máquinas virtuales no se actualicen al mismo tiempo durante un tiempo de inactividad de mantenimiento planeado.
La funcionalidad básica, que se basa en diversos dominios de actualización y de error dentro de una unidad de escalado de Azure, ya se presentó en el capítulo [Dominios de actualización][planning-guide-3.2.2].

* Todas las máquinas virtuales forman parte del mismo **dominio de error**. Por ejemplo, un dominio de error garantiza que las máquinas virtuales estén implementadas de tal manera que ningún único punto de error afecte la disponibilidad de todas las máquinas virtuales.

El número de dominios de error y de actualización que puede utilizar un conjunto de disponibilidad de Azure dentro de una unidad de escalado de Azure no es infinito. Esto significa que, al colocar un número de máquinas virtuales dentro de un conjunto de disponibilidad, tarde o temprano más de una máquina virtual acabará en el mismo dominio de error o de actualización.

Si se implementan unas cuantas instancias del servidor de aplicaciones de SAP en sus máquinas virtuales dedicadas y se parte de la base de que hay cinco dominios de actualización, al final se obtendrá la imagen siguiente. El número máximo real de dominios de error y de actualización dentro de un conjunto de disponibilidad podría cambiar en el futuro:

![Figura 2: alta disponibilidad de los servidores de aplicaciones de SAP en el conjunto de disponibilidad de Azure][planning-guide-figure-3000]
_**Figura 2:**alta disponibilidad de los servidores de aplicaciones de SAP en el conjunto de disponibilidad de Azure_ Más detalles en esta documentación: [administración de la disponibilidad de las máquinas virtuales][virtual-machines-manage-availability].


Los conjuntos de disponibilidad de Azure se presentaron en el capítulo [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3] del documento de planeamiento e implementación de Azure Virtual Machines para SAP NetWeaver.


**Únicamente en caso de discos no administrados**: como la cuenta de Azure Storage es un posible único punto de error, es importante tener, como mínimo, dos cuentas de Azure Storage, en las cuales haya distribuidas, al menos, dos máquinas virtuales. En una configuración ideal, los discos de cada máquina virtual que ejecuta una instancia de diálogo de SAP estarían implementados en una cuenta de almacenamiento distinta.

> [!IMPORTANT]
>
> Se recomienda encarecidamente usar Azure Managed Disks para las instalaciones de alta disponibilidad para SAP, ya que se alinean automáticamente con el conjunto de disponibilidad de la máquina virtual a la que están conectados y, por tanto, aumentan la disponibilidad de la máquina virtual y de los servicios que se ejecutan en la máquina virtual.  
>


### <a name="high-availability-architecture-for-the-sap-ascs-instance"></a>Arquitectura de alta disponibilidad para la instancia de SAP (A)SCS

### <a name="high-availability-architecture-for-the-sap-ascs-instance-on-windows"></a>Arquitectura de alta disponibilidad para la instancia de SAP (A)SCS en Windows


> ![Windows][Logo_Windows] Windows
>

Puede usar la solución de **clústeres de conmutación por error de Windows Server** (**WSFC**) para proteger la instancia de SAP (A)SCS. Hay dos variantes de la solución:

* Agrupación en clústeres de la instancia de SAP (A)SCS mediante **discos compartidos en el clúster**

   Más información sobre la arquitectura de alta disponibilidad con discos compartidos en el clúster en este documento sobre la [agrupación en clústeres de la instancia de SAP (A)SCS en el clúster de conmutación por error de Windows mediante discos compartidos en el clúster][sap-high-availability-guide-wsfc-shared-disk].   

* Agrupación en clústeres de la instancia de SAP (A)SCS mediante un **recurso compartido de archivos**

  Más información sobre la arquitectura de alta disponibilidad con discos compartidos en el clúster en este documento sobre la [agrupación en clústeres de la instancia de SAP (A)SCS en el clúster de conmutación por error de Windows mediante un recurso compartido de archivos][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Alta disponibilidad para la instancia de SAP (A)SCS en Linux


> ![Linux][Logo_Linux] Linux
>

Más información acerca de la agrupación en clústeres de la instancia de SAP (A)SCS con el marco de clústeres de SUSE Linux Enterprise Server en este documento: [Alta disponibilidad para SAP NetWeaver en máquinas virtuales de Azure en SUSE Linux Enterprise Server para aplicaciones de SAP][sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-clustered-sap-ascs-instance"></a>Configuración de varios identificadores de seguridad de SAP NetWeaver para la instancia de SAP (A)SCS en el clúster

> ![Windows][Logo_Windows] Windows
>
>Actualmente, solo se admiten varios identificadores de seguridad con el **clúster de conmutación por error de Windows Server (WSFC)**. Se admiten varios identificadores de seguridad con los **recursos compartidos de archivos** y los **discos compartidos**.
>

Más información sobre la arquitectura de alta disponibilidad con varios identificadores de seguridad en estos documentos:

* [Alta disponibilidad con varios identificadores de seguridad para la instancia SAP (A)SCS para los clústeres de conmutación por error de Windows Server y los recursos compartidos de archivos][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Alta disponibilidad con varios identificadores de seguridad para la instancia SAP (A)SCS con los clústeres de conmutación por error de Windows Server y el disco compartido][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Alta disponibilidad para la instancia de DBMS

DBMS también es un único punto de contacto de un sistema SAP. Debe protegerlo con una solución de alta disponibilidad. En la siguiente ilustración se muestra una solución de alta disponibilidad de SQL Server AlwaysOn en Azure, con clústeres de conmutación por error de Windows Server y el equilibrador de carga interno de Azure. SQL Server AlwaysOn replica los archivos de datos y de registro de DBMS con su propia replicación DBMS. En este caso, no se necesitan discos compartidos en el clúster, lo que simplifica toda la configuración.

![Figura 3: ejemplo de DBMS con SAP de alta disponibilidad, con SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 3:** ejemplo de DBMS con SAP de alta disponibilidad, SQL Server AlwaysOn_

Para más información sobre cómo agrupar clústeres de **DBMS de SQL Server** en Azure con el modelo de implementación de Azure Resource Manager, consulte estos artículos:

* [Configuración de un grupo de disponibilidad AlwaysOn en Azure Virtual Machines mediante Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual].

* [Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Para más información sobre cómo agrupar clústeres de **DBMS de SAP HANA** en Azure con el modelo de implementación de Azure Resource Manager, consulte este artículo:

* [Alta disponibilidad de SAP HANA en las máquinas virtuales (VM) de Azure][sap-hana-ha]
