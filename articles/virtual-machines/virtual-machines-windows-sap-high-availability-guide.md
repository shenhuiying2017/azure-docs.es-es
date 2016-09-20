<properties
   pageTitle="SAP NetWeaver en máquinas virtuales (VM) Windows: guía de alta disponibilidad | Microsoft Azure"
   description="SAP NetWeaver en máquinas virtuales (VM) Windows: guía de alta disponibilidad"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# SAP NetWeaver en máquinas virtuales (VM) Windows: guía de alta disponibilidad

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación de DBMS"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Almacenamiento en caché de máquinas virtuales y VHD"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "Software RAID"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Almacenamiento de Microsoft Azure"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Estructura de una implementación de RDBMS"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Alta disponibilidad y recuperación ante desastres con máquinas virtuales de Azure"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 y versiones posteriores"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 y versiones anteriores"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Uso de imágenes de SQL Server fuera de Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Resumen general de SQL Server para SAP en Azure"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Detalles para SQL Server RDBMS"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configuración de almacenamiento"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Copia de seguridad y restauración"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Consideraciones de rendimiento para copias de seguridad y restauraciones"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Otros"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Recursos SAP"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Implementación de una máquina virtual con una imagen personalizada"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Implementación de cmdlets de Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Descarga e importación de cmdlets de PowerShell para SAP"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Unión de una máquina virtual a un dominio local (solo Windows)"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Descarga, instalación y habilitación del agente de máquina virtual de Azure"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurar la extensión de supervisión mejorada de Azure para SAP"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Comprobación de preparación de la supervisión mejorada de Azure para SAP"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Comprobación de estado de la configuración de la infraestructura de supervisión de Azure"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configuración de la supervisión"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configuración de proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Comprobaciones y solución de problemas de configuración de supervisión de extremo a extremo para SAP en Azure"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "SAP NetWeaver en máquinas virtuales (VM) Windows: guía de planeamiento e implementación"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Recursos"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Alta disponibilidad (HA) y recuperación ante desastres (DR) para la ejecución de SAP NetWeaver en máquinas virtuales de Azure"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Alta disponibilidad en los servidores de aplicaciones de SAP"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Uso de Autostart en las instancias de SAP"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Entre sitios locales: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Regiones de Azure"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Dominios de error"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Dominios de actualización"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Conjuntos de disponibilidad de Azure"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "El concepto de máquina virtual de Microsoft Azure"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Almacenamiento Premium de Azure"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Implementación de una máquina virtual con una imagen específica del cliente"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Preparación de máquinas virtuales con SAP para Azure"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Diferencia entre un disco y una imagen de Azure"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Carga de un VHD desde una instalación local a Azure"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copia de discos entre cuentas de almacenamiento de Azure"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Estructura de VM/VHD para implementaciones de SAP"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Configuración de montaje automático para discos conectados"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Escenario de demostración/aprendizaje de máquina virtual única con SAP NetWeaver"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Conceptos de implementación solo en la nube de instancias de SAP"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Solución de supervisión de Azure para SAP"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Almacenamiento Premium de Azure"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Redes de Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Almacenamiento: Almacenamiento de Microsoft Azure y discos de datos"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "SAP NetWeaver en máquinas virtuales (VM) Windows: guía de alta disponibilidad"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "Requisitos previos"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "Recursos"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Diferencias en alta disponibilidad de SAP entre el modelo de implementación clásica y el de Azure Resource Manager"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "Grupos de recursos"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "Agrupación en clústeres con el modelo de Azure Resource Manager en comparación con el de implementación clásica"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "Clústeres de conmutación por error de Windows Server (WSFC)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "Modos de cuórum"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "Clúster de conmutación por error de Windows local"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "Almacenamiento compartido"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "Redes y resolución de nombres"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "Clúster de conmutación por error de Windows con Microsoft Azure"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "Disco compartido en Microsoft Azure con SIOS DataKeeper"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Resolución de nombres en Microsoft Azure"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "Alta disponibilidad de SAP NetWeaver en IaaS de Azure"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "Alta disponibilidad en los servidores de aplicaciones de SAP"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "Alta disponibilidad para las instancias de (A)SCS de SAP"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "Alta disponibilidad para la instancia de (A)SCS de SAP con clúster de conmutación por error de Windows en Azure"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "Alta disponibilidad para la instancia de DBMS"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "Posibles escenarios de implementación completa de alta disponibilidad"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "Preparación de la infraestructura"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "Implementación de máquinas virtuales con conectividad de red corporativa (entre locales) para uso en producción"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "Implementación solo en la nube de instancias de SAP para pruebas o demostración"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Azure Virtual Network"
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "Direcciones IP de DNS"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "Nombres de host y direcciones IP estáticas para la instancia en clúster de ASCS/SCS de SAP y la instancia en clúster de DBMS"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "Configuración de direcciones IP estáticas para las máquinas virtuales SAP"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "Configuración de dirección IP estática para el equilibrador de carga interno (ILB)"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "Incorporación de máquinas Windows al dominio"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "Incorporación de entradas del Registro en ambos nodos del clúster usados para la instancia de ASCS/SCS de SAP"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "Configuración del clúster de conmutación por error de Windows Server para la instancia de ASCS/SCS de SAP"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "Recopilación de nodos del clúster en la configuración de clúster"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "Configuración del testigo del recurso compartido de archivos de clúster"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "Creación de un recurso compartido de archivos"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "Configuración del cuórum de testigo del recurso compartido de archivos en el Administrador de clústeres de conmutación por error"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "Instalación de SIOS DataKeeper Cluster Edition para un disco compartido de clúster de ASCS/SCS de SAP"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "Incorporación de la característica Microsoft .NET Framework 3.5"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "Instalación de SIOS DataKeeper"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "Configuración de SIOS DataKeeper"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "Instalación del sistema SAP NetWeaver"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "Instalación de SAP con una instancia de ASCS/SCS de alta disponibilidad"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "Creación de un nombre de host virtual para la instancia en clúster de ASCS/SCS de SAP"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "Instalación del primer nodo de clúster de SAP"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "Modificación del perfil SAP de la instancia de ASCS/SCS"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "Incorporación del puerto de sondeo"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "Instalación de la instancia de base de datos"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "Instalación del segundo nodo del clúster"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "Cambio del tipo de inicio del servicio de Windows para la instancia de ERS de SAP"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "Instalación del servidor de aplicaciones principal (PAS) de SAP"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "Instalación del servidor de aplicaciones adicional (AAS) de SAP"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "Prueba de la conmutación por error de la instancia de ASCS/SCS de SAP y de la replicación de SIOS"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "Punto de partida: la instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster A"
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "Proceso de conmutación por error del nodo A al nodo B"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "Resultado final: la instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster B"


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "Matriz de disponibilidad de productos SAP"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

Microsoft Azure permite que las empresas adquieran recursos de procesos, almacenamiento y redes en un tiempo mínimo sin ciclos de adquisición prolongados. Azure Virtual Machines permite que las empresas implementen aplicaciones clásicas como, por ejemplo, aplicaciones basadas en SAP NetWeaver (pila ABAP, Java y ABAP+Java) en Azure y amplíen su confiabilidad y disponibilidad sin tener más recursos disponibles de forma local. Además, Azure Virtual Machines admite la conectividad entre locales, lo que permite a las empresas integrar activamente Azure Virtual Machines en sus dominios locales, nubes privadas e infraestructura del sistema SAP.


En este documento se detallan todos los pasos necesarios para implementar sistemas SAP de alta disponibilidad en Azure usando un método novedoso con el nuevo modelo de implementación de Azure Resource Manager. Esta guía lo orientará por los pasos principales:


- Buscar las guías de instalación y las notas de SAP adecuadas, que aparecen más adelante en la sección titulada [Recursos][sap-ha-guide-2]. El documento complementa la Documentación de instalación de SAP y las Notas de SAP, que representan los principales recursos para las instalaciones e implementaciones de software de SAP en las plataformas proporcionadas.

- Comprender la diferencia entre el modelo de implementación clásica de Azure actual y este nuevo, de Azure Resource Manager.

- Comprender los modos de cuórum del clúster de conmutación por error de Windows Server (WSFC), para que pueda seleccionar el modelo adecuado para la implementación de Azure.

- Comprender el almacenamiento compartido del clúster de conmutación por error de Windows Server (WSFC) en Azure.

- Comprender cómo se pueden proteger en Azure los componentes de único punto de error de SAP, como ASCS/SCS y DBMS de SAP, y los componentes redundantes, como los servidores de aplicaciones de SAP.

- Enfoque paso a paso de cómo instalar y configurar un sistema SAP de alta disponibilidad (HA) en un clúster de conmutación por error de Windows (WSFC) con Microsoft Azure como plataforma y el nuevo Azure Resource Manager.

- Pasos adicionales necesarios para WSFC en Azure que no se necesitan en implementaciones locales.


Para simplificar la implementación y la configuración, se van a usar las nuevas plantillas de Azure Resource Manager para HA de SAP con 3 niveles. Esto automatiza la implementación de toda la infraestructura necesaria para el sistema SAP de alta disponibilidad y eso admite el ajuste de tamaño de SAPS deseado de su sistema SAP.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Requisitos previos

Antes de comenzar, asegúrese de que se cumplan los requisitos previos que se describen en los siguientes capítulos y de comprobar todos los recursos presentados en el capítulo Recursos.

Se van a usar plantillas de Azure Resource Manager para SAP NetWeaver con 3 niveles: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)

Se ofrece información general sobre las plantillas de Azure Resource Manager para SAP aquí: [https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos

Las siguientes guías incluyen temas sobre implementaciones de SAP en Azure:

- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de planeamiento e implementación][planning-guide]
- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación][deployment-guide]
- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de implementación de DBMS][dbms-guide]
- [SAP NetWeaver en máquinas virtuales (VM) Windows: guía de planeamiento e implementación (esta guía)][sap-ha-guide]


> [AZURE.NOTE] Siempre que sea posible, aparece un vínculo a la guía de instalación de SAP a la que se hace referencia (consulte las [guías de instalación de SAP][sap-installation-guides]). En cuanto a los requisitos previos y el proceso de instalación, las guías de instalación de SAP NetWeaver deben leerse detenidamente, dado que el presente documento solo trata tareas específicas para sistemas basados en SAP NetWeaver e instalados en una máquina virtual de Microsoft Azure.

Las siguientes notas de SAP están relacionadas con el tema de SAP en Azure:


| Número de nota | Título                                                    
| ------------- |----------------------------------------------------------
| [1928533] | SAP Applications on Azure: Supported Products and Sizing (Aplicaciones SAP en Azure: productos y tamaños compatibles) 
| [2015553] | SAP on Microsoft Azure: Support Prerequisites (SAP en Microsoft Azure: requisitos previos de compatibilidad)         
| [1999351] | Enhanced Azure Monitoring for SAP (Supervisión mejorada de Azure para SAP)                        
| [2178632] | Key Monitoring Metrics for SAP on Microsoft Azure (Métricas de supervisión clave para SAP en Microsoft Azure)        
| [1999351] | Virtualization on Windows: Enhanced Monitoring (Virtualización en Windows: supervisión mejorada)           


En [este artículo][azure-subscription-service-limits-subscription] se exponen las limitaciones generales predeterminadas y el límite máximo de suscripciones de Azure.

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a> Diferencias en alta disponibilidad de SAP entre el modelo de implementación clásica y el de Azure Resource Manager 

> [AZURE.NOTE] El modelo de implementación clásica también se conoce como modelo de administración de servicios de Azure (ASM).

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
Los grupos de recursos son un concepto nuevo que incluye todos los recursos que comparten el mismo ciclo de vida; por ejemplo, todos los que se crearon y eliminaron al mismo tiempo. Lea este artículo para más información sobre los grupos de recursos.

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Agrupación en clústeres con el modelo de Azure Resource Manager en comparación con el de implementación clásica 

El nuevo modelo de Azure Resource Manager presenta los siguientes cambios en comparación con el de implementación clásica para alta disponibilidad:

- No es necesario tener un servicio en la nube para usar un equilibrador de carga interno (ILB) de Azure.

Si desea usar el anterior modelo clásico de Azure, debe seguir el procedimiento descrito en el documento [SAP NetWeaver on Azure - Clustering SAP ASCS/SCS Instances using Windows Server Failover Cluster on Azure with SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver en Azure: agrupación en clústeres de instancias de ASCS/SCS de SAP mediante el clúster de conmutación por error de Windows Server en Azure con SIOS DataKeeper).

> [AZURE.NOTE] Está muy recomendado usar el nuevo modelo de implementación de Azure Resource Manager para las instalaciones de SAP, ya que ofrece muchas ventajas en comparación con el de implementación clásica. Puede encontrar más información en [este artículo][virtual-machines-azure-resource-manager-architecture-benefits-arm].


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Clústeres de conmutación por error de Windows Server (WSFC) 

WSFC de Microsoft constituye la base técnica para una instalación de ASCS/SCS de SAP y de DBMS con alta disponibilidad en Windows.

Un clúster de conmutación por error es un grupo de 1+n servidores independientes (nodos) que colaboran para aumentar la disponibilidad de aplicaciones y servicios. En caso de que se produzca un error en un nodo, WSFC debe determinar el número de errores que pueden producirse sin que el clúster deje de estar en buen estado para poder proporcionar aplicaciones o servicios definidos. Hay disponibles diferentes modos de cuórum para lograr esto.
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos de cuórum

Con WSFC, existen cuatro modos de cuórum diferentes:

- **Mayoría de nodo:** cada nodo puede votar. El clúster funciona solamente con la mayoría de los votos, es decir, más de la mitad. Se recomienda esta opción si el número de nodos es impar. Por ejemplo: 3 nodos en un clúster de 7 nodos pueden producir un error y el clúster todavía obtendrá la mayoría y seguirá ejecutándose.

- **Mayoría de disco y nodo:** cada nodo más un disco designado en el almacenamiento del clúster (el "testigo de disco") puede votar siempre que esté disponible y comunicándose. El clúster funciona solamente con la mayoría de los votos, es decir, más de la mitad. Este modo resulta útil en un entorno de clúster con un número par de nodos. Siempre que la mitad de los nodos más el disco estén en línea, el clúster permanece en buen estado.

- **Mayoría de recurso compartido de archivos y nodo:** cada nodo más un recurso compartido de archivos designado creado por el administrador (el testigo del recurso compartido de archivos) puede votar si está disponible y comunicándose. El clúster funciona solamente con la mayoría de los votos, es decir, más de la mitad. Este modo resulta útil en un entorno de clúster con un número par de nodos y se parece al modo Mayoría de disco y nodo, aunque usa un recurso compartido de archivos testigo en lugar de un disco testigo. Es fácil de implementar pero, si el recurso compartido de archivos en sí no es de alta disponibilidad, es posible que se convierta en un único punto de error.

- **Sin mayoría - Solo disco:** el clúster tiene cuórum si un nodo está disponible y comunicándose con un disco concreto en el almacenamiento del clúster. Los únicos nodos que pueden unirse al clúster son aquellos que estén comunicándose con ese disco. No se recomienda usar este modo.  

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Clúster de conmutación por error de Windows local

En este ejemplo, hay un clúster que consta de dos nodos. Si se produce un error en la conexión de red entre los nodos pero ambos se mantienen actualizados y en ejecución, es necesario aclarar qué nodo debe seguir proporcionando las aplicaciones y los servicios del clúster. Un disco o un recurso compartido de archivos de cuórum sirven para este propósito. El nodo que tiene acceso al disco o recurso compartido de archivos de cuórum es el que garantizará la accesibilidad de los servicios.

En este ejemplo, se usa un clúster de dos nodos. Por este motivo, se eligió el modo de cuórum de recurso compartido de archivos y nodo. La mayoría de disco y nodo también es una opción válida. En un entorno de producción, se recomienda usar un disco de cuórum en su lugar y emplear tecnología de sistema de almacenamiento y red para hacerlo de alta disponibilidad.

![Figura 1: Propuesta de configuración del clúster de conmutación por error de Windows Server para ASCS/SCS de SAP en Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Propuesta de configuración del clúster de conmutación por error de Windows Server para ASCS/SCS de SAP en Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Almacenamiento compartido

La ilustración anterior muestra un clúster de almacenamiento compartido con dos nodos. En un clúster de almacenamiento compartido local, hay un almacenamiento compartido que resulta visible para todos los nodos del clúster. Un mecanismo de bloqueo protege los datos contra daños. Además, todos los nodos pueden detectar si se produce un error en otro nodo. En este caso, el nodo restante toma posesión de los recursos de almacenamiento y garantiza la disponibilidad de los servicios.

> [AZURE.NOTE] Para lograr alta disponibilidad con algunos sistemas DBMS, como SQL Server, los discos compartidos no son necesarios. SQL Server AlwaysOn realiza la replicación de archivos de datos y de registro de DBMS desde el disco local de un nodo del clúster hacia el disco local de otro nodo del clúster. Por lo tanto, la configuración de clúster de Windows no necesita ningún disco compartido.

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Redes y resolución de nombres

El clúster es accesible a través de una dirección IP virtual y un nombre de host virtual proporcionados por el servidor DNS. Los nodos locales y el servidor DNS pueden administrar varias direcciones IP.

En una configuración típica, se utilizan dos o más conexiones de red:

- una conexión dedicada al almacenamiento;
- una conexión de red interna del clúster para el latido; y
- una red pública, usada por los clientes para conectarse al clúster.



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Clúster de conmutación por error de Windows con Microsoft Azure

En comparación con las implementaciones de nube privada o sin sistema operativo, Microsoft Azure Virtual Machines requiere pasos adicionales para configurar un clúster WSFC. Para crear un disco de clúster compartido, se necesitan varias direcciones IP y nombres de host virtual para la instancia de ASCS/SCS de SAP.

A continuación, se describen los conceptos adicionales y los pasos necesarios al crear un clúster de Central Services con alta disponibilidad de SAP en Microsoft Azure. Los pasos muestran cómo configurar la herramienta de terceros SIOS DataKeeper y configurar el equilibrador de carga interno de Azure. Estas herramientas ofrecerán la posibilidad de crear un clúster de conmutación por error de Windows con un testigo del recurso compartido de archivos en Microsoft Azure.


![Figura 2: Esquema de una configuración de clúster de conmutación por error de Windows Server en Azure sin disco compartido][sap-ha-guide-figure-1001]

_**Figura 2:** Esquema de una configuración de clúster de conmutación por error de Windows Server en Azure sin disco compartido_


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco compartido en Microsoft Azure con SIOS DataKeeper

A partir de septiembre de 2016, Microsoft Azure no proporcionará almacenamiento compartido para crear un clúster de almacenamiento compartido. Sin embargo, se necesita almacenamiento compartido de clúster para una instancia de ASCS/SCS de SAP de alta disponibilidad.

El software de terceros SIOS DataKeeper Cluster Edition permite crear un almacenamiento reflejado que simula el almacenamiento compartido de clúster. La solución SIOS proporciona replicación sincrónica de datos en tiempo real. El procedimiento para crear un recurso de disco compartido para un clúster es el siguiente:

- Se conecta un disco duro virtual de Azure adicional a cada una de las máquinas virtuales que se encuentran en una configuración de clúster de Windows.
- Se ejecuta SIOS DataKeeper Cluster Edition en ambos nodos de la máquina virtual.
- Se configura SIOS DataKeeper Cluster Edition de forma que refleje el contenido del volumen del disco duro virtual adicional conectado procedente de las máquinas virtuales de origen en el volumen del disco duro virtual adicional conectado de la máquina virtual de destino. SIOS DataKeeper abstrae los volúmenes locales de origen y de destino, y los presenta al clúster de conmutación por error de Windows como un disco compartido.

Para más detalles sobre el producto SIOS DataKeeper, consulte esta fuente: [http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/)

 ![Figura 3: Esquema de una configuración de clúster de conmutación por error de Windows Server en Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Esquema de una configuración de clúster de conmutación por error de Windows Server en Azure con SIOS DataKeeper_

> [AZURE.NOTE] Para lograr alta disponibilidad con algunos sistemas DBMS, como SQL Server, los discos compartidos no son necesarios. SQL Server AlwaysOn realiza la replicación de archivos de datos y de registro de DBMS desde el disco local de un nodo del clúster hacia el disco local de otro nodo del clúster. Por lo tanto, la configuración de clúster de Windows no necesita ningún disco compartido.

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolución de nombres en Microsoft Azure

La plataforma en la nube de Microsoft Azure no ofrece la posibilidad de configurar direcciones IP virtuales, por ejemplo, direcciones IP flotantes. Por esta razón, necesita una solución alternativa para configurar una dirección IP virtual que se comunique con el recurso de clúster en la nube. Azure proporciona el equilibrador de carga interno (ILB). Con el equilibrador de carga interno, se puede establecer comunicación con el clúster a través de la dirección IP virtual del clúster. Por lo tanto, debe implementar este equilibrador en el grupo de recursos que contiene los nodos del clúster. Después, debe configurar todas las reglas de reenvío de puertos necesarias con los puertos de sondeo del equilibrador de carga interno. Los clientes pueden conectarse por medio del nombre de host virtual. El servidor DNS resuelve la dirección IP del clúster y el equilibrador de carga interno controla el reenvío al nodo activo del clúster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Alta disponibilidad de SAP NetWeaver en IaaS de Azure

Como se trató antes en el capítulo sobre alta disponibilidad [SAP NetWeaver en máquinas virtuales (VM) de Azure en SAP NetWeaver en máquinas virtuales (VM) Windows: guía de planeamiento e implementación][planning-guide-11], para lograr la alta disponibilidad para aplicaciones SAP, por ejemplo, alta disponibilidad de componentes de software de SAP, es necesario proteger los siguientes componentes:

- servidores de aplicaciones de SAP,
- instancia de ASCS/SCS de SAP,
- servidor DBMS

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Alta disponibilidad en los servidores de aplicaciones de SAP

Para las instancias de diálogo o servidores de aplicaciones de SAP, por lo general no se necesita ninguna solución específica de alta disponibilidad. La alta disponibilidad se logra mediante redundancia y, por tanto, se configuran varias instancias de diálogo en distintas máquinas virtuales de Azure. Debe tener al menos dos instancias de la aplicación SAP instaladas en dos máquinas virtuales de Azure.

![Figura 4: Alta disponibilidad de servidores de aplicaciones de SAP][sap-ha-guide-figure-2000]

_**Figura 4:** Alta disponibilidad de servidores de aplicaciones de SAP_


Todas las máquinas virtuales que hospedan servidores de aplicaciones de SAP deben colocarse en el mismo **conjunto de disponibilidad de Azure**. El conjunto de disponibilidad de Azure garantizará:

- Que todas las máquinas virtuales formen parte de los mismos dominios de actualización, por ejemplo, se asegurará de evitar que las máquinas virtuales puedan actualizarse al mismo tiempo durante el tiempo de inactividad de mantenimiento planeado.
- Y que todas las máquinas virtuales formen parte de los mismos dominios de error, por ejemplo, se asegurará de que las máquinas virtuales se implementen de forma que se evite un punto único de error que pueda afectar a la disponibilidad de todas las máquinas virtuales.

Para más información, consulte [Administración de la disponibilidad de las máquinas virtuales][virtual-machines-manage-availability].

Como la cuenta de almacenamiento de Azure es un posible punto de error único, es importante que tenga al menos dos cuentas de almacenamiento de Azure en las que se distribuyan al menos dos máquinas virtuales. Lo ideal es que cada una de las máquinas virtuales que ejecutan instancias de diálogo de SAP se implemente en una cuenta de almacenamiento diferente.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Alta disponibilidad para las instancias de (A)SCS de SAP 

![Figura 5: Alta disponibilidad de instancias de ASCS/SCS de SAP][sap-ha-guide-figure-2001]

_**Figura 5:** Alta disponibilidad de instancias de ASCS/SCS de SAP_


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Alta disponibilidad para la instancia de (A)SCS de SAP con clúster de conmutación por error de Windows en Azure

En comparación con las implementaciones de nube privada o sin sistema operativo, Microsoft Azure Virtual Machines requiere pasos adicionales para configurar un clúster WSFC. Para crear un clúster de conmutación por error de Windows, se necesitan un disco de clúster compartido, varias direcciones IP y nombres de host virtual, y el equilibrador de carga interno (ILB) de Azure para el agrupamiento en clúster de la instancia de ASCS/SCS de SAP.

Esto se explicará con mayor detalle más adelante en el documento.

![Figura 6: Esquema de una configuración de clúster de conmutación por error de Windows Server para ASCS/SCS de SAP en Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Esquema de una configuración de clúster de conmutación por error de Windows Server para ASCS/SCS de SAP en Azure con SIOS DataKeeper_


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Alta disponibilidad para la instancia de DBMS

DBMS es también un único punto de error de un sistema SAP y debe protegerse mediante una solución de alta disponibilidad. A continuación, se muestra un ejemplo de una solución de alta disponibilidad SQL Server AlwaysOn en Azure que usa un clúster de conmutación por error de Windows Server y el equilibrador de carga interno de Azure. SQL Server AlwaysOn replica los archivos de datos y de registro de DBMS con su propia replicación DBMS. Por lo tanto, no se necesitan discos compartidos de clúster, lo que simplifica la configuración en conjunto.


![Figura 7: Alta disponibilidad de servidores DBMS de SAP: ejemplo de configuración de alta disponibilidad con SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 7:** Alta disponibilidad de servidores DBMS de SAP: ejemplo de configuración de alta disponibilidad con SQL Server AlwaysOn_


Este documento no cubre la agrupación en clúster del sistema DBMS.

Para más información acerca de la agrupación en clúster de SQL Server en Azure mediante el modelo de implementación de Azure Resource Manager, consulte estos artículos:

- [Configuración manual de grupos de disponibilidad Always On en máquinas virtuales de Azure Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Posibles escenarios de implementación completa de alta disponibilidad

Este es un ejemplo de una arquitectura de alta disponibilidad de SAP NetWeaver completa en Azure, en la que se usa un clúster dedicado para la instancia de ASCS/SCS de SAP y otro para DBMS.

![Figura 8: Plantilla 1 de arquitectura de alta disponibilidad de SAP: con un clúster dedicado para ASCS/SCS y otro para la instancia de DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Plantilla 1 de arquitectura de alta disponibilidad de SAP: con un clúster dedicado para ASCS/SCS y otro para la instancia de DBMS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparación de la infraestructura

Para simplificar la implementación de los recursos necesarios para SAP, se han desarrollado plantillas de Azure Resource Manager para SAP.

Estas plantillas de tres niveles también admiten escenarios de alta disponibilidad, por ejemplo:

- **Plantilla 1 de arquitectura**: con dos clústeres, cada uno para puntos únicos de error de SAP en ASCS/SCS de SAP y DBMS

Las plantillas de Azure Resource Manager para el escenario 1 están disponibles aquí:

- Imagen de Azure Marketplace: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- Imagen personalizada: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Cuando haga clic en la imagen de Marketplace de tres niveles de SAP, verá la siguiente interfaz de usuario en Azure Portal:

![Figura 9: Especificación de parámetros de Azure Resource Manager para alta disponibilidad de SAP][sap-ha-guide-figure-3000]

_**Figura 9:** Especificación de parámetros de Azure Resource Manager para alta disponibilidad de SAP_


Asegúrese de elegir **HA** para la opción SYSTEMAVAILABILITY.

Las plantillas crearán:

- Todas las **máquinas virtuales** necesarias para:
    - Máquinas virtuales de servidores de aplicaciones de SAP: `<SAPSystemSID>-di-<Number>`
    - Máquinas virtuales de clúster de ASCS/SCS: `<SAPSystemSID>-ascs-<Number>`
    - Clúster de DBMS: `<SAPSystemSID>-db-<Number>`
- **Tarjetas de red para todas las máquinas virtuales con direcciones IP asociadas**:
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Cuentas de almacenamiento de Azure**
- **Grupos de disponibilidad** para:
    - Máquinas virtuales de servidores de aplicaciones de SAP: `<SAPSystemSID>-avset-di`
    - Máquinas virtuales del clúster de ASCS/SCS de SAP: `<SAPSystemSID>-avset-ascs`
    - Máquinas virtuales del clúster de DBMS: `<SAPSystemSID>-avset-db`
- **Equilibrador de carga interno (ILB) de Azure** con todos los puertos para la instancia de ASCS/SCS y la dirección IP `<SAPSystemSID>-lb-ascs`
-	**Equilibrador de carga interno (ILB) de Azure** con todos los puertos para DBMS de SQL Server y la dirección IP `<SAPSystemSID>-lb-db`
- **Grupo de seguridad de red**: `<SAPSystemSID>-nsg-ascs-0` con puerto RDP externo abierto a la máquina virtual `<SAPSystemSID>-ascs-0`


> [AZURE.NOTE]  Todas las direcciones IP de las tarjetas de red y los equilibradores de carga internos de Azure se crean inicialmente como **dinámicas**. Tiene que cambiarlas a direcciones IP **estáticas**, como se describe más adelante en el documento.


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implementación de máquinas virtuales con conectividad de red corporativa (entre locales) para uso en producción

Para los sistemas SAP de producción, implementará máquinas virtuales de Azure con [conectividad de red corporativa (entre locales)][planning-guide-2.2], por medio de una conexión de Azure de sitio a sitio (VPN) o ExpressRoute.

> [AZURE.NOTE]  En este caso, la red virtual de Azure y la subred ya están creadas y preparadas.


En el campo **NEWOREXISTINGSUBNET**, elija Existente.

En el campo de texto **SUBNETID**, debe agregar la cadena completa de SubnetID de la red de Azure que tenga preparada, donde vaya a implementar las máquinas virtuales de Azure.

Puede obtener una lista de todas las subredes de la red de Azure con este comando de PowerShell:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


Se muestra el identificador **SUBNETID** en el campo Id.

Se puede recuperar una lista de todos los identificadores **SUBNETID** con el siguiente comando de PowerShell:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

El aspecto del identificador **SUBNETID** es similar al siguiente:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implementación solo en la nube de instancias de SAP para pruebas o demostración

También puede implementar el sistema SAP de alta disponibilidad en el modelo de implementación conocido como solo en la nube.

Esta implementación es adecuada principalmente para el caso de uso de demostración, pero no para los de producción.

En el campo NEWOREXISTINGSUBNET, elija _**Nuevo**_. Deje el campo SUBNETID **vacío**.

La red virtual de Azure y la subred se crearán automáticamente con la plantilla de Azure Resource Manager de SAP.

> [AZURE.NOTE] Además, debe implementar al menos una máquina virtual dedicada para AD/DNS en la misma red virtual. Estas máquinas virtuales no se crean mediante la plantilla.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure Virtual Network

En este ejemplo, el espacio de direcciones de la red virtual de Azure es 10.0.0.0/16. Hay una subred llamada _**Subnet**_, con un intervalo de direcciones 10.0.0.0/24. Todas las máquinas virtuales y los equilibradores de carga internos se implementan en esta red virtual.
  
> [AZURE.NOTE] No realice cambios en la configuración de la red dentro del invitado (como la dirección IP, los servidores DNS, la subred, etc.). Todas las configuraciones de red se establecen por medio de Azure y se propagan a través del servicio DHCP.

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Direcciones IP de DNS

Asegúrese de que la opción **Servidores DNS** de la red virtual esté establecida en **DNS personalizado**. En el caso de:

- **[Conectividad de red corporativa (entre locales)][planning-guide-2.2]**: agregue las direcciones IP de los servidores DNS locales. Los servidores DNS locales pueden extenderse a las máquinas virtuales que se ejecutan en Azure. En este caso, puede agregar las direcciones IP de las máquinas virtuales de Azure que estén configuradas para ejecutar el servicio DNS.

-	**[Implementación solo en la nube][planning-guide-2.1]**: implemente una máquina virtual adicional en la misma red virtual, la cual actuará como servidor o servidores DNS. Agregue las direcciones IP de las máquinas virtuales de Azure que estén configuradas para ejecutar el servicio DNS.


![Figura 10: Configuración de servidores DNS para la red virtual de Azure][sap-ha-guide-figure-3001]

_**Figura 10:** Configuración de servidores DNS para la red virtual de Azure_

> [AZURE.NOTE] Si cambia las direcciones IP de los servidores DNS, debe reiniciar las máquinas virtuales de Azure para aplicar el cambio y propagar los nuevos servidores DNS. En este ejemplo, el servicio DNS está instalado y configurado en las siguientes máquinas virtuales Windows:



| Rol de VM | Nombre de host de máquina virtual | Nombre de tarjeta de red | Dirección IP estática  
| ---------------|-------------|--------------------|-------------------
| Primer servidor DNS | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| Segundo servidor DNS | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nombres de host y direcciones IP estáticas para la instancia en clúster de ASCS/SCS de SAP y la instancia en clúster de DBMS

De forma similar al caso local, se necesitan los siguientes nombres de host reservado y direcciones IP:

| Rol de nombre de host virtual | Nombre de host virtual | Dirección IP estática virtual 
| ----------------------------------------------------------------------------|------------------|---------------------------
| Primer nombre de host virtual de clúster de ASCS/SCS de SAP (usado para administrar el clúster) | pr1-ascs-vir | 10\.0.0.42                 
| Nombre de host virtual de **instancia** de ASCS/SCS de SAP | pr1-ascs-sap | `10.0.0.43`             
| Segundo nombre de host virtual de clúster de DBMS de SAP (usado para administrar el clúster) | pr1-dbms-vir | 10\.0.0.32                 
 

Los nombres de host virtual, _**pr1-ascs-vir**_ y _**pr1-dbms-vir**_, y las direcciones IP asociadas, que se usan para administrar el clúster en sí, se crean durante la creación del clúster, tal como se describe en el capítulo [Recopilación de nodos del clúster en la configuración de clúster][sap-ha-guide-8.12.1].

Los otros dos nombres de host virtual, _**pr1-ascs-sap**_ y _**pr1-dbms-sap**_, y las direcciones IP asociadas, que son usadas por la instancia en clúster de ASCS/SCS de SAP y la instancia en clúster de DBMS, pueden crearse manualmente en el servidor DNS, como se describe en el capítulo [Creación de un nombre de host virtual para la instancia de ASCS/SCS de SAP en clúster][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Configuración de direcciones IP estáticas para las máquinas virtuales SAP

Después de implementar las máquinas virtuales para los clústeres, se deben configurar direcciones IP estáticas para todas las máquinas virtuales. Esto no es posible en el sistema operativo invitado, sino que se debe establecer en la configuración de Azure Virtual Network.

Una manera de hacerlo es mediante Azure Portal. En Azure Portal, vaya a:

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

Cambie el campo Asignación de **Dinámica** a **Estática** y escriba el valor que desee en **Dirección IP**.

> [AZURE.NOTE] Si cambia la dirección IP de la tarjeta de red, debe reiniciar las máquinas virtuales de Azure para aplicar el cambio.


![Figura 11: Configuración de la dirección IP estática para la tarjeta de red de cada máquina virtual][sap-ha-guide-figure-3002]

_**Figura 11:** Configuración de la dirección IP estática para la tarjeta de red de cada máquina virtual_

Repita este paso para todas las interfaces de red, es decir, para todas las máquinas virtuales, incluidas las que desee usar para el servicio AD/DNS.

En este ejemplo, aparecen las máquinas virtuales y las direcciones IP estáticas siguientes:

| Rol de VM | Nombre de host de máquina virtual | Nombre de tarjeta de red | Dirección IP estática  
| ----------------------------------------|--------------|--------------------|-------------------
| Primer servidor de aplicaciones de SAP | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| Segundo servidor de aplicaciones de SAP | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| Último servidor de aplicaciones de SAP | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| Primer nodo de clúster para la instancia de ASCS/SCS | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| Segundo nodo de clúster para la instancia de ASCS/SCS | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| Primer nodo de clúster para la instancia de DBMS | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| Segundo nodo de clúster para la instancia de DBMS | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Configuración de dirección IP estática para el equilibrador de carga interno (ILB)

La plantilla de Azure Resource Manager de SAP crea un equilibrador de carga interno (ILB) de Azure que se usa para el clúster de la instancia de ASCS/SCS de SAP y para el clúster de DBMS.

En la implementación inicial, se establece la dirección IP del equilibrador de carga interno como **dinámica**. Es importante cambiar la dirección IP a **estática**.

En este ejemplo, hay dos equilibradores de carga internos de Azure con las siguientes direcciones IP estáticas:

| Rol de equilibrador de carga interno de Azure | Nombre de equilibrador de carga interno de Azure | Dirección IP estática 
| ---------------------------|----------------|-------------------
| Equilibrador de carga interno de instancia de ASCS/SCS de SAP | pr1-lb-ascs | `10.0.0.43`         
| Equilibrador de carga interno de DBMS de SAP | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**Dirección IP del nombre de host virtual de ASCS/SCS de SAP = Dirección IP de Azure Load Balancer de ASCS/SCS de SAP pr1-lb-ascs** **Dirección IP del nombre virtual de DBMS = Dirección IP de Azure Load Balancer de DBMS pr1-lb-dbms**

En este ejemplo, establezca la dirección IP del equilibrador de carga interno _pr1-lb-ascs_ en la dirección IP del nombre de host virtual de la instancia de ASCS/SCS de SAP (`10.0.0.43`).

![Figura 12: Configuración de la dirección IP estática del equilibrador de carga interno (ILB) para la instancia de ASCS/SCS de SAP][sap-ha-guide-figure-3003]

_**Figura 12:** Configuración de la dirección IP estática del equilibrador de carga interno (ILB) para la instancia de ASCS/SCS de SAP_

De la misma manera, establezca la dirección IP del equilibrador de carga _pr1-lb-dbms_ en la dirección IP del nombre de host virtual de la instancia de DBMS (en este ejemplo, 10.0.0.33).

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure

De forma predeterminada, la plantilla de Azure Resource Manager de SAP crea todos los puertos necesarios para:

- Instancia de ASCS ABAP con el número de instancia predeterminado **00**
- Instancia de SCS de Java con el número de instancia predeterminado **01**

Por lo tanto, durante la instalación de la instancia de ASCS/SCS de SAP, tiene que usar estos números de instancia predeterminados, 00 y 01, para la instancia de ASCS ABAP o de SCS de Java.

Se necesitan los siguientes puntos de conexión del equilibrador de carga interno de Azure y se crean para los puertos ASCS ABAP de SAP NetWeaver:


| Servicio/Nombre de regla de equilibrio de carga | Números de puerto predeterminados | Puertos concretos para (instancia de ASCS con el número de instancia 00) (ERS con 10)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Enqueue Server/_lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| ABAP Message Server/_lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| Internal ABAP Message/_lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| Message Server HTTP/_Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| SAP Start Service ASCS HTTP/_Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| SAP Start Service ASCS HTTPS/_Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| Enqueue Replication/_Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| SAP Start Service ERS HTTP/_Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| SAP Start Service ERS HTTP/_Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| Win RM/_Lbrule5985_ | | 5985                                                                     
| File Share/_Lbrule445_ | | 445                                                                      

_**Tabla 1:** Números de puerto de las instancias de ASCS ABAP de SAP NetWeaver_


Se necesitan los siguientes puntos de conexión del equilibrador de carga interno de Azure y se deben crear para los puertos SCS Java de SAP NetWeaver:

| Servicio/Nombre de regla de equilibrio de carga | Números de puerto predeterminados | Puertos concretos para (instancia de SCS con el número de instancia 01) (ERS con 11)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Enqueue Server/_lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| Gateway Server/_lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Java Message Server/_lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| Message Server HTTP/_Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| SAP Start Service SCS HTTP/_Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| SAP Start Service SCS HTTPS/_Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| Enqueue Replication/_Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| SAP Start Service ERS HTTP/_Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| SAP Start Service ERS HTTPS/_Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| Win RM/_Lbrule5985_ | | 5985                                                                     
| File Share/_Lbrule445_ | | 445                                                                      

_**Tabla 2:** Números de puerto de las instancias de SCS Java de SAP NetWeaver_


![Figura 13: Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure][sap-ha-guide-figure-3004]

_**Figura 13:** Reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure_

De la misma manera, establezca la dirección IP del equilibrador de carga _**pr1-lb-dbms**_ en la dirección IP del nombre de host virtual de la instancia de DBMS (en este ejemplo, _**10.0.0.33**_).

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure

Si desea usar otros números de instancia para la instancia de ASCS o de SCS de SAP, debe actualizar los nombres y valores de esos puertos.

Una manera de hacerlo es mediante Azure Portal.

Vaya a `<SID>-lb-ascs load balancer -> Load Balancing Rules`.

Para todas las reglas de equilibrio de carga que pertenezcan a la instancia de ASCS o SCS de SAP, cambie:

- Nombre
- Port
- Puerto back-end

Por ejemplo, si desea cambiar el número de instancia de ASCS predeterminado de 00 a 31, debe hacer los cambios para todos los puertos que aparezcan en _**Tabla 1:** Números de puerto de las instancias de ASCS ABAP de SAP NetWeaver_.

A continuación, se muestra un ejemplo de una actualización para el puerto _lbrule3200_.

![Figura 14: Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure][sap-ha-guide-figure-3005]

_**Figura 14:** Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure_


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Incorporación de máquinas Windows al dominio

Después de asignar direcciones IP estáticas a las máquinas virtuales, agregue estas al dominio.

![Figura 15: Incorporación de una máquina virtual a un dominio][sap-ha-guide-figure-3006]

_**Figura 15:** Incorporación de una máquina virtual a un dominio_


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Incorporación de entradas del Registro en ambos nodos del clúster usados para la instancia de ASCS/SCS de SAP

Las instancias de Azure Load Balancer, incluido el equilibrador de carga interno de Azure, cierran las conexiones cuando estas llevan inactivas cierta cantidad de tiempo (tiempo de espera de inactividad). Por otra parte, los procesos de trabajo de SAP en instancias de diálogo abren conexiones con el proceso de puesta en cola de SAP tan pronto como se deba enviar la primera solicitud de puesta en cola y retirada de cola. Estas conexiones suelen mantenerse hasta que el proceso de trabajo o el de puesta en cola se reinicia. Sin embargo, si la conexión está inactiva algún tiempo, el equilibrador de carga interno de Azure la cerrará. En realidad, no es un problema porque el proceso de trabajo de SAP volverá a establecer la conexión con el proceso de puesta en cola si ha dejado de existir. Sin embargo, estas actividades se documentarán en los seguimientos de desarrollador de los procesos de SAP y, por tanto, crearán una gran cantidad de contenido en dichos ellos sin buena razón. Por lo tanto, se recomienda cambiar los parámetros de TCP/IP `KeepAliveTime` y `KeepAliveInterval` en ambos nodos del clúster. Los cambios de los parámetros de TCP/IP deben combinarse con los parámetros de perfil SAP que se describen más adelante en este documento.

Agregue las siguientes entradas del Registro de Windows en ambos nodos del clúster de Windows para ASCS/SCS de SAP:

| Ruta de acceso | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nombre de la variable | `KeepAliveTime`                                              
| Tipo de variable | REG\_DWORD (Decimal)                                        
| Valor | 120000                                                     
| Vínculo a la documentación | [https://technet.microsoft.com/es-ES/library/cc957549.aspx](https://technet.microsoft.com/es-ES/library/cc957549.aspx) 


_**Tabla 3:** Primer parámetro de TCP/IP que se va a cambiar_


| Ruta de acceso | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nombre de la variable | `KeepAliveInterval`                                          
| Tipo de variable | REG\_DWORD (Decimal)                                        
| Valor | 120000                                                     
| Vínculo a la documentación | [https://technet.microsoft.com/es-ES/library/cc957548.aspx](https://technet.microsoft.com/es-ES/library/cc957548.aspx)


_**Tabla 4:** Segundo parámetro de TCP/IP que se va a cambiar_

A continuación, **reinicie ambos nodos del clúster** para aplicar los cambios.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configuración del clúster de conmutación por error de Windows Server para la instancia de ASCS/SCS de SAP

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Recopilación de nodos del clúster en la configuración de clúster

El primer paso consiste en agregar la característica de clústeres de conmutación por error a ambos nodos del clúster. Esto se hace con el "**Asistente para agregar roles y características**" y no requiere mayor explicación.

En el segundo paso, se configura el clúster de conmutación por error mediante el Administrador de clústeres de conmutación por error de Windows.

En la MMC Administrador de clústeres de conmutación por error, haga clic en Crear clúster y agregue únicamente el nombre del primer nodo de clúster A, por ejemplo, _**pr1-ascs-0**_. No agregue el segundo nodo todavía. Se agregará en un paso posterior.

![Figura 16: Primer paso para agregar una configuración de clúster de conmutación por error: agregar el nombre de servidor o máquina virtual del primer nodo que debe ser nodo del clúster][sap-ha-guide-figure-3007]

_**Figura 16:** Primer paso para agregar una configuración de clúster de conmutación por error: agregar el nombre de servidor o máquina virtual del primer nodo que debe ser nodo del clúster_

En los pasos siguientes, se le pedirá el nombre de red (nombre del host virtual) del clúster.

![Figura 17: Segundo paso para agregar una configuración de clúster de conmutación por error: definir el nombre del clúster][sap-ha-guide-figure-3008]

_**Figura 17:** Segundo paso para agregar una configuración de clúster de conmutación por error: definir el nombre del clúster_


Una vez creado el clúster, se ejecuta una prueba de validación de clúster.

![Figura 18: Último paso para agregar una configuración de clúster de conmutación por error: ejecución de la comprobación de validación de clúster][sap-ha-guide-figure-3009]

_**Figura 18:** Último paso para agregar una configuración de clúster de conmutación por error: ejecución de la comprobación de validación de clúster_


![Figura 19: Último paso para agregar una configuración de clúster de conmutación por error: la comprobación de validación de clúster muestra advertencias si no encuentra ningún disco de cuórum][sap-ha-guide-figure-3010]

_**Figura 19:** Último paso para agregar una configuración de clúster de conmutación por error: la comprobación de validación de clúster muestra advertencias si no encuentra ningún disco de cuórum_

Se puede hacer caso omiso de las advertencias sobre discos en esta fase; más adelante, se agregará un testigo del recurso compartido de archivos junto con los discos compartidos de SIOS. En esta fase no hay necesidad de preocuparse por el cuórum.

![Figura 20: Se define el recurso principal de clúster con la dirección IP: sin embargo, se necesita una nueva dirección IP][sap-ha-guide-figure-3011]

_**Figura 20:** Se define el recurso principal de clúster con la dirección IP: sin embargo, se necesita una nueva dirección IP_


Puesto que la dirección IP del servidor apunta a uno de los nodos de la máquina virtual, el clúster no puede iniciarse. Ahora hay que cambiar la dirección IP del servicio de clúster principal.

Por ejemplo, se debe asignar una dirección IP (en este ejemplo, _**10.0.0.42**_) para el nombre de host virtual del clúster _**pr1-ascs-vir**_. Esto se hace a través de la página de propiedades del recurso IP del servicio de clúster principal como se muestra a continuación.

![Figura 21: Use "Propiedades" para cambiar a la dirección IP correcta][sap-ha-guide-figure-3012]

_**Figura 21:** Use "Propiedades" para cambiar a la dirección IP correcta_


![Figura 22: Asigne la dirección IP reservada para el clúster][sap-ha-guide-figure-3013]

_**Figura 22:** Asigne la dirección IP reservada para el clúster_

Después de cambiar la dirección IP, conecte el nombre de host virtual del clúster.

![Figura 23: Servicio de clúster principal en funcionamiento con la dirección IP correcta][sap-ha-guide-figure-3014]

_**Figura 23:** Servicio de clúster principal en funcionamiento con la dirección IP correcta_

Ahora que el servicio de clúster principal está en funcionamiento, puede agregar el segundo nodo del clúster.

![Figura 24: Agregue el segundo nodo del clúster][sap-ha-guide-figure-3015]

_**Figura 24:** Agregue el segundo nodo del clúster_

![Figura 25: Agregue el nombre de host del segundo nodo del clúster, por ejemplo, pr1-ascs-1][sap-ha-guide-figure-3016]

_**Figura 25:** Agregue el nombre de host del segundo nodo del clúster, por ejemplo, pr1-ascs-1_

![Figura 26: No active la casilla][sap-ha-guide-figure-3017]

_**Figura 26:** No active la casilla_

> [AZURE.NOTE]  
Asegúrese de que la casilla "Agregar todo el almacenamiento apto al clúster" **no** esté activada.

![Figura 27: Haga caso omiso de nuevo de la advertencia sobre el cuórum de disco][sap-ha-guide-figure-3018]

_**Figura 27:** Haga caso omiso de nuevo de la advertencia sobre el cuórum de disco_

Puede pasar por alto las advertencias sobre el cuórum y los discos. La configuración de disco compartido y cuórum se lleva a cabo más tarde, como se describe en el capítulo **[Instalación de SIOS DataKeeper Cluster Edition para un disco compartido de clúster de ASCS/SCS de SAP][sap-ha-guide-8.12.3]**.

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configuración del testigo del recurso compartido de archivos de clúster

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Creación de un recurso compartido de archivos

Se ha elegido un testigo del recurso compartido de archivos en lugar de un disco de cuórum. Esta opción es compatible con SIOS DataKeeper.

En la configuración que se usa para las ilustraciones de este documento, el testigo del recurso compartido de archivos está configurado en el servidor AD/DNS que se ejecuta en Azure y se llama _**domcontr-0**_. Ya que hubiera configurado una conexión VPN a Azure (por medio de una conexión de sitio a sitio o ExpressRoute), AD/DNS reside localmente y, como resultado, no es adecuado para ejecutar un testigo del recurso compartido de archivos.

> [AZURE.NOTE] En el caso de que AD/DNS se ejecute solo localmente, no configure el testigo del recurso compartido de archivos en el sistema operativo Windows de AD/DNS que se ejecuta localmente. La razón es que la latencia de red entre los nodos del clúster que se ejecutan en Azure y en AD/DNS de forma local podría ser excesiva y causar problemas de conectividad. Asegúrese de configurar al testigo del recurso compartido de archivos en una máquina virtual Windows de Azure que se ejecute cerca del nodo del clúster.

La unidad de cuórum necesita como mínimo 1024 MB de espacio disponible. El valor recomendado es 2048 MB.

El primer paso consiste en agregar el objeto de nombre de clúster.

![Figura 28: Asigne los permisos en el recurso compartido para el objeto de nombre de clúster][sap-ha-guide-figure-3019]

_**Figura 28:** Asigne los permisos en el recurso compartido para el objeto de nombre de clúster_

Asegúrese de que los permisos incluyan la posibilidad de cambiar datos en el recurso compartido para el objeto de nombre de clúster (en este ejemplo, _**pr1-ascs-vir$**_). Para agregar el objeto de nombre de clúster a la lista mostrada antes, tiene que presionar "**Agregar**" y cambiar el filtro para permitir también la búsqueda de objetos de equipo, como se muestra a continuación.

![Figura 29: Cambie el tipo de objeto para incluir también los objetos de equipo][sap-ha-guide-figure-3020]

_**Figura 29:** Cambie el tipo de objeto para incluir también los objetos de equipo_

![Figura 30: Active la casilla para objetos de equipo][sap-ha-guide-figure-3021]

_**Figura 30:** Active la casilla para objetos de equipo_

Después de esto, especifique el objeto de nombre de clúster como se muestra en la figura 29. Como ya se debería haber creado el registro, puede cambiar los permisos tal como se muestra en la figura 28.

El siguiente paso consiste en usar la pestaña "Seguridad" del recurso compartido y definir los permisos más pormenorizados para el objeto de nombre de clúster.

![Figura 31: Establezca los atributos de seguridad para el objeto de nombre de clúster en el cuórum de recurso compartido de archivos][sap-ha-guide-figure-3022]

_**Figura 31:** Establezca los atributos de seguridad para el objeto de nombre de clúster en el cuórum de recurso compartido de archivos_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Configuración del cuórum de testigo del recurso compartido de archivos en el Administrador de clústeres de conmutación por error

El siguiente paso consiste en cambiar la configuración del clúster a un testigo del recurso compartido de archivos mediante el Administrador de clústeres de conmutación por error.

![Figura 32: Abra el "Asistente para configurar quórum de clúster" como se muestra aquí][sap-ha-guide-figure-3023]

_**Figura 32:** Abra el "Asistente para configurar quórum de clúster" como se muestra aquí_

![Figura 33: Pantalla de selección de diferentes configuraciones de cuórum][sap-ha-guide-figure-3024]

_**Figura 33:** Pantalla de selección de diferentes configuraciones de cuórum_

En esta selección, debe elegir "_**Seleccionar el testigo de quórum**_".

![Figura 34: Pantalla de selección del testigo del recurso compartido de archivos][sap-ha-guide-figure-3025]

_**Figura 34:** Pantalla de selección del testigo del recurso compartido de archivos_

En este caso, debe elegir "_**Configurar un testigo de recurso compartido de archivos**_".

![Figura 35: Defina la ubicación del recurso compartido de archivos para el recurso compartido testigo][sap-ha-guide-figure-3026]

_**Figura 35:** Defina la ubicación del recurso compartido de archivos para el recurso compartido testigo_


Escriba la ruta de acceso UNC al recurso compartido de archivos (en este ejemplo, `\\domcontr-0\FSW`)

Presione "Siguiente", lo que dará como resultado una lista de los cambios que desee realizar. Compruébelos y presione "Siguiente" de nuevo para cambiar la configuración del clúster.

![Figura 36: Pantalla que muestra la reconfiguración correcta en el clúster][sap-ha-guide-figure-3027]

_**Figura 36:** Pantalla que muestra la reconfiguración correcta en el clúster_

En este último paso, la configuración del clúster debe haberse modificado correctamente.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalación de SIOS DataKeeper Cluster Edition para un disco compartido de clúster de ASCS/SCS de SAP

Actualmente, el estado es el de una configuración de clúster de conmutación por error de Windows Server en funcionamiento en Azure. Sin embargo, esta configuración de clúster aún carece de un recurso de disco compartido. Para instalar una instancia de ASCS/SCS de SAP, se necesita un recurso de disco compartido. Aquí es donde SIOS DataKeeper Cluster Edition entra en juego. Como Azure no permite crear recursos de disco compartido con la funcionalidad necesaria, hay que utilizar, por ejemplo, SIOS DataKeeper para suplirla.

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Incorporación de la característica Microsoft .NET Framework 3.5

Microsoft .NET Framework 3.5 no se habilita ni instala automáticamente en las versiones más recientes de Windows Server. Sin embargo, SIOS DataKeeper necesita .NET Framework en todos los nodos donde se vaya a instalar el producto. Por lo tanto, se debe instalar .NET 3.5 en todos los sistemas operativos invitados de las diferentes máquinas virtuales.

Hay dos maneras de agregar .NET Framework 3.5. La primera posibilidad es usar el "**Asistente para agregar roles y características**" en Windows como se muestra a continuación:

![Figura 37: Instale .NET Framework 3.5 con el "Asistente para agregar roles y características"][sap-ha-guide-figure-3028]

_**Figura 37:** Instale .NET Framework 3.5 con el "Asistente para agregar roles y características"_

![Figura 38: Barra de progreso de la instalación de .NET Framework 3.5 con el "Asistente para agregar roles y características"][sap-ha-guide-figure-3029]

_**Figura 38:** Barra de progreso de la instalación de .NET Framework 3.5 con el "Asistente para agregar roles y características"_

La segunda posibilidad es habilitar la característica .NET Framework 3.5 con la herramienta de línea de comandos _**dism.exe**_. Para este tipo de instalación, el directorio "sxs" de los medios de instalación de Windows debe estar accesible. El siguiente comando se debe ejecutar en una ventana de línea de comandos con permisos elevados:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalación de SIOS DataKeeper

A continuación, se va a explicar la instalación de SIOS DataKeeper Cluster Edition. Debe instalarse en ambos nodos del clúster. SIOS DataKeeper permite la creación de almacenamiento compartido virtual al crear un reflejo sincronizado y simular el almacenamiento compartido del clúster.

Antes de instalar el software SIOS, debe crear un usuario de dominio _**DataKeeperSvc**_.

> [AZURE.NOTE] Agregue este usuario _**DataKeeperSvc**_ al grupo de **administradores locales** en ambos nodos del clúster.
  
Instalación del software SIOS en ambos nodos del clúster

![Instalador de SIOS][sap-ha-guide-figure-3030]

![Figura 39: Primera pantalla de la instalación de SIOS DataKeeper][sap-ha-guide-figure-3031]

_**Figura 39:** Primera pantalla de la instalación de SIOS DataKeeper_

![Figura 40: DataKeeper informa de que un servicio se va a deshabilitar][sap-ha-guide-figure-3032]

_**Figura 40:** DataKeeper informa de que un servicio se va a deshabilitar_

Cuando aparezca la ventana emergente en la figura 40, elija "_**Yes**_" (Sí).

![Figura 41: Selección del usuario para SIOS DataKeeper][sap-ha-guide-figure-3033]

_**Figura 41:** Selección del usuario para SIOS DataKeeper_


En la pantalla anterior, se recomienda elegir _**Domain or Server account**_ (Cuenta de dominio o de servidor).

![Figura 42: Proporcione el usuario de dominio y la contraseña para la instalación de SIOS DataKeeper][sap-ha-guide-figure-3034]

_**Figura 42:** Proporcione el usuario de dominio y la contraseña para la instalación de SIOS DataKeeper_

Especifique la cuenta de dominio que creó para SIOS DataKeeper y las contraseñas de dicha cuenta.

![Figura 43: Proporcione su licencia de SIOS DataKeeper][sap-ha-guide-figure-3035]

_**Figura 43:** Proporcione su licencia de SIOS DataKeeper_

Instale la clave de licencia para su copia de SIOS DataKeeper como se muestra en la figura 43. Al final de la instalación, se le pedirá que **reinicie la máquina virtual**.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configuración de SIOS DataKeeper

Después de instalar SIOS DataKeeper en ambos nodos, es preciso comenzar con la configuración. El objetivo de la configuración es conseguir la replicación sincrónica de datos entre el disco duro virtual adicional conectado a cada una de las máquinas virtuales. En los pasos siguientes se muestra la configuración en ambos nodos.

![Figura 44: Herramienta de configuración y administración de DataKeeper][sap-ha-guide-figure-3036]

_**Figura 44:** Herramienta de configuración y administración de DataKeeper_


Inicie la herramienta de configuración y administración de DataKeeper y presione el vínculo "_**Connect to Server**_" (Conectarse al servidor), que aparece rodeado de un círculo rojo más arriba.

![Figura 45: Inserte el nombre o la dirección TCP/IP del primer nodo y, en un segundo paso, los del segundo nodo; la herramienta de administración debería conectarse][sap-ha-guide-figure-3037]

_**Figura 45:** Inserte el nombre o la dirección TCP/IP del primer nodo y, en un segundo paso, los del segundo nodo; la herramienta de administración debería conectarse_

El siguiente paso es crear el trabajo de replicación entre ambos nodos.

![Figura 46: Cree el trabajo de replicación][sap-ha-guide-figure-3038]

_**Figura 46:** Cree el trabajo de replicación_

Un asistente lo guiará por el proceso.

![Figura 47: Defina el nombre del trabajo de replicación][sap-ha-guide-figure-3039]

_**Figura 47:** Defina el nombre del trabajo de replicación_

![Figura 48: Defina los datos básicos para el nodo que debe ser el de origen actual][sap-ha-guide-figure-3040]

_**Figura 48:** Defina los datos básicos para el nodo que debe ser el de origen actual_

En un primer paso, se deben definir el nombre, la dirección TCP/IP y el volumen de disco del nodo de origen. El segundo paso consiste en definir el nodo de destino. De nuevo, se deben definir el nombre, la dirección TCP/IP y el volumen de disco del nodo de destino.

![Figura 49: Defina los datos básicos para el nodo que debe ser el de destino actual][sap-ha-guide-figure-3041]

_**Figura 49:** Defina los datos básicos para el nodo que debe ser el de destino actual_

El siguiente paso consiste en definir los algoritmos de compresión que se deben aplicar. En este caso, se recomienda comprimir el flujo de replicación. Especialmente en situaciones de resincronización, la compresión del flujo de replicación reduce de forma drástica el tiempo que se tarda en resincronizar. Tenga en cuenta que la compresión utiliza recursos de CPU y RAM de una máquina virtual. Por tanto, cuanto mayor sea la tasa de compresión, más CPU se utilizará. Puede ajustar y cambiar esta configuración más adelante.

Otra configuración que debe comprobar es si la replicación se ejecuta de forma sincrónica o asincrónica. **Para proteger las configuraciones de ASCS/SCS de SAP, se requiere la configuración de replicación sincrónica**.

![Figura 50: Defina los detalles de la replicación][sap-ha-guide-figure-3042]

_**Figura 50:** Defina los detalles de la replicación_

El último paso es definir si se debe volver a presentar el volumen que se replica con el trabajo de replicación a una configuración de clúster WSFC como disco compartido. Para la configuración de ASCS/SCS de SAP, debe elegir "Yes" (Sí) para que el clúster de Windows vea los volúmenes replicados como disco compartido que se pueda usar como volumen de clúster.

![Figura 51: Presione "Yes" para habilitar los volúmenes replicados como volumen de clúster][sap-ha-guide-figure-3043]

_**Figura 51:** Presione "Yes" para habilitar los volúmenes replicados como volumen de clúster_

Una vez finalizada la creación, la herramienta de administración de DataKeeper muestra el trabajo de replicación como activo.

![Figura 52: Reflejo sincrónico de DataKeeper activo para disco compartido de ASCS/SCS de SAP][sap-ha-guide-figure-3044]

_**Figura 52:** Reflejo sincrónico de DataKeeper activo para disco compartido de ASCS/SCS de SAP_

Como resultado, el disco puede ahora verse en el Administrador de clústeres de conmutación por error de Windows como disco de DataKeeper, como se muestra a continuación.

![Figura 53: El disco replicado con DataKeeper aparece en el Administrador de clústeres de conmutación por error][sap-ha-guide-figure-3045]

_**Figura 53:** El disco replicado con DataKeeper aparece en el Administrador de clústeres de conmutación por error_


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalación del sistema SAP NetWeaver

No se va a describir aquí la configuración de DBMS, ya que dependerá del sistema DBMS que se use en cada caso. Sin embargo, se da por supuesto que las inquietudes respecto a la alta disponibilidad con DBMS se solventan con las funcionalidades que admiten los diversos proveedores de DBMS para Azure. Por ejemplo, AlwaysOn o Creación de reflejo de la base de datos para SQL Server y Oracle Data Guard para Oracle. En el escenario de ejemplo usado para esta documentación, no se añadió más protección a DBMS.

Tampoco existen consideraciones especiales respecto a los distintos sistemas DBMS que interactúan con esta configuración de ASCS/SCS de SAP en clúster en Azure.

> [AZURE.NOTE]  
El procedimiento de instalación de sistemas ABAP de SAP NetWeaver, sistemas Java y sistemas ABAP+Java es prácticamente idéntico. La mayor diferencia es que un sistema ABAP de SAP tiene una instancia de ASCS. El sistema Java de SAP tiene una instancia de SCS y el sistema ABAP+Java de SAP, una instancia de ASCS y otra de SCS que se ejecutan en el mismo grupo de clúster de conmutación por error de Microsoft. Cualquier diferencia en la instalación de cada pila de instalación de SAP NetWeaver se mencionará explícitamente. Se da por supuesto que el resto de las partes son iguales.

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalación de SAP con una instancia de ASCS/SCS de alta disponibilidad

> [AZURE.NOTE]  
No coloque el archivo de paginación en los volúmenes reflejados de DataKeeper, porque no lo admite. Puede dejar el archivo de paginación en la unidad D:\\ temporal de una máquina virtual de Azure, donde se coloca ya al implementar una máquina virtual en Azure. Si no es así, corríjalo y coloque el archivo de paginación de Windows en la unidad D:\\ de la máquina virtual de Azure.

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Creación de un nombre de host virtual para la instancia de ASCS/SCS de SAP en clúster

El primer paso es crear la entrada DNS necesaria para el nombre de host virtual de la instancia de ASCS/SCS. Para esto, se usa la herramienta Administrador de DNS de Windows. Además del nombre de host virtual, también se debe definir la dirección IP asignada a dicho nombre.

> [AZURE.NOTE]  
**Tenga en cuenta que la dirección IP que se asigna al nombre de host virtual de la instancia de ASCS/SCS debe ser la misma que se haya asignado a Azure Load Balancer (`<sid>-lb-ascs`) Dirección IP del nombre de host virtual de ASCS/SCS de SAP `(pr1-ascs-sap)` = Dirección IP de Azure Load Balancer `(pr1-lb-ascs)`**

Esto también significa que solo se puede ejecutar un rol de clúster de conmutación por error de SAP (por ejemplo: para el sistema ABAP, una instancia de ASCS; para el sistema Java, una instancia de SCS y para ABAP+Java, una de ASCS y otra de SCS) en un clúster de conmutación por error de Windows Server en Azure.

> [AZURE.NOTE] Los clústeres con varios SID descritos en la guía de instalación de SAP (consulte las [guías de instalación de SAP][sap-installation-guides]) no funcionan actualmente en Azure.

![Figura 54: Definición de la entrada DNS para el nombre virtual del clúster de ASCS/SCS de SAP y la dirección TCP/IP][sap-ha-guide-figure-3046]

_**Figura 54:** Definición de la entrada DNS para el nombre virtual del clúster de ASCS/SCS de SAP y la dirección TCP/IP_

La entrada se muestra en el Administrador de DNS bajo el dominio en la ilustración siguiente.

![Figura 55: Nombre virtual y dirección TCP/IP nuevos para la configuración de clúster de ASCS/SCS de SAP][sap-ha-guide-figure-3047]

_**Figura 55:** Nombre virtual y dirección TCP/IP nuevos para la configuración de clúster de ASCS/SCS de SAP_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalación del primer nodo de clúster de SAP

La instalación del primer nodo de clúster de ASCS/SCS no difiere de ninguna manera de lo incluido en la documentación de instalación de SAP:
- Ejecute la opción de primer nodo del clúster en el nodo de clúster A, por ejemplo, en el host _**pr1-ascs-0**_ como en este ejemplo.

Si desea mantener los puertos predeterminados para el equilibrador de carga interno de Azure, elija:

- Para el **sistema ABAP**: número de instancia de **ASCS** **00**
- Para el **sistema Java**: número de instancia de **SCS** **01**
- Para el **sistema ABAP+Java**: número de instancia de **ASCS** **00** y de **SCS** **01**

Si desea usar números de instancia diferentes de 00 para la instancia de ASCS de ABAP y 01 para la instancia de SCS de Java, debe cambiar primero las reglas predeterminadas de equilibrio de carga del equilibrador de carga interno de Azure. Esto se describe en: **[Cambio de las reglas predeterminadas de equilibrio de carga de ASCS/SCS para el equilibrador de carga interno (ILB) de Azure][sap-ha-guide-8.9]**.

Una vez finalizado este paso, debe realizar algunos más que no se describen en la documentación de instalación de SAP habitual.

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificación del perfil SAP de la instancia de ASCS/SCS

Se debe agregar un nuevo parámetro de perfil, el cual impide el cierre de las conexiones entre los procesos de trabajo de SAP y el servidor de puesta en cola cuando lleven inactivas demasiado tiempo. Ya se ha mencionado el escenario del problema en el capítulo **[Incorporación de entradas del Registro en ambos nodos del clúster usados para la instancia de ASCS/SCS de SAP][sap-ha-guide-8.11]** de este documento. En esa sección, también se presentan dos cambios para algunos parámetros básicos de la conexión TCP/IP. En el segundo paso es necesario configurar el servidor de puesta en cola para que envíe una señal **keep\_alive** de forma que las conexiones no alcancen el umbral de inactividad del equilibrador de carga interno de Azure. Para ello, agregue este parámetro del perfil:

```
enque/encni/set_so_keepalive = true
```

al perfil de la instancia de ASCS/SCS de SAP. En este ejemplo, la ruta de acceso es:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

por ejemplo, al perfil de la instancia de SCS de SAP y la ruta de acceso correspondiente

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Incorporación del puerto de sondeo

Para conseguir que toda la configuración del clúster funcione con Azure Load Balancer, es necesario aprovechar la funcionalidad de sondeo del equilibrador de carga interno. Normalmente, un equilibrador de carga interno de Azure equilibra y distribuye la carga de trabajo entrante por igual entre las máquinas virtuales que participan. Sin embargo, esto no funcionaría en una configuración de clúster de este tipo porque solo hay una instancia activa y la otra es pasiva y no puede aceptar carga de trabajo. Para permitir las configuraciones en que el equilibrador de carga interno de Azure asignará trabajo solamente a las instancias activas, se estableció una funcionalidad de sondeo. Gracias a esa funcionalidad, el equilibrador de carga interno tiene la posibilidad de comprobar cuál de las instancias está activa y así dirigirse la carga de trabajo solo a esa instancia. En primer lugar, se va a mostrar la configuración actual de _**ProbePort**_ con este comando de PowerShell ejecutado en una de las máquinas virtuales que participan en la configuración del clúster:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figura 56: El puerto de sondeo de la configuración de clúster es 0 de forma predeterminada][sap-ha-guide-figure-3048]

_**Figura 56:** El puerto de sondeo de la configuración de clúster es 0 de forma predeterminada_

De forma predeterminada, el número de puerto de sondeo se establece en 0. Para que la configuración funcione, debe definirse un puerto. En este caso, se debe usar el puerto de sondeo _**62300**_, que es el definido en las plantillas de Azure Resource Manager de SAP. Para asignar ese número de puerto, se pueden utilizar los dos comandos siguientes:

Primero obtenga el recurso de clúster para el nombre de host virtual de SAP _**SAP WAC IP**_.

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

Y, a continuación, establezca el puerto de sondeo en 62300.

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Debe detener e iniciar el rol de clúster _**SAP PR1**_ para activar los cambios.

Después de conectar el rol de clúster _**SAP PR1**_, compruebe que _**ProbePort**_ esté establecido en el nuevo valor:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figura 57: Puerto de sondeo del clúster después del cambio][sap-ha-guide-figure-3049]

_**Figura 57:** Puerto de sondeo del clúster después del cambio_

Se ve que _**ProbePort**_ está configurado ahora como _**62300**_. Ahora puede acceder al recurso compartido de archivos _**\\\ascsha-clsap\\sapmnt**_ desde otros hosts como _**ascsha-dbas**_.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalación de la instancia de base de datos

Instalar la instancia de base de datos no es nada diferente del proceso descrito en la documentación de instalación de SAP. Por lo tanto, no se documenta aquí.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instalación del segundo nodo del clúster

De nuevo, la instalación del segundo nodo del clúster no difiere de la documentación de instalación de SAP. Por lo tanto, siga los pasos descritos en la guía de instalación para instalar el segundo nodo del clúster.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Cambio del tipo de inicio del servicio de Windows para la instancia de ERS de SAP

Cambie el tipo de inicio del servicio o servicios de Windows de ERS de SAP a _**Automático (inicio retrasado)**_ en ambos nodos del clúster.

![Figura 58: Cambio de tipo de servicio para la instancia de ERS de SAP a automático retrasado][sap-ha-guide-figure-3050]

_**Figura 58:** Cambio de tipo de servicio para la instancia de ERS de SAP a automático retrasado_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalación del servidor de aplicaciones principal (PAS) de SAP

Ejecute la instalación de la instancia de servidor de aplicaciones principal `<sid>-di-0` en la máquina virtual designada para hospedar PAS. No hay ninguna dependencia de Azure ni aspectos específicos de DataKeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalación del servidor de aplicaciones adicional (AAS) de SAP

Ejecute la instalación de un servidor de aplicaciones adicional de SAP en todas las máquinas virtuales designadas para hospedar un servidor de aplicaciones de SAP, por ejemplo, en `<sid>-di-1` hasta `<sid>-di-<n>`.

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Prueba de la conmutación por error de la instancia de ASCS/SCS de SAP y de la replicación de SIOS

Puede probar y supervisar fácilmente la conmutación por error de una instancia de ASCS/SCS de SAP y la replicación de discos de SIOS mediante el _**Administrador de clústeres de conmutación por error**_ y la interfaz de usuario de SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Punto de partida: la instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster A

El grupo de clúster _**SAP WAC**_ se ejecuta en el nodo de clúster A, por ejemplo, en _**ascsha-clna**_. El disco compartido S:, que forma parte del grupo de clúster _**SAP WAC**_ y que se usa en la instancia de ASCS/SCS, se asigna al nodo de clúster A.

![Figura 59: Administrador de clústeres de conmutación por error: el grupo de clúster <SID> de SAP se ejecuta en el nodo de clúster A][sap-ha-guide-figure-5000]

_**Figura 59:** Administrador de clústeres de conmutación por error: el grupo de clúster <SID> de SAP se ejecuta en el nodo de clúster A_

Mediante la interfaz de usuario de SIOS DataKeeper, puede ver que los datos del disco compartido se replican sincrónicamente desde el volumen de origen S: en el nodo de clúster A (por ejemplo, _**ascsha-clna [10.0.0.41]**_) hacia el volumen de destino _**S:**_ en el nodo de clúster B (por ejemplo, _**ascsha-clnb [10.0.0.42]**_).

![Figura 60: SIOS DataKeeper: Replicación del volumen local del nodo de clúster A al nodo de clúster B][sap-ha-guide-figure-5001]

_**Figura 60:** SIOS DataKeeper: Replicación del volumen local del nodo de clúster A al nodo de clúster B_


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Proceso de conmutación por error del nodo A al nodo B

Puede iniciar una conmutación por error del grupo de clúster <SID> de SAP desde el nodo de clúster A hacia el nodo de clúster B:

- con el Administrador de clústeres de conmutación por error;

- con PowerShell del clúster de conmutación por error;

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- reiniciando el nodo de clúster A en el sistema operativo invitado Windows (esto iniciará una conmutación por error automática del grupo de clúster <SID> de SAP del nodo A al nodo B);

- reiniciando el nodo de clúster A desde Azure Portal (esto iniciará una conmutación por error automática del grupo de clúster <SID> de SAP del nodo A al nodo B);

- reiniciando el nodo de clúster A con Azure PowerShell (esto iniciará una conmutación por error automática del grupo de clúster <SID> de SAP del nodo A al nodo B).

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a> Resultado final: la instancia de ASCS/SCS de SAP se ejecuta en el nodo de clúster B

Después de la conmutación por error, el grupo de clúster `SAP <SID>` se ejecuta en el nodo de clúster B, por ejemplo, _**ascsha-clnb**_.

![Figura 61: Administrador de clústeres de conmutación por error: el grupo de clúster <SID> de SAP se ejecuta en el nodo de clúster B][sap-ha-guide-figure-5002]

_**Figura 61:** Administrador de clústeres de conmutación por error: el grupo de clúster <SID> de SAP se ejecuta en el nodo de clúster B_

Ahora el disco compartido está montado en el nodo de clúster B. SIOS DataKeeper replica datos desde el volumen de origen S: del nodo de clúster B (por ejemplo, _**ascsha-clnb [10.0.0.42]**_) hacia el volumen de destino S: del nodo de clúster A (por ejemplo, _**ascsha-clna [10.0.0.41]**_).

![Figura 62: SIOS DataKeeper: Replicación del volumen local del nodo de clúster B al nodo de clúster A][sap-ha-guide-figure-5003]

_**Figura 62:** SIOS DataKeeper: Replicación del volumen local del nodo de clúster B al nodo de clúster A_

<!---HONumber=AcomDC_0907_2016-->