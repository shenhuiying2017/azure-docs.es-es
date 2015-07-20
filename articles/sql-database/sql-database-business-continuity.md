<properties 
   pageTitle="Información general sobre la continuidad de negocio de la base de datos SQL de Azure"
   description="Obtenga información acerca de las características integradas y las opciones disponibles de la base de datos SQL de Azure que pueden ayudarle a mantener sus aplicaciones de nube críticas de nube en ejecución y a recuperarse de los errores y las interrupciones."
   services="sql-database"
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

# Información general acerca de la continuidad del negocio

La continuidad del negocio implica el diseño, la implementación y la ejecución de su aplicación de manera que sea resistente a los eventos de interrupción, planeados o no planeados, que provocan la pérdida permanente o temporal de la capacidad de la aplicación para realizar su función empresarial. Entre los eventos no planeados se incluyen desde los errores humanos hasta las interrupciones permanentes o temporales, pasando por los desastres regionales que pueden provocar la pérdida a gran escala de la instalación en una determinada región de Azure. Los eventos planificados incluyen la reimplementación de la aplicación en una región diferente, la aplicación de actualizaciones de aplicaciones, etc. El objetivo de la continuidad del negocio es que su aplicación siga funcionando durante estos eventos con un impacto mínimo en la función empresarial.

Para tratar las soluciones de continuidad del negocio, existen varios conceptos con los que debe estar familiarizado.

**Recuperación ante desastres (DR):** proceso que permite restaurar la función empresarial normal de la aplicación.

**Tiempo calculado de recuperación estimado (ERT):** tiempo estimado necesario para que la base de datos vuelva a estar completamente disponible tras una solicitud de restauración o de conmutación por error.

**Objetivo de tiempo de recuperación (RTO)**: tiempo máximo aceptable para la recuperación total de la aplicación después del evento de interrupción. El RTO mide la pérdida máxima de disponibilidad durante los errores.

**Objetivo de punto de recuperación (RPO)**: cantidad máxima de actualizaciones recientes (intervalo de tiempo) que la aplicación puede perder hasta que se recupera completamente tras el evento de interrupción. El RPO mide la pérdida máxima de datos durante los errores.


## Escenarios de continuidad del negocio

La continuidad del negocio aborda los siguientes escenarios clave.

###Diseño para la continuidad del negocio

La aplicación que estoy creando es fundamental para mi negocio Por ello deseo diseñarla y configurarla para que sea capaz de sobrevivir a un desastre regional provocado por un error catastrófico del servicio. Conozco cuáles son los requisitos de RPO y RTO de mi aplicación y elegiré la configuración que se ajuste a dichos requisitos.

###Recuperación de errores humanos

Tengo derechos administrativos para tener acceso a la versión de producción de la aplicación. Como parte del proceso de mantenimiento periódico, he cometido un error y he eliminado algunos datos críticos en producción. Por ello, deseo restaurar rápidamente los datos con el fin de mitigar el impacto del error.

###Recuperación tras una interrupción

Estoy ejecutando la aplicación en producción y recibo una alerta que indica que hay una interrupción importante en la región en la que se ha implementado la aplicación. Deseo iniciar el proceso de recuperación para ponerla en una región distinta para mitigar el impacto en el negocio.

###Exploración de la recuperación ante desastres

Puesto que con la recuperación de la interrupción, se reubicará el nivel de datos de la aplicación en una región distinta, necesito probar de manera periódica el proceso de recuperación y evaluar su impacto en la aplicación para estar preparado.

###Actualización de la aplicación sin tiempo de inactividad

Voy a publicar una actualización importante de mi aplicación. Dicha actualización implica cambios en el esquema de la base de datos, implementación de procedimientos almacenados adicionales, etc. Este proceso requiere detener el acceso de los usuarios a la base de datos. Al mismo tiempo, deseo asegurarme de que la actualización no provoque una interrupción importante en las operaciones de negocio.

##Características de la continuidad del negocio

La tabla siguiente muestra las diferencias de las características de continuidad del negocio en los distintos niveles de servicio:

| Capacidad | Nivel Basic | Nivel Standard |Nivel Premium 
| --- |--- | --- | ---
| Restauración a un momento dado | Cualquier punto de restauración en 7 días | Cualquier punto de restauración en 14 días | Cualquier punto de restauración en 35 días
| Restauración geográfica | ERT < 12h, RPO < 1 h | ERT < 12h, RPO < 1 h | ERT < 12h, RPO < 1 h
| Replicación geográfica estándar | No incluida | ERT < 30 s, RPO < 5 s | ERT < 30 s, RPO < 5 s
| Replicación geográfica activa | No incluida | No incluida | ERT < 30 s, RPO < 5 s

Estas características se proporcionan para abordar los escenarios mencionados anteriormente. Vea la sección [Diseño para la continuidad del negocio](sql-database-business-continuity-design.md) sección para obtener instrucciones sobre cómo seleccionar características específicas.

###Restauración a un momento dado

la restauración a un momento dado está diseñada para restablecer la base de datos a un punto anterior en el tiempo. Esta característica usa las copias de seguridad de la base de datos, las copias de seguridad incrementales y las copias de seguridad de registros de transacciones que el servicio mantiene automáticamente para cada base de datos de usuario. Esta capacidad está disponible para todos los niveles de servicio. Puede recuperar la base de datos de hace 7 días, con el nivel Basic, 14 días, con el nivel Standard, y 35 días, con el nivel Premium. Vea la sección [Recuperación de errores humanos](sql-database-user-error-recovery.md) para obtener más información sobre cómo usar la restauración a un momento dado.

###Restauración geográfica

La restauración geográfica también está disponible en las bases de datos Basic, Standard y Premium. Esta restauración proporciona la opción de recuperación predeterminada cuando la base de datos tampoco está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Al igual que la restauración a un momento dado, la restauración geográfica se basa en copias de seguridad de la base de datos en el almacenamiento de Azure con redundancia geográfica. Esta opción permite restaurar la base de datos desde la copia de seguridad de replicación geográfica. Por lo tanto es resistente a las interrupciones de almacenamiento que tienen lugar en la región principal. Vea la sección [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener más información sobre cómo usar la restauración geográfica.

###Replicación geográfica estándar

La replicación geográfica estándar está disponible para las bases de datos Standard y Premium. Esta función está diseñada para aplicaciones que pueden usar la capacidad de nivel de servicio estándar pero tienen unos requisitos de recuperación más exigentes que los que puede ofrecer la restauración geográfica. Cuando se produce un error en la base de datos principal, se puede iniciar la conmutación por error a una base de datos secundaria no legible almacenada en la región de recuperación ante desastres emparejada. Vea la sección [Diseño para la continuidad del negocio](sql-database-business-continuity-design.md) para obtener más información sobre cómo configurar la replicación geográfica y la sección [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener más información sobre cómo realizar la conmutación por error a la base de datos secundaria.

###Replicación geográfica activa

La replicación geográfica activa está disponible para bases de datos Premium. Esta función está diseñada para aplicaciones de escritura intensiva con los requisitos de recuperación más exigentes. Con la replicación geográfica activa, puede crear hasta cuatro bases de datos secundarias legibles en servidores situados en regiones diferentes. Puede iniciar la conmutación por error a cualquiera de las bases de datos secundarias del mismo modo que con la replicación geográfica estándar. Además, la replicación geográfica activa puede utilizarse para los escenarios de actualización o reubicación de la aplicación, así como para el equilibrio de cargas de trabajo de solo lectura. Vea la sección [Diseño para la continuidad del negocio](sql-database-business-continuity-design.md) para obtener más información sobre cómo configurar la replicación geográfica y la sección [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener más información sobre cómo realizar la conmutación por error a la base de datos secundaria. Vea la sección [Actualización de la aplicación sin tiempo de inactividad](sql-database-business-continuity-application-upgrade.md) para obtener más información sobre cómo implementar la actualización de la aplicación sin tiempo de inactividad.



 

<!---HONumber=July15_HO2-->