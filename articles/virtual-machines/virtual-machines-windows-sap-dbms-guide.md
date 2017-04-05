---
title: "SAP NetWeaver en máquinas virtuales de Azure: guía de implementación de DBMS | Microsoft Docs"
description: "SAP NetWeaver en máquinas virtuales de Azure: guía de implementación de DBMS"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: e0e05b5e-47aa-4c1e-bf83-0d62ee8f614e
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 45eaf758d538e33a55c4d4f0f6c01f33ffc20e30
ms.lasthandoff: 03/29/2017


---
# <a name="sap-netweaver-on-azure-windows-virtual-machines-vms--dbms-deployment-guide"></a>SAP NetWeaver en máquinas virtuales Windows de Azure: guía de implementación de DBMS
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

[azure-cli]:../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver en máquinas virtuales (VM) de Azure: guía de implementación de DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Almacenamiento en caché de máquinas virtuales y VHD) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estructura de una implementación de RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidad y recuperación ante desastres con máquinas virtuales de Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 y versiones posteriores) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 y versiones anteriores) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso de imágenes de SQL Server fuera de Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Resumen general de SQL Server para SAP en Azure) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Detalles para SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuración de almacenamiento) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Copias de seguridad y restauración) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Consideraciones de rendimiento para copias de seguridad y restauraciones) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Otros) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver en máquinas virtuales (VM) de Azure: guía de implementación) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implementación de una máquina virtual con una imagen personalizada) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implementación de cmdlets de Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Descarga e implementación de cmdlets de PowerShell para SAP) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Unión de una máquina virtual a un dominio local [solo Windows]) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Descarga, instalación y habilitación del agente de máquina virtual de Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI de Azure) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar la extensión de supervisión mejorada de Azure para SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Comprobación de preparación de la supervisión mejorada de Azure para SAP) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Comprobación de estado de la configuración de la infraestructura de supervisión de Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver en máquinas virtuales (VM) de Azure: guía de planeamiento e implementación) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta disponibilidad (HA) y recuperación ante desastres (DR) para la ejecución de SAP NetWeaver en Azure Virtual Machines) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidad en los servidores de aplicaciones de SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso de Autostart en las instancias de SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre sitios locales: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiones de Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Dominios de error) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Dominios de actualización) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidad de Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (El concepto de máquina virtual de Azure) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implementación de una máquina virtual con una imagen específica del cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparación de máquinas virtuales con SAP para Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferencia entre un disco y una imagen de Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carga de un VHD desde una instalación local a Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia de discos entre cuentas de Azure Storage) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estructura de VM/VHD para implementaciones de SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Configuración de montaje automático para discos conectados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Escenario de demostración/aprendizaje de máquina virtual única con SAP NetWeaver) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceptos de implementación solo en la nube de instancias de SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solución de supervisión de Azure para SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

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
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

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
[virtual-machines-azurerm-versus-azuresm]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:windows/classic/ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:windows/classic/ps-sql-int-listener.md
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
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../azure-resource-manager/xplat-cli-azure-resource-manager.md

Esta guía forma parte de la documentación sobre la implementación del software de SAP en Microsoft Azure. Antes de leer esta guía, consulte la [Guía de planeamiento e implementación][planning-guide]. En este documento se explica cómo implementar varios sistemas de administración de bases de datos relacionales (RDBMS) y productos relacionados, además de SAP en máquinas virtuales de Microsoft Azure, mediante el uso de las funcionalidades de la infraestructura como servicio (IaaS) de Azure.

Dicho documento complementa la documentación de instalación de SAP y las notas de SAP, que representan los principales recursos para las instalaciones e implementaciones de software de SAP en determinadas plataformas.

## <a name="general-considerations"></a>Consideraciones generales
En este capítulo, se presentan consideraciones sobre ejecución de sistemas DBMS relacionados con SAP en máquinas virtuales de Azure. En este capítulo, se hacen algunas referencias a sistemas DBMS específicos. No obstante, ahondaremos en este tema en el siguiente capítulo de este documento.

### <a name="definitions-upfront"></a>Lista de definiciones
En el documento se utilizarán los términos siguientes:

* IaaS: infraestructura como servicio.
* PaaS: plataforma como servicio.
* SaaS: software como servicio.
* Componente de SAP: una aplicación de SAP individual, como ECC, BW, Solution Manager o EP.  Los componentes de SAP pueden basarse en tecnologías tradicionales, como ABAP o Java, o en una aplicación no basada en NetWeaver, como Business Objects.
* Entorno de SAP: uno o varios componentes de SAP agrupados lógicamente para desempeñar una función empresarial, como desarrollo, control de calidad, aprendizaje, recuperación ante desastres o producción.
* Infraestructura de SAP: hace referencia a todos los activos de SAP de la infraestructura de TI de un cliente. La infraestructura de SAP incluye todos los entornos, tanto los que son de producción como los que no.
* Sistema SAP: la combinación de la capa DBMS y la de aplicaciones, como la de un sistema de desarrollo SAP ERP, de prueba SAP BW, de producción SAP CRM, etc. En las implementaciones de Azure no se admite la división de estas dos capas entre la infraestructura local y de Azure. Esto significa que un sistema SAP debe implementarse de forma local o en Azure, pero no en ambos. Sin embargo, los diferentes sistemas de un entorno de SAP pueden implementarse en Azure o de forma local. Por ejemplo, podría implementar los sistemas de prueba y desarrollo de SAP CRM en Azure, y el sistema de producción de SAP CRM de manera local.
* Implementación exclusiva en la nube: una implementación donde la suscripción de Azure no está conectada de sitio a sitio o a través de ExpressRoute a la infraestructura de red local. En la documentación habitual de Azure este tipo de implementaciones se denomina "implementaciones exclusivas en la nube". A las máquinas virtuales implementadas con este método se tiene acceso a través de Internet y puntos de conexión de Internet asignados a las máquinas virtuales de Azure. El DNS y Active Directory (AD) locales no se extienden a Azure en este tipo de implementaciones. Por lo tanto, las máquinas virtuales no forman parte de Active Directory local. Nota: Las implementaciones exclusivas en la nube se definen en este documento como infraestructuras de SAP completas ejecutadas exclusivamente en Azure sin extensión de Active Directory ni resolución de nombres desde la infraestructura local a la nube pública. No se admiten configuraciones exclusivas en la nube con sistemas de producción SAP, o con configuraciones donde deban usarse SAP STMS u otros recursos locales entre sistemas SAP hospedados en Azure y recursos locales.
* Entre locales: describe un escenario donde se implementan máquinas virtuales en una suscripción de Azure con conexión de sitio a sitio, entre varios sitios o de ExpressRoute entre los centros de datos locales y Azure. En la documentación habitual de Azure, este tipo de implementaciones se denominan "escenarios entre locales". El motivo de la conexión es ampliar los dominios locales, Active Directory local y DNS local a Azure. La infraestructura local se extiende a los recursos de Azure de la suscripción. Con esta extensión, las máquinas virtuales pueden formar parte del dominio local. Los usuarios del dominio local pueden tener acceso a los servidores y ejecutar servicios en esas máquinas virtuales (por ejemplo, servicios de DBMS). Es posible la comunicación y resolución de nombres entre máquinas virtuales implementadas de forma local y en Azure. Esperamos que este sea el escenario más habitual para implementar activos de SAP en Azure. Para más información, consulte [este][vpn-gateway-cross-premises-options] artículo y [este][vpn-gateway-site-to-site-create] vínculo.

> [!NOTE]
> En sistemas de producción SAP, se admiten implementaciones entre locales de sistemas SAP donde las máquinas virtuales de Azure que ejecuten sistemas SAP pertenezcan a un dominio local. Las configuraciones entre locales se admiten para la implementación completa o parcial de infraestructuras de SAP en Azure. Incluso la ejecución de una infraestructura completa de SAP en Azure requiere que esas máquinas virtuales formen parte del dominio local y ADS. En versiones anteriores de la documentación se describen escenarios de TI híbridos, donde "híbrido" implica una conectividad entre locales de la infraestructura local y Azure. En este caso, "híbrido" también significa que las máquinas virtuales de Azure forman parte de Active Directory local.
>
>

Algunos documentos de Microsoft describen escenarios entre locales de un modo ligeramente distinto, en especial en configuraciones de DBMS de alta disponibilidad. En el caso de documentos relacionados con SAP, el escenario entre locales se reduce a una conectividad de sitio a sitio o privada (ExpressRoute) y a que la infraestructura de SAP se distribuye entre medios locales y Azure.

### <a name="resources"></a>Recursos
Hay disponibles las siguientes guías sobre el tema de las implementaciones de SAP en Azure:

* [SAP NetWeaver en máquinas virtuales de Azure: guía de planeamiento e implementación][planning-guide]
* [SAP NetWeaver en máquinas virtuales de Azure: guía de implementación][deployment-guide]
* [SAP NetWeaver en máquinas virtuales de Azure: guía de implementación de DBMS (este documento)][dbms-guide]
* [SAP NetWeaver en máquinas virtuales de Azure: guía de implementación de alta disponibilidad][ha-guide]

Las siguientes notas de SAP están relacionadas con el tema de SAP en Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |SAP Applications on Azure: Supported Products and Azure VM types (Aplicaciones de SAP en Azure: tipos de máquina virtual de Azure y productos compatibles) |
| [2015553] |SAP on Microsoft Azure: Support Prerequisites (SAP en Microsoft Azure: requisitos previos de compatibilidad) |
| [1999351] |Troubleshooting Enhanced Azure Monitoring for SAP (Solución de problemas de la supervisión mejorada de Azure para SAP) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Métricas de supervisión clave para SAP en Microsoft Azure) |
| [1409604] |Virtualization on Windows: Enhanced Monitoring (Virtualización en Windows: supervisión mejorada) |
| [2191498] |SAP on Linux with Azure: Enhanced Monitoring (SAP en Linux con Azure: supervisión mejorada) |
| [2039619] |SAP Applications on Microsoft Azure using the Oracle Database: Supported Products and Versions (Aplicaciones de SAP en Microsoft Azure con Base de datos de Oracle: versiones y productos compatibles) |
| [2233094] |DB6: SAP Applications on Azure Using IBM DB2 for Linux, UNIX, and Windows - Additional Information (DB6: aplicaciones de SAP en Azure con IBM DB2 para Linux, UNIX y Windows: información adicional) |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP license issues (Linux y máquinas virtuales de Microsoft Azure (IaaS): problemas de licencia de SAP) |
| [1984787] |SUSE LINUX Enterprise Server 12: Installation notes (SUSE Linux Enterprise Server 12: notas de instalación) |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation and Upgrade (Red Hat Enterprise Linux 7.x: instalación y actualización) |

Lea también la [wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , que contiene todas las notas de SAP para Linux.

Debe tener conocimientos prácticos sobre la arquitectura de Microsoft Azure y cómo se implementan y utilizan las máquinas virtuales de Microsoft Azure. Puede encontrar más información aquí <https://azure.microsoft.com/documentation/>.

> [!NOTE]
> En este documento **no** hablaremos sobre las ofertas de la plataforma como servicio (PaaS) de Microsoft Azure. En su lugar, explicaremos cómo ejecutar un sistema de administración de bases de datos (DBMS) en máquinas virtuales de Microsoft Azure (IaaS) de la misma forma que ejecutaría este tipo de sistema en un entorno local. Las funcionalidades de bases de datos de estas dos ofertas son muy diferentes y no deben combinarse. Consulte también: <https://azure.microsoft.com/services/sql-database/>.
>
>

Como estamos analizando el modelo IaaS, en general, el proceso de instalación y configuración de DBMS, Windows y Linux es prácticamente el mismo en cualquier máquina virtual o máquina sin sistema operativo que instalaría en un entorno local. Sin embargo, hay algunas decisiones de implementación de administración de sistemas y arquitecturas que diferirán cuando se utilice el modelo IaaS. El objetivo de este documento consiste en explicar las diferencias de administración de sistemas y arquitecturas específicas que debe tener en cuenta a la hora de utilizar IaaS.

En general, las principales diferencias que se describen en este artículo son las siguientes:

* Planear el diseño adecuado de los VHD y las máquinas virtuales de los sistemas SAP para garantizar que se dispone de la distribución correcta de archivos de datos y de la cantidad suficiente de IOPS para la carga de trabajo
* Consideraciones sobre redes al utilizar IaaS
* Características específicas de bases de datos que se deben usar para optimizar el diseño de las bases de datos
* Consideraciones sobre las operaciones de copia de seguridad y restauración en IaaS
* Uso de diferentes tipos de imágenes para tareas de implementación
* Alta disponibilidad en IaaS de Azure

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estructura de una implementación de RDBMS
Para poder ir entendiendo lo que se explica en este capítulo, hay que comprender la información de [este] capítulo [deployment-guide-3] de la [Guía de implementación][deployment-guide]. Antes de leer este capítulo, debe comprender lo que explicamos sobre la serie de máquinas virtuales y sus diferencias, así como las disimilitudes entre el almacenamiento estándar y premium de Azure.

Los VHD de Azure que contienen un sistema operativo tenían una limitación de tamaño de 127 GB, Esta limitación se eliminó en marzo de 2015 (para más información, consulte <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). A partir de esa fecha, los VHD con el sistema operativo puede tener el mismo tamaño que cualquier otro disco duro virtual. No obstante, se prefiere una estructura de implementación en la que el sistema operativo, el DBMS y los archivos binarios finales de SAP sean independientes de los archivos de base de datos. Por lo tanto, se espera que los sistemas SAP que se ejecutan en máquinas virtuales de Azure tengan instalada la máquina virtual base (o VHD) con el sistema operativo, y los archivos ejecutables del sistema de administración de bases de datos administración y de SAP. Los archivos de datos y de registro de DBMS se almacenarán en archivos VHD independientes de Almacenamiento de Azure (estándar o premium) y se asociarán como discos lógicos a la máquina virtual de imagen del sistema operativo de Azure original.

En función de cómo se utilice el almacenamiento de Azure estándar o premium (por ejemplo, con máquinas virtuales de serie DS o GS), habrá otras cuotas de Azure que se explican [aquí][virtual-machines-sizes]. Al planear los VHD de Azure, tendrá que encontrar el mejor equilibrio de las cuotas en los siguientes recursos:

* El número de archivos de datos
* El número de VHD que contienen los archivos
* Las cuotas de IOPS de un solo VHD
* El rendimiento por VHD
* El número de VHD adicionales posibles por tamaño de máquina virtual
* El rendimiento de almacenamiento global que puede proporcionar una máquina virtual

Azure aplicará una cuota de IOPS por unidad de VHD. Estas cuotas son diferentes para los VHD hospedados en el almacenamiento estándar y premium de Azure. Las latencias de E/S también será muy distintas entre los dos tipos de almacenamiento; Almacenamiento premium ofrece mejores latencias de E/S. Cada uno de los distintos tipos de máquina virtual tiene un número limitado de VHD que se pueden asociar. Otra restricción consiste en que solo determinados tipos de máquinas virtuales pueden utilizar el Almacenamiento premium de Azure. Es decir, la decisión de elegir un determinado tipo de máquina virtual podría no estar basada exclusivamente en los requisitos de CPU y memoria, sino también en los de rendimiento de disco, latencia y número de IOPS que normalmente se escalan con el número de VHD o el tipo de discos de Almacenamiento premium. El tamaño de un VHD también podría venir determinado por el número de IOPS y el rendimiento que haya que obtener en cada disco duro virtual, especialmente con el Almacenamiento premium.

El hecho de que la tasa de IOPS general, el número de VHD montados y el tamaño de la máquina virtual estén relacionados puede provocar que la configuración de Azure de un sistema SAP sea diferente a la de la implementación local. Los límites de IOPS por LUN suelen poder configurarse en las implementaciones locales, al contrario que en el almacenamiento de Azure, donde los límites son fijos, o en el Almacenamiento Premium, que dependen del tipo de disco. Por tanto, con las implementaciones locales encontramos configuraciones de clientes de servidores de bases de datos que utilizan muchos volúmenes diferentes para archivos ejecutables especiales como SAP y DBMS, o bien volúmenes especiales para espacios de tablas o bases de datos temporales. Cuando se mueven dichos sistemas locales a Azure, podría perderse ancho de banda de IOPS potencial al desperdiciar un VHD para archivos ejecutables o bases de datos que no realizan operaciones IOPS. Por lo tanto, en máquinas virtuales de Azure recomendamos que los archivos ejecutables de SAP y DBMS se instalen, si es posible, en el disco del sistema operativo.

La colocación de los archivos de base de datos y de registro y el tipo de almacenamiento de Azure que se utilice se debe definir en función de los requisitos de IOPS, latencia y rendimiento. Con el objetivo de disponer de una cantidad suficiente de IOPS para el registro de transacciones, es posible que tenga que utilizar varios VHD para el archivo de registro de transacciones o emplear un disco de Almacenamiento premium de mayor tamaño. En este caso, bastaría simplemente con compilar software RAID (por ejemplo, un grupo de almacenamiento de Windows para Windows o MDADM y LVM [Logical Volumen Manager] para Linux) con los VHD que contendrán el registro de transacciones.

- - -
> ![Windows][Logo_Windows] Windows
>
> La unidad D:\ de una máquina virtual de Azure es una unidad no persistente respaldada por algunos discos locales del nodo de proceso de Azure. Como no es persistente, significa que los cambios realizados en el contenido de la unidad D:\ se perderán cuando se reinicie la máquina virtual. Por "cambios", nos referimos a los archivos guardados, los directorios creados, las aplicaciones instaladas, etc.
>
> ![Linux][Logo_Linux] Linux
>
> Las máquinas virtuales de Linux de Azure montan automáticamente una unidad en /mnt/Resource, que se trata de una unidad no persistente respaldada por discos locales del nodo de proceso de Azure. Como no es persistente, significa que los cambios realizados en el contenido de la unidad /mnt/resource se perderán cuando se reinicie la máquina virtual. Por "cambios", nos referimos a los archivos guardados, los directorios creados, las aplicaciones instaladas, etc.
>
>

- - -
En función de la serie de máquinas virtuales de Azure, los discos locales del nodo de proceso tendrán un rendimiento diferente. Esto se puede clasificar de la siguiente forma:

* A0-A7: rendimiento muy limitado. No se puede usar para un recurso que no sea un archivo de paginación de Windows.
* A8-A11: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie D: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie DS: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie G: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.
* Serie GS: características de rendimiento muy buenas con 10 000 IOPS y una tasa de más de 1 GB/seg.

Las afirmaciones anteriores se aplican a los tipos de máquinas virtuales que estén certificados para utilizarse con SAP. La serie de máquina virtual con un rendimiento y una cantidad de IOPS excelentes puede aprovechar algunas características de DBMS, como tempdb o el espacio de tabla temporal.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Almacenamiento en caché de máquinas virtuales y VHD
Al crear esos discos o VHD mediante el portal o cuando montemos VHD cargados en máquinas virtuales, podremos elegir si almacenar en caché el tráfico de E/S entre la máquina virtual y los VHD ubicados en el almacenamiento de Azure. El almacenamiento estándar y premium de Azure utilizan dos tecnologías diferentes para este tipo de almacenamiento en caché. En ambos casos, la memoria caché sería el disco cuya copia de seguridad se realiza en la misma unidad que emplea el disco temporal (D:\ en Windows o /mnt/resource en Linux) de la máquina virtual.

En el almacenamiento estándar de Azure, estos son los tipos posibles de almacenamiento en caché:

* Sin almacenamiento en caché
* Almacenamiento en caché de lectura
* Almacenamiento en caché de lectura y escritura

Para obtener un rendimiento coherente y determinista, debe establecer el valor del almacenamiento en caché de Almacenamiento de Azure estándar en todos los VHD que contienen **archivos de datos, archivos de registro y espacio de tablas relacionados con DBMS en NINGUNO**. El valor del almacenamiento en caché de la máquina virtual puede seguir siendo el predeterminado.

Para el Almacenamiento premium de Azure se encuentran disponibles las siguientes opciones de almacenamiento en caché:

* Sin almacenamiento en caché
* Almacenamiento en caché de lectura

Para Azure Premium Storage, se recomienda aprovechar el **almacenamiento en caché de lectura para archivos de datos** de la base de datos de SAP y no elegir **almacenamiento en caché para los VHD de archivos de registro**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>RAID de software
Como ya se ha indicado anteriormente, tendrá que equilibrar el número de operaciones IOPS necesarias para los archivos de base de datos entre el número de VDH que se pueden configurar y la cantidad máxima de IOPS que proporcionará una máquina virtual de Azure por tipo de disco de Almacenamiento premium o VHD. La forma más sencilla de gestionar la carga de IOPS en los VHD consiste en compilar software RAID en los diferentes discos duros virtuales. Después, coloque una serie de archivos de datos del DBMS de SAP en los LUN extraídas del software RAID. En función de los requisitos, se recomienda también plantearse el uso de Almacenamiento premium, ya que dos de los tres discos de Almacenamiento premium diferentes proporcionan mayor cuota de IOPS que los VHD basados en el almacenamiento estándar. Además, Almacenamiento premium de Azure ofrece mucha mejor latencia de E/S.

Lo mismo se aplica al registro de transacciones de los diferentes sistemas DBMS. Muchos de ellos simplemente agregan más archivos de registro de transacciones, lo que no resulta de ayuda, ya que los sistemas DBMS solo realizan operaciones de escritura en uno de los archivos a la vez. Si se necesitan tasas de IOPS más elevadas que las que ofrece un único almacenamiento estándar basado en VHD, puede realizar secciones en varios VHD de almacenamiento estándar o usar un tipo de disco de almacenamiento Premium mayor que ofrezca, aparte de mayores tasas de IOPS, una menor latencia de E/S en la operación de E/S de escritura en el registro de transacciones.

Estas son las situaciones experimentadas en las implementaciones de Azure que se favorecerían si se emplea software RAID:

* El registro de transacciones y el de rehacer necesitan más IOPS que lo que ofrece Azure para un solo VHD. Tal y como se mencionó anteriormente, esto puede solucionarse creando un LUN en varios VHD que utilicen software RAID.
* La distribución de cargas de trabajo de E/S desigual por los diferentes archivos de datos de la base de datos de SAP. En tales casos, se puede experimentar que un archivo de datos alcance la cuota con bastante frecuencia; mientras que otros archivos de datos ni siquiera se acercan a la cuota de IOPS de un solo VHD. En estos casos, la solución más sencilla consiste en crear un LUN en varios VHD que utilicen software RAID.
* No sabe cuál es la carga de trabajo de E/S exacta por archivo de datos y solo conoce, más o menos, la carga de trabajo general de IOPS en el DBMS. La forma más sencilla es crear un LUN con la ayuda de un software RAID. La suma de las cuotas de varios VHD subyacentes a este LUN debe alcanzar la tasa de IOPS conocida.

- - -
> ![Windows][Logo_Windows] Windows
>
> Se recomienda usar Windows Server 2012 o espacios de almacenamiento superior, ya que es más eficaz que la funcionalidad de creación de secciones de versiones anteriores de Windows. Tenga en cuenta que es posible que tenga que crear los espacios y los grupos de almacenamiento de Windows mediante comandos de PowerShell al utilizar Windows Server 2012 como sistema operativo. Los comandos de PowerShell se pueden encontrar aquí <https://technet.microsoft.com/library/jj851254.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> Para crear un software RAID en Linux, solo se admiten MDADM y LVM (Logical Volume Manager). Para más información, consulte los siguientes artículos:
>
> * [Configuración de RAID de software en Linux][virtual-machines-linux-configure-raid] (para MDADM)
> * [Configuración del LVM en una máquina virtual Linux en Azure][virtual-machines-linux-configure-lvm]
>
>

- - -
Estas suelen ser las consideraciones que hay que tener en cuenta para utilizar la serie de máquinas virtuales que pueden funcionar con el Almacenamiento premium de Azure normalmente son:

* Peticiones de latencias de E/S que se acerquen a los que proporcionan los dispositivos SAN o NAS
* Petición proporcionar una mejor latencia de E/S que la que puede proporcionar el almacenamiento estándar de Azure.
* Mayor tasa de IOPS por máquina virtual que la que podría obtenerse con varios VHD de almacenamiento estándar en un determinado tipo de máquina virtual.

Puesto que el almacenamiento de Azure subyacente replica cada VHD en al menos tres nodos de almacenamiento, puede usarse la funcionalidad de creación se secciones de RAID 0. No hay que implementar RAID5 o RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Almacenamiento de Microsoft Azure
Almacenamiento de Microsoft Azure almacenará la máquina virtual (con SO) y los VHD o blobs base en, al menos, 3 nodos de almacenamiento independientes. Al crear una cuenta de almacenamiento, se ofrece una opción de protección, tal y como se muestra aquí:

![Replicación geográfica habilitada para la cuenta de Almacenamiento de Azure][dbms-guide-figure-100]

La replicación local de Almacenamiento de Azure (con redundancia local) proporciona niveles de protección contra la pérdida de datos debido a un error de infraestructura que pocos clientes pueden permitirse implementar. Tal y como se mostró anteriormente, hay 4 opciones diferentes con una quinta que es una variación de una de las tres primeras. Si las analizamos más detenidamente, podemos distinguir:

* **Almacenamiento con redundancia local (LRS) premium**: el Almacenamiento premium de Azure ofrece compatibilidad con discos de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Existen 3 réplicas de los datos en el mismo centro de datos de Azure de una región de Azure. Las copias se encontrarán en dominios de error y actualización diferentes (para familiarizarse con los conceptos, consulte [este] [planning-guide-3.2] capítulo de la [Guía de planeamiento][planning-guide]). En el caso de que una réplica de los datos se quede fuera de servicio debido a un error de disco o del nodo de almacenamiento, se generará automáticamente una nueva réplica.
* **Almacenamiento con redundancia local (LRS)**: en este caso, existen tres réplicas de los datos en el mismo centro de datos de Azure de una región de Azure. Las copias se encontrarán en dominios de error y actualización diferentes (para familiarizarse con los conceptos, consulte [este] [planning-guide-3.2] capítulo de la [Guía de planeamiento][planning-guide]). En el caso de que una réplica de los datos se quede fuera de servicio debido a un error de disco o del nodo de almacenamiento, se generará automáticamente una nueva réplica.
* **Almacenamiento con redundancia geográfica (GRS)**: en este caso, hay una replicación asincrónica que alimentará tres réplicas adicionales de los datos en otra región de Azure que se encuentra, en la mayoría de los casos, en la misma región geográfica (como Europa del Norte y Europa Occidental). Esta opción generará 3 réplicas adicionales, por lo que habrá 6 réplicas en total. Una variación de esta opción es una adición en la que pueden utilizarse los datos de la región de Azure replicada geográficamente para operaciones de lectura (redundancia geográfica con acceso de lectura).
* **Almacenamiento con redundancia de zona (ZRS)**: en este caso, las tres réplicas de los datos permanecen en la misma región de Azure. Tal y como se explica en [este][planning-guide-3.1] capítulo de la [Guía de planeamiento][planning-guide], una región de Azure puede incluir varios centros de datos que se encuentren muy cerca. En el caso de LRS, las réplicas se distribuyen por los distintos centros de datos que conforman una región de Azure.

Puede encontrar más información [aquí][storage-redundancy].

> [!NOTE]
> En lo que respecta a las implementaciones de DBMS, no se recomienda usar el almacenamiento con redundancia geográfica.
>
> La replicación geográfica del almacenamiento de Azure es asincrónica. La replicación de VHD individuales montados en una sola máquina virtual no se sincroniza en el paso de bloqueo. Por lo tanto, no es adecuada para replicar los archivos DBMS que se distribuyen por diferentes VHD o que se implementan con un software RAID basado en varios discos duros virtuales. El software de DBMS requiere que el almacenamiento en disco persistente se sincronice con precisión en los distintos LUN y en los VHD, discos o ejes subyacentes. El software de DBMS utiliza diversos mecanismos para secuenciar las actividades de escritura de E/S y un DBMS informará de que el almacenamiento en disco que tiene como destino la replicación está dañado si dichas actividades varían en unos pocos milisegundos. Por consiguiente, si se quiere disponer de una configuración de base de datos con una base de datos que se extienda en varios VHD replicados geográficamente, dicha replicación debe realizarse con la funcionalidad y los medios de la base de datos. No se debe depender de la replicación geográfica del almacenamiento de Azure para llevar a cabo este trabajo.
>
> El problema se explica mejor con un sistema de ejemplo. Supongamos que tiene un sistema SAP cargado en Azure con 8 discos VHD que contienen archivos de datos del DBMS más otro disco duro virtual con el archivo de registro de transacciones. En cada uno de estos 9 VHD se escriben datos con un método coherente con el DBMS, con independencia de que esta operación se realice en los archivos de registro de transacciones o de datos.
>
> Para replicar geográficamente y de manera eficaz los datos y mantener una imagen coherente de la base de datos, el contenido de los 9 VHD tendría que replicarse geográficamente en el orden exacto en que se ejecutaron las operaciones de E/S en estos 9 VHD. Sin embargo, la replicación geográfica del almacenamiento de Azure no permite declarar las dependencias entre los VHD. Es decir, la replicación geográfica del almacenamiento de Microsoft Azure no conoce el hecho de que el contenido de estos 9 VHD diferentes está relacionado entre sí, ni tampoco que los cambios de datos solo son coherentes cuando la replicación se realiza en el orden en que se llevan a cabo las operaciones de E/S en esos 9 discos duros virtuales.
>
> Además de que existen bastantes probabilidades de que las imágenes replicadas geográficamente del escenario no proporcionen una imagen coherente de la base de datos, el almacenamiento con redundancia geográfica puede afectar considerablemente al rendimiento. En resumen, no utilice este tipo de redundancia de almacenamiento para las cargas de trabajo de tipo DBMS.
>
>

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Asignación de VHD en cuentas de almacenamiento del servicio de máquina virtual de Azure
Las cuentas de almacenamiento de Azure se usan con fines administrativos y presentan algunas limitaciones. Sin embargo, las limitaciones serán diferentes en función de si se trata de una cuenta de almacenamiento de Azure estándar o una premium. En [este artículo][storage-scalability-targets] se muestran las funcionalidades y limitaciones exactas.

Es importante señalar que en el almacenamiento estándar de Azure hay un límite en el número de E/S por segundo para cada cuenta de almacenamiento (en la fila de velocidad total de solicitudes de [este artículo][storage-scalability-targets]). Además, hay un límite inicial de 100 cuentas de almacenamiento por suscripción de Azure (a partir de julio de 2015). Por lo tanto, se recomienda equilibrar la cantidad de IOPS de máquinas virtuales entre las diferentes cuentas de almacenamiento al usar el almacenamiento estándar de Azure. Asimismo, lo ideal es que una sola máquina virtual utilice una cuenta de almacenamiento, si es posible. Por consiguiente, si hablamos de implementaciones de DBMS donde cada VHD que se hospeda en el almacenamiento estándar de Azure puede alcanzar su límite de cuota, solo se deben implementar entre 30 y 40 VHD por cuenta de almacenamiento de Azure que utilice el almacenamiento estándar de Azure. Por otro lado, si utiliza el Almacenamiento premium de Azure y desea almacenar volúmenes de base de datos de mayor volumen, le recomendamos equilibrar el número de IOPS. No obstante, una cuenta de Almacenamiento premium de Azure es mucho más restrictiva en lo que respecta al volumen de datos que una estándar. Como resultado, solo se puede implementar un número limitado de VHD en una cuenta de Almacenamiento premium de Azure antes de alcanzar el límite de volumen de datos. Finalmente, también hay que tener en cuenta que una cuenta de almacenamiento de Azure funciona como una SAN virtual y presenta limitaciones en cuanto a capacidad e IOPS. Como resultado, hay que seguir definiendo, como en las implementaciones locales, el diseño de los VHD de los distintos sistemas SAP en los diferentes dispositivos SAN imaginarios o cuentas de almacenamiento de Azure.

Para el almacenamiento estándar de Azure no se recomienda, si es posible, proporcionar el almacenamiento de diferentes cuentas de almacenamiento en una sola máquina virtual.

Al utilizar la serie DS o GS de máquinas virtuales de Azure es posible montar los VHD de cuentas de almacenamiento estándar y Premium Storage de Azure. A la mente vienen casos de uso en los que puede aprovecharse el almacenamiento heterogéneo. Por ejemplo, escribir copias de seguridad en VHD respaldadas por el almacenamiento estándar mientras que los archivos de registro y datos de DBMS se mantienen en el Almacenamiento premium.

En función de las pruebas e implementaciones de entre 30 y 40 VHD que contienen archivos de registro y de datos de base de datos, el aprovisionamiento puede realizarse en una única cuenta de almacenamiento estándar de Azure con un rendimiento aceptable. Tal y como se mencionó anteriormente, es probable que la limitación de una cuenta de Almacenamiento premium de Azure sea la capacidad de los datos que puede albergar y no los IOPS.

Al igual que con los dispositivos SAN locales, para poder compartir recursos hay que realizar algunas tareas de supervisión. De este modo, se pueden detectar los cuellos de botella que puedan producirse en una cuenta de almacenamiento de Azure. La extensión de supervisión de Azure para SAP y el Portal de Azure son algunas de las herramientas que puede utilizar para detectar cuentas de almacenamiento de Azure ocupadas que pueden estar proporcionando un rendimiento de E/S poco óptimo.  Si se detecta esta situación, se recomienda mover las máquinas virtuales ocupadas a otra cuenta de almacenamiento de Azure. Consulte la [Guía de implementación][deployment-guide] para más información sobre cómo activar las funcionalidades de supervisión de hosts de SAP.

Otro artículo donde se resumen los procedimientos recomendados para el almacenamiento estándar de Azure y las cuentas de almacenamiento estándar de Azure se puede encontrar aquí <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Sobre el procedimiento para mover máquinas virtuales de DBMS implementadas del almacenamiento estándar al premium de Azure
Nos encontramos con bastante frecuencia algunos escenarios donde, como cliente, desea mover una máquina virtual implementada del almacenamiento de estándar al premium de Azure. Este procedimiento no puede realizarse sin mover físicamente los datos. Hay varias maneras de conseguir este objetivo.

* Podría copiar simplemente todos los VHD, el VHD base y los VHD de datos en una nueva cuenta de Almacenamiento premium de Azure. Es bastante frecuente que el número de VHD del almacenamiento estándar de Azure no se elija por el hecho de que se necesita ese volumen de datos concreto, sino por los IOPS. Cuando se mueven todos los VHD al Almacenamiento premium de Azure, con menos discos duros virtuales podría obtener el mismo rendimiento de IOPS. Como el almacenamiento estándar de Azure se paga por los datos usados y no por el tamaño nominal del disco, la cantidad de VHD no es relevante en términos de costes. Sin embargo, con el Almacenamiento premium de Azure Premium pagaría por el tamaño nominal del disco. Por lo tanto, la mayoría de los clientes tratan de mantener el número de VHD de Azure del Almacenamiento Premium en la cantidad necesaria para obtener el rendimiento de IOPS necesario. En definitiva, la mayoría de los clientes deciden adoptar el modelo de copia 1:1.
* Si aún no lo ha hecho, monte un solo VHD que pueda contener una copia de seguridad de la base de datos de SAP. Después de realizar la copia de seguridad, desmonte todos los VHD, incluido el que contiene la copia de seguridad, y copie el VHD base y el de la copia de seguridad en una cuenta de Almacenamiento premium de Azure. Después, implementaría la máquina virtual basada en el VHD y montaría el VHD con la copia de seguridad. Ahora, cree discos de Almacenamiento premium vacíos adicionales para la máquina virtual que se utilizó para restaurar la base de datos. Se supone que el DBMS permite cambiar las rutas de acceso a los archivos de registro y de datos como parte del proceso de restauración.
* Otra posibilidad consiste en una variación del proceso anterior, donde simplemente se copia el VHD de copia de seguridad en el Almacenamiento premium de Azure y se asocia en una máquina virtual que haya implementado e instalado recientemente.
* La cuarta posibilidad consistiría en cambiar el número de archivos de datos de la base de datos cuando se necesario. En ese caso, realizaría una copia del sistema homogéneo de SAP mediante procesos de exportación e importación. Coloque los archivos de exportación en un VHD que se haya copiado en una cuenta de Almacenamiento premium de Azure y asóciela a una máquina virtual que utilice para ejecutar los procesos de importación. Los clientes usan esta posibilidad principalmente cuando desean reducir el número de archivos de datos.

### <a name="deployment-of-vms-for-sap-in-azure"></a>Implementación de máquinas virtuales para SAP en Azure
Microsoft Azure ofrece varias maneras de implementar máquinas virtuales y discos asociados. Por tanto, es muy importante comprender las diferencias, puesto que los preparativos de las máquinas virtuales pueden variar según la forma de implementación. En general, nos centramos en los escenarios que se describen en los capítulos siguientes.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Implementación de máquinas virtuales desde Azure Marketplace
Desea seleccionar una imagen proporcionada por Microsoft o un tercero en Azure Marketplace para implementar la máquina virtual. Una vez que se ha implementado la máquina virtual en Azure, sigue las mismas instrucciones y utilice las mismas herramientas para instalar el software de SAP en la máquina virtual de la misma forma que lo haría en un entorno local. Para instalar el software de SAP en la máquina virtual de Azure, SAP y Microsoft recomiendan cargar y almacenar el medio de instalación de SAP en los VHD de Azure, o bien crear una máquina virtual de Azure que funcione como un servidor de archivos que contenga todos los medios de instalación de SAP que resulten necesarios.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Implementación de máquinas virtuales con una imagen generalizada específica del cliente
Debido a los requisitos de revisión específicos en lo que respecta a la versión del SO o DBMS, puede que las imágenes proporcionadas en Azure Marketplace no satisfagan sus necesidades. Por lo tanto, se recomienda crear una máquina virtual con su propia imagen privada de máquina virtual de SO o DBMS que pueda implementarse varias veces más adelante. Para preparar una imagen privada para tareas de duplicación, el sistema operativo de la máquina virtual local debe ser generalizado. Consulte la [Guía de implementación][deployment-guide] para más información sobre cómo generalizar una máquina virtual.

Si ya ha instalado contenido de SAP en la máquina virtual local (especialmente en los sistemas de dos niveles), puede adaptar la configuración del sistema SAP después de realizar la implementación de la máquina virtual de Azure mediante el procedimiento de cambio de nombre de instancia admitido por el administrador de aprovisionamiento de software de SAP (nota de SAP [1619720]). De lo contrario, puede instalar el software de SAP después de la implementación de la máquina virtual de Azure.

A partir del contenido de base de datos que usa la aplicación de SAP, puede generar el contenido nuevo mediante una instalación de SAP o importarlo a Azure utilizando un VHD con una copia de seguridad de base de datos de DBMS o aprovechando las funcionalidades del DBMS para realizar la copia de seguridad directamente en el almacenamiento de Microsoft Azure. En este caso, puede preparar los VHD con los archivos de registro y de datos de DBMS local y, después, importarlos como discos en Azure. Sin embargo, la transferencia de datos de DBMS que se carga desde el entorno local a Azure funcionaría en los discos VHD que tienen que estar preparados en el entorno local.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Sobre el procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado
Planea mover un sistema SAP específico desde un entorno local a Azure (elevar y desplazar). Esto puede hacerse cargando en Azure el VHD que contiene el sistema operativo, los archivos binarios de SAP y los finales de DBMS, así como los discos duros virtuales con los archivos de registro y de datos del DBMS. En contraposición al segundo escenario anterior, mantendrá el nombre de host, el SID de SAP y las cuentas de usuario de SAP en la máquina virtual de Azure, ya que se configuraron en el entorno local. Por lo tanto, no es necesario generalizar la imagen. Este caso se aplicará principalmente a escenarios entre locales donde una parte de la infraestructura de SAP se ejecuta de forma local y otra en Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Alta disponibilidad y recuperación ante desastres con máquinas virtuales de Azure
Azure ofrece las siguientes funcionalidades de alta disponibilidad (HA) y recuperación ante desastres (DR) que se aplican a los diferentes componentes que se utilizarían para las implementaciones de SAP y DBMS.

### <a name="vms-deployed-on-azure-nodes"></a>Máquinas virtuales implementadas en nodos de Azure
La plataforma Azure no ofrece características como la migración en vivo de máquinas virtuales implementadas. Es decir, que si hay que realizar un mantenimiento en un clúster de servidor en el que se ha implementado una máquina virtual, esta debe detenerse y reiniciarse. El mantenimiento de Azure se realiza utilizando los llamados "dominios de actualización" de los clústeres de servidores. Solo se mantiene a la vez un único dominio de actualización. Durante un reinicio se producirá una interrupción del servicio mientras la máquina virtual está apagada, se realiza el mantenimiento y se reinicia la máquina virtual. Sin embargo, la mayoría de los proveedores de DBMS proporciona funcionalidades de alta disponibilidad y recuperación ante desastres que reinician rápidamente los servicios de DBMS de otro nodo si el principal no está disponible. La plataforma Azure ofrece funcionalidades para distribuir las máquinas virtuales, el almacenamiento y otros servicios de Azure por los dominios de actualización para asegurarse de que los errores de infraestructura o mantenimientos planeados afecten solo a un pequeño subconjunto de máquinas virtuales o servicios.  Si se realiza una planeación cuidadosa, se pueden alcanzar niveles de disponibilidad comparables a los de las infraestructuras locales.

Los conjuntos de disponibilidad de Microsoft Azure constituyen una agrupación lógica de máquinas virtuales o servicios que garantizan que las máquinas virtuales y otros servicios se distribuyan por diferentes dominios de error y actualización de un clúster de forma que solo se produzca el apagado de un nodo en un momento dado (consulte [este][virtual-machines-manage-availability] artículo para más información).

Debe configurarse expresamente durante la implementación de máquinas virtuales y como se muestra aquí:

![Definición de conjunto de disponibilidad para configuraciones de alta disponibilidad de DBMS][dbms-guide-figure-200]

Si deseamos crear configuraciones de alta disponibilidad de implementaciones de DBMS (independientemente de la funcionalidad de DBMS de alta disponibilidad que se emplee), las máquinas virtuales de DBMS tendrían que cumplir los siguientes requisitos:

* Agregar las máquinas virtuales a la misma red virtual de Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* Las máquinas virtuales de la configuración de alta disponibilidad también deben estar en la misma subred. El proceso de resolución de nombres entre las diferentes subredes no se puede realizar en las implementaciones exclusivas en la nube, solo funcionará la resolución de direcciones IP. Al utilizar la conectividad ExpressRoute de sitio a sitio para las implementaciones entre locales, ya deberá haber establecida una red con al menos una subred. El proceso de resolución de nombres se realizará según la infraestructura de red y las directivas AD locales.

[comment]: <> (MSSedusch TODO Test if still true in ARM)

#### <a name="ip-addresses"></a>Direcciones IP
Se recomienda encarecidamente configurar las máquinas virtuales para configuraciones de alta disponibilidad de una manera flexible. En Azure no se recomienda depender de las direcciones IP para abordar los asociados de alta disponibilidad dentro de la configuración de alta disponibilidad, a menos que se utilizan direcciones IP estáticas. Hay dos conceptos de apagado en Azure:

* Apagado a través del Portal de Azure o el cmdlet de Azure PowerShell Stop-AzureRmVM: en este caso, la máquina virtual se apaga y se anula la asignación. No se realizará ningún cargo en su cuenta de Azure por esta máquina virtual; los únicos gastos que se le cobrarán serán por el almacenamiento usado. Sin embargo, si la dirección IP privada de la interfaz de red no era estática, se liberará la dirección IP y no se garantizará que la interfaz de red vuelva a tener asignada la dirección IP antigua después de reiniciar la máquina virtual. Al realizar el apagado a través del Portal de Azure o mediante una llamada a Stop-AzureRmVM, se anulará automáticamente la asignación. Si no desea anular la asignación de la máquina, use Stop-AzureRmVM -StayProvisioned.
* Si apaga la máquina virtual desde un sistema operativo, esta se apagará y NO se anulará la asignación. Sin embargo, en este caso, se siguen realizando cargos en la cuenta de Azure por la máquina virtual, a pesar de que está apagada. En tal caso, la asignación de la dirección IP a una máquina virtual detenida permanecerá intacta. Al apagar la máquina virtual desde esta no se forzará automáticamente la anulación de la asignación.

Incluso en los escenarios entre locales, de forma predeterminada, tras realizarse un apagado y una anulación de la asignación, se desasignarán las direcciones IP de la máquina virtual, aunque las directivas locales de la configuración de DHCP sean diferentes.

* Existe una excepción si se asigna una dirección IP estática a una interfaz de red tal y como se describe [aquí][virtual-networks-reserved-private-ip].
* En tal caso, la dirección IP permanecerá fija mientras no se elimine la interfaz de red.

> [!IMPORTANT]
> Para conseguir que todo el proceso de implementación sea sencillo y fácil de administrar, sin duda, se recomienda configurar las máquinas virtuales asociadas a un DBMS de alta disponibilidad o a una configuración de recuperación ante desastres en Azure de manera que se realice correctamente un proceso de resolución de nombres entre las diferentes máquinas virtuales.
>
>

## <a name="deployment-of-host-monitoring"></a>Implementación de funcionalidades de supervisión de hosts
Para usar de manera productiva las aplicaciones de las máquinas virtuales de Azure, SAP requiere que se obtengan datos de supervisión de los hosts físicos que se ejecutan en las máquinas virtuales de Azure. Se requiere un nivel de revisión específico del agente de host de SAP que permita esta funcionalidad en SAPOSCOL y el agente de host de SAP. El nivel de revisión exacto se menciona en la nota de SAP [1409604].

Para más información sobre la implementación de componentes que proporcionen datos de host a SAPOSCOL y el agente de host de SAP, así como sobre la administración del ciclo de vida de los componentes, consulte la [Guía de implementación][deployment-guide].

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Consideraciones concretas sobre Microsoft SQL Server
### <a name="sql-server-iaas"></a>IaaS de SQL Server
A partir de Microsoft Azure, puede migrar fácilmente sus aplicaciones de SQL Server integradas en la plataforma Windows Server a máquinas virtuales de Azure. SQL Server en una máquina virtual permite reducir el coste total de propiedad de implementación, administración y mantenimiento de aplicaciones empresariales al migrar fácilmente estas aplicaciones a Microsoft Azure. Con SQL Server en una máquina virtual de Azure, los administradores y los desarrolladores pueden seguir usando las mismas herramientas de desarrollo y administración que se encuentran disponibles de forma local.

> [!IMPORTANT]
> Tenga en cuenta que no hablaremos sobre Base de datos SQL de Microsoft Azure, que es la oferta de plataforma como servicio de Microsoft Azure. En este documento se aborda la ejecución del producto SQL Server tal cual para implementaciones locales en máquinas virtuales de Azure, con lo que se aprovecha la funcionalidad de infraestructura como servicio. Las funcionalidades de bases de datos de estas dos ofertas son diferentes y no deben combinarse. Consulte también: <https://azure.microsoft.com/services/sql-database/>.
>
>

Se recomienda encarecidamente revisar [esta][virtual-machines-sql-server-infrastructure-services] documentación antes de continuar.

En las secciones siguientes se agregan y mencionan fragmentos de partes de la documentación del vínculo anterior. Se describen de forma más detallada algunos conceptos y se mencionan consideraciones sobre SAP. Sin embargo, se recomienda encarecidamente consultar la documentación de arriba primero antes de leer la documentación específica de SQL Server.

Debe conocer información específica sobre SQL Server en IaaS antes de continuar:

* **Acuerdo de Nivel de Servicio de máquina virtual**: hay un Acuerdo de Nivel de Servicio para Virtual Machines que se ejecuta en Azure que puede encontrarse aquí: <https://azure.microsoft.com/support/legal/sla/>.  
* **Soporte para versiones de SQL**: para los clientes de SAP, prestamos soporte para SQL Server 2008 R2 y posteriores en las máquinas virtuales de Microsoft Azure. No se prestará soporte para versiones anteriores. Revise esta [declaración de soporte](https://support.microsoft.com/kb/956893) general para más información. Tenga en cuenta que, a rasgos generales, Microsoft también presta soporte para SQL Server 2008. Sin embargo, debido a la importante funcionalidad de SAP que se introdujo con SQL Server 2008 R2, SQL Server 2008 R2 es la versión mínima que se requiere para SAP. Tenga en cuenta que SQL Server 2012 y 2014 se ampliaron agregando mayor integración con el escenario de IaaS (como la realización de copias de seguridad directamente en el almacenamiento de Azure). Por lo tanto, el ámbito de este documento se limita a SQL Server 2012 y 2014 con el nivel de revisión más reciente para Azure.
* **Soporte para características de SQL**: se presta soporte para la mayoría de las características de SQL Server en las máquinas virtuales de Microsoft Azure, aunque con algunas excepciones. **No se presta soporte para los clústeres de conmutación por error de SQL Server que usan discos compartidos**.  Las tecnologías distribuidas, como la creación de reflejo de base de datos, los grupos de disponibilidad AlwaysOn, la replicación, el trasvase de registros y Service Broker, se admiten en una única región de Azure. SQL Server AlwaysOn también se admite entre diferentes regiones, como se documenta aquí:  <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Revise la [declaración de soporte](https://support.microsoft.com/kb/956893) para más información. Se muestra un ejemplo de cómo implementar una configuración AlwaysOn en [este][virtual-machines-workload-template-sql-alwayson] artículo. Además, revise los procedimientos recomendados [aquí][virtual-machines-sql-server-infrastructure-services].
* **Rendimiento de SQL**: estamos seguros de que las máquinas virtuales de Microsoft Azure hospedadas funcionarán muy bien en comparación con otras ofertas de virtualización de nube pública, aunque los resultados individuales pueden variar. Consulte [este][virtual-machines-sql-server-performance-best-practices] artículo.
* **Uso de imágenes de Azure Marketplace**: la forma más rápida de implementar una nueva máquina virtual de Microsoft Azure es utilizando una imagen de Azure Marketplace. Hay imágenes en Azure Marketplace que contienen SQL Server. Las imágenes donde ya se ha instalado SQL Server no se pueden utilizar inmediatamente para aplicaciones de SAP NetWeaver. El motivo es que la intercalación de SQL Server predeterminada está instalada en esas imágenes y no la que requieren los sistemas SAP NetWeaver. Para poder usar dichas imágenes, consulte los pasos que se explican en el capítulo [Uso de imágenes de SQL Server fuera de Microsoft Azure Marketplace][dbms-guide-5.6].
* Para más información, consulte [Precios de Azure](https://azure.microsoft.com/pricing/) . La [Guía de licencias de SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) y la de [SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) también constituyen un recurso importante.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Directrices de configuración de SQL Server para SAP relacionadas con las instalaciones de SQL Server en máquinas virtuales de Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendaciones sobre la estructura de máquina virtual y VHD para SAP relacionadas con las implementaciones de SQL Server
Según la descripción general, los archivos ejecutables de SQL Server se deben ubicar o instalar en la unidad del sistema del VHD base de la máquina virtual (unidad C:\).  Normalmente, la carga de trabajo de SAP NetWeaver no utiliza en gran medida la mayoría de las bases de datos de sistema de SQL Server. Por lo tanto, las bases de datos de sistema de SQL Server (master, msdb y model) pueden permanecer también en la unidad C:\. Una excepción podría ser tempdb, que, en el caso de algunas cargas de trabajo de SAP ERP y de todas las de BW, podría requerir un volumen de datos mayor o un volumen de operaciones de E/S que no cumple los requisitos de la máquina virtual original. Para estos sistemas, se deben realizar los pasos siguientes:

* Mueva los archivos de datos de la base de datos tempdb principal a la misma unidad lógica que los archivos de datos principal de la base de datos de SAP.
* Agregue los archivos de datos adicionales de tempdb a cada una del resto de las unidades lógicas que contengan un archivo de datos de la base de datos de usuario de SAP.
* Agregue el archivo de registro de tempdb a la unidad lógica que contiene el archivo de registro de la base de datos de usuario.
* **Solo en los tipos de máquinas virtuales que usan SSD locales** en el nodo de proceso, los archivos de registro y de datos de tempdb podrían colocarse en la unidad D:\. No obstante, se podría recomendar usar varios archivos de datos de tempdb. Tenga en cuenta que los volúmenes de la unidad D:\ serán diferentes en función del tipo de máquina virtual.

Estas configuraciones permiten que tempdb consuma más espacio que el que la unidad del sistema puede proporcionar. Para determinar el tamaño correcto de tempdb, se puede comprobar los tamaños de tempdb en los sistemas existentes que se ejecutan de forma local. Además, dicha configuración admitiría los números de IOPS en la tempdb, que no se pueden proporcionar con la unidad del sistema. De nuevo, los sistemas que se ejecutan de forma local pueden utilizarse para supervisar la carga de trabajo de E/S en tempdb, de forma que puede derivar los números de IOPS que espera observar en tempdb.

Una configuración de máquina virtual que ejecuta SQL Server con una base de datos de SAP donde el archivo de registro de tempdb y el de datos se colocan en la unidad D:\ tendría el siguiente aspecto:

![Configuración de referencia de máquinas virtuales IaaS de Azure en SAP][dbms-guide-figure-300]

Tenga en cuenta que la unidad D:\ tiene distintos tamaños en función del tipo de máquina virtual. Según los requisitos de tamaño de tempdb, podría verse obligado a emparejar los archivos de registro y de datos de tempdb con los de la base de datos de SAP en aquellos casos donde el tamaño de la unidad D:\ sea demasiado pequeño.

#### <a name="formatting-the-vhds"></a>Formateo de los VHD
Para SQL Server, el tamaño de bloque NTFS de los VHD que contienen los archivos de registro y de datos de SQL Server deben tener un tamaño de 64 kB. No hay que formatear la unidad D:\, ya que este proceso se ha realizado previamente.

Para asegurarse de que la restauración o la creación de bases de datos no inicialice los archivos de datos llenando con ceros el contenido de los archivos, hay que asegurarse de que el servicio de SQL Server que se ejecuta en el contexto de usuario tiene un permiso determinado. Normalmente, los usuarios del grupo de administrador de Windows tienen estos permisos. Si el servicio SQL Server se ejecuta en el contexto de usuario del usuario no administrador de Windows, debe asignar a ese usuario el derecho de usuario Realizar tareas de mantenimiento del volumen.  Consulte los detalles en este artículo de Microsoft Knowledge Base: <https://support.microsoft.com/kb/2574695>.

#### <a name="impact-of-database-compression"></a>Impacto de la compresión de las bases de datos
En configuraciones donde el ancho de banda de E/S puede convertirse en un factor de limitación, todas las medidas que reduzca el número de IOPS podrían ayudar a ajustar la carga de trabajo que se puede ejecutar en un escenario de IaaS como Azure. Por lo tanto, si aún no lo ha hecho, se recomienda encarecidamente que Microsoft y SAP apliquen la compresión de página de SQL Server antes de cargar bases de datos de SAP existentes en Azure.

La recomendación para realizar la compresión de la base de datos antes de cargar en Azure viene motivada por dos razones:

* La cantidad de datos que se carga es menor.
* La duración de la ejecución de la compresión es más breve dándose por supuesto que se puede utilizar hardware más eficaz con más CPU, mayor ancho de banda de E/S o menos latencia de E/S en un entorno local.
* Si se utilizan tamaños de base de datos más pequeños, se incurriría en menos costes de asignación de disco.

La compresión de las bases de datos funciona también correctamente en las máquinas virtuales de Azure de la misma forma que en local. Para más información sobre cómo comprimir una base de datos SQL Server existente para SAP, consulte aquí: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>.

### <a name="sql-server-2014--storing-database-files-directly-on-azure-blog-storage"></a>SQL Server 2014: almacenamiento de archivos directamente en Almacenamiento de blobs de Azure
SQL Server 2014 ofrece la posibilidad de almacenar archivos de base de datos directamente en el almacén de blobs de Azure sin necesidad de usar contenedor de un VHD en ellos. Especialmente al utilizar el almacenamiento estándar de Azure o tipos de máquina virtual con tamaños más pequeños, se producen escenarios donde pueden superarse los límites de IOPS impuestos por un número de limitado de VHD que pueden montarse en tipos de máquinas virtuales de menor tamaño. Aunque esto funciona con las bases de datos de usuario, no lo hace con las de sistema de SQL Server. También funciona con los archivos de registro y de datos de SQL Server. Si desea implementar una base de datos de SQL Server de SAP de este modo, en lugar de contenerla en VHD, tenga en cuenta lo siguiente:

* La cuenta de almacenamiento utilizada debe estar en la misma región de Azure que la que se empleó para implementar la máquina virtual que se está ejecutando en SQL Server.
* Para este método de implementaciones también se aplican las consideraciones que se indicaron anteriormente relacionadas con la distribución de los VHD en diferentes cuentas de almacenamiento de Azure. Significa que el número de operaciones de E/S cuenta para los límites de la cuenta de almacenamiento de Azure.

[comment]: <> (MSSedusch TODO But this will use network bandwith and not storage bandwith, doesn't it?)

Los detalles acerca de este tipo de implementación se indican aquí: <https://msdn.microsoft.com/library/dn385720.aspx>.

Para almacenar archivos de datos de SQL Server directamente en Azure Premium Storage, debe tener una versión mínima de revisión de SQL Server 2014 que se explica en este artículo: <https://support.microsoft.com/kb/3063054>. El almacenamiento de archivos de datos de SQL Server en el almacenamiento estándar de Azure funcionan con la versión de lanzamiento de SQL Server 2014. Sin embargo, las mismas revisiones contienen otra serie de correcciones que hacen que sea más confiable usar directamente Almacenamiento de blobs de Azure para copias de seguridad y archivos de datos de SQL Server. Por lo tanto, en general, se recomienda usar estas revisiones.

### <a name="sql-server-2014-buffer-pool-extension"></a>Extensión de grupo de búferes de SQL Server 2014
SQL Server 2014 introdujo una nueva característica denominada "extensión de grupo de búferes". Esta funcionalidad amplía el grupo de búferes de SQL Server que se mantiene en memoria con una memoria caché de segundo nivel respaldada por SSD locales de un servidor o una máquina virtual. De este modo, se puede mantener en memoria un conjunto de datos de mayor volumen. En comparación con el acceso al almacenamiento estándar de Azure, el acceso a la extensión de grupo de búferes que se almacena en SSD locales de una máquina virtual de Azure es mucho más rápido.  Por lo tanto, utilizar la unidad D:\ local de los tipos de máquina virtual que tienen un rendimiento y un número de IOPS excelentes podría ser una forma muy razonable de reducir la carga de IOPS en Almacenamiento de Azure y de mejorar considerablemente los tiempos de respuesta de las consultas. Esto sucede sobre todo cuando no se utiliza el Almacenamiento premium. En el caso del Almacenamiento premium y el uso de la memoria caché de lectura de Azure premium en el nodo de proceso, tal y como se recomienda para los archivos de datos, no se producen resultados muy diferentes. El motivo es que las memorias caché (extensión de grupo de búferes de SQL Server y memoria caché de lectura de almacenamiento premium) utilizan los discos locales de los nodos de proceso.
Para más información sobre esta funcionalidad, consulte esta documentación: <https://msdn.microsoft.com/library/dn133176.aspx>.

### <a name="backuprecovery-considerations-for-sql-server"></a>Consideraciones de copia de seguridad y recuperación en SQL Server
Al implementar SQL Server en Azure, se debe revisar la metodología de copia de seguridad. Aunque no se trate de un sistema productivo, debe realizarse de manera periódica una copia de seguridad de la base de datos de SAP hospedada por SQL Server. Como el almacenamiento de Azure mantiene tres imágenes, una copia de seguridad es menos importante para compensar un bloqueo de almacenamiento. El principal motivo para mantener un plan de copia de seguridad y recuperación adecuado es más que poder compensar los errores lógicos o manuales proporcionando funcionalidades de recuperación a un momento dado. El objetivo es usar las copias de seguridad para restaurar la base de datos a un momento dado o utilizar las copias de seguridad de Azure para propagar otro sistema mediante la copia de la base de datos existente. Por ejemplo, podría transferir archivos desde una configuración de SAP de 2 niveles a una configuración de sistema de 3 niveles del mismo sistema mediante la restauración de una copia de seguridad.

Hay tres formas diferentes de realizar la copia de seguridad de SQL Server en el almacenamiento de Azure:

1. SQL Server 2012 CU4 y posterior puede realizar copias de seguridad de forma nativa de las bases de datos en una dirección URL. Esto se detalla en la entrada de blog [New functionality in SQL Server 2014 – Part 5 – Backup/Restore Enhancements](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)(Nueva funcionalidad en SQL Server 2014 [parte 5]: mejoras en los procesos de copia de seguridad y restauración). Consulte el capítulo [SQL Server 2012 SP1 CU4 y versiones posteriores][dbms-guide-5.5.1].
2. Las versiones de SQL Server anteriores a SQL 2012 CU4 pueden utilizar la funcionalidad de redirección para realizar copias de seguridad en un VHD y, básicamente, mover la secuencia de escritura a una ubicación de almacenamiento de Azure que se haya configurado. Consulte el capítulo [SQL Server 2012 SP1 CU3 y versiones anteriores][dbms-guide-5.5.2].
3. El método final consiste en realizar una copia de seguridad convencional de SQL Server en disco en un dispositivo de VHD.  Se trata de un proceso idéntico al patrón de implementación local y no se explica detalladamente en este documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 y versiones posteriores
Esta funcionalidad permite realizar directamente copias de seguridad en el almacenamiento de blobs de Azure. Sin este método, debe realizar las copias de seguridad en otros VHD de Azure, lo que consumiría la capacidad de IOPS y de los VHD. La idea es básicamente la siguiente:

 ![Uso de copias de seguridad de SQL Server 2012 para blobs de Almacenamiento de Microsoft Azure][dbms-guide-figure-400]

En este caso, la ventaja es que no se necesita utilizar VHD para almacenar las copias de seguridad de SQL Server. Por tanto, tendrá menos VHD asignados y el ancho de banda total de IOPS de los VHD se podrá usar para los archivos de datos y de registro. Tenga en cuenta que el tamaño máximo de una copia de seguridad se limita a un máximo de 1 TB, tal como se describe en la sección "Limitaciones" de este artículo: <https://msdn.microsoft.com/library/dn435916.aspx#limitations>. Si el tamaño de la copia de seguridad, a pesar de usar la compresión de copia de seguridad de SQL Server, es superior a 1 TB, debe usarse la funcionalidad que se describe en el capítulo [SQL Server 2012 SP1 CU3 y versiones anteriores][dbms-guide-5.5.2].

[En la documentación relacionada](https://msdn.microsoft.com/library/dn449492.aspx) donde se describe el proceso de restauración de las bases de datos a partir de copias de seguridad en el almacén de blobs de Azure, se recomienda no llevar a cabo la restauración directamente desde el almacén de blobs de Azure si el tamaño de las copias de seguridad es superior a 25 GB. La recomendación de este artículo se basa simplemente en las consideraciones de rendimiento y no en las restricciones funcionales. Por lo tanto, pueden aplicarse distintas condiciones en cada caso.

Puede encontrar documentación sobre cómo configurar y utilizar este tipo de copia de seguridad en [este](https://msdn.microsoft.com/library/dn466438.aspx) tutorial.

[Aquí puede](https://msdn.microsoft.com/library/dn435916.aspx)consultar un ejemplo de secuencia de pasos.

Al automatizar las copias de seguridad, la prioridad es asegurarse de que los blobs de cada copia de seguridad se denominen de forma distinta. De lo contrario, se sobrescribirán y se romperá la cadena.

Para no mezclar elementos entre los 3 tipos de copias de seguridad diferentes, se recomienda crear distintos contenedores en la cuenta de almacenamiento utilizada para las copias de seguridad. Los contenedores podrían ser solo por tipo de máquina virtual o por tipo de copia de seguridad. El esquema podría ser similar al siguiente:

 ![Uso de copias de seguridad de SQL Server 2012 para blobs de Almacenamiento de Microsoft Azure: contenedores diferentes en distintas cuentas de almacenamiento][dbms-guide-figure-500]

En el ejemplo anterior, las copias de seguridad no se realizarían en la misma cuenta de almacenamiento donde se implementan las máquinas virtuales. Sería en una nueva cuenta de almacenamiento creada específicamente para las copias de seguridad. En las cuentas de almacenamiento, habría diferentes contenedores creados con una matriz del tipo de copia de seguridad y el nombre de la máquina virtual. Gracias a esta segmentación, se podrán administrar las copias de seguridad de las diferentes máquinas virtuales de manera más sencilla.

Los blobs en el que se escriben directamente las copias de seguridad no cuentan para el número de VHD de una máquina virtual. Por lo tanto, se podría maximizar la cantidad máxima de VHD montados de la SKU de máquina virtual específico del archivo de datos y de registro de transacciones, así como seguir ejecutando una copia de seguridad en un contenedor de almacenamiento.

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 y versiones anteriores
El primer paso que se debe realizar para conseguir que una copia de seguridad se realice directamente en Almacenamiento de Azure es descargar el archivo .msi en [este](https://www.microsoft.com/download/details.aspx?id=40740) artículo de Microsoft Knowledge Base.

Descargue la documentación y el archivo de instalación para arquitecturas x64. El archivo instalará un programa llamado: "Herramienta de copia de seguridad de Microsoft SQL Server en Microsoft Azure". Lea detenidamente la documentación del producto.  Básicamente, la herramienta funciona de la siguiente manera:

* Desde SQL Server, se define una ubicación de disco para la copia de seguridad de SQL Server (no utilice la unidad D:\ para este fin).
* La herramienta permitirá definir reglas que se pueden usar para dirigir diferentes tipos de copias de seguridad a distintos contenedores de almacenamiento de Azure.
* Una vez que se hayan definido las reglas, la herramienta redirigirá la secuencia de escritura de la copia de seguridad a uno de los VHD y discos de la ubicación de almacenamiento de Azure que se definió anteriormente.
* La herramienta dejará un archivo de código auxiliar de un tamaño reducido (muy pocos kB) en el VHD o disco definido para la copia de seguridad de SQL Server. **Este archivo debe mantenerse en la ubicación de almacenamiento, ya que es necesario para volver a realizar la restauración desde el almacenamiento de Azure.**
  * Si ha perdido el archivo de código auxiliar (por ejemplo, tras la pérdida de los medios de almacenamiento que contienen dicho archivo) y ha elegido la opción de realizar las copias de seguridad en una cuenta de almacenamiento de Microsoft Azure, puede recuperar el archivo de código auxiliar mediante el almacenamiento de Microsoft Azure descargándolo desde el contenedor de almacenamiento donde se colocó. Después, debe colocar el archivo de código auxiliar en una carpeta de la máquina local donde se haya configurado la herramienta para detectar el mismo contenedor y cargarse en él con la misma contraseña de cifrado si se empleó cifrado con la regla original.

Es decir, el esquema tal y como se describió anteriormente para las versiones más recientes de SQL Server puede colocarse también para las versiones de SQL Server que no pueden redirigirse directamente a una ubicación de almacenamiento de Azure.

Este método no debe usarse con las versiones más recientes de SQL Server que admiten copias de seguridad nativas en Almacenamiento de Azure. Las excepciones se producen cuando las limitaciones de la copia de seguridad nativa en Azure bloquean la ejecución del proceso de copia de seguridad nativa en Azure.

#### <a name="other-possibilities-to-backup-sql-server-databases"></a>Otras posibilidades para realizar copias de seguridad de bases de datos de SQL Server
Otras posibilidades realizar copias de seguridad de bases de datos consisten en asociar VHD adicionales a una máquina virtual que se utilice para almacenar las copias de seguridad. En este caso, tendría que asegurarse de que los VHD no se estén ejecutando por completo. Si lo están haciendo, necesitaría desmontar el VHD y "archivarlo" y remplazarlo por un nuevo VHD vacío. Si va a tomar esta alternativa, le recomendamos mantener estos VHD en cuentas de almacenamiento de Azure independientes de las de los VHD con los archivos de base de datos.

Una segunda posibilidad consiste en usar una máquina virtual de gran tamaño que puede tener muchos VHD asociados. Por ejemplo, D14 con 32 VHD. Utilice espacios de almacenamiento para crear un entorno flexible donde puede crear recursos compartidos que se utilicen como destinos de copia de seguridad de los diferentes servidores de DBMS.

También puede consultar algunos procedimientos recomendados [aquí](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) .

#### <a name="performance-considerations-for-backupsrestores"></a>Consideraciones de rendimiento sobre copias de seguridad y restauraciones
Como en las implementaciones sin sistema operativo, el rendimiento de las copias de seguridad y las restauraciones depende de cuántos volúmenes puedan leerse en paralelo y el rendimiento que estos volúmenes puedan tener. Además, el consumo de CPU que se emplea en la compresión de copias de seguridad puede desempeñar un papel importante en las máquinas virtuales con solo 8 subprocesos de CPU como máximo. Por lo tanto, se pueden asumir los siguientes puntos:

* Cuantos menos VHD se utilicen para almacenar los dispositivos de las bases de datos, menor será el rendimiento general de lectura.
* Cuantos menos subprocesos de CPU haya en la VM, mayor será el impacto de la compresión de copias de seguridad.
* Cuantos menos destinos (blobs o VHD) en los que escribir la copia de seguridad haya, menor será el rendimiento.
* Cuanto menos tamaño tenga la máquina virtual, menor será la cuota de rendimiento de almacenamiento para realizar operaciones de escritura y lectura en el almacenamiento de Azure. Con independencia de si las copias de seguridad se almacenan directamente en Blob de Azure o en VHD que se almacenan de nuevo en Blobs de Azure.

Cuando se usa un blob de almacenamiento de Microsoft Azure como destino de copia de seguridad en las versiones más recientes, solo podrá designar una dirección URL de destino para cada copia de seguridad específica.

No obstante, al utilizar la Herramienta de copia de seguridad de Microsoft SQL Server en Microsoft Azure en las versiones anteriores, puede definir más de un destino de archivo. Con más de un destino, puede escalar la copia de seguridad y el rendimiento de esta operación será mayor. De esta forma, se realizarían copias de seguridad de varios archivos y de la cuenta de almacenamiento de Azure. En nuestras pruebas, pudimos alcanzar definitivamente el rendimiento deseado con las extensiones de copia de seguridad implementadas en SQL Server 2012 SP1 CU4 mediante el uso de varios destinos de archivos. Asimismo, no se produce el bloqueo del límite de 1 TB propio de la copia de seguridad nativa en Azure.

Sin embargo, tenga en cuenta que el rendimiento también depende de la ubicación de la cuenta de almacenamiento de Azure que utilice para la copia de seguridad. Como recomendación, podría ubicar la cuenta de almacenamiento en una región distinta que donde se ejecutan las máquinas virtuales. Por ejemplo, ejecutaría la configuración de máquina virtual en Europa Occidental, pero colocaría la cuenta de almacenamiento que utiliza para realizar la copia de seguridad en Europa del Norte. Sin duda, esto afectará al rendimiento de la copia de seguridad y es probable que no se obtenga un rendimiento de 150 MB/s como parece ser posible en aquellos casos donde el almacenamiento de destino y las máquinas virtuales se ejecutan en el mismo centro de datos regional.

#### <a name="managing-backup-blobs"></a>Gestión de blobs de copia de seguridad
Para administrar las copias de seguridad por su cuenta, tendrá que cumplir un requisito. Puesto que se espera que van a crearse muchos blobs mediante la ejecución de copias de seguridad del registro de transacciones, la administración de esos blobs puede sobrecargar fácilmente el Portal de Azure. Por lo tanto, se recomienda utilizar un explorador de almacenamiento de Azure. Hay disponibles algunos muy buenos que pueden contribuir a administrar una cuenta de almacenamiento de Azure.

* Microsoft Visual Studio con Azure SDK instalado (<https://azure.microsoft.com/downloads/>)
* Explorador de Microsoft Azure Storage (<https://azure.microsoft.com/downloads/>)
* Herramientas de terceros

[comment]: <> (Todavía no se admite en ARM)
[comment]: <> (#### Azure VM backup)
[comment]: <> (Se puede efectuar una copa de seguridad de las máquinas virtuales dentro del sistema SAP mediante la funcionalidad de copia de seguridad de máquinas virtuales de Azure. La funcionalidad de copia de seguridad de máquina virtual de Azure se introdujo a principios de 2015; se trata de un método estándar para realizar copias de seguridad de una máquina virtual completa en Azure. Azure Backup almacena las copias de seguridad en Azure y permite volver a restaurarlas.)
[comment]: <> (Se puede realizar una copia de seguridad de las máquinas virtuales que ejecutan bases de datos de forma coherente si los sistemas de DBMS admiten Windows VSS (Servicio de instantáneas de volumen de Windows) como hace SQL Server, por ejemplo. Por tanto, usar la funcionalidad de copia de seguridad de máquina virtual de Azure podría ser una forma de obtener una copia de seguridad de una base de datos de SAP que puede restaurarse. Sin embargo, tenga en cuenta que no se pueden realizar restauraciones de bases de datos a un momento dado con esta funcionalidad. Por lo tanto, se recomienda realizar copias de seguridad de las bases de datos con la funcionalidad del DBMS en lugar de confiar en la copia de seguridad de máquinas virtuales de Azure.)
[comment]: <> (Para familiarizarse con la copia de seguridad de la máquina virtual de Azure, comience aquí: <https://azure.microsoft.com/documentation/services/backup/>)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Uso de imágenes de SQL Server fuera de Microsoft Azure Marketplace
Microsoft ofrece en Azure Marketplace máquinas virtuales que ya contienen versiones de SQL Server. Para los clientes de SAP que requieran licencias de SQL Server y Windows, podría tratarse de una oportunidad para cubrir la necesidad de licencias activando las máquinas virtuales con SQL Server ya está instalado. Para poder utilizar dichas imágenes para SAP, deben tenerse en cuenta las siguientes consideraciones:

* Las versiones de SQL Server que no son evaluación incurren en costes más elevados que simplemente una máquina virtual exclusivamente con Windows implementada desde Azure Marketplace. Consulte los siguientes artículos para comparar los precios: <https://azure.microsoft.com/pricing/details/virtual-machines/> y <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql>.
* Solo se pueden usar las versiones de SQL Server compatibles con SAP, como SQL Server 2012.
* La intercalación de la instancia de SQL Server que está instalada en las máquinas virtuales que se ofrecen en Azure Marketplace no es la que requiere SAP NetWeaver para ejecutar la instancia de SQL Server. Puede cambiar la intercalación, aunque debe seguir las instrucciones de la sección que encontrará a continuación.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Modificación de la intercalación de SQL Server de una máquina virtual de Windows o SQL Server
Puesto que las imágenes de SQL Server en Azure Marketplace no están configuradas para usar la intercalación necesaria para las aplicaciones de SAP NetWeaver, debe modificarse inmediatamente después de llevar a cabo la implementación. Para SQL Server 2012, este procedimiento puede realizarse siguiendo estos pasos en cuanto se haya implementado la máquina virtual y un administrador puede iniciar sesión en la máquina virtual implementada:

* Abra una ventana de comandos de Windows como administrador.
* Cambie el directorio a C:\Archivos de programa\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Ejecute el comando: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2.   
  * `<local_admin_account_name`> es la cuenta que se definió como cuenta de administrador al implementar la máquina virtual por primera vez por medio de la galería.

Este proceso solo debe llevar unos minutos. Para asegurarse de que obtiene el resultado correcto, realice los pasos siguientes:

* Abra SQL Server Management Studio.
* Abra una ventana de consulta.
* Ejecute el comando sp_helpsort en la base de datos maestra de SQL Server.

El resultado deberá ser similar al que se muestra a continuación:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Si no obtiene lo mismo, DETENGA la implementación de SAP e investigue por qué el comando de instalación no funcionó como se esperaba. **NO** se admite la implementación de aplicaciones de SAP NetWeaver en la instancia de SQL Server con páginas de códigos de SQL Server distintas de la mencionada antes.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>Alta disponibilidad de SQL Server para SAP en Azure
Tal y como se mencionó anteriormente en este documento, no hay ninguna posibilidad de crear un almacenamiento compartido que sea necesario para utilizar la funcionalidad de alta disponibilidad de SQL Server más antigua. Esta funcionalidad instala 2 o más instancias de SQL Server en un clúster de conmutación por error de Windows Server (WSFC) utilizando un disco compartido para las bases de datos de usuario (y, al final, tempdb). Se trata del método estándar de alta disponibilidad que también es compatible con SAP. Dado que Azure no admite el almacenamiento compartido, no se pueden realizar configuraciones de alta disponibilidad de SQL Server con una configuración de clúster de disco compartido. Sin embargo, podemos seguir utilizando muchos otros métodos de alta disponibilidad, que se describen en las secciones siguientes.

[comment]: <> (El artículo sigue refiriéndose a ASM)
[comment]: <> (Antes de leer las distintas tecnologías específicas de alta disponibilidad que se pueden usar con SQL Server en Azure, hay un documento muy bueno que proporciona más información y punteros [aquí][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions])

#### <a name="sql-server-log-shipping"></a>Trasvase de registros de SQL Server
Uno de los métodos de alta disponibilidad (HA) consiste en trasvasar los registros de SQL Server. Si las máquinas virtuales que participan en la configuración de alta disponibilidad tienen una resolución de nombres correcta, no habrá ningún problema y la configuración de Azure no será diferente a cualquier otra realizada de forma local. No se recomienda depender exclusivamente de la resolución de direcciones IP. En lo que respecta a la configuración de trasvase de registros y sus principios, consulte esta documentación:

<https://technet.microsoft.com/library/ms187103.aspx>

Para poder lograr realmente la alta disponibilidad, hay que implementar las máquinas virtuales que estén en una configuración de trasvase de registros que, a su vez, se encuentre en el mismo conjunto de disponibilidad de Azure.

#### <a name="database-mirroring"></a>Creación de reflejo de la base de datos
La funcionalidad de creación de reflejo de la base de datos, que es compatible con SAP (consulte la nota de SAP [965908]), se basa en la definición de un asociado de conmutación por error en la cadena de conexión de SAP. Para los casos entre locales, asumimos que las 2 máquinas virtuales están en el mismo dominio y que las 2 instancias de SQL Server que se están ejecutando en el contexto de usuario son también los usuarios de dominio, así como que tendrán los privilegios suficientes en las 2 instancias de SQL Server. Por lo tanto, el proceso configuración de la creación de reflejo de la base de datos de Azure es el mismo en una configuración o instalación local típica.

Al igual que con las implementaciones exclusivas en la nube, el método más fácil es tener otra configuración de dominio en Azure con el fin de albergar esas máquinas virtuales de DBMS (e, idealmente, las máquinas virtuales de SAP específicas) dentro de un dominio.

Si un dominio no es posible, también se pueden usar certificados para los puntos de conexión de reflejo de la base de datos, como se describe aquí: <https://technet.microsoft.com/library/ms191477.aspx>.

Se puede encontrar un tutorial para la configuración del reflejo de base de datos en Azure aquí: <https://technet.microsoft.com/library/ms189852.aspx>.

#### <a name="alwayson"></a>AlwaysOn
Como AlwaysOn es compatible con un entorno de SAP local (consulte la nota de SAP [1772688]), se puede usar en combinación con SAP en Azure. El hecho de que no pueda crear discos compartidos en Azure no significa que no se pueda crear una configuración AlwaysOn de clúster de conmutación por error de Windows Server (WSFC) entre diferentes máquinas virtuales. Tan solo significa que no tiene la posibilidad de usar un disco compartido como un cuórum en la configuración del clúster. Por lo tanto, puede crear una configuración AlwaysOn de WSFC en Azure y simplemente no seleccionar el tipo de cuórum que utiliza el disco compartido. El entorno de Azure en el que se han implementado esas máquinas virtuales debe resolver las máquinas virtuales por nombre y estas deben encontrarse en el mismo dominio. Lo mismo ocurre con las implementaciones entre locales y exclusivas de Azure. Hay algunas consideraciones especiales respecto a implementar el agente de escucha de grupo de disponibilidad de SQL Server (no debe confundirse con el conjunto de disponibilidad de Azure), ya que Azure en este momento no puede crear un objeto AD y DNS como en las implementaciones locales. Por lo tanto, hay que realizar otros pasos de instalación para solucionar el comportamiento específico de Azure.

Estas son algunas de las consideraciones que hay que tener en cuenta al usar un agente de escucha de grupo de disponibilidad:

* Solo se puede usar un agente de escucha del grupo de disponibilidad con Windows Server 2012 o Windows Server 2012 R2 como SO invitado de la máquina virtual. Para Windows Server 2012 debe asegurarse de que se aplica esta revisión: <https://support.microsoft.com/kb/2854082>.
* Esta revisión no está disponible para Windows Server 2008 R2 y AlwaysOn tendría que usarse de la misma manera que la funcionalidad de creación de reflejo de base de datos especificando un asociado de conmutación por error en la cadena de conexiones (se realiza mediante el parámetro de SAP default.pfl dbs/mss/server; consulte la nota de SAP [965908]).
* Cuando se utiliza un agente de escucha de grupo de disponibilidad, las máquinas virtuales de la base de datos tienen que estar conectadas a un equilibrador de carga específico. La funcionalidad de resolución de nombres de las implementaciones exclusivas en la nube precisaría que todas las máquinas virtuales de un sistema SAP (servidores de aplicaciones, el servidor de DBMS y el servidor ASCS) se encuentren en la misma red virtual, o bien que se realizara un mantenimiento del archivo etc\host en una capa de aplicación de SAP para obtener resueltos los nombres de las máquinas virtuales de SQL Server. Para evitar que Azure asigne nuevas direcciones IP en casos donde ambas máquinas virtuales se apaguen accidentalmente, se deben asignar direcciones IP estáticas a las interfaces de red de esas máquinas virtuales en la configuración AlwaysOn (el proceso definición de una dirección IP estática se describe en [este][virtual-networks-reserved-private-ip] artículo).

[comment]: <> (Blogs anteriores)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>)
* Hay que realizar algunos pasos especiales al crear la configuración del clúster WSFC: el clúster necesita una dirección IP especial, ya la funcionalidad actual de Azure asignaría el nombre del clúster a la misma dirección IP que el nodo donde se ha creado dicho clúster. Es decir, se debe realizar un paso manual para asignar una dirección IP diferente a la del clúster.
* El agente de escucha de grupo de disponibilidad se va a crear en Azure con los puntos de conexión TCP/IP asignados a las máquinas virtuales que ejecutan las réplicas principales y secundarias del grupo de disponibilidad.
* Puede que haya que proteger estos puntos de conexión con ACL.

[comment]: <> (Blog antiguo PENDIENTE)
[comment]: <> (Los pasos detallados y las necesidades de instalar una configuración AlwaysOn en Azure se realizan de mejor manera cuando se sigue el tutorial disponible [aquí][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (La configuración AlwaysOn preconfigurada a través de la galería de Azure <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (La creación de un agente de escucha del grupo de disponibilidad se describe mejor en [este][virtual-machines-windows-classic-ps-sql-int-listener] tutorial)
[comment]: <> (Los puntos de conexión de red de protección con ACL se explican mejor aquí:)
[comment]: <> (*    <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>)

Es posible implementar un grupo de disponibilidad AlwaysOn de SQL Server en distintas regiones de Azure también. Esta funcionalidad aprovechará la conectividad de red virtual a red virtual de Azure ([más información][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Blog antiguo PENDIENTE)
[comment]: <> (La configuración de los grupos de disponibilidad AlwaysOn de SQL Server en tal escenario se describe aquí: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.)

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Resumen de alta disponibilidad de SQL Server en Azure
Como el almacenamiento de Azure está protegiendo el contenido, hay una razón menos para insistir en el uso de una imagen de espera activa. Es decir, el escenario de alta disponibilidad solo tiene que protegerse contra los siguientes casos:

* La no disponibilidad de la máquina virtual como un conjunto debido a tareas de mantenimiento en el clúster del servidor de Azure u otros motivos
* Problemas de software en la instancia de SQL Server
* Protección contra el error manual donde se eliminan los datos y se necesita realizar una recuperación a un momento dado

Al analizar las tecnologías que se ajustan a los requisitos, se puede argumentar que los dos primeros casos se pueden cubrir mediante la funcionalidad de creación de reflejo de base de datos o AlwaysOn, mientras que el tercer caso solo se puede abordar con el trasvase de registros.

Tendrá que equilibrar la configuración más compleja de AlwaysOn, en comparación con la creación de reflejo de base de datos, con las ventajas de AlwaysOn. Estas ventajas son las siguientes:

* Réplicas secundarias legibles
* Copias de seguridad de las réplicas secundarias
* Mejor escalabilidad
* Más de una réplica secundaria legible

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Resumen general de SQL Server para SAP en Azure
En esta guía se ofrecen muchas recomendaciones: le sugerimos que la lea más de una vez antes de planear la implementación de Azure. En general, asegúrese de seguir las 10 principales pautas específicas de DBMS en Azure:

[comment]: <> (2.3 higher throughput than what? ¿Que un disco duro virtual?)
1. Utilice la versión de DBMS más reciente, como SQL Server 2014, que presenta la mayoría de las ventajas de Azure. Para SQL Server, se trata de SQL Server 2012 SP1 CU4, que incluiría la característica de copia de seguridad en el almacenamiento de Azure. Sin embargo, para usarse con SAP recomendamos, como mínimo, SQL Server 2014 SP1 CU1 o SQL Server 2012 SP2 y la CU más reciente.
2. Planee cuidadosamente su infraestructura de sistema SAP en Azure para equilibrar el diseño del archivo de datos y las restricciones de Azure:
   * No disponga de un número de VHD demasiado elevado, solo el suficiente para asegurarse de que puede alcanzar los IOPS necesarios.
   * Recuerde que los valores de E/S también están limitados por cuenta de Azure Storage y que las cuentas de Storage están limitadas en cada suscripción de Azure ([más información][azure-subscription-service-limits]).
   * Si necesita obtener un mayor rendimiento, solo cree secciones en los VHD.
3. Nunca instale software o coloque los archivos que requieren persistencia en la unidad D:\, ya que no es permanente y todos los datos que albergue se perderán tras reiniciar Windows.
4. No utilice el almacenamiento en caché de VHD de Azure para el almacenamiento estándar de Azure.
5. No utilice cuentas de almacenamiento con replicación geográfica de Azure.  Use la redundancia local para las cargas de trabajo de DBMS.
6. Utilice la solución de alta disponibilidad o recuperación ante desastres de su proveedor de DBMS para replicar datos de base de datos.
7. Use siempre la resolución de nombres, no confíe exclusivamente en las direcciones IP.
8. Use el nivel de compresión de base de datos más elevado posible. Para SQL Server es la compresión de página.
9. Tenga cuidado al utilizar imágenes de SQL Server de Azure Marketplace. Si lo hace, debe cambiar la intercalación de la instancia antes de instalar cualquier sistema SAP NetWeaver en ella.
10. Instale y configure la funcionalidad de supervisión de hosts de SAP para Azure tal y como se describe en la [Guía de implementación][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Características específicas de SAP ASE en Windows
Desde Microsoft Azure puede migrar fácilmente sus aplicaciones de SAP ASE a máquinas virtuales de Azure. SAP ASE en una máquina virtual permite reducir el coste total de propiedad de implementación, administración y mantenimiento de aplicaciones empresariales al migrarlas fácilmente a Microsoft Azure. Con SAP ASE en una máquina virtual de Azure, los administradores y los desarrolladores pueden seguir usando las mismas herramientas de desarrollo y administración que se encuentran disponibles de forma local.

Hay un Acuerdo de Nivel de Servicio para Azure Virtual Machines que puede encontrarse aquí: <https://azure.microsoft.com/support/legal/sla>.

Garantizamos que las máquinas virtuales de Microsoft Azure hospedadas funcionarán muy bien en comparación con otras ofertas de virtualización de nube pública, aunque los resultados individuales pueden variar. Los números de SAPS de tamaño de SAP de las distintas SKU para máquinas virtuales certificadas por SAP se proporcionarán en una nota de SAP independiente [1928533].

Se aplican instrucciones y recomendaciones sobre el uso del almacenamiento de Azure, implementación de máquinas virtuales de SAP o supervisión de SAP a las implementaciones de SAP ASE en conjunto con aplicaciones de SAP, como se indica en los primeros cuatro capítulos de este documento.

### <a name="sap-ase-version-support"></a>Compatibilidad de versiones de SAP ASE
Actualmente, SAP admite la versión 16.0 de SAP ASE para poder utilizarse con productos de SAP Business Suite. Todas las actualizaciones para el servidor SAP ASE o los controladores ODBC y JDBC que se utilizarán con productos de SAP Business Suite se proporcionan únicamente a través de SAP Service Marketplace: <https://support.sap.com/swdc>.

En el caso de las instalaciones realizadas de forma local, no descargue las actualizaciones del servidor de SAP ASE ni de los controladores JDBC y ODBC directamente desde los sitios web de Sybase. Para obtener más información sobre las revisiones que pueden utilizarse con los productos de SAP Business Suite en el entorno local y en máquinas virtuales de Azure, consulte las siguientes notas de SAP:

* [1590719]
* [1973241]

Encontrará información general sobre la ejecución de SAP Business Suite en SAP ASE en el [SCN](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Instrucciones de configuración de SAP ASE para SAP relacionadas con las instalaciones de SAP ASE en máquinas virtuales de Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estructura de la implementación de SAP ASE
Según la descripción general, los archivos ejecutables de SAP ASE se deben ubicar o instalar en la unidad del sistema del VHD base de la máquina virtual (unidad C:\)). Normalmente, la mayoría de las bases de datos de herramientas y sistemas SAP ASE no se aprovechan realmente bien mediante las cargas de trabajo de SAP NetWeaver. Por lo tanto, las bases de datos de herramientas y sistemas (master, model, saptools, sybmgmtdb, sybsystemdb) pueden permanecer en la unidad C:\.

Una excepción puede ser la base de datos temporal que contiene todas las tablas de trabajo y temporales que SAP ASE creó, que, en el caso de algunos SAP ERP y todas las cargas de trabajo de BW, podría requerir un mayor volumen de datos o de operaciones de E/S que no quepan en el VHD base de la máquina virtual original (unidad c:\).

Según la versión de SAPInst/SWPM utilizada para instalar el sistema, la base de datos puede contener los siguientes elementos:

* Una única tempdb de SAP ASE que se crea al instalar SAP ASE
* Una tempdb de SAP ASE creada al instalar SAP ASE y un saptempdb adicional creado en la rutina de instalación de SAP
* Una tempdb de SAP ASE creada al instalar SAP ASE y una tempdb adicional que se creó manualmente (por ejemplo, según la nota de SAP [1752266]) para cumplir los requisitos de tempdb específicos de ERP/BW

En el caso de las cargas de trabajo específicas de ERP o de todas las de BW, tiene sentido, en lo que respecta al rendimiento, mantener los dispositivos de tempdb de la tempdb que se creó de forma adicional (mediante SWPM o manualmente) en una unidad distinta de C:\. Si no existe ninguna tempdb adicional, se recomienda crear una (Nota de SAP [1752266]).

En estos sistemas, se deben seguir estos pasos para la tempdb que se creó de forma adicional:

* Mueva el primer dispositivo de tempdb al primero de la base de datos de SAP.
* Agregue dispositivos de tempdb a cada uno de los VHD que contengan un dispositivo de archivos de la base de datos de SAP.

Esta configuración permite que tempdb consuma más espacio que el que la unidad del sistema puede proporcionar. Como referencia, se pueden comprobar los tamaños de los dispositivos de tempdb en los sistemas existentes que se ejecutan de forma local. De lo contrario, dicha configuración admitiría los números de IOPS en la tempdb, que no se pueden proporcionar con la unidad del sistema. Reiteramos que los sistemas que se ejecutan de forma local pueden utilizarse para supervisar la carga de trabajo de E/S en tempdb.

Nunca coloque dispositivos de SAP ASE en la unidad D:\ de la máquina virtual. Esto también se aplica a la base de datos tempdb, aunque los objetos que se conserven en ella sean solo temporales.

#### <a name="impact-of-database-compression"></a>Impacto de la compresión de las bases de datos
En configuraciones donde el ancho de banda de E/S puede convertirse en un factor de limitación, todas las medidas que reduzca el número de IOPS podrían ayudar a ajustar la carga de trabajo que se puede ejecutar en un escenario de IaaS como Azure. Por lo tanto, se recomienda encarecidamente asegurarse de emplear la compresión de SAP ASE antes de cargar una base de datos existente de SAP en Azure.

La recomendación de realizar la compresión antes de realizar esta carga en Azure si no se implementó antes responde a diversos motivos:

* La cantidad de datos que se cargarán en Azure es menor.
* La duración de la ejecución de la compresión es menor, suponiendo que se pueda utilizar un hardware más eficaz con más capacidad de CPU, un mayor ancho de banda de E/S o una latencia de E/S menor en el entorno local.
* Si se utilizan tamaños de base de datos más pequeños, se incurriría en menos costes de asignación de disco.

La compresión de datos y de LOB funciona en una máquina virtual hospedada en máquinas virtuales de Azure del mismo modo a como lo hace en un entorno local. Para más información sobre cómo comprobar si ya se está usando compresión en una base de datos de SAP ASE existente, consulte la nota de SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Uso de DBACockpit para supervisar instancias de base de datos
En el caso de sistemas SAP que usan SAP ASE como plataforma de bases de datos, se puede acceder a DBACockpit mediante ventanas integradas en exploradores en transacciones de DBACockpit o de Webdynpro. Sin embargo, la funcionalidad completa de las actividades de supervisión y administración de bases de datos únicamente está disponible en la implementación de Webdynpro de DBACockpit.

Al igual que en el caso de los sistemas locales, es preciso seguir varios pasos para habilitar toda la funcionalidad de SAP NetWeaver que usa la implementación de Webdynpro de DBACockpit. Siga las indicaciones de la nota de SAP [1245200] para habilitar el uso de los recursos de Webdynpro y generar los necesarios. Las instrucciones de las notas anteriores también lo ayudarán a configurar el administrador de comunicación de Internet (ICM) y los puertos que se utilizarán para las conexiones http y https. La configuración de http predeterminada tiene el siguiente aspecto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
>
>

y los vínculos generados en la transacción de DBACockpit tendrán un aspecto similar al siguiente:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
>
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
>
>

Dependiendo de si la máquina virtual de Azure que hospeda el sistema SAP se conecta de sitio a sitio, con varios sitios o mediante ExpressRoute (implementación entre locales) y en función de cómo lo haga, debe asegurarse de que ese ICM está usando un nombre de host completo que se puede resolver en el equipo desde el que está intentando abrir DBACockpit. Consulte la nota de SAP [773830] para conocer el modo en que ICM determina el nombre de host completo según los parámetros de perfil y, si es necesario, defina específicamente el parámetro icm/host_name_full.

Si implementó la máquina virtual en un escenario solo de nube sin conectividad en varios entornos entre locales y Azure, debe definir una dirección IP pública y un valor de domainlabel. El formato del nombre DNS público de la máquina virtual tendrá un aspecto similar al siguiente:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
>
>

Encontrará más detalles relacionados con el nombre DNS [aquí][virtual-machines-azurerm-versus-azuresm].

El ajuste del parámetro de perfil de SAP icm/host_name_full con el nombre DNS del vínculo de la máquina virtual de Azure puede tener este aspecto:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
>
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
>
>

En ese caso, debe asegurarse de lo siguiente:

* Agregar reglas de entrada al grupo de seguridad de red en el Portal de Azure para los puertos TCP/IP que se utilizan en la comunicación con el ICM
* Agregar reglas de entrada a la configuración del Firewall de Windows para los puertos TCP/IP que se utilizan en la comunicación con el ICM

Para importar de forma automatizada todas las correcciones disponibles, se recomienda aplicar periódicamente la colección de correcciones de la nota de SAP correspondiente a su versión de SAP:

* [1558958]
* [1619967]
* [1882376]

Encontrará más información sobre DBA Cockpit para SAP ASE en las siguientes notas de SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Consideraciones de copia de seguridad y recuperación para SAP ASE
Al implementar SAP ASE en Azure, debe revisar la metodología de copia de seguridad. Incluso si el sistema no es productivo, se debe realizar periódicamente la copia de seguridad de la base de datos de SAP que hospeda SAP ASE. Como el almacenamiento de Azure mantiene tres imágenes, una copia de seguridad es menos importante para compensar un bloqueo de almacenamiento. La razón principal para el mantenimiento de un plan de copia de seguridad y restauración correcto es que puede compensar errores lógicos o manuales gracias a la capacidad de recuperación a un momento dado. El objetivo es usar las copias de seguridad para restaurar la base de datos a un momento dado o utilizar las copias de seguridad de Azure para propagar otro sistema mediante la copia de la base de datos existente. Por ejemplo, podría transferir archivos desde una configuración de SAP de 2 niveles a una configuración de sistema de 3 niveles del mismo sistema mediante la restauración de una copia de seguridad.

La realización de copias de seguridad y restauraciones de bases de datos de Azure funciona del mismo modo que en entornos locales. Consulte las notas de SAP

* [1588316]
* [1585981]

para obtener más información sobre la creación de configuraciones de volcado y la programación de copias de seguridad. Dependiendo de su estrategia y sus necesidades, puede configurar la base de datos y los volcados de registro en disco en uno de los VHD existentes o agregar un VHD adicional para la copia de seguridad.  Para reducir el riesgo de pérdida de datos si se produce un error, se recomienda usar un VHD en el que no se encuentre ningún dispositivo de la base de datos.

Además de compresión de datos y de LOB, SAP ASE también ofrece compresión de copias de seguridad. Para que los volcados de bases de datos y registros ocupen menos espacio, se recomienda usar la compresión de copias de seguridad. Consulte la nota de SAP [1588316] para más información. Este tipo de compresión también es fundamental para reducir la cantidad de datos que se transfieren si planea descargar copias de seguridad o VHD que contengan volcados de copias de seguridad de la máquina virtual de Azure en el entorno local.

No utilice la unidad D:\ como destino de los volcados de bases de datos o registros.

#### <a name="performance-considerations-for-backupsrestores"></a>Consideraciones de rendimiento sobre copias de seguridad / restauraciones
Como en las implementaciones sin sistema operativo, el rendimiento de las copias de seguridad y las restauraciones depende de cuántos volúmenes puedan leerse en paralelo y el rendimiento que estos volúmenes puedan tener. Además, el consumo de CPU que se emplea en la compresión de copias de seguridad puede desempeñar un papel importante en las máquinas virtuales con solo 8 subprocesos de CPU como máximo. Por lo tanto, se pueden asumir los siguientes puntos:

* Cuantos menos VHD se utilicen para almacenar los dispositivos de las bases de datos, menor será el rendimiento general de lectura.
* Cuantos menos subprocesos de CPU haya en la máquina virtual, mayor será el impacto en la compresión de copias de seguridad.
* Cuantos menos destinos (directorios de sección, VHD, etc.) en los que escribir la copia de seguridad haya, menor será el rendimiento.

Para aumentar la cantidad de destinos en los que escribir, existen dos opciones que se pueden usar o combinar según sus necesidades:

* Seccionar el volumen de destino de la copia de seguridad en varios VHD montados con el fin de mejorar el rendimiento de IOPS en ese volumen seccionado
* Crear una configuración de volcado en el nivel de SAP ASE que utilice más de un directorio de destino en el que escribir el volcado

Anteriormente ya se trató en esta guía la sección de un volumen en varios VHD montados. Para más información sobre el uso de varios directorios en la configuración de volcado de SAP ASE, consulte la documentación sobre el procedimiento almacenado sp_config_dump que se utiliza para crear la configuración de volcado en [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperación ante desastres con máquinas virtuales de Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicación de datos con el servidor de replicación SAP Sybase
Con el servidor de replicación SAP Sybase (SRS), SAP ASE proporciona una solución de espera semiactiva para transferir las transacciones de la base de datos a una ubicación distante de manera asincrónica.

La instalación y la actividad de SRS se desarrollan funcionalmente igual de bien en una máquina virtual hospedada en servicios de máquina virtual de Azure que en el entorno local.

Se ha previsto incluir ASE HADR a través de SAP Replication Server en una de las próximas versiones. Se someterá a pruebas con las plataformas de Microsoft Azure y se publicará para estas mismas en cuanto esté disponible.

## <a name="specifics-to-sap-ase-on-linux"></a>Detalles de SAP ASE en Linux
Desde Microsoft Azure puede migrar fácilmente sus aplicaciones de SAP ASE a máquinas virtuales de Azure. SAP ASE en una máquina virtual permite reducir el coste total de propiedad de implementación, administración y mantenimiento de aplicaciones empresariales al migrarlas fácilmente a Microsoft Azure. Con SAP ASE en una máquina virtual de Azure, los administradores y los desarrolladores pueden seguir usando las mismas herramientas de desarrollo y administración que se encuentran disponibles de forma local.

Para implementar máquinas virtuales de Azure es importante conocer los Acuerdos de Nivel de Servicio oficiales que puede encontrar aquí: <https://azure.microsoft.com/support/legal/sla>.

En la nota de SAP [1928533], se proporcionará información de tamaño de SAP y una lista de SKU para máquinas virtuales certificada por SAP. Se pueden encontrar documentos adicionales sobre el ajuste de tamaño de SAP para máquinas virtuales de Azure aquí <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> y aquí <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>.

Se aplican instrucciones y recomendaciones sobre el uso del almacenamiento de Azure, implementación de máquinas virtuales de SAP o supervisión de SAP a las implementaciones de SAP ASE en conjunto con aplicaciones de SAP, como se indica en los primeros cuatro capítulos de este documento.

Las dos siguientes notas de SAP incluyen información general acerca de ASE en Linux y ASE en la nube:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Compatibilidad de versiones de SAP ASE
Actualmente, SAP admite la versión 16.0 de SAP ASE para poder utilizarse con productos de SAP Business Suite. Todas las actualizaciones para el servidor SAP ASE o los controladores ODBC y JDBC que se utilizarán con productos de SAP Business Suite se proporcionan únicamente a través de SAP Service Marketplace: <https://support.sap.com/swdc>.

En el caso de las instalaciones realizadas de forma local, no descargue las actualizaciones del servidor de SAP ASE ni de los controladores JDBC y ODBC directamente desde los sitios web de Sybase. Para obtener más información sobre las revisiones que pueden utilizarse con los productos de SAP Business Suite en el entorno local y en máquinas virtuales de Azure, consulte las siguientes notas de SAP:

* [1590719]
* [1973241]

Encontrará información general sobre la ejecución de SAP Business Suite en SAP ASE en el [SCN](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Instrucciones de configuración de SAP ASE para SAP relacionadas con las instalaciones de SAP ASE en máquinas virtuales de Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estructura de la implementación de SAP ASE
Según la descripción general, los archivos ejecutables de SAP ASE se deben ubicar o instalar en el sistema de archivos raíz de la máquina virtual (/sybase). Normalmente, la mayoría de las bases de datos de herramientas y sistemas SAP ASE no se aprovechan realmente bien mediante las cargas de trabajo de SAP NetWeaver. Por lo tanto, las bases de datos de herramientas y sistemas (master, model, saptools, sybmgmtdb, sybsystemdb) pueden permanecer en el sistema de archivos raíz.

Una excepción puede ser la base de datos temporal que contiene todas las tablas de trabajo y temporales que SAP ASE creó, que, en el caso de algunos SAP ERP y todas las cargas de trabajo de BW, podría requerir un mayor volumen de datos o de operaciones de E/S que no quepan en el disco del sistema operativo de la máquina virtual original.

Según la versión de SAPInst/SWPM utilizada para instalar el sistema, la base de datos puede contener los siguientes elementos:

* Una única tempdb de SAP ASE que se crea al instalar SAP ASE
* Una tempdb de SAP ASE creada al instalar SAP ASE y un saptempdb adicional creado en la rutina de instalación de SAP
* Una tempdb de SAP ASE creada al instalar SAP ASE y una tempdb adicional que se creó manualmente (por ejemplo, según la nota de SAP [1752266]) para cumplir los requisitos de tempdb específicos de ERP/BW

Tiene sentido en el caso de todas las cargas de trabajo de BW o una específica de ERP en cuanto a rendimiento, para mantener los dispositivos de tempdb de las tempdb que se crearon de forma adicional (mediante SWPM o manualmente) en un sistema de archivos distinto que se puede representar mediante un disco de datos de Azure único o un RAID de Linux que abarque varios discos de datos de Azure. Si no existe ninguna tempdb adicional, se recomienda crear una (nota de SAP [1752266]).

En estos sistemas, se deben seguir estos pasos para la tempdb que se creó de forma adicional:

* Traslade el primer directorio de tempdb al primer sistema de archivos de la base de datos de SAP.
* Agregue directorios de tempdb a cada uno de los VHD que contengan un sistema de archivos de la base de datos de SAP.

Esta configuración permite que tempdb consuma más espacio que el que la unidad del sistema puede proporcionar. Como referencia, se pueden comprobar los tamaños de los directorios de tempdb en los sistemas existentes que se ejecutan de forma local. De lo contrario, dicha configuración admitiría los números de IOPS en la tempdb, que no se pueden proporcionar con la unidad del sistema. Reiteramos que los sistemas que se ejecutan de forma local pueden utilizarse para supervisar la carga de trabajo de E/S en tempdb.

Nunca coloque ningún directorio de SAP ASE en las rutas /mnt ni /mnt/resource de la máquina virtual. Esto también se aplica a la tempdb (incluso si los objetos que se mantiene en tempdb son solo temporales) porque tanto /mnt como /mnt/resource son espacios temporales de máquina virtual de Azure predeterminados. Encontrará más información sobre el espacio temporal de máquina virtual de Azure en [este artículo][virtual-machines-linux-how-to-attach-disk].

#### <a name="impact-of-database-compression"></a>Impacto de la compresión de las bases de datos
En configuraciones donde el ancho de banda de E/S puede convertirse en un factor de limitación, todas las medidas que reduzca el número de IOPS podrían ayudar a ajustar la carga de trabajo que se puede ejecutar en un escenario de IaaS como Azure. Por lo tanto, se recomienda encarecidamente asegurarse de emplear la compresión de SAP ASE antes de cargar una base de datos existente de SAP en Azure.

La recomendación de realizar la compresión antes de realizar esta carga en Azure si no se implementó antes responde a diversos motivos:

* La cantidad de datos que se cargarán en Azure es menor.
* La duración de la ejecución de la compresión es menor, suponiendo que se pueda utilizar un hardware más eficaz con más capacidad de CPU, un mayor ancho de banda de E/S o una latencia de E/S menor en el entorno local.
* Si se utilizan tamaños de base de datos más pequeños, se incurriría en menos costes de asignación de disco.

La compresión de datos y de LOB funciona en una máquina virtual hospedada en máquinas virtuales de Azure del mismo modo a como lo hace en un entorno local. Para más información sobre cómo comprobar si ya se está usando compresión en una base de datos de SAP ASE existente, consulte la nota de SAP [1750510]. Consulte también la nota de SAP [2121797] para más información sobre la compresión de bases de datos.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Uso de DBACockpit para supervisar instancias de base de datos
En el caso de sistemas SAP que usan SAP ASE como plataforma de bases de datos, se puede acceder a DBACockpit mediante ventanas integradas en exploradores en transacciones de DBACockpit o de Webdynpro. Sin embargo, la funcionalidad completa de las actividades de supervisión y administración de bases de datos únicamente está disponible en la implementación de Webdynpro de DBACockpit.

Al igual que en el caso de los sistemas locales, es preciso seguir varios pasos para habilitar toda la funcionalidad de SAP NetWeaver que usa la implementación de Webdynpro de DBACockpit. Siga las indicaciones de la nota de SAP [1245200] para habilitar el uso de los recursos de Webdynpro y generar los necesarios. Las instrucciones de las notas anteriores también lo ayudarán a configurar el administrador de comunicación de Internet (ICM) y los puertos que se utilizarán para las conexiones http y https. La configuración de http predeterminada tiene el siguiente aspecto:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
>
>

y los vínculos generados en la transacción de DBACockpit tendrán un aspecto similar al siguiente:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
>
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
>
>

Dependiendo de si la máquina virtual de Azure que hospeda el sistema SAP se conecta de sitio a sitio, con varios sitios o mediante ExpressRoute (implementación entre locales) y en función de cómo lo haga, debe asegurarse de que ese ICM está usando un nombre de host completo que se puede resolver en el equipo desde el que está intentando abrir DBACockpit. Consulte la nota de SAP [773830] para conocer el modo en que ICM determina el nombre de host completo según los parámetros de perfil y, si es necesario, defina específicamente el parámetro icm/host_name_full.

Si implementó la máquina virtual en un escenario solo de nube sin conectividad en varios entornos entre locales y Azure, debe definir una dirección IP pública y un valor de domainlabel. El formato del nombre DNS público de la máquina virtual tendrá un aspecto similar al siguiente:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
>
>

Encontrará más detalles relacionados con el nombre DNS [aquí][virtual-machines-azurerm-versus-azuresm].

El ajuste del parámetro de perfil de SAP icm/host_name_full con el nombre DNS del vínculo de la máquina virtual de Azure puede tener este aspecto:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
>
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
>
>

En ese caso, debe asegurarse de lo siguiente:

* Agregar reglas de entrada al grupo de seguridad de red en el Portal de Azure para los puertos TCP/IP que se utilizan en la comunicación con el ICM
* Agregar reglas de entrada a la configuración del Firewall de Windows para los puertos TCP/IP que se utilizan en la comunicación con el ICM

Para importar de forma automatizada todas las correcciones disponibles, se recomienda aplicar periódicamente la colección de correcciones de la nota de SAP correspondiente a su versión de SAP:

* [1558958]
* [1619967]
* [1882376]

Encontrará más información sobre DBA Cockpit para SAP ASE en las siguientes notas de SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Consideraciones de copia de seguridad y recuperación para SAP ASE
Al implementar SAP ASE en Azure, debe revisar la metodología de copia de seguridad. Incluso si el sistema no es productivo, se debe realizar periódicamente la copia de seguridad de la base de datos de SAP que hospeda SAP ASE. Como el almacenamiento de Azure mantiene tres imágenes, una copia de seguridad es menos importante para compensar un bloqueo de almacenamiento. La razón principal para el mantenimiento de un plan de copia de seguridad y restauración correcto es que puede compensar errores lógicos o manuales gracias a la capacidad de recuperación a un momento dado. El objetivo es usar las copias de seguridad para restaurar la base de datos a un momento dado o utilizar las copias de seguridad de Azure para propagar otro sistema mediante la copia de la base de datos existente. Por ejemplo, podría transferir archivos desde una configuración de SAP de 2 niveles a una configuración de sistema de 3 niveles del mismo sistema mediante la restauración de una copia de seguridad.

La realización de copias de seguridad y restauraciones de bases de datos de Azure funciona del mismo modo que en entornos locales. Consulte las notas de SAP

* [1588316]
* [1585981]

para obtener más información sobre la creación de configuraciones de volcado y la programación de copias de seguridad. Dependiendo de su estrategia y sus necesidades, puede configurar la base de datos y los volcados de registro en disco en uno de los VHD existentes o agregar un VHD adicional para la copia de seguridad.  Para reducir el riesgo de pérdidas de datos si se produce un error, se recomienda usar un VHD donde no haya directorios ni archivos de bases de datos.

Además de compresión de datos y de LOB, SAP ASE también ofrece compresión de copias de seguridad. Para que los volcados de bases de datos y registros ocupen menos espacio, se recomienda usar la compresión de copias de seguridad. Consulte la nota de SAP [1588316] para más información. Este tipo de compresión también es fundamental para reducir la cantidad de datos que se transfieren si planea descargar copias de seguridad o VHD que contengan volcados de copias de seguridad de la máquina virtual de Azure en el entorno local.

No use el espacio temporal /mnt ni /mnt/Resource de máquina virtual de Azure como destino de volcados de registro o de bases de datos.

#### <a name="performance-considerations-for-backupsrestores"></a>Consideraciones de rendimiento sobre copias de seguridad y restauraciones
Como en las implementaciones sin sistema operativo, el rendimiento de las copias de seguridad y las restauraciones depende de cuántos volúmenes puedan leerse en paralelo y el rendimiento que estos volúmenes puedan tener. Además, el consumo de CPU que se emplea en la compresión de copias de seguridad puede desempeñar un papel importante en las máquinas virtuales con solo 8 subprocesos de CPU como máximo. Por lo tanto, se pueden asumir los siguientes puntos:

* Cuantos menos VHD se utilicen para almacenar los dispositivos de las bases de datos, menor será el rendimiento general de lectura.
* Cuantos menos subprocesos de CPU haya en la máquina virtual, mayor será el impacto en la compresión de copias de seguridad.
* Cuantos menos destinos (RAID de software Linux, VHD) en los que escribir la copia de seguridad haya, menor será el rendimiento.

Para aumentar la cantidad de destinos en los que escribir, existen dos opciones que se pueden usar o combinar según sus necesidades:

* Seccionar el volumen de destino de la copia de seguridad en varios VHD montados con el fin de mejorar el rendimiento de IOPS en ese volumen seccionado
* Crear una configuración de volcado en el nivel de SAP ASE que utilice más de un directorio de destino en el que escribir el volcado

Anteriormente ya se trató en esta guía la sección de un volumen en varios VHD montados. Para más información sobre el uso de varios directorios en la configuración de volcado de SAP ASE, consulte la documentación sobre el procedimiento almacenado sp_config_dump que se utiliza para crear la configuración de volcado en [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperación ante desastres con máquinas virtuales de Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicación de datos con el servidor de replicación SAP Sybase
Con el servidor de replicación SAP Sybase (SRS), SAP ASE proporciona una solución de espera semiactiva para transferir las transacciones de la base de datos a una ubicación distante de manera asincrónica.

La instalación y la actividad de SRS se desarrollan funcionalmente igual de bien en una máquina virtual hospedada en servicios de máquina virtual de Azure que en el entorno local.

En este momento no se admite ASE HADR a través de SAP Replication Server. Es posible que más adelante se realicen pruebas con las plataformas de Microsoft Azure y se publiquen para ellas.

## <a name="specifics-to-oracle-database-on-windows"></a>Detalles de la base de datos de Oracle en Windows
Desde mediados de 2013, el software de Oracle es compatible con Oracle para ejecutarse en Microsoft Windows Hyper-V y Azure. Lea este artículo para más información sobre la compatibilidad general de Windows Hyper-V y Azure Oracle: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces>.

A partir de esta compatibilidad general, también se admite el escenario concreto en el que las aplicaciones de SAP aprovechen las bases de datos de Oracle. Los detalles se mencionan en esta parte del documento.

### <a name="oracle-version-support"></a>Compatibilidad de versiones de Oracle
Todos los detalles sobre las versiones de Oracle y las versiones de SO correspondientes que se admiten para ejecutar SAP en Oracle en máquinas virtuales de Azure se encuentran en la nota de SAP [2039619]

Se puede encontrar información general sobre la ejecución de SAP Business Suite en Oracle en SCN: <https://scn.sap.com/community/oracle>.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instrucciones de configuración de Oracle para instalaciones de SAP en máquinas virtuales de Azure
#### <a name="storage-configuration"></a>Configuración de almacenamiento
Solo se admiten versiones de Oracle de solo una versión que usen discos con formato NTFS. Todos los archivos de las bases de datos se deben almacenar en el sistema de archivos NTFS basado en discos VHD. Estos discos duros virtuales están montados en la máquina virtual de Azure y se basan en el almacenamiento de blob en páginas de Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>).
Las unidades de red o los recursos compartidos remotos como los servicios de archivos de Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

**NO** son compatibles con los archivos de bases de datos de Oracle.

Si usa discos duros virtuales de Azure basado en el almacenamiento de blobs en páginas de Azure, las declaraciones realizadas en este documento en el capítulo [Almacenamiento en caché de máquinas virtuales y VHD][dbms-guide-2.1] y [Almacenamiento de Microsoft Azure][dbms-guide-2.3] también se refieren a las implementaciones con la base de datos de Oracle.

Como se explicó anteriormente en la parte general del documento, existen cuotas en el rendimiento IOPS para VHD de Azure. Las cuotas exactas varían según el tipo de máquina virtual que se utilice. Dispone de una lista con los tipos de máquina virtual y sus cuotas [aquí][virtual-machines-sizes].

Para identificar los tipos de máquina virtual de Azure admitidos, consulte la nota de SAP [1928533]

Mientras la cuota actual de IOPS por disco cumpla los requisitos, se pueden almacenar todos los archivos de base de datos en un solo VHD de Azure montado.

Si necesita más IOPS, se recomienda encarecidamente utilizar bloques de almacenamiento de Windows (solo disponibles en Windows Server 2012 y versiones posteriores) o crear secciones de Windows para Windows 2008 R2 a fin de crear un dispositivo lógico de gran tamaño en varios discos VHD montados. Consulte también el capítulo [RAID de software][dbms-guide-2.2] de este documento. Este enfoque simplifica la sobrecarga de administración para administrar el espacio en disco y evita el esfuerzo de distribuir archivos manualmente en varios VHD montados.

#### <a name="backup--restore"></a>Copia de seguridad y restauración
Para utilizar la funcionalidad de copia de seguridad y restauración, las SAP BR*Tools para Oracle se admiten del mismo modo que en los sistemas operativos Windows Server y Hyper-V estándares. También se admite Oracle Recovery Manager (RMAN) para las copias de seguridad en discos y las restauraciones desde discos.

#### <a name="high-availability"></a>Alta disponibilidad
[comment]: <> (el vínculo hace referencia a ASM)
Oracle Data Guard se admite con fines de alta disponibilidad y recuperación ante desastres. Encontrará los detalles en [esta][virtual-machines-windows-classic-configure-oracle-data-guard] documentación.

#### <a name="other"></a>Otros
Todos los demás temas generales, como los conjuntos de disponibilidad de Azure o la supervisión de SAP, se aplican como se describen en los tres primeros capítulos de este documento para implementaciones de máquinas virtuales también con la base de datos de Oracle.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Detalles de la base de datos SAP MaxDB en Windows
### <a name="sap-maxdb-version-support"></a>Compatibilidad de versiones de SAP MaxDB
SAP actualmente admite SAP MaxDB versión 7.9 para su uso con productos basados en SAP NetWeaver en Azure. Todas las actualizaciones del servidor SAP MaxDB o de los controladores ODBC y JDBC que se utilizarán con productos basados en SAP NetWeaver se proporcionan únicamente a través de SAP Service Marketplace en <https://support.sap.com/swdc>.
Se puede encontrar información general sobre la ejecución de SAP NetWeaver en SAP MaxDB en <https://scn.sap.com/community/maxdb>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Versiones de Microsoft Windows y tipos de máquina virtual de Azure admitidos para SAP MaxDB DBMS
Para obtener la versión compatible de Microsoft Windows para SAP MaxDB DBMS en Azure, consulte:

* [Matriz de disponibilidad de productos (PAM) SAP][sap-pam]
* Nota de SAP [1928533]

Se recomienda encarecidamente utilizar la versión más reciente del sistema operativo Microsoft Windows (Microsoft Windows 2012 R2 en el momento en el que se escribe este artículo).

### <a name="available-sap-maxdb-documentation"></a>Documentación de SAP MaxDB disponible
Encontrará la lista de la documentación de SAP MaxDB actualizada en la nota de SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instrucciones de configuración de SAP MaxDB para instalaciones de SAP en máquinas virtuales de Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuración de almacenamiento
Los procedimientos recomendados de almacenamiento de Azure para SAP MaxDB siguen las recomendaciones generales mencionadas en el capítulo [Estructura de una implementación de RDBMS][dbms-guide-2].

> [!IMPORTANT]
> Al igual que otras bases de datos, SAP MaxDB también dispone de archivos de datos y de registro. Sin embargo, en la terminología de SAP MaxDB el término correcto es "volumen" (no "archivo"). Por ejemplo, existen volúmenes de datos y de registro de SAP MaxDB. No los confunda con los volúmenes de disco del sistema operativo.
>
>

En resumen, deberá hacer lo siguiente:

* Establecer la cuenta de almacenamiento de Azure que contiene los volúmenes de registros y datos de SAP MaxDB (es decir, archivos) en **Almacenamiento con redundancia local (LRS)** como se indica en el capítulo [Almacenamiento de Microsoft Azure][dbms-guide-2.3].
* Separar la ruta de acceso de E/S para los volúmenes (es decir, archivos) de datos de SAP MaxDB desde la ruta de acceso de E/S para los volúmenes (de nuevo, archivos) de registro. Esto significa que los volúmenes de datos de SAP MaxDB han de estar instalados en una unidad lógica y que los volúmenes de registro de MaxDB de SAP deben estar instalados en otra.
* Establecer la caché de archivos adecuada para cada blob de Azure en función de si la utiliza para volúmenes de datos o registros de SAP MaxDB y de si se usa almacenamiento estándar o Premium Storage de Azure, tal como se describe en el capítulo [Almacenamiento en caché de máquinas virtuales][dbms-guide-2.1].
* Mientras la cuota actual de IOPS por disco cumpla los requisitos, se pueden almacenar todos los volúmenes de datos en un solo VHD de Azure montado y también almacenar todos los volúmenes de registro de bases de datos en otro VHD único de Azure montado.
* Si necesita más IOPS o espacio, se recomienda encarecidamente utilizar bloques de almacenamiento de Microsoft Windows (solo disponibles en Microsoft Windows Server 2012 y versiones posteriores) o la creación de bandas de Microsoft Windows para Microsoft Windows 2008 R2 a fin de crear un dispositivo lógico de gran tamaño en varios discos VHD montados. Consulte también el capítulo [RAID de software][dbms-guide-2.2] de este documento. Este enfoque simplifica la sobrecarga de administración para administrar el espacio en disco y evita el esfuerzo de distribuir archivos manualmente en varios VHD montados.
* Para los requisitos de IOPS más exigentes, puede usar el almacenamiento Premium de Azure, disponible en las series de máquinas virtuales DS y GS.

![Configuración de referencia de máquinas virtuales IaaS de Azure para SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Copia de seguridad y restauración
Al implementar SAP MaxDB en Azure, debe revisar la metodología de copias de seguridad. Incluso si el sistema no es productivo, se debe realizar periódicamente la copia de seguridad de la base de datos de SAP que hospeda SAP MaxDB. Puesto que el almacenamiento de Azure guarda tres imágenes, ahora una copia de seguridad tiene menos relevancia en cuanto a la protección del sistema contra errores de almacenamiento y más relevancia en cuanto a errores operativos o administrativos. La razón principal para el mantenimiento de un plan de copia de seguridad y restauración correcto es que puede compensar errores lógicos o manuales gracias a la capacidad de recuperación a un momento dado. Por tanto, el propósito es usar copias de seguridad para restaurar la base de datos a un momento dado o utilizar las copias de seguridad de Azure para propagar otro sistema mediante la copia de la base de datos existente. Por ejemplo, podría transferir archivos desde una configuración de SAP de 2 niveles a una configuración de sistema de 3 niveles del mismo sistema mediante la restauración de una copia de seguridad.

La realización de copias de seguridad y restauraciones de una base de datos de Azure funciona del mismo modo que para sistemas locales, por lo que puede usar herramientas estándar de copia de seguridad y restauración de SAP MaxDB, que se describen en uno de los documentos de SAP MaxDB enumerados en la nota de SAP [767598].

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Consideraciones de rendimiento para copias de seguridad y restauraciones
Como en las implementaciones sin sistema operativo, el rendimiento de las copias de seguridad y las restauraciones depende de cuántos volúmenes puedan leerse en paralelo y del rendimiento de estos volúmenes. Además, el consumo de CPU que se emplea en la compresión de copias de seguridad puede desempeñar un papel importante en las máquinas virtuales con 8 subprocesos de CPU como máximo. Por lo tanto, se pueden asumir los siguientes puntos:

* Cuantos menos VHD se utilicen para almacenar los dispositivos de las bases de datos, menor será el rendimiento general de lectura.
* Cuantos menos subprocesos de CPU haya en la máquina virtual, mayor será el impacto en la compresión de copias de seguridad.
* Cuantos menos destinos (directorios de sección, VHD, etc.) en los que escribir la copia de seguridad haya, menor será el rendimiento.

Para aumentar la cantidad de destinos en los que escribir, existen dos opciones a las que puede recurrir (puede que de forma combinada) según sus necesidades:

* Dedicar volúmenes distintos a copias de seguridad
* Seccionar el volumen de destino de la copia de seguridad en varios VHD montados con el fin de mejorar el rendimiento de IOPS en ese volumen de disco seccionado
* Disponer de dispositivos de discos lógicos dedicados distintos para:
  * Volúmenes (archivos) de copia de seguridad de SAP MaxDB
  * Volúmenes de datos de SAP MaxDB
  * Volúmenes de registros de SAP MaxDB

Ya se trató en el capítulo [RAID de software][dbms-guide-2.2] de este documento la sección de un volumen en varios discos duros virtuales montados.

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Otros
Todos los demás temas generales, como los conjuntos de disponibilidad de Azure o la supervisión de SAP, se aplican como se describen en los tres primeros capítulos de este documento para implementaciones de máquinas virtuales con la base de datos de SAP MaxDB.
Otras configuraciones específicas de SAP MaxDB son transparentes para las máquinas virtuales de Azure y se describen en distintos documentos de la nota de SAP [767598] y en estas otras notas:

* [826037]
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Detalles de SAP liveCache en Windows
### <a name="sap-livecache-version-support"></a>Compatibilidad de versiones de SAP liveCache
La versión mínima de SAP liveCache que las máquinas virtuales de Azure admiten es **SAP LC/LCAPPS 10.0 SP 25**, incluida **liveCache 7.9.08.31** y **LCA-Build 25**, publicadas en **EhP 2 para SAP SCM 7.0** y en versiones posteriores.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Versiones de Microsoft Windows y tipos de máquina virtual de Azure admitidos para SAP liveCache DBMS
Para obtener la versión compatible de Microsoft Windows para SAP liveCache en Azure, consulte:

* [Matriz de disponibilidad de productos (PAM) SAP][sap-pam]
* Nota de SAP [1928533]

Se recomienda encarecidamente utilizar la versión más reciente del sistema operativo Microsoft Windows (Microsoft Windows 2012 R2 en el momento en el que se escribe este artículo).

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instrucciones de configuración de SAP liveCache para instalaciones de SAP en máquinas virtuales de Azure
#### <a name="recommended-azure-vm-types"></a>Tipos de máquina virtual de Azure recomendadas
Puesto que SAP liveCache es una aplicación que realiza cálculos de grandes proporciones, la cantidad y la velocidad de CPU y RAM influyen en gran medida en el rendimiento de SAP liveCache.

En el caso de los tipos de máquina virtual de Azure compatibles con SAP (nota de SAP [1928533]), todos los recursos de CPU virtuales asignados a la máquina virtual cuentan con el respaldo de los recursos de CPU físicos dedicados del hipervisor. No existe aprovisionamiento en exceso (y, por tanto, tampoco existe competencia por los recursos de CPU).

Asimismo, para todos los tipos de versión de máquina virtual de Azure compatibles con SAP, la memoria de la máquina virtual se asigna al 100 % a la memoria física; por ejemplo, no se recurre al aprovisionamiento en exceso (exceso de compromiso).

Desde esta perspectiva, se recomienda encarecidamente utilizar las nuevas series D o DS de máquina virtual de Azure (junto con el almacenamiento Premium de Azure), que disponen de procesadores un 60 % más rápidos que la serie A. En el caso de las cargas de RAM y CPU más exigentes, puede utilizar las series G y GS de máquinas virtuales (junto con el almacenamiento Premium de Azure) con la familia de procesadores E5 v3 más recientes de Intel® Xeon®, que ofrecen el doble de memoria y cuatro veces más almacenamiento en unidad de estado sólido (SSD) que las series D y DS.

#### <a name="storage-configuration"></a>Configuración de almacenamiento
Puesto que SAP liveCache se basa en la tecnología SAP MaxDB, todos los procedimientos recomendados de Almacenamiento de Azure que se mencionan con relación a SAP MaxDB en el capítulo [Configuración de almacenamiento][dbms-guide-8.4.1] también se pueden aplicar a SAP liveCache.

#### <a name="dedicated-azure-vm-for-livecache"></a>Máquina virtual de Azure dedicada para liveCache
Dado que SAP liveCache emplea una potencia de cálculo sumamente intensiva, se recomienda encarecidamente implementarla en una máquina virtual de Azure dedicada para obtener resultados productivos.

![Máquina virtual de Azure dedicada para liveCache para un caso de uso productivo][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Copia de seguridad y restauración
Las copias de seguridad y las restauraciones, incluidas las consideraciones de rendimiento, ya se describen en los capítulos pertinentes de SAP MaxDB [Copia de seguridad y restauración][dbms-guide-8.4.2] y [Consideraciones de rendimiento para copias de seguridad y restauraciones][dbms-guide-8.4.3].

#### <a name="other"></a>Otros
Todos los demás temas generales ya se describen en [este] capítulo correspondiente de SAP MaxDB [dbms-guide-8.4.4].

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Detalles de SAP Content Server en Windows
El servidor SAP Content Server es un componente independiente basado en servidor para almacenar contenido como documentos electrónicos en formatos diferentes. SAP Content Server se ofrece para el desarrollo de tecnología y se concibe para su uso en varias aplicaciones con cualquier aplicación de SAP. Se instala en un sistema independiente. Su contenido más habitual es material de aprendizaje y documentación de dibujos técnicos de Knowledge Warehouse que se crean en el sistema de administración de documentos de mySAP PLM.

### <a name="sap-content-server-version-support"></a>Compatibilidad de versiones de SAP Content Server
SAP actualmente admite:

* **SAP Content Server** con la versión **6.50 (y superior)**
* **SAP MaxDB versión 7.9**
* **Microsoft IIS (Internet Information Server) versión 8.0 (y versiones posterior)**

Se recomienda encarecidamente utilizar la versión más reciente de SAP Content Server, que en el momento de la creación de este documento es la **6.50 SP4**, y la versión más reciente de **Microsoft IIS 8.5**.

Compruebe las últimas versiones compatibles de SAP Content Server y Microsoft IIS en [Matriz de disponibilidad de productos (PAM) SAP][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de máquina virtual de Azure y Microsoft Windows admitidos con SAP Content Server
Para conocer la versión de Windows compatible con SAP Content Server en Azure, consulte:

* [Matriz de disponibilidad de productos (PAM) SAP][sap-pam]
* Nota de SAP [1928533]

Se recomienda encarecidamente utilizar la versión más reciente de Microsoft Windows, que en el momento de creación de este documento es **Windows Server 2012 R2**.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instrucciones de configuración de SAP Content Server para las instalaciones de SAP en máquinas virtuales de Azure
#### <a name="storage-configuration"></a>Configuración de almacenamiento
Si configura SAP Content Server para almacenar archivos en la base de datos de SAP MaxDB, todos los procedimientos recomendados de Azure Storage que se mencionan con relación a SAP MaxDB en el capítulo [Configuración de almacenamiento][dbms-guide-8.4.1] también se pueden aplicar en el caso de SAP Content Server.

Si configura SAP Content Server para almacenar archivos en el sistema de archivos, se recomienda usar una unidad lógica exclusiva. El uso de espacios de almacenamiento también permite aumentar el tamaño del disco lógico y el rendimiento de E/S, como se describe en el capítulo [RAID de software][dbms-guide-2.2].

#### <a name="sap-content-server-location"></a>Ubicación de SAP Content Server
SAP Content Server se debe implementar en la misma región de Azure y de red virtual de Azure en la que se implementó el sistema SAP. Puede elegir si desea implementar los componentes de SAP Content Server en una máquina virtual de Azure dedicada o en la misma máquina virtual en la que se ejecuta el sistema SAP.

![Máquina virtual de Azure dedicada para SAP Content Server][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Ubicación del SAP Cache Server
El servidor SAP Cache Server es un componente adicional basado en servidores que proporciona acceso a documentos (en la memoria caché) en entornos locales. SAP Cache Server almacena en la caché los documentos de SAP Content Server. De este modo se optimiza el tráfico de red cuando los documentos se tienen que recuperar más de una vez desde distintas ubicaciones. Por lo general, SAP Cache Server debe encontrarse físicamente cerca del cliente que accede a este servidor.

Dispone de dos opciones:

1. **El cliente es un sistema SAP de back-end** Si se configura un sistema SAP de back-end para acceder a SAP Content Server, dicho sistema SAP será un cliente. Puesto que el sistema SAP y SAP Content Server se implementan en la misma región de Azure –en el mismo centro de datos de Azure–, ambos se encuentran físicamente próximos entre sí. Por lo tanto, no hace falta disponer de un SAP Content Server dedicado. Los clientes de interfaz de usuario de SAP (SAP GUI o un explorador web) acceden directamente al sistema SAP y este recupera los documentos desde SAP Content Server.
2. **El cliente es un explorador web local** SAP Content Server puede configurarse para acceder a él directamente mediante un explorador web. En este caso, un explorador web que se ejecute de forma local es un cliente de SAP Content Server. Se coloca un centro de datos local y el de Azure en diferentes ubicaciones físicas (lo ideal es que se encuentren próximos entre sí). Su centro de datos local se conecta a Azure a través del VPN de sitio a sitio o ExpressRoute de Azure. Aunque ambas opciones ofrecen conexión de red VPN segura a Azure, la conexión de red de sitio a sitio no ofrece un acuerdo de nivel de servicio de latencia y ancho de banda de red entre el centro de datos local y el de Azure. Para agilizar el acceso a los documentos, puede realizar una de las siguientes acciones:
   1. Instalar el servidor de caché de SAP de forma local, cerca del explorador web local (opción de [esta][dbms-guide-900-sap-cache-server-on-premises] ilustración)
   2. Configurar ExpressRoute de Azure, que ofrece una conexión de red dedicada de alta velocidad y baja latencia entre el centro de datos local y el centro de datos de Azure.

![Opción de instalar el servidor de caché de SAP de forma local][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Copia de seguridad y restauración
Si configura SAP Content Server para almacenar archivos en la base de datos de SAP MaxDB, las consideraciones de rendimiento y de procedimiento de copia de seguridad y restauración ya se describieron en los capítulos de SAP MaxDB [Copia de seguridad y restauración][dbms-guide-8.4.2] y [Consideraciones de rendimiento para copias de seguridad y restauraciones][dbms-guide-8.4.3].

Si configura SAP Content Server para almacenar archivos en el sistema de archivos, una opción es ejecutar de forma manual la copia de seguridad o la restauración de toda la estructura de archivos donde se encuentran los documentos. Al igual que las copias de seguridad y las restauraciones de SAP MaxDB, se recomienda disponer de un volumen de disco dedicado para realizar copias de seguridad.

#### <a name="other"></a>Otros
Otras configuraciones específicas de SAP Content Server son transparentes para máquinas virtuales de Azure y se describen en diversos documentos y notas de SAP:

* <https://service.sap.com/contentserver>
* Nota de SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Detalles de IBM DB2 para LUW en Windows
Con Microsoft Azure, puede migrar fácilmente la aplicación SAP existente que se ejecuta en IBM DB2 para Linux, UNIX y Windows (LUW) a máquinas virtuales de Azure. Con SAP en IBM DB2 para LUW, los administradores y los desarrolladores pueden seguir usando las mismas herramientas de desarrollo y administración, disponibles de forma local.
Se puede encontrar información general sobre la ejecución de SAP Business Suite en IBM DB2 para LUW en SAP Community Network: <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Para más información y actualizaciones de SAP en DB2 para LUW en Azure, consulte la nota de SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX y compatibilidad de versiones de Windows
SAP en IBM DB2 para LUW en los servicios de máquina virtual de Microsoft Azure es compatible a partir de la versión 10.5 de DB2.

Para más información de los tipos de máquina virtual de Azure y los productos de SAP compatibles, consulte la nota de SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instrucciones de configuración de IBM DB2 para Linux, UNIX y Windows para instalaciones de SAP en máquinas virtuales de Azure
#### <a name="storage-configuration"></a>Configuración de almacenamiento
Todos los archivos de las bases de datos se deben almacenar en el sistema de archivos NTFS basado en discos VHD. Estos discos duros virtuales están montados en la máquina virtual de Azure y se basan en el almacenamiento de blob en páginas de Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>).
**NINGÚN** tipo de unidad de red o recurso compartido remoto, como los siguientes servicios de archivos de Azure, es compatible con archivos de bases de datos:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Si usa discos duros virtuales de Azure basados en el almacenamiento de blobs en páginas de Azure, las declaraciones realizadas en este documento en el capítulo [Estructura de una implementación de RDBMS][dbms-guide-2] también se aplican a las implementaciones de IBM DB2 para bases de datos LUW.

Como se explicó anteriormente en la parte general del documento, existen cuotas en el rendimiento IOPS para VHD de Azure. Las cuotas exactas varían según el tipo de máquina virtual que se utilice. Dispone de una lista con los tipos de máquina virtual y sus cuotas [aquí][virtual-machines-sizes].

Mientras la cuota actual de IOPS por disco sea suficiente, se pueden almacenar todos los archivos de base de datos en un solo VHD de Azure montado.

Para conocer las consideraciones de rendimiento, consulte también el capítulo "Data Safety and Performance Considerations for Database Directories" (Consideraciones de seguridad y rendimiento de directorios de bases de datos) en las guías de instalación de SAP.

También puede usar grupos de almacenamiento de Windows (solo disponibles en Windows Server 2012 y versiones posteriores) o crear secciones de Windows para Windows 2008 R2, como se describe en el capítulo [RAID de software][dbms-guide-2.2] de este documento, para crear un dispositivo lógico de gran tamaño en varios discos VHD montados.
Para los discos que contienen rutas de acceso de almacenamiento de DB2 para directorios sapdata y saptmp, debe especificar un tamaño de sector de disco físico de 512 kB. Si usa bloques de almacenamiento de Windows, debe crearlos manualmente en la interfaz de la línea de comandos con el parámetro "-LogicalSectorSizeDefault". Para más información, consulte <https://technet.microsoft.com/library/hh848689.aspx>.

#### <a name="backuprestore"></a>Copia de seguridad y restauración
La funcionalidad de copia de seguridad y restauración para IBM DB2 para LUW es compatible del mismo modo que en los sistemas operativos Windows Server y Hyper-V estándares.

Debe asegurarse de seguir una estrategia establecida de copias de seguridad de bases de datos válida.

Como en las implementaciones sin sistema operativo, el rendimiento de las copias de seguridad y las restauraciones depende de cuántos volúmenes puedan leerse en paralelo y el rendimiento que estos volúmenes puedan tener. Además, el consumo de CPU que se emplea en la compresión de copias de seguridad puede desempeñar un papel importante en las máquinas virtuales con solo 8 subprocesos de CPU como máximo. Por lo tanto, se pueden asumir los siguientes puntos:

* Cuantos menos VHD se utilicen para almacenar los dispositivos de las bases de datos, menor será el rendimiento general de lectura.
* Cuantos menos subprocesos de CPU haya en la máquina virtual, mayor será el impacto en la compresión de copias de seguridad.
* Cuantos menos destinos (directorios de sección, VHD, etc.) en los que escribir la copia de seguridad haya, menor será el rendimiento.

Para aumentar la cantidad de destinos en los que escribir, existen dos opciones que se pueden usar o combinar según sus necesidades:

* Seccionar el volumen de destino de la copia de seguridad en varios VHD montados con el fin de mejorar el rendimiento de IOPS en ese volumen seccionado
* Uso de más de un directorio de destino en el que escribir copias de seguridad

#### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidad y recuperación ante desastres
Microsoft Cluster Server (MSCS) no es compatible.

La alta disponibilidad y la recuperación ante desastres (HADR) de DB2 sí son compatibles. Si las máquinas virtuales de la configuración de alta disponibilidad tienen una resolución de nombres correcta, la configuración de Azure no será diferente a cualquier otra realizada de forma local. No se recomienda depender exclusivamente de la resolución de direcciones IP.

No utilice la replicación geográfica de Tienda de Azure. Para más información, consulte los capítulos [Microsoft Azure Storage][dbms-guide-2.3] y [Alta disponibilidad y recuperación ante desastres con máquinas virtuales de Azure][dbms-guide-3].

#### <a name="other"></a>Otros
Todos los demás temas generales, como los conjuntos de disponibilidad de Azure o la supervisión de SAP, se aplican como se describen en los tres primeros capítulos de este documento para implementaciones de máquinas virtuales también con IBM DB2 para LUW.

Consulte también el capítulo [Resumen general de SQL Server para SAP en Azure][dbms-guide-5.8].

