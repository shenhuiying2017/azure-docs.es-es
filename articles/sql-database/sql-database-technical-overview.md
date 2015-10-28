<properties
   pageTitle="¿Qué es la Base de datos SQL | Microsoft Azure"
   description="Descubra los detalles técnicos y las capacidades de Base de datos SQL de Azure, el sistema de administración de bases de datos relacionales (RDBMS) de Microsoft y la solución de PaaS en la nube."
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
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# Introducción a Base de datos SQL

Base de datos SQL es un servicio de bases de datos relacionales en la nube basado en el motor de Microsoft SQL Server líder del mercado, con capacidades críticas. Base de datos SQL ofrece un rendimiento predecible, escalabilidad sin tiempo de inactividad, continuidad empresarial y protección de datos (todo ello casi sin administración). Se puede centrar en el desarrollo rápido de aplicaciones y en reducir el plazo de comercialización, en lugar de administrar las máquinas virtuales y la infraestructura. Al basarse en el motor [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), Base de datos SQL admite las herramientas, bibliotecas y API de SQL Server existentes, lo que facilita el proceso del paso a la nube.

Este artículo presenta los principales conceptos y características de Base de datos SQL relacionadas con el rendimiento, escalabilidad y facilidad de uso, con vínculos para explorar los detalles. Si está listo para dar el salto, puede [crear la primera base de datos SQL](sql-database-get-started.md) o [crear un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md) en cuestión de minutos. Si desea un análisis más profundo, vea este vídeo de 30 minutos.


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## Ajuste del rendimiento y escalabilidad sin tiempo de inactividad
Las bases de datos SQL están disponibles en los *niveles de servicio* Básico, Estándar y Premium. Cada nivel de servicio ofrece [diferentes niveles de rendimiento y capacidades](sql-database-service-tiers.md) para admitir todos los tipos de cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y luego [cambiar el nivel de servicio](sql-database-scale-up.md) manualmente o mediante programación en cualquier momento cuando la aplicación se haga viral en todo el mundo, sin tiempo de inactividad para la aplicación o sus clientes.

Para muchas empresas y aplicaciones, poder crear bases de datos y aumentar o reducir el rendimiento de una base de datos única a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio.

Los [grupos de bases de datos elásticas](sql-database-elastic-pool.md) de Base de datos SQL solucionan este problema. El concepto es sencillo. Se asigna el rendimiento a un grupo y se paga por el rendimiento colectivo del grupo, en lugar de por el de una base de datos única. No es preciso aumentar o reducir el rendimiento de la base de datos. Las bases de datos del grupo, denominadas *bases de datos elásticas*, se escalan o reducen verticalmente para satisfacer la demanda. Las bases de datos elásticas consumen, pero no superan los límites del grupo, por lo que el costo sigue siendo predecible, aunque su uso no lo sea. Es más, puede [agregar bases de datos al grupo y quitarlas del mismo](sql-database-elastic-pool-portal.md), lo que escala la aplicación de un puñado de bases de datos a miles, y todo sin salirse del presupuesto que controla.

Independientemente de lo que elija (única o elásticas), no se quedará bloqueado. Puede fusionar bases de datos únicas con grupos de bases de datos elásticas y cambiar los niveles de servicio tanto de las bases de datos únicas como de los grupos para crear diseños innovadores. Además, con la potencia y el alcance de Azure, puede combinar y hacer coincidir los servicios de Azure con Base de datos SQL para satisfacer sus necesidades únicas de diseño de aplicaciones modernas, impulsar las eficiencias de costos y recursos, y desbloquear nuevas oportunidades de negocio.

Pero, ¿cómo se puede comparar el rendimiento relativo de las bases de datos y los grupos de bases de datos? ¿Cómo se sabe el momento correcto en el que debe hacer clic para detenerse al subir y bajar por el dial? La respuesta es la unidad de transacción de base de datos (DTU) para bases de datos únicas y la DTU elástica (eDTU) para bases de datos elásticas y grupos de bases de datos.

## Descripción de las DTU

La unidad de transacción de base de datos (DTU) es la unidad de medida en Base de datos SQL que representa la potencia relativa de bases de datos en base a una medida del mundo real: la transacción de base de datos. Usamos un conjunto de operaciones que son típicas de una solicitud de procesamiento de transacciones en línea (OLTP) y después medimos cuántas transacciones se podrían completar por segundo en condiciones de carga total (esta es la versión resumida; puede leer los detalles en [formación general de la prueba comparativa](https://msdn.microsoft.com/library/azure/dn741327.aspx)).

Una base de datos de tipo Básica tiene 5 DTU, lo que significa que puede completar 5 transacciones por segundo, mientras que una base de datos de tipo Premium P11 tiene 1750 DTU.

![DTU de base de datos única](./media/sql-database-technical-overview/single_db_dtus.png)

DTU para bases de datos únicas se convierte directamente en eDTU para bases de datos elásticas. Por ejemplo, una base de datos de un grupo de bases de datos elásticas de tipo Básico ofrece hasta 5 eDTU. Es el mismo rendimiento que una base de datos única de tipo Básica. La diferencia es que la base de datos flexible no consumirá ninguna eDTU del grupo hasta que tenga que hacerlo.

![Grupos de elásticas y eDTU](./media/sql-database-technical-overview/sqldb_elastic_pools.png)

Un ejemplo sencillo ayuda. Use un grupo de bases de datos elásticas de tipo Básico con 1000 DTU y coloque 800 bases de datos en él. Siempre y cuando se usen solamente 200 de las 800 bases de datos en un momento dado (5 DTU X 200 = 1000), no se alcanzará la capacidad del grupo y el rendimiento de la base de datos no se degradará. Este es un ejemplo simplificado para mayor claridad. Los cálculos matemáticos reales son un poco más complicados. El portal realiza los cálculos matemáticos y hace una recomendación basándose en el uso histórico de la base de datos. Vea [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para saber cómo funcionan las recomendaciones o para realizar los cálculos matemáticos por su cuenta.

## Mantenimiento de la aplicación y el negocio en funcionamiento

El contrato de nivel de servicio [(SLA)](http://azure.microsoft.com/support/legal/sla/) de disponibilidad del 99,99% líder de la industria de Azure, con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada base de datos SQL, aprovecha las ventajas de protección de datos integrada, tolerancia a errores y protección de datos, algo que de lo contrario tendría que diseñar, adquirir, compilar y administrar. Aun así, en función de las exigencias de su negocio, puede solicitar capas de protección adicionales para asegurarse de que tanto la aplicación como el negocio se pueden recuperar rápidamente en caso de desastre, error o cualquier otro contratiempo grave. Con Base de datos de SQL, cada nivel de servicio ofrece un menú de características diferente que se puede usar para ponerse en funcionamiento. Puede usar la restauración en un momento dado para devolver una base de datos a un estado anterior, con un plazo máximo de 35 días. Además, si el centro de datos que hospeda las bases de datos sufre una interrupción, es posible conmutar a las réplicas de base de datos de otra región. O bien, se pueden usar réplicas para las actualizaciones o la reubicación en otras regiones.

![Replicación geográfica de Base de datos SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Para obtener más información sobre las diferentes características de continuidad del negocio disponibles para los distintos niveles de servicio, vea [Continuidad empresarial](sql-database-business-continuity.md).

## Protección de los datos
SQL Server tradicionalmente ha proporcionado una sólida seguridad de datos que Base de datos SQL respeta con sus características que limitan el acceso, protegen los datos y ayudan a supervisar la actividad. Para ver un resumen rápido de las opciones de seguridad que tiene Base de datos SQL, vea [Protección de bases de datos SQL](sql-database-security.md). Para obtener una vista más completa de las características de seguridad, vea [Centro de seguridad para el motor de base de datos SQL Server y Base de datos SQL](https://msdn.microsoft.com/library/bb510589). Y para obtener información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](http://azure.microsoft.com/support/trust-center/security/).

## Pasos siguientes

- Vea la [página de precios](http://azure.microsoft.com/pricing/details/sql-database/) de una base de datos única y los precios y calculadoras de bases de datos elásticas.

- Comience por [crear su primera base de datos](sql-database-get-started.md). Luego cree su primera aplicación en [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) o [Ruby](sql-database-develop-ruby-simple-linux).

<!---HONumber=Oct15_HO3-->