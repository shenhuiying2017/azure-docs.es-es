---
title: "Copia de seguridad de máquinas virtuales de Azure a escala | Microsoft Docs"
description: "Copia de seguridad simultánea de varias máquinas virtuales en Azure"
services: backup
keywords: "copia de seguridad de máquinas virtuales; copia de seguridad de máquina virtual; copia de seguridad de VM; copia de seguridad de VM de Azure; copia de seguridad y recuperación ante desastres"
author: markgalioto
ms.author: markgal
ms.date: 09/16/2017
ms.topic: article
ms.service: backup
ms.openlocfilehash: f144db921a8b2d01dbfe883a48574c4fabdae6f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Uso de Azure Portal para realizar la copia de seguridad de varias máquinas virtuales

Al realizar una copia de seguridad de datos en Azure, almacena esos datos en un recurso de Azure que se denomina “almacén de Recovery Services”. El recurso de almacén de Recovery Services está disponible en el menú Configuración de la mayoría de los servicios de Azure. La ventaja de tener el almacén de Recovery Services integrado en el menú Configuración de la mayoría de los servicios de Azure facilita enormemente la copia de seguridad de datos. Sin embargo, resulta tedioso trabajar individualmente con cada base de datos o máquina virtual de la empresa. ¿Qué ocurre si desea realizar una copia de seguridad de los datos de todas las máquinas virtuales de un departamento o una ubicación? Es fácil realizar la copia de seguridad de varias máquinas virtuales mediante la creación de una directiva de copia de seguridad y la aplicación de esa directiva a las máquinas virtuales deseadas. Este tutorial explica cómo realizar lo siguiente:

> [!div class="checklist"]
> * Creación de un almacén de Recovery Services
> * Definición de una directiva de copia de seguridad
> * Aplicación de la directiva de copia de seguridad para proteger varias máquinas virtuales
> * Desencadenamiento de un trabajo de copia de seguridad a petición para las máquinas virtuales protegidas

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

El almacén de Recovery Services contiene los datos de la copia de seguridad y la directiva de copia de seguridad que se aplica a las máquinas virtuales protegidas. La copia de seguridad de máquinas virtuales es un proceso local. No puede hacer copias de seguridad de una máquina virtual de una ubicación en un almacén de Recovery Services que está en otra ubicación. Por lo tanto, para cada ubicación de Azure que tenga máquinas virtuales de las que se tengan que realizar copias de seguridad, debe existir al menos un almacén de Recovery Services en esa ubicación.

1. En el menú izquierdo, seleccione **Más servicios** y en la lista de servicios, escriba *Recovery Services*. A medida que escribe, se filtra la lista de recursos. Cuando vea la opción Almacenes de Recovery Services en la lista, selecciónela para abrir el menú Almacenes de Recovery Services.

    ![Abrir el menú Almacén de Recovery Services](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. En el menú **Almacenes de Recovery Services**, haga clic en **Agregar** para abrir el menú Almacén de Recovery Services.

    ![Abrir el menú Almacén](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. En el menú Almacén de Recovery Services, 

    - escriba *myRecoveryServicesVault* en **Nombre**.
    - El id. de suscripción actual aparecerá en **Suscripción**. Si tiene suscripciones adicionales, puede elegir otra suscripción para el nuevo almacén.
    - En **Grupo de recursos**, seleccione **Usar existente** y elija *myResourceGroup*. Si *myResourceGroup* no existe, seleccione **Crear nuevo** y escriba *myResourceGroup*.
    - En el menú desplegable **Ubicación**, elija *Europa Occidental*.
    - Haga clic en **Crear** para crear el almacén de Recovery Services.

Un almacén de Recovery Services debe estar en la misma ubicación que las máquinas virtuales que se están protegiendo. Si tiene máquinas virtuales en varias regiones, cree un almacén de Recovery Services en cada una de ellas. En este tutorial se crea un almacén de Recovery Services en *Europa Occidental*, porque es la ubicación donde se creó *myVM* (la máquina virtual creada con el inicio rápido).

La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones de estado de la parte superior derecha del portal. Una vez creado el almacén, aparece en la lista de almacenes de servicios de recuperación.

Cuando se crea un almacén de Recovery Services, este tiene almacenamiento con redundancia geográfica de forma predeterminada. Para proporcionar resistencia de datos, el almacenamiento con redundancia geográfica replica los datos varias veces en dos regiones de Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Establecer la directiva de copia de seguridad para proteger las máquinas virtuales

Después de crear el almacén de Recovery Services, el siguiente paso es configurar el almacén para el tipo de datos y establecer la directiva de copia de seguridad. La directiva de copia de seguridad es la programación de la frecuencia y el momento de captura de los puntos de recuperación. La directiva también incluye el intervalo de retención de los puntos de recuperación. Para este tutorial, supongamos que su empresa es un complejo deportivo con un hotel, estadio, restaurantes y concesiones, y que va a proteger los datos en las máquinas virtuales. En los pasos siguientes, se crea una directiva de copia de seguridad de los datos financieros.

1. En la lista Almacenes de Recovery Services, seleccione **myRecoveryServicesVault** para abrir su panel.

   ![Abrir el menú Escenario](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. En el menú del panel del almacén, haga clic en **Copia de seguridad** para abrir el menú Copia de seguridad.

3. En el menú Objetivo de Backup, en el menú desplegable **¿Dónde se ejecuta su carga de trabajo?**, elija *Azure*. En la lista desplegable **¿De qué desea hacer una copia de seguridad?**, elija *Máquina virtual* y luego haga clic en **Copia de seguridad**.

    Estas acciones preparan el almacén de Recovery Services para interactuar con una máquina virtual. Los almacenes de Recovery Services tienen una directiva predeterminada que crea un punto de restauración al día y conserva los puntos de restauración durante 30 días.

    ![Abrir el menú Escenario](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Para crear una nueva directiva, en el menú desplegable **Elegir directiva de copia de seguridad** del menú Directiva de copia de seguridad, seleccione *Crear nueva*.

    ![Seleccionar carga de trabajo](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. En el menú **Directiva de copia de seguridad**, en **Nombre de directiva**, escriba *Finanzas*. Especifique los siguientes cambios para la directiva de copia de seguridad: 
    - Para **Frecuencia de copia de seguridad**, establezca la zona horaria en *Hora central*. Puesto que el complejo deportivo se encuentra en Texas, el propietario desea usar la hora local. Deje la frecuencia de copia de seguridad establecida en Diaria a las 3:30 a.m.
    - En **Retención de punto de copia de seguridad diaria**, establezca el período en 90 días.
    - En **Retención de punto de copia de seguridad semanal**, use el punto de restauración *Lunes* y consérvelo 52 semanas.
    - En **Retención de punto de copia de seguridad mensual**, use el punto de restauración desde el primer domingo del mes y el consérvelo 36 meses.
    - Anule la selección de la opción **Retención de punto de copia de seguridad anual**. El director de Finanzas no desea mantener los datos más allá de 36 meses.
    - Haga clic en **Aceptar** para crear la directiva de copia de seguridad.

    ![Seleccionar carga de trabajo](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    Después de crear la directiva de copia de seguridad, asóciela a las máquinas virtuales.

6. En el cuadro de diálogo **Seleccionar máquinas virtuales**, elija *myVM* y haga clic en **Aceptar** para implementar la directiva de copia de seguridad en las máquinas virtuales. 

    Aparecen todas las máquinas virtuales que estén en la misma ubicación y no estén asociadas todavía con una directiva de copia de seguridad. *myVMH1* y *myVMR1* se seleccionan para asociarse a la directiva *Finanzas*.

    ![Seleccionar carga de trabajo](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    Cuando se complete la implementación, recibirá una notificación para indicarle que se completó correctamente.

## <a name="initial-backup"></a>Copia de seguridad inicial

Ha habilitado la copia de seguridad de los almacenes de Recovery Services, pero no se ha creado la copia de seguridad inicial. Es un procedimiento recomendado de recuperación ante desastres para desencadenar la primera copia de seguridad, de modo que sus datos estén protegidos. 

Para ejecutar un trabajo de copia de seguridad a petición:

1. En el panel del almacén, haga clic en **3** en **Elementos de copia de seguridad** para abrir el menú Elementos de copia de seguridad.

    ![Icono Configuración](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Se abrirá el menú **Elementos de copia de seguridad**.

2. En el menú **Elementos de copia de seguridad**, haga clic en **Máquina Virtual de Azure** para abrir la lista de máquinas virtuales asociadas con el almacén.

    ![Icono Configuración](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Se abrirá la lista **Elementos de copia de seguridad**.

    ![Trabajo de copia de seguridad desencadenado](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. En la lista **Elementos de copia de seguridad**, haga clic en el botón de puntos suspensivos **...** para abrir el menú contextual.

4. En el menú contextual, seleccione **Realizar copia de seguridad ahora**.

    ![Menú contextual](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Se abrirá el menú Realizar copia de seguridad ahora.

5. En el menú Realizar copia de seguridad ahora, especifique el último día para conservar el punto de recuperación y haga clic en **Copia de seguridad**.

    ![establece el último día en que se mantiene el punto de recuperación de Realizar copia de seguridad ahora](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Las notificaciones de implementación le permiten saber si se ha desencadenado el trabajo de copia de seguridad y que puede supervisar el progreso del trabajo en la página de trabajos de copia de seguridad. Según el tamaño de la máquina virtual, la creación de la copia de seguridad inicial puede tardar cierto tiempo.

    Cuando se complete el trabajo de copia de seguridad inicial, puede ver su estado en el menú Trabajo de copia de seguridad. El trabajo de copia de seguridad a petición creó el punto de restauración inicial de *myVM*. Si desea hacer una copia de seguridad de otras máquinas virtuales, repita estos pasos para cada una de ellas. 

    ![Icono de Trabajos de copia de seguridad](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en este tutorial. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en este tutorial en Azure Portal.

1. En el panel **myRecoveryServicesVault**, haga clic en **3** en **Elementos de copia de seguridad** para abrir el menú Elementos de copia de seguridad.

    ![Icono Configuración](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. En el menú **Elementos de copia de seguridad**, haga clic en **Máquina Virtual de Azure** para abrir la lista de máquinas virtuales asociadas con el almacén.

    ![Icono Configuración](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    Se abrirá la lista **Elementos de copia de seguridad**.

3. En el menú **Elementos de copia de seguridad**, haga clic en los puntos suspensivos para abrir el menú contextual.

    ![Icono Configuración](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. En el menú contextual, seleccione **Detener copia de seguridad** para abrir el menú Detener copia de seguridad. 

    ![Icono Configuración](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. En el menú **Detener copia de seguridad**, seleccione el menú desplegable superior y elija **Eliminar datos de copia de seguridad**.

6. En el cuadro de diálogo **Escriba el nombre del elemento de copia de seguridad**, escriba *myVM*.
 
7. Una vez comprobado el elemento de copia de seguridad (aparece una marca de verificación), se habilita el botón **Detener copia de seguridad**. Haga clic en **Detener copia de seguridad** para detener la directiva y eliminar los puntos de restauración. 

    ![hacer clic en Detener copia de seguridad para eliminar el almacén](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png).

8. En el menú **myRecoveryServicesVault**, haga clic en **Eliminar**.

    ![hacer clic en Detener copia de seguridad para eliminar el almacén](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Una vez eliminado el almacén, se volverá a mostrar la lista de almacenes de Recovery Services.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial se usa Azure Portal para las siguientes acciones:

> [!div class="checklist"]
> * Creación de un almacén de Recovery Services
> * Configurar el almacén para proteger las máquinas virtuales
> * Crear una directiva de retención y copia de seguridad personalizada
> * Asignar la directiva para proteger varias máquinas virtuales
> * Desencadenar la copia de seguridad a petición de las máquinas virtuales

Continúe con el tutorial siguiente para restaurar una máquina virtual de Azure desde el disco. 

> [!div class="nextstepaction"]
> [Restaurar máquinas virtuales mediante la CLI](./tutorial-restore-disk.md)
