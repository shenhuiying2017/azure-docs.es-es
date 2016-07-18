<properties
   pageTitle="Compatibilidad de Almacenamiento de datos SQL con clientes de nivel inferior para auditoría de datos | Microsoft Azure"
   description="Obtenga información sobre compatibilidad de Almacenamiento de datos SQL con clientes de nivel inferior para auditoría de datos."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/31/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# Almacenamiento de datos SQL: compatibilidad con clientes de nivel inferior para enmascaramiento de datos dinámicos y auditoría

> [AZURE.SELECTOR]
- [Información general sobre seguridad](sql-data-warehouse-overview-manage-security.md)
- [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Introducción a la auditoría](sql-data-warehouse-auditing-overview.md)
- [Auditoría de los clientes de nivel inferior](sql-data-warehouse-auditing-downlevel-clients.md)


[Auditoría](sql-data-warehouse-auditing-overview.md) funciona con los clientes SQL que admiten el redireccionamiento de TDS.

Cualquier cliente que implementa TDS 7.4 también debe admitir el redireccionamiento. Entre las excepciones a esto se incluyen JDBC 4.0, en el que la función de redireccionamiento no es totalmente compatible y Tedious para Node.JS, en cuya redireccionamiento no se ha implementado.

Para "clientes de nivel inferior", es decir, los que admiten TDS versión 7.3 e inferiores, debe modificarse el FQDN del servidor en la cadena de conexión:

FQDN del servidor original en la cadena de conexión: <*nombre del servidor*>. database.windows.net

FQDN del servidor modificado en la cadena de conexión: <*nombre del servidor*>. database.**secure**.windows.net

Una lista parcial de "Clientes de nivel inferior" incluye:

- .NET 4.0 y versiones posteriores,
- ODBC 10.0 y versiones posteriores.
- JDBC (aunque JDBC admite TDS 7.4, la característica de redireccionamiento de TDS no es totalmente compatible)
- Tedious (para Node.JS)

**Comentario:** la anterior modificación de FDQN de servidor puede resultar útil también para aplicar una directiva de auditoría de nivel de SQL Server sin necesidad de un paso de configuración en cada base de datos (mitigación temporal).

<!---HONumber=AcomDC_0706_2016-->