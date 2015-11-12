<properties
   pageTitle="Cambio de nombre en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para cambiar el nombre de objetos y bases de datos en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# Cambio de nombre en el Almacenamiento de datos SQL
SQL Server admite el cambio de nombre de base de datos mediante el procedimiento almacenado ```sp_renamedb```. El Almacenamiento de datos SQL usa la sintaxis DDL para lograr el mismo objetivo. El comando DDL es ```RENAME DATABASE```.

## Cambio de nombre de bases de datos

Para cambiar el nombre de una base de datos, ejecute el siguiente comando.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

Es importante recordar que se no se puede cambiar el nombre de un objeto o una base de datos si otros usuarios están conectados a ellos o los están utilizando. Puede que primero sea necesario terminar sesiones abiertas. Para terminar una sesión deberá usar el comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx). Tenga cuidado cuando use ```KILL```. Una vez ejecutado, se terminará la sesión de destino y se revertirá cualquier trabajo no confirmado.

> [AZURE.NOTE] Las sesiones del Almacenamiento de datos SQL están precedidas por “SID”; deberá incluir esto y el número de sesión al invocar el comando KILL. Por ejemplo, ```KILL 'SID1234'``` terminaría la sesión 1234, suponiendo que tenga los permisos adecuados para ejecutarlo.

## Finalización de sesiones
Para cambiar el nombre de una base de datos, puede que necesite terminar las sesiones conectadas al Almacenamiento de datos SQL. La siguiente consulta generará una lista distinta de comandos KILL para borrar las conexiones (Guardar para la sesión actual).

```
SELECT
    'KILL ''' + session_id + ''''
FROM
    sys.dm_pdw_exec_requests r
WHERE
    r.session_id <> SESSION_ID()
    AND EXISTS
    (
        SELECT
            *
        FROM
            sys.dm_pdw_lock_waits w
        WHERE
            r.session_id = w.session_id
    )
GROUP BY
    session_id;
```

## Cambio de esquemas
Si la intención es cambiar el esquema al que un objeto pertenece, esto se consigue con `ALTER SCHEMA`:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!----HONumber=Oct15_HO4-->