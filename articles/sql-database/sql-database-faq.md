---
title: Preguntas más frecuentes sobre Azure SQL Database | Microsoft Docs
description: Respuestas a las preguntas más comunes que los clientes preguntan sobre las bases de datos en la nube y Azure SQL Database, el sistema de administración de bases de datos relacionales (RDBMS) de Microsoft y bases de datos como un servicio en la nube.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 5d77c2dc121d7f291fa755f66d7d9a5d7213bf9f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195437"
---
# <a name="sql-database-faq"></a>Preguntas más frecuentes sobre la SQL Database

## <a name="what-is-the-current-version-of-sql-database"></a>¿Cuál es la versión actual de SQL Database?
La versión actual de SQL Database es V12. Se ha retirado la versión V11.

## <a name="what-is-the-sla-for-sql-database"></a>¿Qué es el Acuerdo de Nivel de Servicio para SQL Database?
Garantizamos la conectividad en el 99,99 % de las ocasiones, como mínimo, entre su instancia de Microsoft Azure SQL Database y nuestra puerta de enlace de Internet, con independencia del nivel de servicio. Para más información, consulte [Acuerdo de Nivel de Servicio](http://azure.microsoft.com/support/legal/sla/).

## <a name="whatis-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>¿Qué es el nuevo modelo de compra basado en núcleos virtuales (versión preliminar) de Azure SQL Database?

El nuevo modelo de compra se suma al modelo basado en DTU ya existente. El modelo basado en núcleos virtuales está diseñado para proporcionar a los clientes flexibilidad, control, transparencia y una manera sencilla de trasladar los requisitos de las cargas de trabajo locales a la nube. También permite a los clientes escalar los recursos de proceso y almacenamiento en función de las necesidades de la carga de trabajo. Es posible obtener un ahorro de hasta un 30 % en las opciones de bases de datos únicas y grupos elásticos que usan el modelo de núcleos virtuales mediante la [Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Para más información, consulte los artículos sobre el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) y el [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md). 

## <a name="what-is-a-vcore"></a>¿Qué es un núcleo virtual? 
Un núcleo virtual representa una CPU lógica que cuenta con una opción para elegir entre varias generaciones de hardware. Las CPU lógicas Gen 4 se basan en procesadores Intel E5-2673 v3 (Haswell) de 2,4 GHz y las CPU lógicas Gen 5 se basan en procesadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz.

## <a name="is-moving-to-the-vcore-based-model-required"></a>¿Es necesario migrar al modelo basado en núcleos virtuales?
No. La introducción del modelo basado en núcleos virtuales en las opciones de implementación de bases de datos únicas y grupos elásticos refleja nuestro compromiso de ofrecer opciones y flexibilidad al cliente. Si los clientes quieren seguir usando el modelo basado en DTU, este anuncio no implica nada para ellos y su experiencia y facturación seguirán siendo iguales. 

En muchos casos, las aplicaciones pueden beneficiarse de la simplicidad de una agrupación de recursos preconfigurada. Por lo tanto, seguimos ofreciendo y respaldando estas opciones basadas en DTU para nuestros clientes. Si las está usando y satisfacen sus requisitos empresariales, debe seguir haciéndolo.

Los modelos basados en DTU y en núcleos virtuales seguirán coexistiendo. Lanzamos el modelo basado en núcleos virtuales como respuesta a las solicitudes de los clientes de una mayor transparencia en torno a sus recursos de base de datos y la posibilidad de escalar los recursos de proceso y almacenamiento por separado. El modelo basado en núcleos virtuales también permite que los clientes con licencias activas de Software Assurance consigan un ahorro adicional gracias a la Ventaja híbrida de Azure para SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>¿Cómo debo elegir entre el modelo de compra basado en DTU y el modelo de compra basado en núcleos virtuales (versión preliminar)? 
La unidad de transacción de base de datos (DTU) se basa en una medida combinada de CPU, memoria, lecturas y escrituras. Los niveles de rendimiento basados en DTU representan agrupaciones preconfiguradas de recursos que controlan distintos niveles de rendimiento de la aplicación. Los clientes que no desean preocuparse por los recursos subyacentes y prefieren la simplicidad de una agrupación preconfigurada y pagar una cantidad mensual fija, pueden encontrar que el modelo basado en DTU se ajusta mejor a sus necesidades. Sin embargo, para clientes que necesitan información detallada de los recursos subyacentes o escalarlos de manera independiente para lograr un rendimiento óptimo, el modelo basado en núcleos virtuales será el más adecuado.  Además, si el cliente tiene una licencia activa de Software Assurance (SA) para SQL Server, puede aprovechar su inversión actual y ahorrar hasta un 30 % con la [Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Las opciones dentro de cada modelo de compra ofrecen las ventajas de un servicio administrado, tales como copias de seguridad automatizadas, revisiones y actualizaciones de software. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>¿Qué es la Ventaja híbrida de Azure para SQL Server? 
La [Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) ayuda a maximizar el valor de sus inversiones actuales en licencias y a acelerar el proceso de migración a la nube. Se trata de una ventaja basada en Azure que le permite usar las licencias de SQL Server con Software Assurance para pagar una tarifa reducida ("tasa base") en SQL Database. La Ventaja híbrida de Azure para SQL Server está disponible en la versión preliminar pública del modelo de compra basado en núcleos virtuales (versión preliminar) para bases de datos únicas y grupos elásticos de SQL Database. Puede solicitar esta ventaja aunque la SKU esté activa, pero tenga en cuenta que la tasa base se aplica desde el momento en que la seleccione en Azure Portal. No se emitirá ningún crédito con carácter retroactivo.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>¿Hay derechos de doble uso con Ventaja híbrida de Azure para SQL Server?
Dispone de 180 días de derechos de doble uso de la licencia para asegurarse de que las migraciones se ejecutan sin problemas. Transcurrido dicho período, la licencia de SQL Server solo puede usarse en la nube en SQL Database, y carece de derechos de doble uso en el entorno local y en la nube.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>¿En qué se diferencia la Ventaja híbrida de Azure para SQL Server de la movilidad de licencias?
En la actualidad, ofrecemos a los clientes de SQL Server las ventajas de Movilidad de licencias a través de Software Assurance, que permite la reasignación de sus licencias a servidores de terceros compartidos. Esta ventaja puede usarse en IaaS de Azure y AWS EC2.
La Ventaja híbrida de Azure para SQL Server se diferencia de la movilidad de licencias en dos áreas principales:
- Proporciona ventajas económicas para mover cargas de trabajo muy virtualizadas a Azure. Los clientes de SQL EE pueden obtener 4 núcleos en Azure en la SKU de uso general por cada núcleo que posean en el entorno local para aplicaciones muy virtualizadas. La movilidad de licencias no ofrece ninguna ventaja especial sobre los costos de mover cargas de trabajo virtualizadas a la nube.
- Se proporciona para destinos PaaS en Azure que son muy compatibles con SQL Server local, como Instancia administrada de SQL Database.

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>¿Cuáles son los derechos específicos de la Ventaja híbrida de Azure para SQL Server?
Los clientes de SQL Database tendrán asociados los siguientes derechos con la Ventaja híbrida de Azure para SQL Server:

|Superficie de licencia|¿Qué le permite obtener la Ventaja híbrida de Azure para SQL Server?|
|---|---|
|Clientes de núcleo de SQL Server Enterprise Edition con SA|<li>Puede pagar la tasa base sobre la SKU De uso general o Crítico para la empresa</li><br><li>1 núcleo local = 4 núcleos en la SKU De uso general</li><br><li>1 núcleo local = 1 núcleo en SKU Crítico para la empresa</li>|
|Clientes de núcleo de SQL Server Standard Edition con SA|<li>Puede pagar la tasa base solo sobre la SKU De uso general</li><br><li>1 núcleo local = 1 núcleo en la SKU De uso general</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>¿Está disponible el modelo basado en núcleos virtuales en Instancia administrada de SQL Database?
[Instancia administrada](sql-database-managed-instance.md) solo está disponible con el modelo basado en núcleos virtuales. Para más información, consulte la [página de precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>¿El costo de almacenamiento y proceso depende del nivel de servicio que se elija?
El costo de proceso refleja la capacidad de proceso total que se ha aprovisionado para la aplicación. En el nivel de servicio Crítico para la empresa, se asignan automáticamente al menos 3 réplicas Always On. Para reflejar esta asignación adicional de recursos de proceso, el precio del núcleo virtual es aproximadamente 2,7 veces más elevado en el nivel Crítico para la empresa. Por la misma razón, el precio de almacenamiento más alto por GB en el nivel Crítico para la empresa refleja la E/S alta y la baja latencia del almacenamiento SSD. Al mismo tiempo, el costo de almacenamiento de copia de seguridad no es diferente porque en ambos casos se usa una clase de almacenamiento estándar.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>¿Cómo se me cobra por el almacenamiento, en función de lo que configuro con antelación o de lo que usa la base de datos?
Diferentes tipos de almacenamiento se facturan de forma diferente. En el almacenamiento de datos, se le cobra por el almacenamiento aprovisionado en función del tamaño máximo de la base de datos o del grupo que seleccione. El costo no cambia a menos que reduzca o aumente ese máximo. El almacenamiento de copia de seguridad se asocia a las copias de seguridad automatizadas de su instancia. Al aumentar el período de retención de las copias de seguridad, aumenta el almacenamiento de copia de seguridad que consume la instancia. No hay cargos adicionales por el almacenamiento de copia de seguridad hasta el 100 % del total de su almacenamiento de servidor aprovisionado. El consumo adicional de almacenamiento de copia de seguridad se cobra en GB/mes. Por ejemplo, si tiene un tamaño de almacenamiento de base de datos de 100 GB, obtendrá 100 GB de almacenamiento de copia de seguridad sin costo adicional. Pero, si la copia de seguridad ocupa 110 GB, deberá pagar los 10 GB adicionales.

El almacenamiento de copia de seguridad de una única base de datos se le cobra mediante el prorrateo del almacenamiento que se asignó a las copias de seguridad de base de datos menos el tamaño de la base de datos. El almacenamiento de copia de seguridad de un grupo elástico se le cobra mediante el prorrateo del almacenamiento que se asignó a las copias de seguridad de base de datos de todas las bases de datos del grupo menos el tamaño máximo de los datos del grupo elástico. Cualquier aumento en el tamaño de la base de datos o el grupo elástico, o aumento en la velocidad de transacción, requiere más almacenamiento y, por tanto, aumenta la facturación del almacenamiento de copia de seguridad.  Al aumentar el tamaño máximo de los datos, esta cantidad nueva se deduce del tamaño de almacenamiento de copia de seguridad facturado.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>¿Cómo se selecciona la SKU adecuada al convertir una base de datos existente a los nuevos niveles de servicio? 
En las aplicaciones existentes de SQL Database que usan el modelo basado en DTU, el nivel de servicio De uso general es comparable con el nivel Estándar. El nivel de servicio Crítico para la empresa es comparable al nivel Premium. En ambos casos, debe asignar al menos 1 núcleo virtual por cada 100 DTU que use su aplicación en el modelo basado en DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>¿Ofrecen los nuevos niveles de servicio basados en núcleos virtuales los niveles de rendimiento compatibles con todos los objetivos de nivel de servicio (SLO) existentes?
Los nuevos niveles de servicio basados en núcleos virtuales ofrecen opciones de rendimiento comparables para todos los grupos elásticos y bases de datos que usan 100 o más DTU.  Con el tiempo, seguiremos agregando más SLO con el fin de alojar las cargas de trabajo por debajo de 100 DTU.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>¿Hay alguna diferencia en las características de base de datos entre los niveles de servicio basados en DTU existentes y los nuevos basados en núcleos virtuales? 
Los nuevos niveles de servicio admiten un superconjunto de las características disponibles con las ofertas actuales basadas en DTU. Las características adicionales incluyen un conjunto de vistas de administración dinámicas (DMV) adicionales y opciones de configuración de recursos adicionales. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Si la base de datos está enlazada a la CPU y no usa mucho almacenamiento, ¿puedo aumentar los recursos de proceso sin pagar por almacenamiento adicional?
Sí, puede seleccionar de forma independiente el nivel de proceso que necesita su aplicación y mantener el almacenamiento sin cambios. El almacenamiento puede establecerse en 32 GB como mínimo. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>¿Admitirán los nuevos niveles basados en núcleos virtuales la restauración a un momento dado (PITR) durante 35 días como ahora? 
Puede configurar la retención de copia de seguridad de PITR entre 7 y 35 días. El almacenamiento de copia de seguridad se cobra por separado según el consumo de almacenamiento real si supera la cantidad de almacenamiento igual al tamaño máximo de datos. En la versión preliminar, el período de retención de PITR se establece de forma predeterminada en 7 días. En muchos casos, el tamaño máximo de los datos es suficiente para almacenar copias de seguridad durante 7 días.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>¿Por qué se permite la selección de la generación de hardware para los recursos de proceso?
Nuestro objetivo es permitir la flexibilidad máxima para que pueda elegir una configuración de rendimiento que se ajuste lo más posible a las necesidades de la aplicación. En la tabla anterior se muestran las diferencias entre Gen4 y Gen5. En concreto, el hardware de Gen4 ofrece bastante más memoria por núcleo virtual. Sin embargo, el hardware de Gen5 permite escalar verticalmente mucho más alto los recursos de proceso. Queremos que estas diferencias sean transparentes, para que pueda lograr la relación óptima de precio/rendimiento para la aplicación.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>¿Hay que desconectar la aplicación para realizar la conversión de una base de datos basada en DTU a un nivel de servicio basado en núcleos virtuales? 
Los nuevos niveles de servicio ofrecen un método sencillo de conversión en línea similar al proceso existente de actualizar las bases de datos desde el nivel de servicio Estándar a Premium, y viceversa. Esta conversión puede iniciarse con Portal, ARM, PowerShell, la CLI de Azure o T-SQL. Consulte [Administración de bases de datos únicas](sql-database-single-database-resources.md) y [Administración de grupos elásticos](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>¿Se puede convertir una base de datos de un nivel de servicio basado en núcleos virtuales a uno basado en DTU? 
Sí, puede convertir fácilmente su base de datos a cualquier objetivo de rendimiento usando Portal o mediante programación con Portal, ARM, PowerShell, la CLI de Azure o T-SQL. Consulte [Administración de bases de datos únicas](sql-database-single-database-resources.md) y [Administración de grupos elásticos](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>¿Se puede actualizar o degradar entre los niveles de servicio De uso General y Crítico para la empresa? 
Sí, con algunas restricciones. La SKU de destino debe cumplir el tamaño máximo de base de datos o grupo elástico configurado para la implementación existente. Si usa la [Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), la SKU Crítico para la empresa solo está disponible para los clientes con licencias Enterprise Edition. Solo los clientes que migraron del entorno local al nivel de servicio De uso general mediante Ventaja híbrida de Azure para SQL Server con licencias Enterprise Edition pueden actualizarse al nivel Crítico para la empresa. Para más información, consulte [¿Cuáles son los derechos específicos de la Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Esta conversión no produce tiempo de inactividad y se puede iniciar mediante Portal, ARM, PowerShell, la CLI de Azure o T-SQL. Consulte [Administración de bases de datos únicas](sql-database-single-database-resources.md) y [Administración de grupos elásticos](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Estoy usando una base de datos Premium RS que no estará disponible con carácter general. ¿Puedo actualizarla a un nuevo nivel y conseguir una ventaja similar de precio/rendimiento?
Dado que el modelo de núcleos virtuales permite un control independiente sobre la cantidad de almacenamiento y proceso aprovisionados, puede administrar más eficazmente los costos resultantes, lo que lo convierte en un destino atractivo para las bases de datos Premium RS. Además, la [Ventaja para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) proporciona un descuento considerable cuando se usa el modelo basado en núcleos virtuales. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>¿Con qué frecuencia se pueden ajustar los recursos por grupo?
Tan a menudo como desee. Consulte [Administración de grupos elásticos](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>¿Cuánto tiempo se tarda en cambiar el nivel de servicio o el nivel de rendimiento de una base de datos única o en mover una base de datos dentro y fuera de un grupo elástico?
Para cambiar el nivel de servicio de una base de datos e incorporarla y retirarla de un grupo es necesario copiar la base de datos en la plataforma como operación en segundo plano. Cambiar el nivel de servicio puede tardar desde solo unos minutos hasta varias horas, según el tamaño de las bases de datos. En ambos casos, las bases de datos permanecen en línea y disponibles durante el traslado. Para más información sobre cómo cambiar las bases de datos únicas, consulte [Cambiar el nivel de servicio de una base de datos](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>¿Cuándo se deben usar bases de datos elásticas y cuándo una base de datos única?
En general, los grupos elásticos están diseñados para un [patrón de aplicación de software como servicio (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md) típico, donde hay una base de datos por cliente o inquilino. Adquirir bases de datos individuales y aprovisionarlas en exceso para cubrir todas las variaciones y la demanda máxima no resulta rentable. Con los grupos, el rendimiento colectivo del grupo y el escalado horizontal y reducción vertical de las bases de datos se administra automáticamente. El motor inteligente de Azure recomendará un grupo para las bases de datos si un patrón de uso lo garantiza. Para más información, consulte la [guía de grupos elásticos](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>¿Cómo se muestra en mi factura el uso de SQL Database con el modelo de compra basado en DTU?
SQL Database se factura según una tarifa horaria predecible basada en el [modelo de compra](sql-database-service-tiers-dtu.md). El uso real se procesa y se prorratea por horas, por lo que su factura podría mostrar fracciones de una hora. Por ejemplo, si una base de datos existe durante 12 horas al mes, la factura mostrará un uso de 0,5 días. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>¿Qué ocurre si una base de datos única está activa durante menos de una hora o usa un nivel de servicio mayor durante menos de una hora?
Se le cobrará por cada hora que una base de datos exista con el mayor nivel de servicio + nivel de rendimiento aplicable durante esa hora, independientemente del uso o de si la base de datos estuvo activa durante menos de una hora. Por ejemplo, si crea una base de datos única y la elimina a los cinco minutos, se le efectuará un cargo de una hora por usar la base de datos. 

Ejemplos:

* Si crea una base de datos Básica y la actualiza inmediatamente a Estándar S1, se le cobrará la tarifa Estándar S1 por la primera hora.
* Si actualiza una base de datos de Básica a Premium a las 10:00 p.m. y la actualización finaliza a la 1:35 a.m. del día siguiente, se le cobra según la tarifa de Premium a partir de 1:00 a.m. 
* Si degrada una base de datos de Premium a Básica a las 11:00 a.m. y dicha operación finaliza a las 2:15 p.m., la base de datos se cobra según la tarifa Premium hasta las 3:00 p.m. y después según la tarifa Básica.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>¿Cómo se muestra en mi factura el uso de grupos elásticos con el modelo de compra basado en DTU?
Los cargos de los grupos elásticos se muestran en la factura como DTU elásticas (eDTU) o núcleos virtuales más el almacenamiento con los incrementos que se indican en la [página de precios](https://azure.microsoft.com/pricing/details/sql-database/). No se realizan cargos por base de datos para los grupos elásticos. Se le cobra por cada hora que un grupo exista en la eDTU o los núcleos virtuales más altos, con independencia del uso o de si el grupo estuvo activo durante menos de una hora. 

Ejemplos del modelo de compra basado en DTU:

* Si crea un grupo elástico Estándar con 200 eDTU a las 11:18, al agregar cinco bases de datos al grupo se le cobrarán 200 eDTU por la hora completa, empezando a las 11:00. durante el resto del día.
* El día 2, a las 05:05 h, la base de datos 1 empieza a consumir 50 eDTU y se mantiene constante durante todo el día. Las bases de datos 2 a 5 fluctúan entre 0 y 80 eDTU. Durante el día se agregan otras cinco bases de datos con un consumo variable de eDTU durante todo el día. El día 2 es un día completo por el que se facturan 200 eDTU. 
* El día 3, a las 5 a.m. agrega otras 15 bases de datos. El uso de las bases de datos aumenta a lo largo del día hasta el punto en que decide aumentar las eDTU para el grupo de 200 a 400 a las 20:05 h. Los cargos en el nivel de 200 eDTU estaban en vigor hasta las 20:00 h y aumenta a 400 eDTU para las cuatro horas restantes. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>¿Cómo se facturan los grupos elásticos en el modelo de compra basado en DTU?
Los grupos elásticos se facturan según las características siguientes:

* Los grupos elásticos se facturan desde su creación, incluso si no contiene ninguna base de datos.
* Los grupos elásticos se facturan por horas. Se trata de la misma frecuencia de medición que la de los niveles de rendimiento de las bases de datos únicas.
* Si se cambia el tamaño de un grupo elástico, el grupo no se factura según la nueva cantidad de recursos hasta que la operación de cambio de tamaño se complete. Esto sigue el mismo patrón que el cambio de nivel de rendimiento de las bases de datos únicas.
* El precio de un grupo elástico se basa en los recursos del grupo. El precio de un grupo elástico es independiente del número y el uso de las bases de datos elásticas de dentro de él.

Para más información, consulte los artículos sobre los [precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/), el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) y el [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>¿Cómo se muestra en mi factura el uso basado en núcleos virtuales? 
En el modelo basado en núcleos virtuales, el servicio se factura conforme a una tarifa por hora predecible basada en el nivel de servicio, el proceso aprovisionado en núcleos virtuales, el almacenamiento aprovisionado en GB/mes y el almacenamiento de copia de seguridad consumido. Si el almacenamiento de copias de seguridad supera el tamaño total de la base de datos (es decir, 100 % del tamaño de base de datos), se aplican cargos adicionales. Las horas de los núcleos virtuales, el almacenamiento de base de datos configurado, la E/S consumida y el almacenamiento de copia de seguridad se detallan claramente en la factura, de forma que resulta sencillo ver la información de los recursos que ha usado. Se incluye el almacenamiento de copia de seguridad hasta el 100 % del tamaño máximo de la base de datos, por encima del cual se factura en GB/mes consumidos al mes.

Por ejemplo: 
- Si la base de datos SQL existe durante 12 horas al mes, la factura muestra el uso de núcleos virtuales durante 12 horas. Si la base de datos SQL aprovisionó 100 GB más, la factura muestra el uso de almacenamiento en unidades de GB/mes prorrateadas por horas y el número de E/S consumidas en un mes.
- Si la base de datos SQL está activa durante menos de una hora, se le factura cada hora que la base de datos exista con el nivel de servicio más alto seleccionado, el almacenamiento aprovisionado y la E/S que se aplique durante esa hora, con independencia del uso o de que la base de datos estuviese activa durante menos de una hora.
- Si crea una Instancia administrada y la elimina a los cinco minutos, se le cobrará una hora de base de datos.
- Si crea una instancia administrada en el nivel De uso general con 8 núcleos virtuales e, inmediatamente, la amplía a 16 núcleos virtuales, se le cobrará la tarifa de 16 núcleos virtuales la primera hora.

> [!NOTE]
> Por un período limitado, hasta el 30 de junio de 2018, no se cobrarán los cargos por copias de seguridad y operaciones de E/S.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>¿Cómo se muestra en mi factura el uso de la replicación geográfica activa en un grupo elástico?
A diferencia de las bases de datos únicas, el uso de [replicación geográfica activa](sql-database-geo-replication-overview.md) con bases de datos elásticas no afecta a la facturación.  Solo se cobrarán los recursos aprovisionados para cada uno de los grupos (grupo principal y grupo secundario).

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>¿Cómo afectará a mi factura el uso de la característica de auditoría?
La auditoría está integrada en el servicio SQL Database sin costo adicional y está disponible en todos los niveles de servicio. Sin embargo, para almacenar los registros de auditoría, la característica de auditoría usa una cuenta de Azure Storage y se aplican tarifas por tablas y colas de Azure Storage en función del tamaño de su registro de auditoría.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>¿Cómo se puede restablecer la contraseña para el administrador del servidor?
En [Azure Portal](https://portal.azure.com) haga clic en **Servidores SQL Server**, seleccione el servidor en la lista y luego haga clic en **Restablecer contraseña**.

## <a name="how-do-i-manage-databases-and-logins"></a>¿Cómo se administran las bases de datos e inicios de sesión?
Consulte [Administrar bases de datos e inicios de sesión](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>¿Cómo asegurarse de que solo se permiten las direcciones IP autorizadas para acceder al servidor?
Vea [Configuración del firewall en SQL Database](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>¿Qué es un retraso de replicación esperado al replicar geográficamente una base de datos entre dos regiones dentro de la misma ubicación geográfica de Azure?
Actualmente admitimos un RPO de cinco segundos, mientras que el retraso de replicación ha sido inferior a este valor durante el hospedaje de la base de datos secundaria geográficamente en la región emparejada recomendada de Azure y en el mismo nivel de servicio.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>¿Qué es un retraso de replicación esperado cuando la base de datos secundaria geográficamente se crea en la misma región que la base de datos principal?
Según los datos empíricos, no hay demasiada diferencia entre el retraso de replicación dentro de una región y entre regiones cuando se utiliza la región emparejada recomendada de Azure. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Si hay un error de red entre dos regiones, ¿cómo funciona la lógica de reintento cuando está configurada la replicación geográfica?
Si se produce una desconexión, intentamos volver a establecer las conexiones cada 10 segundos.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>¿Qué puedo hacer para tener la seguridad de que se replica un cambio importante en la base de datos principal?
La base de datos secundaria geográficamente es una réplica asincrónica y no intentaremos mantenerla sincronizada completamente con la principal. No obstante, proporcionamos un método para forzar la sincronización a fin de garantizar la replicación de cambios importantes (por ejemplo, actualizaciones de contraseña). La sincronización forzada afecta al rendimiento al bloquear el subproceso de llamada hasta que todas las transacciones confirmadas se replican. Para más información, consulte [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>¿Qué herramientas están disponibles para supervisar el retraso de replicación entre la base de datos principal y la base de datos secundaria geográficamente?
Exponemos el retraso de replicación en tiempo real entre la base de datos principal y la base de datos secundaria geográficamente mediante una DMV. Para más información, consulte [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover una base de datos a un servidor diferente en la misma suscripción
En [Azure Portal](https://portal.azure.com), haga clic en **Bases de datos SQL**, seleccione una base de datos de la lista y haga clic en **Copiar**. Para más información, consulte [Copia de una Base de datos SQL de Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover una base de datos de una suscripción a otra
En [Azure Portal](https://portal.azure.com), haga clic en **Servidores SQL Server** y, en la lista, seleccione el servidor que hospeda la base de datos. Haga clic en **Mover**y seleccione los recursos que va a mover y la suscripción a la que los va a mover.
