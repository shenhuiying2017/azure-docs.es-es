---
title: "Creación de una incidencia de soporte técnico para SQL Data Warehouse | Microsoft Docs"
description: "Creación de una incidencia de soporte técnico en Almacenamiento de datos SQL de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 058ff1229acee5d03db7c0305c5565ae95a85758
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Creación de una incidencia de soporte técnico para Almacenamiento de datos SQL
Si tiene algún problema con su instancia de SQL Data Warehouse, cree una incidencia de soporte técnico para que nuestro equipo de ingenieros pueda ayudarle.

> [!NOTE] 
> A partir del 20/12/2016, la comprobación de estado de los recursos en Azure Portal no es exacta. Estamos trabajando activamente para solucionar este problema. 


## <a name="create-a-support-ticket"></a>Creación de una incidencia de soporte técnico
1. Abra [Azure Portal][Azure portal].
2. En la pantalla de inicio, haga clic en el icono **Ayuda y soporte técnico** .
   
    ![Ayuda y soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. En la hoja Ayuda y soporte técnico, haga clic en **Crear una solicitud de soporte técnico**.
   
    ![Nueva solicitud de soporte](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. Seleccione el **Tipo de solicitud**.
   
    ![Tipo de solicitud](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > De forma predeterminada, cada servidor SQL (por ejemplo, myserver.database.windows.net) tiene una **Cuota de DTU** de 45.000. Esta cuota es simplemente un límite de seguridad. Puede aumentar la cuota creando una incidencia de soporte técnico y seleccionando *Cuota* como el tipo de solicitud. Para calcular las necesidades de DTU, multiplique 7,5 por el total de [DWU][DWU] necesario. Por ejemplo, desea hospedar dos DW6000s en un servidor SQL, así que debe solicitar una cuota de DTU de 90 000.  Puede ver el consumo de DTU actual en la hoja del servidor SQL en el portal. El recuento de las bases de datos, tanto en pausa como continuo, hacia la cuota de DTU. 
   > 
   > 
5. Seleccione la **suscripción** que hospeda la base de datos con el problema que va a notificar.
   
    ![suscripción](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Seleccione **Almacenamiento de datos SQL** como Recurso.
   
    ![Recurso](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. Seleccione su [Plan de soporte técnico de Azure][Azure support plan].
   
   * **facturación, las cuotas y la administración de suscripciones** .
   * El soporte técnico para problemas de tipo **Break-fix** se proporciona a través de los niveles [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct] o [Premier][Premier]. Los problemas "break-fix" son aquellos que experimentan los clientes mientras usan Azure en los que hay una posibilidad razonable de que hayan sido causados por Microsoft.
   * El **aprendizaje para desarrolladores** y los **servicios de asesoramiento** están disponibles en los niveles de soporte técnico [Professional Direct][Professional Direct] y [Premier][Premier]. 
     
     Si tiene un plan de soporte técnico Premier, también puede informar sobre problemas relacionados con SQL Data Warehouse en el [portal Microsoft Premier Online][Microsoft Premier online portal].  Consulte [Soporte técnico de Azure][Azure support plan] para clientes para más información sobre los diversos planes de soporte técnico, incluyendo detalles como ámbito, tiempos de respuesta, precios, etc.  Si desea ver las preguntas más frecuentes sobre el soporte técnico de Azure, consulte [Preguntas más frecuentes de soporte técnico de Azure][Azure support FAQs].  
     
     ![Plan de soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. Seleccionar el **tipo de problema** y la **categoría**. En este ejemplo, se ha elegido "Herramientas" como el tipo de problema y "Herramientas de cliente" como la categoría. 
   
    ![Categoría del tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Describa el problema y elija el nivel de impacto empresarial.
   
    ![Descripción del problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. Su **información de contacto** para esta incidencia de soporte técnico se rellenará automáticamente. Actualice si es necesario.
    
    ![Información de contacto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Haga clic en **Crear** para enviar la solicitud de soporte técnico.

## <a name="monitor-a-support-ticket"></a>Supervisión de una incidencia de soporte técnico
Una vez que ha enviado la solicitud de asistencia, el equipo de soporte técnico de Azure se pondrá en contacto con usted. Para comprobar el estado de la solicitud y los detalles, haga clic en **Administrar solicitudes de soporte técnico** en el panel.

![Comprobar estado](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Otros recursos
Además, puede conectar con la comunidad de SQL Data Warehouse en [Stack Overflow][Stack Overflow] o en el [foro de MSDN de Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

