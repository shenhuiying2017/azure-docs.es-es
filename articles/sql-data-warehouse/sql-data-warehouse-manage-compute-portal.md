<properties
   pageTitle="Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (Portal de Azure) | Microsoft Azure"
   description="Tareas del Portal de Azure para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos."
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
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (Portal de Azure)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-manage-compute.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Escale el rendimiento mediante el escalado horizontal de los recursos de proceso y la memoria para satisfacer las necesidades cambiantes de la carga de trabajo. Ahorre costes reduciendo el escalado de los recursos fuera de horas punta o pausando el proceso por completo.

Esta colección de tareas usa el Portal de Azure para:

- Escalado de proceso
- Pausa del proceso
- Reanudación del proceso

Para más información, consulte [Manage compute power overview][] (Administración de la potencia de proceso (información general)).

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Escalado de la potencia de proceso

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

1. Para suspender los recursos de proceso y memoria, haga clic en **Pausar**. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Confirme la pausa][7]

1. Mientras Almacenamiento de datos SQL está iniciando la base de datos, el estado será "En pausa".
2. Cuando el estado sea **En pausa**, se realizará la operación de pausa y ya no se le cobrará por DWU.

    ![Estado de pausa][4]

<a name="resume-compute-bk"></a>

## Reanudación del proceso

[AZURE.INCLUDE [SQL Data Warehouse resume description (Descripción de reanudación de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-resume-description.md)]Para reanudar una base de datos:

1. Abra el [Portal de Azure][] y abra la base de datos. Tenga en cuenta que el estado sea **En pausa**. 

    ![Base de datos de pausa][4]

1. Para reanudar la base de datos, haga clic en **Iniciar**. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para confirmar o **No** para cancelar.

    ![Confirme la reanudación][5]

1. Mientras Almacenamiento de datos SQL está iniciando la base de datos, el estado será "Reanudando".
2. Cuando el estado sea **En línea**, la base de datos estará lista.

    ![Estado En línea][6]

<a name="next-steps-bk"></a>

## Pasos siguientes
Para más información, consulte [Información general de administración][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-overview-manage-compute.md

<!--MSDN references-->


<!--Other Web references-->

[Portal de Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->