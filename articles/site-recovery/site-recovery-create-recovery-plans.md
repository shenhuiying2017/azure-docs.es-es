---
title: "Creación y personalización de un plan de recuperación para conmutación por error y recuperación en Azure Site Recovery | Microsoft Docs"
description: "Aprenda a crear y personalizar planes de recuperación en Azure Site Recovery. En este artículo se describe cómo conmutar por error y recuperar máquinas virtuales y servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>Creación de un plan de recuperación mediante Site Recovery

En este artículo se describe cómo crear y personalizar un plan de recuperación en [Azure Site Recovery](site-recovery-overview.md).

Cree un plan de recuperación para realizar las siguientes tareas:

* Definir grupos de máquinas que conmuten por error juntas y también se inicien juntas.
* Modelan las dependencias entre máquinas agrupándolas en un grupo de planes de recuperación. Por ejemplo, para conmutar por error y mostrar una aplicación específica, incluya todas las máquinas virtuales de dicha aplicación en el mismo grupo de planes de recuperación.
* Ejecutar una conmutación por error. Puede ejecutar una conmutación por error de prueba, planeada o no planeada en un plan de recuperación.

## <a name="why-use-a-recovery-plan"></a>Por qué usar un plan de recuperación

Los planes de recuperación pueden ayudarle a planear un proceso de recuperación sistemático mediante la creación de unidades pequeñas e independientes que se pueden administrar. Normalmente estas unidades representan una aplicación en su entorno. Un plan de recuperación puede ayudarle a definir la secuencia en la que se inician las máquinas virtuales. También puede usar un plan de recuperación para automatizar tareas comunes durante la recuperación.

> [!TIP]
> Una manera de comprobar que está preparado para la migración a la nube o la recuperación ante desastres es asegurarse de que cada una de las aplicaciones forma parte de un plan de recuperación. Además, asegúrese de que cada plan de recuperación se pueda recuperar en Azure. Con esta preparación, puede migrar o conmutar por error con confianza el centro de datos completo a Azure.
 
Un plan de recuperación tiene tres propuestas de valor principales:

* Modelar una aplicación para capturar dependencias
* Automatizar la mayoría de las tareas de recuperación para reducir el RTO
* Probar la conmutación por error para estar listo en caso de desastres

### <a name="model-an-application-to-capture-dependencies"></a>Modelar una aplicación para capturar las dependencias

Un plan de recuperación es un grupo de máquinas virtuales que comprenden generalmente una aplicación y que conmutan por error juntas. Con las construcciones del plan de recuperación, puede mejorar un grupo de planes de recuperación para capturar las propiedades específicas de la aplicación. 

En este artículo se usa una aplicación típica de tres capas que podrían tener un servidor back-end de SQL, middleware y un servidor front-end web. Puede personalizar el plan de recuperación para garantizar que las máquinas virtuales se inician en el orden correcto tras una conmutación por error. Primero debe iniciarse el servidor back-end de SQL, luego el middleware y por último el servidor front-end web. Este orden garantiza que para cuando la última máquina virtual se inicia, la aplicación está en funcionamiento. 

Por ejemplo, cuando se inicia el middleware, intenta conectarse a la capa de SQL. El plan de recuperación garantiza que la capa SQL ya se está ejecutando. Tener un servidor front-end que se inicie el último también garantiza que los usuarios finales no se conecten a la dirección URL de la aplicación hasta que todos los componentes estén en funcionamiento y la aplicación esté lista para aceptar solicitudes. Para compilar estas dependencias, personalice el plan de recuperación para agregar grupos y, a continuación, seleccione una máquina virtual. Para mover una máquina virtual entre grupos, cambie el grupo de la máquina virtual.

![Captura de pantalla de un plan de recuperación de ejemplo en Site Recovery](./media/site-recovery-create-recovery-plans/rp.png)

Cuando se haya completado la personalización, podrá visualizar los pasos exactos de la recuperación. Este es el orden de los pasos ejecutados durante la conmutación por error de un plan de recuperación:

1. Un paso de apagado intenta desactivar las máquinas virtuales locales. (Excepto en una conmutación por error de prueba, durante la cual el sitio principal se debe seguir ejecutando).
2. Luego desencadena una conmutación por error de todas las máquinas virtuales del plan de recuperación en paralelo. El paso de la conmutación por error prepara los discos de la máquina virtual con los datos replicados.
3. Los grupos de inicio se ejecutan en orden e inician las máquinas virtuales de cada grupo. Primero se ejecuta el grupo 1, luego el grupo 2 y finalmente el grupo 3. Si hay más de una máquina virtual en un grupo (por ejemplo, un servidor front-end web de carga equilibrada), todas las máquinas virtuales se inician en paralelo.

> [!TIP]
> La secuenciación entre grupos garantiza que siempre se respetan las dependencias entre las diversas capas de aplicación. El paralelismo, cuando se usa de la manera adecuada, mejora el RTO de la recuperación de aplicaciones.

   > [!NOTE]
   > Las máquinas que forman parte de un único grupo conmutan por error en paralelo. Las que forman parte de grupos diferentes, conmutan por error en el orden de los grupos. Solo después de que todas las máquinas del grupo 1 se hayan conmutado por error e iniciado, se iniciará la conmutación por error de las máquinas del grupo 2.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatización de la mayoría de las tareas de recuperación para reducir el RTO

La recuperación de aplicaciones de gran tamaño puede ser una tarea compleja. Tener que recordar muchos pasos manuales en caso de conflicto es difícil y hace que el proceso sea proclive a errores. Además, podría ocurrir que alguien que desconoce los entresijos de la aplicación fuera el que desencadenara realmente la conmutación por error. 

Puede usar un plan de recuperación para automatizar las acciones necesarias que se deben realizar en cada paso. Estas acciones se pueden configurar mediante runbooks de Azure Automation. Con los runbooks, puede automatizar las tareas de recuperación comunes, como las de los siguientes ejemplos. En el caso de tareas que no se pueden automatizar, puede insertar acciones manuales en los planes de recuperación.

* **Tareas en la máquina virtual de Azure posteriores a la conmutación por error**: estas tareas son necesarias normalmente para conectar con la máquina virtual. Ejemplos:
    * Creación de una dirección IP pública en la máquina virtual posterior a la conmutación por error
    * Asigne un grupo de seguridad de red a la NIC en la máquina virtual conmutada por error.
    * Agregue un equilibrador de carga a un conjunto de disponibilidad.
* **Tareas dentro de la máquina virtual posteriores a la conmutación por error**: estas tareas reconfiguran la aplicación a fin de que siga funcionando correctamente en el nuevo entorno. Ejemplos:
    * Modificación de la cadena de conexión de base de datos dentro de la máquina virtual
    * Cambie la configuración o las reglas del servidor web.

> [!TIP]
> Consiga una conmutación por error con un clic y optimice el RTO mediante la creación de un plan de recuperación completo que automatice las tareas posteriores a la recuperación mediante runbooks de Automation.

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Prueba de la conmutación por error para estar listo para un desastre

Puede usar un plan de recuperación para desencadenar una conmutación por error o una conmutación por error de prueba. Realice siempre una conmutación por error de prueba en la aplicación antes de realizar la conmutación por error real. Las conmutaciones por error de prueba ayudan a comprobar si la aplicación aparece en el sitio de recuperación. Si se le ha olvidado algún paso de la configuración, puede volver fácilmente al principio y rehacer la conmutación por error de prueba. Realice la conmutación por error de prueba varias veces hasta que sepa con certeza que la aplicación se recupera sin problemas.

![Captura de pantalla de un plan de recuperación de prueba de ejemplo en Site Recovery](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> Como cada aplicación es diferente, es necesario crear planes de recuperación personalizados para cada una. 
>
> Además, en el entorno dinámico actual orientado al centro de datos, las aplicaciones y sus dependencias cambian con frecuencia. Para asegurarse de que el plan de recuperación es actual, realice una conmutación por error de prueba de las aplicaciones cada trimestre.

## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

1. Seleccione **Planes de recuperación** > **Crear plan de recuperación**.
   Especifique un nombre para el plan de recuperación y un origen y destino. La ubicación de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Elija un origen y destino en función de las máquinas virtuales que quiere que formen parte del plan de recuperación. 

   |Escenario                   |Origen               |Destino           |
   |---------------------------|---------------------|-----------------|
   |Azure a Azure             |Región de Azure         |Región de Azure     |
   |VMware en Azure            |Servidor de configuración |Azure            |
   |Virtual Machine Manager (VMM) a Azure               |Nombre para mostrar de VMM    |Azure            |
   |Sitio de Hyper-V a Azure      |Nombre del sitio Hyper-V    |Azure            |
   |Máquinas físicas a Azure |Servidor de configuración |Azure            |
   |VMM a VMM                 |Nombre descriptivo de VMM    |Nombre para mostrar de VMM|

   > [!NOTE]
   > Un plan de recuperación puede contener máquinas virtuales que tengan el mismo origen y destino. Las máquinas virtuales de VMware y System Center Virtual Machine Manager (VMM) no pueden estar en el mismo plan de recuperación. Sin embargo, puede agregar máquinas virtuales de VMware y máquinas virtuales físicas al mismo plan de recuperación. En este caso, el origen de ambas máquinas es un servidor de configuración.

2. En **Seleccionar máquinas virtuales**, seleccione las máquinas virtuales (o el grupo de replicación) que quiere agregar al grupo predeterminado (grupo 1) del plan de recuperación. Solo puede seleccionar las máquinas virtuales que se protegieron en el origen (como se seleccionaron en el plan de recuperación) y que están protegidas en el destino (como se seleccionaron en el plan de recuperación).

## <a name="customize-and-extend-recovery-plans"></a>Personalización y extensión de planes de recuperación

Para personalizar y ampliar los planes de recuperación, vaya al panel de recursos del plan de recuperación de Site Recovery. Seleccione la pestaña **Personalizar**. Puede personalizar y ampliar los planes de recuperación mediante las siguientes opciones:

- **Agregar nuevos grupos**: puede agregar hasta siete grupos de planes de recuperación adicionales al grupo predeterminado. Luego, puede agregar más máquinas o grupos de replicación a esos grupos de planes de recuperación. Los grupos se numeran en el orden en que se agregan. Solo se puede incluir una máquina virtual o un grupo de replicación en un grupo de planes de recuperación.
- **Agregar una acción manual**: puede agregar acciones manuales que se ejecuten antes o después de un grupo de planes de recuperación. Cuando se ejecuta el plan de recuperación, se detiene en el punto en que se insertó la acción manual. En un cuadro de diálogo, se le pide que especifique que se completó la acción manual.
- **Agregar un script**: puede agregar scripts que se ejecutan antes o después de un grupo de planes recuperación. Cuando agrega un script, este agrega un nuevo conjunto de acciones al grupo. Por ejemplo, se crea un conjunto de pasos previos del grupo 1 con el nombre *Grupo 1: pasos previos*. Dentro de este conjunto se enumeran todos los pasos previos. Solo puede agregar un script en el sitio principal si hay un servidor VMM implementado. Para más información, consulte [Adición de scripts de VMM a los planes de recuperación](site-recovery-how-to-add-vmmscript.md).
- **Agregar runbooks de Azure**: puede ampliar los planes de recuperación con runbooks de Azure. Por ejemplo, puede usar un runbook para automatizar las tareas o para crear una recuperación en un solo paso. Para más información, consulte [Adición de runbooks de Azure Automation a los planes de recuperación](site-recovery-runbook-automation.md).


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>Adición de un script, un runbook o una acción manual a un plan

Después de agregar máquinas virtuales o grupos de replicación a un grupo de planes de recuperación predeterminado, puede agregar un script o una acción manual al grupo de planes de recuperación.

1. Abra el plan de recuperación.
2. En la lista de **pasos**, seleccione un elemento. A continuación, seleccione **Script** o **Manual Action** (Acción manual).
3. Especifique si quiere agregar el script o la acción antes o después del elemento seleccionado. Para subir o bajar la posición del script, seleccione los botones **Move Up** (Subir) o **Move Down** (Bajar).
4. Si agrega un script de VMM, seleccione **Failover to VMM script** (Conmutación por error en script de VMM). En **Script Path** (Ruta de acceso del script), escriba la ruta de acceso relativa al recurso compartido. Por ejemplo, **\RPScripts\RPScript.PS1**.
5. Si agrega un runbook de Automation, especifique la cuenta de Automation en la que se encuentra el runbook. A continuación, seleccione el script del runbook de Azure.
6. Realice una conmutación por error del plan de recuperación para asegurarse de que el script funciona según lo previsto.

Las opciones del script o el runbook solo están disponibles en los siguientes escenarios y durante una conmutación por error o una conmutación por recuperación. Hay disponible una acción manual para la conmutación por error y la conmutación por recuperación.


|Escenario               |Conmutación por error |Conmutación por recuperación |
|-----------------------|---------|---------|
|Azure a Azure         |Runbook |Runbook  |
|VMware en Azure        |Runbook |N/D       | 
|VMM a Azure           |Runbook |Script   |
|Sitio de Hyper-V a Azure  |Runbook |N/D       |
|VMM a VMM             |Script   |Script   |


## <a name="next-steps"></a>pasos siguientes

* Aprenda más sobre la [ejecución de conmutaciones por error](site-recovery-failover.md).  
* Consulte este vídeo para ver el plan de recuperación en acción:
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
