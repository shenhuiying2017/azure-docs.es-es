<properties 
   pageTitle="Recomendaciones sobre el nivel de precios de Base de datos SQL de Azure" 
   description="Si se cambian los niveles de precios en el Portal de Azure, se recomienda el nivel más apropiado para ejecutar la carga de trabajo de una Base de datos SQL de Azure existente." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="05/18/2015"
   ms.author="sstein"/>

# Recomendaciones sobre el nivel de precios de Base de datos SQL

 Si se cambian los niveles de precios en el Portal de Azure, se recomienda el nivel más apropiado para ejecutar la carga de trabajo de una Base de datos SQL de Azure existente.

> [AZURE.NOTE]Las recomendaciones sobre el nivel de precios solo están disponibles para las bases de datos Web y Business, y solo en el [Portal de Azure](https://portal.azure.com/).


## Información general

Azure analiza el rendimiento actual y los requisitos de características mediante la evaluación del uso de recursos históricos para Base de datos SQL. Además, el nivel de servicio mínimo aceptable se determina en función del tamaño de la base de datos y las características de [continuidad del negocio](https://msdn.microsoft.com/library/azure/hh852669.aspx) habilitadas.

Esta información se analiza y se recomienda el nivel de servicio y el nivel de rendimiento que mejor se adapte para ejecutar la carga de trabajo típica de la base de datos y para mantener el conjunto de características actual.

- El servicio examina entre 15 a 30 días anteriores de datos históricos (uso de recursos, tamaño de base de datos y actividad de la base de datos) y realiza una comparación entre la cantidad de recursos consumidos y las limitaciones reales de los niveles de servicio disponibles y los niveles de rendimiento.
- Los datos se analizan en intervalos de 15 segundos y se clasifica el conjunto de resultados de cada intervalo en el nivel de servicio existente y nivel de rendimiento que sean más adecuados para controlar la carga de trabajo de ese conjunto de resultados.
- Después, estos ejemplos de 15 segundos se agregan en el análisis más extenso de 15 a 30 días y se recomienda el nivel de servicio y el del rendimiento que pueda manejar de manera óptima el 95% de la carga de trabajo histórica.

### Recomendaciones

Según el uso de la base de datos, se pueden encontrar dos categorías de recomendaciones:+


| Recomendación | Descripción |
| :--- | :--- |
| Actualizar | Actualice a un nuevo nivel. |
| No disponible | Las bases de datos requieren una carga de trabajo mínima o aproximadamente 14 días de actividad. No hay suficientes datos para proporcionar una recomendación válida. |

## Obtención de recomendaciones de nivel de precios

Para obtener recomendaciones sobre los niveles de precios, seleccione  una base de datos Web o Business existente y haga clic en el icono **Nivel de precios**.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **EXAMINAR** en el menú de la izquierda.
3. Haga clic en **Bases de datos SQL** en la hoja **Examinar**.
4. En la hoja **Bases de datos SQL**, haga clic en la base de datos en la que desea que se analice el servicio.

    ![Seleccionar base de datos][1]

5. En la hoja de la base de datos, seleccione el icono **Nivel de precios**.

    ![Nivel de precios][2]


7. Tras hacer clic en el icono **Nivel de precios**, aparecerá con la hoja **Niveles de precios recomendados** donde puede haga clic en el nivel sugerido y después haga clic en el botón **Seleccionar** para cambiar a ese nivel.

    ![Suscríbase a la vista previa][4]

8. Opcionalmente, haga clic en **Ver detalles de uso** para abrir la hoja **Detalles de recomendaciones de niveles de precios** donde puede ver el nivel recomendado para la base de datos, una comparación de características entre los niveles actuales y recomendados y un gráfico del análisis de uso de recursos históricos.

    ![Suscríbase a la vista previa][5]



## Resumen

Las recomendaciones de niveles de precios proporcionan una experiencia automatizada para recopilar datos de telemetría para cada Base de datos SQL y para recomendar la combinación del mejor nivel de servicio/nivel de rendimiento según los requisitos de las características y necesidades de rendimiento real de una base de datos. Haga clic en el icono **Nivel de precios** de una hoja de base de datos para ver las recomendaciones relativas al nivel de precios.



## Pasos siguientes

Dependiendo de los detalles de la base de datos específica, la realización de una actualización a un nivel de servicio superior o inferior no se suele producir de manera instantánea. El Portal de administración proporcionará notificaciones cuando la base de datos realice la transición a su nuevo nivel, o puede supervisar el estado de actualización mediante la consulta de la vista [sys.dm_operation_status (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/dn270022.aspx) en la base de datos maestra de la base de datos de SQL Server.


<!--Image references-->
[1]: ./media/sql-database-pricing-tier-recommendations/select-database.png
[2]: ./media/sql-database-pricing-tier-recommendations/pricing-tier.png
[3]: ./media/sql-database-pricing-tier-recommendations/preview-sign-up.png
[4]: ./media/sql-database-pricing-tier-recommendations/choose-pricing-tier.png
[5]: ./media/sql-database-pricing-tier-recommendations/usage-details.png

<!---HONumber=58--> 