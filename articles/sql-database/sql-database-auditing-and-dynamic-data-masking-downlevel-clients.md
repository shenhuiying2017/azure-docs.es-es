<properties 
	pageTitle="Base de datos SQL: compatibilidad con clientes de nivel inferior de enmascaramiento de datos dinámicos y auditoría | Azure" 
	description="Base de datos SQL: compatibilidad con clientes de nivel inferior de enmascaramiento de datos dinámicos y auditoría" 
	services="sql-database" 
	documentationCenter="" 
	authors="nadavhelfman" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="nadavhelfman"/>
 
# Base de datos SQL: compatibilidad con clientes de nivel inferior de enmascaramiento de datos dinámicos y auditoría 


[Auditoría](sql-database-auditing-get-started.md) y [Enmascaramiento de datos dinámicos](sql-database-dynamic-data-masking-get-started.md) funcionan con los clientes SQL que admiten el redireccionamiento de TDS.

Cualquier cliente que implementa TDS 7.4 también debe admitir el redireccionamiento. Entre las excepciones a esto se incluyen JDBC 4.0, en el que la función de redireccionamiento no es totalmente compatible y Tedious para Node.JS, en cuya redireccionamiento no se ha implementado.

Para "clientes de nivel inferior", es decir, los que admiten TDS versión 7.3 e inferiores, debe modificarse el FQDN del servidor en la cadena de conexión:

FQDN del servidor original en la cadena de conexión: <*nombre del servidor*>. database.windows.net

FQDN del servidor modificado en la cadena de conexión: <*nombre del servidor*>. database.**secure**.windows.net

Una lista parcial de "Clientes de nivel inferior" incluye:

- .NET 4.0 y versiones posteriores,
- ODBC 10.0 y versiones posteriores.
- JDBC 4.0 y versiones posteriores (aunque JDBC 4.0 admite TDS 7.4, la función de redireccionamiento de TDS no es totalmente compatible)
- Tedious (para Node.JS)

**Comentario:** la anterior modificación de FDQN de servidor puede resultar útil también para aplicar una directiva de auditoría de nivel de SQL Server sin necesidad de un paso de configuración en cada base de datos (mitigación temporal).

 

<!---HONumber=August15_HO6-->