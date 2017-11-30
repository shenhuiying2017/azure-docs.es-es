---
title: "Configuración de la recuperación ante desastres para las máquinas virtuales de Azure en una región secundaria de Azure con Azure Site Recovery (versión preliminar)"
description: "Aprenda a configurar la recuperación ante desastres para las máquinas virtuales de Azure en una región de Azure diferente con el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7dfe543f30772fc11174a7fff43369c1e2f19029
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Configuración de la recuperación ante desastres para las máquinas virtuales de Azure en una región secundaria de Azure (versión preliminar)

El servicio [Azure Site Recovery](../site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

Este tutorial muestra cómo configurar la recuperación ante desastres en una región secundaria de Azure para máquinas virtuales de Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un almacén de Recovery Services
> * Comprobación de la configuración de los recursos de destino
> * Configuración del acceso de salida para las máquinas virtuales
> * Habilitación de la replicación para una máquina virtual

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Asegúrese de entender la [arquitectura y los componentes del escenario](concepts-azure-to-azure-architecture.md).
- Revise los [requisitos de compatibilidad](site-recovery-support-matrix-azure-to-azure.md) de todos los componentes.

## <a name="create-a-vault"></a>Creación de un almacén

Cree el almacén en cualquier región, excepto en la de origen.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Haga clic en **Nuevo** > **Supervisión y administración** > **Backup y Site Recovery**.
3. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. Si tiene más de una suscripción, seleccione la apropiada.
4. Cree un grupo de recursos o seleccione uno existente. Especifique una región de Azure. Para comprobar las regiones admitidas, consulte la disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** y, después, en **Crear**.

   ![Almacén nuevo](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   El nuevo almacén se agrega al **Panel**, en **Todos los recursos**, y en la página principal **Almacenes de Recovery Services**.

## <a name="verify-target-resources"></a>Comprobación de los recursos de destino

1. Compruebe que su suscripción de Azure permite crear máquinas virtuales en la región de destino que se usa para la recuperación ante desastres. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

2. Asegúrese de que su suscripción tiene suficientes recursos para admitir máquinas virtuales con tamaños que se correspondan con las máquinas virtuales de origen. Site Recovery elige el mismo tamaño para la máquina virtual de destino o el más cercano posible.

## <a name="configure-outbound-network-connectivity"></a>Configuración de la conectividad de red saliente

Para que Site Recovery funcione de la forma esperada, debe realizar algunos cambios en la conectividad de red de salida desde las máquinas virtuales que desee replicar.

- Site Recovery no admite el uso de un servidor proxy de autenticación para la conectividad de la red de control.
- Si tiene un servidor proxy de autenticación, no se puede habilitar la replicación.

### <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en la dirección URL para controlar la conectividad de salida, permita el acceso a las siguientes direcciones URL usadas por Site Recovery.

| **URL** | **Detalles** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que los datos se puedan escribir desde la máquina virtual a la cuenta de almacenamiento de caché en la región de origen. |
| login.microsoftonline.com | Proporciona autorización y autenticación de las direcciones URL del servicio Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que la máquina virtual se comunique con el servicio Site Recovery. |
| *.servicebus.windows.net | Permite que la máquina virtual escriba los datos de diagnóstico y supervisión de Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

Si utiliza un firewall basado en IP, proxy o reglas NSG para controlar la conectividad saliente, los siguientes intervalos de direcciones IP tienen que estar en la lista de permitidos. Descargue una lista de intervalos de los siguientes vínculos:

  - [Intervalos de direcciones IP del centro de datos de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653)
  - [Intervalos de direcciones IP del centro de datos de Windows Azure en Alemania](http://www.microsoft.com/download/details.aspx?id=54770)
  - [Intervalos de direcciones IP del centro de datos de Windows Azure en China](http://www.microsoft.com/download/details.aspx?id=42064)
  - [Direcciones URL e intervalos de direcciones IP de Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Direcciones IP de puntos de conexión del servicio Site Recovery](https://aka.ms/site-recovery-public-ips)

Utilice estas listas para configurar los controles de acceso de red en la red. Puede utilizar este [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) para crear las reglas NSG requeridas.

## <a name="verify-azure-vm-certificates"></a>Comprobación de los certificados de la máquina virtual de Azure

Asegúrese de que todos los certificados raíz más recientes estén presentes en la máquina virtual de Windows o Linux que desee replicar. Si los certificados raíz más recientes no están, la máquina virtual no se puede registrar en Site Recovery debido a restricciones de seguridad.

- Para las máquinas virtuales de Windows, instale las actualizaciones de Windows más recientes en la máquina virtual, de modo que todos los certificados raíz de confianza estén en ella. En un entorno desconectado, siga los procesos estándar de actualización de certificados y de Windows Update en su organización.

- En las máquinas virtuales Linux, para obtener los certificados raíz de confianza y la lista de revocación de certificados en la máquina virtual, siga las instrucciones proporcionadas por su distribuidor de Linux.

## <a name="set-permissions-on-the-account"></a>Obtención de permisos en la cuenta

Azure Site Recovery proporciona tres roles integrados para controlar las operaciones de administración de Site Recovery.

- **Colaborador de Site Recovery**: este rol tiene todos los permisos necesarios para administrar las operaciones de Azure Site Recovery en un almacén de Recovery Services. Sin embargo, un usuario con este rol no puede crear ni eliminar un almacén de Recovery Services, ni tampoco asignar derechos de acceso a otros usuarios. Este rol es ideal para administradores de recuperación ante desastres que pueden habilitar y administrar la recuperación ante desastres para aplicaciones u organizaciones completas.

- **Operador de Site Recovery**: este rol tiene permisos para ejecutar y administrar operaciones de conmutación por error y por recuperación. Un usuario con este rol no puede habilitar ni deshabilitar la replicación, crear ni eliminar almacenes, registrar una nueva infraestructura ni asignar derechos de acceso a otros usuarios. Este rol es ideal para un operador de recuperación ante desastres que puede conmutar por error desde máquinas virtuales o aplicaciones cuando se lo indican los propietarios de las aplicaciones y los administradores de TI. Tras la resolución del desastre, el operador de recuperación ante desastres puede volver a proteger las máquinas virtuales y realizar la conmutación por recuperación.

- **Lector de Site Recovery**: este rol tiene permisos para ver todas las operaciones de administración de Site Recovery. Este rol es ideal para un ejecutivo de supervisión de TI que puede supervisar el estado actual de protección y crear vales de soporte.

Más información sobre [roles integrados del control de acceso basado en rol de Azure](../../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Habilitar replicación

### <a name="select-the-source"></a>Seleccione el origen

1. En los almacenes de Recovery Services, haga clic en el nombre del almacén y > **+Replicar**. 
2. En **Origen**, seleccione **Azure - VERSIÓN PRELIMINAR**.
3. En **Ubicación de origen**, seleccione la región de Azure de origen donde se ejecutan actualmente sus máquinas virtuales.
4. Seleccione el **modelo de implementación de las máquinas virtuales de Azure**: **Resource Manager** o **Clásico**.
5. Seleccione el **grupo de recursos de origen** para las máquinas virtuales de Resource Manager o el **servicio en la nube** para las máquinas virtuales clásicas.
6. Haga clic en **Aceptar** para guardar la configuración.

### <a name="select-the-vms"></a>Seleccione las máquinas virtuales

Site Recovery recupera una lista de las máquinas virtuales asociadas a la suscripción y el servicio en la nube/grupo de recursos.

1. En **Máquinas virtuales**, seleccione las máquinas virtuales que quiere replicar.
2. Haga clic en **Aceptar**.

### <a name="configure-replication-settings"></a>Configuración de las opciones de replicación

Site Recovery crea la configuración predeterminada y la directiva de replicación para la región de destino. Puede cambiar la configuración para ajustarla a sus requisitos.

1. Haga clic en **Configuración** para ver la configuración de destino.
2. Para invalidar la configuración de destino de forma predeterminada, haga clic en **Personalizar**. 

![Definición de la configuración](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Ubicación de destino**: la región de destino que se usa para la recuperación ante desastres. Se recomienda que la ubicación de destino coincida con la ubicación del almacén de Site Recovery.

- **Grupo de recursos de destino**: el grupo de recursos en la región de destino que contiene las máquinas virtuales de Azure después de la conmutación por error. De forma predeterminada, Site Recovery crea un nuevo grupo de recursos en la región de destino con un sufijo "asr".

- **Red virtual de destino**: la red en la región de destino en la que las máquinas virtuales se encuentran después de la conmutación por error.
  De forma predeterminada, Site Recovery crea una nueva red (y subredes) virtual en la región de destino con un sufijo "asr".

- **Cuentas de almacenamiento en caché**: Site Recovery utiliza una cuenta de almacenamiento en la región de origen. Los cambios en las máquinas virtuales de origen se envían a esta cuenta, antes de la replicación en la ubicación de destino.

- **Cuentas de almacenamiento de destino**: de forma predeterminada, Site Recovery crea una nueva cuenta de almacenamiento en la región de destino, para reflejar la cuenta de almacenamiento de la máquina virtual de origen.

- **Conjuntos de disponibilidad de destino**: de forma predeterminada, Site Recovery crea un nuevo conjunto de disponibilidad en la región de destino con el sufijo "asr". Solo puede agregar conjuntos de disponibilidad si las máquinas virtuales forman parte de un conjunto en la región de origen.

- **Nombre de la directiva de replicación**: Nombre de la directiva.

- **Retención del punto de recuperación**: de forma predeterminada, Site Recovery conserva los puntos de recuperación durante 24 horas. Puede configurar un valor entre 1 y 72 horas.

- **Frecuencia de instantáneas coherentes con la aplicación**: de forma predeterminada, Site Recovery toma una instantánea coherente con la aplicación cada cuatro horas. Puede configurar cualquier valor entre 1 y 12 horas. Una instantánea coherente con la aplicación es una instantánea en un momento dado de los datos de la aplicación dentro de la máquina virtual. El Servicio de instantáneas de volumen (VSS) garantiza que la aplicación en la máquina virtual se encuentre en un estado coherente cuando se toma la instantánea.

### <a name="track-replication-status"></a>Seguimiento del estado de replicación

1. En **Configuración**, haga clic en **Actualizar** para obtener el estado más reciente.

2. Puede hacer un seguimiento del progreso del trabajo **Habilitar la protección** en **Configuración**>**Trabajos**>**Trabajos de Site Recovery**.

3. En **Configuración** > **Elementos replicados**, puede ver el estado de las máquinas virtuales y el progreso inicial de la replicación. Haga clic en la máquina virtual para ir a los detalles de su configuración.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se configuró la recuperación ante desastres para una máquina virtual de Azure. El paso siguiente es probar la configuración.

> [!div class="nextstepaction"]
> [Exploración de la recuperación ante desastres](azure-to-azure-tutorial-dr-drill.md)
