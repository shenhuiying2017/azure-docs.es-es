---
title: "Implementación de Azure Virtual Machines para SAP en Linux | Microsoft Docs"
description: "Aprenda a implementar software de SAP en máquinas virtuales Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 4a9c9b35b1b1740152214193eb5bb5ef951de781
ms.openlocfilehash: cc75cfa43cd60628d9a4d51827bd14f72715d2b4
ms.lasthandoff: 02/15/2017


---
# <a name="azure-virtual-machines-deployment-for-sap-on-linux"></a>Implementación de Azure Virtual Machines para SAP en Linux
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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (Azure Virtual Machines DBMS deployment for SAP on Linux)
[dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching for VMs and VHDs)
[dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structure of a RDBMS deployment)
[dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (High availability and disaster recovery with Azure VMs)
[dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later)
[dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 and earlier releases)
[dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Using a SQL Server image from the Azure Marketplace)
[dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General SQL Server for SAP on Azure summary)
[dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifics to SQL Server RDBMS)
[dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Storage configuration)
[dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup and restore)
[dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Performance considerations for backup and restore)
[dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other)
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (Azure Virtual Machines deployment for SAP on Linux)
[deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP resources)
[deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Deploying a VM by using a custom image)
[deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Deploying a VM from the Azure Marketplace for SAP)
[deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Deploying a VM with a custom image for SAP)
[deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Moving a VM from on-premises using a non-generalized Azure VHD with SAP)
[deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Deployment scenarios of VMs for SAP on Microsoft Azure)
[deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Deploying Azure PowerShell cmdlets)
[deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download and import SAP-relevant PowerShell cmdlets)
[deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Join a VM to an on-premises domain - Windows only)
[deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Download, install, and enable the Azure VM Agent)
[deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure the Azure Enhanced Monitoring Extension for SAP)
[deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Readiness check for Azure Enhanced Monitoring for SAP)
[deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Health check for the Azure monitoring infrastructure)
[deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Troubleshooting Azure monitoring for SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure the proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and troubleshooting for setting up end-to-end monitoring)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (Azure Virtual Machines planning and implementation for SAP on Linux)
[planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resources)
[planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (High availability and disaster recovery for SAP NetWeaver running on Azure Virtual Machines)
[planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (High availability for SAP Application Servers)
[planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Using Autostart for SAP instances)
[planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud-only - Virtual Machine deployments in Azure without dependencies on the on-premises customer network)
[planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises - Deployment of single or multiple SAP VMs in Azure fully integrated with the on-premises network)
[planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regions)
[planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fault domains)
[planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgrade domains)
[planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure availability sets)
[planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure virtual machines concept)
[planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)
[planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Deploying a VM with a customer specific image)
[planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparation for moving a VM from on-premises to Azure with a non-generalized disk)
[planning-guide-5.2.2]:virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparation for deploying a VM with a customer specific image for SAP)
[planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparing VMs with SAP for Azure)
[planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Difference between an Azure disk and an Azure image)
[planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Uploading a VHD from on-premises to Azure)
[planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copying disks between Azure Storage accounts)
[planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD structure for SAP deployments)
[planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Setting automount for attached disks)
[planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Single VM with SAP NetWeaver demo/training scenario)
[planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concepts of Cloud-Only deployment of SAP instances)
[planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)
[planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and data disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines by using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines es la solución para organizaciones que necesitan recursos de almacenamiento y proceso, en un tiempo mínimo y sin ciclos de adquisición prolongados. Puede usar Azure Virtual Machines para implementar en Azure aplicaciones clásicas, por ejemplo, aplicaciones basadas en SAP NetWeaver. Amplíe la confiabilidad y disponibilidad de una aplicación sin recursos locales adicionales. Azure Virtual Machines admite la conectividad entre locales, así que podrá integrar Azure Virtual Machines a los dominios locales, las nubes privadas y la infraestructura de sistema SAP de la organización.

En este artículo, trataremos los pasos a seguir para implementar aplicaciones de SAP en máquinas virtuales Linux en Azure, incluidas las opciones de implementación alternativas y solución de problemas. Este artículo se basa en la información de [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]. También complementa la documentación sobre la instalación de SAP y las notas de SAP, que son los recursos principales para la instalación e implementación de software de SAP.

## <a name="prerequisites"></a>Requisitos previos
Configurar una máquina virtual de Azure para la implementación de software de SAP conlleva varios pasos y recursos. Antes de empezar, asegúrese de que cumple los requisitos previos para instalar el software SAP en máquinas virtuales Linux en Azure.

### <a name="local-computer"></a>Equipo local
Para administrar máquinas virtuales Windows o Linux, puede usar un script de PowerShell y Azure Portal. Para ambas herramientas, necesita un equipo local que ejecute Windows 7 o una versión posterior de Windows. Si desea administrar solo máquinas virtuales Linux y quiere usar un equipo Linux para esta tarea, puede utilizar la CLI de Azure.

### <a name="internet-connection"></a>Conexión a Internet
Para descargar y ejecutar las herramientas y los scripts necesarios para la implementación de software de SAP, debe estar conectado a Internet. Además, la máquina virtual de Azure que ejecuta la extensión de supervisión mejorada de Azure para SAP debe tener acceso a Internet. Si esta máquina virtual de Azure forma parte de una red virtual de Azure o de un dominio local, asegúrese de que la configuración de proxy pertinente esté establecida según lo descrito en [Configuración de proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Suscripción de Microsoft Azure
Necesita una cuenta de Azure activa.

### <a name="topology-and-networking"></a>Topología y redes
Debe definir la topología y la infraestructura de la implementación de SAP en Azure:

* Cuentas de almacenamiento de Azure que se utilizarán.
* Red virtual en la que desea implementar el sistema SAP.
* Grupo de recursos en el que desea implementar el sistema SAP.
* Región de Azure donde desea implementar el sistema SAP.
* Configuración de SAP (dos o tres niveles).
* Tamaños de máquina virtual y número de discos duros virtuales (VHD) adicionales que se montarán en las máquinas virtuales.
* Configuración de SAP Correction and Transport System (CTS)

Cree y configure las cuentas de almacenamiento de Azure o las redes virtuales de Azure antes de comenzar el proceso de implementación del software de SAP. Para obtener información acerca de cómo crear y configurar estos recursos, consulte [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide].

### <a name="sap-sizing"></a>Ajuste del tamaño de SAP
Obtenga la siguiente información para ajustar el tamaño de SAP:

* Calcule la carga de trabajo de SAP proyectada, por ejemplo, con la herramienta SAP Quick Sizer, y el valor del estándar de rendimiento de las aplicaciones SAP (SAPS).
* Cuánta memoria y cuántos recursos de CPU consume el sistema SAP.
* Número de operaciones de entrada/salida (E/S) por segundo.
* Ancho de banda de red que se requiere ante una eventual comunicación entre distintas máquinas virtuales en Azure.
* Ancho de banda de red que se requiere entre los recursos locales y los sistemas SAP implementados en Azure.

### <a name="resource-groups"></a>Grupos de recursos
En Azure Resource Manager, puede usar los grupos de recursos para administrar todos los recursos de aplicación de la suscripción de Azure. Para más información, consulte [Información general de Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos de SAP
Cuando se está configurando la implementación de software de SAP, se necesitan los siguientes recursos SAP:

* Nota de SAP [1928533], que incluye:
  * La lista de tamaños de máquina virtual de Azure que se admiten para la implementación de software de SAP.
  * Información importante sobre la capacidad para los tamaños de máquina virtual de Azure.
  * Software de SAP admitido y combinaciones de sistema operativo y base de datos.
  * Versión del kernel de SAP necesaria para Windows y Linux en Microsoft Azure.

* La nota de SAP [2015553] enumera los requisitos previos para las implementaciones de software de SAP admitidas por SAP en Azure.
* La nota de SAP [2178632] contiene información detallada sobre todas las métricas de supervisión notificadas para SAP en Azure.
* La nota de SAP [1409604] incluye la versión de SAP Host Agent necesaria para Windows en Azure.
* La nota de SAP [2191498] incluye la versión de SAP Host Agent necesaria para Linux en Azure.
* La nota de SAP [2243692] incluye información acerca de las licencias de SAP en Linux en Azure.
* La nota de SAP [1984787] incluye información general sobre SUSE Linux Enterprise Server 12.
* La nota de SAP [2002167] incluye información general sobre Red Hat Enterprise Linux 7.x.
* La nota de SAP [1999351] contiene más soluciones de problemas de la extensión de supervisión mejorada de Azure para SAP.
* La [WIKI de la comunidad SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene todas las notas de SAP que se necesitan para Linux.
* Los cmdlets de PowerShell específicos de SAP y que forman parte de [Azure PowerShell][azure-ps].
* Los comandos de la CLI de Azure específicos de SAP y que forman parte de la [CLI de Azure][azure-cli].

[comment]: <> (MSSedusch TODO Add ARM patch level for SAP Host Agent in SAP Note 1409604)

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos de Windows
Estos artículos de Microsoft se refieren a las implementaciones de SAP en Azure:

* [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]
* [Implementación de Azure Virtual Machines para SAP en Linux (este artículo)][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
* [Azure Portal][azure-portal]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Escenarios de implementación de software de SAP en máquinas virtuales de Azure
Tiene varias opciones para implementar máquinas virtuales y los discos asociados en Azure. Es importante comprender las diferencias entre las opciones de implementación, porque podría seguir pasos diferentes para preparar las máquinas virtuales para la implementación en función del tipo de implementación que elija.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP
Puede usar una imagen proporcionada por Microsoft o por un tercero en Azure Marketplace para implementar la máquina virtual. Marketplace ofrece algunas imágenes de sistema operativo estándar de Windows Server y diferentes distribuciones de Linux. También puede implementar una imagen que incluya SKU del sistema de administración de base de datos (DBMS), por ejemplo, Microsoft SQL Server. Para más información sobre el uso de imágenes con SKU de DBMS, consulte [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide].

El diagrama de flujo siguiente muestra la secuencia de pasos específica de SAP para implementar una máquina virtual desde Azure Marketplace:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con una imagen de máquina virtual de Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Creación de una máquina virtual con Azure Portal
La manera más fácil de crear una máquina virtual nueva con una imagen de Azure Marketplace es mediante Azure Portal.

1.  Vaya a <https://portal.azure.com/#create>.  O bien, en el menú de Azure Portal, seleccione **+ Nuevo**.
2.  Seleccione **Proceso** y, a continuación, seleccione el tipo de sistema operativo que desea implementar. Por ejemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12) o Red Hat Enterprise Linux 7.2 (RHEL 7.2). La vista de lista predeterminada no muestra todos los sistemas operativos compatibles. Seleccione **ver todo** para obtener una lista completa. Para más información acerca de los sistemas operativos compatibles para la implementación de software de SAP, consulte la nota de SAP [1928533].
3.  En la siguiente página, revise los términos y condiciones.
4.  En la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager**.
5.  Seleccione **Crear**.

El asistente le guiará a través de los parámetros que se requieren para crear la máquina virtual junto con todos los recursos necesarios, como las interfaces de red o las cuentas de almacenamiento. Algunos de estos parámetros son:

1. **Aspectos básicos**:
  * **Nombre**: el nombre del recurso (la máquina virtual).
 * **Nombre de usuario y contraseña** o **Clave pública SSH**: escriba el nombre de usuario y la contraseña que se crearon durante el aprovisionamiento. En una máquina virtual Linux, también puede escribir la clave de Secure Shell (SSH) pública que se usa para iniciar sesión en la máquina.
 * **Suscripción**: seleccione la suscripción que quiere usar para aprovisionar la máquina virtual nueva.
 * **Grupo de recursos**: el nombre del grupo de recursos para la máquina virtual. Puede escribir el nombre de un grupo de recursos nuevo o uno existente.
 * **Ubicación**: dónde se implementará la nueva máquina virtual. Si desea conectar la máquina virtual con la red local, asegúrese de seleccionar la ubicación de la red virtual que conecta Azure con su red local. Para más información, consulte [Redes de Microsoft Azure][planning-guide-microsoft-azure-networking] en [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide].
2. **Tamaño**:

     Lea la nota de SAP [1928533] para ver una lista de los tipos de máquinas virtuales que se admiten. Asegúrese de seleccionar el tipo de máquina virtual correcto si quiere usar Azure Premium Storage. No todos los tipos de VM son compatibles con el Almacenamiento premium. Para más información, consulte [Almacenamiento: Microsoft Azure Storage y discos de datos][planning-guide-storage-microsoft-azure-storage-and-data-disks] y [Azure Premium Storage][planning-guide-azure-premium-storage] en [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide].

3. **Configuración**:
   * **Cuenta de almacenamiento**: seleccione una cuenta de almacenamiento o cree una. No todos los tipos de almacenamiento admiten la ejecución de aplicaciones SAP. Para más información sobre los tipos de almacenamiento, consulte [Microsoft Azure Storage][dbms-guide-2.3] en [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide].
   * **Red virtual** y **Subred**: seleccione la red virtual que está conectada a la red local si desea integrar la máquina virtual en la intranet.
   * **Dirección IP pública**: seleccione la dirección IP pública que quiere usar o escriba los parámetros para crear una dirección IP pública nueva. Puede usar una dirección IP pública para tener acceso a la máquina virtual a través de Internet. Asegúrese de crear también un grupo de seguridad de red para ayudar a proteger el acceso a la máquina virtual.
   * **Grupo de seguridad de red**: para más información, consulte [Control del flujo de tráfico de red con grupos de seguridad de red][virtual-networks-nsg].
   * **Disponibilidad**: seleccione un conjunto de disponibilidad o escriba los parámetros para crear un conjunto de disponibilidad nuevo. Consulte [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3] para más información.
   * **Supervisión**: puede seleccionar **Deshabilitar** para la supervisión de diagnóstico. Se habilita automáticamente cuando se ejecutan los comandos para habilitar la extensión de supervisión mejorada de Azure, tal y como se describe en [Configuración de la supervisión][deployment-guide-configure-monitoring-scenario-1].

4. **Resumen**:

  Revise las opciones seleccionadas y, a continuación, seleccione **Aceptar**.

La máquina virtual se implementa en el grupo de recursos que seleccionó.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Creación de una máquina virtual con una plantilla
También puede crear una máquina virtual con una de las plantillas de SAP publicadas en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github], o bien crear una máquina virtual manualmente con [Azure Portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o la [CLI de Azure][virtual-machines-linux-tutorial].

* [**Plantilla de configuración de dos niveles (solo una máquina virtual)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual.
* [**Plantilla de configuración de tres niveles (varias máquinas virtuales)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Use esta plantilla para crear un sistema de tres niveles con varias máquinas virtuales.

En Azure Portal, especifique los siguientes parámetros para la plantilla:

1. **Aspectos básicos**:
  * **Suscripción**: la suscripción que desea usar para implementar la plantilla.
  * **Grupo de recursos**: el grupo de recursos que desea usar para implementar la plantilla. Puede crear un grupo de recursos o seleccionar uno en la suscripción.
  * **Ubicación**: dónde se implementará la plantilla. Si ha seleccionado un grupo de recursos, se utilizará la ubicación de ese grupo de recursos.

2. **Configuración**:
  * **Identificador de sistema SAP**: el identificador del sistema SAP (SID).
  * **Tipo de SO**: el sistema operativo que desea implementar, por ejemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12) o Red Hat Enterprise Linux 7.2 (RHEL 7.2).

    La vista de lista predeterminada no muestra todos los sistemas operativos compatibles. Seleccione **ver todo** para obtener una lista completa. Para más información acerca de los sistemas operativos compatibles para la implementación de software de SAP, consulte la nota de SAP [1928533].
  * **Tamaño del sistema SAP**: el tamaño del sistema SAP.

    La cantidad de SAPS que el sistema nuevo proporciona. Si no está seguro de cuántos SAPS necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP.
  * **Disponibilidad del sistema**: la disponibilidad del sistema (solo para las plantillas de tres niveles).

    Seleccione **HA** para una configuración en la que se puede realizar una instalación de alta disponibilidad. Se crean dos servidores de base de datos y dos servidores para ABAP SAP Central Services (ASCS).
  * **Tipo de almacenamiento** (solo plantilla de dos niveles): el tipo de almacenamiento que se usará.

    En los sistemas grandes, se recomienda usar Azure Premium Storage. Para más información acerca de los tipos de almacenamiento, consulte estos recursos:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso de almacenamiento de SSD premium de Azure para la instancia de DBMS de SAP)
      * [Microsoft Azure Storage][dbms-guide-2.3] en [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
      * [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure][storage-premium-storage-preview-portal]
      * [Introducción a Microsoft Azure Storage][storage-introduction]
  * **Admin username** (Nombre de usuario de administrador) y **Admin password** (Contraseña de administrador): nombre de usuario y contraseña.
    Se crea un nuevo usuario para iniciar sesión en la máquina virtual.
  * **New or existing subnet** (Subred nueva o existente): determina si es necesario crear una red virtual y una subred nuevas o si se debe usar una que ya exista. Si ya tiene una red virtual conectada a la red local, seleccione **Existing** (Existente).
  * **Subnet ID** (Id. de subred): identificador de la subred a la que se conectarán las máquinas virtuales. Seleccione la subred de la red privada virtual (VPN) o de la red virtual de Azure ExpressRoute que se usará para conectar la máquina virtual a la red local. El identificador suele tener este formato: /subscriptions/&lt;id. de suscripción>/resourceGroups/&lt;nombre del grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nombre de red virtual>/subnets/&lt;nombre de subred>

3. **Términos y condiciones**:  
    Lea y acepte los términos legales.

4.  Seleccione **Comprar**.

El agente de máquina virtual de Azure se implementa de manera predeterminada cuando se usa una imagen de Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configuración de los valores de proxy
Según cómo esté configurada la red local, quizás tenga que configurar el servidor proxy en su máquina virtual. Si la máquina virtual está conectada a la red local a través de VPN o ExpressRoute, puede que la máquina virtual no tenga acceso a Internet y no pueda descargar las extensiones necesarias ni recopilar los datos de supervisión. Para más información, consulte [Configuración de servidores proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Unión a un dominio (solo Windows)
Si la implementación de Azure está conectada a una instancia local de Active Directory o DNS mediante una conexión de VPN de sitio a sitio de Azure o ExpressRoute (esto se denomina *entre locales* en [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]), se espera que la máquina virtual se una a un dominio local. Para más información acerca de las consideraciones para esta tarea, consulte [Unión de una máquina virtual a un dominio local (solo Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configuración de la supervisión
Para asegurarse de que su entorno admite SAP, configure la extensión de supervisión mejorada de Azure para SAP tal y como se describe en [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5]. Compruebe los requisitos previos de la supervisión de SAP y las versiones mínimas del kernel de SAP y de SAP Host Agent requeridas, en los recursos que se indican en [Recursos de SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Comprobación de la supervisión
Compruebe si la supervisión funciona según lo descrito en [Comprobaciones y solución de problemas de configuración de la supervisión de extremo a extremo][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación
Después de crear y de implementar la máquina virtual, debe instalar los componentes de software necesarios en la máquina virtual. Debido a la secuencia de instalación de software e implementación en este tipo de implementación de máquina virtual, el software que se va a instalar ya debe estar disponible, ya sea en Azure, en otra máquina virtual o como un disco que se puede conectar. O bien, considere la posibilidad de utilizar un escenario entre locales en los que ya existe conexión con los recursos locales (recursos compartidos de instalación).

Una vez que se ha implementado la máquina virtual en Azure, siga las mismas directrices e instrucciones para instalar el software de SAP en la máquina virtual de la misma forma que lo haría en un entorno local. Para instalar el software de SAP en una máquina virtual de Azure, SAP y Microsoft recomiendan cargar y guardar los medios de instalación de SAP en los discos duros virtuales de Azure, o bien crear una máquina virtual de Azure que funcione como un servidor de archivos y que contenga todos los medios de instalación de SAP necesarios.

[comment]: <> (MSSedusch TODO why do we need to recommend a file management, for example, File Server or VHD? Is that so different from on-premises?)

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP
Como las diferentes versiones de un sistema operativo o DBMS tienen requisitos de revisión diferentes, las imágenes que encuentre en Azure Marketplace podrían no ajustarse a sus necesidades. En su lugar, quizás quiera crear una máquina virtual con su propia imagen de máquina virtual de sistema operativo o DBMS, que puede implementar de nuevo más tarde.
Los pasos para crear una imagen privada de Linux y una imagen privada de Windows son diferentes.

- - -
> ![Windows][Logo_Windows] Windows
>
> Para preparar una imagen de Windows que pueda usar para implementar varias máquinas virtuales, la configuración de Windows (como el nombre de host y el SID de Windows) debe estar resumida o generalizada en la máquina virtual local. Puede utilizar [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) para ello.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar una imagen de Linux que se pueda usar para implementar varias máquinas virtuales, parte de la configuración de Linux debe estar resumida o generalizada en la máquina virtual local. Puede utilizar `waagent -deprovision` para ello. Para más información, consulte [Capturar una máquina virtual Linux en Azure][virtual-machines-linux-capture-image] y [Guía de usuario del agente de Linux de Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Puede preparar y crear una imagen personalizada y, a continuación, usarla para crear varias máquinas virtuales nuevas. Esto se describe en [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]. Puede configurar el contenido de base de datos con el administrador de aprovisionamiento de software de SAP para instalar un nuevo sistema SAP (restaura una copia de seguridad de base de datos desde un disco duro virtual conectado a la máquina virtual) o puede restaurar directamente una copia de seguridad de base de datos desde Azure Storage (si DBMS lo permite). Para más información, consulte [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]. Si ya instaló un sistema SAP en la máquina virtual local (especialmente para los sistemas de dos niveles), puede adaptar la configuración del sistema SAP después de realizar la implementación de la máquina virtual de Azure mediante el procedimiento de cambio de nombre del sistema admitido por el administrador de aprovisionamiento de software de SAP (nota de SAP [1619720]). De lo contrario, puede instalar el software de SAP después de la implementación de la máquina virtual de Azure.

El diagrama de flujo siguiente muestra la secuencia de pasos específica de SAP para implementar una máquina virtual desde una imagen personalizada:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con una imagen de máquina virtual de Marketplace privado][deployment-guide-figure-300]

#### <a name="create-the-virtual-machine"></a>Creación de la máquina virtual
Para crear una implementación con una imagen de sistema operativo privada mediante Azure Portal, use una de las siguientes plantillas de SAP. Estas plantillas están publicadas en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. También puede crear una máquina virtual manualmente con [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Plantilla de configuración de dos niveles (solo una máquina virtual)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual.
* [**Plantilla de configuración de tres niveles (varias máquinas virtuales)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Use esta plantilla para crear un sistema de tres niveles con varias máquinas virtuales o su propia imagen de sistema operativo.

En Azure Portal, especifique los siguientes parámetros para la plantilla:

1. **Aspectos básicos**:
  * **Suscripción**: la suscripción que desea usar para implementar la plantilla.
  * **Grupo de recursos**: el grupo de recursos que desea usar para implementar la plantilla. Puede crear un grupo de recursos o seleccionar uno en la suscripción.
  * **Ubicación**: dónde se implementará la plantilla. Si ha seleccionado un grupo de recursos, se utilizará la ubicación de ese grupo de recursos.
2. **Configuración**:
  * **Identificador de sistema SAP**: el identificador del sistema SAP.
  * **Tipo de SO**: el sistema operativo que quiere implementar (Windows o Linux).
  * **Tamaño del sistema SAP**: el tamaño del sistema SAP.

    La cantidad de SAPS que el sistema nuevo proporciona. Si no está seguro de cuántos SAPS necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP.
  * **Disponibilidad del sistema**: la disponibilidad del sistema (solo para las plantillas de tres niveles).

    Seleccione **HA** para una configuración en la que se puede realizar una instalación de alta disponibilidad. Se crean dos servidores de base de datos y dos servidores para ASCS.
  * **Tipo de almacenamiento** (solo plantilla de dos niveles): el tipo de almacenamiento que se usará.

    En los sistemas grandes, se recomienda usar Azure Premium Storage. Para más información acerca de los tipos de almacenamiento, consulte estos recursos:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso de almacenamiento de SSD premium de Azure para la instancia de DBMS de SAP)
      * [Microsoft Azure Storage][dbms-guide-2.3] en [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
      * [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure][storage-premium-storage-preview-portal]
      * [Introducción a Microsoft Azure Storage][storage-introduction]
  * **User Image VHD URI** (URI del VHD de la imagen del usuario): URI del disco duro virtual de la imagen de SO privada; por ejemplo, https://&lt;nombreDeCuenta>.blob.core.windows.net/vhds/userimage.vhd.
  * **User image storage account** (Cuenta de almacenamiento de imagen de usuario): nombre de la cuenta de almacenamiento donde se almacena la imagen de sistema operativo privada, por ejemplo, &lt;nombreDeCuenta> en https://&lt;nombreDeCuenta>.blob.core.windows.net/vhds/userimage.vhd.
  * **Admin username** (Nombre de usuario de administrador) y **Admin password** (Contraseña de administrador): nombre de usuario y contraseña.

    Se crea un nuevo usuario para iniciar sesión en la máquina virtual.
  * **New or existing subnet** (Subred nueva o existente): determina si es necesario crear una red virtual y una subred nuevas o si se debe usar una que ya exista. Si ya tiene una red virtual conectada a la red local, seleccione **Existing** (Existente).
  * **Subnet ID** (Id. de subred): identificador de la subred a la que se conectarán las máquinas virtuales. Seleccione la subred de la VPN o la red virtual de ExpressRoute que se usará para conectar la máquina virtual a la red local. Normalmente, el identificador tiene este aspecto:

    /subscriptions/&lt;id. de suscripción>/resourceGroups/&lt;nombre del grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nombre de red virtual>/subnets/&lt;nombre de subred>

3. **Términos y condiciones**:  
    Lea y acepte los términos legales.

4.  Seleccione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalación del agente de máquina virtual (solo Linux)
Para utilizar las plantillas que se describen en la sección anterior, el agente de Linux ya debe estar instalado en la imagen de usuario, o se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la imagen de usuario, tal y como se describe en [Descarga, instalación y habilitación del agente de máquina virtual de Azure][deployment-guide-4.4]. Si no usa las plantillas, también puede instalar más adelante el agente de máquina virtual.

#### <a name="join-a-domain-windows-only"></a>Unión a un dominio (solo Windows)
Si la implementación de Azure está conectada a una instancia local de Active Directory o DNS mediante una conexión de VPN de sitio a sitio de Azure o Azure ExpressRoute (esto se denomina *entre entornos locales* en [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]), se espera que la máquina virtual se una a un dominio local. Para más información acerca de las consideraciones para este paso, consulte [Unión de una máquina virtual a un dominio local (solo Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configuración de los valores de proxy
Según cómo esté configurada la red local, quizás tenga que configurar el servidor proxy en su máquina virtual. Si la máquina virtual está conectada a la red local a través de VPN o ExpressRoute, puede que la máquina virtual no tenga acceso a Internet y no pueda descargar las extensiones necesarias ni recopilar los datos de supervisión. Para más información, consulte [Configuración de servidores proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configuración de la supervisión
Para asegurarse de que su entorno admite SAP, configure la extensión de supervisión mejorada de Azure para SAP tal y como se describe en [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5]. Compruebe los requisitos previos de la supervisión de SAP y las versiones mínimas del kernel de SAP y de SAP Host Agent requeridas, en los recursos que se indican en [Recursos de SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Comprobación de la supervisión
Compruebe si la supervisión funciona según lo descrito en [Comprobaciones y solución de problemas de configuración de la supervisión de extremo a extremo][deployment-guide-troubleshooting-chapter].




### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Escenario 3: Traslado de una máquina virtual local con un VHD no generalizado de Azure con SAP
En este escenario, tiene previsto mover un sistema SAP específico desde un entorno local a Azure. Esto puede hacerse cargando a Azure el disco duro virtual que contiene el sistema operativo, los archivos binarios de SAP y los archivos binarios de DBMS, así como los discos duros virtuales con los archivos de registro y de datos del DBMS. A diferencia del escenario descrito en [Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP][deployment-guide-3.3], en este caso se mantiene el nombre de host, el SID de SAP y las cuentas de usuario de SAP en la máquina virtual de Azure, porque se configuraron en el entorno local. No es necesario generalizar el sistema operativo. Este escenario se da con mayor frecuencia entre entornos locales, donde una parte del panorama SAP se ejecuta en un entorno local y otra parte del mismo se ejecuta en Azure.

En este escenario, el agente de máquina virtual no se instala automáticamente durante la implementación. Como el agente de máquina virtual y la extensión de supervisión mejorada de Azure para SAP son un requisito previo para ejecutar SAP, debe descargar, instalar y habilitar los dos componentes manualmente después de crear la máquina virtual.

Para más información acerca del agente de máquina virtual de Azure, consulte los siguientes recursos.

[comment]: <> (MSSedusch TODO Update Windows Link below)

- - -
> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Guía de usuario del agente de Linux de Azure][virtual-machines-linux-agent-user-guide]
>
>

- - -

El siguiente diagrama de flujo muestra la secuencia de pasos para trasladar una máquina virtual local mediante un disco duro virtual de Azure no generalizado:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP mediante un disco de máquina virtual][deployment-guide-figure-400]

Suponiendo que el disco ya está cargado y definido en Azure (consulte [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]), realice las tareas que se describen en las próximas secciones.

#### <a name="create-a-virtual-machine"></a>de una máquina virtual
Para crear una implementación con un disco de sistema operativo privado mediante Azure Portal, use la plantilla de SAP que está publicada en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. También puede crear una máquina virtual manualmente con PowerShell.

* [**Plantilla de configuración de dos niveles (solo una máquina virtual)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual.

En Azure Portal, especifique los siguientes parámetros para la plantilla:

1. **Aspectos básicos**:
  * **Suscripción**: la suscripción que desea usar para implementar la plantilla.
  * **Grupo de recursos**: el grupo de recursos que desea usar para implementar la plantilla. Puede crear un grupo de recursos o seleccionar uno en la suscripción.
  * **Ubicación**: dónde se implementará la plantilla. Si ha seleccionado un grupo de recursos, se utilizará la ubicación de ese grupo de recursos.
2. **Configuración**:
  * **Identificador de sistema SAP**: el identificador del sistema SAP.
  * **Tipo de SO**: el sistema operativo que quiere implementar (Windows o Linux).
  * **Tamaño del sistema SAP**: el tamaño del sistema SAP.

    La cantidad de SAPS que el sistema nuevo proporciona. Si no está seguro de cuántos SAPS necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP.
  * **Tipo de almacenamiento** (solo plantilla de dos niveles): el tipo de almacenamiento que se usará.

    En los sistemas grandes, se recomienda usar Azure Premium Storage. Para más información acerca de los tipos de almacenamiento, consulte estos recursos:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso de almacenamiento de SSD premium de Azure para la instancia de DBMS de SAP)
      * [Microsoft Azure Storage][dbms-guide-2.3] en [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
      * [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure][storage-premium-storage-preview-portal]
      * [Introducción a Microsoft Azure Storage][storage-introduction]
  * **OS disk VHD URI** (URI del VHD de disco de SO): URI del disco de sistema operativo privado; por ejemplo, https://&lt;nombreDeCuenta>.blob.core.windows.net/vhds/osdisk.vhd.
  * **New or existing subnet** (Subred nueva o existente): determina si es necesario crear una red virtual y una subred nuevas o si se debe usar una que ya exista. Si ya tiene una red virtual conectada a la red local, seleccione **Existing** (Existente).
  * **Subnet ID** (Id. de subred): identificador de la subred a la que se conectarán las máquinas virtuales. Seleccione la subred de la VPN o la red virtual de Azure ExpressRoute que se usará para conectar la máquina virtual a la red local. Normalmente, el identificador tiene este aspecto:

    /subscriptions/&lt;id. de suscripción>/resourceGroups/&lt;nombre del grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nombre de red virtual>/subnets/&lt;nombre de subred>

3. **Términos y condiciones**:  
    Lea y acepte los términos legales.

4.  Seleccione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar el agente de VM
Para usar las plantillas que se describen en la sección anterior, el agente de máquina virtual debe estar instalado en el disco de sistema operativo, o se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la máquina virtual, tal y como se describe en [Descarga, instalación y habilitación del agente de máquina virtual de Azure][deployment-guide-4.4].

Si no usa las plantillas descritas en la sección anterior, también puede instalar más adelante el agente de máquina virtual.

#### <a name="join-a-domain-windows-only"></a>Unión a un dominio (solo Windows)
Si la implementación de Azure está conectada a una instancia local de Active Directory o DNS mediante una conexión de VPN de sitio a sitio de Azure o ExpressRoute (esto se denomina *entre locales* en [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]), se espera que la máquina virtual se una a un dominio local. Para más información acerca de las consideraciones para esta tarea, consulte [Unión de una máquina virtual a un dominio local (solo Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configuración de los valores de proxy
Según cómo esté configurada la red local, quizás tenga que configurar el servidor proxy en su máquina virtual. Si la máquina virtual está conectada a la red local a través de VPN o ExpressRoute, puede que la máquina virtual no tenga acceso a Internet y no pueda descargar las extensiones necesarias ni recopilar los datos de supervisión. Para más información, consulte [Configuración de servidores proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configuración de la supervisión
Para asegurarse de que su entorno admite SAP, configure la extensión de supervisión mejorada de Azure para SAP tal y como se describe en [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5]. Compruebe los requisitos previos de la supervisión de SAP y las versiones mínimas del kernel de SAP y de SAP Host Agent requeridas, en los recursos que se indican en [Recursos de SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Comprobación de la supervisión
Compruebe si la supervisión funciona según lo descrito en [Comprobaciones y solución de problemas de configuración de la supervisión de extremo a extremo][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Actualización de la configuración de supervisión para SAP
Actualice la configuración de supervisión de SAP en los casos siguientes:
* El equipo conjunto de Microsoft y SAP aumenta las funcionalidades de supervisión y solicita más o menos contadores.
* Microsoft presenta una versión nueva de la infraestructura de Azure subyacente que brinda los datos de supervisión y la extensión de supervisión mejorada de Azure para SAP debe adaptarse a esos cambios.
* Puede agregar más VHD a la máquina virtual de Azure, o bien quitar uno. En este caso, actualice la colección de datos relacionados con el almacenamiento. Agregar o eliminar puntos de conexión o asignar direcciones IP a una máquina virtual no afecta a la configuración de la supervisión.
* Cambia el tamaño de la máquina virtual de Azure, por ejemplo, de A5 a cualquier otro tamaño de máquina virtual.
* Agrega interfaces de red nuevas a la máquina virtual de Azure.

Siga los pasos que se explican en el capítulo [Configuración de la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5] para actualizar la infraestructura de supervisión y actualizar la configuración de la supervisión.

## <a name="detailed-tasks-for-sap-software-deployment-on-a-linux-vm"></a>Tareas detalladas para la implementación de software de SAP en una máquina virtual Linux
En esta sección se detallan los pasos para realizar tareas específicas en el proceso de configuración e implementación.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementación de cmdlets de Azure PowerShell
1.  Vaya a [Descargas de Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  En **Herramientas de línea de comandos**, en **PowerShell**, seleccione **Instalación para Windows**.
3.  En el cuadro del administrador de descargas de Microsoft para el archivo descargado (por ejemplo, WindowsAzurePowershellGet.3f.3f.3fnew.exe), seleccione **Ejecutar**.
4.  Para ejecutar el instalador de plataforma web de Microsoft (Microsoft Web PI), seleccione **Sí**.
5.  Se muestra una página como la siguiente:

  ![Página de instalación de cmdlets de Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Seleccione **Instalar** y acepte los términos de licencia del software de Microsoft.
7.  PowerShell se instala. Si selecciona **Finalizar**, se cerrará el asistente para la instalación.

Compruebe periódicamente si existen actualizaciones para los cmdlets de PowerShell, que normalmente se actualizan mensualmente. La manera más fácil de buscar actualizaciones es realizar los pasos de instalación anteriores, hasta la página de instalación que se muestra en el paso 5. El número y la fecha de la versión de los cmdlets se muestran en la página que aparece en el paso 5. Salvo que se indique lo contrario en las notas de SAP [1928533] o [2015553], le recomendamos que trabaje con la versión más reciente de los cmdlets de Azure PowerShell.

Para comprobar la versión de los cmdlets de Azure PowerShell que están instalados en su equipo, ejecute este comando de PowerShell:
```powershell
Import-Module Azure
(Get-Module Azure).Version
```
El resultado se parecerá al siguiente:

![Resultado de la comprobación de versión de los cmdlets de Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Si la versión de los cmdlets de Azure instalada en el equipo es la versión actual, la primera página del asistente para la instalación lo indica agregando **(Instalado)** al título del producto (consulte la siguiente captura de pantalla). Los cmdlets de Azure PowerShell están actualizados. Seleccione **Salir** para cerrar el asistente para la instalación.

![Pantalla de instalación de cmdlets de Azure PowerShell que indica que está instalada la versión más reciente de los cmdlets de Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementación de la CLI de Azure
1.  Vaya a [Descargas de Microsoft Azure](https://azure.microsoft.com/downloads/).
2.  En **Herramientas de línea de comandos**, en **Interfaz de línea de comandos de Azure**, seleccione el vínculo **Instalación** correspondiente a su sistema operativo.
3.  En el cuadro del administrador de descargas de Microsoft para el archivo descargado (por ejemplo, WindowsAzureXPlatCLI.3f.3f.3fnew.exe), seleccione **Ejecutar**.
4.  Para ejecutar el instalador de plataforma web de Microsoft (Microsoft Web PI), seleccione **Sí**.
5.  Se muestra una página como la siguiente:

  ![Página de instalación de cmdlets de Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

6.  Seleccione **Instalar** y acepte los términos de licencia del software de Microsoft.
7.  La CLI de Azure está instalada. Si selecciona **Finalizar**, se cerrará el asistente para la instalación.

Compruebe regularmente si hay actualizaciones de la CLI de Azure, que normalmente se actualiza mensualmente. La manera más fácil de buscar actualizaciones es realizar los pasos de instalación anteriores, hasta la página de instalación que se muestra en el paso 5.

Para comprobar la versión de la CLI de Azure que está instalada en el equipo, ejecute este comando:
```
azure --version
```

El resultado se parecerá al siguiente:

![Resultado de la comprobación de versión de CLI de Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Unión de una máquina virtual a un dominio local (solo Windows)
Si implementa máquinas virtuales de SAP en un escenario entre entornos locales en los que las instancias de Active Directory y DNS locales se extienden a Azure, se espera que las máquinas virtuales estén unidas a un dominio local. Los pasos detallados para unir una máquina virtual a un dominio local y el software adicional que se requiere para ser miembro de un dominio local varía según el cliente. Por lo general, para unir una máquina virtual a un dominio local, debe instalar software adicional, como software antimalware, de copia de seguridad o de supervisión.

En este escenario, también tiene que asegurarse de que si se fuerza la configuración de proxy de Internet cuando una máquina virtual se une a un dominio en su entorno, la cuenta de sistema local de Windows (S-1-5-18) en la máquina virtual invitada tiene la misma configuración de proxy. La manera más sencilla es obligar al servidor proxy mediante una directiva de grupo de dominio que se aplica a los sistemas de del dominio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Descarga, instalación y habilitación del agente de máquina virtual de Azure
Para las máquinas virtuales que se implementan desde una imagen de sistema operativo que no está generalizada (por ejemplo, una imagen que no se origina en la herramienta de preparación del sistema de Windows o sysprep), debe descargar, instalar y habilitar manualmente el agente de máquina virtual de Azure.

Si implementa una máquina virtual desde Azure Marketplace, este paso no es necesario. Las imágenes de Azure Marketplace ya tienen el agente de máquina virtual de Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
1.  Descargue el agente de máquina virtual de Azure:
  1.  Descargue el [paquete del instalador del agente de máquina virtual de Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
  2.  Almacene el paquete MSI del agente de máquina virtual localmente en su equipo o en un servidor.
2.  Instale el agente de máquina virtual de Azure:
  1.  Conéctese a la máquina virtual de Azure mediante el Protocolo de Escritorio remoto (RDP).
  2.  Abra una ventana del Explorador de Windows en la máquina virtual y seleccione el directorio de destino del archivo MSI del agente de máquina virtual.
  3.  Arrastre el archivo MSI del instalador del agente de máquina virtual de Azure desde su equipo o servidor local y suéltelo en el directorio de destino en la máquina virtual.
  4.  Haga doble clic en el archivo MSI en la máquina virtual.
3.  En el caso de las máquinas virtuales unidas a dominios locales, asegúrese de que las posibles configuraciones de proxy de Internet se apliquen también a la cuenta de sistema local de Windows (S-1-5-18) en la máquina virtual, tal y como se describe en [Configuración de proxy][deployment-guide-configure-proxy]. El agente de máquina virtual se ejecuta en este contexto y necesita poder conectarse a Azure.

No se necesita ninguna interacción del usuario para actualizar el agente de máquina virtual de Azure. El agente de máquina virtual se actualiza automáticamente y no requiere que la máquina virtual se reinicie.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Use los comandos siguientes para instalar el agente de máquina virtual para Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL)**

  ```
  sudo yum install WALinuxAgent
  ```

Si el agente ya está instalado, para actualizar el agente de Linux de Azure, siga los pasos descritos en [Actualización del agente de Linux de Azure en una máquina virtual a la última versión desde GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configuración de proxy
Los pasos que se siguen para configurar el proxy en Windows son diferentes en Linux.

#### <a name="windows"></a>Windows
La configuración de proxy debe establecerse correctamente para que la cuenta de sistema local tenga acceso a Internet. Si la configuración de proxy no se establece mediante directiva de grupo, puede configurar las opciones de la cuenta de sistema local.

1. Vaya a **Inicio**, escriba **gpedit.msc** y seleccione **ENTRAR**.
2. Seleccione **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Internet Explorer**. Asegúrese de que la opción **Configuración de proxy por equipo y no por usuario** esté desactivada o no configurada.
3. En el **Panel de Control**, vaya a **Centro de redes y recursos compartidos** > **Opciones de Internet**.
4. En la pestaña **Conexiones**, seleccione el botón **Configuración de LAN**.
5. Desactive la casilla **Detectar la configuración automáticamente**.
6. Active la casilla **Usar un servidor proxy para la LAN** y escriba el puerto y la dirección del servidor proxy.
7. Seleccione el botón **Advanced** (Opciones avanzadas).
8. En el cuadro **Excepciones**, escriba la dirección IP **168.63.129.16**. Seleccione **Aceptar**.

#### <a name="linux"></a>Linux
Configure el proxy correcto en el archivo de configuración del agente invitado de Microsoft Azure, que está en \\etc\\waagent.conf.

Establezca los siguientes parámetros:

1.  **HTTP proxy host** (Host de proxy HTTP). Por ejemplo, establézcalo en **proxy.corp.local**.
  ```
  HttpProxy.Host=<proxy host>

  ```
2.  **HTTP proxy port** (Puerto de proxy HTTP). Por ejemplo, establézcalo en **80**.
  ```
  HttpProxy.Port=<port of the proxy host>

  ```
3.  Reinicie el agente.

  ```
  sudo service waagent restart
  ```

La configuración de proxy en \\etc\\waagent.conf también se aplica a las extensiones de máquina virtual requeridas. Si desea usar los repositorios de Azure, asegúrese de que el tráfico a estos repositorios no pase por la intranet local. Si creó rutas definidas por el usuario para habilitar la tunelización forzada, asegúrese de agregar una ruta que enrute el tráfico hacia los repositorios directamente a Internet y no a través de su conexión de sitio a sitio.

* **SLES**

  También es necesario agregar rutas para las direcciones IP que aparecen en \\etc\\regionserverclnt.cfg. La ilustración siguiente muestra un ejemplo:

  ![Tunelización forzada][deployment-guide-figure-50]


* **RHEL**

  También es necesario que agregue rutas para las direcciones IP de los hosts que aparecen en \\etc\\yum.repos.d\\rhui-load-balancers. La ilustración anterior muestra un ejemplo.

Para más información sobre las rutas definidas por el usuario, consulte [Rutas definidas por el usuario y reenvío IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configuración de la extensión de supervisión mejorada de Azure para SAP
Cuando la máquina virtual esté preparada tal y como se describe en [Escenarios de implementación de máquinas virtuales para SAP en Azure][deployment-guide-3], el agente de máquina virtual de Azure se instala en la máquina virtual. El siguiente paso es implementar la extensión de supervisión mejorada de Azure para SAP, que está disponible en el repositorio de extensiones de Azure, en los centros de datos globales de Azure. Para más información, consulte [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide-9.1].

Puede usar PowerShell o la CLI de Azure para instalar y configurar la extensión de supervisión mejorada de Azure para SAP. Para instalar la extensión en una máquina virtual Linux o Windows mediante una máquina Windows, consulte [Azure PowerShell][deployment-guide-4.5.1]. Para instalar la extensión en una máquina virtual Linux con un equipo de escritorio Linux, consulte [CLI de Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para máquinas virtuales Linux y con Windows
Para instalar la extensión de supervisión mejorada de Azure para SAP mediante PowerShell:

1. Asegúrese de tener instalada la versión más reciente del cmdlet de Azure PowerShell. Para más información, consulte [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1].  
2. Ejecute el siguiente cmdlet de PowerShell.
    Para ver la lista de entornos disponibles, ejecute `commandlet Get-AzureRmEnvironment`. Si quiere usar una instancia pública de Azure, el entorno es **AzureCloud**. Si quiere usar Azure en China, seleccione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Después de proporcionar los datos de la cuenta y de identificar la máquina virtual de Azure, el script implementa las extensiones requeridas y habilita las funciones necesarias. Esto puede tardar varios minutos.
Para más información acerca de `Set-AzureRmVMAEMExtension`, consulte [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![Ejecución correcta del cmdlet de Azure específico de SAP Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

La configuración `Set-AzureRmVMAEMExtension` realiza todos los pasos para configurar la supervisión de hosts para SAP.

La salida del script incluye la información siguiente:

* La confirmación de que configuró la supervisión del disco duro virtual base (donde está el SO), además de todos los discos duros virtuales adicionales montados en la máquina virtual.
* Los próximos dos mensajes confirman la configuración de las métricas de almacenamiento de una cuenta de almacenamiento específica.
* Una línea de la salida muestra el estado de la actualización real de la configuración de supervisión.
* Otra línea de la salida confirma que la configuración se implementó o se actualizó.
* La última línea de la salida es informativa. Muestra las opciones para probar la configuración de supervisión.
* Para comprobar que todos los pasos de la supervisión mejorada de Azure se ejecutaron correctamente y de que la infraestructura de Azure proporciona los datos necesarios, continúe con la comprobación de la preparación de la extensión de supervisión mejorada de Azure para SAP, tal y como se describe en [Comprobación de preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1].
* Espere de 15 a 30 minutos para que Diagnósticos de Azure recopile los datos pertinentes.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI de Azure para máquinas virtuales de Linux
Para instalar la extensión de supervisión mejorada de Azure para SAP mediante la CLI de Azure:

1. Instale la CLI de Azure tal y como se describe en [Instalación de la CLI de Azure][azure-cli].
2. Inicie sesión con su cuenta de Azure:

  ```
  azure login
  ```

3. Cambie al modo de Azure Resource Manager:

  ```
  azure config mode arm
  ```

4. Habilite la supervisión mejorada de Azure:

  ```
  azure vm enable-aem <resource-group-name> <vm-name>
  ```

5. Compruebe que la extensión de supervisión mejorada de Azure está activa en la máquina virtual Linux de Azure. Compruebe si el archivo \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existe. Si existe, en un símbolo del sistema, ejecute este comando para mostrar la información recopilada por la extensión de supervisión mejorada de Azure:
```
cat /var/lib/AzureEnhancedMonitor/PerfCounters
```

El resultado tendrá un aspecto similar al siguiente:
```
2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
…
…
```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Comprobaciones y solución de problemas de supervisión de extremo a extremo
Después de implementar la máquina virtual de Azure y de configurar la infraestructura de supervisión de Azure correspondiente, revise si todos los componentes de la extensión de supervisión mejorada de Azure funcionan correctamente.

Ejecute la comprobación de la preparación de la extensión de supervisión mejorada de Azure para SAP tal y como se describe en [Comprobación de la preparación de la extensión de supervisión mejorada de Azure para SAP][deployment-guide-5.1]. Si todos los resultados de la comprobación de la preparación son positivos y todos los contadores de rendimiento pertinentes son correctos, la supervisión de Azure se configuró correctamente. Puede continuar con la instalación de SAP Host Agent, tal y como se describe en las notas de SAP en [Recursos de SAP][deployment-guide-2.2]. Si la comprobación de la preparación indica que faltan contadores, ejecute la comprobación del estado de mantenimiento de la infraestructura de supervisión de Azure, tal y como se describe en [Comprobación del estado de la configuración de la infraestructura de supervisión de Azure][deployment-guide-5.2]. Para ver más opciones de solución de problemas, consulte [Solución de problemas de supervisión de Azure para SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Comprobación de la preparación de la extensión de supervisión mejorada de Azure para SAP
Con esta comprobación, se asegura de que la infraestructura de supervisión de Azure subyacente proporcione todas las métricas que aparecen dentro de la aplicación SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ejecución de la comprobación de la preparación en una máquina virtual Windows

1.  Inicie sesión en la máquina virtual de Azure (no se necesita una cuenta de administrador).
2.  Abra una ventana de símbolo del sistema.
3.  En el símbolo del sistema, vaya a la carpeta de instalación de la extensión de supervisión mejorada de Azure para SAP: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versión>\\drop.

  La *versión* que aparece en la ruta de acceso a la extensión de supervisión puede variar. Si ve carpetas de varias versiones de la extensión de supervisión en la carpeta de instalación, revise la configuración del servicio de Windows AzureEnhancedMonitoring y cambie a la carpeta indicada como *Ruta de acceso al ejecutable*.

  ![Propiedades del servicio que ejecuta la extensión de supervisión mejorada de Azure para SAP][deployment-guide-figure-1000]

4.  En el símbolo del sistema, ejecute **azperflib.exe** sin ningún parámetro.

  > [!NOTE]
  > Azperflib.exe se ejecuta en un bucle y actualiza los contadores recopilados cada 60 segundos. Para finalizar el bucle, cierre la ventana de símbolo del sistema.
  >
  >

Si la extensión de supervisión mejorada de Azure no está instalada, o el servicio AzureEnhancedMonitoring no se está ejecutando, la extensión no se configuró correctamente. Para obtener información detallada sobre cómo implementar la extensión, consulte [Solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Revise la salida del archivo azperflib.exe.
La salida del archivo azperflib.exe muestra todos los contadores de rendimiento de Azure para SAP rellenados. En la parte inferior de la lista de los contadores recopilados, encontrará un resumen y un indicador que muestra el estado de la supervisión de Azure.

![Salida de la comprobación del estado mediante la ejecución de azperflib.exe, que indica que no hay problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Vea el valor de **Counters total** (Total de contadores) en la salida, que se notifica como vacío, y el valor de **Health status** (Estado de mantenimiento), que se muestra en la ilustración anterior.

Puede interpretar los valores del resultado de la siguiente manera:

| Valores del resultado de azperflib.exe | Estado de mantenimiento de la supervisión de Azure |
| --- | --- |
| **Llamadas a API: no disponible** | Los contadores que no están disponibles puede que no se utilicen con la configuración de las máquinas virtuales o que haya errores. Consulte **Estado de mantenimiento**. |
| **Total de contadores: vacíos** |Los siguientes dos contadores Azure Storage pueden estar vacíos: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Todos los demás contadores deben tener valores. |
| **Estado de mantenimiento** |Solo es correcto si el estado del resultado muestra **Correcto**. |
| **Diagnóstico** |Información detallada sobre el estado de mantenimiento. |

Si el valor de **Estado de mantenimiento** no es **Correcto**, siga las instrucciones de [Comprobación del estado de mantenimiento de la configuración de la infraestructura de supervisión de Azure][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ejecución de la comprobación de la preparación en una máquina virtual Linux

1.  Conéctese a la máquina virtual de Azure mediante SSH.

2.  Compruebe la salida de la extensión de supervisión mejorada de Azure.

  a.  Ejecute `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Resultado esperado**: devuelve una lista de contadores de rendimiento. El archivo no debe estar vacío.

 b. Ejecute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Resultado esperado**: devuelve una línea en la que el error es **none**, por ejemplo, **3;config;Error;;0;0;none;0;1456416792;tst-servercs;**

  c. Ejecute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

    **Resultado esperado**: devuelve un resultado vacío o no existente.

Si la comprobación anterior no fue correcta, ejecute estas comprobaciones adicionales:

1.  Asegúrese de que waagent esté instalado y habilitado.

  a.  Ejecute `sudo ls -al /var/lib/waagent/`

      **Resultado esperado**: muestra el contenido del directorio de waagent.

  b.  Ejecute `ps -ax | grep waagent`

   **Resultado esperado**: muestra una entrada similar a: `python /usr/sbin/waagent -daemon`

2. Asegúrese de que la extensión de diagnóstico de Linux esté instalada y habilitada.

  a.  Ejecute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-'`

   **Resultado esperado**: muestra el contenido del directorio de la extensión de diagnóstico de Linux.

 b. Ejecute `ps -ax | grep diagnostic`

   **Resultado esperado**: muestra una entrada similar a: `python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon`

3.   Asegúrese de que la extensión de supervisión mejorada de Azure esté instalada y en ejecución.

  a.  Ejecute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-/'`

    **Resultado esperado**: muestra el contenido del directorio de la extensión de supervisión mejorada de Azure.

  b. Ejecute `ps -ax | grep AzureEnhanced`

     **Resultado esperado**: muestra una entrada similar a: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

3. Instale SAP Host Agent tal y como se describe en la nota de SAP [1031096], y compruebe la salida de `saposcol`.

  a.  Ejecute `/usr/sap/hostctrl/exe/saposcol -d`

  b.  Ejecute `dump ccm`

  c.  Compruebe si la métrica **Virtualization_Configuration\Enhanced Monitoring Access** es **true**.

Si ya tiene instalado un servidor de aplicaciones de SAP NetWeaver ABAP, abra la transacción ST06 y compruebe si la supervisión mejorada está habilitada.

Si alguno de estos pasos produce un error y para obtener información detallada sobre cómo volver a implementar la extensión, consulte [Solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Comprobación del estado de mantenimiento de la configuración de la infraestructura de supervisión de Azure
Si parte de los datos de supervisión no se entregan correctamente según lo indicado en la prueba que se describe en [Comprobación de la preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1], ejecute el cmdlet `Test-AzureRmVMAEMExtension` para comprobar si la configuración de la infraestructura de supervisión de Azure y de la extensión de supervisión para SAP es correcta.

1.  Asegúrese de tener instalada la versión más reciente del cmdlet de Azure PowerShell, tal y como se describe en [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1].
2.  Ejecute el siguiente cmdlet de PowerShell. Para ver la lista de entornos disponibles, ejecute el cmdlet `Get-AzureRmEnvironment`. Para usar una nube pública de Azure, seleccione el entorno **AzureCloud**. Si quiere usar Azure en China, seleccione **AzureChinaCloud**.
  ```powershell
  $env = Get-AzureRmEnvironment -Name <name of the environment>
  Login-AzureRmAccount -Environment $env
  Set-AzureRmContext -SubscriptionName <subscription name>
  Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
  ```

3.  Escriba los datos de la cuenta e identifique la máquina virtual de Azure.

  ![Página de entrada del cmdlet de Azure específico para SAP Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

4. El script comprueba la configuración de la máquina virtual que seleccione.

  ![Salida de una prueba correctamente ejecutada de la infraestructura de supervisión de Azure para SAP][deployment-guide-figure-1300]

Asegúrese de que todos los resultados de la comprobación del estado de mantenimiento sean **OK**. Si algunas comprobaciones no muestran **OK**, ejecute el cmdlet de actualización tal y como se describe en [Configuración de la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5]. Espere 15 minutos y vuelva a ejecutar las comprobaciones que se describen en [Comprobación de la preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1] y [Comprobación del estado de la configuración de la infraestructura de supervisión de Azure][deployment-guide-5.2]. Si las comprobaciones siguen con problemas en algunos contadores o en todos ellos, consulte [Solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Solución de problemas de la infraestructura de supervisión de Azure para SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] No aparecen contadores de rendimiento de Azure
El servicio AzureEnhancedMonitoring Windows recopila métricas de rendimiento en Azure. Si el servicio no se instaló correctamente o si no se está ejecutando en la máquina virtual, no se recopilará ninguna métrica de rendimiento.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>El directorio de instalación de la extensión de supervisión mejorada de Azure está vacío

###### <a name="issue"></a>Problema
El directorio de instalación C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versión>\\drop está vacío.

###### <a name="solution"></a>Solución
La extensión no está instalada. Determine si se trata de un problema de proxy (como se describió anteriormente). Puede que tenga que reiniciar la máquina o volver a ejecutar el script de configuración `Set-AzureRmVMAEMExtension`.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>El servicio de supervisión mejorada de Azure no existe

###### <a name="issue"></a>Problema
El servicio de Windows AzureEnhancedMonitoring no existe.

La salida de Azperflib.exe produce un error:

![La ejecución de azperflib.exe indica que el servicio de la extensión de supervisión mejorada de Azure para SAP no se está ejecutando][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solución
Si el servicio no existe, significa que la extensión de supervisión mejorada de Azure para SAP no se instaló correctamente. Implemente de nuevo la extensión según los pasos correspondientes a su escenario de implementación, que se describen en [Escenarios de implementación de máquinas virtuales para SAP en Azure][deployment-guide-3].

Después de implementar la extensión, vuelva a comprobar en una hora si los contadores de rendimiento de Azure aparecen en la máquina virtual de Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>El servicio de supervisión mejorada de Azure existe, pero no se inicia

###### <a name="issue"></a>Problema
El servicio de Windows AzureEnhancedMonitoring existe y está habilitado, pero no se inicia. Consulte el registro de eventos de la aplicación para más información.

###### <a name="solution"></a>Solución
La configuración es incorrecta. Reinicie la extensión de supervisión en la máquina virtual, tal como se describe en [Configuración de la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Faltan algunos contadores de rendimiento de Azure
El servicio AzureEnhancedMonitoring Windows recopila métricas de rendimiento en Azure. El servicio obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, y algunas métricas de rendimiento se leen de Diagnósticos de Azure. Los contadores de almacenamiento proceden de los registros en el nivel de suscripción de almacenamiento.

Si la solución de problemas descrita en la nota de SAP [1999351] no resuelve el problema, vuelva a ejecutar el script de configuración `Set-AzureRmVMAEMExtension`. Puede que tenga que esperar una hora porque los contadores de diagnóstico o análisis de almacenamiento no se crean de inmediato una vez habilitados. Si el problema continúa, abra un mensaje de servicio al cliente de SAP acerca del componente BC-OP-NT-AZR para una máquina virtual Windows o BC-OP-LNX-AZR para una máquina virtual Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] No aparecen contadores de rendimiento de Azure
Un demonio recopila las métricas de rendimiento en Azure. Si el demonio no se está ejecutando, no se recopilará ninguna métrica de rendimiento.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>El directorio de instalación de la extensión de supervisión mejorada de Azure está vacío

###### <a name="issue"></a>Problema
El directorio \\var\\lib\\waagent\\ no contiene un subdirectorio para la extensión de supervisión mejorada de Azure.

###### <a name="solution"></a>Solución
La extensión no está instalada. Determine si se trata de un problema de proxy (como se describió anteriormente). Puede que tenga que reiniciar la máquina o volver a ejecutar el script de configuración `Set-AzureRmVMAEMExtension`.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Faltan algunos contadores de rendimiento de Azure
Un demonio recopila las métricas de rendimiento en Azure, y obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, y algunas métricas de rendimiento se leen de Diagnósticos de Azure. Los contadores de almacenamiento proceden de los registros en su suscripción de almacenamiento.

Para ver una lista completa y actualizada de los problemas conocidos, consulte la nota de SAP [1999351], que contiene información adicional sobre la solución de problemas de la supervisión mejorada de Azure para SAP.

Si la solución de problemas descrita en la nota de SAP [1999351] no resuelve el problema, vuelva a ejecutar el script de configuración `Set-AzureRmVMAEMExtension` tal y como se describe en [Configuración de la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5]. Puede que tenga que esperar una hora porque los contadores de diagnóstico o análisis de almacenamiento no se crean de inmediato una vez habilitados. Si el problema continúa, abra un mensaje de servicio al cliente de SAP acerca del componente BC-OP-NT-AZR para una máquina virtual Windows o BC-OP-LNX-AZR para una máquina virtual Linux.

