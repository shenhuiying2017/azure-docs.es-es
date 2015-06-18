<properties 
   pageTitle="Cambio de los niveles de servicio y niveles de rendimiento de servicio de base de datos" 
   description="Obtenga información acerca de cómo escalar dinámicamente una base de datos en la nube hacia arriba y hacia abajo mediante los niveles de servicio de la Base de datos SQL de Azure, que puede utilizar para marcar el rendimiento hacia arriba y hacia abajo en función de los requisitos de negocio y costo sin tiempo de inactividad de la aplicación." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# Cambio de los niveles de servicio y niveles de rendimiento de servicio de base de datos 

En este tema se describen los pasos necesarios para mover Base de datos SQL de Azure entre los distintos niveles de servicio y niveles de rendimiento.

## Cambio de niveles de servicio 

Utilice la información de [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-new-service-tiers.md) y [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx) para determinar el nivel de capa y el rendimiento de servicio adecuado para la Base de datos SQL de Azure.

Puede moverse fácilmente entre los distintos niveles de servicio mediante el Portal de administración de Azure, [PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) o la [API de REST](https://msdn.microsoft.com/library/dn505719.aspx).

Al moverse entre los niveles de servicio, tenga en cuenta lo siguiente: - Antes de la actualización entre los niveles de servicio o niveles de rendimiento, asegúrese de tener una cuota disponible en el servidor. Si necesita una mayor cuota, llame al servicio de soporte al cliente. -Las bases de datos federadas no se pueden actualizar a los niveles de servicio Basic, Standard o Premium.

- Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. Para obtener más información sobre el tamaño permitido para cada nivel de servicio, consulte la tabla de niveles de servicio y tamaños de bases de datos, que encontrará en esta misma sección.

- Al realizar una degradación desde un nivel de servicio Premium, primero es preciso finalizar todas las relaciones de Replicación geográfica. Puede seguir los pasos que se describen en el tema [Terminar una relación de copia continua](https://msdn.microsoft.com/library/azure/dn741323.aspx) para detener el proceso de replicación entre la base de datos principal y las bases de datos secundarias activas.

- Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).

- Puede realizar un máximo de cuatro cambios de bases de datos individuales (nivel de servicio o niveles de rendimiento) en un periodo de 24 horas.

- Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

Tenga en cuenta que los niveles de servicio Business y Web se retirarán en septiembre de 2015. Para obtener más información, consulte [Preguntas más frecuentes sobre la retirada de las ediciones Web y Business](https://msdn.microsoft.com/library/azure/dn741330.aspx).

<note included> -Es importante tener en cuenta que la implementación actual de [las federaciones finalizará con los niveles de servicio con Web y Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). Se recomienda utilizar [Escalado elástico de Base de datos SQL de Azure](sql-database-elastic-scale-get-started.md) para crear una solución compartida de escala horizontal en Base de datos SQL de Azure. Para probarlo, consulte Introducción a la vista previa del escalado flexible de Base de datos SQL de Azure.

## Actualización a un nivel de servicio superior
Para actualizar una base de datos utilice cualquiera de los métodos siguientes. Los pasos son específicos para actualizar a un nivel de servicio Premium, pero se aplican a todas las actualizaciones.

### Uso del Portal de administración de Azure
1. Use la cuenta Microsoft para iniciar sesión en el Portal de administración de Azure
2. Navegue hasta la pestaña **BASES DE DATOS SQL**.
3. Seleccione una base de datos en la lista **Bases de datos**. La base de datos se abre en el **Panel de base de datos** o en la página **Inicio rápido**.
4. Seleccione la pestaña **Escala** de la base de datos.
5. En la sección **General**, seleccione **PREMIUM** en nivel de servicio.
6. En **Nivel de rendimiento**, seleccione **P1**, **P2** o **P3**. Los recursos que potencian cada nivel de rendimiento se representan en las DTU. Para obtener más información sobre los niveles de rendimiento y las DTU, consulte Niveles de servicio y niveles de rendimiento de base de datos SQL de Azure.
8. En la barra de comandos de la parte inferior de la pantalla, haga clic en el botón **Guardar**.
9. Aparecerá una **confirmación**. Lea la información proporcionada y active la casilla para confirmar.


### Uso de Azure PowerShell
1. Use Set-AzureSqlDatabase para especificar el nivel de rendimiento, el tamaño máximo de la base de datos y el nivel de servicio de la base de datos.  Para obtener una lista de los tamaños de base de datos que admiten los distintos niveles de servicio, consulte Niveles de servicio de Base de datos SQL de Azure (ediciones).
2. Establezca el contexto del servidor con el cmdlet New-AzureSqlDatabaseServerContext. La sintaxis de ejemplo se proporciona en la sección Uso de comandos de PowerShell de Azure.
3. Obtenga un identificador de la base de datos y el nivel de rendimiento de destino. Especifique el nivel de rendimiento con Set-AzureSqlDatabase – ServiceObjective

**Ejemplo de uso** En este ejemplo: - En este ejemplo se muestra la actualización a un nivel de servicio Premium. - Se crea el identificador $db que apunta al nombre de la base de datos "somedb". - Se crea el identificador $P1 y señala al nivel de rendimiento Premium 1. - El nivel de rendimiento de la base de datos $db se establece en $P1.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## Degradación a un nivel de servicio inferior
Utilice uno de los métodos siguientes para degradar una base de datos a un nivel de servicio inferior:

### Uso del Portal de administración de Azure
1. Use la cuenta Microsoft para iniciar sesión en el Portal de administración de Azure
2. Navegue hasta la pestaña **BASES DE DATOS SQL**.
3. Seleccione la pestaña **ESCALA** de la base de datos que desee.
4. En la sección **General**, seleccione el nivel de servicio al que desee realizar la degradación.
5. En la barra de comandos de la parte inferior de la pantalla, haga clic en el botón **Guardar**.
6. Si corresponde, en la página **Confirmación**, lea la información proporcionada y seleccione la casilla para confirmar el cambio.

### Uso de Azure PowerShell
1. Use Set-AzureSqlDatabase para especificar el nivel de servicio, el nivel de rendimiento  y el tamaño máximo de la base de datos
2. Establezca el contexto de servidor mediante New-AzureSqlDatabaseServerContext con la sintaxis de ejemplo proporcionada en la sección Usar comandos de Azure PowerShell.
3. Haga lo siguiente:
 - Obtenga un identificador para la base de datos.
 - Obtenga un identificador para el nivel de rendimiento.
 - Especifique el nivel de servicio, el nivel de rendimiento  y el tamaño máximo de la base de datos con Set-AzureSqlDatabase –ServiceObjective.

**Ejemplo de uso**

Este ejemplo muestra como degradar una base de datos desde el nivel de servicio Premium al nivel de servicio Standard:

- Se crea el identificador $db, que apunta al nombre de la base de datos, "somedb".

- Se crea la variable $S2, que apunta al nivel de rendimiento Standard S2.

- El nivel de rendimiento de la base de datos $db se establece en $S2.

- Especifique el nivel de servicio y el tamaño máximo de la base de datos mediante los parámetros –Edition y –MaxSizeGB. El valor especificado para el parámetro –MaxSizeGB debe ser válido para el nivel de servicio de destino. En este mismo tema se ha incluido tabla con los valores de MaxSize para cada nivel de servicio.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##Cambio de los niveles de rendimiento
Se puede aumentar o reducir los niveles de rendimiento de una base de datos Premium o Standard mediante uno de los métodos siguientes. El cambio del nivel de rendimiento de la base de datos puede tardar un tiempo. Para obtener más información, consulte la sección [Impacto de los cambios en bases de datos Premium](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact), que encontrará a continuación.

Si va a cambiar el nivel de rendimiento de una base de datos Premium que tiene relaciones de Replicación geográfica activa configuradas, utilice el orden siguiente para las bases de datos primaria y secundaria activa:

Esto se debe a que las bases de datos secundarias activas deben tener un nivel de rendimiento igual o mayor que la principal. -Si está cambiando de un nivel de rendimiento superior a un nivel de rendimiento inferior, empiece con la base de datos principal en primer lugar seguido de una o más bases de datos secundarias activas.

- Si va a cambiar de un nivel de rendimiento inferior a un nivel superior, empiece por las bases de datos secundarias activas y deje la principal para el final.

###Uso del Portal de administración de Azure
1. Use la cuenta Microsoft para iniciar sesión en el Portal de administración de Azure
2. Navegue hasta la pestaña **BASES DE DATOS SQL**.
3. Seleccione una base de datos en la lista **Bases de datos**, bien para la cuenta bien para un servidor concreto. La base de datos se abre en el **Panel de base de datos** o en la página **Inicio rápido**.
5. Seleccione la pestaña **Escala** de la base de datos.
6. En la opción de **nivel de rendimiento**, seleccione un nivel de rendimiento.
7. En la barra de comandos de la parte inferior de la pantalla, haga clic en el botón **Guardar**

###Uso de Azure PowerShell
1. Use Set-AzureSqlDatabase para especificar el nivel de rendimiento de la base de datos
2. Establezca el contexto del servidor con el cmdlet New-AzureSqlDatabaseServerContext. La sintaxis de ejemplo se proporciona en la sección Uso de comandos de PowerShell de Azure.
3. Haga lo siguiente:
- Obtenga un identificador para la base de datos.
- Obtenga un identificador para el nivel de rendimiento.
- Especifique el nivel de rendimiento con Set-AzureSqlDatabase – ServiceObjective.

**Ejemplo de uso**

En este ejemplo:

- Se crea el identificador $db, que apunta al nombre de la base de datos, "somedb".

- Se crea el identificador $P2, que apunta al nivel 2 de rendimiento Premium.

- El nivel de rendimiento de la base de datos $db se establece en $P2.

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##Impacto de los cambios en la base de datos
En esta sección se proporciona información sobre los efectos de actualizar a un nivel de servicio Standard o Premium o de realizar cambios en el nivel de rendimiento de la base de datos.

###Tratamiento de las conexiones de la aplicación durante los cambios de la base de datos
Las conexiones a la base de datos pueden interrumpirse temporalmente cuando se completa un cambio de nivel de rendimiento o de nivel de servicio y pueden pasar algunos segundos antes de que se puedan restablecer. Las aplicaciones de Base de datos SQL deben estar codificadas de forma que resistan esta interrupción en las conexiones ya que esto puede ocurrir en cualquier momento en una Base de datos SQL cuando un equipo deja de funcionar en un centro de datos y el servicio de Base de datos SQL conmuta por error la base de datos. La aplicación no requiere cambios de implementación para utilizar una base de datos Premium o para realizar cambios en el nivel de rendimiento de una base de datos Premium.

###Descripción y cálculo de la latencia en los cambios de la base de datos
Un cambio de SLO en una base de datos a menudo implica un movimiento de datos y, por consiguiente, que puedan transcurrir muchas horas hasta que se complete la solicitud de cambios y el cambio de facturación asociado sea efectivo. El movimiento de datos tiene lugar siempre que se producen cambios al degradar una base de datos y también puede producirse si se cambia el nivel de rendimiento de la base de datos.

**Latencia de los cambios de SLO que implican el movimiento de datos**

Tras determinar el tamaño de almacenamiento de la base de datos, la latencia de una solicitud de cambio de SLO se puede evaluar mediante la siguiente heurística:

x 3 (5 minutos + tamaño de base de datos/150 MB por minuto)

Por ejemplo, si el tamaño de la base de datos es de 50 GB, la latencia de la solicitud de cambio de SLO se estima con la heurística siguiente:

3 x (5 minutos + 50 GB x 1024 MB/GB / 150 MB/minuto) ≈17 horas

Las estimaciones de los límites inferior y superior que usan esta heurística varían entre 15 minutos para una base de datos vacía y aproximadamente dos días para una base de datos de 150 GB. Los cálculos pueden variar más según las condiciones del centro de datos.

**Latencia para cambiar de un nivel de rendimiento superior a uno inferior**

Por lo general, no hay movimientos de datos si el nivel de rendimiento de la base de datos se cambia de un nivel de rendimiento superior a un tamaño menor. En tales casos, la latencia del cambio de SLO es mucho menor y este normalmente se completa en cuestión de segundos.

Advertencia: la instrucción anterior sólo se aplica a las degradaciones entre los niveles de servicio Premium y Standard. La degradación a un nivel de servicio Web, Business o Basic implica el movimiento de datos.

###Comprobación del estado del cambio de la base de datos
Puede comprobar el estado de la base de datos durante la actualización o degradación de niveles de servicio, o cuando se cambia el nivel de rendimiento con uno de los métodos siguientes.

####Uso del Portal de administración de Azure
1. Use la cuenta Microsoft para iniciar sesión en el Portal de administración de Azure
2. Seleccione una base de datos en la lista **Bases de datos**. La base de datos se abre en el **Panel de base de datos** o en la página **Inicio rápido**.
3. En el **Panel de bases de datos**, consulte el área **Vista rápida**, donde encontrará información del estado en la sección **Edición**.
4. El objetivo de nivel de servicio (SLO) representa el nivel de rendimiento dentro de un nivel de servicio.


##Uso de comandos de Azure PowerShell
En  esta sección se indican los requisitos previos para utilizar los comandos de Azure PowerShell.

**Requisitos previos**

Para usar los cmdlets de Azure PowerShell que se describen en este tema, debe tener el siguiente software instalado en el equipo en que se ejecuta PowerShell. 1. Descargue una versión de Windows PowerShell, nunca inferior a la 3.0, en http://www.microsoft.com/es-es/download/details.aspx?id=34595.

2. Descargue Azure PowerShell en la sección Herramienta de línea de comandos de [Descargas de SDK y herramientas de Azure](http://azure.microsoft.com/downloads/).

Realice la siguientes operaciones: en la pantalla **Inicio** o en el menú **Inicio**, inicie Azure PowerShell.

Escriba el nombre de usuario y contraseña del servidor.

Cree el contexto de servidor con **New-AzureSqlDatabaseServerContext**.

**Ejemplo**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Referencia de Azure PowerShell** Para ver información detallada sobre los cmdlets de Azure PowerShell usados en este tema, consulte [Cmdlets de Base de datos SQL de Azure](https://msdn.microsoft.com/library/dn546726.aspx).

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)

<!---HONumber=58--> 