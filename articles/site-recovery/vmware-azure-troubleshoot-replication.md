---
title: Solución de problemas de replicación de máquinas virtuales de VMware y de servidores físicos en Azure con Azure Site Recovery | Microsoft Docs
description: En este artículo se proporcionan soluciones a los problemas comunes al replicar máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: asgang
ms.openlocfilehash: 9291840428c9a8d7ba5d65bc94ce5964728316f3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solución de problemas de replicación de máquinas virtuales de VMware y de servidores físicos

Puede recibir un mensaje de error específico al proteger sus máquinas virtuales de VMware o servidores físicos con Azure Site Recovery. En este artículo se describen algunos problemas comunes que pueden surgir al replicar máquinas virtuales de VMware locales y servidores físicos en Azure mediante [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problemas de replicación inicial

En muchos casos, los errores de replicación inicial con los que se encuentra el soporte técnico se deben a problemas de conectividad entre el servidor de origen y el de proceso o entre el servidor de proceso y Azure. En la mayoría de los casos, puede solucionar estos problemas siguiendo los pasos indicados a continuación.

### <a name="verify-the-source-machine"></a>Verificación de la máquina de origen
* Desde la línea de comandos de la máquina del servidor de origen, utilice Telnet para hacer ping al servidor de proceso con el puerto https (el valor predeterminado es 9443) tal y como se muestra a continuación para ver si hay problemas de conectividad de red o problemas de bloqueo de puertos en el firewall.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Utilice Telnet, no use PING para probar la conectividad.  Si no está instalado Telnet, siga los pasos indicados [aquí](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Si no se puede conectar, habilite el puerto de entrada 9443 en el servidor de proceso y compruebe si el problema todavía persiste. Ha habido algunos casos en los que el servidor de proceso está detrás de la DMZ y que fue la causa del problema.

* Compruebe el estado del servicio `InMage Scout VX Agent – Sentinel/OutpostStart` si no está en ejecución y compruebe si el problema persiste.   

## <a name="verify-the-process-server"></a>Verificación del servidor de procesos

* **Compruebe si el servidor de proceso inserta datos en Azure de un modo activo**

Desde el equipo del servidor de proceso, abra el Administrador de tareas (presione CTRL-MAYÚS-ESC). Vaya a la pestaña Rendimiento y haga clic en el vínculo 'Abrir el Monitor de recursos'. En el Administrador de recursos, vaya a la pestaña Red. Compruebe en 'Procesos con actividad de red' si cbengine.exe está enviando activamente gran volumen (en MB) de datos.

![Habilitar replicación](./media/vmware-azure-troubleshoot-replication/cbengine.png)

En caso negativo, siga los pasos que se indican a continuación:

* **Compruebe si el servidor de proceso es capaz de conectarse a Azure Blob**: seleccione y marque cbengine.exe para ver las conexiones TCP y ver si hay conectividad entre el servidor de proceso y la dirección URL del blob de Azure Storage.

![Habilitar replicación](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

En caso negativo vaya al Panel de Control > Servicios y compruebe si los siguientes servicios están activados y en ejecución:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re)inicie cualquier servicio que no se esté ejecutando y compruebe si el problema persiste.

* **Compruebe si el servidor de proceso es capaz de conectarse a través del puerto 443 a la dirección IP pública de Azure**

Abra el archivo CBEngineCurr.errlog más reciente desde `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` y busque :443 e intento fallido de conexión.

![Habilitar replicación](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Si hay problemas, a continuación, desde la línea de comandos del servidor de proceso, use telnet para hacer ping a la dirección IP pública de Azure (aparece enmascarada en la imagen superior) encontrada en CBEngineCurr.currLog mediante el puerto 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Si no puede conectarse, compruebe si el problema de acceso es debido al firewall o Proxy, como se describe en el paso siguiente.


* **Compruebe si el firewall basado en direcciones IP en el servidor de proceso no está bloqueando el acceso**: si está usando reglas de firewall basadas en direcciones IP en el servidor, descargue la lista completa de los intervalos IP de Microsoft Azure Datacenter desde [aquí](https://www.microsoft.com/download/details.aspx?id=41653) y agréguelos a la configuración del firewall para asegurarse de que permiten la comunicación a Azure [y el puerto HTTPS (443)].  Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).

* **Compruebe si el firewall basado en direcciones URL en el servidor de proceso no está bloqueando el acceso**: si está usando reglas de firewall basadas en direcciones URL en el servidor, asegúrese de que las direcciones URL siguientes se agregan a la configuración del firewall.

  `*.accesscontrol.windows.net:` Se usa para el control de acceso y la administración de identidades

  `*.backup.windowsazure.com:` Se usa para la transferencia y orquestación de los datos de replicación

  `*.blob.core.windows.net:` Se usa para tener acceso a la cuenta de almacenamiento que almacena los datos replicados

  `*.hypervrecoverymanager.windowsazure.com:` Se usa para las operaciones de administración de replicación y la orquestación

  `time.nist.gov` y `time.windows.com`: se usan para comprobar la sincronización de la hora entre el sistema y la hora global.

Direcciones URL para **Azure Government Cloud**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Compruebe si la configuración del Proxy del servidor de proceso no está bloqueando el acceso**.  Si está utilizando un servidor Proxy, asegúrese de que el servidor DNS está resolviendo el nombre del servidor proxy.
Para comprobar lo proporcionado en el momento de la instalación del servidor de configuración. Vaya a la clave del registro

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Ahora asegúrese de que el agente de Azure Site Recovery utiliza la misma configuración para el envío de datos.
Busque Microsoft Azure Backup

Ábralo y haga clic en Acción > Cambiar propiedades. En la pestaña Configuración de Proxy, debería ver la dirección del proxy, que debe ser la misma que la que se muestra en la configuración del registro. En caso negativo, cámbiela por la misma dirección.


* **Compruebe si la limitación de ancho de banda no está restringida en el servidor de proceso**: aumente el ancho de banda y compruebe si el problema persiste.

## <a name="next-steps"></a>Pasos siguientes
Si necesita más ayuda, puede enviar una consulta al [foro de Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Contamos con una comunidad activa y uno de nuestros ingenieros podrá ayudarle.
