---
title: Directivas de copia de seguridad de StorSimple Snapshot Manager | Microsoft Docs
description: "Describe cómo usar el complemento MMC de Administrador de instantáneas StorSimple para crear y administrar las directivas de copia de seguridad que controlan las copias de seguridad programadas."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 218c89e403673c16c72da95aa2c1d685bbed5a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Use Administrador de instantáneas StorSimple para crear y administrar directivas de copia de seguridad
## <a name="overview"></a>Información general
Una directiva de copia de seguridad crea una programación para realizar una copia de seguridad de los datos del volumen localmente o en la nube. Cuando crea una directiva de copia de seguridad, también puede especificar una directiva de retención. (Puede retener un máximo de 64 instantáneas). Para obtener más información sobre las directivas de copia de seguridad, consulte [Tipos de copia de seguridad](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) en [Serie StorSimple 8000: una solución de almacenamiento en la nube híbrida](storsimple-overview.md).

Este tutorial explica cómo realizar lo siguiente:

* Creación de una directiva de copia de seguridad
* Edición de una directiva de copia de seguridad
* Eliminación de una directiva de copia de seguridad

## <a name="create-a-backup-policy"></a>Creación de una directiva de copia de seguridad
Use el procedimiento siguiente para crear una nueva directiva de copia de seguridad.

#### <a name="to-create-a-backup-policy"></a>Para crear una directiva de copia de seguridad
1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.
2. En el panel **Ámbito**, haga clic con el botón derecho en **Directivas de copia de seguridad** y haga clic en **Crear directiva de copia de seguridad**.

    ![Creación de una directiva de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Aparecerá el cuadro de diálogo **Crear una directiva** .

    ![Creación de una directiva - pestaña General](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. En la pestaña **General** , complete la siguiente información:

   1. En el cuadro de texto **Nombre** , escriba un nombre para la directiva.
   2. En el cuadro de texto **Grupo de volúmenes** , escriba el nombre del grupo de volúmenes asociado con la directiva.
   3. Seleccione **Instantánea local** o **Instantánea de nube**.
   4. Seleccione el número de instantáneas que desea retener. Si selecciona **Todas**, se retendrán 64 instantáneas (el número máximo).
4. Haga clic en la pestaña **Programar** .

    ![Creación de una directiva - pestaña Programación](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. En la pestaña **Programación** , complete la siguiente información:

   1. Haga clic en la casilla **Habilitar** para programar la siguiente copia de seguridad.
   2. En **Configuración**, seleccione **Una vez**, **Diario**, **Semanal** o **Mensual**.
   3. En el cuadro de texto **Inicio** , haga clic en el icono de calendario y seleccione una fecha de inicio.
   4. En **Configuración avanzada**, puede establecer programaciones de repetición opcionales y una fecha de finalización.
   5. Haga clic en **Aceptar**.

Después de crear una directiva de copia de seguridad, aparece la siguiente información en el panel **Resultados** :

* **Nombre** : nombre de la directiva de copia de seguridad.
* **Tipo** : instantánea local o instantánea de nube.
* **Grupo de volúmenes** : grupo de volúmenes asociado a la directiva.
* **Retención** : número de instantáneas retenidas; el número máximo es 64.
* **Creado** : fecha en que se creó esta directiva.
* **Habilitado**: si la directiva está actualmente en vigor. **True** indica que está en vigor; **False** indica que no está en vigor.

## <a name="edit-a-backup-policy"></a>Edición de una directiva de copia de seguridad
Use el procedimiento siguiente para editar una directiva de copia de seguridad existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar una directiva de copia de seguridad
1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.
2. En el panel **Ámbito**, haga clic en el nodo **Directivas de copia de seguridad**. Todas las directivas de copia de seguridad aparecen en el panel **Resultados** .
3. Haga clic con el botón derecho en la directiva que desee editar y después haga clic en **Editar**.

    ![Edición de una directiva de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Cuando aparezca la ventana **Crear una directiva**, escriba los cambios y, después, haga clic en **Aceptar**.

## <a name="delete-a-backup-policy"></a>Eliminación de una directiva de copia de seguridad
Use el procedimiento siguiente para eliminar una directiva de copia de seguridad.

#### <a name="to-delete-a-backup-policy"></a>Para eliminar una directiva de copia de seguridad
1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.
2. En el panel **Ámbito**, haga clic en el nodo **Directivas de copia de seguridad**. Todas las directivas de copia de seguridad aparecen en el panel **Resultados** .
3. Haga clic con el botón derecho en la directiva de copia de seguridad que desee eliminar y después haga clic en **Eliminar**.
4. Cuando aparezca el mensaje de confirmación, haga clic en **Sí**.

    ![Eliminación de la confirmación de una directiva de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre el [uso de Snapshot Manager de StorSimple para administrar la solución de StorSimple](storsimple-snapshot-manager-admin.md).
* Obtenga más información acerca de [uso de Snapshot Manager de StorSimple para ver y administrar trabajos de copia de seguridad](storsimple-snapshot-manager-manage-backup-jobs.md).
