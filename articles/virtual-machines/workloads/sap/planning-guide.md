---
title: "SAP NetWeaver en máquinas virtuales Linux en Azure: Implementación y planeamiento | Microsoft Docs"
description: "SAP NetWeaver en máquinas virtuales (VM) de Linux – Guía de planeación e implementación"
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d55582868c09781891a8dcd6e535f6dc4f472b5f
ms.contentlocale: es-es
ms.lasthandoff: 04/03/2017


---
# <a name="sap-netweaver-on-azure-virtual-machines-vms--planning-and-implementation-guide"></a>SAP NetWeaver en máquinas virtuales de Azure: guía de planeación e implementación
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b 

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../../storage/storage-premium-storage.md
[storage-redundancy]:../../../storage/storage-redundancy.md
[storage-scalability-targets]:../../../storage/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../../../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure permite a las empresas adquirir recursos de proceso y almacenamiento en un tiempo mínimo sin ciclos de adquisición prolongados. Las máquinas virtuales de Azure permiten a las empresas implementar aplicaciones clásicas (por ejemplo, aplicaciones basadas en SAP NetWeaver) en Azure y extender su confiabilidad y disponibilidad sin tener más recursos disponibles de forma local. Los servicios de máquinas virtuales de Azure también admiten la conectividad entre locales, que permite a las empresas integrar activamente las máquinas virtuales de Azure en sus dominios locales, nubes privadas e infraestructura del sistema SAP.
Estas notas del producto describen los fundamentos de las máquinas virtuales de Microsoft Azure y proporcionan un tutorial sobre los aspectos que tener en cuenta al planear e implementar instalaciones de SAP NetWeaver en Azure y, por lo tanto, es el documento que se debe leer antes de iniciar las implementaciones reales de SAP NetWeaver en Azure.
El documento complementa la Documentación de instalación de SAP y las Notas de SAP, que representan los principales recursos para las instalaciones e implementaciones de software de SAP en las plataformas proporcionadas.

## <a name="summary"></a>Resumen
La informática en la nube es un término ampliamente usado que está cobrando cada vez más importancia en la industria de TI, desde pequeñas empresas hasta las grandes corporaciones y multinacionales.

Microsoft Azure es la plataforma de servicios en la nube de Microsoft que ofrece una amplia gama de nuevas posibilidades. Ahora los clientes pueden aprovisionar y desaprovisionar rápidamente aplicaciones como un servicio en la nube, por lo que no se verán limitados por restricciones técnicas o presupuestarias. En lugar de invertir tiempo y presupuesto en infraestructura de hardware, las empresas pueden centrarse en la aplicación, en los procesos empresariales y en sus ventajas para clientes y usuarios.

Con los servicios de máquinas virtuales de Microsoft Azure, Microsoft ofrece una completa plataforma de infraestructura como servicio (IaaS). Las aplicaciones de basadas en SAP NetWeaver son compatibles en las máquinas virtuales de Azure (IaaS). Estas notas del producto describen cómo planear e implementar aplicaciones basadas en SAP NetWeaver en Microsoft Azure como plataforma preferida.

El documento en sí se centrará en dos aspectos principales:

* La primera parte describe dos modelos de implementación compatibles para las aplicaciones basadas en SAP NetWeaver en Azure. También describe el control general de Azure desde el punto de vista de las implementaciones de SAP.
* La segunda parte detalla la implementación de los dos escenarios descritos en la primera.

Para obtener recursos adicionales, consulte el capítulo [Recursos][planning-guide-1.2] de este documento.

### <a name="definitions-upfront"></a>Lista de definiciones
En el documento se utilizarán los términos siguientes:

* IaaS: infraestructura como servicio.
* PaaS: plataforma como servicio.
* SaaS: software como servicio.
* ARM: Azure Resource Manager.
* Componente de SAP: una aplicación de SAP individual, como ECC, BW, Solution Manager o EP.  Los componentes de SAP pueden basarse en tecnologías tradicionales, como ABAP o Java, o en una aplicación no basada en NetWeaver, como Business Objects.
* Entorno de SAP: uno o varios componentes de SAP agrupados lógicamente para desempeñar una función empresarial, como desarrollo, control de calidad, aprendizaje, recuperación ante desastres o producción.
* Infraestructura de SAP: hace referencia a todos los activos de SAP de la infraestructura de TI de un cliente. La infraestructura de SAP incluye todos los entornos, tanto los que son de producción como los que no.
* Sistema SAP: la combinación de la capa DBMS y la de aplicaciones, como la de un sistema de desarrollo SAP ERP, de prueba de SAP BW, de producción de SAP CRM, etc. En las implementaciones de Azure no se admite la división de estas dos capas entre la infraestructura local y de Azure. Esto significa que un sistema SAP debe implementarse de forma local o en Azure, pero no en ambos. Sin embargo, los diferentes sistemas de una infraestructura de SAP pueden implementarse en Azure o de forma local. Por ejemplo, pueden implementarse sistemas de pruebas y de desarrollo de SAP CRM en Azure, a la vez que se implementa el sistema de producción de forma local.
* Implementación exclusiva en la nube: una implementación donde la suscripción de Azure no está conectada de sitio a sitio o a través de ExpressRoute a la infraestructura de red local. En la documentación habitual de Azure este tipo de implementaciones se denomina "implementaciones exclusivas en la nube". A las máquinas virtuales implementadas con este método se tiene acceso a través de Internet y una dirección IP pública o un nombre DNS público asignado a las máquinas virtuales de Azure. En Microsoft Windows, el DNS y Active Directory (AD) locales no se extienden a Azure en este tipo de implementaciones. Por lo tanto, las máquinas virtuales no forman parte de Active Directory local. Ocurre lo mismo en implementaciones de Linux en las que se usa, por ejemplo, OpenLDAP y Kerberos.

> [!NOTE]
> Las implementaciones exclusivas en la nube se definen en este documento como infraestructuras de SAP completas ejecutadas exclusivamente en Azure sin extensión de Active Directory/OpenLDAP ni resolución de nombres desde la infraestructura local a la nube pública. No se admiten configuraciones exclusivas en la nube con sistemas de producción SAP, o con configuraciones donde deban usarse SAP STMS u otros recursos locales entre sistemas SAP hospedados en Azure y recursos locales.
>
>

* Entre locales: describe un escenario donde se implementan máquinas virtuales en una suscripción de Azure con conexión de sitio a sitio, entre varios sitios o de ExpressRoute entre los centros de datos locales y Azure. En la documentación habitual de Azure, este tipo de implementaciones se denominan "escenarios entre locales". La razón para la conexión es ampliar los dominios locales, Active Directory / OpenLDAP locales y DNS local en Azure. La infraestructura local se extiende a los recursos de Azure de la suscripción. Con esta extensión, las máquinas virtuales pueden formar parte del dominio local. Los usuarios del dominio local pueden tener acceso a los servidores y ejecutar servicios en esas máquinas virtuales (por ejemplo, servicios de DBMS). Es posible la comunicación y resolución de nombres entre máquinas virtuales implementadas de forma local y en Azure. Este es el escenario de implementación más previsible de los activos de SAP. Para más información, consulte [este][vpn-gateway-cross-premises-options] artículo y [este][vpn-gateway-site-to-site-create] vínculo.

> [!NOTE]
> En sistemas de producción SAP, se admiten implementaciones entre locales de sistemas SAP donde las máquinas virtuales de Azure que ejecuten sistemas SAP pertenezcan a un dominio local. Las configuraciones entre locales se admiten para la implementación completa o parcial de infraestructuras de SAP en Azure. Se requiere que esas máquinas virtuales formen parte del dominio y ADS/OpenLDAP locales incluso al ejecutar una infraestructura completa de SAP en Azure. En versiones anteriores de la documentación se describen escenarios de TI híbridos, donde "híbrido" implica una conectividad entre locales de la infraestructura local y Azure. Además, se da la circunstancia de que las máquinas virtuales de Azure forman parte de Active Directory / OpenLDAP local.
>
>

Algunos documentos de Microsoft describen escenarios entre locales de un modo ligeramente distinto, en especial en configuraciones de DBMS de alta disponibilidad. En el caso de documentos relacionados con SAP, el escenario entre locales se reduce a una conectividad de sitio a sitio o privada (ExpressRoute), y a la distribución de la infraestructura de SAP entre medios locales y Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
Las siguientes guías incluyen temas sobre implementaciones de SAP en Azure:

* [SAP NetWeaver en Azure Virtual Machines: guía de planeamiento e implementación (este documento)][planning-guide]
* [SAP NetWeaver en Azure Virtual Machines: guía de implementación][deployment-guide]
* [SAP NetWeaver en Azure Virtual Machines: guía de implementación de DBMS][dbms-guide]

> [!IMPORTANT]
> Siempre que sea posible, se usa un vínculo a la guía de instalación de SAP a la que se hace referencia (referencia InstGuide-01, consulte <http://service.sap.com/instguides>). En cuanto a los requisitos previos y el proceso de instalación, las guías de instalación de SAP NetWeaver deben leerse detenidamente, dado que el presente documento solo trata tareas específicas para sistemas SAP NetWeaver instalados en una máquina virtual de Microsoft Azure.
>
>

Las siguientes notas de SAP están relacionadas con el tema de SAP en Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |SAP Applications on Azure: Supported Products and Sizing (Aplicaciones SAP en Azure: productos y tamaños compatibles) |
| [2015553] |SAP on Microsoft Azure: Support Prerequisites (SAP en Microsoft Azure: requisitos previos de compatibilidad) |
| [1999351] |Troubleshooting Enhanced Azure Monitoring for SAP (Solución de problemas de la supervisión mejorada de Azure para SAP) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Métricas de supervisión clave para SAP en Microsoft Azure) |
| [1409604] |Virtualization on Windows: Enhanced Monitoring (Virtualización en Windows: supervisión mejorada) |
| [2191498] |SAP on Linux with Azure: Enhanced Monitoring (SAP en Linux con Azure: supervisión mejorada) |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP license issues (Linux y máquinas virtuales de Microsoft Azure (IaaS): problemas de licencia de SAP) |
| [1984787] |SUSE LINUX Enterprise Server 12: Installation notes (SUSE Linux Enterprise Server 12: notas de instalación) |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation and Upgrade (Red Hat Enterprise Linux 7.x: instalación y actualización) |

Lea también la [wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , que contiene todas las notas de SAP para Linux.

En [este artículo][azure-subscription-service-limits-subscription] se exponen las limitaciones generales predeterminadas y el límite máximo de suscripciones de Azure. 

## <a name="possible-scenarios"></a>Escenarios posibles
Con frecuencia, SAP se considera una de las aplicaciones críticas en las empresas. La arquitectura y las operaciones de estas aplicaciones suelen resultar muy complejas. Es importante asegurarse de que se cumplen los requisitos de disponibilidad y rendimiento.

Por ello, las empresas tienen que pensar detenidamente qué aplicaciones pueden ejecutarse en un entorno de nube pública, independientemente del proveedor de nube elegido.

La lista siguiente muestra los posibles tipos de sistemas para la implementación de aplicaciones basadas en SAP NetWeaver en entornos de nube pública:

1. Sistemas de producción de tamaño medio
2. Sistemas de desarrollo
3. Sistemas de pruebas
4. Sistemas de prototipos
5. Sistemas de aprendizaje y de demostración

Para implementar correctamente sistemas SAP en una IaaS general o en la de Azure, es importante comprender las diferencias significativas entre las ofertas de outsourcers o proveedores de servicios de hosting tradicionales y las ofertas de IaaS. Mientras que el outsourcer o proveedor de hosting tradicional adaptará la infraestructura (tipo de red, almacenamiento y servidor) a la carga de trabajo que el cliente desea hospedar, en realidad es responsabilidad del cliente elegir la carga de trabajo adecuada para las implementaciones de IaaS.

Como primer paso, los clientes deben comprobar los elementos siguientes:

* Los tipos de máquina virtual de Azure compatibles con SAP
* Productos/versiones de Azure compatibles con SAP
* Las versiones de SO y DBMS compatibles para las versiones específicas de SAP en Azure
* Rendimiento de SAPS proporcionado por diferentes SKU de Azure

Las respuestas a estas preguntas pueden encontrarse en la nota de SAP [1928533].

Como segundo paso, las limitaciones de recursos y ancho de banda de Azure deben compararse con el consumo de recursos real en los sistemas locales. Por lo tanto, los clientes deben estar familiarizados con las diferentes funcionalidades de los tipos de Azure compatibles con SAP en lo que respecta a lo siguiente:

* Recursos de CPU y memoria de diferentes tipos de máquina virtual
* Ancho de banda de IOPS de diferentes tipos de máquina virtual
* Funcionalidades de red de diferentes tipos de máquina virtual

Se puede encontrar la mayoría de los datos [aquí][virtual-machines-sizes].

Tenga en cuenta que los límites mencionados en el vínculo anterior son los superiores. No significa que siempre puedan proporcionarse esos límites para todos los recursos, como IOPS. Sin embargo, las excepciones son los recursos de CPU y memoria de un tipo de máquina virtual elegido. Para los tipos de máquina virtual compatibles con SAP, los recursos de CPU y memoria están reservados y, por tanto, están disponibles en cualquier momento para su consumo en la máquina virtual.

La plataforma Microsoft Azure, como otras plataformas de IaaS, es una plataforma multiempresa. Esto significa que el almacenamiento, la red y otros recursos se comparten entre los inquilinos. Se usa la limitación inteligente y la lógica de cuotas para impedir que un inquilino perjudique el rendimiento de otro (vecino ruidoso) significativamente. Aunque la lógica de Azure intenta que las variaciones de ancho de banda sean pequeñas, las plataformas con más uso compartido suelen generar mayores variaciones en la disponibilidad de recursos y ancho de banda que las experimentadas por muchos clientes en sus implementaciones locales. Como resultado, pueden experimentarse diferentes niveles de ancho de banda en lo que respecta a los valores de E/S de datos en redes o almacenamiento (volumen y latencia) en cada instante. Debe tenerse en cuenta que un sistema SAP en Azure podría sufrir mayores variaciones que un entorno local.

Un último paso es evaluar los requisitos de disponibilidad. Puede ocurrir que la infraestructura subyacente de Azure necesite actualizarse y que los hosts que ejecuten las máquinas virtuales tengan que reiniciarse. En ese caso, estas máquinas virtuales también se cerrarán y reiniciarán. Este mantenimiento se lleva a cabo fuera del horario de oficina de una región concreta, pero el intervalo de varias horas durante el cual puede ocurrir el reinicio es relativamente amplio. Hay varias tecnologías en la plataforma Azure que pueden configurarse para mitigar total o parcialmente los efectos de estas actualizaciones. Se han diseñado mejoras futuras de la plataforma Azure, DBMS y la aplicación SAP para minimizar el impacto de estos reinicios.

Para implementar correctamente un sistema SAP en Azure, el sistema operativo, base de datos y aplicaciones SAP del entorno SAP local deben aparecer en la matriz de soporte de SAP de Azure. Además, deben ser adecuados a los recursos que la infraestructura de Azure puede proporcionar y deben funcionar con los Acuerdos de Nivel de Servicio que ofrece Microsoft Azure en lo que respecta a la disponibilidad. Cuando identifique esos sistemas, debe decidirse por uno de los dos siguientes escenarios de implementación.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente
![Implementación de una sola máquina virtual con un escenario de demostración o aprendizaje de SAP en Azure][planning-guide-figure-100]

Este escenario es típico de aprendizajes o sistemas de demostración, donde se instalan todos los componentes del software, ya sean de SAP o no, dentro de una única máquina virtual. No se admiten sistemas de producción de SAP en este escenario de implementación. En general, este escenario cumple los requisitos siguientes:

* Se puede acceder a las propias máquinas virtuales a través de la red pública. No se requiere conectividad de red directa entre las aplicaciones que se ejecutan dentro de las máquinas virtuales y la red local del cliente o de la empresa que posee el contenido de demostración o aprendizaje.
* Si hay varias máquinas virtuales involucradas en el escenario de aprendizaje o demostración, la resolución de nombres y las comunicaciones de red deben funcionar entre dichas máquinas virtuales. No obstante, las comunicaciones entre el conjunto de máquinas virtuales tienen que estar aisladas para que varios conjuntos de máquinas virtuales puedan implementarse en paralelo sin interferencias.  
* Se requiere conectividad a Internet para que el usuario final inicie sesión de forma remota en las máquinas virtuales hospedadas en Azure. Dependiendo del SO invitado, se usará Terminal Services / RDS o VNC/ssh para tener acceso a la máquina virtual con el fin de completar las tareas de aprendizaje o realizar las demostraciones. Si también pueden exponerse puertos SAP, como 3200, 3300 y 3600, se podrá acceder a la instancia de la aplicación SAP desde cualquier PC de sobremesa conectado a Internet.
* Los sistemas y máquinas virtuales de SAP representan un escenario independiente en Azure que solo requiere una conexión pública a Internet para el acceso de los usuarios finales y no precisa una conexión a otras máquinas virtuales de Azure.
* En la máquina virtual se instalan y se ejecutan directamente SAPGUI y un explorador.
* Se requiere el restablecimiento rápido de una máquina virtual a su estado original y una nueva implementación de ese estado original.
* En el caso de escenarios de demostración y aprendizaje que se llevan a cabo en varias máquinas virtuales, se requiere un servicio de DNS o Active Directory / OpenLDAP para cada conjunto de máquinas virtuales.

![Representación de un escenario de demostración o aprendizaje por parte de un grupo de VM en un servicio en la nube de Azure][planning-guide-figure-200]

Es importante tener en cuenta que las máquinas virtuales de cada conjunto deben implementarse en paralelo y que los nombres de las máquinas virtuales son los mismos en cada conjunto.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Entre locales: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local
![VPN con conectividad de sitio a sitio (entre locales)][planning-guide-figure-300]

Este es un escenario entre locales con numerosos patrones de implementación posibles. Puede describirse como de una forma tan simple como la ejecución de algunas partes de la infraestructura de SAP en sistemas locales y otras en Azure. Todos los aspectos relativos al hecho de que parte de los componentes de SAP se ejecuten en Azure deben ser transparentes para los usuarios finales. Por lo tanto, elementos como el sistema de corrección de transporte de SAP (STMS), comunicación de RFC, impresión, seguridad (como SSO), etc., funcionarán perfectamente en los sistemas SAP en Azure. Sin embargo, el escenario entre locales también describe un escenario donde toda la infraestructura de SAP se ejecuta en Azure con el DNS y dominio del cliente extendidos a Azure.

> [!NOTE]
> Este es el escenario de implementación admitido para la ejecución de sistemas SAP productivos.
>
>

Consulte [este artículo][vpn-gateway-create-site-to-site-rm-powershell] para obtener más información sobre cómo conectar la red local con Microsoft Azure.

> [!IMPORTANT]
> Cuando se habla de escenarios entre locales en los que intervienen implementaciones del cliente locales y de Azure, hay que detenerse en la granularidad de sistemas SAP completos. Los casos que *no admiten* escenarios entre locales son los siguientes:
>
> * La ejecución de varias capas de aplicaciones SAP en diferentes métodos de implementación. Por ejemplo, ejecutar la capa DBMS en local y el nivel de aplicación de SAP en máquinas virtuales de Azure o viceversa.
> * Combinar algunos componentes de una capa SAP en Azure y otras en medios locales. Por ejemplo, dividir las instancias del nivel de aplicación SAP entre máquinas virtuales locales y de Azure.
> * No se admite la distribución de máquinas virtuales que ejecuten instancias SAP de un sistema en varias regiones de Azure.
>
> La razón de estas restricciones es el requisito de una red de alto rendimiento de latencia muy baja en un sistema SAP, especialmente entre las instancias de la aplicación y la capa DBMS de un sistema SAP.
>
>

### <a name="supported-os-and-database-releases"></a>Versiones de bases de datos y sistemas operativos compatibles
* Este artículo incluye una lista del software de servidor de Microsoft compatible con los servicios de Azure Virtual Machine: <http://support.microsoft.com/kb/2721672>.
* La nota de SAP [1928533]indica las versiones de sistemas operativos y de bases de datos admitidos en los servicios de máquinas virtuales de Azure con software SAP.
* La nota de SAP [1928533]muestra las aplicaciones y versiones de SAP compatibles con los servicios de máquinas virtuales de Azure.
* Solo se admite la ejecución de imágenes de 64 bits como máquinas virtuales invitadas en Azure para escenarios de SAP. Por ello, solo se admiten aplicaciones y bases de datos de SAP de 64 bits.

## <a name="microsoft-azure-virtual-machine-services"></a>Servicios de máquinas virtuales de Microsoft Azure
La plataforma Microsoft Azure es una plataforma de servicios en la nube a escala de Internet hospedada y cuyo funcionamiento se dirige desde los centros de datos de Microsoft. La plataforma incluye los servicios de máquinas virtuales de Microsoft Azure (infraestructura como servicio o IaaS) y un conjunto de funcionalidades sofisticadas de plataforma como servicio (PaaS).

La plataforma Azure reduce la necesidad de adquisiciones iniciales de tecnología e infraestructura. Simplifica el mantenimiento y funcionamiento de aplicaciones proporcionando procesos y almacenamiento a petición para hospedar, escalar y administrar aplicaciones web y aplicaciones conectadas. La administración de la infraestructura se automatiza con una plataforma diseñada para lograr alta disponibilidad y escalado dinámico con el fin de satisfacer las necesidades de uso con la opción de un modelo de precios de pago por uso.

![Posicionamiento de servicios de máquinas virtuales de Microsoft Azure][planning-guide-figure-400]

Con los servicios de máquina virtual de Azure, Microsoft permite implementar imágenes de servidor personalizadas en Azure como instancias de IaaS (consulte la ilustración 4). Las máquinas virtuales de Azure se basan en unidades de disco duro virtuales (VHD) Hyper-V y pueden ejecutar diferentes sistemas operativos como SO invitado.

Desde una perspectiva operativa, el servicio de máquinas virtuales de Azure es similar a las máquinas virtuales implementadas en local. Sin embargo, la gran ventaja es que la infraestructura no requiere adquisición ni ningún tipo de administración. Los desarrolladores y administradores tienen control total de la imagen de sistema operativo dentro de estas máquinas virtuales. Los administradores pueden iniciar sesión de forma remota en las máquinas virtuales para realizar tareas de mantenimiento, de solución de problemas y de implementación de software. Con respecto a la implementación, las únicas restricciones son los tamaños y las capacidades de las máquinas virtuales de Azure. Su configuración podría no ser tan granular como en el caso del entorno local. Hay una gran variedad de tipos de máquina virtual que combinan lo siguiente:

* Cantidad de vCPU
* Memoria
* Número de discos duros virtuales que se pueden conectar
* Anchos de banda de red y almacenamiento

El tamaño y las limitaciones de varios tamaños de máquinas virtuales están documentados en una tabla de [este artículo][virtual-machines-sizes].

Hay diversas familias o series de máquinas virtuales. Con fecha de diciembre de 2015, pueden distinguirse las siguientes familias de máquinas virtuales:

* Tipos de máquinas virtuales A0-A7: no todos están certificados para SAP. Primera serie de máquinas virtuales en las que se introdujo IaaS de Azure.
* Tipos de máquinas virtuales A8-A11: instancias de procesos de alto rendimiento. Ejecución en distintos hosts con procesos de mejor rendimiento que otras máquinas virtuales de la serie A.
* Tipos de máquinas virtuales de la serie D: mejor rendimiento que A0-A7. No todos los tipos de máquina virtual están certificadas para SAP.
* Tipos de máquinas virtuales de la serie DS: se usan los mismos hosts que en la serie D, pero pueden conectarse a Azure Premium Storage (consulte el capítulo [Azure Premium Storage][planning-guide-3.3.2] de este documento). De nuevo, no todos los tipos de máquinas virtuales están certificadas para SAP.
* Tipos de máquinas virtuales de la serie G: tipos de máquinas virtuales de memoria alta.
* Tipos de máquinas virtuales de la serie GS: son análogas a la serie G, pero con la opción de usar Azure Premium Storage (consulte el capítulo [Azure Premium Storage][planning-guide-3.3.2] de este documento). Si se usan máquinas virtuales de la serie GS como servidores de base de datos, es obligatorio utilizar el almacenamiento Premium para los archivos de registro de datos y transacciones de las base de datos.

Se pueden encontrar las mismas configuraciones de memoria y CPU en varias series de máquinas virtuales. No obstante, el rendimiento de estas máquinas virtuales de distintas series podría diferir significativamente. Esto sucede a pesar de tener la misma configuración de memoria y CPU. El motivo es que el hardware de servidor del host subyacente durante la introducción de los diferentes tipos de máquina virtual tenía características de rendimiento diferentes.  Normalmente la diferencia en rendimiento también se refleja en el precio de las diferentes máquinas virtuales.

Hay que tener en cuenta que no todas las distintas series de máquinas virtuales podrían ofrecerse en todas las regiones de Azure (consulte el capítulo siguiente para obtener información sobre las regiones de Azure). También hay que saber que no todas las máquinas virtuales o series de máquinas virtuales están certificadas para SAP.

> [!IMPORTANT]
> Al usar aplicaciones basadas en SAP NetWeaver, solo se admite el subconjunto de tipos de máquinas virtuales y configuraciones indicados en la nota de SAP [1928533] .
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiones de Azure
Microsoft permite implementar máquinas virtuales en las denominadas "regiones de Azure". Una región de Azure puede constar de uno o varios centros de datos próximos entre sí. En la mayoría de las regiones geopolíticas en el mundo, Microsoft tiene al menos dos regiones de Azure. Por ejemplo, en Europa hay una región de Azure "Europa del Norte" y otra "Europa Occidental". Estas dos regiones de Azure dentro de una misma área geopolítica están separadas por una distancia suficiente para que los desastres naturales o técnicos no afecten a ambas al mismo tiempo. Puesto que Microsoft está creando continuamente nuevas regiones de Azure en diferentes áreas geopolíticas en todo el mundo, el número de las regiones de Azure crece de manera constante y en diciembre de 2015 ya había 20 y se habían anunciado otras nuevas. Los clientes pueden implementar sistemas SAP en todas estas regiones, incluidas las dos regiones de Azure en China. Para información actualizada sobre las regiones de Azure, consulte este sitio web: <https://azure.microsoft.com/regions/>.

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>El concepto de máquina virtual de Microsoft Azure
Microsoft Azure ofrece una solución de infraestructura como servicio (IaaS) para hospedar máquinas virtuales con funcionalidades similares como una solución de virtualización local. Se pueden crear máquinas virtuales desde el portal de Azure, PowerShell o CLI, que también ofrecen capacidades de implementación y administración.

El Administrador de recursos de Azure le permite aprovisionar sus aplicaciones mediante una plantilla declarativa. En una plantilla, puede implementar varios servicios junto con sus dependencias. Se usa la misma plantilla de forma repetida para implementar la aplicación en cada fase de su ciclo de vida.

Para obtener más información sobre las plantillas de ARM, consulte las siguientes referencias:

* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y CLI de Azure][virtual-machines-linux-cli-deploy-templates]
* [Administre máquinas virtuales con Azure Resource Manager y PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Otra característica interesante es la posibilidad de crear imágenes de máquinas virtuales, lo cual permite preparar algunos repositorios desde los que implementar rápidamente instancias de máquinas virtuales para satisfacer cualquier necesidad.

Para obtener más información sobre la creación de imágenes de máquinas virtuales, consulte [este artículo][virtual-machines-linux-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Dominios de error
Los dominios de error representan una unidad física de error. Están relacionados muy estrechamente con la infraestructura física en los centros de datos y, si bien un bastidor o blade físico puede considerarse un dominio de error, no hay ninguna relación directa entre ambos.

Cuando se implementen varias máquinas virtuales como parte de un sistema SAP en los servicios de máquina virtual de Microsoft Azure, se puede influir en el controlador de tejido de Azure para implementar su aplicación en diferentes dominios de error, lo cual cumple los requisitos del Acuerdo de Nivel de Servicio de Microsoft Azure. Sin embargo, no se tienen el control de la distribución de los dominios de error en una unidad de escalado de Azure (colección de cientos de nodos de proceso o nodos de almacenamiento y redes) ni de la asignación de máquinas virtuales a un dominio de error específico. Para ordenar al controlador de tejido de Azure que implemente un conjunto de máquinas virtuales en diferentes dominios de error, debe asignar un conjunto de disponibilidad de Azure a las máquinas virtuales en tiempo de implementación. Para más información sobre los conjuntos de disponibilidad de Azure, consulte el capítulo [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3] de este documento.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Dominios de actualización
Los dominios de actualización representan una unidad lógica que ayudan a determinar cómo se actualizará una máquina virtual dentro de un sistema SAP, compuesto por instancias de SAP ejecutadas en varias máquinas virtuales. Cuando se produzca una actualización, Microsoft Azure actualiza estos dominios de actualización uno a uno. Al distribuir las máquinas virtuales en tiempo de implementación en diferentes dominios de actualización, se puede proteger parcialmente el sistema SAP frente a posibles tiempos de inactividad. Para forzar que Azure implemente las máquinas virtuales de un sistema SAP distribuidas en diferentes dominios de actualización, se debe establecer un atributo específico durante la implementación de cada máquina virtual. De un modo similar a los dominios de error, una unidad de escalado de Azure se divide en varios dominios de actualización. Para ordenar al controlador de tejido de Azure que implemente un conjunto de máquinas virtuales en diferentes dominios de actualización, debe asignar un conjunto de disponibilidad de Azure a las máquinas virtuales en tiempo de implementación. Para más información sobre conjuntos de disponibilidad de Azure, consulte el capítulo [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3] a continuación.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidad de Azure
El controlador de tejido de Azure distribuirá las máquinas virtuales de Azure de un conjunto de disponibilidad de Azure en diferentes dominios de error y de actualización. El propósito de la distribución en diferentes dominios de error y de actualización es evitar el cierre de todas las máquinas virtuales de un sistema SAP en caso de mantenimiento de la infraestructura o error en un dominio de error. De forma predeterminada, las máquinas virtuales no forman parte de un conjunto de disponibilidad. La participación de una máquina virtual en un conjunto de disponibilidad se define en tiempo de implementación o más adelante mediante una reconfiguración y reimplementación de una máquina virtual.

Para comprender el concepto de conjuntos de disponibilidad de Azure y la relación entre los conjuntos de disponibilidad y los dominios de error y actualización, consulte [este artículo][virtual-machines-manage-availability].

Para definir conjuntos de disponibilidad para ARM mediante una plantilla de json, consulte las [especificaciones de la API de REST](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) y busque "disponibilidad".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Almacenamiento: Almacenamiento de Microsoft Azure y discos de datos
Las máquinas virtuales de Microsoft Azure usan varios tipos de almacenamiento. Al implementar SAP en los servicios de máquina virtual de Azure, es importante comprender las diferencias entre estos dos tipos principales de almacenamiento:

* Almacenamiento volátil, no persistente.
* Almacenamiento persistente.

El almacenamiento no persistente está conectado directamente a las máquinas virtuales en ejecución y reside en los propios nodos de proceso: el almacenamiento de instancia local (almacenamiento temporal). El tamaño depende del tamaño de la máquina virtual elegida en el inicio de la implementación. Este tipo de almacenamiento es volátil y, por tanto, el disco se inicializa cuando se reinicia una instancia de la máquina virtual. Normalmente, el archivo de paginación del sistema operativo se encuentra en este disco temporal.

- - -
> ![Windows][Logo_Windows] Windows
>
> En las máquinas virtuales de Windows la unidad temporal se monta como unidad D:\ en una máquina virtual implementada.
>
> ![Linux][Logo_Linux] Linux
>
> En las máquinas virtuales de Linux está montada como /mnt/Resource o /mnt. Para obtener información, consulte:
>
> * [Acoplamiento de un disco de datos a una máquina virtual Linux][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx>
>
>

- - -
La unidad en sí es volátil porque se almacena en el servidor host. Si mueve la máquina virtual en una reimplementación (por ejemplo, debido a mantenimiento en el host o el apagado y reinicio), el contenido de la unidad se pierde. Por tanto, no deben almacenarse datos importantes en esta unidad. El tipo de medios usados para este tipo de almacenamiento es diferente en cada serie de máquinas virtuales. Las características de rendimiento eran las siguientes en junio de 2015:

* A5-A7: rendimiento muy limitado. Solo se recomienda para el archivo de paginación.
* A8-A11: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie D: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie DS: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie G: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie GS: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.

Las afirmaciones anteriores se aplican a los tipos de máquinas virtuales que estén certificados para utilizarse con SAP. Algunas características de DBMS sacan partido a las series de máquinas virtuales con rendimiento y cantidad de IOPS excelentes. Consulte la [Guía de implementación de DBMS][dbms-guide] para más información.

El almacenamiento de Microsoft Azure proporciona almacenamiento persistente y los niveles habituales de protección y redundancia del almacenamiento SAN. Los discos basados en el almacenamiento de Azure son el disco duro virtual (VHD) ubicado en los servicios de almacenamiento de Azure. El disco de sistema operativo local (Windows C:\, Linux / [/dev/sda1 ]) se guarda en Azure Storage, al igual que los volúmenes o discos adicionales montados en la máquina virtual.

Se puede cargar un disco duro virtual desde el sistema local o crear unidades virtuales vacías en Azure para asociarlas a las máquinas virtuales implementadas. Los discos duros virtuales se denominan discos de Azure.

Después de crear o cargar un disco duro virtual en el almacenamiento de Azure, se puede montar o conectar esta unidad a una máquina virtual, así como copiar discos duros virtuales (no montados).

Dado que esos discos duros virtuales son persistentes, los datos y las modificaciones no se pierden en los reinicios y nuevas instancias de la máquina virtual. Incluso aunque se elimine una instancia, estos discos duros virtuales se mantienen a salvo y pueden volver a implementarse o, en el caso de discos de SO, pueden montarse en otras máquinas virtuales.

Dentro de la red de almacenamiento de Azure pueden configurarse varios niveles de redundancia:

* El nivel mínimo que se puede seleccionar es "redundancia local", que es equivalente a tres réplicas de los datos en el mismo centro de datos de una región de Azure (consulte el capítulo [Regiones de Azure][planning-guide-3.1]).
* En el almacenamiento con redundancia de zona se propagan las tres imágenes en diferentes centros de datos en la misma región de Azure.
* El nivel de redundancia predeterminado es la redundancia geográfica, que replica asincrónicamente el contenido en otras tres imágenes de los datos en otra región de Azure ubicada en la misma región geopolítica.

Consulte también la tabla de la parte superior de este artículo para ver las diferentes opciones de redundancia: <https://azure.microsoft.com/pricing/details/storage/>.

Las siguientes referencias incluyen más información sobre el almacenamiento de Azure:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Almacenamiento de Azure estándar
El almacenamiento de blobs estándar de Azure era el tipo de almacenamiento disponible cuando se lanzó IaaS de Azure. Se exigían cuotas de IOPS por cada disco duro virtual. La latencia experimentada no estaba en el mismo nivel que la de los dispositivos de SAN/NAS implementados habitualmente en sistemas de gama alta de SAP hospedados de forma local. No obstante, el almacenamiento estándar de Azure resultó suficiente para cientos de sistemas SAP implementados en Azure en ese momento.

El almacenamiento estándar de Azure se cobra según los datos reales que se almacenan, el volumen de las transacciones de almacenamiento, las transferencias de datos salientes y la opción de redundancia seleccionada. Pueden crearse varios discos duros virtuales con el tamaño máximo de 1 TB, pero como mientras permanezcan vacíos no se realiza ningún cobro. Si después se llena un disco duro virtual con 100 GB, se cobrará el almacenamiento de 100 GB y no el tamaño nominal con el que se creó.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Almacenamiento premium de Azure
En abril de 2015, Microsoft presentó el almacenamiento Premium de Azure. El almacenamiento Premium se introdujo con el objetivo de proporcionar lo siguiente:

* Mejor latencia de E/S
* Mejor rendimiento
* Menor variabilidad de la latencia de E/S

Para ello, se introdujeron numerosos cambios. Los dos más importantes son los siguientes:

* Uso de discos SSD en los nodos de almacenamiento de Azure
* Una nueva memoria caché de lectura respaldada por el SSD local de un nodo de proceso de Azure

En contraposición al almacenamiento Estándar, cuyas funcionalidades no dependían del tamaño del disco (o disco duro virtual), Premium Storage tiene actualmente 3 categorías diferentes de disco, que se indican al final de este artículo antes de la sección de preguntas más frecuentes: <https://azure.microsoft.com/pricing/details/storage/>.

Se puede comprobar que la cantidad de IOPS y el rendimiento de cada disco duro virtual dependen de la categoría de tamaño de los discos.

La base del coste en el caso de almacenamiento Premium no es el volumen real de los datos almacenados en estos discos duros virtuales, sino la categoría de tamaño de estas unidades virtuales, independientemente de la cantidad de datos almacenados en ellas.

También se pueden crear discos duros virtuales en almacenamiento Premium sin correspondencia directa con las categorías de tamaño mostradas a continuación. Esto puede ocurrir especialmente cuando se copian discos duros virtuales del almacenamiento estándar al almacenamiento Premium. En esos casos, se asigna la opción de disco de almacenamiento Premium inmediatamente superior.

Hay que tener en cuenta que solo ciertas series de máquinas virtuales pueden aprovechar el almacenamiento Premium de Azure. En diciembre de 2015, estas series eran la DS y GS. La serie DS es prácticamente idéntica a la serie D, con la excepción de que en la serie DS se pueden montar máquinas virtuales basadas en almacenamiento Premium además de los discos duros virtuales hospedados en el almacenamiento estándar de Azure. Ocurre lo mismo con la serie G en comparación con la serie GS.

En la sección sobre máquinas virtuales de la serie DS de [este artículo][virtual-machines-sizes] también se indica que existen limitaciones del volumen de datos en VHD de Premium Storage en lo que respecta a la granularidad del nivel de máquina virtual. Las máquinas virtuales de las series DS y GS también tienen limitaciones diferentes con respecto al número de discos duros virtuales que se pueden montar. Estos límites están documentados en el artículo mencionado anteriormente. Básicamente significa que si, por ejemplo, se montan 32 discos o discos duros virtuales P30 en una sola máquina virtual DS14, NO se puede obtener 32 veces el rendimiento máximo de un disco P30. En su lugar, el rendimiento máximo en el nivel de máquina virtual limitará el rendimiento, como se indica en el artículo.

Se puede encontrar más información sobre Premium Storage aquí: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>.

#### <a name="azure-storage-accounts"></a>Cuentas de almacenamiento de Azure
Al implementar servicios o máquinas virtuales en Azure, la implementación de discos duros virtuales e imágenes de máquinas virtuales debe estar organizada en unidades denominadas cuentas de almacenamiento de Azure. Al planear una implementación de Azure, debe considerar detenidamente las restricciones de Azure. Por un lado, hay un número limitado de cuentas de almacenamiento por suscripción de Azure. Aunque cada cuenta de almacenamiento de Azure puede contener un gran número de archivos de disco duro virtual, hay un límite fijo en el número total de IOPS por cuenta de almacenamiento. Al implementar cientos de máquinas virtuales de SAP con sistemas DBMS que generen una cantidad importante de llamadas de E/S, se recomienda distribuir las máquinas virtuales de DBMS con IOPS elevado entre varias cuentas de almacenamiento de Azure. Hay que tener cuidado para no superar el límite actual de cuentas de almacenamiento de Azure por suscripción. Dado que el almacenamiento es una parte fundamental de la implementación de bases de datos en un sistema SAP, este concepto se analiza con más detenimiento en la [Guía de implementación de DBMS][dbms-guide] ya mencionada.

Se puede encontrar más información sobre las cuentas de Azure Storage en [este artículo][storage-scalability-targets]. En este artículo se indican las diferencias de limitaciones entre las cuentas de almacenamiento estándar y las cuentas de almacenamiento Premium de Azure. Las mayores diferencias vienen del volumen de datos que se pueden almacenar en cada cuenta de almacenamiento. En el almacenamiento estándar el volumen es una magnitud mayor que en el almacenamiento Premium. Por otro lado, la cuenta de almacenamiento estándar está muy limitada en lo que respecta a IOPS (consulte la columna "Velocidad de solicitudes"), mientras que la cuenta de almacenamiento Premium de Azure no tiene estas limitaciones. Los detalles y los resultados de estas diferencias cuando se tratan en la sección sobre implementaciones de sistemas SAP, especialmente los servidores DBMS.

En una cuenta de almacenamiento, pueden crearse contenedores diferentes con el fin de organizar y clasificar varios discos duros virtuales. Estos contenedores se suelen usar para, por ejemplo, separar discos duros virtuales de diferentes máquinas virtuales. Usar un único contenedor o varios contenedores en una sola cuenta de almacenamiento de Azure no influye en el rendimiento.

Dentro de Azure, un nombre de disco duro virtual tiene la siguiente nomenclatura, que debe indicar un nombre único para el disco duro virtual en Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Como se ha mencionado, la cadena de texto anterior debe identificar de forma unívoca el disco duro virtual almacenado en el almacenamiento de Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Redes de Microsoft Azure
Microsoft Azure proporcionará una infraestructura de red que permite la correspondencia de todos los escenarios que deseamos obtener con el software SAP. Principales capacidades:

* Acceso desde el exterior directamente a las máquinas virtuales a través de ssh/VNC o de Windows Terminal Services
* Acceso a servicios y puertos específicos usados por las aplicaciones dentro de las máquinas virtuales
* Comunicación interna y resolución de nombres entre un grupo de máquinas virtuales implementadas como máquinas virtuales de Azure
* Conectividad entre locales entre la red local de un cliente y la red de Azure
* Conectividad entre sitios de Azure y varias regiones de Azure o varios centros de datos

Se puede encontrar más información así: <https://azure.microsoft.com/documentation/services/virtual-network/>

Hay muchas posibilidades diferentes para configurar el nombre y la resolución IP en Azure. En este documento, los escenarios exclusivos en la nube dependen del uso predeterminado de DNS de Azure (a diferencia de definir un servicio propio de DNS). También puede usarse un nuevo servicio DNS de Azure en lugar de configurar un servidor DNS propio. Se puede encontrar más información en [este artículo][virtual-networks-manage-dns-in-vnet] y en [esta página](https://azure.microsoft.com/services/dns/).

Para escenarios entre locales se da por hecho que las instalaciones locales de AD/OpenLDAP/DNS se han ampliado a través de VPN o conexión privada a Azure. Para determinados escenarios documentados aquí, sería necesario disponer de una réplica de AD/OpenLDAP instalada en Azure.

Dado que la red y la resolución de nombres es una parte fundamental de la implementación de bases de datos en un sistema SAP, este concepto se analiza con más detenimiento en la [Guía de implementación de DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes virtuales de Azure
Se puede definir el intervalo de direcciones IP privadas asignadas por la funcionalidad de DHCP de Azure mediante la creación de una red virtual de Azure. En escenarios entre locales, el intervalo de direcciones IP definido se seguirá asignando mediante DHCP por Azure. Sin embargo, la resolución de nombres de dominio se llevará a cabo en local (suponiendo que las máquinas virtuales formen parte de un dominio local) y, por tanto, se pueden resolver direcciones independientemente de los diferentes servicios en la nube de Azure.

[comment]: <> (MSSedusch still needed? TODO Originalmente, una red virtual de Azure estaba asociada a un grupo de afinidad. Esto implicaba que una red virtual en Azure quedaba restringida a la unidad de escalado de Azure a la que se asignaba el grupo de afinidad. Al final, el resultado era que la red virtual estaba restringida a los recursos disponibles en la unidad de escalado de Azure. Desde entonces, esta situación ha cambiado y ahora las redes virtuales de Azure pueden abarcar varias unidades de escalado de Azure. Sin embargo, es necesario que las redes virtuales de Azure **NO** estén asociadas a grupos de afinidad en el momento de la creación. Se mencionó anteriormente que, en contraposición a las recomendaciones del año anterior, ya **NO se deben aprovechar los grupos de afinidad de Azure**. Para más información, consulte <https://azure.microsoft.com/blog/regional-virtual-networks/>)

Cada máquina virtual de Azure debe estar conectada a una red virtual.

Se puede encontrar más información en [este artículo][resource-groups-networking] y en [esta página](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO Couldn't find an article which includes the OpenLDAP topic + ARM; )
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> De forma predeterminada, una vez que se implementa una máquina virtual no se puede cambiar la configuración de red virtual. La configuración de TCP/IP debe depender del servidor DHCP de Azure. El comportamiento predeterminado es la asignación de IP dinámicas.
>
>

La dirección MAC de la tarjeta de red virtual puede cambiar, por ejemplo, después de un cambio de tamaño. En ese caso, el SO invitado Windows o Linux identificará la nueva tarjeta de red y utilizará automáticamente DHCP para asignar las direcciones IP y DNS.

##### <a name="static-ip-assignment"></a>Asignación de direcciones IP estáticas
Se pueden asignar direcciones IP reservadas o fijas a las máquinas virtuales de una red virtual de Azure. La ejecución de máquinas virtuales en una red virtual de Azure es una gran oportunidad para aprovechar esta funcionalidad así lo requieren algunos escenarios. La asignación de IP seguirá siendo válida mientras exista la máquina virtual, independientemente de si la máquina virtual está en ejecución o cerrada. Como resultado, se debe tener en cuenta el número total de máquinas virtuales (en ejecución y detenidas) al definir el intervalo de direcciones IP para la red virtual. La asignación de dirección IP se mantiene hasta que se elimine la máquina virtual y su interfaz de red o hasta que se cancele la asignación. Para obtener más información, consulte [este artículo][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Varias NIC por máquina virtual
Se pueden definir varias tarjetas de interfaz de red virtual (vNIC) en una máquina virtual de Azure. Con la capacidad de tener varias vNIC, se puede comenzar a configurar el tráfico de red donde, por ejemplo, el tráfico del cliente se enrute a través de una vNIC y el tráfico back-end se enrute a través de una segunda vNIC. En función del tipo de máquina virtual, hay varias limitaciones con respecto al número de vNICs. En los artículos siguientes se indican los detalles, funcionalidades y restricciones:

* [Creación de una máquina virtual con varias NIC][virtual-networks-multiple-nics]
* [Implementación de varias máquinas virtuales de NIC con una plantilla][virtual-network-deploy-multinic-arm-template]
* [Implementación de máquinas virtuales con varias NIC mediante PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implementación de varias máquinas virtuales de la NIC con la CLI de Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividad de sitio a sitio
Las máquinas virtuales de Azure tienen características entre locales, mientras que las instalaciones locales están vinculadas a una conexión VPN permanente y transparente. Se espera que se convierta en el modelo de implementación de SAP más común en Azure. Se supone que los procesos y procedimientos operativos con instancias de SAP en Azure deben funcionar de forma transparente. Esto significa que se podrá imprimir a partir de estos sistemas, así como el sistema de administración del transporte (TMS) de SAP para el transporte de cambios de un sistema de desarrollo en Azure a un sistema de prueba de implementación local. Se puede encontrar más documentación sobre la conectividad de sitio a sitio en [este artículo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN
Para crear una conexión de sitio a sitio (centro de datos local al centro de datos de Azure), necesitará obtener o configurar un dispositivo VPN, o utilizar el servicio de Enrutamiento y acceso remoto (RRAS) que se introdujo como componente de software en Windows Server 2012.

* [Crear una red virtual con una conexión VPN de sitio a sitio mediante PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Acerca de los dispositivos VPN para las conexiones VPN Gateway de sitio a sitio][vpn-gateway-about-vpn-devices]
* [Preguntas más frecuentes sobre VPN Gateway][vpn-gateway-vpn-faq]

![Conexión de sitio a sitio entre sistemas locales y Azure][planning-guide-figure-600]

La ilustración anterior muestra dos suscripciones de Azure con subintervalos de direcciones IP reservados para las redes virtuales en Azure. La conectividad desde la red local a Azure se establece a través de VPN.

#### <a name="point-to-site-vpn"></a>VPN de punto a sitio
VPN de punto a sitio requiere que cada equipo cliente se conecte a Azure con su propio VPN. En los escenarios SAP examinados aquí, la conectividad de punto a sitio no es práctica. Por tanto, no se proporcionarán más referencias de la conectividad VPN punto a sitio.

[comment]: <> (MSSedusch -- More information can be found here)
[comment]: <> (MShermannd TODO Link no longer valid; But ARM is anyway not supported - see next link below)
[comment]: <> (MSSedusch -- <http://msdn.microsoft.com/library/azure/dn133798.aspx>.)
[comment]: <> (MShermannd TODO Point to Site not supported yet with ARM )
[comment]: <> (MSSedusch -- <https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### <a name="multi-site-vpn"></a>VPN de varios sitios
Asimismo, en la actualidad Azure ofrece la posibilidad de crear una conectividad VPN multisitio para una suscripción de Azure. Anteriormente, las suscripciones únicas estaban limitaban a una conexión VPN de sitio a sitio. Esta limitación desapareció con las conexiones VPN multisitio para suscripciones únicas. De este modo, una suscripción específica puede aprovechar varias regiones de Azure mediante configuraciones entre locales.

Para obtener documentación vea [este artículo][vpn-gateway-create-site-to-site-rm-powershell].

[comment]: <> (MShermannd TODO found no ARM docu link)

#### <a name="vnet-to-vnet-connection"></a>Conexión de red virtual a red virtual
Con VPN multisitio, se debe configurar una red virtual de Azure independiente en cada región. Sin embargo, a menudo se requiere que los componentes de software de las diferentes regiones se comuniquen entre sí. Lo ideal sería que esta comunicación no se enrutara desde una región de Azure a instalaciones locales, y desde ahí a otra región de Azure. Para el acceso directo, Azure ofrece la posibilidad de configurar una conexión de una red virtual de Azure en una región a una red virtual de Azure hospedada en otra región. Esta funcionalidad se denomina conexión de red virtual a red virtual. Se puede encontrar más información sobre esta funcionalidad aquí: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure--expressroute"></a>Conexión privada a Azure: ExpressRoute
Microsoft Azure ExpressRoute permite la creación de conexiones privadas entre centros de datos de Azure y la infraestructura local del cliente o un entorno de colocalización. ExpressRoute está disponible en varios proveedores de VPN de MPLS (conmutación por paquetes) u otros proveedores de servicios de red. Las conexiones ExpressRoute no pasan por la red pública de Internet. Las conexiones ExpressRoute ofrecen más confiabilidad, velocidad y seguridad en varios circuitos en paralelo, además de mayor velocidad y menor latencia que las conexiones a Internet habituales.

Se puede obtener más información sobre Azure ExpressRoute y su oferta aquí:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

ExpressRoute permite varias suscripciones de Azure a través de un circuito ExpressRoute, como está documentado aquí

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premise"></a>Tunelización forzada en instalaciones entre entornos
Con las máquinas virtuales que unan dominios locales a través de conexiones sitio a sitio, punto a sitio o de ExpressRoute, también hay que asegurarse de implementar la configuración de proxy de Internet para todos los usuarios de esas máquinas virtuales. De forma predeterminada, el software ejecutado en esas máquinas virtuales y los usuarios que empleen un navegador se conectarán directamente a Internet a través de Azure, en lugar de a través del proxy de la empresa. Aun así, la configuración de proxy no es una solución completa para dirigir el tráfico a través del proxy de la empresa, ya que es responsabilidad del software y los servicios buscar el servidor proxy. Si el software ejecutado en la máquina virtual no lleva a cabo esta operación o un administrador modifica la configuración, el tráfico a Internet podría desviarse de nuevo directamente a través de Azure.

Para evitar esta situación, puede configurar la tunelización forzada con conectividad de sitio a sitio entre instalaciones locales y Azure. La descripción detallada de la característica de tunelización forzada está publicada aquí <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>.

La tunelización forzada con ExpressRoute la habilitan clientes que anuncian una ruta predeterminada a través de las sesiones de emparejamiento BGP de ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Resumen de las redes de Azure
Este capítulo contiene muchos puntos importantes sobre la red de Azure. Este es un resumen de los puntos principales:

* Las redes virtuales de Azure permiten configurar la red según cada necesidad.
* Las redes virtuales de Azure pueden aprovecharse para asignar intervalos de direcciones IP a las máquinas virtuales o asignar direcciones IP fijas a máquinas virtuales.
* Para configurar una conexión de sitio a sitio o de punto a sitio, primero se debe crear una red virtual de Azure.
* Una vez que se ha implementado una máquina virtual, ya no se puede cambiar la red virtual asignada a la máquina virtual.

### <a name="quotas-in-azure-virtual-machine-services"></a>Cuotas de los servicios de máquina virtual de Azure
Debe quedar claro que la infraestructura de red y almacenamiento se comparte entre las máquinas virtuales que ejecuten una amplia variedad de servicios en la infraestructura de Azure. Y, al igual que en los centros de datos del cliente, hay un cierto aprovisionamiento excesivo de algunos de los recursos de la infraestructura. La plataforma Microsoft Azure utiliza cuotas de disco, de CPU, de red y otras cuotas para limitar el consumo de recursos y mantener un rendimiento uniforme y determinista.  Los diferentes tipos de máquinas virtuales (A5, A6, etc.) tienen diferentes cuotas de número de discos, CPU, RAM y red.

> [!NOTE]
> Los recursos de memoria y CPU de los tipos de máquina virtual compatibles con SAP se preasignan en los nodos de host. Esto significa que, una vez implementada la máquina virtual, los recursos en el host estarán disponibles como defina el tipo de máquina virtual.
>
>

Al planear y dimensionar SAP en soluciones de Azure, se deben tener en cuenta las cuotas de tamaño de cada máquina virtual.  [Aquí][virtual-machines-sizes] se describen las cuotas de máquinas virtuales.

Las cuotas descritas representan los valores máximos teóricos.  El límite de IOPS por disco duro virtual se puede lograr con valores de E/S pequeños (8 kB), pero posiblemente no pueda lograrse con valores de E/S grandes (1 MB).  El límite de IOPS se aplica en la granularidad de discos duros virtuales únicos.

Es posible utilizar el siguiente árbol de decisión aproximado para decidir si un sistema SAP encaja en los servicios de máquinas virtuales de Azure y sus funciones, o bien si un sistema existente debe configurarse de otro modo para poder implementar el sistema en Azure:

![Árbol de decisión para decidir la capacidad de implementación de SAP en Azure][planning-guide-figure-700]

**Paso 1:**La información más importante por la que empezar es el requisito de SAPS de un sistema SAP determinado. Los requisitos de SAPS deben dividirse en la parte de DBMS y en la de la aplicación SAP, aunque el sistema SAP ya esté implementado de forma local en una configuración de dos niveles. Para los sistemas existentes, los SAPS relacionados con el hardware que se usa se suele poder determinar o calcular mediante los bancos de pruebas de SAP existentes. Los resultados se pueden encontrar aquí: <http://global.sap.com/campaigns/benchmark/index.epx>.
En lo que respecta a los sistemas SAP de implementación reciente, tendrá que haber efectuado una tarea de determinación del tamaño con la que se habrán identificado los requisitos de SAPS del sistema.
Consulte también este blog y el documento adjunto para ver el ajuste de tamaño de SAP en Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>.

**Paso 2:**Para los sistemas existentes, se debe medir el volumen de E/S y las operaciones de E/S por segundo en el servidor de DBMS. En el caso de los sistemas planeados recientemente, la tarea de determinación de tamaño para el nuevo sistema también debe aportar una idea aproximada de los requisitos de E/S para DBMS. Si no está seguro, tendrá que terminar realizando una prueba de concepto.

**Paso 3:**Compare el requisito de SAPS del servidor de DBMS con los SAPS que pueden proporcionar los distintos tipos de máquinas virtuales de Azure. La información sobre los SAPS de los diferentes tipos de máquinas virtuales de Azure se incluye en la nota de SAP [1928533]. Primero nos debemos centrar en la máquina virtual de DBMS, puesto que la de la base de datos es la capa de un sistema SAP NetWeaver que no se escala horizontalmente en la mayoría de las implementaciones. Por el contrario, la capa de aplicaciones de SAP se puede escalar horizontalmente. Si ninguno de los tipos de máquinas virtuales de Azure compatibles con SAP puede proporcionar los SAPS necesarios, la carga de trabajo del sistema SAP planeado no podrá ejecutarse en Azure. Tendrá que implementar el sistema de forma local o cambiar su volumen de carga de trabajo.

**Paso 4**: Como se documenta [aquí][virtual-machines-sizes], Azure exige una cuota de E/S por segundo para cada VHD, al margen de si se usa almacenamiento estándar o premium. El número de VHD que se pueden montar varía según el tipo de máquina virtual. Como resultado, puede calcular el número máximo de IOPS que se puede lograr con cada uno de los distintos tipos de máquinas virtuales. Según el diseño del archivo de base de datos, puede seccionar los VHD para convertirlos en un volumen en el SO invitado. Sin embargo, si el volumen actual de IOPS de un sistema SAP implementado supera los límites calculados del tipo de máquina virtual más grande de Azure y no hay ninguna posibilidad de compensar con más memoria, la carga de trabajo del sistema SAP puede verse gravemente afectada. En tales casos, se puede llegar a un punto en el que no se debería implementar el sistema en Azure.

**Paso 5:**Especialmente en sistemas SAP que estén implementados localmente en configuraciones de dos niveles, lo más probable es que el sistema deba configurarse en Azure en una configuración de tres niveles. En este paso, debe comprobar si hay algún componente en la capa de aplicaciones de SAP que no se pueda escalar horizontalmente y que no podría encajar con los recursos de memoria y CPU que ofrecen los distintos tipos de máquinas virtuales de Azure. Si, en efecto, se detecta tal componente, no se podrá implementar en Azure el sistema SAP ni su carga de trabajo. Pero, si es posible escalar horizontalmente los componentes de aplicaciones de SAP en varias máquinas virtuales de Azure, el sistema podrá implementarse en dicha plataforma en la nube de Microsoft.

**Paso 6:**Si los componentes de capa de la aplicación de SAP y DBMS pueden ejecutarse en máquinas virtuales de Azure, se debe definir una serie de opciones de configuración relacionadas con los siguientes aspectos:

* Número de máquinas virtuales de Azure
* Tipos de máquinas virtuales para los componentes individuales
* Número de VHD en la máquina virtual de DBMS para proporcionar IOPS suficientes

## <a name="managing-azure-assets"></a>Administración de activos de Azure
### <a name="azure-portal"></a>Portal de Azure
El Portal de Azure constituye una de las tres interfaces creadas para administrar las implementaciones de máquinas virtuales de Azure. Es posible realizar las tareas de administración básicas, como implementar máquinas virtuales a partir de imágenes, a través del Portal de Azure. Además, la creación de cuentas de almacenamiento, redes virtuales y otros componentes de Azure son tareas que pueden desempeñarse con resultados muy satisfactorios en el Portal de Azure. No obstante, funciones como la carga de VHD de entornos locales a Azure o la copia de un VHD dentro de Azure constituyen tareas para las que se requiere bien una herramienta de terceros, bien conocimientos de administración a través de PowerShell o la CLI.

![Portal de Microsoft Azure - información general sobre las máquinas virtuales][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

También es posible llevar a cabo tareas de administración y configuración para las instancias de las máquinas virtuales desde el Portal de Azure.

Aparte de reiniciar y apagar una máquina virtual, también puede asociar, desasociar y crear discos de datos para la instancia de la VM con el objetivo de capturarla para preparar una imagen y configurar el tamaño de dicha instancia.

El Portal de Azure proporciona funciones básicas con las que implementar y configurar las máquinas virtuales y muchos otros servicios de Azure. Sin embargo, el portal no abarca todas las funciones disponibles. En el Portal de Azure, no es posible realizar tareas como las siguientes:

* Cargar discos duros virtuales en Azure
* Copiar máquinas virtuales

[comment]: <> (MShermannd TODO what about automation service for SAP VMs ? )
[comment]: <> (MSSedusch deployment of multiple VMs os meanwhile possible)
[comment]: <> (MSSedusch Also any type of automation regarding deployment is not possible with the Azure portal. Tareas como la implementación por script de varias máquinas virtuales no es posible mediante Azure Portal.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Administración mediante los cmdlets de Microsoft Azure PowerShell
Windows PowerShell es un marco eficaz y extensible que ha tenido una amplia adopción entre clientes que implementan un mayor número de sistemas en Azure. Es posible ejecutar de forma remota los cmdlets de PowerShell tras instalarlos en un equipo de escritorio, un portátil o una estación de administración dedicada.

En [este artículo][powershell-install-configure] se describe el proceso mediante el cual se habilita el uso de cmdlets de Azure PowerShell en un equipo de escritorio o portátil local, así como el modo de configurarlos para su uso con las suscripciones de Azure.

También podrá encontrar pasos más detallados sobre cómo instalar, actualizar y configurar los cmdlets de Azure PowerShell en [este capítulo de la Guía de implementación][deployment-guide-4.1].

Hasta el momento, de la experiencia de los clientes se extrae que PowerShell (PS) es sin lugar a dudas la herramienta más eficaz para implementar máquinas virtuales y crear pasos personalizados en la implementación de estas. Todos los clientes que ejecutan instancias de SAP en Azure usan cmdlets de PS como complemento a las tareas de administración que llevan a cabo en el Portal de Azure o incluso los utilizan exclusivamente para administrar sus implementaciones en Azure. Como los cmdlets específicos de Azure comparten la misma convención de nomenclatura que los más de 2000 cmdlets relacionados con Windows, a los administradores de Windows les resulta sencillo utilizarlos.

Consulte el ejemplo aquí: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>.

[comment]: <> (MShermannd TODO describe new CLI command when tested )
La implementación de la extensión de supervisión de Azure para SAP (consulte el capítulo [Solución de supervisión de Azure para SAP][planning-guide-9.1] de este documento) solo es posible mediante PowerShell o la CLI. Por lo tanto, es obligatorio para instalar y configurar PowerShell o la CLI al implementar o administrar un sistema SAP NetWeaver en Azure.  

Como Azure ofrece más funciones, se agregarán nuevos cmdlets de PS que requieren una actualización de los cmdlets. Por lo tanto, es recomendable consultar el sitio de descargas de Azure al menos una vez al mes <https://azure.microsoft.com/downloads/> para ver si se ha publicado una nueva versión de los cmdlets. La nueva versión simplemente se instalará sobre la anterior.

Para ver una lista general de comandos de PowerShell relacionados con Azure, consulte aquí: <https://msdn.microsoft.com/library/azure/dn708514.aspx>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Administración a través de los comandos de CLI de Microsoft Azure
Para los clientes que utilizan Linux y desean administrar recursos de Azure, es posible que puedan recurrir a PowerShell. Microsoft ofrece la CLI de Azure como una alternativa.
La CLI de Azure proporciona un conjunto de comandos de código abierto y multiplataforma para trabajar con la plataforma de Azure. La CLI de Azure proporciona muchas de las mismas funciones presentes en el Portal de Azure.

Para obtener más información sobre la instalación, la configuración y el uso de los comandos de la CLI para realizar tareas de Azure, consulte estos temas:

* [Instalación de la CLI de Azure][xplat-cli]
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y CLI de Azure][virtual-machines-linux-cli-deploy-templates]
* [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager][xplat-cli-azure-resource-manager]

Lea también el capítulo [CLI de Azure para máquinas virtuales de Linux][deployment-guide-4.5.2] de la [Guía de implementación][planning-guide] a fin de descubrir cómo usar la CLI de Azure para implementar la extensión de supervisión de Azure para SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes formas de implementar máquinas virtuales para SAP en Azure
En este capítulo, se expondrán las diferentes formas de implementar una máquina virtual en Azure. En este capítulo también se tratan los procedimientos de preparación adicionales, así como la administración de VHD y máquinas virtuales en Azure.

### <a name="deployment-of-vms-for-sap"></a>Implementación de máquinas virtuales para SAP
Microsoft Azure ofrece varias maneras de implementar máquinas virtuales y discos asociados. Por lo tanto, es muy importante comprender las diferencias, puesto que los preparativos de las máquinas virtuales pueden variar según el método de implementación. En general, analizaremos los siguientes escenarios:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado
Planea mover un sistema SAP específico desde un entorno local a Azure. Esto puede hacerse cargando en Azure el VHD que contiene el sistema operativo, los archivos binarios de SAP y los de DBMS, así como los discos duros virtuales con los archivos de registro y de datos del DBMS. En contraposición al [segundo escenario siguiente][planning-guide-5.1.2], mantendrá el nombre de host, el SID de SAP y las cuentas de usuario de SAP en la máquina virtual de Azure, ya que se configuraron en el entorno local. Por lo tanto, no es necesario generalizar la imagen. Consulte el capítulo [Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado][planning-guide-5.2.1] de este documento para obtener información sobre los pasos de preparación del entorno local y la carga de máquinas virtuales o VHD no generalizados en Azure. Lea el capítulo [Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP][deployment-guide-3.4] de la [Guía de implementación][deployment-guide] para obtener pasos detallados relativos a la implementación de dicha imagen en Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implementación de una máquina virtual con una imagen específica del cliente
Debido a los requisitos de revisión específicos de la versión del SO o DBMS, puede que las imágenes proporcionadas en Azure Marketplace no satisfagan sus necesidades. Por lo tanto, se recomienda crear una máquina virtual con su propia imagen privada de máquina virtual de SO o DBMS que pueda implementarse varias veces más adelante. Para preparar una imagen privada con fines de duplicación, se deben tener en cuenta los siguientes aspectos:

[comment]: <> (MSSedusch > See more details here :)
[comment]: <> (MShermannd TODO first link is about classic model. No se encontró un artículo del documento de Azure)
[comment]: <> (MSSedusch > <https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>)
[comment]: <> (MSSedusch > <http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)

- - -
> ![Windows][Logo_Windows] Windows
>
> La configuración de Windows (como el nombre de host y el SID de Windows) debe abstraerse o generalizarse en la máquina virtual local mediante el comando sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Siga los pasos descritos en los siguientes artículos para [SUSE][virtual-machines-linux-create-upload-vhd-suse] o [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] para preparar un disco duro virtual que se cargará en Azure.
>
>

- - -
Si ya ha instalado contenido de SAP en la máquina virtual local (especialmente en los sistemas de dos niveles), puede adaptar la configuración del sistema SAP después de realizar la implementación de la máquina virtual de Azure mediante el procedimiento de cambio de nombre de instancia admitido por el administrador de aprovisionamiento de software de SAP (nota de SAP [1619720]). Consulte los capítulos [Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP][planning-guide-5.2.2] y [Carga de un VHD desde una instalación local a Azure][planning-guide-5.3.2] de este documento para información sobre los pasos de preparación del entorno local y la carga de una máquina virtual generalizada en Azure. Lea el capítulo [Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP][deployment-guide-3.3] de la [Guía de implementación][deployment-guide] para obtener pasos detallados relativos a la implementación de dicha imagen en Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implementación de máquinas virtuales de Azure Marketplace
Desea usar una imagen proporcionada por Microsoft o un tercero en Azure Marketplace para implementar la máquina virtual. Una vez que se ha implementado la máquina virtual en Azure, sigue las mismas instrucciones y utilice las mismas herramientas para instalar el software de SAP o DBMS en la máquina virtual de la misma forma que lo haría en un entorno local. Para una descripción más detallada de la implementación, consulte el capítulo [Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP][deployment-guide-3.2] de la [Guía de implementación][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparación de máquinas virtuales con SAP para Azure
Antes de cargar las máquinas en Azure, debe asegurarse de que las VM y los VHD cumplan determinados requisitos. Existen pequeñas diferencias según el método de implementación que se utilice.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado
Un método de implementación habitual consiste en mover una máquina virtual existente en la que se ejecuta un sistema SAP del entorno local a Azure. Esa máquina virtual y el sistema SAP incluido en ella deben ejecutarse en Azure con el mismo nombre de host y, con toda probabilidad, el mismo SID de SAP. En este caso, no debe generalizarse el SO invitado de la máquina virtual para varias implementaciones. Si la red local se ha ampliado en Azure (consulte el capítulo [Entre entornos: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local][planning-guide-2.2] de este documento), se podrán usar en las máquinas virtuales las mismas cuentas de dominio utilizadas en el entorno local.

Estos son los requisitos que se deben cumplir a la hora de preparar su propio disco de VM de Azure:

* Originalmente, el VHD que contiene el sistema operativo podía tener un tamaño máximo de 127 GB. Esta limitación se eliminó a finales de marzo de 2015. Ahora el VHD que contiene el sistema operativo puede tener un tamaño máximo de 1 TB, al igual que cualquier otro VHD hospedado en Almacenamiento de Azure.

[comment]: <> (MShermannd  TODO have to check if CLI also converts to static )
* Debe tener el formato de VHD fijo. Los VHD dinámicos o en formato VHDx aún no se admiten en Azure. Los VHD dinámicos se convertirán en VHD estáticos al cargarlos con los cmdlets de PowerShell o la CLI.
* Los VHD que estén montados en la máquina virtual y se deban volver a montar en ella en Azure deben tener el formato VHD fijo. A los discos de datos también se les aplica el mismo límite de tamaño del disco del SO. Los VHD pueden tener un tamaño máximo de 1 TB. Los VHD dinámicos se convertirán en VHD estáticos al cargarlos con los cmdlets de PowerShell o la CLI.
* Agregue otra cuenta local con privilegios de administrador, que puede utilizar el soporte técnico de Microsoft o que se puede asignar como contexto para que se ejecuten los servicios y las aplicaciones hasta que se implemente la máquina virtual y se puedan usuarios más adecuados.
* Para los casos en los que se use el escenario de implementación exclusiva en la nube (consulte el capítulo [Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente][planning-guide-2.1] de este documento) en combinación con este método de implementación, las cuentas de dominio podrían no funcionar una vez que el disco de Azure se implemente en Azure. Esto se aplica especialmente a las cuentas que se usan para ejecutar servicios como las aplicaciones de DBMS o SAP. Por lo tanto, debe reemplazar esas cuentas de dominio por cuentas locales de máquinas virtuales y eliminar las cuentas de dominio locales de la máquina virtual. Como se describe en el capítulo [Entre entornos: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local][planning-guide-2.2] de este documento, mantener a los usuarios del dominio local en la imagen de la máquina virtual no supone un problema cuando esta se implementa en el escenario entre locales.
* Si las cuentas de dominio se usaron como usuarios o inicios de sesión de DBMS al ejecutar el sistema de forma local y esas máquinas virtuales se deben implementar en escenarios exclusivos en la nube, se tendrán que eliminar los usuarios del dominio. Debe asegurarse de agregar al administrador local, más otro usuario local de la máquina virtual, como inicio de sesión o usuario en el DBMS (como administradores).
* Agregue otras cuentas locales, ya que se podrían necesitar para el escenario de implementación concreto.

- - -
> ![Windows][Logo_Windows] Windows
>
> En este escenario, hace falta no generalizar (sysprep) la máquina virtual para cargarla e implementarla en Azure.
> Asegúrese de que la unidad D:\ no se esté usando. Configure el montaje automático para los discos conectados tal y como se describe en el capítulo [Configuración de montaje automático para discos conectados][planning-guide-5.5.3] del presente documento.
>
> ![Linux][Logo_Linux] Linux
>
> En este escenario, hace falta no generalizar (waagent -deprovision) la máquina virtual para cargarla e implementarla en Azure.
> Asegúrese de que no se usa /mnt/resource y de que todos los discos se montan mediante UUID. En lo que respecta al disco del SO, cerciórese de que la entrada del cargador de arranque también refleja el montaje basado en UUID.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP
Los archivos VHD que contienen un SO generalizado también se almacenan en contenedores en cuentas de almacenamiento de Azure. Puede implementar una nueva máquina virtual de dicho VHD de imagen si hace referencia al VHD como uno de origen en los archivos de plantilla de implementación, tal y como se describe en el capítulo [Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP][deployment-guide-3.3] de la [Guía de implementación][deployment-guide].

Estos son los requisitos que se deben cumplir a la hora de preparar su propia imagen de VM de Azure:

* Originalmente, el VHD que contiene el sistema operativo podía tener un tamaño máximo de 127 GB. Esta limitación se eliminó a finales de marzo de 2015. Ahora el VHD que contiene el sistema operativo puede tener un tamaño máximo de 1 TB, al igual que cualquier otro VHD hospedado en Almacenamiento de Azure.

[comment]: <> (MShermannd  TODO have to check if CLI also converts to static )
* Debe tener el formato de VHD fijo. Los VHD dinámicos o en formato VHDx aún no se admiten en Azure. Los VHD dinámicos se convertirán en VHD estáticos al cargarlos con los cmdlets de PowerShell o la CLI.
* Los VHD que estén montados en la máquina virtual y se deban volver a montar en ella en Azure deben tener el formato VHD fijo. A los discos de datos también se les aplica el mismo límite de tamaño del disco del SO. Los VHD pueden tener un tamaño máximo de 1 TB. Los VHD dinámicos se convertirán en VHD estáticos al cargarlos con los cmdlets de PowerShell o la CLI.
* Puesto que todos los usuarios del dominio registrados como usuarios de la máquina virtual no existirán en un escenario de implementación exclusiva en la nube (consulte el capítulo [Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente][planning-guide-2.1] de este documento), es posible que los servicios que usen estas cuentas de dominio no funcionen una vez que se haya implementado la imagen en Azure. Esto se aplica especialmente a las cuentas que se usan para ejecutar servicios como las aplicaciones de DBMS o SAP. Por lo tanto, debe reemplazar esas cuentas de dominio por cuentas locales de máquinas virtuales y eliminar las cuentas de dominio locales de la máquina virtual. Como se describe en el capítulo [Entre entornos: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local][planning-guide-2.2] de este documento, mantener a los usuarios del dominio local en la imagen de la máquina virtual podría no suponer un problema cuando esta se implementa en el escenario entre locales.
* Agregue otra cuenta local con privilegios de administrador, que puede utilizar el soporte técnico de Microsoft durante investigaciones de problemas o que se puede asignar como contexto para que se ejecuten los servicios y las aplicaciones hasta que se implemente la máquina virtual y se puedan usuarios más adecuados.
* En implementaciones exclusivas en la nube y situaciones en las que las cuentas de dominio se usaran como usuarios o inicios de sesión de DBMS mientras se ejecuta el sistema de forma local, se deberán eliminar los usuarios de dominio. Debe asegurarse de agregar al administrador local, más otro usuario local de la máquina virtual, como inicio de sesión o usuario de DBMS (como administradores).
* Agregue otras cuentas locales, ya que se podrían necesitar para el escenario de implementación concreto.
* Si la imagen contiene una instalación de SAP NetWeaver y es probable que se vaya a modificar el nombre original del host en el momento de la implementación de Azure, se recomienda copiar en la plantilla las últimas versiones del DVD del administrador de aprovisionamiento de software de SAP. Así podrá utilizar fácilmente la función de cambio de nombre de SAP proporciona para adaptar el nombre de host cambiado o modificar al SID del sistema SAP dentro de la imagen de la máquina virtual implementada en cuanto se inicie una nueva copia.

- - -
> ![Windows][Logo_Windows] Windows
>
> Asegúrese de que la unidad D:\ no se esté usando. Configure el montaje automático para los discos conectados tal y como se describe en el capítulo [Configuración de montaje automático para discos conectados][planning-guide-5.5.3] del presente documento.
>
> ![Linux][Logo_Linux] Linux
>
> Asegúrese de que no se usa /mnt/resource y de que todos los discos se montan mediante UUID. En lo que respecta al disco del SO, cerciórese de que la entrada del cargador de arranque también refleja el montaje basado en UUID.
>
>

- - -
* GUI de SAP (para fines de administración e instalación) pueden estar ya instalados en dicha plantilla.
* Es posible instalar cualquier otro software necesario para ejecutar la máquina virtual correctamente en escenarios entre locales siempre que este pueda funcionar con el cambio de nombre de la VM.

Si la máquina virtual está lo suficientemente preparada para ser genérica y finalmente independiente de cuentas o usuarios no disponibles en el escenario de implementación de Azure objetivo, se lleva a cabo el último paso de preparación, que consiste en la generalización de la imagen.

##### <a name="generalizing-a-vm"></a>Generalización de una máquina virtual
- - -
[comment]: <> (MShermannd  TODO have to find better articles / docu about generalizing the VMs for ARM )
> ![Windows][Logo_Windows] Windows
>
> El último paso es iniciar sesión en una máquina virtual con una cuenta de administrador. Abra una ventana de comandos de Windows como administrador. Vaya a ...\windows\system32\sysprep y ejecute sysprep.exe.
> Se mostrará una pequeña ventana. Es importante marcar la opción "Generalizar" (estará desmarcada de manera predeterminada) y cambie la opción de apagado de "Reiniciar" (el valor predeterminado) a "Apagar". Para este procedimiento, se supone que el proceso de sysprep se ejecuta localmente en el SO invitado de una máquina virtual.
> Si desea llevar a cabo el procedimiento con una máquina virtual que ya se ejecute en Azure, siga los pasos que se describen en [este artículo][virtual-machines-windows-capture-image].
>
> ![Linux][Logo_Linux] Linux
>
> [Captura de una máquina virtual con Linux para usarla como plantilla de Resource Manager][virtual-machines-linux-capture-image-resource-manager]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferencia de máquinas y discos duros virtuales entre locales a Azure
Como no es posible cargar discos e imágenes de máquinas virtuales en Azure mediante el Portal de Azure, tendrá que usar los cmdlets de Azure PowerShell o la CLI. Otra posibilidad es utilizar la herramienta AzCopy. Esta herramienta puede copiar discos duros virtuales entre locales y Azure (en ambas direcciones). También puede copiar discos duros virtuales entre las regiones de Azure. Consulte [esta documentación][storage-use-azcopy] para conocer cómo descargar y usar AzCopy.

Una tercera alternativa sería usar varias herramientas orientadas a GUI de terceros. Sin embargo, asegúrese de que estas herramientas son compatibles con los blobs en páginas de Azure. Para nuestros fines debemos usar el almacén de blobs en páginas de Azure (las diferencias se describen aquí: <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>). Por su parte, las herramientas que proporciona Azure también resultan muy eficaces a la hora de comprimir las máquinas y los discos duros virtuales que se deban cargar. Se trata de un factor importante, ya que esta eficacia en la compresión permite reducir el tiempo de carga (que varía según el vínculo de carga a Internet de las instalaciones locales y la región de implementación de Azure objetivo). En este sentido, resulta razonable suponer que la carga de una máquina virtual o un disco duro virtual desde una ubicación europea en los centros de datos de Azure situados en los EE. UU. tardará más que si se cargan los mismos elementos a los centros de datos europeos de Azure.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carga de un VHD desde una instalación local a Azure
Para cargar una máquina virtual o VHD existente desde la red local, dichos elementos deben cumplir los requisitos especificados en el capítulo [Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado][planning-guide-5.2.1] del presente documento.

No hace falta generalizar dicha máquina virtual, sino que se puede cargar con el estado y la forma que tenga tras su apagado en el lado local. Lo mismo se aplica a los VHD adicionales que no contengan ningún sistema operativo.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carga de un VHD y su conversión en un disco de Azure
En este caso, queremos cargar un VHD (con o sin un sistema operativo en él) y montarlo en una máquina virtual como un disco de datos o utilizarlo a modo de disco del SO. Se trata de un proceso compuesto por varios pasos.

**PowerShell**

* Inicie sesión en su suscripción con *Login-AzureRmAccount*
* Establezca la suscripción de su contexto con *Set-AzureRmContext* y el parámetro SubscriptionId o SubscriptionName (consulte <https://msdn.microsoft.com/library/mt619263.aspx>).
* Cargue el disco duro virtual con *Add-AzureRmVhd* en una cuenta de Azure Storage (consulte, <https://msdn.microsoft.com/library/mt603554.aspx>).
* Establezca el disco del sistema operativo de una nueva configuración de máquina virtual con *Set-AzureRmVMOSDisk* (consulte <https://msdn.microsoft.com/library/mt603746.aspx>).
* Cree una nueva máquina virtual a partir de la configuración de máquina virtual con *New-AzureRmVM* (consulte <https://msdn.microsoft.com/library/mt603754.aspx>).
* Agregue un disco de datos a una nueva máquina virtual con *Add-AzureRmVMDataDisk* (consulte <https://msdn.microsoft.com/library/mt603673.aspx>).

**CLI de Azure**

* Cambie al modo de Azure Resource Manager con *azure config mode arm*
* Inicie sesión en su suscripción con *azure login*
* Seleccione su suscripción con *azure account set`<subscription name or id`>*.
* Cargue el VHD con *azure storage blob upload* (consulte [Uso de la CLI de Azure con Azure Storage)][storage-azure-cli].
* Cree una nueva máquina virtual especificando el VHD cargado como disco del SO con *azure vm create* y el parámetro -d.
* Agregue un disco de datos a una nueva máquina virtual con *vm disk attach-new*

**Plantilla**

* Cargue el VHD con PowerShell o la CLI de Azure.
* Implemente la máquina virtual con una plantilla de JSON en la que se haga referencia al VHD, como se muestra en [esta plantilla de JSON de ejemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implementación de una imagen de máquina virtual
Para cargar una máquina virtual o un VHD existente desde la red local a fin de poder utilizarlo como una imagen de máquina virtual de Azure, dichos elementos tendrán que cumplir los requisitos enumerados en el capítulo [Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP][planning-guide-5.2.2] de este documento.

* Utilice *sysprep* en Windows o *waagent -deprovision* en Linux para generalizar la máquina virtual (consulte [Referencia técnica de Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows o[ Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager][virtual-machines-linux-capture-image-resource-manager-capture] para Linux).
* Inicie sesión en su suscripción con *Login-AzureRmAccount*
* Establezca la suscripción de su contexto con *Set-AzureRmContext* y el parámetro SubscriptionId o SubscriptionName (consulte <https://msdn.microsoft.com/library/mt619263.aspx>).
* Cargue el disco duro virtual con *Add-AzureRmVhd* en una cuenta de Azure Storage (consulte, <https://msdn.microsoft.com/library/mt603554.aspx>).
* Establezca el disco del sistema operativo de una nueva configuración de máquina virtual en el VHD con *Set-AzureRmVMOSDisk -SourceImageUri -CreateOption fromImage* (consulte <https://msdn.microsoft.com/library/mt603746.aspx>).
* Cree una nueva máquina virtual a partir de la configuración de máquina virtual con *New-AzureRmVM* (consulte <https://msdn.microsoft.com/library/mt603754.aspx>).

**CLI de Azure**

* Utilice *sysprep* en Windows o *waagent -deprovision* en Linux para generalizar la máquina virtual (consulte [Referencia técnica de Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows o[ Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager][virtual-machines-linux-capture-image-resource-manager-capture] para Linux).
* Cambie al modo de Azure Resource Manager con *azure config mode arm*
* Inicie sesión en su suscripción con *azure login*
* Seleccione su suscripción con *azure account set`<subscription name or id`>*.
* Cargue el VHD con *azure storage blob upload* (consulte [Uso de la CLI de Azure con Azure Storage)][storage-azure-cli].
* Cree una nueva máquina virtual especificando el VHD cargado como disco del SO con *azure vm create* y el parámetro -Q.

**Plantilla**

* Utilice *sysprep* en Windows o *waagent -deprovision* en Linux para generalizar la máquina virtual (consulte [Referencia técnica de Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows o[ Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager][virtual-machines-linux-capture-image-resource-manager-capture] para Linux).
* Cargue el VHD con PowerShell o la CLI de Azure.
* Implemente la VM con una plantilla de JSON en la que se haga referencia al VHD de imagen, como se muestra en [esta plantilla de JSON de ejemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-to-on-premises"></a>Descarga de discos duros virtuales al entorno local
La solución de infraestructura como servicio de Azure no solo funciona unidireccionalmente, en el sentido de que solo permite cargar sistemas SAP y VHD, sino que también puede mover sistemas SAP de Azure de nuevo al entorno local.

Mientras se estén descargando, los VHD no pueden estar activos. Incluso cuando se descargan los VHD que estén montados en máquinas virtuales, la VM debe estar apagada. Si solo quiere descargar el contenido de la base de datos, que después se debe usar para configurar un nuevo sistema local, y si desea que, durante el tiempo que dure la descarga y la configuración del nuevo sistema, el sistema presente en Azure continúe estando operativo, podría evitar un tiempo de inactividad prolongado realizando una copia de seguridad comprimida de la base de datos en un VHD para sencillamente descargar este último en lugar de descargar también la VM base del SO.

#### <a name="powershell"></a>PowerShell
Una vez que se haya detenido el sistema SAP y se haya apagado la máquina virtual, podrá usar el cmdlet de PowerShell Save-AzureRmVhd en el destino local para volver a descargar los discos VHD al entorno local. Para ello, necesita la dirección URL del VHD, que puede encontrar en la sección "Almacenamiento" del Portal de Azure (debe ir a la cuenta de almacenamiento y al contenedor de almacenamiento donde se creó el disco duro virtual), y tiene que saber dónde se debe copiar el VHD.

Entonces podrá utilizar el comando con solo definir el parámetro SourceUri como la URL del VHD que se va a descargar y LocalFilePath como la ubicación física del disco duro virtual (incluido su nombre). El comando podría presentar una sintaxis parecida a esta:

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

Para más información sobre el cmdlet Save-AzureRmVhd, consulte aquí <https://msdn.microsoft.com/library/mt622705.aspx>.

#### <a name="cli"></a>CLI
Una vez que se haya detenido el sistema SAP y se haya apagado la máquina virtual, podrá usar el comando de la CLI de Azure azure storage blob download en el destino local para volver a descargar los discos VHD al entorno local. Para ello, necesita el nombre y el contenedor del VHD, que puede encontrar en la sección "Almacenamiento" del Portal de Azure (debe ir a la cuenta de almacenamiento y al contenedor de almacenamiento donde se creó el disco duro virtual), y tiene que saber dónde se debe copiar el VHD.

Entonces podrá utilizar el comando con solo definir los parámetros blob y container del VHD que se va a descargar, y destination como la ubicación física de destino del disco duro virtual (incluido su nombre). El comando podría presentar una sintaxis parecida a esta:

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download>
```

### <a name="transferring-vms-and-vhds-within-azure"></a>Transferencia de máquinas y discos duros virtuales dentro de Azure
#### <a name="copying-sap-systems-within-azure"></a>Copia de los sistemas SAP en Azure
Es probable que un sistema SAP o incluso un servidor de DBMS dedicado que respalde una capa de aplicaciones de SAP se componga de varios VHD que contengan el sistema operativo con los archivos binarios o los archivos de registro y datos de la base de datos de SAP. Ni la funcionalidad de copiar discos duros virtuales de Azure ni la de guardar dichos VHD en el disco cuenta con un mecanismo de sincronización que pueda realizar instantáneas de varios VHD al mismo tiempo. Por lo tanto, el estado de los VHD copiados o guardados, aunque estén montados en la misma máquina virtual, sería distinto. Es decir, en el caso concreto de tener distintos archivos de registro y datos incluidos en los distintos VHD, la base de datos terminaría siendo incoherente.

**Conclusión: para copiar o guardar VHD que formen parte de la configuración de un sistema SAP, tendrá que detener dicho sistema y también apagar las máquinas virtuales implementadas. Solo entonces podrá copiar o descargar el conjunto de discos duros virtuales para crear una copia del sistema SAP en Azure o en el entorno local.**

Los discos de datos se almacenan como archivos VHD en una cuenta de almacenamiento de Azure y se pueden asociar a una máquina virtual directamente o utilizarse como una imagen. En este caso, el VHD se copia en otra ubicación antes de asociarse a la máquina virtual. El nombre completo del archivo VHD de Azure debe ser único dentro de Azure. Como ya se ha mencionado anteriormente, el nombre se compone de tres partes y presenta una sintaxis similar a esta:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### <a name="powershell"></a>PowerShell
Puede usar cmdlets de Azure PowerShell para copiar un VHD, como se muestra en [este artículo][storage-powershell-guide-full-copy-vhd].

##### <a name="cli"></a>CLI
Puede utilizar la CLI de Azure para copiar un VHD, como se muestra en [este artículo][storage-azure-cli-copy-blobs].

##### <a name="azure-storage-tools"></a>Herramientas de Almacenamiento de Azure
* <http://azurestorageexplorer.codeplex.com/releases/view/125870>

También hay ediciones profesionales del Explorador de almacenamiento de Azure, que podrá encontrar aquí:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

La copia de un VHD dentro de una cuenta de almacenamiento es un proceso que solo tarda unos pocos segundos (como sucede en el caso de la creación de instantáneas con copia diferida y copia en escritura por parte de hardware de SAN). Una vez que tenga una copia del archivo VHD, podrá asociarla a una máquina virtual o usarla como una imagen para conectar copias del VHD a máquinas virtuales.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### <a name="cli"></a>CLI
```
azure config mode arm

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copia de discos entre cuentas de almacenamiento de Azure
Esta tarea no puede realizarse en el Portal de Azure. Puede usar cmdlets de Azure PowerShell, la CLI de Azure o un explorador de almacenamiento de terceros. Los cmdlets de PowerShell o los comandos de la CLI pueden crear y administrar blobs, lo que abarca la capacidad de copiar blobs de forma asincrónica entre las distintas cuentas de almacenamiento y regiones de la suscripción de Azure.

##### <a name="powershell"></a>PowerShell
También es posible copiar discos duros virtuales entre suscripciones. Para más información, consulte [este artículo][storage-powershell-guide-full-copy-vhd].

El flujo básico de la lógica de los cmdlets de PowerShell se compone, a grandes rasgos, de estos pasos:

* Crear un contexto de la cuenta de almacenamiento de la cuenta de almacenamiento de origen con *New-AzureStorageContext* (consulte <https://msdn.microsoft.com/library/dn806380.aspx>).
* Cree un contexto de cuenta de almacenamiento para la cuenta de almacenamiento de destino con *New-AzureStorageContext* (consulte <https://msdn.microsoft.com/library/dn806380.aspx>).
* Inicie la copia con

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Compruebe el estado de la copia en un bucle con

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Asocie el nuevo VHD a una máquina virtual como se describió anteriormente.

Consulte [este artículo][storage-powershell-guide-full-copy-vhd] para obtener ejemplos.

##### <a name="cli"></a>CLI
* Inicie la copia con

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* Compruebe el estado de la copia en un bucle con

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Asocie el nuevo VHD a una máquina virtual como se describió anteriormente.

Consulte [este artículo][storage-azure-cli-copy-blobs] para obtener ejemplos.

### <a name="disk-handling"></a>Administración de discos
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estructura de VM/VHD para implementaciones de SAP
Lo ideal es que la administración de la estructura de una máquina virtual y los VHD asociados sea muy simple. En las instalaciones locales, los clientes han desarrollado muchas formas de estructurar una instalación de servidor.

* Un VHD base que contenga el sistema operativo y todos los archivos binarios de DBMS o SAP. Desde marzo de 2015, este VHD puede tener un tamaño máximo de 1 TB en lugar de las restricciones anteriores que lo limitaban a 127 GB.
* Uno o varios VHD que contengan el archivo de registro de DBMS de la base de datos SAP y el del área de almacenamiento temporal de DBMS (si el DBMS es compatible con esto). Si los requisitos de IOPS de registro de la base de datos son elevados, tendrá que seccionar varios VHD para lograr el volumen de IOPS necesario.
* Diversos VHD que contengan uno o dos archivos de base de datos de la base de datos de SAP, así como los archivos de datos temporales de DBMS (si el DBMS es compatible con esto).

![Configuración de referencia de máquinas virtuales IaaS de Azure en SAP][planning-guide-figure-1300]

[comment]: <> (MShermannd  TODO describe Linux structure  )

- - -
> ![Windows][Logo_Windows] Windows
>
> En el caso de muchos clientes, observamos configuraciones en las que, por ejemplo, los archivos binarios de SAP y DBMS no estaban instalados en la unidad c:\ en la que sí lo estaba el sistema operativo. Existían varios motivos para ello, pero cuando nos fijábamos en la causa principal, normalmente se debía a que los discos eran pequeños y actualizaciones del sistema operativo necesitaban más espacio hace 10 o 15 años. Ambas condiciones han dejado de aplicarse actualmente. En la actualidad, la unidad c:\ puede asignarse a máquinas virtuales o discos de gran volumen. A fin de simplificar la estructura de las implementaciones, se recomienda seguir el patrón de implementación para sistemas SAP NetWeaver en Azure que verá a continuación.
>
> El archivo de paginación del sistema operativo de Windows debe estar en la unidad D:\ (disco no persistente).
>
> ![Linux][Logo_Linux] Linux
>
> Coloque el archivo de intercambio de Linux en /mnt/mnt/resource en Linux, tal y como se describe en [este artículo][virtual-machines-linux-agent-user-guide]. El archivo de intercambio puede configurarse en el archivo de configuración del /etc/waagent.conf del agente de Linux. Agregue o cambie las siguientes opciones de configuración:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para activar los cambios, debe reiniciar al agente de Linux con

```
sudo service waagent restart
```

Lea la nota de SAP [1597355] para más información sobre el tamaño recomendado del archivo de intercambio.

- - -
Los requisitos de IOPS y la latencia necesaria determinan el número de VHD utilizados para los archivos de datos de DBMS y el tipo de Almacenamiento de Azure en el que se hospedan estos discos duros virtuales. Las cuotas exactas se describen en [este artículo][virtual-machines-sizes].

La experiencia en implementaciones de SAP que hemos acumulado durante los últimos 2 años nos ha permitido aprender una serie de lecciones que se pueden resumir del siguiente modo:

* El tráfico de IOPS a los diferentes archivos de datos no es siempre el mismo porque puede que los sistemas existentes del cliente presenten archivos de datos de distinto tamaño, los cuales que representan sus bases de datos de SAP. Por tanto, resultó ser más recomendable usar una configuración de RAD en lugar de varios VHD para colocar los LUN de archivos de datos extraídos de estos. Se han producido situaciones, especialmente con el almacenamiento estándar de Azure, en las que una tasa IOPS alcanzó la cuota de un solo VHD en relación con el registro de transacciones del DBMS. En estos casos, se recomienda usar el almacenamiento premium o, como método alternativo, agregar varios VHD de almacenamiento estándar con un RAID de software.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Procedimientos recomendados para SQL Server en Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Configuración del RAID de software en Linux][virtual-machines-linux-configure-raid]
> * [Configuración del LVM en una máquina virtual Linux en Azure][virtual-machines-linux-configure-lvm]
> * [Azure Storage secrets and Linux I/O optimizations (Secretos de Almacenamiento de Azure y optimizaciones de E/S de Linux)](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* El almacenamiento premium muestra un rendimiento bastante superior, especialmente en el caso de escrituras del registro de transacciones cruciales. Para escenarios de SAP en los que se espera obtener un rendimiento similar al de un entorno de producción, se recomienda encarecidamente usar una serie de máquinas virtuales que puedan aprovechar el almacenamiento premium de Azure.

Tenga en cuenta que el VHD que contiene el sistema operativo y, como se recomienda, también los archivos binarios de SAP y la base de datos (la máquina virtual base), ya no se encuentra limitado a 127 GB. Ahora puede tener un tamaño máximo de 1 TB. Con este límite, se contará con espacio suficiente para guardar todos los archivos necesarios, incluidos los registros de trabajos por lotes de SAP, por ejemplo.

Para más sugerencias e información, en concreto para las máquinas virtuales de DBMS, consulte la [Guía de implementación de DBMS][dbms-guide].

#### <a name="disk-handling"></a>Administración de discos
En la mayoría de los escenarios, tendrá que crear discos adicionales para implementar la base de datos de SAP en la máquina virtual. En el capítulo [Estructura de VM/VHD para implementaciones de SAP][planning-guide-5.5.1] de este documento ya vimos las consideraciones sobre el número de VHD. El Portal de Azure permite asociar y desasociar discos una vez implementada una máquina virtual base. Los discos se pueden asociar y desasociar cuando la máquina virtual se está ejecutando, así como cuando se encuentra detenida. Al asociar un disco, el Portal de Azure ofrece la posibilidad de asociar un disco vacío o uno existente que en ese momento no esté conectado a ninguna otra máquina virtual.

**Note:**Los VHD solo pueden estar asociados a una máquina virtual al mismo tiempo.

![Conexión/desconexión de discos con el almacenamiento estándar de Azure][planning-guide-figure-1400]

Debe decidir si desea crear un VHD nuevo y vacío (que se crearía en la misma cuenta de almacenamiento en la que se encuentre la máquina virtual base) o si quiere seleccionar un VHD existente que se haya cargado con anterioridad y deba asociar ahora a la VM.

**IMPORTANTE**: **no** se recomienda usar el almacenamiento en caché de host con almacenamiento Estándar de Azure. Debe dejar la preferencia de caché de host en el valor predeterminado, es decir, NINGUNO. Con el almacenamiento premium de Azure, debería habilitar el almacenamiento en caché de lectura si la característica de E/S es principalmente de lectura, como el tráfico de E/S relativo a archivos de datos de bases de datos. En el caso del archivo de registro de transacciones, se recomienda no utilizar el almacenamiento en caché.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Cómo conectar un disco de datos en Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Si los discos están asociados, tendrá que iniciar sesión en la máquina virtual para abrir el administrador de discos de Windows. Si el montaje automático no está habilitado como se recomienda en el capítulo [Configuración de montaje automático para discos conectados][planning-guide-5.5.3], el volumen recién conectado debe activarse e inicializarse.
>
> ![Linux][Logo_Linux] Linux
>
> Si los discos están asociados, deberá iniciar sesión en la máquina virtual e inicializar los discos, como se describe en [este artículo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
>
>

- - -
Si el nuevo disco es un disco vacío, también debe dar formato al disco. Para dar formato, especialmente en el caso de los archivos de datos y de registro de DBMS, se aplican las mismas recomendaciones en cuanto a implementaciones sin sistema operativo del DBMS.

Como ya se ha mencionado en el capítulo [El concepto de máquina virtual de Microsoft Azure][planning-guide-3.2], una cuenta de Azure Storage no proporciona recursos infinitos en lo relativo a volumen de E/S, E/S por segundo y volumen de datos. Por lo general, las máquinas virtuales de DBMS son las que más afectadas se ven por esto. Puede que resulte más recomendable utilizar una cuenta de almacenamiento independiente para cada máquina virtual si tiene que implementar algunas VM con un gran volumen de E/S. De este modo, podrá respetar el límite de volumen de la cuenta de almacenamiento de Azure. De lo contrario, tendrá que analizar cómo puede asignar estas máquinas virtuales a distintas cuentas de almacenamiento sin superar el límite de cada una de ellas. En la [Guía de implementación de DBMS][dbms-guide] podrá encontrar más información al respecto. También debe tener en cuenta estas limitaciones para máquinas virtuales de servidor de aplicaciones de SAP puras u otras VM que podrían terminar por necesitar VHD adicionales.

Otro tema pertinente para las cuentas de almacenamiento es si los VHD de una cuenta de almacenamiento se replican geográficamente. La replicación geográfica se habilita o deshabilita en el nivel de cuenta de almacenamiento, y no en el de máquina virtual. Si la replicación geográfica está habilitada, los VHD incluidos en la cuenta de almacenamiento podrían replicarse en otro centro de datos de Azure de la misma región. Antes tomar una decisión al respecto, debería considerar la siguiente restricción:

La replicación geográfica de Azure funciona localmente en cada disco duro virtual de una máquina virtual y no replica las E/S por orden cronológico a lo largo de varios VHD de una VM. Por lo tanto, el disco duro virtual que representa la máquina virtual base, así como cualquier VHD adicional asociado a la VM, se replican sin tener en cuenta los demás. Es decir, los cambios de los distintos VHD no están sincronizados. El hecho de que las E/S se replican independientemente del orden en el que se escriben significa que la replicación geográfica no aporta ninguna ventaja para los servidores de bases de datos que tienen sus bases de datos distribuidas en diversos VHD. Además del DBMS, también puede haber otras aplicaciones en las que los procesos escriben o manipulan datos en diferentes VHD y donde es importante mantener el orden de los cambios. Si esto último figura como requisito, no debe habilitarse la replicación geográfica en Azure. En función de si necesita o desea la habilitar la replicación geográfica para un conjunto de máquinas virtuales, pero no para otro conjunto, ya puede categorizar las VM y sus VHD relacionados en distintas cuentas de almacenamiento que tengan la replicación geográfica habilitada o deshabilitada.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Configuración de montaje automático para discos conectados
- - -
> ![Windows][Logo_Windows] Windows
>
> En el caso de las máquinas virtuales que se crean a partir de imágenes o discos propios, hay que comprobar y, posiblemente, establecer el parámetro automount. Si se establece este parámetro, la máquina virtual podrá, tras un reinicio o una nueva implementación en Azure, volver a montar automáticamente las unidades asociadas o montadas.
> El parámetro ya está establecido en las imágenes que proporciona Microsoft en Azure Marketplace.
>
> Para configurar el montaje automático, consulte la documentación del ejecutable de línea de comandos diskpart.exe aquí:
>
> * [Opciones de línea de comandos de DiskPart](https://technet.microsoft.com/library/cc766465.aspx)
> * [Automount (Montaje automático)](http://technet.microsoft.com/library/cc753703.aspx)
>
> Se debe abrir la ventana de línea de comandos de Windows como administrador.
>
> Si los discos están asociados, tendrá que iniciar sesión en la máquina virtual para abrir el administrador de discos de Windows. Si el montaje automático no está habilitado como se recomienda en el capítulo [Configuración de montaje automático para discos conectados][planning-guide-5.5.3], el volumen recién conectado debe activarse e inicializarse.
>
> ![Linux][Logo_Linux] Linux
>
> Debe inicializar un disco vacío recién asociado, como se describe en [este artículo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> También debe agregar discos nuevos a /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Implementación final
Para más información sobre la implementación final y los pasos exactos, especialmente en relación con la implementación de la supervisión extendida de SAP, consulte la [Guía de implementación][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Acceso a sistemas SAP que se ejecutan en máquinas virtuales de Azure
Para escenarios exclusivos en la nube, se recomienda establecer la conexión a esos sistemas SAP a través de la red pública de Internet mediante la GUI de SAP. En estos casos, deben aplicarse los siguientes procedimientos.

Más adelante en el documento, veremos el otro escenario principal, la conexión a sistemas SAP en implementaciones entre locales que tienen una conexión de sitio a sitio (túnel VPN) o conexión de ExpressRoute de Azure entre los sistemas locales y los de Azure.

### <a name="remote-access-to-sap-systems"></a>Acceso remoto a sistemas SAP
Con Azure Resource Manager, ya no hay ningún punto de conexión predeterminado, como sucedía en el modelo clásico anterior. Todos los puertos de una máquina virtual de Azure Resource Manager están abiertos siempre que se cumplan estos requisitos:

1. No haya ningún grupo de seguridad de red definido para la subred o la interfaz de red. El tráfico de red a las máquinas virtuales de Azure puede protegerse mediante los denominados "grupos de seguridad de red". Para más información, consulte [¿Qué es un grupo de seguridad de red?][virtual-networks-nsg]
2. No se haya definido ningún Azure Load Balancer para la interfaz de red.   

Consulte la diferencia de arquitectura entre el modelo clásico y el de Azure Resource Manager, que se describe en [este artículo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Configuración de la conectividad del sistema SAP y la GUI de SAP para el escenario exclusivo en la nube
Consulte este artículo que describe los detalles de este tema: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>.

#### <a name="changing-firewall-settings-within-vm"></a>Cambio de la configuración del firewall en la máquina virtual
Puede que haga falta configurar el firewall en las máquinas virtuales para permitir el tráfico entrante al sistema SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> De forma predeterminada, el Firewall de Windows está activado en una máquina virtual implementada de Azure. Ahora debe permitir la apertura del puerto de SAP; de lo contrario, la GUI de SAP no podrá conectarse.
> Para ello, siga estos pasos:
>
> * Abra Panel de control\Sistema y seguridad\Firewall de Windows y vaya a "Configuración avanzada".
> * Ahora haga clic con el botón derecho en Reglas de entrada y seleccione "Nueva regla".
> * En el asistente que aparece a continuación, elija crear una nueva regla de "Puerto".
> * En el paso siguiente del asistente, deje marcada la opción TCP y escriba el número del puerto que desea abrir. Puesto que el identificador de instancia de SAP es 00, utilizamos 3200. Si la instancia tiene un número de instancia distinto, deberá abrir el puerto que definió anteriormente según el número de instancia.
> * En la siguiente ventana del asistente, tendrá que dejar marcado el elemento "Permitir la conexión".
> * En el siguiente paso, tendrá que definir si la regla se aplica a redes de tipo Dominio, Privado o Público. Ajuste las opciones necesarias para satisfacer sus necesidades. Sin embargo, si va a conectarse a la GUI de SAP desde el exterior a través de la red pública, tendrá que tener la regla aplicada a dicho tipo de red.
> * En el último paso del asistente, debe asignar un nombre a la regla y, después, guardarla presionando "Finalizar".
>
> La regla se empieza a aplicar de inmediato.
>
> ![Definición de la regla de puertos][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Las imágenes de Linux de Azure Marketplace no habilitan el firewall iptables de forma predeterminada, por lo que la conexión a su sistema SAP debe funcionar. Si habilitó iptables u otro firewall, consulte la documentación de iptables o del firewall utilizado para permitir el tráfico TCP entrante al puerto 32xx (donde xx es el número de su sistema SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Recomendaciones de seguridad
La GUI de SAP no se conecta inmediatamente a cualquiera de las instancias de SAP (puerto 32xx) que se están ejecutando, sino que primero se conecta mediante el puerto abierto para el proceso de servidor de mensajes de SAP (puerto 36xx). Anteriormente, el servidor de mensajes usaba el mismo puerto para la comunicación interna con las instancias de la aplicación. Para evitar que los servidores de aplicaciones locales se comuniquen por accidente con un servidor de mensajes en Azure, es posible cambiar los puertos de comunicación internos. Se recomienda encarecidamente cambiar la comunicación interna entre el servidor de mensajes de SAP y sus instancias de aplicación a un número de puerto diferente en los sistemas que se hayan clonado a partir de sistemas locales, como un clon de desarrollo para pruebas de proyectos, entre otros casos. Esto puede hacerse con el parámetro de perfil predeterminado:

> rdisp/msserv_internal
>
>

como se documenta en: <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm>.

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Conceptos de implementación solo en la nube de instancias de SAP
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Escenario de demostración/aprendizaje de máquina virtual única con SAP NetWeaver
![Ejecución de sistemas de demostración de SAP de una sola máquina virtual con los mismos nombres de VM, de forma aislada en los servicios en la nube de Azure][planning-guide-figure-1700]

En este caso (consulte el capítulo [Solo en la nube][planning-guide-2.1] de este documento), se va a implementar un escenario habitual de sistema de demostración o aprendizaje en el que dicho escenario se encuentra por completo en una sola máquina virtual. Se supone que la implementación se realiza mediante plantillas de imagen de máquina virtual. También se supone que se deben implementar varias de estas máquinas virtuales de demostración o aprendizaje, y que estas tendrán el mismo nombre.

La suposición es que ha creado una imagen de máquina virtual como se describe en algunas secciones del capítulo [Preparación de máquinas virtuales con SAP para Azure][planning-guide-5.2] de este documento.

La secuencia de eventos para implementar el escenario es similar a esta:

[comment]: <> (MShermannd  TODO have to provide ARM sample / description using json template + clarification regarding unique VM name within ARM virtual network  )   
##### <a name="powershell"></a>PowerShell
* Cree un nuevo grupo de recursos para cada entorno de aprendizaje o demostración.

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* Creación de una cuenta de almacenamiento nueva

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Cree una nueva red virtual para cada entorno de aprendizaje o demostración a fin de habilitar el uso del mismo nombre de host y las mismas direcciones IP. La red virtual está protegida por un grupo de seguridad de red que solo permite el tráfico dirigido al puerto 3389 para habilitar el acceso de escritorio remoto y al puerto 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Cree una nueva dirección IP pública que se pueda usar para acceder a la máquina virtual desde Internet.

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Cree una nueva interfaz de red para la máquina virtual.

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Cree una máquina virtual. Para el escenario exclusivo en la nube, todas las máquinas virtuales presentarán el mismo nombre. El SID de SAP de las instancias de SAP NetWeaver de esas máquinas virtuales será el mismo. Dentro de un grupo de recursos de Azure, el nombre de la máquina virtual debe ser único, pero puede ejecutar máquinas virtuales con el mismo nombre en diferentes grupos de recursos de Azure. La cuenta "Administrador" predeterminada de Windows o "root" de Linux no son válidas. Por lo tanto, se debe definir un nuevo nombre de usuario administrador, junto con una contraseña. También debe definirse el tamaño de la máquina virtual.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, agregue discos adicionales y restaure el contenido necesario. Tenga en cuenta que todos los nombres de blob (direcciones URL para acceder a los blobs) deben ser únicos dentro de Azure.

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### <a name="cli"></a>CLI
El siguiente código de ejemplo puede utilizarse en Linux. En el caso de Windows, use PowerShell como se describe arriba o adapte el ejemplo para usar %rgName% en lugar de $rgName y establezca la variable de entorno con el comando de Windows *set*.

* Cree un nuevo grupo de recursos para cada entorno de aprendizaje o demostración.

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* Creación de una cuenta de almacenamiento nueva

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* Cree una nueva red virtual para cada entorno de aprendizaje o demostración a fin de habilitar el uso del mismo nombre de host y las mismas direcciones IP. La red virtual está protegida por un grupo de seguridad de red que solo permite el tráfico dirigido al puerto 3389 para habilitar el acceso de escritorio remoto y al puerto 22 para SSH.

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* Cree una nueva dirección IP pública que se pueda usar para acceder a la máquina virtual desde Internet.

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* Cree una nueva interfaz de red para la máquina virtual.

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet
```

* Cree una máquina virtual. Para el escenario exclusivo en la nube, todas las máquinas virtuales presentarán el mismo nombre. El SID de SAP de las instancias de SAP NetWeaver de esas máquinas virtuales será el mismo. Dentro de un grupo de recursos de Azure, el nombre de la máquina virtual debe ser único, pero puede ejecutar máquinas virtuales con el mismo nombre en diferentes grupos de recursos de Azure. La cuenta "Administrador" predeterminada de Windows o "root" de Linux no son válidas. Por lo tanto, se debe definir un nuevo nombre de usuario administrador, junto con una contraseña. También debe definirse el tamaño de la máquina virtual.

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* Opcionalmente, agregue discos adicionales y restaure el contenido necesario. Tenga en cuenta que todos los nombres de blob (direcciones URL para acceder a los blobs) deben ser únicos dentro de Azure.

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### <a name="template"></a>Plantilla
Puede utilizar las plantillas de ejemplo del repositorio de plantillas de inicio rápido de Azure en GitHub.

* [Máquina virtual de Linux simple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Máquina virtual de Windows simple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Máquina virtual a partir de imagen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementación de un conjunto de máquinas virtuales que necesitan comunicarse en Azure
Este escenario exclusivo en la nube es uno habitual para fines de aprendizaje y demostración, en el que el software que representa el escenario de aprendizaje o demostración se divide entre varias máquinas virtuales. Los diferentes componentes instalados en las distintas máquinas virtuales deben comunicarse entre sí. Una vez más, en este escenario no se requiere ninguna comunicación de red local ni ningún escenario entre locales.

Este escenario es una extensión de la instalación descrita en el capítulo [Escenario de demostración/aprendizaje de máquina virtual única con SAP NetWeaver][planning-guide-7.1] de este documento. En este caso, se agregarán más máquinas virtuales a un grupo de recursos existente. En el ejemplo siguiente el entorno de aprendizaje consta de una máquina virtual de SAP ASCS/SCS, una VM que ejecuta un DBMS y otra de instancia del servidor de aplicaciones de SAP.

Antes de poner en práctica este escenario, tendrá que considerar la configuración básica, como ya se hizo en el escenario anterior.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nomenclatura de los grupos de recursos y las máquinas virtuales
Todos los nombres de los grupos de recursos deben ser únicos. Desarrolle su propio esquema de nomenclatura de los recursos como, por ejemplo, `<rg-name`>-sufijo.

El nombre de la máquina virtual tiene que ser único dentro del grupo de recursos.

#### <a name="setup-network-for-communication-between-the-different-vms"></a>Configuración de la red para la comunicación entre las distintas máquinas virtuales
![Conjunto de máquinas virtuales dentro de una red virtual de Azure][planning-guide-figure-1900]

Para evitar colisiones de nomenclatura con clones de los mismos entornos de aprendizaje o demostración, deberá crear una red virtual de Azure para cada entorno. La resolución de nombres DNS correrá a cargo de Azure o podrá configurar su propio servidor DNS fuera de Azure (sobre lo cual no se proporcionarán más detalles en el presente artículo). En este escenario, no se debe configurar un DNS propio. La comunicación a través de los nombres de host se habilitará para todas las máquinas virtuales dentro de una red virtual de Azure.

Los motivos para separar los entornos de aprendizaje o demostración por redes virtuales en lugar de por grupos de recursos pueden ser los siguientes:

* Tal y como está configurado, el entorno de SAP necesita su propio AD/OpenLDAP y un servidor de dominio debe formar parte de cada uno de los entornos.  
* Tal y como está configurado, el entorno de SAP tiene componentes que deben funcionar con direcciones IP fijas.

Se puede encontrar información más detallada sobre las redes virtuales de Azure y cómo definirlas en [este artículo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implementación de máquinas virtuales de SAP con conectividad de red corporativa (entre locales)
Ejecuta un entorno de SAP y desea dividir la implementación entre equipos sin sistema operativo para servidores de DBMS de gama alta, entornos locales virtualizados para capas de aplicaciones, así como sistemas SAP e IaaS de Azure de menor tamaño configurados en dos niveles. El supuesto básico es que los sistemas SAP dentro de un entorno de SAP deben comunicarse entre sí y con muchos otros componentes de software implementados en la empresa, independientemente del modo de implementación. El modo de implementación tampoco debería introducir ninguna diferencia para el usuario final que se conecta con la GUI de SAP u otras interfaces. Estas condiciones solo pueden cumplirse cuando se dispone de servicios de Active Directory/OpenLDAP locales y servicios DNS extendidos a los sistemas de Azure por medio de una conectividad de sitio a sitio o de varios sitios, o mediante conexiones privadas como Azure ExpressRoute.

Para más información sobre los detalles de implementación de SAP en Azure, se recomienda leer el capítulo [Conceptos de implementación solo en la nube de instancias de SAP][planning-guide-7] de este documento, donde se explican algunas de las construcciones básicas de Azure y cómo deben utilizarse con aplicaciones de SAP en Azure.

### <a name="scenario-of-a-sap-landscape"></a>Escenario de un entorno de SAP
El escenario entre locales se puede describir aproximadamente como se indica en los gráficos siguientes:

![Conectividad de sitio a sitio entre activos locales y de Azure][planning-guide-figure-2100]

El escenario mostrado anteriormente describe un escenario donde los AD/OpenLDAP y DNS locales se extienden a Azure. En el lado local, hay un intervalo de direcciones IP determinado reservado por la suscripción de Azure. El intervalo de direcciones IP se asignará a una red virtual de Azure en el lado de Azure.

#### <a name="security-considerations"></a>Consideraciones sobre la seguridad
El requisito mínimo es el uso de protocolos de comunicaciones seguras como SSL/TLS para el acceso del navegador o conexiones basadas en VPN para el acceso del sistema a los servicios de Azure. Se presupone que las empresas controlan la conexión VPN entre su red corporativa y Azure de forma muy distinta. Puede que algunas empresas se limiten a abrir todos los puertos. Es posible que otras deseen ser muy precisas, por ejemplo, en cuanto a los puertos que hay que abrir.

En la tabla siguiente, se enumeran los puertos de comunicación de SAP típicos. Básicamente, basta con abrir el puerto de la puerta de enlace de SAP.

| Servicio | Nombre del puerto | Ejemplo `<nn`> = 01 | Intervalo predeterminado (mín.-máx.) | Comentario |
| --- | --- | --- | --- | --- |
| Distribuidor |sapdp`<nn>` consulte * |3201 |3200-3299 |Distribuidor de SAP, utilizado por la GUI de SAP para Windows y Java |
| Servidor de mensajes |sapms`<sid`> consulte ** |3600 |sapms libre`<anySID`> |sid = identificador del sistema SAP |
| Puerta de enlace |sapgw`<nn`> consulte * |3301 |libre |Puerta de enlace de SAP, utilizada para la comunicación de CPIC y RFC |
| Enrutador de SAP |sapdp99 |3299 |libre |Solo los nombres de servicio de CI (instancia central) se pueden reasignar a /etc/services con un valor arbitrario después de la instalación. |

*) nn = número de instancia de SAP

**) sid = identificador del sistema SAP

Podrá encontrar información más detallada sobre los puertos necesarios para diferentes productos o servicios de productos de SAP aquí <http://scn.sap.com/docs/DOC-17124>.
Con este documento debería poder abrir los puertos dedicados del dispositivo VPN necesarios para escenarios y productos de SAP concretos.

Otra posible medida de seguridad para la implementación de máquinas virtuales en este escenario es la creación de un [grupo de seguridad de red][virtual-networks-nsg] para definir las reglas de acceso.

### <a name="dealing-with-different-virtual-machine-series"></a>Manejo de varias series de máquinas virtuales
Durante los últimos 12 meses, Microsoft agregó muchos más tipos VM que difieren en el número de vCPU, en la memoria o, lo que es más importante, en el hardware en el que se ejecutan. No todas las máquinas virtuales son compatibles con SAP (consulte los tipos de VM admitidos en la nota de SAP [1928533]). Algunas de estas máquinas virtuales se ejecutan en generaciones de hardware de host distintas. Estas generaciones de hardware de host se implementan en la granularidad de una unidad de escalado de Azure. Esto significa que se pueden dar casos en los que los diferentes tamaños de VM elegidos no se puedan ejecutar en la misma unidad de escalado. Un conjunto de disponibilidad tiene una capacidad limitada para abarcar unidades de escalado basadas en diversos productos de hardware.  Por ejemplo, si desea ejecutar el DBMS en máquinas virtuales A5-A11 y la capa de aplicación de SAP en máquinas virtuales de la serie G, deberá implementar un único sistema SAP o diversos sistemas SAP en conjuntos de disponibilidad distintos.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Impresión en una impresora de red local desde la instancia de SAP en Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Impresión por TCP/IP en el escenario entre locales
La configuración de las impresoras de red basadas en TCP/IP locales en una VM de Azure es generalmente la misma que la de la red corporativa, partiendo de la base de que tenga establecidos un túnel VPN de sitio a sitio o una conexión ExpressRoute.

- - -
> ![Windows][Logo_Windows] Windows
>
> Para ello, siga estos pasos:
>
> * Algunas impresoras de red incluyen un Asistente para la configuración, lo que permite configurarlas más fácilmente en una VM de Azure. Si no se distribuyó ningún software de asistente con la impresora, la forma "manual" de configurar la impresora consiste en crear un nuevo puerto de impresora TCP/IP.
> * Abra Panel de control -> Dispositivos e impresoras -> Agregar una impresora.
> * Elija Agregar una impresora por medio de una dirección TCP/IP o un nombre de host.
> * Escriba la dirección IP de la impresora.
> * Puerto de impresora estándar 9100.
> * Si es necesario, instale manualmente el controlador de impresora adecuado.
>
> ![Linux][Logo_Linux] Linux
>
> * Como en Windows, solo tiene que seguir el procedimiento estándar para la instalación de una impresora de red.
> * Solo siga las guías de Linux públicas de [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) o [Red Hat](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre cómo agregar una impresora.
>
>

- - -
![Impresión en red][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impresora basada en host por SMB (impresora compartida) en el escenario entre locales
Las impresoras basadas en host están diseñadas para no ser compatibles con la red. No obstante, una impresora basada en host se puede compartir entre los equipos de una red si la impresora está conectada a un equipo encendido. Conecte la red corporativa sitio a sitio o por ExpressRoute y comparta la impresora local. El protocolo SMB usa NetBIOS en lugar de DNS como servicio de nombres. El nombre de host de NetBIOS puede ser diferente del nombre de host de DNS. Lo normal es que el nombre de host de NetBIOS y el nombre de host de DNS sean idénticos. El dominio de DNS no tiene sentido en el espacio de nombres de NetBIOS. En consecuencia, el nombre de host de DNS completo que consta del nombre de host de DNS y el dominio de DNS no debe utilizarse en el espacio de nombres de NetBIOS.

El recurso compartido de impresora se identifica mediante un nombre único en la red:

* Nombre de host del host de SMB (se requiere siempre).
* Nombre del recurso compartido (se requiere siempre).
* Nombre del dominio si el recurso compartido de impresora no está en el mismo dominio que el sistema SAP.
* Además, puede que se requieran un nombre de usuario y una contraseña para acceder al recurso compartido de impresora.

Control de

- - -
> ![Windows][Logo_Windows] Windows
>
> Comparta la impresora local.
> En la máquina virtual de Azure, abra el Explorador de Windows y escriba el nombre de recurso compartido de impresora.
> El Asistente para la instalación de la impresora le guiará a través del proceso de instalación.
>
> ![Linux][Logo_Linux] Linux
>
> A continuación, se ofrecen algunos ejemplos de documentación sobre la configuración de impresoras de red en Linux e incluso un capítulo sobre la impresión en Linux. Funcionará del mismo modo que en una máquina virtual de Linux en Azure si la máquina virtual forma parte de una VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Impresora USB (reenvío de impresora)
En Azure, la capacidad de los Servicios de Escritorio remoto para permitir a los usuarios que accedan a sus dispositivos de impresora local en una sesión remota no está disponible.

- - -
> ![Windows][Logo_Windows] Windows
>
> Se pueden encontrar más detalles sobre la impresión con Windows aquí: <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integración de sistemas SAP de Azure en el sistema de corrección y transporte (TMS) en el escenario entre locales
Se debe configurar Change and Transport System (TMS) de SAP para exportar las solicitudes de transporte en todos los sistemas del entorno e importarlas de ellos. Se presupone que las instancias de desarrollo de un sistema SAP (de desarrollo) se encuentran en Azure, mientras que el control de calidad (QA) y los sistemas productivos (PRD) son locales. Además, se presupone que hay un directorio central de transporte.

##### <a name="configuring-the-transport-domain"></a>Configuración del dominio de transporte
Configure el dominio de transporte en el sistema que designó como controlador de dominio de transporte según lo descrito en [Configuring the Transport Domain Controller](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)(Configuración del controlador del dominio de transporte). Se creará un TMSADM de usuarios del sistema y se generará el destino requerido de la RFC. Puede comprobar estas conexiones RFC mediante el SM59 de transacciones. La resolución de nombre de host debe estar habilitada en todo el dominio de transporte.

Control de

* En nuestro escenario, hemos decidido que el sistema de control de calidad local será el controlador de dominio de CTS. Llame al STMS de transacciones. Aparecerá el cuadro de diálogo de TMS. Se mostrará un cuadro de diálogo Configure Transport Domain (Configurar dominio de transporte). (Este cuadro de diálogo aparecerá solo si todavía no ha configurado un dominio de transporte).
* Asegúrese de que el TMSADM de usuario creado automáticamente está autorizado (SM59 -> ABAP Connection [Conexión ABAP] -> TMSADM@E61.DOMAIN_E61 -> Details [Detalles] -> Utilities(M) [Utilidades (M)] -> Authorization Test [Prueba de autorización]). La pantalla inicial del STMS de transacciones debería mostrar que este sistema SAP ahora funciona como el controlador del dominio de transporte, tal y como se muestra a continuación:

![Pantalla inicial del STMS de transacciones en el controlador de dominio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inclusión de los sistemas SAP en el dominio de transporte
La secuencia de inclusión de un sistema SAP en un dominio de transporte tiene el aspecto siguiente:

* En el sistema de desarrollo de Azure, vaya al sistema de transporte (cliente 000) y llame al STMS de transacciones. Elija Other Configuration (Otra configuración) en el cuadro de diálogo y continúe con Include System in Domain (Incluir sistema en el dominio). Especifique el controlador de dominio como host de destino [[Including SAP Systems in the Transport Domain](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)(Inclusión de los sistemas SAP en el dominio de transporte)]. El sistema se pondrá en espera de que se le incluya en el dominio de transporte.
* Por motivos de seguridad, deberá volver al controlador de dominio para confirmar la solicitud. Elija System Overview (Información general del sistema) y Approve of the waiting system (Aprobar el sistema en espera). A continuación, confirme el aviso y la configuración se distribuirá.

Este sistema SAP ahora contiene la información necesaria sobre todos los demás sistemas SAP en el dominio de transporte. Al mismo tiempo, los datos de la dirección del nuevo sistema SAP se envían a todos los demás sistemas SAP y el sistema SAP se introduce en el perfil de transporte del programa de control de transporte. Compruebe si las RFC y el acceso al directorio de transporte del dominio funcionan.

Continúe con la configuración del sistema de transporte como de costumbre según lo descrito en la documentación [Change and Transport System](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)(Sistema de cambios y transportes).

Control de

* Asegúrese de que su STMS local está configurado correctamente.
* Asegúrese de que la máquina virtual en Azure puede resolver el nombre de host del controlador de dominio de transporte y viceversa.
* Call transaction STMS (Llamar a STMS de transacciones) -> Other Configuration (Otra configuración) -> Include System in Domain (Incluir sistema en el dominio).
* Confirme la conexión en el sistema de TMS local.
* Configure las rutas, grupos y capas de transporte como de costumbre.

En escenarios conectados entre locales conectados de sitio a sitio, la latencia entre el entorno local y el de Azure todavía puede ser considerable. Si sigue la secuencia del transporte de objetos a través de los sistemas de desarrollo y pruebas hasta la producción o se plantea la aplicación de paquetes de transporte o de soporte a los distintos sistemas, se dará cuenta de que, en función de la ubicación del directorio central de transporte, algunos de los sistemas experimentarán una alta latencia al leer o escribir datos en dicho directorio. La situación es similar a las configuraciones en el entorno de SAP, donde los distintos sistemas se propagan a través de diversos centros de datos con una distancia considerable entre los centros de datos.

Para solucionar tal latencia y que los sistemas funcionen con rapidez para las tareas de lectura o escritura o desde el directorio de transporte, puede configurar dos dominios de transporte STMS (uno para los sistemas locales y otro con los sistemas en Azure) y vincular los dominios de transporte. Revise esta documentación que explica los principios que se esconden tras este concepto en el TMS de SAP: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Control de

* Configurar un dominio de transporte en cada ubicación (local y Azure) mediante STMS de transacciones <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Vincular los dominios con un vínculo de dominio y confirmar el vínculo entre ambos.
  <http://help.SAP.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/Content.htm>
* Distribución de la configuración al sistema vinculado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfico de RFC entre las instancias de SAP ubicadas en Azure y las locales (entre locales)
El tráfico de RFC entre los sistemas en Azure y los locales debe funcionar. Para configurar un SM59 de transacciones de llamadas de conexión en un sistema de origen donde, se debe definir una conexión RFC hacia el sistema de destino. La configuración es similar a la configuración estándar de una conexión RFC estándar.

Se presupone que en el escenario entre locales, las máquinas virtuales que ejecutan sistemas SAP que deben comunicarse entre sí se encuentran en el mismo dominio. Por lo tanto, la configuración de una conexión RFC entre sistemas SAP no difiere de los pasos y las entradas de configuración de los escenarios locales.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Acceso a recursos compartidos de archivos "locales" desde instancias de SAP ubicadas en Azure o viceversa
Las instancias de SAP ubicadas en Azure deben acceder a los recursos compartidos de archivos que están dentro de los locales corporativos. Además, las instancias de SAP locales deben acceder a los recursos compartidos de archivos que se encuentran en Azure. Para habilitar los recursos compartidos de archivos, se deben configurar los permisos y las opciones de uso compartido en el sistema local. Asegúrese de abrir los puertos en la conexión VPN o ExpressRoute entre Azure y su centro de datos.

## <a name="supportability"></a>Compatibilidad
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Solución de supervisión de Azure para SAP
Para habilitar la supervisión de sistemas SAP críticos en Azure, las herramientas de supervisión de SAP SAPOSCOL o SAP Host Agent obtienen datos del host del servicio de máquina virtual de Azure por medio de una extensión de supervisión de Azure para SAP. Puesto que las demandas de SAP eran muy específicas para las aplicaciones de SAP, Microsoft decidió implementar de forma no genérica la funcionalidad requerida en Azure, pero dejar al criterio de los clientes la implementación de las configuraciones y componentes de supervisión necesarios para sus máquinas virtuales que se ejecuten en Azure. Sin embargo, Azure automatizará la mayor parte de la implementación y la administración del ciclo de vida de los componentes.

#### <a name="solution-design"></a>Diseño de la solución
La solución desarrollada para habilitar la supervisión de SAP se basa en la arquitectura del marco de agente y extensión de VM de Azure. La idea del marco de agente y extensión de VM Azure es permitir la instalación de las aplicaciones de software disponibles en la galería de extensiones de VM de Azure dentro de una máquina virtual. La idea principal que subyace tras este concepto es permitir (en casos como el de la extensión de supervisión de Azure para SAP) la implementación de una funcionalidad especial en una máquina virtual y la configuración de dicho software al realizar la implementación.

Desde febrero de 2014, el "agente de VM de Azure" que permite controlar extensiones específicas de VM de Azure dentro de la máquina virtual se inyecta en las máquinas virtuales de Windows de forma predeterminada al crear VM en el portal de Azure. En el caso de SUSE o Red Hat Linux, el agente de VM ya forma parte de la imagen de Azure Marketplace. En caso de que se desee cargar una VM de Linux desde un sistema local a Azure, el agente de VM se debe instalar manualmente.

Los bloques de creación básicos de la solución de supervisión de Azure para SAP tienen el aspecto siguiente:

![Componentes de la extensión de Microsoft Azure][planning-guide-figure-2400]

Como se muestra en el diagrama de bloques anterior, una parte de la solución de supervisión para SAP se hospeda en la galería de imágenes de VM de Azure y de extensiones de Azure (un repositorio replicado globalmente que está administrado por las operaciones de Azure). El equipo conjunto de SAP/Microsoft que trabaja en la implementación de SAP en Azure debe trabajar con las operaciones de Azure para publicar nuevas versiones de la extensión de supervisión de Azure para SAP. Esta extensión de supervisión de Azure para SAP utilizará la extensión Diagnósticos de Microsoft Azure (WAD) o Diagnósticos de Azure para Linux (LAD) para obtener la información necesaria.

Cuando se implementa una máquina virtual de Windows nueva, el "agente de VM de Azure" se agrega automáticamente a la máquina virtual. La función de este agente es coordinar la carga y la configuración de las extensiones de Azure para la supervisión de los sistemas SAP NetWeaver. En las máquinas virtuales de Linux, el agente de VM de Azure ya forma parte de la imagen de SO de Azure Marketplace.

Sin embargo, el cliente aún deberá realizar otro paso, que consiste en habilitar y configurar la recopilación de rendimiento. El proceso relacionado con la "configuración" se automatiza mediante un script de PowerShell o un comando de la CLI. Se puede descargar el script de PowerShell en el Centro de scripts de Microsoft Azure como se describe en la [Guía de implementación][deployment-guide].

La arquitectura general de la solución de supervisión de Azure para SAP tiene el siguiente aspecto:

![Solución de supervisión de Azure para SAP NetWeaver][planning-guide-figure-2500]

**Para obtener las instrucciones exactas y los pasos detallados para utilizar estos cmdlets de PowerShell o el comando de la CLI durante las implementaciones, siga las instrucciones indicadas la [Guía de implementación][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integración de una instancia de SAP ubicada en Azure en SAProuter
Las instancias de SAP que se ejecutan en Azure también deben ser accesibles desde SAProuter.

![Conexión de red SAP-enrutador][planning-guide-figure-2600]

SAProuter permite la comunicación de TCP/IP entre los sistemas participantes si no hay ninguna conexión IP directa. Esto aporta la ventaja de que no se requiere ninguna conexión de un extremo a otro entre los asociados de comunicación en el ámbito de la red. De forma predeterminada, SAProuter escucha en el puerto 3299.
Para conectarse a instancias de SAP por medio de SAProuter, se debe proporcionar la cadena y el nombre de host de SAProuter cada vez que trate de conectarse.

## <a name="sap-netweaver-as-java"></a>AS de SAP NetWeaver en Java
Hasta ahora, este documento se ha centrado en SAP NetWeaver en general o en la pila de ABAP de SAP NetWeaver. En esta pequeña sección, se ofrecen las consideraciones específicas para la pila de Java de SAP. Una de las aplicaciones de SAP NetWeaver basadas exclusivamente en Java más importantes es SAP Enterprise Portal. Otras aplicaciones basadas en SAP NetWeaver como SAP PI y SAP Solution Manager usan las pilas de ABAP y de Java de SAP NetWeaver. Por lo tanto, indudablemente también se deben tener en consideración los aspectos específicos relacionados con la pila de Java de SAP NetWeaver.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal
La configuración de un portal de SAP en una máquina virtual de Azure no difiere de la instalación local si se desea implementar escenarios entre locales. Dado que el DNS se ejecuta en el entorno local, la configuración local se puede extrapolar a la de los puertos de las instancias individuales. Las recomendaciones y restricciones descritas hasta ahora en este documento se aplican en una aplicación como SAP Enterprise Portal o la pila de Java de SAP NetWeaver en general.

![Exposición del portal de SAP][planning-guide-figure-2700]

Un escenario de implementación especial de algunos clientes es la exposición directa de SAP Enterprise Portal a Internet mientras el host de máquina virtual está conectado a la red corporativa por medio del túnel VPN de sitio a sitio o ExpressRoute. En semejante escenario, hay que asegurarse de que determinados puertos estén abiertos y no estén bloqueados por el firewall ni por el grupo de seguridad de red. Se deberá aplicar la misma mecánica si desea conectarse a una instancia de Java de SAP desde un sistema local en un escenario exclusivo en la nube.

El URI inicial del portal es http(s):`<Portalserver`>:5XX00/irj, donde el puerto está formado por un valor superior a 50000 (número de sistema x 100). El URI predeterminado del portal del sistema SAP 00 es `<dns name`>.`<azure region`>.Cloudapp.azure.com:PublicPort/irj. Para más información, eche un vistazo a <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configuración del punto de conexión][planning-guide-figure-2800]

Si desea personalizar la URL o los puertos de SAP Enterprise Portal, consulte esta documentación:

* [Change Portal URL (Cambio de la dirección URL del portal)](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Change Default port numbers, Portal port numbers (Cambio de los números de puerto predeterminados y los números de puerto del portal)](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Alta disponibilidad (HA) y recuperación ante desastres (DR) para la ejecución de SAP NetWeaver en máquinas virtuales de Azure
### <a name="definition-of-terminologies"></a>Definición de terminología
El término **alta disponibilidad (HA)** se asocia generalmente a un conjunto de tecnologías que minimiza las interrupciones de TI al proporcionar una continuidad empresarial de los servicios de TI mediante componentes redundantes, tolerantes a errores o protegidos mediante conmutación por error dentro del **mismo** centro de datos. En nuestro caso, dentro de una sola región de Azure.

La **recuperación ante desastres (DR)** también tiene el objetivo de minimizar las interrupciones en los servicios de TI y recuperarlos pero en **distintos** centros de datos que suelen encontrarse a cientos de kilómetros de distancia. En nuestro caso, suelen estar repartidos entre distintas regiones de Azure dentro de la misma región geopolítica o según lo haya establecido usted como cliente.

### <a name="overview-of-high-availability"></a>Información general sobre la alta disponibilidad
Podemos descomponer la explicación sobre la alta disponibilidad de SAP en Azure en dos partes:

* **Alta disponibilidad de la infraestructura de Azure**, por ejemplo, la alta disponibilidad del proceso (VM), la red, el almacenamiento, etc., y sus ventajas para aumentar la disponibilidad de las aplicaciones de SAP.
* **Alta disponibilidad de las aplicaciones de SAP**, por ejemplo, la alta disponibilidad de los componentes de software de SAP:
  * servidores de aplicaciones de SAP,
  * instancia de SAP ASCS/SCS,
  * servidor de base de datos,

y cómo se puede combinar con la alta disponibilidad de la infraestructura de Azure.

La alta disponibilidad de SAP en Azure presenta algunas diferencias con respecto a la alta disponibilidad de SAP en un entorno físico o virtual local. Las siguientes notas del producto de SAP describen las configuraciones estándares de la alta disponibilidad de SAP en entornos virtualizados de Windows: <http://scn.sap.com/docs/DOC-44415>. No hay ninguna configuración de alta disponibilidad de SAP integrada en SAPInst para Linux como la que existe para Windows. Con respecto a la alta disponibilidad de SAP local para Linux, se puede encontrar más información aquí: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Alta disponibilidad de la infraestructura de Azure
Actualmente, no hay ningún acuerdo de nivel de servicio de VM única disponible para las máquinas virtuales de Azure. Para hacerse una idea de cómo podría ser la disponibilidad de una sola máquina virtual, basta con compilar el producto de los distintos SLA de Azure disponibles: <https://azure.microsoft.com/support/legal/sla/>.

La base para el cálculo es de30 días por mes o 43 200 minutos. Por lo tanto, un tiempo de inactividad del 0,05 % corresponde a 21,6 minutos. Como de costumbre, la disponibilidad de los distintos servicios se multiplicará de la siguiente manera:

(Servicio de disponibilidad n.º 1/100) * (servicio de disponibilidad n.º 2/100) * (servicio de disponibilidad n.º 3/100) *…

Como, por ejemplo:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 o una disponibilidad general del 99,75 %.

#### <a name="virtual-machine-vm-high-availability"></a>Alta disponibilidad de la máquina virtual (VM)
Existen dos tipos de eventos de plataforma Azure que pueden afectar a la disponibilidad de sus máquinas virtuales: mantenimiento planeado y mantenimiento no planeado.

* Eventos de mantenimiento planeado son actualizaciones periódicas realizadas por Microsoft en la plataforma Azure subyacente para mejorar en general la fiabilidad, el rendimiento y la seguridad de la infraestructura de la plataforma sobre las que se funcionan sus máquinas virtuales.
* Eventos de mantenimiento no planeado se producen cuando el hardware o la infraestructura física subyacente a su máquina virtual presentan algún tipo de error. Entre estos podemos encontrar errores de la red local, errores de los discos locales u otras errores a nivel de bastidor. Cuando se encuentra un error de este tipo, la plataforma Azure migrará automáticamente la máquina virtual del servidor físico en mal estado que hospeda la máquina virtual a un servidor físico con un estado correcto. Estos eventos no son habituales, pero pueden hacer que su máquina virtual se reinicie.

Se pueden encontrar más detalles en esta documentación: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>.

#### <a name="azure-storage-redundancy"></a>Redundancia de almacenamiento de Azure
Los datos de la cuenta de almacenamiento de Microsoft Azure se replican siempre para garantizar su durabilidad y alta disponibilidad, en cumplimiento con el Acuerdo de Nivel de Servicio de almacenamiento de Azure, incluso en caso de errores transitorios del hardware.

Dado que el almacenamiento de Azure mantiene de forma predeterminada tres imágenes de los datos, no se precisan RAID5 o RAID1 en varios discos de Azure.

Se pueden encontrar más detalles en este artículo: <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilización del reinicio de VM de la infraestructura de Azure para lograr una "mayor disponibilidad" de las aplicaciones de SAP
Si decide no utilizar funcionalidades como los clústeres de conmutación por error de Windows Server (WSFC) o una funcionalidad equivalente para Linux (todavía no se admite esta última en Azure en combinación con el software de SAP), el reinicio de VM de Azure se utiliza para proteger un sistema SAP contra el tiempo de inactividad, ya esté planeada o no, de la infraestructura de servidores físicos de Azure y la plataforma global de Azure subyacente.

> [!NOTE]
> Es importante mencionar que el reinicio de VM de Azure protege principalmente las máquinas virtuales, pero NO las aplicaciones. El reinicio de VM no ofrece una alta disponibilidad en las aplicaciones de SAP, pero sí un cierto grado de disponibilidad de la infraestructura y, por tanto, ofrece indirectamente una "mayor disponibilidad" de los sistemas SAP. Tampoco hay ningún acuerdo de nivel de servicio para el tiempo que se tarda en reiniciar una máquina virtual después de una interrupción del host planeada o no planeada. Por lo tanto, este método de "alta disponibilidad" no es adecuado para los componentes esenciales de un sistema SAP como (A)SCS o DBMS.
>
>

El almacenamiento es otro elemento infraestructural importante para la alta disponibilidad. Por ejemplo, el Acuerdo de Nivel de Servicio de almacenamiento de Azure presenta una disponibilidad del 99,9 %. Si se implementan todas las máquinas virtuales con sus discos en una sola cuenta de almacenamiento de Azure, la eventual indisponibilidad del almacenamiento de Azure ocasionará que no estén disponibles ninguna de las máquinas virtuales ubicadas en dicha cuenta de almacenamiento ni tampoco los componentes de SAP que se ejecutan dentro de esas máquinas virtuales.  

En lugar de colocar todas las máquinas virtuales en una sola cuenta de almacenamiento de Azure, también puede usar cuentas de almacenamiento dedicadas para cada máquina virtual y, de esta manera, aumentar la disponibilidad general de las aplicaciones de SAP al usar varias cuentas de almacenamiento de Azure independientes.

La arquitectura de un sistema SAP NetWeaver que utilice la alta disponibilidad de la infraestructura de Azure podría ser, por ejemplo, como la siguiente:

![Utilización de la alta disponibilidad de la infraestructura de Azure para lograr una "mayor" disponibilidad de las aplicaciones de SAP][planning-guide-figure-2900]

Hasta la fecha, en los componentes esenciales de SAP se ha conseguido lo siguiente:

* Alta disponibilidad de los servidores de aplicaciones (AS) de SAP

Las instancias de los servidores de aplicaciones de SAP son componentes redundantes. Cada instancia del servidor de aplicaciones de SAP se implementa en su propia máquina virtual, que se ejecuta en otro dominio de error y de actualización de Azure (consulte los capítulos [Dominios de error][planning-guide-3.2.1] y [Dominios de actualización][planning-guide-3.2.2]). Esto se garantiza empleando conjuntos de disponibilidad de Azure (consulte el capítulo [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3]). La eventual indisponibilidad planeada o no de un dominio de error o de actualización de Azure hará que un número limitado de máquinas virtuales y sus respectivas instancias de SAP no estén disponibles.
Cada instancia del servidor de aplicaciones de SAP se coloca en su propia cuenta de almacenamiento de Azure, por lo que la eventual indisponibilidad de una cuenta de almacenamiento de Azure hará que solo deje de estar disponible una máquina virtual y su respectiva instancia de SAP. Sin embargo, tenga en cuenta que las suscripciones de Azure admiten un número de cuentas de almacenamiento de Azure limitado. Para garantizar el inicio automático de la instancia de (A)SCS después del reinicio de la máquina virtual, asegúrese de establecer el parámetro Autostart en el perfil de inicio de la instancia de (A)SCS como se indica en el capítulo [Uso de Autostart en las instancias de SAP][planning-guide-11.5].
Lea también el capítulo [Alta disponibilidad en los servidores de aplicaciones de SAP][planning-guide-11.4.1] para más información.

* *Mayor* disponibilidad de la instancia de SAP (A)SCS

Aquí se utiliza el reinicio de VM de Azure para proteger la máquina virtual con la instancia de SAP (A)SCS instalada. En el caso de un tiempo de inactividad planeado o no de los servidores de Azure, las máquinas virtuales se reiniciarán en otro servidor disponible. Como se mencionó anteriormente, el reinicio de VM de Azure protege principalmente las máquinas virtuales, pero NO las aplicaciones (en este caso, la instancia de [A]SCS). Mediante el reinicio de VM, se alcanza indirectamente una "mayor disponibilidad" de la instancia de SAP (A)SCS. Para garantizar el inicio automático de la instancia de (A)SCS después del reinicio de la máquina virtual, asegúrese de establecer el parámetro Autostart en el perfil de inicio de la instancia de (A)SCS como se indica en el capítulo [Uso de Autostart en las instancias de SAP][planning-guide-11.5]. Esto significa que la instancia de (A)SCS como único punto de error (SPOF) que se ejecuta en una sola máquina virtual será el factor determinante para la disponibilidad de todo el entorno de SAP.

* *Mayor* disponibilidad del servidor de DBMS

Aquí, de forma similar al caso de uso de la instancia de SAP (A)SCS, se utiliza el reinicio de VM de Azure para proteger la máquina virtual con el software de DBMS instalado, y se alcanza una "mayor disponibilidad" del software de DBMS al reiniciar la máquina virtual.
El DBMS que se ejecute en una sola máquina virtual también es un SPOF, por lo que es el factor determinante para la disponibilidad de todo el entorno de SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Alta disponibilidad de las aplicaciones de SAP en IaaS de Azure
Para alcanzar una alta disponibilidad total en el sistema SAP, se deben proteger todos los componentes esenciales del sistema SAP como, por ejemplo, los servidores de aplicaciones de SAP redundantes y los componentes únicos (por ejemplo, único punto de error) como la instancia de SAP (A)SCS y el DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidad en los servidores de aplicaciones de SAP
Para las instancias de diálogo o de servidores de aplicaciones de SAP no hay que buscar una solución específica de alta disponibilidad. La alta disponibilidad se consigue simplemente mediante la redundancia y, por tanto, disponiendo de la redundancia suficiente en las distintas máquinas virtuales. Todas se deben colocar en el mismo conjunto de disponibilidad de Azure para evitar la posibilidad de que las máquinas virtuales se actualicen al mismo tiempo durante el tiempo de inactividad planeado de mantenimiento. La funcionalidad básica que se basa en diversos dominios de actualización y de error dentro de una unidad de escalado de Azure ya se presentó en el capítulo [Dominios de actualización][planning-guide-3.2.2]. Los conjuntos de disponibilidad de Azure se presentaron en el capítulo [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3] de este documento.

El número de dominios de error y de actualización que puede utilizar un conjunto de disponibilidad de Azure dentro de una unidad de escalado de Azure no es infinito. Esto significa que, al poner un número de máquinas virtuales dentro de un conjunto de disponibilidad, tarde o temprano más de una máquina virtual acabará en el mismo dominio de error o de actualización.

Si se implementan unas cuantas instancias del servidor de aplicaciones de SAP en sus máquinas virtuales dedicadas y se parte de la base de que hay cinco dominios de actualización, al final se obtendrá la imagen siguiente. El número máximo real de dominios de error y de actualización dentro de un conjunto de disponibilidad podría cambiar en el futuro:

![Alta disponibilidad de los servidores de aplicaciones de SAP en Azure][planning-guide-figure-3000]

Se pueden encontrar más detalles en esta documentación: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>.

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Alta disponibilidad en la instancia de SAP (A)SCS en Windows
El clúster de conmutación por error de Windows Server (WSFC) es una solución que se usa con frecuencia para proteger la instancia de SAP (A)SCS. Además, está integrada en SAPInst como una "instalación de alta disponibilidad". En estos momentos, la infraestructura de Azure no puede proporcionar la funcionalidad necesaria para configurar el clúster de conmutación por error de Windows Server del mismo modo que en el entorno local.

Desde enero de 2016, la plataforma en la nube de Azure que se ejecuta en el sistema operativo Windows no permite usar un volumen compartido de clúster en un disco compartido entre dos máquinas virtuales de Azure.

Sin embargo, hay una solución válida que consiste en usar un software de terceros que proporcione un volumen compartido mediante la replicación sincrónica y transparente del disco y que se pueda integrar en el WSFC. Este método implica que solo el nodo de clúster activo podrá acceder a una de las copias del disco en un momento dado. Desde enero de 2016, se admite esta configuración de alta disponibilidad para proteger la instancia de SAP (A)SCS en el SO invitado Windows en máquinas virtuales de Azure en combinación con el software de terceros SIOS DataKeeper.

La solución SIOS DataKeeper proporciona un clúster de disco compartido para los clústeres de conmutación por error de Windows si se reúnen las condiciones siguientes:

* Un VHD de Azure adicional conectado a cada una de las máquinas virtuales (VM) que se encuentran en una configuración de clúster de Windows.
* SIOS DataKeeper Cluster Edition debe ejecutarse en ambos nodos de VM.
* SIOS DataKeeper Cluster Edition debe estar configurado de forma que refleje de forma sincrónica el contenido del volumen del VHD adicional conectado procedente de las máquinas virtuales de origen en el volumen del VHD adicional conectado a la máquina virtual de destino.
* SIOS DataKeeper debe abstraer los volúmenes locales de origen y de destino presentarlos en el clúster de conmutación por error de Windows como un único disco compartido.

Se pueden encontrar todos los detalles sobre cómo instalar un clúster de conmutación por error de Windows con SIOS Datakeeper y SAP en las notas del producto [Clustering SAP ASCS Instance using Windows Server Failover Cluster on Azure with SIOS DataKeeper][ha-guide-classic] (Agrupación en clústeres de una instancias de SAP ASCS usando el clúster de conmutación por error de Windows Server en Azure con SIOS DataKeeper).

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Alta disponibilidad en la instancia de SAP (A)SCS en Linux
Desde diciembre de 2015, tampoco hay ningún equivalente para WSFC de disco compartido para las máquinas virtuales de Linux en Azure. Las soluciones alternativas mediante software de terceros como SIOS para Windows aún no están validadas para que se en ejecuten en SAP en Linux con Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Alta disponibilidad en la instancia de base de datos de SAP
La configuración típica de alta disponibilidad del DBMS de SAP se basa en dos máquinas virtuales de DBMS donde la funcionalidad de alta disponibilidad del DBMS se utiliza para replicar datos de la instancia activa del DBMS a la segunda máquina virtual en una instancia pasiva del DBMS.

La funcionalidad de recuperación ante desastres y de alta disponibilidad del DBMS en general, así como de los DBMS específicos, se describe en la [Guía de implementación de DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidad de un extremo a otro en todo el sistema SAP
A continuación, se ofrecen dos ejemplos de una arquitectura completa de alta disponibilidad de SAP NetWeaver en Azure: una para Windows y otra para Linux.
Es posible que se deba cuestionar la siguiente explicación de los conceptos cuando se implementen muchos sistemas SAP y que el número de máquinas virtuales implementadas supere el límite máximo de cuentas de almacenamiento por suscripción. En tales casos, se deben combinar los VHD de las máquinas virtuales dentro de una cuenta de almacenamiento. Normalmente, este procedimiento se llevaría a cabo combinando los VHD de las máquinas virtuales de la capa de aplicación de SAP en diversos sistemas SAP.  También se combinan varios VHD de diversas máquinas virtuales del DBMS de sistemas SAP distintos en una sola cuenta de almacenamiento de Azure. Por lo tanto, teniendo en cuenta los límites IOPS de cuentas de Azure Storage (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>).

##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Alta disponibilidad en Windows
![Arquitectura de alta disponibilidad de las aplicaciones de SAP NetWeaver con SQL Server en IaaS de Azure][planning-guide-figure-3200]

Las siguientes construcciones de Azure se utilizan en el sistema SAP NetWeaver para minimizar el impacto causado por problemas de infraestructura y la aplicación de revisiones en el host:

* El sistema completo se implementa en Azure (obligatorio: la capa del DBMS, la instancia de [A]SCS y la capa de aplicación completa deben ejecutarse en la misma ubicación).
* El sistema se ejecuta completamente dentro de una suscripción de Azure (obligatorio).
* El sistema se ejecuta completamente dentro de una red virtual de Azure (obligatorio).
* Las máquinas virtuales de un sistema SAP se pueden dividir en tres conjuntos de disponibilidad aunque todas las máquinas virtuales pertenezcan a la misma red virtual.
* Todas las máquinas virtuales que ejecutan instancias del DBMS de un sistema SAP se encuentran en un único conjunto de disponibilidad. Se presupone que hay más de una máquina virtual que ejecuta instancias del DBMS por sistema, ya que se utilizan características nativas de alta disponibilidad del DBMS como SQL Server AlwaysOn u Oracle Data Guard.
* Todas las máquinas virtuales que ejecutan instancias del DBMS utilizan su propia cuenta de almacenamiento. Los archivos de registro y datos del DBMS se replican desde una cuenta de almacenamiento a otra mediante funciones de alta disponibilidad del DBMS que sincronizan los datos. La indisponibilidad de una cuenta de almacenamiento ocasionará que uno de los nodos de clúster de SQL de Windows deje de estar disponible, pero no el servicio de SQL Server al completo.
* Todas las máquinas virtuales que ejecutan la instancia de (A)SCS de un sistema SAP se encuentran en un único conjunto de disponibilidad. Dentro de esas máquinas virtuales se configura el clúster de conmutación por error de Windows Server (WSFC) para proteger la instancia de (A)SCS.
* Todas las máquinas virtuales que ejecutan instancias de (A)SCS utilizan su propia cuenta de almacenamiento. Los archivos de la instancia de (A)SCS y la carpeta global de SAP se replican de una cuenta de almacenamiento a otra mediante la replicación de SIOS DataKeeper. La indisponibilidad de una cuenta de almacenamiento ocasionará que uno de los nodos de clúster de (A)SCS de Windows deje de estar disponible, pero no el servicio de (A)SCS al completo.
* TODAS las máquinas virtuales que representa la capa del servidor de aplicaciones de SAP se encuentran en un tercer conjunto de disponibilidad.
* TODAS las máquinas virtuales que ejecutan servidores de aplicaciones de SAP usan su propia cuenta de almacenamiento. La indisponibilidad de una cuenta de almacenamiento ocasionará que un servidor de aplicaciones de SAP deje de estar disponible, mientras que el resto de los servidores de aplicaciones de SAP continuarán ejecutándose.

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Alta disponibilidad en Linux
La arquitectura de alta disponibilidad de SAP en Linux en Azure es básicamente la misma que la descrita anteriormente para Windows. Sin embargo, desde enero de 2016 hay dos restricciones:

* Actualmente, solo SAP ASE 16 es compatible con Linux en Azure sin las características de replicación de ASE.
* Todavía no hay ninguna solución de alta disponibilidad de SAP (A)SCS compatible con Linux en Azure.

Como consecuencia, desde enero de 2016, un sistema SAP-Linux-Azure no puede alcanzar la misma disponibilidad que un sistema SAP-Windows-Azure debido a falta de alta disponibilidad en la instancia de (A)SCS y en la base de datos de SAP ASE de una sola instancia.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Uso de Autostart en las instancias de SAP
SAP ofrecía una funcionalidad para iniciar las instancias de SAP inmediatamente después de iniciar el sistema operativo en la máquina virtual. Los pasos exactos se documentaron en el artículo de Knowledge Base de SAP [1909114]: Inicio automático de instancias de SAP mediante el parámetro Autostart. Sin embargo, SAP recomienda dejar de utilizar esta configuración, ya que no hay ningún control del orden en el que se reinicia la instancia en el caso de que se vean afectadas más de una máquina virtual o que se ejecuten varias instancias por máquina virtual. Si se presupone un escenario de Azure típico de una instancia del servidor de aplicaciones de SAP en una máquina virtual y la circunstancia de que solo acabe reiniciándose una sola máquina virtual, Autostart no planteará ningún verdadero problema, por lo que podrá habilitarse agregando este parámetro:

    Autostart = 1

Se debe agregar en el perfil de inicio de la instancia de ABAP o Java de SAP.

> [!NOTE]
> El parámetro Autostart también puede presentar algunas deficiencias. Concretamente, este parámetro desencadena el inicio de una instancia de Java o ABAP de SAP cuando se inicia el servicio de Windows/Linux relacionado de la instancia. No cabe duda de que esto es lo que sucede cuando se inician los sistemas operativos. Sin embargo, los reinicios de los servicios de SAP también son frecuentes en la funcionalidad de administración del ciclo de vida de software de SAP como SUM u otras actualizaciones o modificaciones. Estas funcionalidades no esperan en absoluto que una instancia se reinicie automáticamente. Por lo tanto, se debe deshabilitar el parámetro Autostart antes de ejecutar tales tareas. El parámetro Autostart tampoco debe usarse en las instancias de SAP agrupadas en clústeres como ASCS, SCS o CI.
>
>

Se puede consultar más información sobre el inicio automático de las instancias de SAP aquí:

* [Start/Stop SAP along with your Unix Server Start/Stop (Inicio/detención de SAP al iniciar/detener el servidor de Unix)](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starting and Stopping SAP NetWeaver Management Agents (Inicio y detención de los agentes de administración de SAP NetWeaver)](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [How to enable auto Start of HANA Database (Habilitación del inicio automático de la base de datos HANA)](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP de tres niveles más grandes
Los aspectos relativos a la alta disponibilidad de las configuraciones de SAP de tres niveles ya se comentaron en las secciones anteriores. ¿Pero qué sucede con los sistemas en los que los requisitos del servidor del DBMS son demasiado grandes como para que este se encuentre en Azure, pero la capa de aplicación de SAP podría implementarse en Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Ubicación de las configuraciones de SAP de tres niveles
No se puede dividir la propia capa de aplicación ni la aplicación y la capa del DBMS entre un sistema local y Azure. Los sistemas SAP se deben implementar completamente en el entorno local O en Azure. Tampoco se puede hacer que algunos de los servidores de aplicaciones se ejecuten en el entorno local y otros, en Azure. Este es el punto de partida de esta exposición. Tampoco se permite implementar los componentes del DBMS de un sistema SAP y la capa del servidor de aplicaciones de SAP en dos regiones de Azure diferentes. Por ejemplo, implementar el DBMS en el oeste de EE. UU. y la capa de aplicación de SAP en el centro de EE. UU. El motivo para no admitir tales configuraciones es la sensibilidad a la latencia de la arquitectura de SAP NetWeaver.

Sin embargo, a lo largo del año pasado, unos asociados de centro de datos desarrollaron coubicaciones de las regiones de Azure. Dichas coubicaciones suelen estar muy cerca de los centros de datos físicos de Azure de una región de Azure. Esta reducida distancia y la conexión de los activos de la coubicación con Azure por medio de ExpressRoute pueden generar una latencia inferior a 2 ms. En tales casos, se puede localizar la capa del DBMS (incluidos la SAN o el NAS de almacenamiento) en esta coubicación y la capa de aplicación de SAP en Azure. Desde diciembre de 2015, ya no se realizan implementaciones de ese tipo. No obstante, hay diversos clientes con implementaciones de aplicaciones distintas de SAP que siguen empleando tales métodos.

### <a name="offline-backup-of-sap-systems"></a>Copia de seguridad sin conexión de sistemas SAP
En función de la configuración de SAP elegida (de dos o de tres niveles) es posible que se deba realizar una copia de seguridad, tanto del contenido de la propia máquina virtual como de la base de datos. Se espera que las copias de seguridad relacionadas con el DBMS se realicen mediante métodos de base de datos. Se puede encontrar una descripción detallada de las distintas bases de datos en la [Guía de implementación de DBMS][dbms-guide]. Por otro lado, se puede realizar una copia de seguridad sin conexión de los datos de SAP (incluido el contenido de la base de datos) tal y como se describe en esta sección o realizarla en línea como se describe en la sección siguiente.

La copia de seguridad sin conexión requiere básicamente apagar la máquina virtual mediante el portal de Azure, así como realizar una copia del disco de la VM de base y de todos los VHD conectados a la máquina virtual. De este modo, se puede conservar una imagen de la VM y su disco asociado en un momento dado. Se recomienda copiar las "copias de seguridad" en otra cuenta de almacenamiento de Azure. Por lo tanto, se aplicaría el procedimiento descrito en el capítulo [Copia de discos entre cuentas de Azure Storage][planning-guide-5.4.2] de este documento.
Además del apagado mediante Azure Portal, también se puede hacer a través de Powershell o la CLI, como se describe aquí: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>.

Para realizar una restauración a este estado, se debe eliminar la máquina virtual de base, así como los discos originales y los VHD montados de dicha VM; volver a copiar los VHD guardados en la cuenta de almacenamiento original; y luego volver a implementar el sistema.
En este artículo se muestra un ejemplo de cómo crear un script de este proceso en Powershell: <http://www.westerndevs.com/azure-snapshots/>.

Hay que asegurarse de instalar una nueva licencia de SAP, ya que al restaurar una copia de seguridad de la máquina virtual del modo descrito más arriba, se crea una clave de hardware nueva.

### <a name="online-backup-of-an-sap-system"></a>Copia de seguridad en línea de un sistema SAP
Las copias de seguridad de DBMS se realizan mediante métodos específicos para DBMS descritos en la [Guía de DBMS][dbms-guide].

Se puede efectuar una copa de seguridad de otras máquinas virtuales dentro del sistema SAP mediante la funcionalidad de copia de seguridad de máquinas virtuales de Azure. Esta funcionalidad se introdujo a principios de 2015 y, desde entonces, es un método estándar para la realización de una copia de seguridad de una máquina virtual completa en Azure. La copia de seguridad de Azure almacena las copias de seguridad en Azure y permite volver a restaurarlas.

> [!NOTE]
> Desde diciembre de 2015, la copia de seguridad de máquinas virtuales NO conserva el identificador único de VM que se utiliza para la concesión de licencias de SAP. Esto significa que restaurar la copia de seguridad de una máquina virtual requiere instalar una clave de licencia de SAP nueva, ya que se considera que la máquina virtual restaurada es una máquina virtual nueva en lugar de una sustitución de la anterior que estaba guardada.
> Desde enero de 2016, la copia de seguridad de máquinas virtuales de Azure todavía no es compatible con las máquinas virtuales implementadas con Azure Resource Manager.
>
> ![Windows][Logo_Windows] Windows
>
> Teóricamente, también se puede realizar una copia de seguridad de las máquinas virtuales que ejecutan bases de datos de forma coherente si los sistemas de DBMS admiten Windows VSS (Servicio de instantáneas de volumen <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) como, por ejemplo, hace SQL Server.
> Sin embargo, se debe tener en cuenta que no se pueden restaurar bases de datos en un momento dado a partir de las copias de seguridad de máquinas virtuales de Azure. Por lo tanto, se recomienda realizar copias de seguridad de las bases de datos con la funcionalidad del DBMS en lugar de confiar en la copia de seguridad de máquinas virtuales de Azure.
>
> Para familiarizarse con la Copia de seguridad de la máquina virtual de Azure, comience aquí: <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>.
>
> Otras posibilidades son usar una combinación del Microsoft Data Protection Manager instalado en una máquina virtual de Azure y la copia de seguridad de Azure para efectuar copias de seguridad o restaurar bases de datos. Se puede encontrar más información aquí: <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>.  
>
> ![Linux][Logo_Linux] Linux
>
> No hay ningún equivalente de Windows VSS en Linux. Por lo tanto, solo se pueden realizar copias de seguridad coherentes con archivos, pero no copias de seguridad coherentes con la aplicación. La copia de seguridad del DBMS de SAP se debe efectuar mediante la funcionalidad del DBMS. El sistema de archivos que incluye los datos relacionados con SAP se pueden guardar, por ejemplo, mediante tar, como se describe aquí: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>.
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como sitio de recuperación ante desastres para entornos de producción de SAP
Desde mediados de 2014, las extensiones de diversos componentes de Hyper-V, System Center y Azure permiten utilizar Azure como sitio de recuperación ante desastres para las máquinas virtuales basadas en Hyper-V que se ejecutan en el entorno local.

Hay un blog que detalla cómo implementar esta solución que se documenta aquí: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## <a name="summary"></a>Resumen
Los aspectos más destacados de la alta disponibilidad en los sistemas SAP en Azure son los siguientes:

* En estos momentos, el único punto de error de SAP no se puede proteger exactamente del mismo modo que en las implementaciones locales. El motivo es que todavía no se pueden compilar clústeres de disco compartido en Azure sin recurrir a software de terceros.
* Para la capa del DBMS, se debe utilizar una funcionalidad del DBMS que no se basa en la tecnología de clúster de disco compartido. Los detalles están documentados en la [Guía de DBMS][dbms-guide].
* Para minimizar el impacto de los problemas que se produzcan en los dominios de error sobre la infraestructura de Azure o el mantenimiento de hosts, se deben usar los conjuntos de disponibilidad de Azure:
  * Se recomienda tener un conjunto de disponibilidad para la capa de aplicación de SAP.
  * Se recomienda tener un conjunto de disponibilidad independiente para la capa del DBMS de SAP.
  * NO se recomienda aplicar el mismo conjunto de disponibilidad a las máquinas virtuales de sistemas SAP distintos.
* Para realizar copias de seguridad de la capa de DBMS de SAP, se debe consultar la [Guía de DBMS][dbms-guide].
* No tiene mucho sentido efectuar una copia de seguridad de las instancias de diálogo de SAP, ya que volver a implementar instancias de diálogo sencillas suele ser un método más rápido.
* Por el contrario, sí que tiene sentido realizar una copia de seguridad de la máquina virtual que contiene el directorio global del sistema SAP y, con él, todos los perfiles de las distintas instancias, lo cual debe hacerse mediante las copias de seguridad de Windows o p. ej., mediante tar en Linux. Dado que existen diferencias entre Windows Server 2008 (R2) y Windows Server 2012 (R2), pues las versiones más recientes de Windows Server facilitan la realización de copias de seguridad, se recomienda ejecutar Windows Server 2012 (R2) como sistema operativo invitado Windows.

