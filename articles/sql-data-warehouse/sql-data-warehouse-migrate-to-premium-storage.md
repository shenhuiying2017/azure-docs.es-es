<properties
   pageTitle="Migración del almacenamiento de datos SQL de Azure existente a almacenamiento premium | Microsoft Azure"
   description="Instrucciones para migrar un almacenamiento de datos SQL existente a almacenamiento premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Información de migración al Almacenamiento premium
En el servicio Almacenamiento de datos SQL, se introdujo recientemente el [Almacenamiento premium para poder predecir el rendimiento de manera más eficaz][]. Ahora se pueden migrar los almacenes de datos existentes del Almacenamiento estándar al premium. Siga leyendo para obtener más información sobre cómo y cuándo se producen las migraciones automáticas, además de cómo realizar migraciones manuales en caso de que prefiera tener el control en los tiempos de inactividad.

Si tiene más de un almacén de datos, emplee el [programa de migración automática][] siguiente para determinar cuándo se migrará.

## Información de la migración automática
De forma predeterminada, la base de datos se migrará automáticamente durante las 18:00 y las 6:00 (la hora local de su región) en algún momento del [programa de migración automática][] siguiente. Durante la migración, no podrá utilizarse el almacén de datos existente. Estimamos que el proceso de migración dura aproximadamente 1 hora por cada TB de almacenamiento de cada almacén de datos. Asimismo, nos aseguraremos de que no se le cobre nada en ningún momento del proceso.

> [AZURE.NOTE] Durante la migración, no podrá usar el almacén de datos existente. Cuando finalice el proceso, el almacenamiento de datos volverá a estar en línea.

A continuación, encontrará los pasos que Microsoft realizará para completar la migración sin que usted tenga que hacer nada. Para este ejemplo, imagine que su almacenamiento de datos del Almacenamiento estándar se denomina "miAD".

1.	Microsoft cambiará el nombre de miAD a miAD\_ DO\_NOT\_USE\_ [Marca de tiempo].
2.	Microsoft pausará miAD\_ DO\_NOT\_USE\_ [Marca de tiempo]. Durante este periodo, Microsoft realizará una copia de seguridad. Es posible que observe que se llevan a cabo varias tareas de pausa y reanudación si se producen problemas durante este proceso.
3.	Microsoft creará un nuevo almacenamiento de datos denominado "miAD" en el Almacenamiento premium a partir de la copia de seguridad realizada en el paso 2 anterior. miAD no aparecerá hasta que acabe el proceso de restauración.
4.	Cuando finalice, se devolverán miAD en el mismo nivel de DWU y el estado de en pausa o activo que hubiera antes de la migración.
5.	Cuando acabe el proceso de migración, Microsoft eliminará miAD\_DO\_NOT\_USE\_ [Marca de tiempo].
	
> [AZURE.NOTE] Esta configuración no se transmitirá como parte de la migración:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

### Programa de migración automática
La migración automática se llevará a cabo desde las 18:00 a las 6:00 (la hora local de dicha región) en algún momento durante el programa de interrupción siguiente.

| Region | Fecha de inicio estimada | Fecha de finalización estimada |
| :------------------ | :--------------------------- | :--------------------------- |
| Australia Oriental | Sin determinar | Sin determinar |
| Sudeste de Australia | Sin determinar | Sin determinar |
| Centro de Canadá | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de Canadá | 23 de junio de 2016 | 1 de julio de 2016 |
| Central EE. UU.: | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de China | Sin determinar | Sin determinar |
| Asia oriental | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de EE. UU. | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de EE. UU. 2 | 23 de junio de 2016 | 1 de julio de 2016 |
| India central | 23 de junio de 2016 | 1 de julio de 2016 |
| Sur de India | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de Japón | Sin determinar | Sin determinar |
| Oeste de Japón | Sin determinar | Sin determinar |
| Centro-Norte de EE. UU | Sin determinar | Sin determinar |
| Europa del Norte | Sin determinar | Sin determinar |
| Centro-Sur de EE. UU | 23 de junio de 2016 | 1 de julio de 2016 |
| Sudeste asiático | 23 de junio de 2016 | 1 de julio de 2016 |
| Europa occidental | 23 de junio de 2016 | 1 de julio de 2016 |
| Oeste de EE. UU. | 23 de junio de 2016 | 1 de julio de 2016 |

## Migración manual al Almacenamiento premium
Si desea tener el control en los tiempos de inactividad, puede realizar los pasos siguientes para migrar un almacén de datos existente del Almacenamiento estándar al premium. Si decide realizar la migración manual, debe hacerlo antes de que se inicie la automática en dicha región para evitar riesgos de que esta última cause algún conflicto (consulte el [programa de migración automática][]).

> [AZURE.NOTE] En estos momentos, el servicio Almacenamiento de datos SQL con Almacenamiento premium no dispone de redundancia geográfica. Es decir, una vez que se migre el almacén de datos al Almacenamiento premium, los datos solo residirán en la región actual. Cuando esté disponible esta característica, las copias de seguridad con redundancia geográfica copiarán el almacenamiento de datos cada 24 horas en la [región emparejada de Azure][]; es decir, podrá realizar la restauración a partir de la copia de seguridad con redundancia geográfica en cualquier región de Azure. Una vez que la funcionalidad de copia de seguridad con redundancia geográfica esté disponible en las migraciones manuales, se anunciará en nuestro [sitio de documentación principal][]. En cambio, las migraciones automáticas no cuentan con esta limitación.

### Determinación del tipo de almacenamiento
Si creó un almacenamiento de datos antes de las fechas siguientes, significa que está utilizando el Almacenamiento estándar.

| Region | Almacenamiento de datos creado antes de esta fecha |
| :------------------ | :-------------------------------- |
| Australia Oriental | Almacenamiento premium no disponible todavía |
| Sudeste de Australia | Almacenamiento premium no disponible todavía |
| Centro de Canadá | 25 de mayo de 2016 |
| Este de Canadá | 26 de mayo de 2016 |
| Central EE. UU.: | 26 de mayo de 2016 |
| Este de China | Almacenamiento premium no disponible todavía |
| Asia oriental | 25 de mayo de 2016 |
| Este de EE. UU. | 26 de mayo de 2016 |
| Este de EE. UU. 2 | 27 de mayo de 2016 |
| India central | 27 de mayo de 2016 |
| Sur de India | 26 de mayo de 2016 |
| Este de Japón | Almacenamiento premium no disponible todavía |
| Oeste de Japón | Almacenamiento premium no disponible todavía |
| Centro-Norte de EE. UU | Almacenamiento premium no disponible todavía |
| Europa del Norte | Almacenamiento premium no disponible todavía |
| Centro-Sur de EE. UU | 27 de mayo de 2016 |
| Sudeste asiático | 24 de mayo de 2016 |
| Europa occidental | 25 de mayo de 2016 |
| Oeste de EE. UU. | 26 de mayo de 2016 |


### Instrucciones de migración manual
Si desea tener el control en los tiempos de inactividad, puede migrar manualmente el almacén de datos mediante la funcionalidad de copia de seguridad o restauración. Se espera que el componente de restauración de la migración tarde aproximadamente 1 hora por cada TB de almacenamiento en cada almacenamiento de datos. Si desea conservar el mismo nombre cuando se complete la migración, siga estos pasos de la [solución alternativa de cambio de nombre][].

1.	[Pause][] el almacenamiento de datos que realizará una copia de seguridad automática.
2.	Lleve a cabo la [restauración][] a partir de la instantánea más reciente.
3.	Elimine el almacenamiento de datos existente del Almacenamiento estándar. **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos**.

> [AZURE.NOTE] Esta configuración no se transmitirá como parte de la migración:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

#### Opcional: solución alternativa de cambio de nombre 
Dos bases de datos que se encuentren en el mismo servidor lógico no pueden tener el mismo nombre. En estos momentos, Almacenamiento de datos SQL no permite cambiar nombres de almacenamientos de datos. Gracias a las instrucciones siguientes, no necesitará esta característica aún no implementada durante el proceso de migración manual. Nota: Las migraciones automáticas no cuentan con esta limitación.

Para este ejemplo, imagine que su almacenamiento de datos del Almacenamiento estándar se denomina "miAD".

1.	[Pause][] miAD, que realizará una copia de seguridad automática.
2.	Realice una [restauración][] a partir de la instantánea más reciente de una nueva base de datos con otro nombre, como miADTemp.
3.	Elimine miAD. **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos**.
4.	Como miADTemp es un almacenamiento de datos que se ha creado recientemente, la copia de seguridad no estará disponible para restaurarse a partir de un periodo concreto. Se recomienda proseguir con las operaciones de miADTemp durante un par de horas y, después, continuar con los pasos 5 y 6.
5.	[Pause][] miADTemp, que realizará una copia de seguridad automática.
6.	Realice una [restauración][] a partir de la instantánea miADTemp más reciente de una nueva base de datos con el nombre miAD.
7.	Elimine miADTemp. **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos**.

> [AZURE.NOTE] Esta configuración no se transmitirá como parte de la migración:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the Database level will need to be re-added

## Pasos siguientes
Si tiene problemas con el almacén de datos, [cree una incidencia de soporte técnico][] e indique que la posible causa es la migración al Almacenamiento premium.

<!--Image references-->

<!--Article references-->
[programa de migración automática]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[cree una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[región emparejada de Azure]: ./best-practices-availability-paired-regions.md
[sitio de documentación principal]: ./services/sql-data-warehouse.md
[Pause]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[restauración]: ./sql-data-warehouse-manage-database-restore-portal.md
[solución alternativa de cambio de nombre]: #optional-rename-workaround

<!--MSDN references-->


<!--Other Web references-->
[Almacenamiento premium para poder predecir el rendimiento de manera más eficaz]: https://azure.microsoft.com/es-ES/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/

<!---HONumber=AcomDC_0615_2016-->