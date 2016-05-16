<properties
   pageTitle="Administración de tareas de escalabilidad de Almacenamiento de datos SQL de Azure (Portal de Azure) | Microsoft Azure"
   description="Tareas del Portal de Azure para escalar el rendimiento de Almacenamiento de datos SQL de Azure Cambiar recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Administración de tareas de escalabilidad de Almacenamiento de datos SQL de Azure (Portal de Azure)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-scalability.md)
- [Portal](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Escale horizontalmente con total flexibilidad memoria y recursos de proceso para satisfacer las demandas de cambio de su carga de trabajo y ahorre costos reduciendo recursos durante las horas de poca actividad.

Esta colección de tareas usa el Portal de Azure para:

- Escalar el rendimiento mediante el ajuste de DWU
- Pausar recursos de proceso
- Reanudar recursos de proceso

Para obtener más información al respecto, consulte [Información general sobre la escalabilidad de rendimiento][].

<a name="scale-performance-bk"></a>

## Rendimiento a escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description (Descripción de escalado de DWU de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar los recursos de proceso:

1. Abra el [Portal de Azure][], abra la base de datos y haga clic en **Escalar**.

    ![Haga clic en Escala][1]

1. En la hoja Escalar, mueva el control deslizante izquierdo o derecho para cambiar el valor de DWU.

    ![Mueva el control deslizante][2]

1. Haga clic en **Guardar**. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Haga clic en Guardar][3]

<a name="pause-compute-bk"></a>

## Pausa del proceso

[AZURE.INCLUDE [SQL Data Warehouse pause description (Descripción de pausa de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos:

1. Abra el [Portal de Azure][] y abra la base de datos. Tenga en cuenta que el estado sea **En línea**. 

    ![Estado En línea][6]

1. Para suspender los recursos de proceso y memoria, haga clic en **Pausar**, y, a continuación, aparecerá un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Confirme la pausa][7]

1. Mientras Almacenamiento de datos SQL está iniciando la base de datos, el estado será "En pausa".
2. Cuando el estado sea **En pausa**, se realizará la operación de pausa y ya no se le cobrará por DWU.

    ![Estado de pausa][4]

<a name="resume-compute-bk"></a>

## Reanudación del proceso

[AZURE.INCLUDE [SQL Data Warehouse resume description (Descripción de reanudación de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-resume-description.md)]Para reanudar una base de datos:

1. Abra el [Portal de Azure][] y abra la base de datos. Tenga en cuenta que el estado sea **En pausa**. 

    ![Base de datos de pausa][4]

1. Para reanudar la base de datos, haga clic en **Iniciar** y, a continuación, aparecerá un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Confirme la reanudación][5]

1. Mientras Almacenamiento de datos SQL está iniciando la base de datos, el estado será "Reanudando".
2. Cuando el estado sea **En línea**, la base de datos estará lista.

    ![Estado En línea][6]

<a name="next-steps-bk"></a>

## Pasos siguientes
Para obtener más información, consulte [Introducción a la administración][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[Introducción a la administración]: ./sql-data-warehouse-overview-manage.md
[Información general sobre la escalabilidad de rendimiento]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[Portal de Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->