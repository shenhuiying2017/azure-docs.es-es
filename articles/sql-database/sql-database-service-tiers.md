<properties
   pageTitle="Niveles de servicio de Base de datos SQL"
   description="Compare las características de rendimiento y de continuidad del negocio de los niveles de servicio de Base de datos SQL de Azure para encontrar el equilibrio adecuado entre costo y funcionalidad a medida que escalan a petición sin tiempo de inactividad."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="04/15/2015"
   ms.author="shkurhek"/>

# Niveles de servicio

[Base de datos SQL](sql-database-technical-overview.md) está disponible en los niveles de servicio Basic, Standard y Premium. Los tres niveles de servicio tienen un [SLA](http://azure.microsoft.com/support/legal/sla/) de tiempo de actividad del 99,99% y ofrecen un rendimiento predecible, opciones de continuidad del negocio flexibles, características de seguridad y facturación por hora. Con varios niveles de rendimiento dentro de cada nivel de servicio, tiene la flexibilidad de elegir el nivel que mejor se adapte a las exigencias de la carga de trabajo. Si necesita escalar vertical u horizontalmente, puede cambiar fácilmente los niveles de la base de datos en el Portal de Azure sin que haya tiempo de inactividad en la aplicación. Para obtener información más detallada, consulte [Modificación de niveles de servicio y de rendimiento de la base de datos](https://msdn.microsoft.com/library/azure/dn369872.aspx).

> [AZURE.IMPORTANT]Retirada de las ediciones Web y Business. Descubra cómo [actualizar las ediciones Web y Business](sql-database-upgrade-new-service-tiers.md). Si piensa seguir usando las ediciones Web y Business, lea [P+F de Sunset](http://azure.microsoft.com/pricing/details/sql-database/web-business/).

## Básica

El nivel Basic está diseñado para las aplicaciones con poca carga de trabajo transaccional. Un caso de uso típico es una aplicación ligera que necesita una base de datos pequeña con una única operación en un momento dado.

**Rendimiento, tamaño y características**


| Nivel de servicio | Básica |
| :-- | :-- |
| Unidades de rendimiento de base de datos (DTU) | 5 |
| Tamaño máximo de la base de datos | 2 GB |
| Restauración a un momento dado (PITR) | Hasta el milisegundo en los últimos 7 días |
| Recuperación ante desastres | Restauración geográfica, restaurar en cualquier región de Azure |
| Objetivos de rendimiento | Tasa de transacción por hora |


## Standard

El nivel Standard es la opción predilecta para empezar a trabajar con cargas de trabajo transaccionales. Ofrece un mejor rendimiento y mejores características de continuidad de negocio integrados en comparación con el nivel Basic. Un caso de uso típico es una aplicación con varias transacciones simultáneas.

**Rendimiento y tamaño**


| Nivel de servicio | S0 Standard | S1 Standard | S2 Standard | S3 Standard |
| :-- | :-- | :-- | :-- | :-- |
| Unidades de rendimiento de base de datos (DTU) | 10 | 20 | | 50 | 100 |
| Tamaño máximo de la base de datos | 250 GB | 250 GB | 250 GB | 250 GB |


**Características**


| Nivel de servicio | Standard (S0, S1, S2, S3) |
| :-- | :-- |
| Restauración a un momento dado (PITR) | Hasta el milisegundo en los últimos 14 días |
| Recuperación ante desastres | Replicación geográfica estándar, 1 secundaria sin conexión |
| Objetivos de rendimiento | Tasa de transacción por minuto |


## Premium

El nivel Premium está diseñado para aplicaciones críticas. Ofrece el mejor nivel de rendimiento y acceso a características avanzadas de continuidad empresarial, que incluyen la replicación geográfica activa, en un máximo de 4 regiones de Azure que elija. Un caso de uso típico es una aplicación con alto volumen transaccional y muchos usuarios simultáneos.

**Rendimiento y tamaño**


| Nivel de servicio | Premium P1 | Premium P2 | Premium P3 |
| :-- | :-- | :-- | :-- |
| Unidades de rendimiento de base de datos (DTU) | 125 | 250 | 1000 |
| Tamaño máximo de la base de datos | 500 GB | 500 GB | 500 GB |


**Características**


| Nivel de servicio | Premium (P1, P2, P3) |
| :-- | :-- |
| Restauración a un momento dado (PITR) | Hasta el milisegundo en los últimos 35 días |
| Recuperación ante desastres | Replicación geográfica activa, hasta 4 secundarias legibles en línea |
| Objetivos de rendimiento | Tasa de transacción por segundo |


Para obtener más información sobre los precios de estos niveles, consulte [Precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).

Ahora que conoce los niveles de Base de datos SQL, pruébelos con una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera base de datos SQL](sql-database-get-started.md).
 

<!---HONumber=July15_HO2-->