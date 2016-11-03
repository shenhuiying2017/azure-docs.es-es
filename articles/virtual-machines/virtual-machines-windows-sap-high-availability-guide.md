<properties
   pageTitle="SAP NetWeaver en máquinas virtuales (VM): guía de alta disponibilidad | Microsoft Azure"
   description="Guía de alta disponibilidad para SAP NetWeaver en máquinas virtuales de Windows"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="goraco"/>


# <a name="sap-netweaver-on-windows-virtual-machines-vms-highavailability-guide"></a>SAP NetWeaver en máquinas virtuales de Windows (VM): guía de alta disponibilidad

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver en máquinas virtuales de Windows (VM): guía de implementación de DBMS) 
[dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Almacenamiento en caché de máquinas virtuales y VHD) 
[dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) 
[dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage) 
[dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estructura de una implementación de RDBMS) 
[dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidad y recuperación ante desastres con Azure Virtual Machines) 
[dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 and later) 
[dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 y versiones anteriores) 
[dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso de imágenes de SQL Server fuera de Microsoft Azure Marketplace) 
[dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Resumen general de SQL Server para SAP en Azure) 
[dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Detalles para SQL Server RDBMS) 
[dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuración de almacenamiento) 
[dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Copias de seguridad y restauración) 
[dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Consideraciones de rendimiento para copias de seguridad y restauraciones) 
[dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Otros) 
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación) 
[deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP) 
[deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implementación de una máquina virtual con una imagen personalizada) 
[deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP) 
[deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP) 
[deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP) 
[deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure) 
[deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implementación de cmdlets de Azure PowerShell) 
[deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Descarga e implementación de cmdlets de PowerShell para SAP) 
[deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Unión de una máquina virtual a un dominio local [solo Windows]) 
[deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) 
[deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Descarga, instalación y habilitación del agente de máquina virtual de Azure) 
[deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) 
[deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI de Azure) 
[deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar la extensión de supervisión mejorada de Azure para SAP) 
[deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Comprobación de preparación de la supervisión mejorada de Azure para SAP) 
[deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Comprobación de estado de la configuración de la infraestructura de supervisión de Azure) 
[deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP)

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

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

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

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver en máquinas virtuales (VM) Windows: guía de planeamiento e implementación) 
[planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos) 
[planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta disponibilidad (HA) y recuperación ante desastres (DR) para la ejecución de SAP NetWeaver en Azure Virtual Machines) 
[planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidad en los servidores de aplicaciones de SAP) 
[planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso de Autostart en las instancias de SAP) 
[planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente) 
[planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre sitios locales: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local) 
[planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiones de Azure) 
[planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Dominios de error) 
[planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Dominios de actualización) 
[planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidad de Azure) 
[planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (El concepto de máquina virtual de Azure) 
[planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage) 
[planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado) 
[planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implementación de una máquina virtual con una imagen específica del cliente) 
[planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado) 
[planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP) 
[planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparación de máquinas virtuales con SAP para Azure) 
[planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferencia entre un disco y una imagen de Azure) 
[planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carga de un VHD desde una instalación local a Azure) 
[planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia de discos entre cuentas de Azure Storage) 
[planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estructura de VM/VHD para implementaciones de SAP) 
[planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Configuración de montaje automático para discos conectados) 
[planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Escenario de demostración/aprendizaje de máquina virtual única con SAP NetWeaver) 
[planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceptos de implementación solo en la nube de instancias de SAP) 
[planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solución de supervisión de Azure para SAP) 
[planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium Storage)

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

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa (Alta disponibilidad de SAP NetWeaver en infraestructura como servicio [IaaS] de Azure) 
[sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e (Alta disponibilidad en los servidores de aplicaciones de SAP) 
[sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db (Instancia de ASCS/SCS de SAP de alta disponibilidad) 
[sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 (Alta disponibilidad para la instancia de ASCS/SCS de SAP con Clústeres de conmutación por error de Windows Server en Azure) 
[sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 (Alta disponibilidad para la instancia de DBMS) 
[sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 (Escenarios de implementación completa de alta disponibilidad) 
[sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf (Preparación de la infraestructura) 
[sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 (Implementación de máquinas virtuales con conectividad de red corporativa (entre locales) para uso en producción) 
[sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 (Implementación solo en la nube de instancias de SAP para pruebas o demostración) 
[sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Azure Virtual Network) 
[sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e (Direcciones IP de DNS) 
[sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f (Nombres de host y direcciones IP estáticas para la instancia en clúster de ASCS/SCS de SAP y la instancia en clúster de DBMS) 
[sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 (Configuración de direcciones IP estáticas para las máquinas virtuales SAP) 
[sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e (Creación de una dirección IP estática para el equilibrador de carga interno) 
[sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c (Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure) 
[sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 (Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure) 
[sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c (Incorporación de máquinas virtuales de Windows al dominio) 
[sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 (Incorporación de entradas del Registro en ambos nodos del clúster usados para la instancia de ASCS/SCS de SAP) 
[sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab (Configuración del clúster de Clústeres de conmutación por error de Windows Server para la instancia de ASCS/SCS de SAP) 
[sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 (Recopilación de nodos del clúster en la configuración de clúster) 
[sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca (Configuración del testigo del recurso compartido de archivos de clúster) 
[sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 (Creación de un recurso compartido de archivos) 
[sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d (Configuración del cuórum de testigo del recurso compartido de archivos en el Administrador de clústeres de conmutación por error) 
[sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 (Instalación de SIOS DataKeeper Cluster Edition para un disco compartido de clúster de ASCS/SCS de SAP) 
[sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 (Incorporación de la característica .NET Framework 3.5) 
[sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 (Instalación de SIOS DataKeeper) 
[sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 (Configuración de SIOS DataKeeper) 
[sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b (Instalación del sistema SAP NetWeaver) 
[sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 (Instalación de SAP con una instancia de ASCS/SCS de alta disponibilidad) 
[sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 (Creación de un nombre de host virtual para la instancia de ASCS/SCS de SAP en clúster) 
[sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 (Instalación del primer nodo de clúster de SAP) 
[sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 (Modificación del perfil SAP de la instancia de ASCS/SCS) 
[sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 (Incorporación del puerto de sondeo) 
[sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 (Instalación de la instancia de base de datos) 
[sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 (Instalación del segundo nodo del clúster) 
[sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a (Cambio del tipo de inicio del servicio de Windows para la instancia de ERS de SAP) 
[sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 (Instalación del servidor de aplicaciones principal (PAS) de SAP) 
[sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 (Instalación del servidor de aplicaciones adicional (AAS) de SAP) 
[sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 (Prueba de la conmutación por error de la instancia de ASCS/SCS de SAP y de la replicación de SIOS) 
[sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 (La instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster A) 
[sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 (Proceso de conmutación por error del nodo A al nodo B) 
[sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 (La instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster B))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
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


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
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
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
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
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
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
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


Azure Virtual Machines es la solución para organizaciones que necesitan recursos de red, almacenamiento y proceso, en un tiempo mínimo y sin ciclos de adquisición prolongados. Puede usar Azure Virtual Machines para implementar aplicaciones clásicas como ABAP basado en SAP NetWeaver, Java y la pila de ABAP+Java. Amplíe la confiabilidad y disponibilidad sin recursos locales adicionales. Como Azure Virtual Machines admite la conectividad entre locales, la organización puede integrar Azure Virtual Machines a los dominios locales, las nubes privadas y la infraestructura de sistema SAP.

En este artículo analizaremos los pasos que puede llevar a cabo para implementar sistemas SAP de alta disponibilidad en Azure con el uso del nuevo modelo de implementación de Azure Resource Manager. Le guiaremos por estas tareas principales:

- Encontrar las notas y guías de instalación de SAP adecuadas, que aparecen en la sección [Recursos][sap-ha-guide-2]. Este documento complementa la documentación sobre la instalación de SAP y las notas de SAP, que son los recursos principales que le ayudan a instalar e implementar software de SAP en plataformas específicas.

- Conocer las diferencias entre el modelo de implementación clásica de Azure y el modelo de implementación de Azure Resource Manager.

- Obtener información sobre los modos de cuórum de Clústeres de conmutación por error de Windows Server, a fin de poder seleccionar el modelo adecuado para la implementación de Azure.

- Obtener información sobre el almacenamiento compartido de Clústeres de conmutación por error de Windows Server en los servicios de Azure.

- Obtener información sobre cómo proteger en Azure los componentes con un único punto de error, como ABAP SAP Central Services (ASCS)/SAP Central Services (SCS) y sistemas de administración de bases de datos (DBMS), además de componentes redundantes como los servidores de aplicaciones de SAP.

- Seguir un ejemplo detallado de la instalación y configuración de un sistema SAP de alta disponibilidad en un clúster de Clústeres de conmutación por error de Windows Server mediante Azure como plataforma, con Azure Resource Manager.

- Obtener información sobre los pasos adicionales que se necesitan para usar Clústeres de conmutación por error de Windows Server en Azure, pero que no son necesarios en una implementación local.

Para simplificar la implementación y configuración, en este artículo usamos las nuevas plantillas de Resource Manager de alta disponibilidad y de tres niveles de SAP. Las plantillas automatizan la implementación de toda la infraestructura que necesita en un sistema SAP de alta disponibilidad. La infraestructura también admite el ajuste de tamaño de SAPS del sistema SAP.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="a-name217c547955954cd8870d15ab00d4f84ca-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Requisitos previos

Antes de comenzar, asegúrese de cumplir los requisitos previos que se describen en las secciones siguientes. Además, asegúrese de revisar todos los recursos que aparecen en la sección [Recursos][sap-ha-guide-2].

En este artículo usamos las plantillas de Azure Resource Manager para [SAP NetWeaver de tres niveles](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Consulte [Plantillas de Azure Resource Manager para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) para ver información general útil sobre las plantillas.

## <a name="a-name42b8f6007ba34606b8a553c4f026da08a-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos

Estas guías también se refiere a las implementaciones de SAP en Azure:

- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de planeamiento e implementación][planning-guide]
- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación][deployment-guide]
- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación de DBMS][dbms-guide]
- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de alta disponibilidad [esta guía] ][sap-ha-guide]

> [AZURE.NOTE] Siempre que sea posible, aparece un vínculo a la guía de instalación de SAP a la que se hace referencia (consulte [Guías de instalación de SAP][sap-installation-guides]). Para consultar los requisitos previos e información sobre el proceso de instalación, se recomienda leer cuidadosamente las guías de instalación de SAP NetWeaver. En este artículo solo se abarcan tareas específicas para los sistemas basados en SAP NetWeaver que puede usar con Azure Virtual Machines.

Estas notas de SAP están relacionadas con el tema de SAP en Azure:

| Número de nota   | Título                                                    
| ------------- |----------------------------------------------------------
| [1928533]       | SAP Applications on Azure: Supported Products and Sizing (Aplicaciones SAP en Azure: productos y tamaños compatibles)
| [2015553]       | SAP on Microsoft Azure: Support Prerequisites (SAP en Microsoft Azure: requisitos previos de compatibilidad)         
| [1999351]       | Enhanced Azure Monitoring for SAP (Supervisión mejorada de Azure para SAP)                        
| [2178632]       | Key Monitoring Metrics for SAP on Microsoft Azure (Métricas de supervisión clave para SAP en Microsoft Azure)        
| [1999351]       | Virtualization on Windows: Enhanced Monitoring (Virtualización en Windows: supervisión mejorada)      


Obtenga más información sobre las [limitaciones de las suscripciones de Azure][azure-subscription-service-limits-subscription], incluidas las limitaciones máximas y predeterminadas generales.

## <a name="a-name42156640c601cf45a9b2254baa678b24f1ahighavailability-sap-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de alta disponibilidad con el modelo de implementación de Azure Resource Manager en comparación con el modelo de implementación clásica

El modelo de implementación de Azure Resource Manager y el modelo de implementación clásica son distintos de dos maneras:

- Grupos de recursos
- Requisitos de agrupación en clústeres

### <a name="a-namef76af27319934d83b12d65deeae23686a-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
En Azure Resource Manager, puede usar los grupos de recursos para administrar todos los recursos de aplicación de la suscripción de Azure. En un enfoque integrado, todos los recursos de un grupo de recursos tienen el mismo ciclo de vida. Por ejemplo, todos los recursos se crean en el mismo momento y se eliminan de la misma manera. Puede obtener más información sobre los [grupos de recursos](resource-group-overview.md#resource-groups).

### <a name="a-name3e85fbe084b1489287afd9b65ff91860a-clustering-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Agrupación en clústeres con el modelo de Azure Resource Manager en comparación con el modelo de implementación clásica

En el modelo de Azure Resource Manager, no necesita un servicio en la nube a fin de usar el balanceo de carga interno de Azure para obtener alta disponibilidad.

Si desea usar el modelo clásico de Azure, siga los procedimientos que se describen en [SAP NetWeaver in Azure: Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver en Azure: agrupación de clústeres de instancias de ASCS/SCS de SAP mediante Clústeres de conmutación por error de Windows Server en Azure con SIOS DataKeeper).

> [AZURE.NOTE] Recomendamos encarecidamente usar el modelo de implementación de Azure Resource Manager para las instalaciones de SAP. Ofrece muchas ventajas que no están disponibles en el modelo de implementación clásica. Puede obtener más información sobre los [modelos de implementación] de Azure[virtual-machines-azure-resource-manager-architecture-benefits-arm].   

## <a name="a-name8ecf3ba067c044959c14feec1a2255b7a-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Clústeres de conmutación por error de Windows Server

Clústeres de conmutación por error de Windows Server es la base de una instalación de ASCS/SCS de SAP de alta disponibilidad y DBMS en Windows.

Un clúster de conmutación por error es un grupo de 1+n servidores independientes (nodos) que colaboran para aumentar la disponibilidad de aplicaciones y servicios. Si se produce un error de nodo, Clústeres de conmutación por error de Windows Server calcula el número de errores que se pueden producir y mantiene un clúster en buen estado para proporcionar las aplicaciones y servicios definidos. Para ello, puede elegir entre distintos modos de cuórum.

### <a name="a-name1a3c5408b16846d699f54219ad1b1ff2a-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos de cuórum

Puede elegir entre cuatro modos de cuórum cuando use Clústeres de conmutación por error de Windows Server:

- **Mayoría de nodo**. Cada nodo del clúster puede votar. El clúster funciona solamente con la mayoría de votos, es decir, con más de la mitad. Se recomienda esta opción para los clústeres con un número impar de nodos. Por ejemplo, 3 nodos de un clúster de 7 nodos pueden presentar un error y el clúster todavía obtendrá la mayoría y se seguirá ejecutando.  

- **Mayoría de disco y nodo**. Cada nodo y disco designado (un testigo de disco) en el almacenamiento del clúster puede votar siempre que esté disponible y comunicándose. El clúster funciona solamente con la mayoría de los votos, es decir, con más de la mitad. Este modo resulta útil en un entorno de clúster con un número par de nodos. Si la mitad de los nodos y el disco están en línea, el clúster permanece en buen estado.

- **Mayoría de recurso compartido de archivos y nodo**. Cada nodo más un recurso compartido de archivos designado (un testigo de recurso de archivos) que el administrador crea puede votar, independientemente de si está disponible y comunicándose. El clúster funciona solamente con la mayoría de los votos, es decir, con más de la mitad. Este modo resulta útil en un entorno de clúster con un número par de nodos. Es similar al modo Mayoría de disco y nodo, pero usa un recurso compartido de archivos testigo en lugar de un disco testigo. Este modo es fácil de implementar pero, si el recurso compartido de archivos en sí no es de alta disponibilidad, es posible que se convierta en un único punto de error.

- **Sin mayoría: solo disco**. El clúster tiene cuórum si hay un nodo disponible y comunicándose con un disco específico en el almacenamiento del clúster. Los únicos nodos que pueden unirse al clúster son aquellos que estén comunicándose con ese disco. No se recomienda usar este modo.
 
## <a name="a-namefdfee8756e66483aa34314bbaee33275a-windows-server-failover-clustering-onpremises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Clústeres de conmutación por error de Windows Server local
El ejemplo de la figura 1 muestra un clúster de dos nodos. Si se produce un error en la conexión de red entre los nodos pero ambos nodos se mantienen activos y en ejecución, un recurso compartido de archivos o un disco de cuórum determinará el nodo que seguirá proporcionando las aplicaciones y servicios del clúster. El nodo que tiene acceso al recurso compartido de archivos o disco de cuórum es el nodo que garantiza la continuación de los servicios.

Debido a que este ejemplo usa un clúster de dos nodos, usamos el modo de cuórum Mayoría de recurso compartido de archivos y nodo. Otra opción válida es el modo Mayoría de disco y nodo. En un entorno de producción, se recomienda usar un disco de cuórum. Puede usar tecnología de sistema de almacenamiento y red para hacerlo de alta disponibilidad.

![Figura 1: Ejemplo de una configuración de Clústeres de conmutación por error de Windows Server para ASCS/SCS de SAP en Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Ejemplo de una configuración de Clústeres de conmutación por error de Windows Server para ASCS/SCS de SAP en Azure_

### <a name="a-namebe21cf3efb01402b995554fbecf66592a-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Almacenamiento compartido

En la figura 1 también se muestra un clúster de almacenamiento compartido de dos nodos. En un clúster de almacenamiento compartido local, todos los nodos del clúster detectan el almacenamiento compartido. Un mecanismo de bloqueo protege los datos contra daños. Todos los nodos pueden detectar si se produce un error en otro nodo. Si se produce un error en un nodo, el nodo restante asume la propiedad de los recursos de almacenamiento y garantiza la disponibilidad de los servicios.

> [AZURE.NOTE] No necesita discos compartidos para obtener alta disponibilidad con algunas aplicaciones de DBMS, como SQL Server. SQL Server Always On replica archivos de registro y datos de DBMS desde el disco local de un nodo del clúster hasta el disco local del otro nodo del clúster. En ese caso, la configuración de clúster de Windows no necesita un disco compartido.

### <a name="a-nameff7a9a062bc54b20860a46cdb44669cda-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Redes y resolución de nombres

Los equipos cliente se comunican con el clúster a través de una dirección IP virtual y un nombre de host virtual que el servidor DNS proporciona. Los nodos locales y el servidor DNS pueden administrar varias direcciones IP.

En una configuración típica, puede usar dos o más conexiones de red:

- Una conexión dedicada al almacenamiento.
- Una conexión de red interna del clúster para el latido.
- Una red pública que los clientes usan para conectarse al clúster.

## <a name="a-name2ddba413a7f54e4e9a5187908879c10aa-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Clústeres de conmutación de Windows Server en Azure

En comparación con las implementaciones de nube privada o sin sistema operativo, Azure Virtual Machines requiere pasos adicionales para configurar Clústeres de conmutación por error de Windows Server. Cuando compila un disco de clúster compartido, deberá establecer varias direcciones IP y nombres de host virtual para la instancia de ASCS/SCS de SAP.

En este artículo se analizan los conceptos clave y los pasos adicionales que se requieren para compilar un clúster de servicios centrales de alta disponibilidad de SAP en Azure. Mostramos cómo configurar la herramienta de terceros SIOS DataKeeper y configurar el equilibrador de carga interno de Azure. Puede usar estas herramientas para crear un clúster de conmutación por error de Windows con un testigo de recurso compartido de archivos en Azure.


![Figura 2: Configuración de Clústeres de conmutación por error de Windows Server en Azure sin un disco compartido][sap-ha-guide-figure-1001]

_**Figura 2:** Configuración de Clústeres de conmutación por error de Windows Server en Azure sin un disco compartido_


### <a name="a-name1a464091922b48d79d087cecf757f341a-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco compartido en Azure con SIOS DataKeeper

Se necesita almacenamiento compartido de clúster para una instancia de ASCS/SCS de SAP de alta disponibilidad. A partir de septiembre de 2016, Azure no ofrece almacenamiento compartido que puede usar para crear un clúster de almacenamiento compartido. Puede usar el software de terceros SIOS DataKeeper Cluster Edition para crear un almacenamiento reflejado que simula el almacenamiento compartido de clúster. La solución SIOS proporciona replicación sincrónica de datos en tiempo real. Este es el procedimiento para crear un recurso de disco compartido para un clúster:

1. Conecte un disco duro virtual (VHD) de Azure adicional a cada una de las máquinas virtuales de una configuración de clúster de Windows.
2. Ejecute SIOS DataKeeper Cluster Edition en ambos nodos de la máquina virtual.
3. Configure SIOS DataKeeper Cluster Edition de forma que refleje el contenido del volumen del VHD adicional conectado desde la máquina virtual de origen al volumen conectado del VHD adicional de la máquina virtual de destino. SIOS DataKeeper abstrae los volúmenes locales de origen y de destino, y los presenta a Clústeres de conmutación por error de Windows Server como un disco compartido.

Obtenga más información sobre [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

 ![Figura 3: Configuración de Clústeres de conmutación por error de Windows Server en Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Configuración de Clústeres de conmutación por error de Windows Server en Azure con SIOS DataKeeper_

> [AZURE.NOTE] No necesita discos compartidos para obtener alta disponibilidad con algunos productos de DBMS, como SQL Server. SQL Server Always On replica archivos de registro y datos de DBMS desde el disco local de un nodo del clúster hasta el disco local del otro nodo del clúster. En este caso, la configuración de clúster de Windows no necesita un disco compartido.

### <a name="a-name44641e18a94e431f95ff303ab65e0bcba-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolución de nombres en Azure

 La plataforma en la nube de Azure no ofrece la opción de configurar direcciones IP virtuales, como direcciones IP flotantes. Debido a esto, necesita una solución alternativa para configurar una dirección IP virtual que se comunique con el recurso de clúster en la nube.
Azure tiene un equilibrador de carga interno en el servicio Azure Load Balancer. Con el equilibrador de carga interno, los clientes se comunican con el clúster a través de la dirección IP virtual del clúster.
Necesita implementar el equilibrador de carga interno en el grupo de recursos que contiene los nodos del clúster. Luego, configure todas las reglas de enrutamiento de puerto necesarias con los puertos de sondeo del equilibrador de carga interno.
Los clientes se pueden conectar por medio del nombre de host virtual. El servidor DNS resuelve la dirección IP del clúster y el equilibrador de carga interno controla el enrutamiento de puerto al nodo activo del clúster.

## <a name="a-name2e3fec50241e441b87080b1864f66dfaa-highavailability-sap-netweaver-in-azure-infrastructureasaservice-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver de alta disponibilidad en infraestructura como servicio (IaaS) de Azure

Para obtener alta disponibilidad en las aplicaciones de SAP, como para los componentes de software de SAP, necesita proteger los componentes siguientes. Esto se analiza con mayor detalle en [SAP NetWeaver en máquinas virtuales (VM) de Windows: guía de planeamiento e implementación][planning-guide-11].

- servidores de aplicaciones de SAP,
- instancia de SAP ASCS/SCS,
- servidor DBMS

### <a name="a-name93faa747907e440ab00a1ae0a89b1c0ea-highavailability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Alta disponibilidad en los servidores de aplicaciones de SAP

Por lo general, no se necesita una solución de alta disponibilidad específica para las instancias de diálogo y servidores de aplicaciones de SAP. Obtiene alta disponibilidad mediante redundancia y configurará varias instancias de diálogo en distintas instancias de Azure Virtual Machines. Debe tener, como mínimo, 2 instancias de aplicaciones de SAP instaladas en 2 instancias de Azure Virtual Machines.

![Figura 4: Alta disponibilidad en los servidores de aplicaciones de SAP][sap-ha-guide-figure-2000]

_**Figura 4:** Alta disponibilidad en los servidores de aplicaciones de SAP_

Debe colocar todas las máquinas virtuales que hospedan servidores de aplicaciones de SAP en el mismo conjunto de disponibilidad de Azure. Un conjunto de disponibilidad de Azure garantiza lo siguiente:

- Todas las máquinas virtuales forman parte del mismo dominio de actualización. Por ejemplo, un dominio de actualización garantiza que las máquinas virtuales no se actualicen al mismo tiempo durante un tiempo de inactividad de mantenimiento planeado.
- Todas las máquinas virtuales forman parte del mismo dominio de error. Por ejemplo, un dominio de error garantiza que las máquinas virtuales estén implementadas de tal manera que ningún único punto de error afecte la disponibilidad de todas las máquinas virtuales.

Obtenga información sobre cómo [administrar la disponibilidad de las máquinas virtuales][virtual-machines-manage-availability].

Como la cuenta de almacenamiento de Azure es un punto único de error potencial, es importante tener, como mínimo, 2 cuentas de almacenamiento de Azure, en los cuales hay distribuidas, al menos, 2 máquinas virtuales. En una configuración ideal, cada máquina virtual que ejecuta una instancia de diálogo de SAP estaría implementada en una cuenta de almacenamiento distinta.

### <a name="a-namef559c285ee684eecadd1f60fe7b978dba-highavailability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instancia de ASCS/SCS de SAP de alta disponibilidad

![Figura 5: Instancia de ASCS/SCS de SAP de alta disponibilidad][sap-ha-guide-figure-2001]

_**Figura 5:** Instancia de ASCS/SCS de SAP de alta disponibilidad_


#### <a name="a-nameb5b1fd0b1db44d499162de07a0132a51a-highavailability-sap-ascsscs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Instancia de ASCS/SCS de SAP de alta disponibilidad con Clústeres de conmutación por error de Windows Server en Azure

En comparación con las implementaciones de nube privada o sin sistema operativo, Azure Virtual Machines requiere pasos adicionales para configurar Clústeres de conmutación por error de Windows Server. A fin de compilar un clúster de conmutación por error de Windows, necesita un disco de clúster compartido, varias direcciones IP, varios nombres de host virtual y un equilibrador de carga interno de Azure para la agrupación en clústeres de una instancia de ASCS/SCS de SAP.

Analizaremos esto con más detalle más adelante en el artículo.

![Figura 6: Clústeres de conmutación por error de Windows Server para una configuración de ASCS/SCS de SAP en Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Clústeres de conmutación por error de Windows Server para una configuración de ASCS/SCS de SAP en Azure con SIOS DataKeeper_


### <a name="a-nameddd878a09c2f4b8e896826ce60be1027a-highavailability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Alta disponibilidad para la instancia de DBMS

DBMS también es un único punto de contacto de un sistema SAP. Debe protegerlo con una solución de alta disponibilidad. La figura 7 muestra un ejemplo de una solución de alta disponibilidad de SQL Server AlwaysOn en Azure mediante el uso de Clústeres de conmutación por error de Windows Server y el equilibrador de carga interno de Azure. SQL Server AlwaysOn replica los archivos de datos y de registro de DBMS con su propia replicación DBMS. En este caso, no se necesitan discos compartidos de clúster, lo que simplifica toda la configuración.

![Figura 7: Ejemplo de DBMS de SAP de alta disponibilidad: SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 7:** Ejemplo de DBMS de SAP de alta disponibilidad: SQL Server AlwaysOn_


Para más información sobre cómo agrupar el clústeres SQL Server en Azure con el modelo de implementación de Azure Resource Manager, consulte estos artículos:

- [Configuración manual de grupos de disponibilidad AlwaysOn en Azure Virtual Machines con Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn en Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="a-name045252ed02774fc88f46c5a29694a816a-endtoend-highavailability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Escenarios de implementación completa de alta disponibilidad

En la figura 8 se muestra un ejemplo de una arquitectura de SAP NetWeaver de alta disponibilidad en Azure. En este escenario, usamos un clúster dedicado para la instancia de ASCS/SCS de SAP y otro para DBMS.

![Figura 8: Plantilla 1 de arquitectura de alta disponibilidad de SAP, con un clúster dedicado para ASCS/SCS y otro para la instancia de DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Plantilla 1 de arquitectura de alta disponibilidad: clústeres dedicados para ASCS/SCS y DBMS_

## <a name="a-name78092dbe165b454c92f54972bdbef9bfa-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparación de la infraestructura

Las plantillas de Azure Resource Manager para SAP ayudan a simplificar la implementación de los recursos necesarios.

Las plantillas de 3 niveles también admiten escenarios de alta disponibilidad, como Plantilla 1 de arquitectura, que tiene 2 clústeres. Cada clúster es un único punto de error de SAP para ASCS/SCS de SAP y DBMS.

Aquí puede obtener las plantillas de Azure Resource Manager para el escenario 1:

- [Imagen de Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
- [Imagen personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Cuando selecciona la imagen de Marketplace de 3 niveles de SAP, se muestra esta pantalla en Azure Portal:

![Figura 9: Especificación de los parámetros de Azure Resource Manager para alta disponibilidad de SAP][sap-ha-guide-figure-3000]

_**Figura 9:** Especificación de los parámetros de Azure Resource Manager para alta disponibilidad de SAP_


En **SYSTEMAVAILABILITY**, seleccione **HA**.

Las plantillas crean:

- **Máquinas virtuales**:
    - Máquinas virtuales de Servidor de aplicaciones de SAP: <*SIDSistemaSAP*>-di-<*Número*>
    - Máquinas virtuales de clúster de ASCS/SCS: <*SIDSistemaSAP*>-ascs-<*Número*>
    - Un clúster de DBMS: <*SIDSistemaSAP*>-db-<*Número*>
- **Tarjetas de red para todas las máquinas virtuales con direcciones IP asociadas**:
    - <*SIDSistemaSAP*>-nic-di-<*Número*>
    - <*SIDSistemaSAP*>-nic-ascs-<*Número*>
    - <*SIDSistemaSAP*>-nic-db-<*Número*>
- **Cuentas de almacenamiento de Azure**
- **Grupos de disponibilidad** para:
    - Máquinas virtuales de Servidor de aplicaciones de SAP: <*SIDSistemaSAP*>-avset-di
    - Máquinas virtuales de clúster de ASCS/SCS de SAP: <*SIDSistemaSAP*>-avset-ascs
    - Máquinas virtuales de clúster de DBMS: <*SIDSistemaSAP*>-avset-db
- **Equilibrador de carga interno de Azure**:
  - Con todos los puertos para la dirección IP y la instancia de ASCS/SCS <*SIDSistemaSAP*>-lb-ascs
  - Con todos los puertos para la dirección IP y DBMS de SQL Server <*SIDSistemaSAP*>-lb-db
- **Grupo de seguridad de red**: <*SIDSistemaSAP*>-nsg-ascs-0  
    - Con un puerto de Protocolo de escritorio remoto (RDP) externo abierto para la máquina virtual <*SIDSistemaSAP*>-ascs-0


> [AZURE.NOTE] De manera predeterminada, todas las direcciones IP de las tarjetas de red y los equilibradores de carga internos de Azure son **dinámicos**. Cámbielas a direcciones IP **estáticas**. Esto se describe más adelante en el artículo.


### <a name="a-namec87a8d3fb1dc4d2fb23cda4b72977489a-deploy-virtual-machines-with-corporate-network-connectivity-crosspremises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implementación de máquinas virtuales con conectividad de red corporativa (entre locales) para uso en producción

Para los sistemas de producción de SAP, implemente máquinas virtuales de Azure con [conectividad de red corporativa (entre locales)][planning-guide-2.2] mediante el uso de VPN de sitio a sitio de Azure o Azure ExpressRoute.

> [AZURE.NOTE] Puede usar la instancia de Azure Virtual Network. La red virtual y la subred ya se crearon y están preparadas.

En **NEWOREXISTINGSUBNET**, seleccione **existing** (existente).

En **SUBNETID**, agregue la cadena completa de SubnetID de la red de Azure, donde planea implementar las máquinas virtuales de Azure.

Ejecute este comando de PowerShell para obtener una lista de todas las subredes de red de Azure:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

El campo **ID** muestra el valor de **SUBNETID**.

Para recuperar una lista de todos los valores de **SUBNETID**, use este comando de PowerShell:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

El aspecto del identificador **SUBNETID** es similar al siguiente:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="a-name7fe9af0e3cce495ba5ecdcb4d8e0a310a-cloudonly-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implementación solo en la nube de instancias de SAP para prueba o demostración

También puede implementar el sistema de SAP de alta disponibilidad en un modelo de implementación solo en la nube.

Este tipo de implementación se usa principalmente para demostrar o probar casos de uso. No es idóneo para los casos de uso de producción.

En **NEWOREXISTINGSUBNET**, seleccione **new** (nuevo). Deje vacío el campo **SUBNETID**.

La plantilla de Azure Resource Manager para SAP crea automáticamente la subred y red virtual de Azure.

> [AZURE.NOTE] También debe implementar, como mínimo, una máquina virtual dedicada para Active Directory y DNS en la misma instancia de Azure Virtual Network. La plantilla no crea estas máquinas virtuales.

### <a name="a-name47d5300aa83041d483dd1a0d1ffdbe6aa-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure Virtual Network

En nuestro ejemplo, el espacio de direcciones de la máquina virtual de Azure es 10.0.0.0/16. Hay una subred llamada **Subnet**, con un intervalo de direcciones de 10.0.0.0/24. Todas las máquinas virtuales y los equilibradores de carga internos están implementados en esta red virtual.

> [AZURE.NOTE] No haga ningún cambio en la configuración de red dentro del sistema operativo invitado. Esto incluye direcciones IP, servidores DNS y subred. Ajuste toda la configuración de red de Azure. El servicio Protocolo de configuración dinámica de host (DHCP) propaga la configuración.

### <a name="a-nameb22d7b3b434340ffa319097e13f62f9ea-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Direcciones IP de DNS

Asegúrese de que la opción **Servidores DNS** de la red virtual esté establecida en **DNS personalizado**.
Luego, seleccione la configuración según el tipo de red que tiene:

- [Conectividad de red corporativa (entre locales)][planning-guide-2.2]: agregue las direcciones IP de los servidores DNS locales.  

    Puede extender los servidores DNS locales a las máquinas virtuales que se ejecutan en Azure. En ese escenario, puede agregar las direcciones IP de las máquinas virtuales de Azure en las que ejecuta el servidor DNS.

-   [Implementación solo en la nube][planning-guide-2.1]: implemente una máquina virtual adicional en la misma instancia de Virtual Network que actúa como servidor DNS. Agregue las direcciones IP de las máquinas virtuales de Azure que configuró para ejecutar el servicio DNS.


![Figura 10: Configuración de servidores DNS para Azure Virtual Network][sap-ha-guide-figure-3001]

_**Figura 10:** Configuración de servidores DNS para Azure Virtual Network_

> [AZURE.NOTE] Si cambia las direcciones IP de los servidores DNS, debe reiniciar las máquinas virtuales de Azure para aplicar el cambio y propagar los nuevos servidores DNS.

En este ejemplo, el servicio DNS está instalado y configurado en estas máquinas virtuales de Windows:

| Rol de máquina virtual        | Nombre de host de máquina virtual | Nombre de tarjeta de red  | Dirección IP estática  
| ---------------|-------------|--------------------|-------------------
| Primer servidor DNS | domcontr-0  | pr1-nic-domcontr-0 | 10.0.0.10         
| Segundo servidor DNS | domcontr-1  | pr1-nic-domcontr-1 | 10.0.0.11         


### <a name="a-name9fbd43c05850496597262a921d85d73fa-host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nombres de host y direcciones IP estáticas para la instancia en clúster de ASCS/SCS de SAP y la instancia de clúster de DBMS

Para la implementación local, necesita estas direcciones IP y nombres de host reservados:

| Rol de nombre de host virtual                                                       | Nombre de host virtual | Dirección IP estática virtual
| ----------------------------------------------------------------------------|------------------|---------------------------
| Primer nombre de host virtual de clúster de ASCS/SCS de SAP (para la administración del clúster) | pr1-ascs-vir     | 10.0.0.42                 
| Nombre de host virtual de la instancia de ASCS/SCS de SAP                                  | pr1-ascs-sap     | 10.0.0.43             
| Segundo nombre de host virtual de clúster de DBMS de SAP (administración del clúster)     | pr1-dbms-vir     | 10.0.0.32                 

Cuando crea el clúster, crea los nombres de host virtual **pr1-ascs-vir** y **pr1-dbms-vir**, además de las direcciones IP asociadas que administrar el clúster mismo. El proceso se describe en [Recopilación de nodos del clúster en la configuración de clúster][sap-ha-guide-8.12.1].

Puede crear manualmente los otros 2 nombres de host virtual, **pr1-ascs-sap** y **pr1-dbms-sap**, y las direcciones IP asociadas en el servidor DNS. La instancia de ASCS/SCS de SAP en clúster y la instancia de DBMS en clúster usan estos recursos. Esto se describe en [Creación de un nombre de host virtual para la instancia de ASCS/SCS de SAP en clúster][sap-ha-guide-9.1.1].

### <a name="a-name84c019fe8c584dac9e54173efd4b2c30a-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Configuración de direcciones IP estáticas para las máquinas virtuales SAP

Después de implementar las máquinas virtuales que usará en el clúster, deberá establecer direcciones IP estáticas para todas las máquinas virtuales. Debe hacerlo en la configuración de Azure Virtual Network y no en el sistema operativo invitado.

Una forma de establecer una dirección IP estática es mediante Azure Portal. En Azure Portal, vaya a **Grupo de recursos** > **Tarjeta de red** > **Configuración** > **Dirección IP**.

En **Asignación**, seleccione **Estática**. En el campo **Dirección IP**, escriba la dirección IP que desea usar.

> [AZURE.NOTE] Si cambia la dirección IP de la tarjeta de red, deberá reiniciar las máquinas virtuales de Azure para aplicar el cambio.  


![Figura 11: Establecimiento de direcciones IP estáticas para la tarjeta de red de cada máquina virtual][sap-ha-guide-figure-3002]

_**Figura 11:** Establecimiento de direcciones IP estáticas para la tarjeta de red de cada máquina virtual_

Repita este paso para todas las interfaces de red, es decir, para todas las máquinas virtuales, incluidas las que desea usar para Active Directory/servicio DNS.

En este ejemplo, aparecen estas máquinas virtuales y direcciones IP estáticas:

| Rol de máquina virtual                                 | Nombre de host de máquina virtual  | Nombre de tarjeta de red  | Dirección IP estática  
| ----------------------------------------|--------------|--------------------|-------------------
| Primer servidor de aplicaciones de SAP              | pr1-di-0     | pr1-nic-di-0       | 10.0.0.50         
| Segundo servidor de aplicaciones de SAP              | pr1-di-1     | pr1-nic-di-1       | 10.0.0.51         
| ...                                     | ...          | ...                | ...               
| Último servidor de aplicaciones de SAP             | pr1-di-5     | pr1-nic-di-5       | 10.0.0.55         
| Primer nodo de clúster para la instancia de ASCS/SCS  | pr1-ascs-0   | pr1-nic-ascs-0     | 10.0.0.40         
| Segundo nodo de clúster para la instancia de ASCS/SCS  | pr1-ascs-1   | pr1-nic-ascs-1     | 10.0.0.41         
| Primer nodo de clúster para la instancia de DBMS      | pr1-db-0     | pr1-nic-db-0       | 10.0.0.30         
| Segundo nodo de clúster para la instancia de DBMS      | pr1-db-1     | pr1-nic-db-1       | 10.0.0.31         

### <a name="a-name7a8f3e9b062440519e41b73fff816a9ea-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Configuración de una dirección IP estática para el equilibrador de carga interno

La plantilla de Azure Resource Manager para SAP crea un equilibrador de carga interno de Azure que se usa para el clúster de instancia de ASCS/SCS de SAP y el clúster de DBMS.

En la implementación inicial se establece la dirección IP del equilibrador de carga interno como **Dinámica**. Es importante cambiar la dirección IP a **Estática**.

En este ejemplo, aparecen 2 equilibradores de carga internos de Azure que tienen estas direcciones IP estáticas:

| Rol del equilibrador de carga interno de Azure             | Nombre del equilibrador de carga interno de Azure | Dirección IP estática
| ---------------------------|----------------|-------------------
| Equilibrador de carga interno de instancia de ASCS/SCS de SAP  | pr1-lb-ascs    | 10.0.0.43         
| Equilibrador de carga interno de DBMS de SAP               | pr1-lb-dbms    | 10.0.0.33         


> [AZURE.NOTE] La dirección IP del nombre de host virtual de la instancia de ASCS/SCS de SAP es la misma dirección IP del equilibrador de carga interno de ASCS/SCS de SAP, pr1-lb-ascs.
La dirección IP del nombre virtual de la instancia de DBMS es la misma dirección IP del equilibrador de carga interno de DBMS, pr1-lb-dbms.

En este ejemplo, establecemos la dirección IP del equilibrador de carga interno **pr1-lb-ascs** en la dirección IP del nombre de host externo de la instancia de ASCS/SCS de SAP (**10.0.0.43** en el ejemplo).

![Figura 12: Establecimiento de direcciones IP estáticas para el equilibrador de carga interno de la instancia de ASCS/SCS de SAP][sap-ha-guide-figure-3003]

_**Figura 12:** Establecimiento de direcciones IP estáticas para el equilibrador de carga interno de la instancia de ASCS/SCS de SAP_

Establezca la dirección IP del equilibrador de carga interno **pr1-lb-dbms** en la dirección IP del nombre de host virtual de la instancia de DBMS (**10.0.0.33** en el ejemplo).

### <a name="a-namef19bd997154d4583a46e7f5a69d0153ca-default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure

La plantilla de Azure Resource Manager para SAP crea los puertos que necesita:

- Una instancia de ASCS ABAP con el número de instancia predeterminado **00**
- Una instancia de SCS de Java con el número de instancia predeterminado **01**

Cuando instala la instancia de ASCS/SCS de SAP, debe usar el número de instancia predeterminado 00 para la instancia de ASCS ABAP y el número de instancia predeterminado 01 para la instancia de SCS de Java.

A continuación, cree estos puntos de conexión de equilibrio de carga interno necesarios para los puertos de ASCS ABAP de SAP NetWeaver:

| Nombre de regla de equilibrio de carga/servicio           | Números de puerto predeterminados | Puertos concretos para (instancia de ASCS con el número de instancia 00) (ERS con 10)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Enqueue Server/ _lbrule3200_                | 32<*NúmeroInstancia*>  | 3200                                                                     
| ABAP Message Server/ _lbrule3600_           | 36<*NúmeroInstancia*>  | 3600                                                                     
| Internal ABAP Message/ _lbrule3900_         | 39<*NúmeroInstancia*>  | 3900                                                                     
| Message Server HTTP/ _Lbrule8100_           | 81<*NúmeroInstancia*>  | 8100                                                                     
| SAP Start Service ASCS HTTP/ _Lbrule50013_  | 5<*NúmeroInstancia*>13 | 50013                                                                    
| SAP Start Service ASCS HTTPS/ _Lbrule50014_ | 5<*NúmeroInstancia*>14 | 50014                                                                    
| Enqueue Replication/ _Lbrule50016_          | 5<*NúmeroInstancia*>16 | 50016                                                                    
| SAP Start Service ERS HTTP/ _Lbrule51013_     | 5<*NúmeroInstancia*>13 | 51013                                                                    
| SAP Start Service ERS HTTP/ _Lbrule51014_     | 5<*NúmeroInstancia*>14 | 51014                                                                    
| Win RM/ _Lbrule5985_                          |                       | 5985                                                                     
| File Share/ _Lbrule445_                       |                       | 445                                                                      

_**Tabla 1:** Números de puerto de las instancias de ASCS ABAP de SAP NetWeaver_


Luego, cree estos puntos de conexión de equilibrio de carga interno necesarios para los puertos de SCS de Java de SAP NetWeaver:

| Nombre de regla de equilibrio de carga/servicio           | Números de puerto predeterminados | Puertos concretos para (instancia de SCS con el número de instancia 01) (ERS con 11)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Enqueue Server/ _lbrule3201_                | 32<*NúmeroInstancia*>  | 3201                                                                     
| Gateway Server/ _lbrule3301_                | 33<*NúmeroInstancia*>  | 3301                                                                     
| Java Message Server/ _lbrule3900_           | 39<*NúmeroInstancia*>  | 3901                                                                     
| Message Server HTTP/ _Lbrule8101_           | 81<*NúmeroInstancia*>  | 8101                                                                     
| SAP Start Service SCS HTTP/ _Lbrule50113_   | 5<*NúmeroInstancia*>13 | 50113                                                                    
| SAP Start Service SCS HTTPS/ _Lbrule50114_  | 5<*NúmeroInstancia*>14 | 50114                                                                    
| Enqueue Replication/ _Lbrule50116_          | 5<*NúmeroInstancia*>16 | 50116                                                                    
| SAP Start Service ERS HTTP/ _Lbrule51113_     | 5<*NúmeroInstancia*>13 | 51113                                                                    
| SAP Start Service ERS HTTPS/ _Lbrule51114_     | 5<*NúmeroInstancia*>14 | 51114                                                                    
| Win RM/ _Lbrule5985_                          |                       | 5985                                                                     
| File Share/ _Lbrule445_                       |                       | 445                                                                      

_**Tabla 2:** Números de puerto de las instancias de SCS de Java de SAP NetWeaver_


![Figura 13: Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure][sap-ha-guide-figure-3004]

_**Figura 13:** Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure_

Establezca la dirección IP del equilibrador de carga **pr1-lb-dbms** en la dirección IP del nombre de host virtual de la instancia de DBMS (**10.0.0.33** en el ejemplo).

### <a name="a-namefe0bd8b52b4345e3829580bee5415716a-change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure

Si desea usar otros números para las instancias de ASCS o SCS de SAP, debe actualizar los nombres y valores de esos puertos.

Una forma de actualizar los números de las instancias es mediante Azure Portal:

Vaya a **<*SID*>-lb-ascs load balancer** > **Reglas de equilibrio de carga.**

Cambie estos valores para todas las reglas de equilibrio de carga que pertenezcan a la instancia de ASCS o SCS de SAP:

- Nombre
- Port
- Puerto de back-end

Por ejemplo, si desea cambiar el número de instancia de ASCS predeterminado de 00 a 31, debe hacer cambios en todos los puertos que aparecen en la tabla 1.

A continuación, se muestra un ejemplo de una actualización para el puerto _lbrule3200_.

![Figura 14: Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure][sap-ha-guide-figure-3005]

_**Figura 14:** Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure_

### <a name="a-namee69e9a34460147a3a41cd2e11c626c0ca-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Incorporación de máquinas virtuales de Windows al dominio

Después de asignar una dirección IP estática a las máquinas virtuales, agregue las máquinas virtuales al dominio.

![Figura 15: Incorporación de una máquina virtual a un dominio][sap-ha-guide-figure-3006]

_**Figura 15:** Incorporación de una máquina virtual a un dominio_

### <a name="a-name661035b24d0f4d3186f8dc0a50d78158a-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Incorporación de entradas del Registro en ambos nodos del clúster usados para la instancia de ASCS/SCS de SAP

Azure Load Balancer tiene un equilibrador de carga interno que cierra las conexiones cuando están inactivas durante un período establecido (tiempo de espera de inactividad). Los proceso de trabajo de SAP en instancias de diálogo abren conexiones con el proceso de puesta en cola de SAP tan pronto como se deba enviar la primera solicitud para poner en cola y para quitar de la cola. Estas conexiones suelen mantenerse hasta que el proceso de trabajo o el de puesta en cola se reinicia. Sin embargo, si la conexión está inactiva durante un período, el equilibrador de carga interno de Azure cierra la conexión. Esto no supone un problema, porque el proceso de trabajo de SAP restablece la conexión con el proceso de puesta en cola si ya no existe. Estas actividades se documentan en los seguimientos de desarrollador de los procesos de SAP, pero crean una gran cantidad de contenido adicional en esos seguimientos. Se recomienda cambiar los parámetros de TCP/IP `KeepAliveTime` y `KeepAliveInterval` en ambos nodos del clúster. Combine estos cambios en los parámetros de TCP/IP con los parámetros de perfil de SAP, lo que se describe más adelante en este artículo.

Agregue estas entradas del Registro de Windows en los nodos de clúster de Windows para ASCS/SCS de SAP:

| Ruta de acceso                  | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters   
| ----------------------|-----------------------------------------------------------
| Nombre de la variable         | `KeepAliveTime`                                              
| Tipo de variable         | REG_DWORD (Decimal)                                        
| Valor                 | 120000                                                     
| Vínculo a la documentación | [https://technet.microsoft.com/es-es/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx)


_**Tabla 3:** Cambio del primer parámetro de TCP/IP_


| Ruta de acceso                  | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters   
| ----------------------|-----------------------------------------------------------
| Nombre de la variable         | `KeepAliveInterval`                                          
| Tipo de variable         | REG_DWORD (Decimal)                                        
| Valor                 | 120000                                                     
| Vínculo a la documentación | [https://technet.microsoft.com/es-es/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx)


_**Tabla 4:** Cambio del segundo parámetro de TCP/IP_

Para aplicar los cambios, reinicie ambos nodos del clúster.

### <a name="a-name0d67f090792843e087725ccbf8f59aaba-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configuración del clúster de Clústeres de conmutación por error de Windows para la instancia de ASCS/SCS de SAP

#### <a name="a-name5eecb071c7034cccba6dfe9c6ded9d79a-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Recopilación de nodos del clúster en la configuración de clúster

El primer paso consiste en agregar clústeres de conmutación por error a ambos nodos del clúster. Use el Asistente para agregar roles y características.

En el segundo paso, se configura el clúster de conmutación por error mediante el Administrador de clústeres de conmutación por error.

En Administrador de clústeres de conmutación por error, seleccione **Crear clúster** y, luego, agregue solo el nombre del primer nodo de clúster A. Por ejemplo, agregue **pr1-ascs-0**. No agregue el segundo nodo todavía. Agregará el segundo nodo en un paso más adelante.

![Figura 16: Incorporación del nombre de servidor o máquina virtual del primer nodo del clúster][sap-ha-guide-figure-3007]

_**Figura 16:** Incorporación del nombre de servidor o máquina virtual del primer nodo del clúster_

A continuación, se le pedirá el nombre de red (nombre de host virtual) del clúster.

![Figura 17: Definición del nombre del clúster][sap-ha-guide-figure-3008]

_**Figura 17:** Definición del nombre del clúster_


Una vez creado el clúster, ejecute una prueba de validación del clúster.

![Figura 18: Ejecución de la comprobación de validación del clúster][sap-ha-guide-figure-3009]

_**Figura 18:** Ejecución de la comprobación de validación del clúster_

![Figura 19: No se encuentra disco de cuórum][sap-ha-guide-figure-3010]

_**Figura 19:** No se encuentra disco de cuórum_

En este punto del proceso, puede omitir cualquier advertencia sobre los discos. Agregará un testigo de recurso de archivos y los discos compartidos de SIOS más tarde. En esta fase, no es necesario preocuparse por el cuórum.

![Figura 20: El recurso de clúster principal necesita una dirección IP nueva][sap-ha-guide-figure-3011]

_**Figura 20:** El recurso de clúster principal necesita una dirección IP nueva_

El clúster no se puede iniciar, porque la dirección IP del servidor apunta a uno de los nodos de la máquina virtual. Debe cambiar la dirección IP del servicio de clúster principal.

Por ejemplo, necesitamos asignar una dirección IP (**10.0.0.42** en el ejemplo) para el nombre de host virtual del clúster **pr1-ascs-vir**. Para ello, vaya a la página de propiedad del recurso de dirección IP del servicio de clúster principal que se muestra en la figura 21.

![Figura 21: En el cuadro de diálogo **Propiedades**, cambie la dirección IP][sap-ha-guide-figure-3012]

_**Figura 21:** En el cuadro de diálogo **Propiedades**, cambie la dirección IP_

![Figura 22: Asignación de la dirección IP reservada para el clúster][sap-ha-guide-figure-3013]

_**Figura 22:** Asignación de la dirección IP reservada para el clúster_

Después de cambiar la dirección IP, conecte el nombre de host virtual del clúster.

![Figura 23: El servicio principal del clúster está activo, en ejecución y tiene la dirección IP correcta][sap-ha-guide-figure-3014]

_**Figura 23:** El servicio principal del clúster está activo, en ejecución y tiene la dirección IP correcta_

Ahora que el servicio de clúster principal está en funcionamiento, puede agregar el segundo nodo del clúster.

![Figura 24: Agregue el segundo nodo del clúster][sap-ha-guide-figure-3015]

_**Figura 24:** Agregue el segundo nodo del clúster_

![Figura 25: Incorporación del nombre de host del segundo nodo del clúster, por ejemplo, pr1-ascs-1][sap-ha-guide-figure-3016]

_**Figura 25:** Incorporación del nombre de host del segundo nodo del clúster, por ejemplo, **pr1-ascs-1**_

![Figura 26: No active la casilla][sap-ha-guide-figure-3017]

_**Figura 26:** *No* active la casilla_

> [AZURE.IMPORTANT] Asegúrese de que la casilla **Agregar todo el almacenamiento apto al clúster** *no* esté activada.  

![Figura 27: Omisión de las advertencias sobre el cuórum de disco][sap-ha-guide-figure-3018]

_**Figura 27:** Omisión de las advertencias sobre el cuórum de disco_

Puede pasar por alto las advertencias sobre el cuórum y los discos. Establecerá el cuórum y compartirá el disco más adelante, tal como se describe en [Instalación de SIOS DataKeeper Cluster Edition para un disco compartido de clúster de ASCS/SCS de SAP][sap-ha-guide-8.12.3].

#### <a name="a-namee49a452950c94dcfbde715a0c21d21caa-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configuración de un testigo de recurso compartido de archivos de clúster

##### <a name="a-name06260b30d6974c4db1c9d22c0bd64855a-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Creación de un recurso compartido de archivos

Seleccione un testigo de recurso de archivos en lugar de un disco de cuórum. SIOS DataKeeper admite esta opción.

En los ejemplos de este artículo, el testigo de recurso compartido de archivos se encuentra en la instancia de Active Directory/el servidor DNS que se ejecuta en Azure. El testigo de recurso compartido de archivos se llama **domcontr-0**. Dado que habría configurado una conexión de red privada virtual (VPN) con Azure (a través de VPN de sitio a sitio o Azure ExpressRoute), su instancia de Active Directory/servidor DNS es local y no es apta para ejecutar un testigo de recurso compartido de archivos.

> [AZURE.NOTE] Si la instancia de Active Directory/el servidor DNS solo se ejecuta localmente, no configure el testigo de recurso compartido de archivos en la instancia de Active Directory/sistema operativo Windows de DNS que se ejecuta localmente. La latencia de red entre los nodos de clúster que se ejecutan en Azure y Active Directory/DNS local puede ser demasiado grande y provocar problemas de conectividad. Asegúrese de configurar el testigo de recurso compartido de archivos en una máquina virtual de Azure que se ejecuta cerca del nodo del clúster.  

La unidad de cuórum necesita, como mínimo, 1024 MB de espacio disponible. Se recomiendan 2048 MB de espacio disponible.

El primer paso consiste en agregar el objeto de nombre de clúster.

![Figura 28: Asignación de los permisos en el recurso compartido para el objeto de nombre de clúster][sap-ha-guide-figure-3019]

_**Figura 28:** Asignación de los permisos en el recurso compartido para el objeto de nombre de clúster_

Asegúrese de que los permisos incluyan la autoridad para cambiar datos en el recurso compartido para el objeto de nombre de clúster (**pr1-ascs-vir$** en el ejemplo). Para agregar el objeto de nombre de clúster a la lista, seleccione **Agregar**. Cambie el filtro para buscar objetos de equipo, además de los que aparecen en la figura 29:

![Figura 29: Cambio del tipo de objeto para incluir los objetos de equipo][sap-ha-guide-figure-3020]

_**Figura 29:** Cambio del tipo de objeto para incluir los objetos de equipo_

![Figura 30: Activación de la casilla para objetos de equipo][sap-ha-guide-figure-3021]

_**Figura 30:** Activación de la casilla para objetos de equipo_

Luego, escribe el objeto de nombre de clúster, como aparece en la figura 29. Como ya se creó el registro, puede cambiar los permisos, tal como aparece en la figura 28.

A continuación, seleccione la pestaña **Seguridad** del recurso compartido y, luego, establezca permisos más detallados para el objeto de nombre de clúster.

![Figura 31: Establecimiento de los atributos de seguridad para el objeto de nombre de clúster en el cuórum de recurso compartido de archivos][sap-ha-guide-figure-3022]

_**Figura 31:** Establecimiento de los atributos de seguridad para el objeto de nombre de clúster en el cuórum de recurso compartido de archivos_

##### <a name="a-name4c08c38778a046b19d27b497b08cac3da-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Configuración del cuórum de testigo del recurso compartido de archivos en el Administrador de clústeres de conmutación por error

En el Administrador de clústeres de conmutación por error, cambie la configuración del clúster a un testigo de recurso compartido de archivos.

![Figura 32: Inicio del Asistente para configurar cuórum de clúster][sap-ha-guide-figure-3023]

_**Figura 32:** Inicio del Asistente para configurar cuórum de clúster_

![Figura 33: Configuraciones de cuórum que puede elegir][sap-ha-guide-figure-3024]

_**Figura 33:** Configuraciones de cuórum que puede elegir_

Seleccione **Seleccionar el testigo de cuórum**.

![Figura 34: Selección del testigo de recurso compartido de archivos][sap-ha-guide-figure-3025]

_**Figura 34:** Selección del testigo de recurso compartido de archivos_

Seleccione **Configurar un testigo de recurso compartido de archivos**.

![Figura 35: Definición de la ubicación del recurso compartido de archivos para el recurso compartido testigo][sap-ha-guide-figure-3026]

_**Figura 35:** Definición de la ubicación del recurso compartido de archivos para el recurso compartido testigo_

Escriba la ruta de acceso UNC al recurso compartido de archivos (\\domcontr-0\FSW en el ejemplo).

Seleccione **Siguiente** para ver una lista de los cambios que puede hacer. Seleccione los cambios que desee y, luego, seleccione **Siguiente**.

![Figura 36: Confirmación de que volvió a configurar el clúster][sap-ha-guide-figure-3027]

_**Figura 36:** Confirmación de que volvió a configurar el clúster_

En este último paso, necesita volver a configurar correctamente el clúster, tal como aparece en la figura 36.  

### <a name="a-name5c8e5482841e45e1a89da05c0907c868a-install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalación de SIOS DataKeeper Cluster Edition para un disco compartido de clúster de ASCS/SCS de SAP

Ahora tiene una configuración de Clústeres de conmutación por error de Windows Server activa en Azure. Pero, para instalar una instancia de ASCS/SCS de SAP, necesita un recurso de disco compartido. No puede crear los recursos de disco compartido que necesita en Azure. SIOS DataKeeper Cluster Edition es una solución de terceros que puede usar para crear recursos de disco compartido.

#### <a name="a-name1c2788c336484e829e0de058e475e2a3a-add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Incorporación de la característica .NET Framework 3.5

Microsoft .NET Framework 3.5 no está activo ni instalado automáticamente en Windows Server 2012 R2. Sin embargo, SIOS DataKeeper requiere que .NET Framework se encuentre en todos los nodos en los que instala DataKeeper. Por este motivo, debe instalar .NET Framework 3.5 en el sistema operativo invitado de todas las máquinas virtuales del clúster.

Hay dos maneras de agregar .NET Framework 3.5. Una manera es usar el Asistente para agregar roles y características en Windows, que se muestra en la figura 37.

![Figura 37: Instalación de .NET Framework 3.5 por medio del Asistente para agregar roles y características][sap-ha-guide-figure-3028]

_**Figura 37:** Instalación de .NET Framework 3.5 por medio del Asistente para agregar roles y características_

![Figura 38: Barra de progreso de instalación cuando instala .NET Framework 3.5 por medio del Asistente para agregar roles y características][sap-ha-guide-figure-3029]

_**Figura 38:** Barra de progreso de instalación cuando instala .NET Framework 3.5 por medio del Asistente para agregar roles y características_

La segunda opción para activar la característica .NET Framework 3.5 es mediante la herramienta de la línea de comandos dism.exe. Para este tipo de instalación, necesita tener acceso al directorio SxS en los medios de instalación de Windows. Ejecute este comando en un símbolo del sistema con privilegios elevados:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="a-namedd41d5a28083415b9878839652812102a-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalación de SIOS DataKeeper

Instale SIOS DataKeeper Cluster Edition en cada nodo del clúster. Con SIOS DataKeeper, para crear el almacenamiento compartido virtual, cree un reflejo sincronizado y, luego, simule el almacenamiento compartido de clúster.

Antes de instalar el software SIOS, cree el usuario de dominio **DataKeeperSvc**.

> [AZURE.NOTE] Agregue el usuario **DataKeeperSvc** al grupo de **administradores locales** en ambos nodos del clúster.

Instale el software SIOS en ambos nodos del clúster.

![Instalador de SIOS][sap-ha-guide-figure-3030]

![Figura 39: Primera pantalla de la instalación de SIOS DataKeeper][sap-ha-guide-figure-3031]

_**Figura 39:** Primera pantalla de la instalación de SIOS DataKeeper_

![Figura 40: DataKeeper le informa que se deshabilitará un servicio][sap-ha-guide-figure-3032]

_**Figura 40:** DataKeeper le informa que se deshabilitará un servicio_

En el cuadro de diálogo que aparece en la figura 40, seleccione **Sí**.

![Figura 41: Selección del usuario para SIOS DataKeeper][sap-ha-guide-figure-3033]

_**Figura 41:** Selección del usuario para SIOS DataKeeper_


En la pantalla que aparece en la figura 41, se recomienda seleccionar **Cuenta de dominio o de servidor**.

![Figura 42: Ingreso del nombre de usuario y contraseña de dominio para la instalación de SIOS DataKeeper][sap-ha-guide-figure-3034]

_**Figura 42:** Ingreso del nombre de usuario y contraseña de dominio para la instalación de SIOS DataKeeper_

Escriba el nombre de usuario y contraseñas de cuenta de dominio que creó para SIOS DataKeeper.

![Figura 43: Ingreso de la licencia de SIOS DataKeeper][sap-ha-guide-figure-3035]

_**Figura 43:** Ingreso de la licencia de SIOS DataKeeper_

Instale la clave de licencia para la instancia de SIOS DataKeeper, tal como aparece en la figura 43. Al final de la instalación, se le pedirá reiniciar la máquina virtual.

#### <a name="a-named9c1fc8e87104dffbec21f535db7b006a-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configuración de SIOS DataKeeper

Después de instalar SIOS DataKeeper en ambos nodos, debe iniciar la configuración. El objetivo de la configuración es conseguir la replicación sincrónica de datos entre los VHD adicionales conectados a cada una de las máquinas virtuales. Estos son los pasos que debe llevar a cabo para configurar ambos nodos.

![Figura 44: Herramienta de configuración y administración de SIOS DataKeeper][sap-ha-guide-figure-3036]

_**Figura 44:** Herramienta de configuración y administración de SIOS DataKeeper_

Inicie la herramienta de configuración y administración de DataKeeper y, luego, seleccione **Servidor de conexión**. (En la figura 44, esta opción aparece rodeada de un círculo de color rojo).

![Figura 45: Inserción del nombre o la dirección TCP/IP del primer nodo al que se debe conectar la herramienta de configuración y administración y, en un segundo paso, el segundo nodo][sap-ha-guide-figure-3037]

_**Figura 45:** Inserción del nombre o la dirección TCP/IP del primer nodo al que se debe conectar la herramienta de configuración y administración y, en un segundo paso, el segundo nodo_

El paso siguiente es crear el trabajo de replicación entre los dos nodos.

![Figura 46: Creación de un trabajo de replicación][sap-ha-guide-figure-3038]

_**Figura 46:** Creación de un trabajo de replicación_

Un asistente le guía por el proceso de crear un trabajo de replicación.

![Figura 47: Definición del nombre del trabajo de replicación][sap-ha-guide-figure-3039]

_**Figura 47:** Definición del nombre del trabajo de replicación_

![Figura 48: Definición de los datos básicos para el nodo que debe ser el nodo de origen actual][sap-ha-guide-figure-3040]

_**Figura 48:** Definición de los datos básicos para el nodo que debe ser el nodo de origen actual_

En el primer paso, necesita definir el nombre, la dirección TCP/IP y el volumen de disco del nodo de origen. El segundo paso consiste en definir el nodo de destino. Tal como ya se explicó, necesita definir el nombre, la dirección TCP/IP y el volumen de disco del nodo de destino.

![Figura 49: Definición de los datos básicos para el nodo que debe ser el nodo de destino actual][sap-ha-guide-figure-3041]

_**Figura 49:** Definición de los datos básicos para el nodo que debe ser el nodo de destino actual_

A continuación, defina los algoritmos de compresión. En el ejemplo, se recomienda comprimir el flujo de replicación. Especialmente en situaciones de resincronización, la compresión del flujo de replicación reduce considerablemente el tiempo que se tarda en resincronizar. Tenga en cuenta que la compresión usa los recursos de CPU y RAM de una máquina virtual. A medida que aumenta la tasa de compresión, también aumenta el volumen de los recursos de CPU usados. Puede ajustar esta configuración más adelante.

Otra configuración que debe comprobar es si la replicación se ejecuta de forma sincrónica o asincrónica. *Cuando proteja las configuraciones de ASCS/SCS de SAP, debe usar la replicación sincrónica*.  

![Figura 50: Definición de los detalles de la replicación][sap-ha-guide-figure-3042]

_**Figura 50:** Definición de los detalles de la replicación_

El paso final es definir si el volumen que el trabajo de replicación replica se debe representar en una configuración de clúster de Clústeres de conmutación por error de Windows Server como disco compartido. Para la configuración de ASCS/SCS de SAP, seleccione **Sí** de manera que el clúster de Windows vea el volumen replicado como un disco compartido que puede usar como volumen de clúster.

![Figura 51: Selección de **Sí** para establecer el volumen replicado como volumen de clúster][sap-ha-guide-figure-3043]

_**Figura 51:** Selección de **Sí** para establecer el volumen replicado como volumen de clúster_

Una vez creado el volumen, la herramienta de configuración y administración de DataKeeper muestra que el trabajo de replicación está activo.

![Figura 52: El reflejo sincrónico de DataKeeper para el disco compartido de ASCS/SCS de SAP está activo][sap-ha-guide-figure-3044]

_**Figura 52:** El reflejo sincrónico de DataKeeper para el disco compartido de ASCS/SCS de SAP está activo_

Ahora, el Administrador de clústeres de conmutación por error muestra el disco como un disco de DataKeeper, tal como aparece en la figura 53.

![Figura 53: El Administrador de clústeres de conmutación por error muestra el disco que DataKeeper replicó][sap-ha-guide-figure-3045]

_**Figura 53:** El Administrador de clústeres de conmutación por error muestra el disco que DataKeeper replicó_


## <a name="a-namea06f0b498a7a42bf8b0dc12026c5746ba-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalación del sistema SAP NetWeaver

No se describirá la configuración de DBMS, porque las configuraciones varían en función del sistema de DBMS que usa. Sin embargo, se da por supuesto que las inquietudes respecto de la alta disponibilidad con DBMS se abordan con las funcionalidades que admiten los diversos proveedores de DBMS para Azure. Por ejemplo, AlwaysOn o creación de reflejo de la base de datos para SQL Server y Oracle Data Guard para Oracle. En el escenario de este artículo, no se agregó más protección a DBMS.

No hay ninguna consideración especial cuando distintos servicios de DBMS interactúan con esta variante de configuración de ASCS/SCS de SAP en clúster en Azure.


> [AZURE.NOTE] Los procedimientos de instalación de sistemas ABAP de SAP NetWeaver, sistemas Java y sistemas ABAP+Java son casi idénticos. La diferencia más significativa es que un sistema ABAP de SAP tiene una instancia de ASCS. El sistema Java de SAP tiene una instancia de SCS. El sistema ABAP+Java de SAP tiene una instancia de ASCS y una instancia de SCS en ejecución en el mismo grupo de clústeres de conmutación por error de Microsoft. Cualquier diferencia de instalación de cada pila de instalación de SAP NetWeaver se menciona explícitamente. Puede asumir que todos los demás componentes son iguales.  

### <a name="a-name31c6bd4f51df40579fdf3fcbc619c170a-install-sap-with-a-highavailability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalación de SAP con una instancia de ASCS/SCS de alta disponibilidad

> [AZURE.IMPORTANT] Asegúrese de no colocar el archivo de paginación en los volúmenes reflejados de DataKeeper. DataKeeper no admite los volúmenes reflejados. Puede dejar el archivo de paginación en la unidad temporal D de una máquina virtual de Azure, que es la opción predeterminada. Si todavía no se encuentra ahí, mueva el archivo de paginación de Windows a la unidad D de la máquina virtual de Azure.

#### <a name="a-namea97ad604909444fea364f89cb39bf097a-create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Creación de un nombre de host virtual para la instancia de ASCS/SCS de SAP en clúster

En primer lugar, en el Administrador de DNS de Windows, cree una entrada de DNS para el nombre de host virtual de la instancia de ASCS/SCS. Luego, defina la dirección IP asignada al nombre de host virtual.

> [AZURE.NOTE]  
Recuerde que la dirección IP que asigna al nombre de host virtual de la instancia de ASCS/SCS debe ser la misma dirección IP que asignó a Azure Load Balancer (<*SID*>-lb-ascs).  

La dirección IP del nombre de host de ASCS/SCS de SAP virtual (pr1-ascs-sap) es la misma dirección IP de Azure Load Balancer (pr1-lb-ascs).

Solo un rol de clúster de conmutación por error de SAP se puede ejecutar en un clúster de conmutación por error de SAP de Windows Server en Azure. Por ejemplo, esto significa una instancia de ACS para el sistema ABAP y una instancia de SCS para el sistema Java. En el caso del sistema ABAP+Java, sería una instancia de ASCS y una de SCS.

> [AZURE.NOTE] Actualmente, la agrupación en clústeres de varios SID descrita en las guías de instalación de SAP (consulte [Guías de instalación de SAP][sap-installation-guides]) no funciona en Azure.

![Figura 54: Definición de la entrada DNS del nombre virtual del clúster de ASCS/SCS de SAP y la dirección TCP/IP][sap-ha-guide-figure-3046]

_**Figura 54:** Definición de la entrada DNS del nombre virtual del clúster de ASCS/SCS de SAP y la dirección TCP/IP_

La entrada está en el Administrador de DNS, debajo del dominio, tal como aparece en la figura 55.

![Figura 55: Nuevo nombre virtual y dirección TCP/IP para la configuración del clúster de ASCS/SCS de SAP][sap-ha-guide-figure-3047]

_**Figura 55:** Nuevo nombre virtual y dirección TCP/IP para la configuración del clúster de ASCS/SCS de SAP_

#### <a name="a-nameeb5af918b42f4803bb50eff41f84b0b0a-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalación del primer nodo de clúster de SAP

Para instalar el primer clúster de SAP, ejecute la opción de primer nodo de clúster en el nodo de clúster A. Por ejemplo, en el host **pr1-ascs-0**.

Si desea mantener los puertos predeterminados para el equilibrador de carga interno de Azure, seleccione:

- **Sistema ABAP**: número de instancia de **ASCS** **00**
- **Sistema Java**: número de instancia de **SCS** **01**
- **Sistema ABAP+JAVA**: número de instancia de **ASCS** **00** y número de instancia de **SCS** **01**

Si desea usar números de instancia diferentes de 00 para la instancia de ASCS de ABAP y 01 para la instancia de SCS de Java, primero debe cambiar las reglas predeterminadas de equilibrio de carga del equilibrador de carga interno de Azure, tal como se describe en [Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno de Azure][sap-ha-guide-8.9].

Luego, realice unos pasos que no se describen en la documentación de instalación usual de SAP.

> [AZURE.NOTE] La documentación de instalación de SAP describe cómo instalar el primer nodo de clúster de ASCS/SCS.

#### <a name="a-namee4caaab2e90f4f2cbc842cd2e12a9556a-modify-the-sap-profile-of-the-ascsscs-ipowershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificación del perfil SAP de la instancia de ASCS/SCS

Debe agregar un nuevo parámetro de perfil. El parámetro de perfil evita el cierre de las conexiones entre los procesos de trabajo de SAP y el servidor de puesta en cola cuando lleven inactivas demasiado tiempo. Mencionamos el escenario del problema en [Incorporación de entradas del Registro en ambos nodos del clúster usados para la instancia de ASCS/SCS de SAP][sap-ha-guide-8.11] de este artículo. En esa sección, también se presentan 2 cambios para algunos parámetros básicos de la conexión TCP/IP. En el segundo paso, es necesario configurar el servidor de puesta en cola para enviar una señal **keep_alive** de forma que las conexiones no alcancen el umbral de inactividad del equilibrador de carga interno de Azure.

Agregue este parámetro de perfil al perfil de la instancia de ASCS/SCS de SAP:
```
enque/encni/set_so_keepalive = true
```
En este ejemplo, la ruta de acceso es:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

Por ejemplo, al perfil de la instancia de SCS de SAP y la ruta de acceso correspondiente:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="a-name10822f4f32e74871b63a9b86c76ce761a-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Incorporación de un puerto de sondeo

Use la funcionalidad de sondeo del equilibrador de carga interno para que toda la configuración del clúster funcione con Load Balancer. Habitualmente, el equilibrador de carga interno de Azure distribuye la carga de trabajo entrante de manera equitativa entre las máquinas virtuales que participan. Sin embargo, esto no funcionará en algunas configuraciones de clúster porque solo hay una instancia activa. La otra instancia es pasiva y no puede aceptar nada de la carga de trabajo. Una funcionalidad de sondeo resulta útil cuando el equilibrador de carga interno de Azure asigna trabajo solo a una instancia activa. Con la funcionalidad de sondeo, el equilibrador de carga interno puede detectar cuáles son las instancias activas y, luego, orientarse solo a la instancia con la carga de trabajo.

Primero, compruebe la configuración de **ProbePort** actual con este comando de PowerShell. Ejecútelo dentro de una de las máquinas virtuales de la configuración del clúster:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figura 56: De manera predeterminada, el puerto de sondeo de configuración de clúster es 0][sap-ha-guide-figure-3048]

_**Figura 56:** De manera predeterminada, el puerto de sondeo de configuración de clúster es 0_

Luego, defina un puerto de sondeo. El número de puerto de sondeo predeterminado es 0. En el ejemplo, se usa el puerto de sondeo **62300**.

El número de puerto está definido en las plantillas de Azure Resource Manager para SAP. Puede asignar el número de puerto en PowerShell.

Primero, obtenga el recurso de clúster de nombre de host virtual de SAP **SAP WAC IP**.

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

Luego, establezca el puerto de sondeo en **62300**.

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Para activar los cambios, detenga e inicie el rol de clúster **SAP PR1**.

Después de conectar el rol de clúster **SAP PR1**, compruebe que **ProbePort** esté establecido en el nuevo valor:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figura 57: Sondeo del puerto de clúster después de establecer el valor nuevo][sap-ha-guide-figure-3049]

_**Figura 57:** Sondeo del puerto de clúster después de establecer el valor nuevo_

El valor **ProbePort** está establecido en **62300**. Ahora puede tener acceso al recurso compartido de archivos **\\\ascsha-clsap\sapmnt** desde otros hosts, como **ascsha-dbas**.

### <a name="a-name85d78414b21d409792b634d8bcb724b7a-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalación de la instancia de base de datos

Para instalar la instancia de base de datos, siga el proceso que se describe en la documentación de instalación de SAP.

### <a name="a-name8a276e16f5074071b829cdc0a4d36748a-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instalación del segundo nodo del clúster

Para instalar el segundo clúster, siga los pasos que aparecen en la guía de instalación de SAP.

### <a name="a-name094bc89531d4447191cc1513b64e406aa-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Cambio del tipo de inicio del servicio de Windows para la instancia de ERS de SAP

Cambie el tipo de inicio del servicio de Windows de Enqueue Replication Server (ERS) de SAP a **Automático (inicio retrasado)** en ambos nodos del clúster.

![Figura 58: Cambio del tipo de servicio de la instancia de ERS de SAP a automático retrasado][sap-ha-guide-figure-3050]

_**Figura 58:** Cambio del tipo de servicio de la instancia de ERS de SAP a automático retrasado_

### <a name="a-name2477e58fc5a74a5d9ae37b91022cafb5a-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalación del servidor de aplicaciones principal de SAP

Instale la instancia del servidor de aplicaciones principal (PAS) <*SID*>-di-0 en la máquina virtual que designó para hospedar el PAS. No hay ninguna dependencia de Azure ni aspectos específicos de DataKeeper.

### <a name="a-name0ba4a6c1cc374bcfa8dc025de4263772a-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalación del servidor de aplicaciones adicional de SAP

Instale un servidor de aplicaciones adicional (AAS) de SAP en todas las máquinas virtuales que designó para hospedar un servidor de aplicaciones de SAP. Por ejemplo, en <*SID*>-di-1 a <*SID*>-di-<n>.

## <a name="a-name18aa2b9d92d24c0e8ddd5acaabda99e9a-test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Prueba de la conmutación por error de la instancia de ASCS/SCS de SAP y de la replicación de SIOS

Es fácil probar y supervisar la conmutación por error de la instancia de ASCS/SCS de SAP y la replicación de disco de SIOS mediante el uso del Administrador de clústeres de conmutación por error y la interfaz de usuario de SIOS DataKeeper.

### <a name="a-name65fdef0f9f9441f9b314ea45bbfea445a-sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> La instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster A

El grupo de clústeres **SAP WAC** se ejecuta en el nodo de clúster A. Por ejemplo, en **ascsha-clna**. Asigne la unidad de disco compartido S, que forma parte del grupo de clústeres **SAP WAC** y que la instancia de ASCS/SCS usa, al nodo de clúster A.

![Figura 59: Administrador de clústeres de conmutación por error: el grupo de clústeres <*SID*> de SAP se ejecuta en el nodo de clúster A][sap-ha-guide-figure-5000]

_**Figura 59:** Administrador de clústeres de conmutación por error: el grupo de clústeres <*SID*> de SAP se ejecuta en el nodo del clúster A_

Mediante la interfaz de usuario de SIOS DataKeeper, puede ver que los datos del disco compartido se replican sincrónicamente desde la unidad de volumen de origen S en el nodo de clúster A. Por ejemplo, de **ascsha-clna [10.0.0.41]** a **ascsha-clnb [10.0.0.42]**.

![Figura 60: En SIOS DataKeeper, replique el volumen local desde el nodo de clústeres A al nodo de clústeres B][sap-ha-guide-figure-5001]

_**Figura 60:** En SIOS DataKeeper, replique el volumen local desde el nodo de clústeres A al nodo de clústeres B_


### <a name="a-name5e959fa98fcd49e5a12c37f6ba07b916a-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Conmutación por error del nodo A al nodo B

Puede usar una de estas opciones para iniciar una conmutación por error del grupo de clústeres <*SID*> de SAP desde el nodo del clúster A al nodo del clúster B:

- Uso del Administrador de clústeres de conmutación por error  
- Uso de PowerShell del clúster de conmutación por error
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
- Reinicie el nodo del clúster A dentro del sistema operativo invitado Windows (inicia una conmutación por error automática del grupo de clústeres <*SID*> de SAP desde el nodo A al nodo B)  
- Reinicie el nodo del clúster A desde Azure Portal (inicia una conmutación por error automática del grupo de clústeres <*SID*> de SAP desde el nodo A al nodo B)  
- Reinicie el nodo del clúster A con Azure PowerShell (inicia una conmutación por error automática del grupo de clústeres de SAP <*SID*> desde el nodo A al nodo B)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

Después de la conmutación por error, el grupo de clústeres <*SID*> de SAP se ejecuta en el nodo del clúster B. Por ejemplo, en **ascsha-clnb**.

![Figura 61: En el Administrador de clústeres de conmutación por error, el grupo de clústeres <*SID*> de SAP se ejecuta en el nodo del clúster B][sap-ha-guide-figure-5002]

_**Figura 61:** En el Administrador de clústeres de conmutación por error, el grupo de clústeres <*SID*> de SAP se ejecuta en el nodo del clúster B_

El disco compartido ahora está montado en el nodo del clúster B. SIOS DataKeeper replica datos desde la unidad de volumen de origen S en el nodo del clúster B a la unidad de volumen de destino S en el nodo del clúster A. Por ejemplo, de **ascsha-clnb [10.0.0.42]** a **ascsha-clna [10.0.0.41]**.


![Figura 62: SIOS DataKeeper replica el volumen local desde el nodo del clúster B al nodo del clúster A][sap-ha-guide-figure-5003]

_**Figura 62:** SIOS DataKeeper replica el volumen local desde el nodo del clúster B al nodo del clúster A_



<!---HONumber=Oct16_HO2-->


