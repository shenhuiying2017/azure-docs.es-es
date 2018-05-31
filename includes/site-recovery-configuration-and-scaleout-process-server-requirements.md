---
title: archivo de inclusión
description: archivo de inclusión
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 05/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2464fa41deaa1e9c43e5f53e1a900ca11b582040
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33901302"
---
**Requisitos del servidor de proceso/configuración**

**Componente** | **Requisito** 
--- | ---
**HARDWARE** | 
**Núcleos de CPU** | 8 
**RAM** | 16 GB
**Número de discos** | 3, incluidos el disco del sistema operativo, el disco de memoria caché del servidor de procesos, la unidad de retención para la conmutación por recuperación 
**Espacio libre en el disco (caché del servidor de procesos)** | 600 GB
**Espacio libre en el disco (disco de retención)** | 600 GB
**SOFTWARE** | 
**Sistema operativos** | Windows Server 2012 R2 <br> Windows Server 2016
**Configuración regional del sistema operativo** | Español (es-es)
**Roles de Windows Server** | No habilite estos roles: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
**Directivas de grupo** | No habilite estas directivas de grupo: <br> - Impedir el acceso al símbolo del sistema. <br> - Impedir el acceso a herramientas de edición del Registro. <br> - Confiar en la lógica de datos adjuntos de archivos. <br> - Activar la ejecución de scripts. <br> [Más información](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
**IIS** | - Ningún sitio web predeterminado debe existir previamente <br> - Ningún sitio web o aplicación que escuche en el puerto 443 deben existir previamente <br>- Habilitar la [autenticación anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar la configuración de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
**RED** | 
**Tipo de dirección IP** | estática 
**Acceso a Internet** | El servidor necesita acceder a estas direcciones URL (directamente o a través de proxy) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (si configura un servidor de configuración) <br> - time.nist.gov <br> - time.windows.com 
**Puertos** | 443 (orquestación del canal de control)<br>9443 (Transporte de datos) 
**VMWARE (cuando configura el servidor de proceso/configuración como máquina virtual VMware)**
**Tipo de NIC** | VMXNET3  
**VMware vSphere PowerCLI en ejecución en VM* | [PowerCLI versión 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")

**Requisitos de tamaño del servidor de proceso/configuración**

**CPU** | **Memoria** | **Disco de caché** | **Frecuencia de cambio de datos** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 sockets * 4 núcleos @ 2,5 GHz | 16 GB | < 300 GB | 500 GB o menos | < 100 máquinas
12 vCPU<br/><br/> 2 socks * 6 núcleos @ 2,5 GHz | 18 GB | 600 GB | 500 GB - 1 TB | De 100 a 150 máquinas
16 vCPU<br/><br/> 2 socks * 8 núcleos @ 2,5 GHz | 32 GB | 1 TB | 1 - 2 TB | De 150 a 200 máquinas

