---
title: Diseño de soluciones de nube para la recuperación ante desastres mediante la replicación geográfica de la Base de datos SQL | Microsoft Docs
description: Aprenda a diseñar la solución de nube para recuperación ante desastres eligiendo el modelo correcto de conmutación por error.
services: sql-database
documentationcenter: ''
author: anosov1960
manager: jhubbard
editor: monicar

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2016
ms.author: sashan

---
# Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL
A lo largo de los años, hemos aprendido que los servicios en la nube no son infalibles y que los incidentes catastróficos pueden ocurrir y lo harán. Base de datos SQL proporciona una serie de funcionalidades para proporcionar la continuidad del negocio de la aplicación cuando se producen estos incidentes. Los [grupos elásticos](sql-database-elastic-pool.md) y las bases de datos independientes admiten el mismo tipo de funcionalidades de recuperación ante desastres. En este artículo se describen varias estrategias de recuperación ante desastres para grupos elásticos que sacan partido a esas características de continuidad de negocio de Base de datos SQL.

Para los fines de este artículo, usaremos el patrón de aplicaciones de ISV de SaaS canónico:

<i>Una aplicación web moderna basada en la nube aprovisiona una base de datos SQL para cada usuario final. El ISV tiene un gran número de clientes y, por tanto, utiliza muchas bases de datos, conocidas como bases de datos de inquilino. Dado que las bases de datos de inquilino normalmente tienen patrones de actividad impredecibles, el ISV usa un grupo elástico para que el costo de la base de datos sea muy predecible durante prolongados períodos. El grupo elástico también simplifica la administración del rendimiento cuando aumenta la actividad del usuario. Además de las bases de datos de inquilino, la aplicación también utiliza varias bases de datos para administrar los perfiles de usuario, la seguridad o la recopilación de los patrones de uso, entre otros. La disponibilidad de los inquilinos individuales no afecta a la disponibilidad de la aplicación como conjunto. Sin embargo, la disponibilidad y el rendimiento de las bases de datos de administración son fundamentales para el funcionamiento de la aplicación y, si las bases de datos de administración están sin conexión, pasará lo mismo con toda la aplicación.</i>

En el resto del artículo, trataremos las estrategias de recuperación ante desastres que abarcan una variedad de escenarios que va desde las aplicaciones de inicio sensibles al costo a aquellas con requisitos estrictos de disponibilidad.

## Escenario 1. Inicio sensible al costo
<i>Acabo de crear una empresa y me preocupan sobremanera los costos. Quiero simplificar la implementación y administración de la aplicación y estoy dispuesto a tener un contrato de nivel de servicio limitado para clientes individuales. Sin embargo, quiero garantizar que nunca se quede sin conexión toda la aplicación.</i>

Para satisfacer el requisito de simplicidad, debe implementar todas las bases de datos de inquilino en un grupo elástico de la región de Azure de su elección e implementar las bases de datos de administración como bases de datos de replicación geográfica independiente. Para la recuperación ante desastres de los inquilinos, use la restauración geográfica, que se incluye sin costo adicional. Para garantizar la disponibilidad de las bases de datos de administración, deben ser replicadas geográficamente en otra región (paso 1). El costo en curso de la configuración de recuperación ante desastres en este escenario es igual al costo total de las bases de datos secundarias. En el siguiente diagrama se ilustra esta configuración.

![En la Ilustración 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

En el caso de una interrupción en la región principal, se ilustran los pasos de recuperación para conectar la aplicación en el diagrama siguiente.

* Realice inmediatamente una conmutación por error en las bases de datos de administración (2) en la región de recuperación ante desastres.
* Cambie la cadena de conexión de la aplicación para que apunte a la región de recuperación ante desastres. Todas las cuentas nuevas y las bases de datos de inquilino se crearán en la región de recuperación ante desastres. Los clientes existentes verán que sus datos no están disponibles temporalmente.
* Cree el grupo elástico con la misma configuración que el grupo original (3).
* Use la restauración geográfica para crear copias de las bases de datos de inquilino (4). Considere desencadenar las restauraciones individuales por las conexiones de los usuarios finales o utilizar algún otro esquema de prioridad específica de la aplicación.

En este momento, la aplicación vuelve a estar conectada en la región de recuperación ante desastres, pero algunos clientes experimentarán una demora al tener acceso a sus datos.

![Ilustración 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Si la interrupción fue temporal, es posible que Azure recupere la región principal antes de que todas las restauraciones se completen en la región de recuperación ante desastres. En este caso, debe organizar el regreso de la aplicación a la región principal. El proceso realizará los pasos que se muestran en el diagrama siguiente.

* Cancele todas las solicitudes de restauración geográfica pendientes.
* Realice la conmutación por error de las bases de datos de administración a la región principal (5). Nota: Después de la recuperación de la región, las principales anteriores se convierten automáticamente en secundarias. Ahora volverán a cambiar los roles.
* Cambie la cadena de conexión de la aplicación para que vuelva a apuntar a la región principal. Ahora todas las cuentas nuevas y las bases de datos de inquilino se crearán en la región principal. Algunos clientes existentes verán que sus datos no están disponibles temporalmente.
* Establezca todas las bases de datos en el grupo de recuperación ante desastres de solo lectura para asegurarse de que no se puede modificar en la región de recuperación ante desastres (6).
* Para las bases de datos del grupo de recuperación ante desastres que hayan cambiado desde la recuperación, cambie el nombre o elimine las bases de datos correspondientes en el grupo principal (7).
* Copie las bases de datos actualizadas desde el grupo de recuperación ante desastres al grupo principal (8).
* Elimine el grupo de recuperación ante desastres (9).

En este momento la aplicación estará en línea en la región principal con todas las bases de datos de inquilino disponibles en el grupo principal.

![Ilustración 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

La principal **ventaja** de esta estrategia es el bajo costo continuo para la redundancia de nivel de datos. Las copias de seguridad las realiza automáticamente el servicio de Base de datos SQL sin necesidad de reescritura de aplicación y sin costo adicional. El costo se contrae solo cuando se restauran las bases de datos elásticas. La **contrapartida** es que la recuperación completa de todas las bases de datos de inquilino tardará mucho tiempo. Dependerá del número total de restauraciones que se inicien en la región de recuperación ante desastres y del tamaño general de las bases de datos de inquilino. Aunque si se priorizan algunas restauraciones de inquilinos sobre otras, estará compitiendo con todas las demás restauraciones que se inicien en la misma región, ya que el servicio arbitrará y se limitará para minimizar la repercusión global en las bases de datos de los clientes existentes. Además, la recuperación de las bases de datos de inquilino no podrá iniciarse hasta que se cree el nuevo grupo elástico en la región de recuperación ante desastres.

## Escenario 2. Aplicación madura con servicio en capas
<i>Tengo una aplicación de SaaS desarrollada con ofertas de servicio en capas y distintos Acuerdos de Nivel de Servicio para clientes de versiones de prueba y de pago. Para los clientes de versiones de prueba, tengo que reducir el costo tanto como sea posible. Los clientes de versiones de prueba pueden asumir el tiempo de inactividad, pero quiero reducir su probabilidad. Para los clientes de versiones de pago, los tiempos de inactividad es un riesgo de vuelo. Por tanto, quiero asegurarme de que los clientes con versiones de pago siempre tengan acceso a sus datos.</i>

Para admitir este escenario, debe separar los inquilinos de versiones de prueba de los inquilinos de versiones de pago, colocándolos en grupos elásticos independientes. Los clientes de versiones de prueba tendrán un eDTU menor por inquilino y un contrato de nivel de servicio menor con un tiempo de recuperación mayor. Los clientes de versiones de pago se encontrarán en un grupo con mayor eDTU por inquilino y un contrato de nivel de servicio superior. Para garantizar el menor tiempo de recuperación, las bases de datos de inquilino de los clientes de versiones de pago deben replicarse geográficamente. En el siguiente diagrama se ilustra esta configuración.

![Ilustración 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Al igual que en el primer escenario, las bases de datos de administración estarán bastante activas, por tanto puede utilizar una base de datos de replicación geográfica independiente para ello (1). Esto garantiza un rendimiento predecible para las nuevas suscripciones de cliente, actualizaciones del perfil y otras operaciones de administración. La región en la que residen los principales de las bases de datos de administración será la región principal y la región en la que residen los secundarios de las bases de datos de administración será la región de recuperación ante desastres.

Las bases de datos de inquilino de los clientes con versiones de pago tendrán bases de datos activas en el grupo aprovisionado "de pago" de la región principal. Debe aprovisionar un grupo secundario con el mismo nombre en la región de recuperación ante desastres. Cada inquilino estará replicado geográficamente en el grupo secundario (2). Esto permitirá una recuperación rápida de todas las bases de datos de inquilino mediante la conmutación por error.

En el caso de una interrupción en la región principal, en el diagrama siguiente se muestran los pasos de recuperación para volver a conectar la aplicación.

![Ilustración 5.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Realice inmediatamente una conmutación por error en las bases de datos de administración en la región de recuperación ante desastres (3).
* Cambie la cadena de conexión de la aplicación para que apunte a la región de recuperación ante desastres. Ahora todas las cuentas nuevas y las bases de datos de inquilino se crearán en la región de recuperación ante desastres. Los clientes de versiones de prueba existentes verán que sus datos no están disponibles temporalmente.
* Realice una conmutación por error en las bases de datos de inquilino de versiones de pago, en el grupo de la región de recuperación ante desastres, para restaurar inmediatamente su disponibilidad (4). Dado que la conmutación por error es un rápido cambio de nivel de metadatos, se puede considerar una optimización cuando se desencadenan las conmutaciones por error individuales a petición de las conexiones del usuario final.
* Si el tamaño de eDTU del grupo secundario era menor que el principal porque las bases de datos secundarias solo requieren la capacidad para procesar los registros de cambio mientras eran secundarias, debe aumentar inmediatamente la capacidad del bloque para acomodar la carga de trabajo completa de todos los inquilinos (5).
* Cree el nuevo grupo elástico con el mismo nombre y la misma configuración en la región de recuperación ante desastres para bases de datos de los clientes de versiones de prueba (6).
* Una vez creado el grupo de clientes de versiones de prueba, puede usar la restauración geográfica para restaurar las bases de datos de inquilino de versiones de prueba individuales en el nuevo grupo (7). Considere desencadenar las restauraciones individuales por las conexiones de los usuarios finales o utilizar algún otro esquema de prioridad específica de la aplicación.

En este momento la aplicación vuelve a estar en línea en la región de recuperación ante desastres. Todos los clientes de versiones de pago tienen acceso a los datos, mientras que los clientes de versiones de prueba experimentarán una demora al acceder a los suyos.

Cuando Azure recupera la región primaria *después* de haber restaurado la aplicación en la región de recuperación ante desastres, puede continuar ejecutando la aplicación en dicha región, o bien decidir realizar una conmutación por recuperación en la región principal. Si se recupera la región primaria *antes* de que se complete el proceso de conmutación por error, debe plantearse utilizar la conmutación por recuperación inmediatamente. La conmutación por recuperación realizará los pasos que se muestran en el diagrama siguiente.

![Ilustración 6.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Cancele todas las solicitudes de restauración geográfica pendientes.
* Realice una conmutación por error en las bases de datos de administración (8). Después de la recuperación de la región, la principal anterior se convierte automáticamente en secundaria. Ahora vuelve a convertir en principal.
* Realice una conmutación por error en las bases de datos de inquilino de versiones de pago (9). De igual forma, después de la recuperación de la región, las principales anteriores se convierten automáticamente en secundarias. Ahora volverán a ser principales.
* Establezca las bases de datos de versiones de prueba restauradas que han cambiado en la región de recuperación ante desastres en solo lectura (10).
* Para las bases de datos del grupo de recuperación ante desastres de los clientes de versiones de prueba que hayan cambiado desde la recuperación, cambie el nombre o elimine la base de datos correspondiente en el grupo principal (11).
* Copie las bases de datos actualizadas desde el grupo de recuperación ante desastres al grupo principal (12).
* Elimine el grupo de recuperación ante desastres (13).

> [!NOTE]
> La operación de conmutación por error es asincrónica. Para minimizar el tiempo de recuperación, es importante ejecutar comando de conmutación por error de las bases de datos de inquilino en lotes de al menos 20 bases de datos.
> 
> 

La principal **ventaja** de esta estrategia es que proporciona el Acuerdo de Nivel de Servicio superior para los clientes de versiones de pago. También garantiza que las nuevas versiones de prueba se desbloquean en cuanto se crea el grupo de recuperación ante desastres de versiones de prueba. La **contrapartida** es que esta configuración aumentará el costo total de las bases de datos de inquilino debido al costo del grupo de recuperación ante desastres secundario para los clientes de versiones de pago. Además, si el grupo secundario tiene un tamaño diferente, los clientes de versiones de pago experimentarán un rendimiento menor después de la conmutación por error hasta que se complete la actualización del grupo en la región de recuperación ante desastres.

## Escenario 3. Aplicación distribuida geográficamente con servicio en capas
<i>Tengo una aplicación de SaaS desarrollada con ofertas de servicio en capas. Deseo ofrecer un contrato de nivel de servicio muy agresivo a mis clientes de versiones de pago y minimizar el riesgo de impacto cuando se produzcan interrupciones, porque incluso una breve interrupción puede causar la insatisfacción del cliente. Es fundamental que los clientes de versiones de pago siempre puedan acceder a sus datos. Las versiones de prueba son gratuitas y no se ofrece ningún Acuerdo de Nivel de Servicio durante el periodo de prueba. </i>

Para este escenario, debe disponer de tres grupos elásticos independientes. Deben aprovisionarse dos grupos de igual tamaño con elevados eDTU por base de datos en dos regiones diferentes para contener las bases de datos de inquilino de clientes de versiones de pago. El tercer grupo que contiene a los inquilinos de versiones de prueba tendría menores eDTU por base de datos y se puede aprovisionar en una de las dos regiones.

Para garantizar el menor tiempo de recuperación durante las interrupciones en las bases de datos de inquilino de los clientes de versiones de pago, deben replicarse geográficamente con el 50 % de las bases de datos principales en cada una de las dos regiones. De forma similar, cada región tendría el 50 % de las bases de datos secundarias. De este modo si una región está sin conexión, el 50 % de las bases de datos de los clientes de versiones de pago se podrían ver afectados y tendrían que realizar la conmutación por error. Las otras bases de datos permanecerán intactas. Esta configuración se ilustra en el diagrama siguiente:

![Ilustración 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Al igual que en los escenarios anteriores, las bases de datos de administración estarán bastante activas, por tanto debe configurarlas como bases de datos de replicación geográfica independiente (1). Esto garantiza un rendimiento predecible de las nuevas suscripciones de cliente, actualizaciones del perfil y otras operaciones de administración. La región A podría ser la región principal de las bases de datos de administración y la región B se podría usar para la recuperación de las bases de datos de administración.

Las bases de datos de inquilino de los clientes de versiones de pago también se replicarán geográficamente pero las principales y secundarias se dividen entre la región A y la región B (2). De este modo las bases de datos principales de inquilino afectadas por la interrupción puede conmutar por error a otra región y estar disponibles. La otra mitad de las bases de datos de inquilino no se verá afectada.

El siguiente diagrama ilustra los pasos de recuperación que hay que llevar a cabo si se produce una interrupción en la región A.

![Ilustración 5.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Realice inmediatamente una conmutación por error en las bases de datos de administración en la región B (3).
* Cambie la cadena de conexión de la aplicación para que señale a las bases de datos de administración de la región B. Modifique las bases de datos de administración para asegurarse de que las nuevas cuentas y bases de datos de inquilino se crearán en la región B y que las bases de datos de inquilino existentes se encontrarán allí también. Los clientes de versiones de prueba existentes verán que sus datos no están disponibles temporalmente.
* Realice una conmutación por error en las bases de datos de inquilino de versiones de pago en el grupo 2 de la región B, para restaurar inmediatamente su disponibilidad (4). Dado que la conmutación por error es un rápido cambio de nivel de metadatos, se puede considerar una optimización cuando se desencadenan las conmutaciones por error individuales a petición de las conexiones del usuario final.
* Ya que ahora el grupo 2 contiene solo bases de datos principales, aumentará la carga de trabajo total del grupo, por lo que debería aumentar de inmediato su tamaño de eDTU (5).
* Cree el nuevo grupo elástico con el mismo nombre y la misma configuración en la región B para bases de datos de los clientes de versiones de prueba (6).
* Una vez creado el grupo, puede usar la restauración geográfica para restaurar las bases de datos de inquilino de versiones de prueba individuales en el grupo (7). Considere desencadenar las restauraciones individuales por las conexiones de los usuarios finales o utilizar algún otro esquema de prioridad específica de la aplicación.

> [!NOTE]
> La operación de conmutación por error es asincrónica. Para minimizar el tiempo de recuperación, es importante ejecutar comando de conmutación por error de las bases de datos de inquilino en lotes de al menos 20 bases de datos.
> 
> 

En este punto, la aplicación vuelve a estar en línea en la región B. Todos los clientes de versiones de pago tienen acceso a sus datos, mientras que los clientes de versiones de prueba experimentarán una demora al acceder a los suyos.

Cuando se recupera la región A, debe decidir si quiere usar la región B para clientes de versiones prueba o realizar una conmutación por recuperación mediante el grupo de clientes de versiones de prueba en la región A. Un criterio podría ser el porcentaje de bases de datos de inquilino de versiones de prueba modificadas desde la recuperación. Con independencia de esta decisión, debe volver a equilibrar los inquilinos de versiones de pago entre los dos grupos. En el siguiente diagrama se muestra el proceso de la conmutación por recuperación de las bases de datos de inquilino de versiones de prueba en la región A.

![Ilustración 6.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Cancele todas las solicitudes de restauración geográfica pendientes en el grupo de recuperación ante desastres de versiones de prueba.
* Realice una conmutación por error en la base de datos de administración (8). Después de la recuperación de la región, la región principal anterior se convierte automáticamente en secundaria. Ahora vuelve a convertir en principal.
* Seleccione qué bases de datos de inquilino de versiones de pago producirán una conmutación por recuperación en el grupo 1 e inicie la conmutación por error en sus secundarias (9). Después de la recuperación de la región, todas las bases de datos del grupo 1 se convertirán automáticamente en secundarias. Ahora el 50 % volverán a ser principales.
* Reduzca el tamaño del grupo 2 a la eDTU original (10).
* Establezca todas las bases de datos de versiones de prueba restauradas de la región B en solo lectura (11).
* Para las bases de datos del grupo de recuperación ante desastres de versiones de prueba que hayan cambiado desde la recuperación, cambie el nombre o elimine las bases de datos correspondientes del grupo principal (12).
* Copie las bases de datos actualizadas desde el grupo de recuperación ante desastres al grupo principal (13).
* Elimine el grupo de recuperación ante desastres (14).

Las principales **ventajas** de esta estrategia son las siguientes:

* Admite el contrato de nivel de servicio más agresivo para los clientes de versiones de pago porque garantiza que una interrupción no puede afectar a más del 50 % de las bases de datos de inquilino.
* También garantiza que las nuevas versiones de prueba se desbloquean tan pronto se cree el grupo de recuperación ante desastres de versiones de prueba durante la recuperación.
* Permite un uso más eficaz de la capacidad del grupo, ya que se garantiza que el 50 % de las bases de datos secundarias de los grupos 1 y 2 sean menos activas que las bases de datos principales.

Las **contrapartidas** principales son las siguientes:

* Las operaciones de CRUD en las bases de datos de administración tendrán una latencia menor para los usuarios finales conectados a la región A que para los usuarios finales conectados a la región B, ya que se ejecutarán en la principal de las bases de datos de administración.
* Requiere un diseño más complejo de la base de datos de administración. Por ejemplo, cada registro de inquilinos tendría que tener una etiqueta de ubicación que debe cambiarse durante la conmutación por error y la conmutación por recuperación.
* Los clientes de versiones de pago pueden experimentar un rendimiento inferior al normal hasta que se complete la actualización de los grupos en la región B.

## Resumen
Este artículo se centra en las estrategias de recuperación ante desastres para el nivel de base de datos utilizado por una aplicación multiinquilino ISV de SaaS. La estrategia que seleccione debe basarse en las necesidades de la aplicación, como el modelo de negocio, el contrato de nivel de servicio que desea ofrecer a sus clientes, la restricción del presupuesto, entre otras. Cada estrategia descrita describe las ventajas y los inconvenientes para que pueda tomar una decisión informada. Además, la aplicación específica probablemente incluya otros componentes de Azure. Debe revisar las instrucciones de continuidad de negocio de los mismos y coordinar la recuperación de la capa de base de datos con ellos. Para obtener más información sobre la administración de la recuperación de aplicaciones de base de datos en Azure, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica en la Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## Pasos siguientes
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Continuidad empresarial con Base de datos SQL de Azure](sql-database-business-continuity.md).
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md) (Restauración bases de datos a partir de las copias de seguridad iniciadas por el servicio).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->