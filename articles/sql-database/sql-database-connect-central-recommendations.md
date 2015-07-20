<properties 
	pageTitle="Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño" 
	description="Un tema de punto de partida que reúne vínculos y recomendaciones para los programas cliente que se conectan a Base de datos SQL de Azure a partir de tecnologías como ADO.NET y PHP." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="genemi"/>


# Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño


Este tema es un buen lugar para empezar a trabajar con conectividad de cliente a Base de datos SQL de Azure. Proporciona vínculos a ejemplos de código para diversas tecnologías que puede usar para conectarse e interactuar con la Base de datos SQL. Las tecnologías incluyen, entre otras, Enterprise Library, JDBC y PHP. Se ofrecen recomendaciones que generalmente se aplican independientemente de la tecnología de conexión específica o del lenguaje de programación.


## Recomendaciones independientes de la tecnología


La información de esta sección se aplica independientemente de la tecnología específica que se use para conectarse a la Base de datos SQL.


- [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](http://msdn.microsoft.com/library/azure/ee336282.aspx) - Se incluye lo siguiente:
 - Puertos
 - Firewalls
 - Cadenas de conexión
- [Administración de recursos de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) - Se incluye lo siguiente:
 - Regulador de recursos
 - Cumplimiento de límites
 - Limitaciones


Independientemente de la tecnología de conexión que se use, ciertas opciones de firewall para el servidor de Base de datos SQL y e incluso para las bases de datos individuales, son importantes:


- [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## Recomendaciones: conexión


- En la lógica de conexión de cliente, reemplace el tiempo de espera predeterminado para que sea de 30 segundos.
 - El valor predeterminado de 15 segundos es demasiado corto para conexiones que dependen de Internet.
- Si está utilizando un [grupo de conexiones](http://msdn.microsoft.com/library/8xx3tyca.aspx), cierre la conexión en el momento en que el programa no la esté usando activamente y no esté solo preparándose para volver a usarla.
 - A menos que el programa vuelva a usar la conexión para otra operación inmediatamente, sin pausas, se recomienda el siguiente patrón: <br/><br/>Abrir una conexión. <br/>Hacer una operación a través de la conexión. <br/>Cerrar la conexión.<br/><br/>
- Use lógica de reintento con la lógica de conexión, pero solo para los errores transitorios. Cuando se usa la Base de datos SQL, los intentos de abrir una conexión o emitir una consulta pueden producir un error por diversas razones.
 - Un motivo persistente de error podría ser que la cadena de conexión esté mal formada.
 - Un motivo transitorio de error podría ser que el sistema de Base de datos SQL de Azure necesite equilibrar la carga general. El motivo transitorio desaparece por sí mismo, lo que significa que el programa debería volverlo a intentar.
 - Al volver a intentar una consulta, cierre primero la conexión y, después, abra otra.
- Asegúrese de que el [firewall de su Base de datos SQL de Azure](http://msdn.microsoft.com/library/ee621782.aspx) permite la comunicación TCP saliente en el puerto 1433.
 - Puede definir la configuración del [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) en un servidor de Base de datos SQL o en una base de datos individual.


## Recomendación: autenticación


- Use la autenticación de Base de datos SQL, no la autenticación de Windows.
- Especifique una base de datos determinada, en lugar de utilizar la base de datos *maestra* predeterminada.
- A veces, se debe facilitar el nombre de usuario con el sufijo de *@nombredesuservidor*, pero otras veces se debe omitir el sufijo. Depende de cómo se haya escrito la herramienta o API.
 - Compruebe los detalles de cada tecnología individual.
- Conéctese mediante la especificación de un usuario en una [base de datos independiente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Este enfoque ofrece mejor rendimiento y escalabilidad al evitar la necesidad de un inicio de sesión en la base de datos maestra.
 - No puede utilizar la instrucción **USE NombreDeMiBaseDeDatos;** de Transact-SQL en Base de datos SQL.


## Errores transitorios


Los servicios en la nube como Azure y su servicio Base de datos SQL tienen el desafío interminable de equilibrar las cargas de trabajo y administrar los recursos. Si dos bases de datos que se sirven desde el mismo equipo participan en un procesamiento excepcionalmente duro simultáneamente, el sistema de administración podría detectar la necesidad de desplazar la carga de trabajo de una base de datos a otro recurso que tenga exceso de capacidad.


Durante el cambio, la base de datos podría no estar disponible temporalmente. Esto podría bloquear nuevas conexiones o podría provocar que el programa cliente pierda su conexión. Pero el cambio de recursos es transitorio y podría solucionarle por sí solo en un par de minutos o en varios segundos. Una vez completado el cambio, el programa de cliente puede restablecer su conexión y reanudar su trabajo. La pausa en el procesamiento es mejor que un error evitable del programa cliente.


Cuando se produce cualquier error en Base de datos SQL, se inicia [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx). SqlException contiene un código de error numérico en su propiedad **Number**. Si el código de error identifica un error transitorio, el programa debería reintentar la llamada.


- [Mensajes de error (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - Su sección **Errores de pérdida de conexión** es una lista de los errores transitorios que garantizan un reintento automático.
 - Por ejemplo, inténtelo de nuevo si se produce el número de error 40613, que indica algo similar a<br/>*La base de datos 'MiBaseDeDatos' del servidor 'ElServidor' no está actualmente disponible.*


Los *errores* transitorios a veces se denominan *anomalías* transitorias. Este tema considera estos dos términos sinónimos.


Para obtener más ayuda si se produce un error de conexión, ya sea permanente o transitorio, consulte:


- [Solucionar problemas de conexión con Base de datos SQL de Azure](http://support.microsoft.com/kb/2980233/)


## Tecnologías


Los siguientes temas contienen vínculos a ejemplos de código para varios lenguajes y tecnologías de controlador que puede usar para conectarse a Base de datos SQL Azure desde su programa cliente.


Se proporcionan varios ejemplos de código para clientes que se ejecutan en Windows y Linux.


**Ejemplos generales:** hay ejemplos de código para multitud de lenguajes de programación, como PHP, Python, Node.js y .NET CSharp. Además, se proporcionan ejemplos para clientes que se ejecutan en Windows o Linux.


- [Ejemplos de código de desarrollo de cliente y de inicio rápido para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Desarrollo de base de datos SQL de Azure: temas de procedimientos](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Lógica de reintento:** hay ejemplos de código que se han diseñado con la sofisticación necesaria para controlar los errores transitorios con lógica de reintento automático.


- [Conexión confiable a Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Conexión a Base de datos SQL de Azure mediante ADO.NET con Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [Conexión con Base de datos SQL de Azure mediante ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)


**Escalado elástico:** para obtener información acerca de la conectividad a bases de datos de Escalado elástico, vea:


- [Introducción a la vista previa de Escalado elástico de Base de datos SQL de Azure](sql-database-elastic-scale-get-started.md)
- [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliotecas de controlador:** para obtener información acerca de las bibliotecas de controlador de conexión, incluidas las versiones recomendadas, vea:


- [Bibliotecas de conexiones para la base de datos SQL y SQL Server](sql-database-libraries.md)


## Otras referencias


- [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md)

 

<!---HONumber=July15_HO2-->