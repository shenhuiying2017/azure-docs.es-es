<properties 
   pageTitle="Obtención de detalles de la recuperación de una base de datos SQL tras un desastre" 
   description="Obtenga instrucciones e información sobre prácticas recomendadas acerca del uso de la base de datos SQL de Azure para la realización de tareas de obtención de detalles de la recuperación ante desastres. Dichas tareas le ayudarán a mantener la capacidad de recuperación ante errores y fallos de las aplicaciones de negocio críticas." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/15/2015"
   ms.author="mihaelab"/>

#Obtención de detalles de la recuperación ante desastres

Se recomienda validar periódicamente el flujo de trabajo de preparación de la aplicación para la recuperación. Comprobar el comportamiento de la aplicación y las implicaciones de las pérdidas de datos o de las interrupciones que conlleva la conmutación por error es una buena práctica de ingeniería. También es un requisito de la mayoría de estándares del sector como parte de la certificación de continuidad del negocio.

Obtener los detalles de una recuperación ante desastres implica lo siguiente:

- Simular la interrupción del nivel de datos.
- Realizar la recuperación. 
- Validar la integridad de la aplicación tras la recuperación.

Dependiendo del [diseño para la continuidad del negocio](sql-database-business-continuity.md), el flujo de ejecución del proceso de obtención de detalles puede variar. A continuación se describen prácticas recomendadas de obtención de detalles de la recuperación ante desastres en el contexto de base de datos SQL de Azure.

##Restauración geográfica

Para evitar la posible pérdida de datos durante la obtención de detalles de la recuperación ante desastres, se recomienda obtener los detalles mediante la creación de una copia del entorno de producción y utilizando dicho entorno para comprobar el flujo de trabajo de conmutación por error de la aplicación.
 
####Simulación de interrupción

- Simular la interrupción mediante la eliminación o el cambio de nombre de la base de datos de origen puede producir un error de conectividad de la aplicación. De este modo, podrá validar la detección de interrupciones/alertas y medir el RTO durante la recuperación.

####Recuperación

- Realice la restauración geográfica de la base de datos en un servidor diferente, tal como se describe [aquí](sql-database-disaster-recovery.md). 
- Cambie la configuración de la aplicación para conectarse a las bases de datos recuperadas y siga las directrices de la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md) para completar la recuperación.

####Validación

- Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (es decir, las cadenas de conexión, los inicios de sesión, la comprobación de funciones básicas u otras validaciones que formen parte de los procedimientos estándar de validación de aplicaciones).

##Replicación geográfica estándar

Una base de datos protegida mediante replicación geográfica estándar solo puede tener una base de datos secundaria no legible. El ejercicio de obtención de detalles incluirá la finalización forzada del vínculo, momento en que la base de datos estará sin protección. Además, existe una posibilidad de que se pierdan datos, por lo que no recomendamos a los clientes realizar esta prueba en las bases de datos de producción. En su lugar, se recomienda crear una copia del entorno de producción y utilizarlo para comprobar el flujo de trabajo de conmutación por error de la aplicación.

####Simulación de interrupción

- Simule la carga de trabajo en la base de datos principal. Pueden perderse datos si la base de datos principal está activa en el momento de la finalización. Esto hará que la obtención de detalles sea más realista.
- Elimine la base de datos principal o [realice la finalización forzada](sql-database-disaster-recovery.md) del vínculo en la base de datos secundaria.

####Recuperación

- Cambie la configuración de la aplicación para conectarse a la anterior base de datos secundaria de solo lectura que pasará a estar totalmente accesible. La aplicación usará dicha base de datos como nueva base de datos principal. 
- Siga las directrices de la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md) para completar la recuperación.

####Validación

- Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (es decir, las cadenas de conexión, los inicios de sesión, la comprobación de funciones básicas u otras validaciones que formen parte de los procedimientos estándar de validación de aplicaciones).

##Replicación geográfica activa

La obtención de detalles de la recuperación ante desastres se realizará mediante el uso de un servidor de destino paralelo y con la creación de otro conjunto de bases de datos secundarias incluidos en el servidor. Deberá usar una versión de prueba del nivel de aplicación para comprobar el estado de funcionamiento y la integridad de los datos mediante la ejecución de pruebas en dicho servidor tras la finalización forzada.

####Simulación de interrupción

- [Cree un nuevo vínculo de replicación geográfica activa](sql-database-business-continuity-design.md) desde la base de datos principal a un servidor de pruebas secundario. Pueden perderse datos si la base de datos principal está activa en el momento de la finalización. Esto hará que la obtención de detalles sea más realista.
- [Realice la finalización forzada](sql-database-disaster-recovery.md) del vínculo en la base de datos secundaria que reside en el servidor de pruebas.

####Recuperación

- Cambie la configuración de la aplicación para conectarse a la anterior base de datos secundaria de solo lectura que estará disponible para la escritura tras la finalización.
- Siga las directrices de la guía [Finalización de una base de datos recuperada](sql-database-recovered-finalize.md) para completar la recuperación.

####Validación

- Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (es decir, las cadenas de conexión, los inicios de sesión, la comprobación de funciones básicas u otras validaciones que formen parte de los procedimientos estándar de validación de aplicaciones).

<!---HONumber=July15_HO4-->