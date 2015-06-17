<properties 
	pageTitle="Conexiones a una Base de datos SQL de Azure Recomendaciones centrales" 
	description="Un tema central que proporciona vínculos a temas más específicos sobre diversos controladores, como ADO.NET y PHP, para conectarse a la base de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#Conexiones a la Base de datos SQL: Recomendaciones centrales


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


En este tema se proporcionan vínculos a ejemplos de código para diversas tecnologías que puede usar para conectarse e interactuar con la Base de datos SQL de Azure. Las tecnologías incluyen Enterprise Library, JDBC y PHP. Se ofrecen recomendaciones que generalmente se aplican independientemente de la tecnología de conexión específica.


##Recomendaciones independientes de la tecnología


La información de esta sección se aplica independientemente de la tecnología específica que se use para conectarse a la Base de datos SQL.


- [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](http://msdn.microsoft.com/library/azure/ee336282.aspx) - se incluye lo siguiente:
 - Puertos
 - Firewalls
 - Cadenas de conexión
- [Administración de recursos de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) - se incluye lo siguiente:
 - Regulador de recursos
 - Cumplimiento de límites
 - Limitaciones


Independientemente de la tecnología de conexión que se use, ciertas opciones de firewall para el servidor de Base de datos SQL y e incluso para las bases de datos individuales, son importantes:


- [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###Recomendaciones rápidas


####Conexión


- En la lógica de conexión de cliente, reemplace el tiempo de espera predeterminado para que sea de 30 segundos.
 - El valor predeterminado de 15 segundos es demasiado corto para conexiones que dependen de Internet.
- Si está utilizando un [grupo de conexiones](http://msdn.microsoft.com/library/8xx3tyca.aspx), cierre la conexión en el momento en que el programa no la esté usando activamente y no esté solo preparándose para volver a usarla.
 - A menos que el programa vuelva a usar la conexión para otra operación inmediatamente, sin pausas, se recomienda el siguiente patrón:
<br/><br/>Abrir una conexión.
<br/>Hacer una operación a través de la conexión.
<br/>Cerrar la conexión.<br/><br/>
- Use lógica de reintento con la lógica de conexión, pero solo para los errores transitorios. Cuando se usa la Base de datos SQL, los intentos de abrir una conexión o emitir una consulta pueden producir un error por diversas razones.
 - Un motivo persistente de error podría ser que la cadena de conexión esté mal formada.
 - Un motivo transitorio de error podría ser que el sistema de Base de datos SQL de Azure necesite equilibrar la carga general. El motivo transitorio desaparece por sí mismo, lo que significa que el programa debería volverlo a intentar.
 - Al volver a intentar una consulta, cierre primero la conexión y, después, abra otra.
- Asegúrese de que el [firewall de su Base de datos SQL de Azure](http://msdn.microsoft.com/library/ee621782.aspx) permite la comunicación TCP saliente en el puerto 1433.
 - Puede configurar la configuración del [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) en un servidor de Base de datos SQL o a una base de datos individual.


####Autenticación


- Use la autenticación de Base de datos SQL, no la autenticación de Windows.
- Especifique una base de datos determinada, en lugar de utilizar la base de datos *master*.
- A veces, se debe facilitar el nombre de usuario con el sufijo de *@nombredesuservidor*, pero otras veces se debe omitir el sufijo. Depende de cómo se haya escrito la herramienta o API.
 - Compruebe los detalles de cada tecnología individual.
- Conéctese mediante la especificación de un usuario en una [base de datos independiente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Este enfoque ofrece mejor rendimiento y escalabilidad al evitar la necesidad de un inicio de sesión en la base de datos maestra.
 - No puede utilizar la instrucción **USE nombreDeMiBaseDeDatos;** de Transact-SQL en la Base de datos SQL.


###Errores transitorios


Algunos errores de conexión de Base de datos SQL son persistentes y no hay ninguna razón para intentar la conexión inmediatamente. Otros errores son transitorios y se recomienda que el programa realice unos reintentos automatizados. Ejemplos:


- *Persistente:* Si se escribe mal el nombre del servidor de su Base de datos SQL cuando se intenta conectar, no tiene sentido volverlo a intentar.
- *Transitorio:* Si su conexión funcional con la Base de datos SQL se detiene de forzosamente debido a los sistemas de equilibrio de carga o de limitación de Azure, se recomienda intentar reconectar.


La [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) que genera la llamada a la Base de datos SQL contiene un código de error numérico en su propiedad **Number**. Si el código de error es uno que aparece como un error transitorio, el programa debería reintentar la llamada.


- [Mensajes de error (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - su sección **Errores de pérdida de conexión** es una lista de los errores transitorios que garantizan un reintento.
 - Por ejemplo, inténtelo de nuevo si se produce el número de error 40613, que indica algo similar a<br/>*La base de datos 'mydatabase' en el servidor 'theserver' no está disponible en estos momentos.*


Para obtener más ayuda si se produce un error de conexión permanente o transitorio, consulte:


- [Solucionar problemas de conexión con la Base de datos SQL de Azure](http://support.microsoft.com/es-es/kb/2980233/es-es)


##Tecnologías


El siguiente tema contiene vínculos a ejemplos de código para varias tecnologías de conexión:


- [Desarrollo de Base de datos SQL de Azure: Temas de procedimientos](http://msdn.microsoft.com/library/azure/ee621787.aspx)


Para obtener información sobre ADO.NET con Enterprise Library y las clases de tratamiento de errores transitorios, consulte:


- [Instrucciones acerca de cómo: Conexión de confianza a una Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


Para el Escalado elástico, consulte:


- [Introducción a la vista previa de Escalado elástico de Base de datos SQL de Azure](sql-database-elastic-scale-get-started.md)
- [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md)


##Publicaciones incompletas u obsoletas


Los vínculos de esta sección son entradas de blog u orígenes similares, por lo que podrían estar incompletos u obsoletos. Aun así, podrían tener algún valor de fondo.


- [Lógica de reintento para errores transitorios en Bases de datos SQL de Microsoft Azure](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49--> 