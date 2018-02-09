---
title: "Creación y personalización de los planes de recuperación para la conmutación por error y recuperación en Azure Site Recovery | Microsoft Docs"
description: "Se describe cómo crear y personalizar planes de recuperación en Azure Site Recovery para conmutar por error y recuperar máquinas virtuales y servidores físicos."
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
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>Creación de planes de recuperación


En este artículo se proporciona información sobre cómo crear y personalizar planes de recuperación en [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Cree planes de recuperación para realizar las siguientes tareas:

* Definen grupos de máquinas que conmutan por error juntas y también se inician juntas.
* Modelan dependencias entre máquinas, agrupándolas en un grupo de planes de recuperación. Por ejemplo, para conmutar por error y abrir una aplicación específica, agrupe todas las máquinas virtuales de dicha aplicación en el mismo grupo de planes de recuperación.
* Ejecutar una conmutación por error. Puede ejecutar una conmutación por error de prueba, planeada o no planeada en un plan de recuperación.

## <a name="why-use-recovery-plans"></a>Motivos por los que usar planes de recuperación

Los planes de recuperación le ayudan a planear un proceso de recuperación sistemática mediante la creación de unidades pequeñas e independientes que se pueden administrar. Normalmente estas unidades representarán una aplicación en su entorno. El plan de recuperación no solo le permite definir la secuencia en la que iniciar las máquinas virtuales, sino que también le ayuda a automatizar tareas comunes durante la recuperación.


**Básicamente, una manera de comprobar si está preparado para la migración a la nube o la recuperación ante desastres es asegurándose de que cada una de sus aplicaciones forme parte de un plan de recuperación y que cada uno de dichos planes esté probado para la recuperación en Microsoft Azure. Con esta preparación, puede migrar o conmutar por error con seguridad el centro de datos completo a Microsoft Azure.**
 
A continuación, se muestran tres proposiciones de para clave-valor de un plan de recuperación:

### <a name="model-an-application-to-capture-dependencies"></a>Modelar una aplicación para capturar las dependencias

Un plan de recuperación es un grupo de máquinas virtuales que generalmente forman una aplicación y conmutan por error conjuntamente. Con las construcciones del plan de recuperación, puede mejorar este grupo para capturar las propiedades específicas de la aplicación.
 
Pongamos el ejemplo de una aplicación típica de tres niveles con

* un servidor back-end de SQL
* un software intermedio
* un front-end web

El plan de recuperación puede personalizarse para garantizar que las máquinas virtuales aparezcan en el orden correcto tras una conmutación por error. En primer lugar, debe aparecer el back-end de SQL, después el software intermedio y, por último, el front-end web. Este orden garantiza que la aplicación funcione en el momento en que aparece la última máquina virtual. Por ejemplo, cuando aparezca el software intermedio, intentará conectar con el nivel de SQL y el plan de recuperación se asegurará de que ya se está ejecutando el nivel de SQL. Los servidores front-end que aparecen por último también garantizan que los usuarios finales no se conecten a la dirección URL de la aplicación por error hasta que se ejecuten todos los componentes y la aplicación esté preparada para aceptar las solicitudes. Para compilar estas dependencias, puede personalizar el plan de recuperación para agregar grupos. A continuación, seleccione una máquina virtual y cambie su grupo para moverla entre grupos.

![Plan de recuperación de muestra](./media/site-recovery-create-recovery-plans/rp.png)

Cuando se haya completado la personalización, podrá visualizar los pasos exactos de la recuperación. A continuación, se indica el orden de los pasos ejecutados durante la conmutación por error de un plan de recuperación:

* En primer lugar, hay un paso de apagado que intenta desactivar las máquinas virtuales locales (excepto en la conmutación por error de prueba en la que el sitio primario debe continuar ejecutándose).
* A continuación, desencadena una conmutación por error de todas las máquinas virtuales del plan de recuperación en paralelo. El paso de la conmutación por error prepara los discos de las máquinas virtuales de los datos replicados.
* Por último, los grupos de inicio se ejecutan en su orden, iniciando las máquinas virtuales de cada grupo: el grupo 1 en primer lugar, a continuación, el grupo 2 y, finalmente, el grupo 3. Si hay más de una máquina virtual en un grupo (por ejemplo, un front-end web de carga equilibrada), se inician todas en paralelo.

**La secuenciación en grupos garantiza que se respeten las dependencias entre las distintas capas de aplicación y el paralelismo, si procede, mejora el RTO de recuperación de la aplicación.**

   > [!NOTE]
   > Las máquinas que forman parte de un único grupo conmutarán por error en paralelo. Las que forman parte de grupos diferentes, conmutarán por error en el orden de los grupos. Solo después de que todas las máquinas del grupo 1 se hayan conmutado por error y reiniciado, se iniciará la conmutación por error de las máquinas del grupo 2.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatización de la mayoría de las tareas de recuperación para reducir el RTO

La recuperación de aplicaciones de gran tamaño puede ser una tarea compleja. También es difícil recordar los pasos exactos de personalización posteriores a la migración o a la conmutación por error. A veces, no es usted, sino otra persona que no es consciente de las complejidades de la aplicación, quien necesita desencadenar la conmutación por error. Recordar demasiados pasos manuales en momentos de caos es difícil y propenso a errores. Un plan de recuperación le proporciona una manera de automatizar las acciones necesarias que debe realizar en cada paso mediante el uso de runbooks de Microsoft Azure Automation. Con los runbooks, puede automatizar las tareas comunes de recuperación como en los ejemplos que se indican a continuación. En el caso de las tareas que no se pueden automatizar, los planes de recuperación también le proporcionan la capacidad de insertar acciones manuales.

* Tareas en las máquinas virtuales de Azure posteriores a la conmutación por error: suelen ser necesarias a fin de que pueda conectarse a la máquina virtual. A continuación, se muestran algunos ejemplos:
    * Creación de una IP pública en la máquina virtual posterior a la conmutación por error
    * Asignación de un NSG al adaptador de red de la máquina virtual de la conmutación por error
    * Adición de un equilibrador de carga a un conjunto de disponibilidad
* Tareas dentro de la máquina virtual posteriores a la conmutación por error: vuelven a configurar la aplicación a fin de que siga funcionando correctamente en el nuevo entorno. A continuación, se muestran algunos ejemplos:
    * Modificación de la cadena de conexión de base de datos dentro de la máquina virtual
    * Cambio de las reglas o la configuración del servidor web

**Con un plan de recuperación completo que automatice las tareas posteriores a la recuperación mediante runbooks de automatización, puede lograr la conmutación por error con un solo clic y optimizar el RTO.**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Prueba de la conmutación por error para estar listo para un desastre

Se puede usar un plan de recuperación para desencadenar una conmutación por error o una conmutación por error de prueba. Siempre debe realizar una conmutación por error de prueba en la aplicación antes de realizar una conmutación por error. La conmutación por error de prueba le ayuda a comprobar si la aplicación aparecerá en el sitio de recuperación.  Si no se ha ejecutado algo, puede desencadenar la limpieza fácilmente y rehacer la conmutación por error de prueba. Realice la conmutación por error de prueba varias veces hasta que sepa con certeza que la aplicación se recupera sin problemas.

![Prueba del plan de recuperación](./media/site-recovery-create-recovery-plans/rptest.png)

**Cada aplicación es diferente y es necesario crear planes de recuperación personalizados para cada una. Además, en este mundo dinámico de centro de datos, las aplicaciones y sus dependencias cambian continuamente. Realice una conmutación por error de prueba en las aplicaciones una vez por trimestre para comprobar que el plan de recuperación es actual.**

## <a name="how-to-create-a-recovery-plan"></a>Cómo crear un plan de recuperación

1. Haga clic en **Planes de recuperación** > **Crear plan de recuperación**.
   Especifique un nombre para el plan de recuperación y un origen y destino. La ubicación de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Elija un origen y destino en función de las máquinas virtuales que quiere que formen parte del plan de recuperación. 

   |Escenario                   |Origen               |Destino           |
   |---------------------------|---------------------|-----------------|
   |Azure a Azure             |Región de Azure         |Región de Azure     |
   |VMware a Azure            |Servidor de configuración |Azure            |
   |VMM a Azure               |Nombre descriptivo de VMM    |Azure            |
   |Sitio Hyper-V a Azure      |Nombre del sitio Hyper-V    |Azure            |
   |Máquinas físicas a Azure |Servidor de configuración |Azure            |
   |VMM a VMM                 |Nombre descriptivo de VMM    |Nombre descriptivo de VMM|

   > [!NOTE]
   > Un plan de recuperación puede contener máquinas virtuales que tengan el mismo origen y destino. Las máquinas virtuales de VMware y VMM no puede formar parte del mismo plan de recuperación. Las máquinas virtuales de VMware y las máquinas físicas, sin embargo, se pueden agregar al mismo plan porque el origen para ambas es un servidor de configuración.

2. En **Seleccionar máquinas virtuales**, seleccione las máquinas virtuales (o un grupo de replicación) que desee agregar al grupo predeterminado (Grupo 1) del plan de recuperación. Solo se permite seleccionar las máquinas virtuales que se protegieron en el origen (tal como aparecen seleccionadas en el plan de recuperación) y están protegidas en el destino (tal como aparecen seleccionadas en el plan de recuperación).

## <a name="how-to-customize-and-extend-recovery-plans"></a>Cómo personalizar y extender los planes de recuperación

Para personalizar y extender los planes de recuperación, vaya a la hoja de recursos del plan de recuperación de Site Recovery y haga clic en la pestaña Personalizar.

Puede personalizar y extender los planes de recuperación:

- **Agregar grupos nuevos**: agregue más grupos de planes de recuperación (hasta siete) al grupo predeterminado y después agregue más máquinas o grupos de replicación a esos grupos de planes de recuperación. Los grupos se numeran en el orden en que se agregan. Solo se puede incluir una máquina virtual o un grupo de replicación en un grupo de planes de recuperación.
- **Agregar una acción manual**: puede agregar acciones manuales que se ejecuten antes o después de un grupo de planes de recuperación. Cuando se ejecuta el plan de recuperación, se detiene en el punto en que se insertó la acción manual. En un cuadro de diálogo, se le pide que especifique que se completó la acción manual.
- **Agregar un script**: puede agregar scripts que se ejecutan antes o después de un grupo de planes recuperación. Cuando agrega un script, este agrega un nuevo conjunto de acciones al grupo. Por ejemplo, un conjunto de pasos previos para el grupo 1 se creará con el nombre: Grupo 1: pasos previos. Dentro de este conjunto se enumeran todos los pasos previos. Solo puede agregar un script en el sitio principal si hay un servidor VMM implementado. [Más información](site-recovery-how-to-add-vmmscript.md).
- **Agregar runbooks de Azure**: puede extender planes de recuperación con runbooks de Azure. Por ejemplo, para automatizar tareas o para crear una recuperación en un solo paso. [Más información](site-recovery-runbook-automation.md).


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>Cómo incorporar un script, un runbook o una acción manual a un plan

Puede agregar un script o una acción manual al grupo de planes de recuperación predeterminado después de haberle agregado máquinas virtuales o grupos de replicación y de haber creado el plan.

1. Abra el plan de recuperación.
2. Haga clic en un elemento en la lista **Paso** y en **Script** o **Acción manual**.
3. Especifique si desea agregar el script o la acción antes o después del elemento seleccionado. Para subir o bajar la posición del script, use los botones **Subir** y **Bajar**.
4. Si agrega un script de VMM, seleccione **Failover to VMM script** (Conmutación por error en script de VMM). En **Ruta de acceso del script**, escriba la ruta de acceso relativa al recurso compartido. En el ejemplo siguiente con VMM, especifique la ruta de acceso: **\RPScripts\RPScript.PS1**.
5. Si agrega un runbook de Azure Automation, especifique la cuenta de Azure Automation donde se encuentra el runbook y seleccione el valor de script de runbook de Azure adecuado.
6. Para asegurarse de que el script funcione según lo previsto, realice una conmutación por error del plan de recuperación.

Las opciones del script o el runbook solo están disponibles en los siguientes escenarios al realizar una conmutación por error o una conmutación por recuperación. Hay disponible una acción manual para la conmutación por error y la conmutación por recuperación.


|Escenario               |Conmutación por error |Conmutación por recuperación |
|-----------------------|---------|---------|
|Azure a Azure         |Runbooks |Runbook  |
|VMware a Azure        |Runbooks |N/D       | 
|VMM a Azure           |Runbooks |Script   |
|Sitio Hyper-V a Azure  |Runbooks |N/D       |
|VMM a VMM             |Script   |Script   |


## <a name="next-steps"></a>pasos siguientes

[Aprenda más](site-recovery-failover.md) sobre la ejecución de conmutaciones por error.

Consulte este vídeo para ver el plan de recuperación en acción.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
