---
title: "Migración a Azure Premium Storage con Azure Site Recovery | Microsoft Docs"
description: "Migre las máquinas virtuales existentes a Azure Premium Storage con Site Recovery. El Almacenamiento premium le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en máquinas virtuales de Azure."
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: <service per approved list>
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2016
ms.author: luywang
translationtype: Human Translation
ms.sourcegitcommit: ebd9a8cf3d3fd0d2eec3be0b74eb679ebcf2a8f6
ms.openlocfilehash: 775f57ea15369228a41814a8ef62c46be911af34


---
# <a name="migrating-to-premium-storage-using-azure-site-recovery"></a>Migración a Premium Storage con Azure Site Recovery
## <a name="overview"></a>Información general

[Azure Premium Storage](storage-premium-storage.md) le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales (VM) que ejecutan cargas de trabajo intensivas de E/S. El propósito de esta guía es ayudar a los usuarios a migrar sus discos de máquinas virtuales desde una cuenta de almacenamiento estándar a una cuenta de Premium Storage mediante [Azure Site Recovery](../site-recovery/site-recovery-overview.md).

Site Recovery es un servicio de Azure que contribuye a su estrategia de continuidad empresarial y recuperación ante desastres al organizar la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Site Recovery proporciona conmutaciones por error de prueba que admiten maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados. Puede ejecutar las conmutaciones por error no planeadas con la mínima pérdida de datos (según la frecuencia de replicación) para desastres inesperados. En el escenario de migración a Premium Storage, puede utilizar una [conmutación por error no planeada](../site-recovery/site-recovery-failover.md#types-of-failover) en Azure Site Recovery para migrar los discos de destino a una cuenta de Premium Storage.

Se recomienda migrar a Premium Storage con Site Recovery porque esta opción proporciona un tiempo de inactividad mínimo y evita la ejecución manual de copiar los discos y la creación de nuevas máquinas virtuales. Site Recovery de forma sistemática copiará los discos y creará nuevas máquinas virtuales durante la conmutación por error. Site Recovery admite varios tipos de conmutación por error con un tiempo de inactividad mínimo, o ninguno. Para planear el tiempo de inactividad y calcular la pérdida de datos, consulte la tabla de [tipos de conmutación por error](../site-recovery/site-recovery-failover.md#types-of-failover) en Site Recovery. Si se [prepara para conectarse a las máquinas virtuales de Azure después de la conmutación por error](../site-recovery/site-recovery-vmware-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), debe ser capaz de conectarse a la máquina virtual de Azure con RDP después de la conmutación por error.

![][1]

## <a name="migration-scenario-components"></a>Componentes del escenario de migración

**Componentes de Site Recovery pertinentes en este escenario de migración:**

* Un **servidor de configuración** es una máquina virtual de Azure que coordina la comunicación y administra los procesos de replicación y recuperación de datos. En esta máquina virtual se ejecutará un archivo de configuración única para instalar el servidor de configuración y un componente adicional, denominado servidor de procesos, como una puerta de enlace de replicación. Lea sobre los [requisitos previos del servidor de configuración](../site-recovery/site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). El servidor de configuración solo debe configurarse una vez y puede utilizarse para todas las migraciones a la misma región.

* Un **servidor de procesos** es una puerta de enlace de replicación que recibe datos de replicación de máquinas virtuales de origen y optimiza los datos con almacenamiento en caché, compresión y cifrado, y lo envía a una cuenta de almacenamiento. También controla la instalación de inserción del servicio de movilidad en máquinas virtuales de origen y realiza la detección automática de máquinas virtuales de origen. El servidor de procesos predeterminado está instalado en el servidor de configuración. Puede implementar servidores de procesos independientes adicionales para escalar la implementación. Obtenga información sobre [los procedimientos recomendados para la implementación de servidor de procesos](https://azure.microsoft.com/en-us/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) y [la implementación de los servidores de procesos adicionales](../site-recovery/site-recovery-vmware-to-azure.md#deploy-additional-process-servers). El servidor de procesos solo debe configurarse una vez y puede utilizarse para todas las migraciones a la misma región.

* Un **servicio de movilidad** es un componente que se implementa en cada máquina virtual estándar que desea replicar. Captura las escrituras de datos en la máquina virtual estándar y los reenvía al servidor de procesos. Lea sobre los [requisitos previos de máquinas replicadas](../site-recovery/site-recovery-vmware-to-azure.md#replicated-machine-prerequisites).

**Información esencial de Azure**: 

* Una suscripción de Azure
* Una cuenta de Azure Premium Storage para almacenar los datos replicados
* Una red virtual de Azure a la que se conectarán las máquinas virtuales cuando se creen en la conmutación por error. La red virtual de Azure debe estar en la misma región que en la que se ejecuta Site Recovery.
* Una cuenta de almacenamiento estándar de Azure en la que se van a almacenar los registros de replicación. Puede ser la misma cuenta de almacenamiento que los discos de máquina virtual que se están migrando.

En el gráfico se muestra cómo interactúan estos componentes.

![][15]

> [!NOTE]
> Site Recovery no admite la migración de los discos de espacios de Storage.

Para otros componentes de otros escenarios, consulte [Arquitectura del escenario](../site-recovery/site-recovery-vmware-to-azure.md#scenario-architecture).

## <a name="prerequisites"></a>Requisitos previos

* Comprender los componentes del escenario de migración pertinentes de la sección anterior
* Planear el tiempo de inactividad al obtener información sobre la conmutación por error no planeada en [Conmutación por error en Site Recovery](../site-recovery/site-recovery-failover.md#types-of-failover)

## <a name="setup-and-migration-steps"></a>Pasos de configuración y migración

Puede usar Site Recovery para migrar máquinas virtuales de IaaS de Azure entre regiones o dentro de la misma región. Las instrucciones siguientes se han adaptado a este escenario de migración a partir del artículo [Replicar máquinas virtuales de VMware o servidores físicos en Azure](../site-recovery/site-recovery-vmware-to-azure.md). Siga los vínculos para conocer otros pasos además de las instrucciones de este artículo.

1. **Creación de un almacén de Recovery Services** Cree y administre el almacén de Site Recovery a través de [Azure Portal](https://portal.azure.com). Haga clic en **Nuevo** > **Administración** > **Backup** y **Site Recovery (OMS)**. También puede hacer clic en **Examinar** > **Almacén de Recovery Services** > **Agregar**. Las máquinas virtuales se replicarán en la región que haya especificado en este paso. A efectos de la migración en la misma región, seleccione la región donde están máquinas virtuales de origen y las cuentas de almacenamiento de origen. Tenga en cuenta que solo se admite la migración a las cuentas de Premium Storage en [Azure Portal](https://portal.azure.com), no en el [portal clásico](https://manage.windowsazure.com).

2. **Elija los objetivos de protección**. En la máquina virtual en la que desea instalar el servidor de configuración, abra [Azure Portal](https://portal.azure.com). Vaya a **Almacenes de Recovery Services** > **Configuración**. En **Configuración**, seleccione **Site Recovery**. En **Site Recovery**, seleccione **Paso 1: Preparar la infraestructura**. En **Preparar la infraestructura**, seleccione **Objetivo de protección**.
  
  ![][2]
  
  En **Objetivo de protección**, en la primera lista desplegable, seleccione **En Azure**. En la segunda lista desplegable, seleccione **No virtualizado/Otro** y, después, haga clic en **Aceptar**.
  
  ![][3]
  
3. **Configure el entorno de origen (servidor de configuración)**. Descargue la **instalación unificada de Azure Site Recovery** y la **clave de registro del almacén** en la hoja **Preparar la infraestructura** > **Preparar origen** > **Agregar servidor**. Necesitará la clave de registro del almacén para ejecutar la instalación unificada. La clave será válida durante 5 días a partir del momento en que se genera.
  
  ![][4]
  
  ![][5]
  
  En la máquina virtual que usa como servidor de configuración, ejecute la instalación unificada para instalar el servidor de configuración y el servidor de procesos. Puede comenzar [aquí](../site-recovery/site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment) con las capturas de pantalla para completar la instalación. Puede consultar las capturas de pantalla siguientes para conocer los pasos especificados para este escenario de migración.

  En **Antes de comenzar**, seleccione **Install the configuration server and process server** (Instalar el servidor de configuración y el servidor de procesos).
  
  ![][6]

  En **Registro**, busque y seleccione la clave de registro que descargó del almacén.
  
  ![][7]

  En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. En este escenario de migración, elija **No**.
  
  ![][8]
  
  Una vez completada la instalación, verá la ventana del **servidor de configuración de Microsoft Azure Site Recovery**. Utilice la pestaña **Administrar cuentas** para crear la cuenta que puede usar Site Recovery para la detección automática. (En el escenario sobre cómo proteger las máquinas físicas, no es pertinente configurar la cuenta, pero necesita al menos una cuenta para habilitar uno de los pasos siguientes. En este caso, puede denominar a la cuenta y contraseña como cualquier otra). Use la pestaña **Registro del almacén** para cargar el archivo de credenciales del almacén.
  
  ![][9]

4. **Configure el entorno de destino**. Haga clic en **Preparar la infraestructura** > **Destino** y especifique el modelo de implementación que desea usar para las máquinas virtuales después de la conmutación por error. Puede elegir **Clásico** o **Resource Manager**, en función de su escenario.
  
  ![][10]

  Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles. Tenga en cuenta que si utiliza una cuenta de Premium Storage para los datos replicados, deberá configurar una cuenta de almacenamiento estándar adicional para almacenar los registros de replicación.

5. **Configure las opciones de replicación**. Siga los pasos que se indican [aquí](../site-recovery/site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings) para comprobar que el servidor de configuración se asoció correctamente a la directiva de replicación que ha creado.

6. **Planeamiento de capacidad** Utilice el [planeamiento de capacidad](../site-recovery/site-recovery-capacity-planner.md) para calcular con precisión el ancho de banda de red, almacenamiento y otros requisitos para satisfacer las necesidades de replicación. Cuando haya terminado, seleccione **Sí** en **¿Completó el planeamiento de capacidad?**.
  
  ![][11]

7. **Instale el servicio de movilidad y habilite la replicación**. Puede elegir [insertar la instalación](../site-recovery/site-recovery-vmware-to-azure.md#prepare-for-automatic-push-on-windows-machines) en las máquinas virtuales de origen o [instalar manualmente el servicio de movilidad](../site-recovery/site-recovery-vmware-to-azure.md#install-the-mobility-service-manually) en las máquinas virtuales de origen. Puede encontrar el requisito de insertar la instalación y la ruta de acceso del instalador manual en el vínculo proporcionado. Si está realizando una instalación manual, debe usar una dirección IP interna para buscar el servidor de configuración. 
  
  ![][12]
  
  La máquina virtual conmutada por error tendrá dos discos temporales: uno de la máquina virtual principal y el otro creado durante el aprovisionamiento de la máquina virtual en la región de recuperación. Para excluir el disco temporal antes de la réplica, instale el servicio de movilidad antes de habilitar la replicación. Para más información acerca de cómo excluir el disco temporal, consulte la sección [Excluir discos de la replicación](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication).
**Ahora habilite la replicación como sigue:**
  * Haga clic en **Replicar la aplicación** > **Origen**. Después de habilitar la replicación por primera vez, haga clic en +Replicar en el almacén para habilitar la replicación de más máquinas.
  * En el paso 1, configure el origen como servidor de procesos.
  * En el paso 2, especifique el modelo de implementación posterior a la conmutación por error, una cuenta de Premium Storage donde migrar, una cuenta de almacenamiento estándar para guardar los registros y una red virtual que producirá un error. 
  * En el paso 3, agregue las máquinas virtuales protegidas por dirección IP (es posible que necesite una dirección IP interna para encontrarlas). 
  * En el paso 4, configure las propiedades mediante la selección de las cuentas que configuró anteriormente en el servidor de procesos. 
  * En el paso 5, elija la directiva de replicación que creó anteriormente, Configuración de las opciones de replicación. 
  Haga clic en **Aceptar** y habilite la replicación.
  
  > [!NOTE]
  > Cuando se cancela la asignación de una máquina virtual de Azure y se vuelve a iniciar, no hay ninguna garantía de que obtendrá la misma dirección IP. Si cambia la dirección IP del servidor de procesos o servidor de configuración, o de las máquinas virtuales de Azure protegidas, es posible que la replicación en este escenario no funcione correctamente.
  
  ![][13]
  
  Cuando se diseña el entorno de Azure Storage, se recomienda utilizar cuentas de almacenamiento separadas para cada máquina virtual en un conjunto de disponibilidad. Es recomendable que siga el procedimiento recomendado en la capa de almacenamiento para máquinas virtuales [Windows](../virtual-machines/virtual-machines-windows-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) y [Linux](../virtual-machines/virtual-machines-linux-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set). La distribución de discos de máquina virtual a varias cuentas de almacenamiento ayuda a mejorar la disponibilidad de almacenamiento y distribuye la entrada/salida en toda la infraestructura de Azure Storage. Si las máquinas virtuales están en un conjunto de disponibilidad, en lugar de replicar los discos de todas las máquinas virtuales en una cuenta de almacenamiento, se recomienda encarecidamente migrar varias máquinas virtuales varias veces, para que las máquinas virtuales del mismo conjunto de disponibilidad no comparten una única cuenta de almacenamiento. Use la hoja **Habilitar replicación** para configurar una cuenta de almacenamiento de destino para cada máquina virtual, de una en una. Puede elegir un modelo de implementación posterior a la conmutación por error según sus necesidades. Si elige Resource Manager (RM) como modelo de implementación posterior a la conmutación por error, puede conmutar por error una máquina virtual de RM en una máquina virtual de RM, o puede conmutar por error una máquina virtual clásica en una máquina virtual de RM.

8. **Ejecute una conmutación por error de prueba**. Para comprobar si la replicación está completa, haga clic en Site Recovery y, después, haga clic en **Configuración** > **Elementos replicados**. Verá el estado y el porcentaje del proceso de replicación. Cuando la replicación inicial haya finalizado, ejecute la conmutación por error de prueba para validar la estrategia de replicación. Para ver los pasos detallados de la conmutación por error de prueba, consulte [Ejecución de una conmutación por error de prueba de Site Recovery](../site-recovery/site-recovery-vmware-to-azure.md#step-7--run-a-test-failover). Puede ver el estado de conmutación por error de prueba en **Configuración** > **Trabajos** > **nombreDeSuPlanDeConmutaciónPorError**. En la hoja, verá un desglose de los pasos y resultados correctos o con errores. Si se produce un error en la conmutación por error de prueba en cualquier paso, haga clic en el paso para comprobar el mensaje de error. Asegúrese de que las máquinas virtuales y la estrategia de replicación cumplen los requisitos antes de ejecutar una conmutación por error no planeada.

9. **Ejecute una conmutación por error no planeada**. Después de realizada la conmutación por error de prueba, ejecute una conmutación por error no planeada para migrar los discos en Premium Storage y replicar las instancias de máquinas virtuales. Siga los pasos detallados en [Ejecución de una conmutación por error no planeada en Site Recovery](../site-recovery/site-recovery-failover.md#run-an-unplanned-failover). Asegúrese de seleccionar **Apagar máquinas virtuales y sincronizar los últimos datos** para especificar que Site Recovery debe intentar apagar las máquinas virtuales protegidas y sincronizar los datos para que se realice la conmutación por error de la versión más reciente de los datos. Si no selecciona esta opción o el intento no se realiza correctamente, la conmutación por error se realizará a partir del último punto de recuperación disponible para la máquina virtual. Site Recovery creará una instancia de máquina virtual cuyo tipo sea igual o similar a una máquina virtual compatible con Premium Storage. Puede comprobar el rendimiento y el precio de varias instancias de máquinas virtuales en [Precios de máquinas virtuales Windows](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/) o [Precios de máquinas virtuales Linux](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Pasos posteriores a la migración

1. **Configure máquinas virtuales replicadas en el conjunto de disponibilidad, si procede**. Site Recovery no admite la migración de máquinas virtuales junto con el conjunto de disponibilidad. Dependiendo de la implementación de la máquina virtual replicada, realice una de las siguientes acciones:
  * Para una máquina virtual que se crea con el modelo de implementación clásica: agregue la máquina virtual al conjunto de disponibilidad en Azure Portal. Para ver los pasos detallados, consulte [Adición de una máquina virtual existente a un conjunto de disponibilidad](../virtual-machines/virtual-machines-windows-classic-configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set).
  * Para el modelo de implementación de Resource Manager: guarde la configuración de la máquina virtual y, después, elimine y vuelva a crear las máquinas virtuales en el conjunto de disponibilidad. Para ello, use el script que encontrará en [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) (Establecimiento del conjunto de disponibilidad de máquinas virtuales de Azure Resource Manager). Compruebe la limitación de este script y planee el tiempo de inactividad antes de ejecutar el script.

2. **Elimine las máquinas virtuales y los discos antiguos**. Antes de eliminarlos, asegúrese de que los discos Premium sean coherentes con los discos de origen y que las nuevas máquinas virtuales realizan la misma función que las máquinas virtuales de origen. En el modelo de implementación de Resource Manager (RM), elimine la máquina virtual y los discos de las cuentas de almacenamiento de origen en Azure Portal. En el modelo de implementación clásico, puede eliminar la máquina virtual y los discos en el portal clásico o en Azure Portal. Si hay un problema en el cual el disco no se elimina aunque haya eliminado la máquina virtual, consulte [Solución de errores de eliminación de VHD en una implementación de Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md) o [Solución de problemas de eliminación de VHD en una implementación clásica](storage-cannot-delete-storage-account-container-vhd.md).

3. **Limpie la infraestructura de Azure Site Recovery**. Si ya no se necesita Site Recovery, puede limpiar su infraestructura mediante la eliminación de elementos replicados, el servidor de configuración y la directiva de recuperación y, después, eliminar el almacén de Azure Site Recovery.

## <a name="troubleshooting"></a>Solución de problemas

* [Protección de supervisión y solución de problemas para las máquinas virtuales y los servidores físicos](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Foro de Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Pasos siguientes

Vea los siguientes recursos para conocer otros escenarios específicos de migración de máquinas virtuales.

* [Migrar máquinas virtuales de Azure entre cuentas de almacenamiento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Crear y cargar un VHD de Windows Server a Azure](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrar máquinas virtuales de Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consulte también los siguientes recursos para más información sobre Azure Storage y Azure Virtual Machines:

* [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md)

[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png



<!--HONumber=Feb17_HO1-->


