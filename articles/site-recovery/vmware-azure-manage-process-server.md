---
title: Administración de un servidor de procesos en Azure Site Recovery | Microsoft Docs
description: En este artículo se describe cómo administrar un servidor de procesos configurado para la replicación de servidores físicos y máquinas virtuales de VMware en Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="manage-process-servers"></a>Administración de servidores de proceso

De forma predeterminada, el servidor de procesos utilizado al replicar máquinas virtuales de VMware o servidores físicos en Azure se instala en el equipo del servidor de configuración local. Existen un par de casos en los que es necesario configurar un servidor de procesos independiente:

- Para implementaciones de gran tamaño, es posible que necesite servidores de procesos locales adicionales para escalar la capacidad.
- Para conmutación por recuperación, necesitará un servidor de procesos temporal configurado en Azure. Esta máquina virtual se puede eliminar cuando finalice la conmutación por recuperación. 

En este artículo se resumen las tareas de administración habituales para estos servidores de procesos adicionales.

## <a name="upgrade-a-process-server"></a>Actualizar un servidor de procesos

Un servidor de procesos que se ejecuta de forma local o en Azure (para fines de conmutación por recuperación) se actualiza como se indica a continuación:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Normalmente, al usar la imagen de la galería de Azure para crear un servidor de procesos en Azure para fines de conmutación por recuperación, se estará utilizando la versión más reciente disponible. Los equipos de Site Recovery publican periódicamente correcciones y mejoras y se recomienda mantener actualizados los servidores de procesos.



## <a name="reregister-a-process-server"></a>Volver a registrar un servidor de procesos

Si necesita volver a registrar un servidor de procesos que se ejecute de forma local o en Azure con el servidor de configuración, haga lo siguiente:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Después de guardar la configuración, haga lo siguiente:

1. En el servidor de procesos, abra un símbolo del sistema de administrador.
2. Vaya a la carpeta **%PROGRAMDATA%\ASR\Agent** y ejecute el comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificar la configuración de proxy para un servidor de procesos local

Si el servidor de procesos usa un proxy para conectarse a Site Recovery en Azure, utilice este procedimiento si necesita modificar la configuración de proxy existente.

1. Inicie sesión en el equipo del servidor de procesos. 
2. Abra la ventana de comandos de administrador de PowerShell y ejecute el comando siguiente:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Vaya a la carpeta **%PROGRAMDATA%\ASR\Agent** y ejecute el siguiente comando:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Eliminar un servidor de procesos

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


