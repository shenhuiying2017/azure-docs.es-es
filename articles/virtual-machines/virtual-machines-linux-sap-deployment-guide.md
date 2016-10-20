<properties
   pageTitle="SAP NetWeaver en máquinas virtuales (VM) de Linux – Guía de implementación | Microsoft Azure"
   description="SAP NetWeaver en máquinas virtuales (VM) de Linux – Guía de implementación"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# SAP NetWeaver en máquinas virtuales de Azure: guía de implementación

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

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-linux-sap-dbms-guide.md "SAP NetWeaver en máquinas virtuales (VM) de Linux – Guía de implementación de DBMS"
[dbms-guide-2.1]: virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Almacenamiento en caché de máquinas virtuales y VHD"
[dbms-guide-2.2]: virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "Software RAID"
[dbms-guide-2.3]: virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Almacenamiento de Microsoft Azure"
[dbms-guide-2]: virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Estructura de una implementación de RDBMS"
[dbms-guide-3]: virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Alta disponibilidad y recuperación ante desastres con máquinas virtuales de Azure"
[dbms-guide-5.5.1]: virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 y versiones posteriores"
[dbms-guide-5.5.2]: virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 y versiones anteriores"
[dbms-guide-5.6]: virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Uso de imágenes de SQL Server fuera de Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Resumen general de SQL Server para SAP en Azure"
[dbms-guide-5]: virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Detalles para SQL Server RDBMS"
[dbms-guide-8.4.1]: virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configuración de almacenamiento"
[dbms-guide-8.4.2]: virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Copia de seguridad y restauración"
[dbms-guide-8.4.3]: virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Consideraciones de rendimiento para copias de seguridad y restauraciones"
[dbms-guide-8.4.4]: virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Otros"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-linux-sap-deployment-guide.md "SAP NetWeaver en máquinas virtuales (VM) de Linux – Guía de implementación"
[deployment-guide-2.2]: virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Recursos SAP"
[deployment-guide-3.1.2]: virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Implementación de una máquina virtual con una imagen personalizada"
[deployment-guide-3.2]: virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP"
[deployment-guide-3.3]: virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP"
[deployment-guide-3.4]: virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP"
[deployment-guide-3]: virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure"
[deployment-guide-4.1]: virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Implementación de cmdlets de Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Descarga e importación de cmdlets de PowerShell para SAP"
[deployment-guide-4.3]: virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Unión de una máquina virtual a un dominio local (solo Windows)"
[deployment-guide-4.4.2]: virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Descarga, instalación y habilitación del agente de máquina virtual de Azure"
[deployment-guide-4.5.1]: virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-4.5]: virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurar la extensión de supervisión mejorada de Azure para SAP"
[deployment-guide-5.1]: virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Comprobación de preparación de la supervisión mejorada de Azure para SAP"
[deployment-guide-5.2]: virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Comprobación de estado de la configuración de la infraestructura de supervisión de Azure"
[deployment-guide-5.3]: virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configuración de la supervisión"
[deployment-guide-configure-proxy]: virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configuración de proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Comprobaciones y solución de problemas de configuración de supervisión de extremo a extremo para SAP en Azure"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-linux-sap-get-started.md
[getting-started-dbms]: virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-linux-sap-planning-guide.md "SAP NetWeaver en máquinas virtuales (VM) de Linux – Guía de planeación e implementación"
[planning-guide-1.2]: virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Recursos"
[planning-guide-11]: virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Alta disponibilidad (HA) y recuperación ante desastres (DR) para la ejecución de SAP NetWeaver en máquinas virtuales de Azure"
[planning-guide-11.4.1]: virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Alta disponibilidad en los servidores de aplicaciones de SAP"
[planning-guide-11.5]: virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Uso de Autostart en las instancias de SAP"
[planning-guide-2.1]: virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente"
[planning-guide-2.2]: virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Entre sitios locales: implementación de una o varias máquinas virtuales de SAP en Azure con el requisito de estar totalmente integradas en la red local"
[planning-guide-3.1]: virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Regiones de Azure"
[planning-guide-3.2.1]: virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Dominios de error"
[planning-guide-3.2.2]: virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Dominios de actualización"
[planning-guide-3.2.3]: virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Conjuntos de disponibilidad de Azure"
[planning-guide-3.2]: virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "El concepto de máquina virtual de Microsoft Azure"
[planning-guide-3.3.2]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Almacenamiento Premium de Azure"
[planning-guide-5.1.1]: virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado"
[planning-guide-5.1.2]: virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Implementación de una máquina virtual con una imagen específica del cliente"
[planning-guide-5.2.1]: virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado"
[planning-guide-5.2.2]: virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP"
[planning-guide-5.2]: virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Preparación de máquinas virtuales con SAP para Azure"
[planning-guide-5.3.1]: virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Diferencia entre un disco y una imagen de Azure"
[planning-guide-5.3.2]: virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Carga de un VHD desde una instalación local a Azure"
[planning-guide-5.4.2]: virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copia de discos entre cuentas de almacenamiento de Azure"
[planning-guide-5.5.1]: virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Estructura de VM/VHD para implementaciones de SAP"
[planning-guide-5.5.3]: virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Configuración de montaje automático para discos conectados"
[planning-guide-7.1]: virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Escenario de demostración/aprendizaje de máquina virtual única con SAP NetWeaver"
[planning-guide-7]: virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Conceptos de implementación solo en la nube de instancias de SAP"
[planning-guide-9.1]: virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Solución de supervisión de Azure para SAP"
[planning-guide-azure-premium-storage]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Almacenamiento Premium de Azure"

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
[planning-guide-microsoft-azure-networking]: virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Redes de Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Almacenamiento: Almacenamiento de Microsoft Azure y discos de datos"

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
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Administración de máquinas virtuales con el Administrador de recursos de Azure y con PowerShell"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
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
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.

Microsoft Azure permite a las empresas adquirir recursos de proceso y almacenamiento en un tiempo mínimo sin ciclos de adquisición prolongados. Máquinas virtuales de Azure permite a las empresas implementar aplicaciones clásicas (por ejemplo, aplicaciones basadas en SAP NetWeaver) en Azure y extender su confiabilidad y disponibilidad sin tener más recursos disponibles de forma local. Microsoft Azure también admite la conectividad entre sitios locales, que permite a las empresas integrar activamente las máquinas virtuales de Azure en sus dominios locales, nubes privadas e infraestructura del sistema SAP.

Estas notas del producto describen paso a paso cómo está preparada una máquina virtual de Azure para la implementación de aplicaciones basadas en SAP NetWeaver. Para estas notas del producto, se debe conocer la información incluida en la [Guía de planeamiento e implementación][planning-guide]. Si no es así, primero se debe leer el documento en cuestión.

El documento complementa la Documentación de instalación de SAP y las Notas de SAP, que representan los principales recursos para las instalaciones e implementaciones de software de SAP en las plataformas proporcionadas.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## Introducción
Una gran cantidad de compañías de todo el mundo usan aplicaciones basadas en SAP NetWeaver, principalmente SAP Business Suite, para ejecutar sus procesos críticos de negocio. Por lo tanto, el estado del sistema es un recurso fundamental y, además, la capacidad de ofrecer soporte técnico empresarial en caso de un error de funcionamiento, incluidos incidentes de rendimiento, se convierte en un requisito esencial. Microsoft Azure ofrece instrumentación de plataforma superior para adaptarse a los requisitos de compatibilidad de todas las aplicaciones fundamentales para el negocio. Esta guía asegura que una Máquina virtual de Microsoft Azure orientada a la implementación de software de SAP esté configurada de manera tal que se puede ofrecer soporte técnico empresarial, independientemente de la manera en que se creó la máquina virtual, ya sea si se seleccionó de Azure Marketplace o se creó a partir de una imagen específica de cliente. A continuación, todos los pasos de configuración necesarios se describen detalladamente.

## Requisitos previos y recursos
### Requisitos previos
Antes de comenzar, asegúrese de cumplir los requisitos previos que se describen en los capítulos siguientes.

#### Equipo local
La configuración de una Máquina virtual de Azure para la implementación de software de SAP consta de varios pasos. Para administrar máquinas virtuales de Windows o Linux, debe usar un script de PowerShell y el Portal de Microsoft Azure. Para eso, necesita un equipo con Windows 7 o una versión superior. Si solo desea administrar máquinas virtuales de Linux y desea usar una máquina Linux para esta tarea, también puede usar la interfaz de la línea de comandos de Azure (CLI de Azure).

#### Conexión a Internet
Para descargar y ejecutar las herramientas y los scripts necesarios, debe tener una conexión a Internet. Además, la Máquina virtual de Microsoft Azure que ejecuta la extensión de supervisión mejorada de Azure debe tener acceso a Internet. Ante la eventualidad de que esta VM de Azure forme parte de una red virtual de Azure o de un dominio local, asegúrese de que la configuración de proxy pertinente esté establecida según lo descrito en el capítulo [Configuración de proxy][deployment-guide-configure-proxy] de este documento.

#### Suscripción de Microsoft Azure
Ya existe una cuenta de Azure y se conocen las credenciales de inicio de sesión correspondientes.

#### Consideración de topología y redes
Se debe definir la topología y la infraestructura de la implementación de SAP en Azure. Con respecto a la arquitectura, se debe considerar lo siguiente:

* Las cuentas de Almacenamiento de Microsoft Azure que se usarán.
* La red virtual en la que se implementará el sistema SAP.
* El grupo de recursos en el que se implementará el sistema SAP.
* La región de Azure donde se implementará el sistema SAP.
* La configuración de SAP (de 2 o 3 niveles).
* Los tamaños de VM y la cantidad de VHD adicionales que se montarán en las VM.
* La configuración del sistema de corrección y transporte de SAP.

Ya debe haber cuentas de Almacenamiento de Azure o redes virtuales de Azure creadas y configuradas como tales. La [Guía de planeamiento e implementación][planning-guide] describe cómo crearlas y configurarlas.

#### Tamaño de SAP
* La carga de trabajo proyectada de SAP está determinada, por ejemplo, por la herramienta SAP Quicksizer y se sabe la cantidad de SAP correspondiente.
* Se debe saber cuánta memoria y cuántos recursos de CPU consume el sistema SAP.
* Se debe saber cuántas operaciones de E/S se requieren por segundo.
* Se conoce el ancho de banda de red que se requiere ante una eventual comunicación entre distintas VM en Azure.
* Se conoce el ancho de banda de red que se requiere entre los recursos locales y los sistemas SAP implementados en Azure.

#### Grupos de recursos
Los grupos de recursos son un concepto nuevo que incluye todos los recursos que tienen el mismo ciclo de vida; por ejemplo, que se crearon y eliminaron al mismo tiempo. Obtenga más información sobre los grupos de recursos en [este artículo][resource-group-overview].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos SAP
Durante el trabajo de configuración, se requieren los recursos siguientes:

* Nota de SAP [1928533]
	* La lista de tamaños de Máquinas virtuales de Azure, que se admite para la implementación de software de SAP.
	* Información importante sobre capacidad por tamaño de Máquina virtual de Azure.
	* La combinación admitida de software de SAP y SO y BD.
* La nota de SAP [2015553], que muestra los requisitos previos que debe admitir SAP cuando se implemente software de SAP en Microsoft Azure.
* La nota de SAP [1999351], que incluye información adicional para la solución de problemas de la supervisión mejorada de Azure para SAP.
* La nota de SAP [2178632], que incluye información detallada sobre todas las métricas de supervisión disponibles para SAP en Microsoft Azure.
* La nota de SAP [1409604], que incluye la versión requerida de SAP Host Agent para Windows en Microsoft Azure cuando se implemente en el nuevo Azure Resource Manager.
* La nota de SAP [2191498], que incluye la versión requerida de SAP Host Agent para Linux en Microsoft Azure cuando se implemente en el nuevo Azure Resource Manager.
* La nota de SAP [2243692], que contiene información sobre las licencias de SAP en Linux en Azure.
* La nota de SAP [1984787] que contiene información general sobre SUSE LINUX Enterprise Server 12.
* La nota de SAP [2002167] que contiene información general sobre Red Hat Enterprise Linux 7.x.
* [SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contiene todas las notas de SAP que se requieren para Linux.
* Cmdlets de PowerShell específicos para SAP y que forman parte de [Azure PowerShell][azure-ps].
* CLI de Azure específica para SAP y que forma parte de la [CLI de Azure][azure-cli].
* [Portal de Microsoft Azure][azure-portal]

[comment]: <> (MSSedusch TODO Add ARM patch level for SAP Host Agent in SAP Note 1409604)
 
Las guías siguientes también abarcan el tema de SAP en Microsoft Azure:

* [SAP NetWeaver en máquinas virtuales de Azure: guía de planeación e implementación][planning-guide]
* [SAP NetWeaver en máquinas virtuales de Azure: guía de implementación (este documento)][deployment-guide]
* [SAP NetWeaver en máquinas virtuales de Azure: guía de implementación de DBMS][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure
En este capítulo, aprenderá las distintas formas de implementación y los pasos de cada tipo de implementación.

### Implementación de máquinas virtuales para SAP
Microsoft Azure ofrece varias maneras de implementar máquinas virtuales y discos asociados. Por tanto, es muy importante comprender las diferencias, puesto que los preparativos de las máquinas virtuales pueden variar según la forma de implementación. En general, observamos los siguientes escenarios:

#### Implementación de máquinas virtuales de Azure Marketplace
Desea seleccionar una imagen proporcionada por Microsoft o un tercero en Azure Marketplace para implementar la máquina virtual. Una vez que se ha implementado la máquina virtual en Microsoft Azure, sigue las mismas instrucciones y utiliza las mismas herramientas para instalar el software de SAP en la máquina virtual de la misma forma que lo haría en un entorno local. Para instalar el software de SAP en la máquina virtual de Azure, SAP y Microsoft recomiendan cargar y almacenar el medio de instalación de SAP en los VHD de Azure, o bien crear una máquina virtual de Azure que funcione como un servidor de archivos que contenga todos los medios de instalación de SAP que resulten necesarios.

[comment]: <> (MSSedusch TODO why do we need to recommend a file management e.g. File Server or VHD? Is that so different from on-premises?)

Consulte más detalles en el capítulo [Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP][deployment-guide-3.2].

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Implementación de una máquina virtual con una imagen personalizada
Debido a los requisitos de revisión específicos en lo que respecta a la versión del SO o DBMS, puede que las imágenes proporcionadas en Azure Marketplace no satisfagan sus necesidades. Por lo tanto, se recomienda crear una máquina virtual con su propia imagen privada de máquina virtual de SO o DBMS que pueda implementarse varias veces más adelante. Los pasos para crear una imagen privada son distintos para una imagen de Windows y una imagen de Linux.

___

> ![Windows][Logo_Windows] Windows
>
> Para preparar una imagen de Windows que se pueda usar para implementar varias máquinas virtuales, la configuración de Windows (como el nombre de host y SID de Windows) debe estar resumida o generalizada en la máquina virtual local. Para ello, use sysprep tal como se describe en <https://technet.microsoft.com/library/cc721940.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar una imagen de Linux que se pueda usar para implementar varias máquinas virtuales, parte de la configuración de Linux debe estar resumida o generalizada en la máquina virtual local. Para ello, use waagent -deprovision tal como se describe en [este artículo][virtual-machines-linux-capture-image] o en [este otro artículo][virtual-machines-linux-agent-user-guide-command-line-options].

___

Puede configurar el contenido de base de datos con el administrador de aprovisionamiento de software de SAP para instalar un nuevo sistema SAP, restaurar una copia de seguridad de base de datos a partir de un VHD conectado a la máquina virtual o restaurar directamente una copia de seguridad de base de datos desde Almacenamiento de Azure si DBMS admite esa función. (Consulte la [Guía de implementación de DBMS][dbms-guide]). Si ya instaló un sistema SAP en la máquina virtual local (especialmente para los sistemas de 2 niveles), puede adaptar la configuración del sistema SAP después de realizar la implementación de la máquina virtual de Azure mediante el procedimiento de cambio de nombre de sistema del administrador de aprovisionamiento de software de SAP (nota de SAP [1619720]). De lo contrario, puede instalar el software de SAP después de la implementación de la máquina virtual de Azure.

Consulte más detalles en el capítulo [Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP][deployment-guide-3.3].

#### Procedimiento para mover máquinas virtuales del entorno local a Microsoft Azure con un disco no generalizado
Planea mover un sistema SAP específico desde un entorno local a Microsoft Azure. Esto puede hacerse cargando en Microsoft Azure el VHD que contiene el sistema operativo, los archivos binarios de SAP y los finales de DBMS, así como los discos duros virtuales con los archivos de registro y de datos del DBMS. En contraposición con el escenario descrito anteriormente en el capítulo [Implementación de una máquina virtual con una imagen personalizada][deployment-guide-3.1.2], mantendrá el nombre de host, el SID de SAP y las cuentas de usuario de SAP en la máquina virtual de Azure, ya que se configuraron en el entorno local. Por lo tanto, no es necesario generalizar el sistema operativo. Este caso se aplicará principalmente a escenarios entre sitios locales donde una parte de la infraestructura de SAP se ejecuta de forma local y otra en Microsoft Azure.

Consulte más detalles en el capítulo [Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP][deployment-guide-3.4].

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Escenario 1: Implementación de una máquina virtual de Azure Marketplace para SAP
Microsoft Azure ofrece la posibilidad de implementar una instancia de máquina virtual desde Azure Marketplace, lo que ofrece algunas imágenes estándar del SO de Windows Server y distintas distribuciones de Linux. También es posible implementar una imagen que incluya SKU de DBMS, como SQL Server. Consulte la [Guía de implementación de DBMS][dbms-guide] para ver los detalles sobre cómo usar esas imágenes con SKU de DBMS.

La secuencia específica para SAP de los pasos de implementación de una VM desde Azure Marketplace tendría un aspecto similar al siguiente:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con una imagen de VM de Azure Marketplace][deployment-guide-figure-100]

Siguiendo el diagrama de flujo, se deben ejecutar los pasos siguientes:

#### Creación de una máquina virtual a través del Portal de Azure
La manera más fácil de crear una máquina virtual nueva con una imagen de Azure Marketplace es a través del Portal de Azure. Vaya a <https://portal.azure.com/#create>. Escriba el tipo de sistema operativo que desea implementar en el campo de búsqueda, por ejemplo, Windows, SLES o RHEL y, luego, seleccione la versión. Asegúrese de seleccionar el modelo de implementación "Azure Resource Manager" y haga clic en Crear.

El asistente le guiará a través de los parámetros que se requieren para crear la máquina virtual junto con todos los recursos necesarios, como interfaces de red o cuentas de almacenamiento. Algunos de estos parámetros son:

1. Aspectos básicos
    1. Nombre: el nombre del recurso, es decir, el nombre de la máquina virtual.
    1. Nombre de usuario y contraseña/clave pública SSH: escriba el nombre de usuario y la contraseña que se crearon durante el aprovisionamiento. En una máquina virtual de Linux, también puede escribir la clave SSH pública que quiere usar para iniciar sesión en la máquina con SSH.
    1. Suscripción: seleccione la suscripción que quiere usar para aprovisionar la máquina virtual nueva.
    1. Grupo de recursos: el nombre del grupo de recursos. Puede insertar el nombre de un grupo de recursos nuevo o de uno existente.
    1. Ubicación: seleccione la ubicación donde se implementará la máquina virtual nueva. Si desea conectar la máquina virtual con la red local, asegúrese de seleccionar la ubicación de la red virtual que conecta Azure con su red local. Consulte el capítulo [Redes de Microsoft Azure][planning-guide-microsoft-azure-networking] de la [Guía de planeamiento][planning-guide] para más detalles.
1. Tamaño: lea la nota de SAP [1928533] para ver una lista de los tipos de VM que se admiten. También asegúrese de seleccionar el tipo correcto si quiere usar Almacenamiento premium. No todos los tipos de VM son compatibles con el Almacenamiento premium. Consulte los capítulos [Almacenamiento: Almacenamiento de Microsoft Azure y discos de datos][planning-guide-storage-microsoft-azure-storage-and-data-disks] y [Almacenamiento Premium de Azure][planning-guide-azure-premium-storage] de la [Guía de planeamiento][planning-guide] para más información.
1. Settings
    1. Cuenta de almacenamiento: puede seleccionar una cuenta de almacenamiento existente o crear una nueva. Lea el capítulo [Almacenamiento de Microsoft Azure][dbms-guide-2.3] de la [Guía de DBMS][dbms-guide] para más detalles sobre los distintos tipos de almacenamiento. Tenga en cuenta que no todos los tipos de almacenamiento admiten la ejecución de aplicaciones SAP.
    1. Red virtual y subred: seleccione la red virtual que está conectada a la red local si desea integrar la máquina virtual a la intranet.
    1. Dirección IP pública: seleccione la dirección IP pública que quiere usar o escriba los parámetros para crear una dirección IP pública nueva. Puede usar una dirección IP pública para tener acceso a la máquina virtual a través de Internet. Asegúrese de crear también un grupo de seguridad de red para filtrar el acceso a la máquina virtual.
    1. Grupo de seguridad de red: consulte [Qué es un grupo de seguridad de red][virtual-networks-nsg] para más detalles.
    1. Supervisión: puede deshabilitar la configuración de diagnóstico Se habilitará automáticamente cuando ejecute los comandos para habilitar la supervisión mejorada de Azure, tal como se describe en el capítulo [Configurar la supervisión][deployment-guide-configure-monitoring-scenario-1].
    1. Disponibilidad: seleccione un conjunto de disponibilidad o escriba los parámetros para crear un conjunto de disponibilidad nuevo. Consulte el capítulo [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3] para más información.
1. Resumen: valide la información que se entrega en la página de resumen y haga clic en Aceptar.

Cuando finalice el asistente, la máquina virtual se implementará en el grupo de recursos que seleccionó.

#### Creación de una máquina virtual con una plantilla
También puede crear una implementación con una de las plantillas de SAP que están publicadas en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. O bien, puede crear una máquina virtual manualmente con el [Portal de Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o la [CLI de Azure][virtual-machines-linux-tutorial].

* [Plantilla de configuración de 2 niveles (solo una máquina virtual)][sap-templates-2-tier-marketplace-image] Use esta plantilla si quiere crear un sistema de 2 niveles con solo una máquina virtual.
* [Plantilla de configuración de 3 niveles (varias máquinas virtuales)][sap-templates-3-tier-marketplace-image] Use esta plantilla si quiere crear un sistema de 3 niveles con varias máquinas virtuales.

Una vez que abre una de las plantillas anteriores, el Portal de Azure le dirige al panel Editar parámetros. Escriba la siguiente información:

* **sapSystemId**: el id. del sistema SAP.
* **osType**: el sistema operativo que quiere implementar, como Windows Server 2012 R2, SLES 12 o RHEL 7.2.
    * La lista solo incluye las versiones compatibles con SAP en Microsoft Azure.
* **sapSystemSize**: el tamaño del sistema SAP.
    * La cantidad de SAPS que proporcionará el sistema nuevo. Si no está seguro de cuántos SAPS necesitará el sistema, consulte con el integrador de sistemas o el socio tecnológico de SAP.
* **systemAvailability**: la disponibilidad del sistema, solo para las plantillas de 3 niveles.
    * Seleccione HA para una configuración en la que se puede realizar una instalación de alta disponibilidad. Se crearán dos servidores de base de datos y dos servidores para ASCS.
* storageType: el tipo de almacenamiento que se debe usar, solo para las plantillas de 2 niveles.
    * El uso de Almacenamiento premium se recomienda para los sistemas de mayor tamaño. Para más información sobre los distintos tipos de almacenamiento, lea
        * [Almacenamiento de Microsoft Azure][dbms-guide-2.3] de la [Guía de DBMS][dbms-guide].
        * [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure][storage-premium-storage-preview-portal]
        * [Introducción a Almacenamiento de Microsoft Azure][storage-introduction]
* **adminUsername** y **adminPassword**: el nombre de usuario y la contraseña.
    * Se crea un usuario nuevo que se puede usar para iniciar sesión en la máquina.
* **newOrExistingSubnet**: determina si es necesario crear una red virtual y subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione la existente.
* **subnetId**: el id. de la subred a la que deben conectarse las máquinas virtuales. Seleccione la subred de la VPN o la red virtual de Express Route para conectar la máquina virtual a la red local. Generalmente, el id. tiene un aspecto similar al siguiente: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

Una vez que escriba todos los parámetros, seleccione la suscripción y el grupo de recursos que quiere usar. Puede seleccionar un grupo de recursos existente o crear uno nuevo; para ello, seleccione "+ Nuevo" en el menú desplegable. Si crea un grupo de recursos nuevo, también puede seleccionar la región en la que se creará el grupo de recursos y la máquina virtual.

Revise y acepte las condiciones legales y, luego, haga clic en Crear.

Tenga en cuenta que el agente de máquina virtual de Azure se implementa de manera predeterminada cuando se usa una imagen de Azure Marketplace.

#### Configuración de los valores de proxy
Según la configuración de red local, puede que sea necesario configurar el proxy en la máquina virtual si se conecta a la red local a través de VPN o Express Route. De lo contrario, es posible que la máquina virtual no pueda tener acceso a Internet y, por lo tanto, no se puedan descargar las extensiones necesarias ni recopilar datos de supervisión. Consulte el capítulo [Configuración de proxy][deployment-guide-configure-proxy] de este documento.

#### Unión a un dominio (solo Windows)
En el caso de que la implementación en Azure esté conectada a AD/DNS local a través de sitio a sitio de Azure o Express Route (que también se denomina como una conexión entre sitios locales en la [Guía de planeamiento e implementación][planning-guide]), se espera que la VM se una a un dominio local. Consulte las consideraciones de este paso en el capítulo [Unión de una VM a un dominio local (solo Windows)][deployment-guide-4.3] de este documento.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configuración de la supervisión
Configure la extensión de supervisión mejorada de Azure para SAP tal como se describe en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5] de este documento.

Compruebe los requisitos previos de la supervisión de SAP para ver las versiones mínimas requeridas de SAP Kernel y SAP Host Agent en los recursos que aparecen en el capítulo [Recursos SAP][deployment-guide-2.2] de este documento.

#### Comprobación de la supervisión
Compruebe si la supervisión funciona según lo descrito en el capítulo [Comprobaciones y solución de problemas de configuración de supervisión de extremo a extremo para SAP en Azure][deployment-guide-troubleshooting-chapter].

#### Pasos posteriores a la implementación
La VM se implementará una vez creada y deberá instalar en ella todos los componentes de software necesarios. Por lo tanto, para este tipo de implementación de VM, el software necesario ya debe estar disponible en Microsoft Azure en alguna otra máquina virtual o como un disco que se pueda conectar. O bien estamos en presencia de escenarios entre sitios locales en los que la conectividad con los recursos locales (recursos compartidos de instalación) es un hecho.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP
Tal como se describe detalladamente en la [Guía de planeamiento e implementación][planning-guide], hay una forma de preparar, crear y usar una imagen personalizada para crear varias máquinas virtuales nuevas. La secuencia de pasos del diagrama de flujo tendrá un aspecto similar al siguiente:
 
![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con una imagen de VM de Marketplace privado][deployment-guide-figure-300]

Siguiendo el diagrama de flujo, se deben ejecutar los pasos siguientes:

#### Create virtual machine
Para crear una implementación con una imagen de SO privada mediante el Portal de Azure, use una de las plantillas de SAP que están publicadas en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. También puede crear una máquina virtual manualmente con [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [Plantilla de configuración de 2 niveles (solo una máquina virtual)][sap-templates-2-tier-user-image] Use esta plantilla si quiere crear un sistema de 2 niveles con solo una máquina virtual y su propia imagen de SO.
* [Plantilla de configuración de 3 niveles (varias máquinas virtuales)][sap-templates-3-tier-user-image] Use esta plantilla si quiere crear un sistema de 3 niveles con varias máquinas virtuales y su propia imagen de SO.

Una vez que abre una de las plantillas anteriores, el Portal de Azure le dirige al panel Editar parámetros. Escriba la siguiente información:

* **sapSystemId**: el id. del sistema SAP.
* **osType**: el sistema operativo que quiere implementar, ya sea Windows o Linux.
* **sapSystemSize**: el tamaño del sistema SAP.
    * La cantidad de SAPS que proporcionará el sistema nuevo. Si no está seguro de cuántos SAPS necesitará el sistema, consulte con el integrador de sistemas o el socio tecnológico de SAP.
* **systemAvailability**: la disponibilidad del sistema, solo para las plantillas de 3 niveles.
    * Seleccione HA para una configuración en la que se puede realizar una instalación de alta disponibilidad. Se crearán dos servidores de base de datos y dos servidores para ASCS.
* **storageType**: el tipo de almacenamiento que se debe usar, solo para las plantillas de 2 niveles.
    * El uso de Almacenamiento premium se recomienda para los sistemas de mayor tamaño. Para más información sobre los distintos tipos de almacenamiento, lea
        * [Almacenamiento de Microsoft Azure][dbms-guide-2.3] de la [Guía de DBMS][dbms-guide].
        * [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure][storage-premium-storage-preview-portal]
        * [Introducción a Almacenamiento de Microsoft Azure][storage-introduction]
* **adminUsername** y **adminPassword**: el nombre de usuario y la contraseña.
    * Se crea un usuario nuevo que se puede usar para iniciar sesión en la máquina.
* **userImageVhdUri**: el URI del VHD de la imagen de SO privada; por ejemplo, https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
* **userImageStorageAccount**: el nombre de la cuenta de almacenamiento en que se almacena la imagen de SO privada; por ejemplo, `<accountname`> en el URI de ejemplo anterior.
* **newOrExistingSubnet**: determina si es necesario crear una red virtual y subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione la existente.
* **subnetId**: el id. de la subred a la que deben conectarse las máquinas virtuales. Seleccione la subred de la VPN o la red virtual de Express Route para conectar la máquina virtual a la red local. Generalmente, el id. tiene un aspecto similar al siguiente: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

Una vez que escriba todos los parámetros, seleccione la suscripción y el grupo de recursos que quiere usar. Puede seleccionar un grupo de recursos existente o crear uno nuevo; para ello, seleccione "+ Nuevo" en el menú desplegable. Si crea un grupo de recursos nuevo, también puede seleccionar la región en la que se creará el grupo de recursos y la máquina virtual.

Revise y acepte las condiciones legales y, luego, haga clic en Crear.

#### Instalación de un agente de máquina virtual (solo Linux)
El agente de Linux ya debe estar instalado en la imagen de usuario si quiere usar las plantillas anteriores. De lo contrario, se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la imagen de usuario, tal como se describe en el capítulo [Descarga, instalación y habilitación del agente de máquina virtual de Azure][deployment-guide-4.4] de este documento. Si no usa las plantillas anteriores, también puede instalar más adelante el agente de máquina virtual.

#### Unión a un dominio (solo Windows)
En el caso de que la implementación en Azure esté conectada a AD/DNS local a través de sitio a sitio de Azure o Express Route (que también se denomina como una conexión entre sitios locales en la [Guía de planeamiento e implementación][planning-guide]), se espera que la VM se una a un dominio local. Consulte las consideraciones de este paso en el capítulo [Unión de una VM a un dominio local (solo Windows)][deployment-guide-4.3] de este documento.

#### Configuración de los valores de proxy
Según la configuración de red local, puede que sea necesario configurar el proxy en la máquina virtual si se conecta a la red local a través de VPN o Express Route. De lo contrario, es posible que la máquina virtual no pueda tener acceso a Internet y, por lo tanto, no se puedan descargar las extensiones necesarias ni recopilar datos de supervisión. Consulte el capítulo [Configuración de proxy][deployment-guide-configure-proxy] de este documento.

#### Configuración de la supervisión
Configure la extensión de supervisión de Azure para SAP tal como se describe en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5] de este documento. Compruebe los requisitos previos de la supervisión de SAP para ver las versiones mínimas requeridas de SAP Kernel y SAP Host Agent en los recursos que aparecen en el capítulo [Recursos SAP][deployment-guide-2.2] de este documento.

#### Comprobación de la supervisión
Compruebe si la supervisión funciona según lo descrito en el capítulo [Comprobaciones y solución de problemas de configuración de supervisión de extremo a extremo para SAP en Azure][deployment-guide-troubleshooting-chapter].

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Escenario 3: Mover una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP
Este escenario abarca el caso de un sistema SAP que simplemente se mueve en su forma actual del sitio local a Azure. Esto significa que no se realiza ningún cambio de nombre de host de Windows o Linux ni del SID de SAP ni nada similar. En este caso, no se hace referencia al VHD como imagen durante la implementación, pero sí se usa directamente como el disco del SO. En lo que respecta a la implementación, este caso es distinto a los dos casos anteriores porque el agente de máquina virtual no se puede instalar automáticamente durante la implementación. Por lo tanto, es necesario descargar el agente de máquina virtual de Azure desde Microsoft y se debe instalar y habilitar manualmente dentro de la máquina virtual más adelante. Una vez que esta tarea se realiza correctamente, puede seguir iniciando la extensión de supervisión de host de SAP de Azure y su configuración. Consulte este artículo para detalles sobre la función del agente de máquina virtual de Azure:

[comment]: <> (MSSedusch TODO Update Windows Link below)

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Guía de usuario del Agente de Linux de Azure][virtual-machines-linux-agent-user-guide]

___

El flujo de trabajo de los distintos pasos tiene un aspecto similar al siguiente:
 
![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con un disco de VM][deployment-guide-figure-400]

Siga estos pasos siempre que el disco ya esté cargado y definido en Azure (consulte la [Guía de planeamiento e implementación][planning-guide]).

#### Create virtual machine
Para crear una implementación con un disco de SO privado a través del Portal de Azure, use la plantilla de SAP que está publicada en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. También puede crear una máquina virtual manualmente con PowerShell o la CLI de Azure.

* [Plantilla de configuración de 2 niveles (solo una máquina virtual)][sap-templates-2-tier-os-disk]
    * Use esta plantilla si quiere crear un sistema de 2 niveles con solo una máquina virtual.

Una vez que abre la plantilla anterior, el Portal de Azure le dirige al panel Editar parámetros. Escriba la siguiente información:

* **sapSystemId**: el id. del sistema SAP.
* **osType**: el sistema operativo que quiere implementar, ya sea Windows o Linux.
* **sapSystemSize**: el tamaño del sistema SAP.
    * La cantidad de SAPS que proporcionará el sistema nuevo. Si no está seguro de cuántos SAPS necesitará el sistema, consulte con el integrador de sistemas o el socio tecnológico de SAP.
* **storageType**: el tipo de almacenamiento que se debe usar, solo para las plantillas de 2 niveles.
    * El uso de Almacenamiento premium se recomienda para los sistemas de mayor tamaño. Para más información sobre los distintos tipos de almacenamiento, lea
        * [Almacenamiento de Microsoft Azure][dbms-guide-2.3] de la [Guía de DBMS][dbms-guide].
        * [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure][storage-premium-storage-preview-portal]
        * [Introducción a Almacenamiento de Microsoft Azure][storage-introduction]
* **osDiskVhdUri**: el URI del disco de SO privado; por ejemplo, https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
* **newOrExistingSubnet**: determina si es necesario crear una red virtual y subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione la existente.
* **subnetId**: el id. de la subred a la que deben conectarse las máquinas virtuales. Seleccione la subred de la VPN o la red virtual de Express Route para conectar la máquina virtual a la red local. Generalmente, el id. tiene un aspecto similar al siguiente: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

Una vez que escriba todos los parámetros, seleccione la suscripción y el grupo de recursos que quiere usar. Puede seleccionar un grupo de recursos existente o crear uno nuevo; para ello, seleccione "+ Nuevo" en el menú desplegable. Si crea un grupo de recursos nuevo, también puede seleccionar la región en la que se creará el grupo de recursos y la máquina virtual.

Revise y acepte las condiciones legales y, luego, haga clic en Crear.

#### Instalación del agente de máquina virtual
El agente de Linux ya debe estar instalado en el disco de OS si quiere usar las plantillas anteriores. De lo contrario, se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la máquina virtual, tal como se describe en el capítulo [Descarga, instalación y habilitación del agente de máquina virtual de Azure][deployment-guide-4.4] de este documento.

Si no usa las plantillas anteriores, también puede instalar más adelante el agente de máquina virtual.

#### Unión a un dominio (solo Windows)
En el caso de que la implementación en Azure esté conectada a AD/DNS local a través de sitio a sitio de Azure o Express Route (que también se denomina como una conexión entre sitios locales en la [Guía de planeamiento e implementación][planning-guide]), se espera que la VM se una a un dominio local. Consulte las consideraciones de este paso en el capítulo [Unión de una VM a un dominio local (solo Windows)][deployment-guide-4.3] de este documento.

#### Configuración de los valores de proxy
Según la configuración de red local, puede que sea necesario configurar el proxy en la máquina virtual si se conecta a la red local a través de VPN o Express Route. De lo contrario, es posible que la máquina virtual no pueda tener acceso a Internet y, por lo tanto, no se puedan descargar las extensiones necesarias ni recopilar datos de supervisión. Consulte el capítulo [Configuración de proxy][deployment-guide-configure-proxy] de este documento.

#### Configuración de la supervisión
Configure la extensión de supervisión mejorada de Azure para SAP tal como se describe en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5] de este documento.

Compruebe los requisitos previos de la supervisión de SAP para ver las versiones mínimas requeridas de SAP Kernel y SAP Host Agent en los recursos que aparecen en el capítulo [Recursos SAP][deployment-guide-2.2] de este documento.

#### Comprobación de la supervisión
Compruebe si la supervisión funciona según lo descrito en el capítulo [Comprobaciones y solución de problemas de configuración de supervisión de extremo a extremo para SAP en Azure][deployment-guide-troubleshooting-chapter].

### Escenario 4: Actualizar la configuración de supervisión para SAP
Hay casos en los que podría ser necesario actualizar la configuración de supervisión:

* El equipo conjunto de MS/SAP extendió las funcionalidades de supervisión y decidió agregar o eliminar contadores.
* Microsoft presenta una versión nueva de la infraestructura de Azure subyacente que brinda los datos de supervisión y la extensión de supervisión mejorada de Azure para SAP se adapta a esos cambios.
* Puede agregar VHD adicionales montados en la máquina virtual de Azure, o bien puede quitar un VHD. En este caso, debe actualizar la colección de datos relacionados con el almacenamiento. Si agrega o elimina puntos de conexión o asigna direcciones IP a una máquina virtual, con lo que la configuración, no afectará la configuración de la supervisión.
* Puede cambiar el tamaño de la máquina virtual de Azure, por ejemplo, de A5 a cualquier otro tamaño de máquina virtual.
* Puede agregar interfaces de red nuevas a la máquina virtual de Azure.

Siga estos pasos para actualizar la configuración de la supervisión:

* Siga los pasos que se explican en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5] de este documento para actualizar la infraestructura de supervisión. Una ejecución nueva del script que se describe en este capítulo detectará que hay implementada una configuración de supervisión y hará los cambios necesarios en la configuración de supervisión.

___

> ![Windows][Logo_Windows] Windows
>
> No se requiere intervención del usuario para actualizar el agente de máquina virtual de Azure. El agente de máquina virtual se actualiza automáticamente y no es necesario reiniciar la máquina virtual.
>
> ![Linux][Logo_Linux] Linux
>
> Siga los pasos de [este artículo][virtual-machines-linux-update-agent] para actualizar el agente Linux de Azure.

___

## Pasos de implementación detallados

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementación de cmdlets de Azure PowerShell
* Vaya a <https://azure.microsoft.com/downloads/>.
* En "Herramientas de línea de comandos", hay una sección llamada "Windows PowerShell". Siga el vínculo "Instalar".
* El Administrador de descargas de Microsoft aparecerá en una ventana emergente con un elemento de línea terminado en .exe. Seleccione la opción "Ejecutar".
* Aparecerá una ventana emergente que preguntará si ejecutar el Instalador de plataforma web de Microsoft. Presione SÍ.
* Aparecerá una pantalla como esta:
 
![Pantalla de instalación de cmdlets de Azure PowerShell][deployment-guide-figure-500] <a name="figure-5"></a>

* Presione Instalar y acepte el CLUF.

Revise periódicamente si se actualizaron los cmdlets de PowerShell. Normalmente, las actualizaciones aparecen de manera mensual. La forma más fácil de hacerlo es siguiendo los pasos de instalación descritos anteriormente hasta llegar a la pantalla de instalación que aparece en [esta][deployment-guide-figure-5] ilustración. En esta pantalla aparece la fecha de lanzamiento del cmdlet, además del número de versión real. Salvo que se indique otra cosa en las notas de SAP [1928533] o [2015553], le recomendamos que trabaje con la versión más reciente de los cmdlets de Azure PowerShell.

Puede revisar la versión instalada actualmente de los cmdlets de Azure en el equipo de escritorio o portátil con el comando PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

El resultado debe aparecer como en [esta][deployment-guide-figure-6] ilustración.

![Resultado de la comprobación de versión de cmdlets de Azure PS][deployment-guide-figure-600] <a name="figure-6"></a>

Si la versión de cmdlet de Azure instalada en el equipo de escritorio o portátil es la actual, la primera pantalla después de iniciar el Instalador de plataforma web de Microsoft se verá algo diferente a la pantalla que aparece en [esta][deployment-guide-figure-5] ilustración.

Observe el círculo rojo que aparece en la [ilustración][deployment-guide-figure-7] a continuación.
 
![Pantalla de instalación de cmdlets de Azure PowerShell que indica que está instalada la versión más reciente de los cmdlets de Azure PS][deployment-guide-figure-700] <a name="figure-7"></a>

Si la pantalla se ve como la ilustración [anterior][deployment-guide-figure-7] es porque ya está instalada la versión más reciente del cmdlet de Azure y no es necesario seguir con la instalación. En este caso, puede salir de la instalación en esta etapa.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementación de la CLI de Azure
* Vaya a <https://azure.microsoft.com/downloads/>.
* En "Herramientas de línea de comandos", hay una sección llamada "Interfaz de la línea de comandos de Azure". Siga el vínculo de instalación correspondiente al sistema operativo.

Revise periódicamente si se actualizó la CLI de Azure. Normalmente, las actualizaciones aparecen de manera mensual. La forma más fácil de hacerlo es siguiendo los pasos descritos anteriormente.

Puede revisar la versión instalada de la CLI de Azure en el equipo de escritorio o portátil con este comando:

```
azure --version
```

El resultado debe aparecer como en [esta][deployment-guide-figure-azure-cli-version] ilustración.

![Resultado de la comprobación de versión de CLI de Azure][deployment-guide-figure-760] <a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Unión de una VM a un dominio local (solo Windows)
En los casos en que se implementan máquinas virtuales de SAP en un escenario entre sitios locales en los que AD y DNS local se extienden a Azure, se espera que las VM estén unidas a un dominio local. Los pasos detallados para unir una máquina virtual a un dominio local y el software adicional que se requiere para ser miembro de un dominio local dependen del cliente. Normalmente, unir una máquina virtual a un dominio local significa instalar software adicional, como software de protección contra malware o diversos agentes de software de copia de seguridad o supervisión.

Además, en casos en los que la configuración del proxy de Internet se realice de manera forzada cuando se una a un dominio, debe asegurarse de que la cuenta de sistema local de Windows (S-1-5-18) en la máquina virtual invitada. La manera más simple es forzar el proxy con la directiva de grupo de dominio que se aplica a los sistemas dentro del dominio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Descarga, instalación y habilitación del agente de máquina virtual de Azure
Los pasos siguientes son necesarios cuando una máquina virtual para SAP se implementa desde una imagen de SO que no está generalizada, es decir, que no está preparada con sysprep para Windows. No es necesario instalar el agente para las máquinas virtuales implementadas desde Azure Marketplace. Estas imágenes ya incluyen el agente de Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Descargue el agente de máquina virtual de Azure:
	* Descargue el paquete del instalador del agente de máquina virtual de Azure desde: <https://go.microsoft.com/fwlink/?LinkId=394789>
	* Almacene el paquete MSI del agente de máquina virtual localmente en el portátil o en un servidor.
* Instale el agente de máquina virtual de Azure:
	* Conecte la máquina virtual de Azure implementada con Terminal Services (RDP).
	* Abra una ventana del Explorador de Windows en la máquina virtual y, luego, un directorio de destino para el archivo MSI del agente de máquina virtual.
	* Arrastre el archivo MSI del instalador del agente de máquina virtual de Azure del portátil o servidor local y suéltelo en el directorio de destino en la máquina virtual.
	* Haga doble clic en el archivo MSI en la máquina virtual.
	* En el caso de una máquina virtual unida a dominios locales, asegúrese de que una eventual configuración de proxy de Internet se aplique para la cuenta de sistema local de Windows (S-1-5-18) en la máquina virtual tal como se describe en el capítulo [Configuración de proxy][deployment-guide-configure-proxy]. El agente de máquina virtual se ejecutará en este contexto y deberá poder conectarse a Azure.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Instale el agente de máquina virtual de Linux con el comando siguiente.

- **SLES**

```
sudo zypper install WALinuxAgent
```
- **RHEL**

```
sudo yum install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configuración de proxy
Los pasos de configuración de proxy son distintos entre Windows y Linux.

#### Windows
Esta configuración también debe ser válida para que la cuenta LocalSystem tenga acceso a Internet. Si una directiva de grupo no establece la configuración del proxy, puede seguir estos pasos para configurarla para la cuenta LocalSystem.

1.	Abra gpedit.msc.
1.	Vaya a Configuración del equipo –> Plantillas administrativas -> Componentes de Windows -> Internet Explorer y habilite "Configuración de proxy por equipo y no por usuario".
1.	Abra el Panel de control y vaya a Redes e Internet -> Opciones de Internet.
1.	Abra la pestaña Conexiones y haga clic en Configuración de LAN.
1.	Deshabilite "Detectar la configuración automáticamente".
1.	Habilite "Usar un servidor proxy para la LAN" y escriba el puerto y el host del proxy.

#### Linux
Configure el proxy correcto en el archivo de configuración del agente invitado de Microsoft Azure que está en /etc/waagent.conf. Se deben establecer los parámetros siguientes:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Reinicie el agente una vez que cambie la configuración con

```
sudo service waagent restart
```

La configuración de proxy en /etc/waagent.conf también se aplica a las extensiones requeridas de máquina virtual. Si desea usar los repositorios de Azure, asegúrese de que el tráfico a estos repositorios no pase por la intranet local. Si creó rutas definidas por el usuario para habilitar la tunelización forzada, asegúrese de agregar una ruta que enrute el tráfico a los repositorios directamente a Internet y no a través de su conexión de sitio a sitio.

- **SLES** También es necesario que agregue rutas para las direcciones IP que aparecen en /etc/regionserverclnt.cfg. Se muestra un ejemplo en la captura de pantalla siguiente.

- **RHEL** También es necesario que agregue rutas para las direcciones IP de los hosts que aparecen en /etc/yum.repos.d/rhui-load-balancers. Se muestra un ejemplo en la captura de pantalla siguiente.

Consulte [este artículo][virtual-networks-udr-overview] para más detalles sobre las rutas definidas por el usuario.

![Tunelización forzada][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar la extensión de supervisión mejorada de Azure para SAP
Una vez que la máquina virtual esté preparada como se describe en el capítulo [Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure][deployment-guide-3], el agente de máquina virtual de Azure se instala en la máquina. El siguiente paso importante es implementar la extensión de supervisión mejorada de Azure para SAP, que está disponible en el repositorio de extensiones de Azure de los centros de datos globales de Microsoft Azure. Consulte la [Guía de planeamiento e implementación][planning-guide-9.1] para más detalles.

Puede usar Azure PowerShell o la CLI de Azure para instalar y configurar la extensión de supervisión mejorada de Azure para SAP. Lea el capítulo [Azure PowerShell][deployment-guide-4.5.1] si desea instalar la extensión en una máquina virtual Linux o con Windows con un equipo Windows. Lea el capítulo [CLI de Azure][deployment-guide-4.5.2] para información sobre cómo instalar la extensión en una máquina virtual Linux con un equipo de escritorio de Linux.

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para máquinas virtuales Linux y con Windows
Siga estos pasos para realizar las tareas de instalación de la extensión de supervisión mejorada de Azure para SAP:

* Asegúrese de tener instalada la versión más reciente del cmdlet de Microsoft Azure PowerShell. Consulte el capítulo [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1] de este documento.
* Ejecute el siguiente cmdlet de PowerShell. Para ver una lista de los entornos disponibles, ejecute el commandlet Get-AzureRmEnvironment. Si quiere usar una instancia pública de Azure, el entorno es AzureCloud. Si quiere usar Azure en China, seleccione AzureChinaCloud.

```powershell
	$env = Get-AzureRmEnvironment -Name <name of the environment>
	Login-AzureRmAccount -Environment $env
	Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Después de que proporcione los datos de cuenta y la Máquina virtual de Azure, el script implementará las extensiones requeridas y habilitará las funciones necesarias. Esto puede tardar varios minutos. Lea [este artículo de MSDN][msdn-set-azurermvmaemextension] para más información sobre Set-AzureRmVMAEMExtension.
  
![Pantalla de resultados de la ejecución correcta del cmdlet Set-AzureRmVMAEMExtension de Azure específico para SAP][deployment-guide-figure-900]

Si Set-AzureRmVMAEMExtension se ejecuta correctamente, se realizarán todos los pasos necesarios para configurar la funcionalidad de supervisión de host para SAP.

La salida del script debe verse de la siguiente manera:

* La confirmación de que se estableció la configuración de supervisión del VHD base (donde está el SO), además de todos los VHD adicionales montados en la máquina virtual.
* Los próximos dos mensajes confirman la configuración de métricas de almacenamiento de una cuenta de almacenamiento específica.
* Una línea de salida mostrará un estado de la actualización real de la configuración de supervisión.
* Otra mostrará la confirmación de que se implementó o actualizó la configuración.
* La última línea de la salida muestra información sobre la posibilidad de probar la configuración de supervisión.
* Para comprobar que todos los pasos de la supervisión mejorada de Azure se ejecutaron correctamente y que la infraestructura de Azure proporciona los datos necesarios, siga con la comprobación de preparación de la extensión de supervisión mejorada de Azure para SAP, tal como se describe en el capítulo [Comprobación de preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1] en este documento.
* Para seguir con esto, espere entre 15 y 30 minutos hasta que Diagnósticos de Azure haya recopilado los datos pertinentes.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI de Azure para máquinas virtuales de Linux

Siga estos pasos para realizar las tareas de instalación de la extensión de supervisión mejorada de Azure para SAP con la CLI de Azure:

1. Instale la CLI de Azure como se describe en [este][azure-cli] artículo.
1. Inicie sesión en su cuenta de Azure.

    ```
    azure login
    ```
1. Cambie al modo de Azure Resource Manager.

    ```
    azure config mode arm
    ```
1. Habilite la supervisión mejorada de Azure.

    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
1. Compruebe que la supervisión mejorada de Azure está activa en la máquina virtual Linux de Azure. Compruebe si existe el archivo /var/lib/AzureEnhancedMonitor/PerfCounters. Si existe, muestre la información recopilada por AEM con:

    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    A continuación, obtendrá resultados como los siguientes:
    
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Comprobaciones y solución de problemas de configuración de supervisión de extremo a extremo para SAP en Azure
Una vez que implemente la máquina virtual de Azure y configure la infraestructura de supervisión de Azure correspondiente, revise si todos los componentes de la supervisión mejorada de Azure funcionan correctamente.

Por tanto, ejecute la comprobación de preparación de la extensión de supervisión mejorada de Azure para SAP tal como se describe en el capítulo [Comprobación de preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1]. Si el resultado de esta comprobación es favorable y se reciben todos los contadores de rendimiento correspondientes, la supervisión de Azure se configuró correctamente. En este caso, siga con la instalación de SAP Host Agent tal como se describe en las notas de SAP que aparecen en el capítulo [Recursos SAP][deployment-guide-2.2] de este documento. Si el resultado de la comprobación de preparación indica que faltan contadores, siga ejecutando la comprobación de estado de la infraestructura de supervisión de Azure, tal como se describe en el capítulo [Comprobación de estado de la configuración de la infraestructura de supervisión de Azure][deployment-guide-5.2]. En caso de que surja algún problema con la configuración de la supervisión de Azure, revise el capítulo [Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3] para más ayuda en la solución de problemas.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Comprobación de preparación de la supervisión mejorada de Azure para SAP
Con esta comprobación, puede asegurarse de que la infraestructura de supervisión de Azure subyacente proporcione completamente las métricas que aparecerán dentro de la aplicación SAP.

#### Ejecución de la comprobación de preparación en una máquina virtual de Windows
Inicie sesión en la Máquina virtual de Azure (no se necesita cuenta de administrador) y siga estos pasos para ejecutar la comprobación de preparación:

* Abra un símbolo del sistema de Windows y vaya a la carpeta de instalación de la extensión de supervisión de Azure para SAP C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop

La parte de la versión en la anterior ruta de acceso a la extensión de supervisión puede variar. Si ve varias carpetas de la versión de la extensión de supervisión en la carpeta de instalación, revise la configuración del servicio de Windows "AzureEnhancedMonitoring" y cambie a la carpeta indicada como "Ruta de acceso al ejecutable".
 
![Propiedades del servicio que ejecuta la extensión de supervisión mejorada de Azure para SAP][deployment-guide-figure-1000]

* Ejecute azperflib.exe sin ningún parámetro en la ventana del comando.

> [AZURE.NOTE] El archivo azperflib.exe se ejecuta en un bucle y actualiza los contadores recopilados cada 60 segundos. Cierre la ventana del comando para finalizar el bucle.

Si la extensión de supervisión mejorada de Azure no está instalada, o bien el servicio "AzureEnhancedMonitoring" no está en ejecución, la extensión no se configuró correctamente. En este caso, lea el capítulo [Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3] para instrucciones detalladas sobre cómo implementar de nuevo la extensión.

##### Revise la salida del archivo azperflib.exe.
La salida del archivo azperflib.exe muestra todos los contadores de rendimiento de Azure para SAP rellenados. En la parte inferior de la lista de los contadores recopilados, encontrará un resumen y un indicador de estado, el que indica el estado de la supervisión de Azure.
 
![Salida de la comprobación de estado mediante la ejecución de azperflib.exe que indica que no existen problemas][deployment-guide-figure-1100] <a name="figure-11"></a>

Compruebe el resultado del "Total de contadores" que genera la salida, que se informan como vacíos, además de la "Comprobación de estado", tal como se muestra en la ilustración [anterior][deployment-guide-figure-11].

Puede interpretar los valores del resultado de la siguiente manera:

| Valores del resultado de azperflib.exe | Estado de preparación de supervisión de Azure |
| ------------------------------|----------------------------------- |
| **Total de contadores: vacíos** | Los siguientes 2 contadores de Almacenamiento de Azure pueden estar vacíos: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Todos los otros contadores deben presentar valores. |
| **Comprobación de estado** | Solo es correcta si el estado del resultado muestra Correcto. |

Si ambos valores devueltos de azperflib.exe no muestran que los contadores rellenados se devuelven correctamente, siga las instrucciones de la comprobación de estado de la configuración de la infraestructura de supervisión de Azure, tal como se describe en el capítulo [Comprobación de estado de la configuración de la infraestructura de supervisión de Azure][deployment-guide-5.2] a continuación.

#### Ejecución de la comprobación de preparación en una máquina virtual de Linux
Conéctese con SSH a la Máquina virtual de Azure y siga estos pasos para ejecutar la comprobación de preparación:

* Compruebe la salida de la extensión de supervisión mejorada de Azure.
    * more /var/lib/AzureEnhancedMonitor/PerfCounters
        * Debe proporcionarle una lista de los contadores de rendimiento. El archivo no debe estar vacío.
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
        * Debe devolver una línea donde el error tenga el valor "none"; por ejemplo, 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;
    * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * Debe estar vacío o no debería existir.
* Si la primera comprobación no se realizó correctamente, ejecute estas pruebas adicionales:
    * Asegúrese de que waagent esté instalado y se haya iniciado.
        * sudo ls -al /var/lib/waagent/
            * Debe mostrar el contenido del directorio waagent.
        * ps -ax | grep waagent
            * Debe mostrar una entrada similar a "python /usr/sbin/waagent -daemon".
    * Asegúrese de que la extensión de diagnóstico de Linux esté instalada y se haya iniciado.
        * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
            * Debe mostrar el contenido del directorio de la extensión de diagnóstico de Linux.
        * ps -ax | grep diagnostic
            * Debe mostrar una entrada similar a "python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon".
    * Asegúrese de que la extensión de supervisión mejorada de Azure esté instalada y se haya iniciado.
        * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
            * Debe mostrar el contenido del directorio de la extensión de supervisión mejorada de Azure.
        * ps -ax | grep AzureEnhanced
            * Debe mostrar una entrada similar a "python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon".
* Instale SAP Host Agent tal como se describe en la nota de SAP [1031096] y compruebe la salida de saposcol.
    * Ejecute /usr/sap/hostctrl/exe/saposcol -d.
    * Ejecute dump ccm.
    * Revise si la métrica "Virtualization\_Configuration\\Enhanced Monitoring Access" tiene un valor True.
* Si ya tiene instalado un servidor de aplicación de SAP NetWeaver ABAP, abra las transacciones ST06 y compruebe si está habilitada la supervisión mejorada.

Si una de las comprobaciones anteriores presenta algún error, lea el capítulo [Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3] para instrucciones detalladas sobre cómo implementar de nuevo la extensión.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Comprobación de estado de la configuración de la infraestructura de supervisión de Azure
Si parte de los datos de supervisión no se entregan correctamente según lo indicado en la prueba que se describe en el capítulo [Comprobación de preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1] anterior, ejecute el cmdlet Test-AzureRmVMAEMExtension para probar si la configuración actual de la infraestructura de supervisión de Azure y la extensión de supervisión para SAP es correcta.

Ejecute la secuencia siguiente para probar la configuración de supervisión:

* Asegúrese de tener instalada la versión más reciente del cmdlet de Microsoft Azure PowerShell tal como se describe en el capítulo [Implementación de cmdlets de Azure PowerShell][deployment-guide-4.1] de este documento.
* Ejecute el siguiente cmdlet de PowerShell. Para ver una lista de los entornos disponibles, ejecute el commandlet Get-AzureRmEnvironment. Si quiere usar una instancia pública de Azure, el entorno es AzureCloud. Si quiere usar Azure en China, seleccione AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Después de que proporcione los datos de cuenta y la Máquina virtual de Azure, el script probará la configuración de la máquina virtual que elija.

 
![Pantalla de entrada del cmdlet Test-VMConfigForSAP\_GUI de Azure específico para SAP][deployment-guide-figure-1200]

Una vez que escriba la información sobre la cuenta y la Máquina virtual de Azure, el script probará la configuración de la máquina virtual que elija.
 
![Salida de una prueba correctamente ejecutada de la infraestructura de supervisión de Azure para SAP][deployment-guide-figure-1300]

Asegúrese de que cada comprobación esté marcada como ejecutada correctamente. Si algunas de las comprobaciones no se realizaron correctamente, ejecute el cmdlet de actualización tal como se describe en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5] de este documento. Espere otros 15 minutos y vuelva a ejecutar las comprobaciones que se describen en [Comprobación de preparación de la supervisión mejorada de Azure para SAP][deployment-guide-5.1] y [Comprobación de estado de la configuración de la infraestructura de supervisión de Azure][deployment-guide-5.2]. Si las comprobaciones siguen con problemas en algunos contadores o en la totalidad de ellos, pase al capítulo [Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP][deployment-guide-5.3].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Más información sobre solución de problemas de la infraestructura de supervisión de Azure para SAP

#### ![Windows][Logo_Windows] No aparecen contadores de rendimiento de Azure
El servicio de Windows "AzureEnhancedMonitoring" realiza la colección de métricas de rendimiento. Si el servicio no se instaló correctamente o si no se ejecuta en la máquina virtual, no se recopilará ninguna métrica de rendimiento.

##### El directorio de instalación de la extensión de supervisión mejorada de Azure está vacío 

###### Problema
El directorio de instalación C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\`<version`>\\drop está vacío.

###### Solución
La extensión no está instalada. Revise si hay un problema con el proxy (según lo descrito anteriormente). Puede que tenga que reiniciar la máquina o que deba ejecutar de nuevo el script de configuración Set-AzureRmVMAEMExtension.

##### El servicio de supervisión mejorada de Azure no existe 

###### Problema
El servicio "AzureEnhancedMonitoring" no existe. Azperflib.exe: la salida del archivo azperlib.exe genera un error, tal como se muestra en la [siguiente ilustración][deployment-guide-figure-14].
 
![La ejecución de azperflib.exe indica que el servicio de la extensión de supervisión mejorada de Azure para SAP no está ejecutada][deployment-guide-figure-1400] <a name="figure-14"></a>

###### Solución
Si el servicio no existe, tal como se muestra en la [ilustración anterior][deployment-guide-figure-14], la extensión de supervisión de Azure para SAP no se instaló correctamente. Implemente de nuevo la extensión según los pasos correspondientes al escenario de implementación que se describen en el capítulo [Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure][deployment-guide-3].

Una vez que se implemente la extensión, vuelva a comprobar en 1 hora si los contadores de rendimiento de Azure aparecen dentro de la máquina virtual de Azure.

##### El servicio de supervisión mejorada de Azure existe, pero no se inicia 

###### Problema
El servicio de Windows "AzureEnhancedMonitoring" existe y está habilitado, pero no se inicia. Revise el registro de eventos de la aplicación para más información.

###### Solución
Configuración incorrecta. Vuelva a habilitar la extensión de supervisión en la máquina virtual, tal como se describe en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5].

#### ![Windows][Logo_Windows] Faltan algunos contadores de rendimiento de Azure
El servicio de Windows "AzureEnhancedMonitoring" realiza la colección de métricas de rendimiento y obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, las métricas de rendimiento se leen desde Diagnósticos de Azure y los contadores de rendimiento se usan desde el registro en el nivel de suscripción de almacenamiento.

Si la solución de problemas que se describe en la nota de SAP [1999351] no funcionó, vuelva a ejecutar el script de configuración Set-AzureRmVMAEMExtension. Puede que tenga que esperar una hora porque los contadores de diagnóstico o el análisis de almacenamiento probablemente no se creen de inmediato una vez habilitados. Si el problema continúa, abra un mensaje de servicio al cliente de SAP en el componente BC-OP-NT-AZR.

#### ![Linux][Logo_Linux] No aparecen contadores de rendimiento de Azure

Un demonio realiza la colección de las métricas de rendimiento en Azure. Si el demonio no está en ejecución, no se puede recopilar ninguna métrica de rendimiento.

##### El directorio de instalación de la extensión de supervisión mejorada de Azure está vacío 

###### Problema
El directorio /var/lib/waagent/ no contiene un subdirectorio para la extensión de supervisión mejorada de Azure.

###### Solución
La extensión no está instalada. Revise si hay un problema con el proxy (según lo descrito anteriormente). Puede que tenga que reiniciar la máquina o que deba ejecutar de nuevo el script de configuración Set-AzureRmVMAEMExtension.

#### ![Linux][Logo_Linux] Faltan algunos contadores de rendimiento de Azure

Un demonio realiza la colección de métricas de rendimiento en Azure y obtiene datos de varios orígenes. Algunos datos de configuración se recopilan localmente, las métricas de rendimiento se leen desde Diagnósticos de Azure y los contadores de rendimiento se usan desde el registro en el nivel de suscripción de almacenamiento.

Consulte la nota de SAP [1999351] con información adicional sobre la solución de problemas de la supervisión mejorada de Azure para SAP para una lista completa y actualizada de los problemas conocidos.

Si la solución de problemas que se describe en la nota de SAP [1999351] no funcionó, vuelva a ejecutar el script de configuración Set-AzureRmVMAEMExtension según lo descrito en el capítulo [Configurar la extensión de supervisión mejorada de Azure para SAP][deployment-guide-4.5]. Puede que tenga que esperar una hora porque los contadores de diagnóstico o el análisis de almacenamiento probablemente no se creen de inmediato una vez habilitados. Si el problema continúa, abra un mensaje de servicio al cliente de SAP en el componente BC-OP-NT-AZR para una máquina virtual de Windows o BC-OP-LNX-AZR para una máquina virtual de Linux.

<!---HONumber=AcomDC_0928_2016-->