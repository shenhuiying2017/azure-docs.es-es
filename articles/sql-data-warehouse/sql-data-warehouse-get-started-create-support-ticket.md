<properties
   pageTitle="Creación de una incidencia de soporte técnico para Almacenamiento de datos SQL | Microsoft Azure"
   description="Creación de una incidencia de soporte técnico en Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="sonyam;barbkess;sonyama"/>

# Creación de una incidencia de soporte técnico para Almacenamiento de datos SQL
 
Si tiene algún problema con su instancia de Almacenamiento de datos SQL, cree una incidencia de soporte técnico para que nuestro equipo de ingenieros pueda ayudarle.

## Creación de una incidencia de soporte técnico

1. Abra el [Portal de Azure][].

2. En la pantalla de inicio, haga clic en el icono **Ayuda y soporte técnico**.

    ![Ayuda y soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. En la hoja Ayuda y soporte técnico, haga clic en **Crear una solicitud de soporte técnico**.

    ![Nueva solicitud de soporte](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a>

4. Seleccione el **Tipo de solicitud**.

    ![Tipo de solicitud](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  De forma predeterminada, cada servidor SQL (por ejemplo, myserver.database.windows.net) tiene una **Cuota de DTU** de 45.000. Esta cuota es simplemente un límite de seguridad. Puede aumentar la cuota creando una incidencia de soporte técnico y seleccionando *Cuota* como el tipo de solicitud. Para calcular sus necesidades de DTU, tiene que saber que cada 100 [DWU][] de Almacenamiento de datos SQL consume 750 DTU. Por lo tanto, la cuota predeterminada de 45.000 le permitirá crear hasta un valor DW6000 (45.000 / 750) o varias bases de datos más pequeñas con menos DWU. Si, por ejemplo, desea hospedar dos DW6000s en un servidor SQL, debe solicitar una cuota DTU de 90.000. Puede ver el consumo de DTU actual en la hoja del servidor SQL en el portal. El recuento de las bases de datos, tanto en pausa como continuo, hacia la cuota de DTU.

5. Seleccione la **Suscripción** que hospeda la base de datos con el problema sobre el que desea informar.

    ![La suscripción](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Seleccione **Almacenamiento de datos SQL** como Recurso.

    ![Recurso](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Seleccione su [Plan de soporte técnico de Azure][].

    - En todos los niveles de soporte técnico se proporciona asistencia para problemas relacionados con la ** facturación, las cuotas y la administración de suscripciones**.
    - El soporte técnico para problemas de tipo **break-fix** se proporciona a través de los niveles [Developer][], [Standard][], [Professional Direct][] o [Premier][]. Los problemas "break-fix" son aquellos que experimentan los clientes mientras usan Azure en los que hay una posibilidad razonable de que hayan sido causados por Microsoft.
    - El **aprendizaje para desarrolladores** y los **servicios de asesoramiento** están disponibles en los niveles de soporte técnico [Professional Direct][] y [Premier][].
    
    Si tiene un plan de asistencia Premier, también puede informar sobre problemas relacionados con Almacenamiento de datos SQL en el [portal Microsoft Premier Online][]. Consulte [Soporte técnico de Azure para clientes][Azure support plan] para más información sobre los diversos planes de soporte técnico, incluyendo detalles como ámbito, tiempos de respuesta, precios, etc. Si desea ver las preguntas más frecuentes sobre el soporte técnico de Azure, consulte [Preguntas más frecuentes de soporte técnico de Azure][].

    ![Plan de soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Seleccione **Tipo de problema** y **Categoría**.

    ![Categoría del tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Describa el problema y elija el nivel de impacto empresarial.

    ![Descripción del problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Su **información de contacto** para esta incidencia de soporte técnico se rellenará automáticamente. Actualice si es necesario.

    ![Información de contacto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Haga clic en **Crear** para enviar la solicitud de soporte técnico.


## Supervisión de una incidencia de soporte técnico

Una vez que ha enviado la solicitud de asistencia, el equipo de soporte técnico de Azure se pondrá en contacto con usted. Para comprobar el estado de la solicitud y los detalles, haga clic en **Administrar solicitudes de soporte técnico** en el panel.

![Comprobar estado](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## Otros recursos

Además, puede conectar con la comunidad de Almacenamiento de datos SQL en [Stack Overflow][] o en el [foro de MSDN de Almacenamiento de datos SQL de Azure][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portal de Azure]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/
[Plan de soporte técnico de Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/
[Developer]: https://azure.microsoft.com/support/plans/developer/
[Standard]: https://azure.microsoft.com/support/plans/standard/
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/
[Premier]: https://azure.microsoft.com/support/plans/premier/
[Preguntas más frecuentes de soporte técnico de Azure]: https://azure.microsoft.com/support/faq/
[portal Microsoft Premier Online]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[foro de MSDN de Almacenamiento de datos SQL de Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

<!---HONumber=AcomDC_0720_2016-->