---
title: "Protección de un servidor de archivos mediante Azure Site Recovery"
description: "En este artículo se describe cómo replica proteger un servidor de archivos mediante Azure Site Recovery"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: ac734ffc6cb57188b7b0959cbe7655949b2853de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Protección de un servidor de archivos mediante Azure Site Recovery 

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a la estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Azure Site Recovery administra y coordina la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure, lo que incluye la replicación, la conmutación por error y la recuperación de varias cargas de trabajo.

En este artículo se describe cómo proteger un servidor de archivos mediante Azure Site Recovery y otras recomendaciones para adaptar distintos entornos.     

- [Replicación de las máquinas de servidor de archivos de IaaS de Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replicación de un servidor de archivos local con Azure Site Recovery](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>Arquitectura del servidor de archivos
El objetivo de un sistema de uso compartido de archivos distribuidos abiertos es proporcionar un entorno en el que un grupo de usuarios geográficamente distribuido pueda colaborar para trabajar de forma eficiente en los archivos y garantizar que se cumplen sus requisitos de integridad. Un ecosistema de servidor de archivos local típico que admite un alto número de usuarios simultáneos y un gran número de elementos de contenido usan la Replicación del sistema de archivos distribuido (DFSR) para la programación de la replicación y el límite de ancho de banda. DFSR utiliza un algoritmo de compresión que se conoce como compresión del diferencial remoto (RDC), que puede usarse para actualizar los archivos de forma eficaz a través de una red de ancho de banda limitado. Detecta las inserciones, eliminaciones y reorganizaciones de datos en archivos, lo que permite que DFSR replique solo los bloques de archivo modificados cuando se actualizan los archivos. También hay entornos de servidor de archivos, donde se realizan copias de seguridad diarias en intervalos de fuera de horas punta, lo que responde a las necesidades de desastre y no hay implementación de DFSR.

La topología siguiente muestra el entorno de servidor de archivos con DFSR implementado.
                
![dfsr-architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

En la referencia anterior, varios servidores de archivos a los que se hace referencia como miembros, participan activamente en la replicación de archivos a través de un grupo de replicación. El contenido de la carpeta replicada estará disponible para todos los clientes que envían solicitudes a otros miembros, incluso en el caso de que uno de los miembros esté desconectado.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Recomendación de la recuperación ante desastres para servidores de archivo:

1.  Replicación de un servidor de archivos mediante Azure Site Recovery: los servidores de archivos se pueden replicar en Azure con Azure Site Recovery. Cuando no es posible acceder a una instancia local de servidores de archivos, las máquinas virtuales de recuperación pueden llevarse a Azure, que pueden ofrecer solicitudes de clientes, en instancias locales, en donde se proporciona conectividad VPN de sitio a sitio y un directorio activo configurado en Azure. Esto puede hacerse en caso de un entorno configurado de DFSR o un entorno de servidor de archivos simple sin DFSR. 

2.  Amplíe DFSR a una máquina virtual de Iaas de Azure: en un entorno de servidor de archivos con almacenamiento en clúster con DFSR implementado, se utiliza un enfoque sugerido para ampliar DFSR local a Azure. A continuación, se habilita una máquina virtual de Azure para realizar el rol de servidor de archivos. 

    Una vez que se administran las dependencias de Active Directory y la conectividad VPN de sitio a sitio y DFSR está en su lugar, cuando no se puede acceder a las instancias locales de los servidores de archivos, los clientes pueden seguir conectándose a la máquina virtual de Azure, que proporcionará las solicitudes.

    Se recomienda este enfoque en caso de que las máquinas virtuales dispongan de configuraciones no compatibles con Azure Site Recovery, por ejemplo: disco de clúster compartido de uso frecuente en entornos de servidor de archivos.  DFSR también funciona correctamente en entornos de ancho de banda reducido con la tasa de renovación intermedia. El costo adicional de tener una máquina virtual de Azure ejecutándose todo el tiempo también necesita acomodarse a esto.  

3.  Utilice el servicio Azure File Sync para replicar los archivos: si prepara un traslado a la nube, o está usando ya una máquina virtual de Azure, sugerimos que use el servicio Azure File Sync, que ofrece la sincronización de recursos compartidos de archivos totalmente administrados en la nube a los que se puede acceder a través del protocolo [Bloque de mensajes del servidor](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB) estándar del sector. Los recursos compartidos de Azure Files se pueden montar simultáneamente en implementaciones de Windows, Linux y macOS en la nube o locales. 

El diagrama siguiente proporciona una representación gráfica destinada a facilitar las decisiones de la estrategia que utilizar para su entorno de servidor de archivos.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>Factores que hay que tener en cuenta al tomar la decisión de recuperación ante desastres en Azure

|Environment  |Recomendación  |Puntos que se deben tener en cuenta |
|---------|---------|---------|
|Entorno de servidor de archivos con/sin DFSR|   [Uso de Azure Site Recovery para la replicación](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Site Recovery no es compatible con el clúster de disco compartido, NAS. Si su entorno usa alguna de estas configuraciones, utilice cualquiera de los demás enfoques según corresponda. <br> Azure Site Recovery no es compatible con SMB 3.0, lo que significa que solo cuando los cambios realizados en los archivos se actualicen en la ubicación original del archivo, la máquina virtual replicada incorporará los cambios.
|Entorno de servidor de archivos con DFSR     |  [Extensión de DFRS a una máquina virtual de IaaS de Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR funciona correctamente en entornos extremadamente estudiados de ancho de banda. Sin embargo, este enfoque requiere disponer de una máquina virtual de Azure en funcionamiento todo el tiempo. El costo de la máquina virtual debe tenerse en cuenta en la planificación.         |
|Máquina virtual de IaaS de Azure     |     [Azure File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     En un escenario de recuperación ante desastres, si usa Azure File Sync, deben realizarse acciones manuales durante la conmutación por error para garantizar que se puede acceder a los recursos compartidos de archivos de forma transparente en el equipo cliente. AFS requiere que el puerto 445 esté abierto desde el equipo cliente.     |


### <a name="site-recovery-support"></a>Compatibilidad de Site Recovery
Como la replicación de Site Recovery no depende de la aplicación, se espera que las recomendaciones proporcionadas aquí sirvan también para los escenarios siguientes:
| Origen    |En un sitio secundario    |En Azure
|---------|---------|---------|
|Azure| -|Sí|
|Hyper-V|   Sí |Sí
|VMware |Sí|   Sí
|Servidor físico|   Sí |Sí
 

> [!IMPORTANT]
> Antes de continuar con cualquiera de los siguiente tres enfoques, asegúrese de que tiene en cuenta las siguientes dependencias:

**Conectividad de sitio a sitio**: la conexión directa entre el sitio local y la red de Azure debe establecerse para permitir la comunicación entre servidores.  Esto puede garantizarse mediante una conexión VPN de sitio a sitio segura en una Microsoft Azure Virtual Network que se usará como sitio de recuperación ante desastres.  
Consulte: [Creación de una conexión de sitio a sitio mediante Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**:  DFSR depende de Active Directory.  Esto significa que el bosque de Active Directory con controladores de dominio locales se amplía al sitio de recuperación ante desastres en Azure. Incluso si no está usando DFSR, si se tiene que conceder o comprobar el acceso de los usuarios previstos como en la mayoría de las organizaciones, tienen que realizarse estos pasos.
Consulte: [Protección de Active Directory y DNS con Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Recomendación de recuperación ante desastres para máquinas virtuales de IaaS de Azure

Si va a configurar y administrar la recuperación ante desastres de servidores de archivo hospedados en máquinas virtuales de IaaS de Azure, puede elegir entre dos opciones, según si desea cambiar a [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Uso de Azure File Sync](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Uso de Azure Site Recovery](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Uso del servicio Azure File Sync para replicar archivos hospedados en una máquina virtual de IaaS

**Azure Files** puede utilizarse para reemplazar totalmente o complementar los servidores de archivos tradicionales locales o en dispositivos NAS. Los recursos compartidos de Azure Files se pueden replicar también con Azure File Sync en servidores de Windows Server, locales o en la nube, para obtener un almacenamiento en caché eficiente y distribuido de los datos allí donde se usan. A continuación se encuentran los pasos que detallan la recomendación de la recuperación ante desastres para las máquinas virtuales de Azure que realizan la misma funcionalidad como servidores de archivos tradicionales:
1.  Proteja las máquinas mediante Azure Site Recovery con los pasos mencionados [aquí](azure-to-azure-quickstart.md).
2.  Use Azure File Sync para replicar archivos desde la máquina virtual que funciona como servidor de archivo en la nube.
3.  Utilice la función del [plan de recuperación](site-recovery-create-recovery-plans.md) de Azure Site Recovery para agregar scripts para [montar el recurso compartido de archivos de Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) y acceda al recurso compartido en la máquina virtual.

Los pasos siguientes describen brevemente cómo utilizar el servicio Azure File Sync:

1. [Cree una cuenta de almacenamiento en Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Si eligió el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) en las cuentas de almacenamiento, tendrá acceso de lectura a los datos de la región secundaria en caso de un desastre. Consulte las [estrategias de recuperación ante desastres del recurso compartido de archivos de Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obtener más información.
2. [Cree un recurso compartido de archivos](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Implemente Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) en el servidor de archivos de Azure.
4. Cree un grupo de sincronización: los puntos de conexión dentro de un grupo de sincronización se mantienen sincronizados entre sí. Un grupo de sincronización debe contener al menos un punto de conexión de nube, que representa un recurso compartido de Azure Files, y un punto de conexión de servidor, que representa una ruta de acceso de un servidor de Windows Server.
5.  Los archivos ahora se mantienen sincronizados entre el recurso compartido de Azure Files y el servidor local.
6.  Si se produce un desastre en el entorno local, realice el procedimiento de una conmutación por error con un [plan de recuperación](site-recovery-create-recovery-plans.md) y agregue el script para montar el [recurso compartido de archivos de Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) y acceda al recurso en su máquina virtual.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Replicación de una máquina virtual de servidor de archivos IaaS mediante Azure Site Recovery

Si dispone de clientes locales que acceden a la máquina virtual del servidor de archivos de IaaS, realice los dos primeros pasos o vaya al paso 3.

1. Establezca una conexión VPN de sitio a sitio entre el sitio local y la red de Azure.
1. Amplíe Active Directory local.
1. [Configure la recuperación ante desastres](azure-to-azure-tutorial-enable-replication.md) para la máquina del servidor de archivos IaaS en una región secundaria.


Para más información sobre recuperación ante desastres en una región secundaria, vea [aquí](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Replicación de un servidor de archivos local con Azure Site Recovery

Los pasos siguientes detallan la replicación de una máquina virtual de VMware. Para ver los pasos para replicar una máquina virtual de Hyper-V, consulte [aquí](tutorial-hyper-v-to-azure.md).

1.  [Prepare recursos de Azure](tutorial-prepare-azure.md) para la replicación de máquinas locales.
2.  Establezca una conexión VPN de sitio a sitio entre el sitio local y la red de Azure.  
3. Amplíe Active Directory local.
4.  [Prepare servidores de VMware locales](tutorial-prepare-on-premises-vmware.md).
5.  [Configure la recuperación ante desastres](tutorial-vmware-to-azure.md) para máquinas virtuales de VMware locales en Azure.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Extensión de DFRS a una máquina virtual de IaaS de Azure

1.  Establezca una conexión VPN de sitio a sitio entre el sitio local y la red de Azure. 
2.  Amplíe Active Directory local.
3.  [Cree y aprovisione una máquina virtual del servidor de archivos](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) en Windows Azure Virtual Network.
Asegúrese de que la máquina virtual esté agregada a la misma Windows Azure Virtual Network, que dispone de la conectividad cruzada con el entorno local. 
4.  Instale y [configure Replicación DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) en Windows Server.
5.  [Implemente un espacio de nombres DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Con el espacio de nombres DFS implementado, la conmutación por error de carpetas compartidas de la producción a sitios de recuperación ante desastres puede realizarse actualizando los destinos de las carpetas de espacio de nombres DFS.  Una vez que estos cambios de espacios de nombres de DFS se replican a través de Active Directory, los usuarios se conectan a los destinos de la carpeta apropiados de forma transparente.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Uso del servicio Azure File Sync para replicar los archivos locales
Utilice el servicio Azure File Sync para replicar los archivos deseados en la nube de manera que en el caso de que se produzca un desastre o el servidor de archivos local no esté disponible, pueda montar las ubicaciones de archivos deseadas desde la nube y continuar con solicitudes de servicio desde equipos clientes.
El enfoque sugerido de integración de Azure File Sync con Azure Site Recovery es el siguiente:
1.  Proteja las máquinas de servidor de archivos mediante Azure Site Recovery con los pasos que se mencionan [aquí](tutorial-vmware-to-azure.md).
2.  Use Azure File Sync para replicar archivos desde la máquina virtual que funciona como servidor de archivo en la nube.
3.  Utilice la función del plan de recuperación de Azure Site Recovery para agregar scripts para montar el recurso compartido en la máquina virtual de FileServer que se ha conmutado por error en Azure.

Los pasos de abajo detallan el uso del servicio Azure File Sync:

1. [Cree una cuenta de almacenamiento en Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Si eligió el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) (recomendado) en las cuentas de almacenamiento, tendrá acceso de lectura a los datos de la región secundaria e caso de desastre. Consulte las [estrategias de recuperación ante desastres del recurso compartido de archivos de Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obtener más información.
2. [Cree un recurso compartido de archivos](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Implemente Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) en el servidor de archivos local.
4. Cree un grupo de sincronización: los puntos de conexión dentro de un grupo de sincronización se mantienen sincronizados entre sí. Un grupo de sincronización debe contener al menos un punto de conexión de nube, que representa un recurso compartido de Azure Files, y un punto de conexión de servidor, que representa una ruta de acceso de un servidor de Windows Server local.
1. Los archivos ahora se mantienen sincronizados entre el recurso compartido de Azure Files y el servidor local.
6.  Si se produce un desastre en el entorno local, realice el procedimiento de una conmutación por error con un [plan de recuperación](site-recovery-create-recovery-plans.md) y agregue el script para montar el recurso compartido de archivos de Azure y acceda al recurso en su máquina virtual.

> [!NOTE]
> Asegúrese de que el puerto 445 está abierto: Azure Files usa el protocolo SMB. SMB se comunica a través del puerto TCP 445: compruebe que el firewall no bloquea el puerto TCP 445 en el equipo cliente.


## <a name="doing-a-test-failover"></a>Realización de una conmutación por error de prueba

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
2.  Haga clic en el plan de recuperación creado para el entorno de FileServer.
3.  Haga clic en 'Probar conmutación por error'.
4.  Seleccione un punto de recuperación y una red virtual de Azure para iniciar el proceso de conmutación por error de prueba.
5.  Cuando el entorno secundario esté activo, podrá realizar las validaciones.
6.  Después de completar las validaciones, haga clic en Limpiar conmutación por error de prueba en el plan de recuperación para limpiar el entorno de la conmutación por error de prueba.

Para más información sobre la realización de la conmutación por error de prueba, vea [aquí](site-recovery-test-failover-to-azure.md).

Para obtener instrucciones sobre cómo realizar la conmutación por error para AD y DNS, consulte las [consideraciones sobre la conmutación por error de prueba para AD y DNS](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>Realización de una conmutación por error

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
2.  Haga clic en el plan de recuperación creado para el entorno de FileServer.
3.  Haga clic en 'Conmutación por error'.
4.  Seleccione el punto de recuperación en el que se va a iniciar el proceso de conmutación por error.

Para más información sobre la realización de la conmutación por error de prueba, consulte [aquí](site-recovery-failover.md).
