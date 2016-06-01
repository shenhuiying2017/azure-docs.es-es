<properties
   pageTitle="Habilitación del cifrado de datos transparente (TDE) para SQL Server Stretch Database en Azure TSQL | Microsoft Azure"
   description="Habilitación del cifrado de datos transparente (TDE) para SQL Server Stretch Database en Azure TSQL"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager=""
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/17/2016"
   ms.author="douglaslMS"/>

# Habilitación del cifrado de datos transparente (TDE) para SQL Server Stretch Database en Azure
> [AZURE.SELECTOR]
- [Portal de Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-encryption-tde-tsql.md)

El cifrado de datos transparente (TDE) facilita la protección contra la amenaza de actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo sin requerir cambios en la aplicación.

TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos. La clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de Azure. Microsoft alterna automáticamente estos certificados al menos cada 90 días. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente (TDE)].

##Habilitar el cifrado

Para habilitar TDE para una base de datos de Azure que almacena los datos migrados desde una base de datos SQL Server habilitada para Stretch, haga lo siguiente:

1. Conéctese a la base de datos *maestra* en el servidor que hospeda la base de datos mediante un inicio de sesión que es un administrador o un miembro del rol **dbmanager** en la base de datos maestra.
2. Ejecute la siguiente instrucción para cifrar la base de datos.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##Deshabilitar el cifrado

Para deshabilitar TDE para una base de datos de Azure que almacena los datos migrados desde una base de datos SQL Server habilitada para Stretch, haga lo siguiente:

1. Conéctese a la base de datos *maestra* mediante un inicio de sesión que es un administrador o un miembro del rol **dbmanager** en la base de datos maestra.
2. Ejecute la siguiente instrucción para cifrar la base de datos.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##Comprobación del cifrado

Para comprobar el estado de cifrado de una base de datos de Azure que almacena los datos migrados desde una base de datos de SQL Server habilitada para Stretch, haga lo siguiente:

1. Conéctese a la base de datos *maestra* o de instancia mediante un inicio de sesión que es un administrador o un miembro del rol **dbmanager** en la base de datos maestra.
2. Ejecute la siguiente instrucción para cifrar la base de datos.

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Un resultado de ```1``` indica una base de datos cifrada, ```0``` indica una base de datos no cifrada.


<!--Anchors-->
[Cifrado de datos transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0518_2016-->