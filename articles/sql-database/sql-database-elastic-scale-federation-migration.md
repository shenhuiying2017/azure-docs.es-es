<properties 
    pageTitle="Migración de federaciones" 
    description="Describe los pasos para migrar una aplicación existente creada con la característica Federaciones al modelo de Base de datos elástica." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/14/2015" 
    ms.author="sidneyh"/>

# Migración de federaciones 

La característica Federaciones de Base de datos SQL de Azure se retirará en septiembre de 2015 junto con las ediciones Web/Business. En ese momento, las aplicaciones que utilizan la característica Federaciones dejarán de ejecutarse. Para garantizar una migración exitosa, se recomienda encarecidamente que los esfuerzos de migración comiencen lo antes posible para permitir una planeación y ejecución suficientes.

Si la aplicación aún no está lista para trabajar sin las federaciones, póngase en contacto con el Soporte técnico de Microsoft siguiendo las instrucciones que se mencionan [aquí](https://support.microsoft.com/kb/3087180).

Este documento proporciona el contexto, los ejemplos y una presentación de la utilidad Migración de federaciones que ilustra cómo migrar con éxito una aplicación de Federaciones actual sin problemas a las API de la [biblioteca de cliente de Base de datos elástica](http://go.microsoft.com/?linkid=9862592) para la partición. El objetivo del documento es guiarlo por los pasos sugeridos para migrar una aplicación de Federaciones sin ningún desplazamiento de datos.

Hay tres pasos importantes para la migración de una aplicación de Federaciones existente a una que usa las herramientas de Base de datos elástica.

1. [Creación de un Administrador de asignación de particiones desde una raíz de federación](#create-a-shard-map-manager-from-a-federation-root) 
2. [Modificación de la aplicación existente](#modify-the-existing-application)
3. [Desactivación de los miembros de una federación existente](#switch-out-existing-federation-members)
    

### La herramienta de migración de ejemplo
Para ayudar en este proceso, se creó una [utilidad de Migración de federaciones](http://go.microsoft.com/?linkid=9862613). La utilidad realiza los pasos 1 y 3.

## Creación de un Administrador de asignación de particiones desde una raíz de federación
El primer paso para migrar una aplicación de Federaciones es clonar los metadatos de una raíz de federación a las construcciones de un administrador de mapa de particiones.

![Realizar un clon de la raíz de federación en un Administrador de mapa de particiones][1]
 
Comience con una aplicación de Federaciones existente en un entorno de prueba.
 
Use la **utilidad de Migración de federaciones** para clonar los metadatos raíz de federación en las construcciones del [Administrador de asignación de particiones](http://go.microsoft.com/?linkid=9862595) de la biblioteca de cliente de Base de datos elástica. Análoga a una raíz de federación, la base de datos del Administrador de mapa de partición es una base de datos independiente que contiene los mapas de particiones (es decir, las federaciones), referencias a particiones (es decir, miembros de la federación) y las asignaciones de intervalos respectivas.

La clonación de la raíz de federación en el Administrador de mapa de particiones es una copia y una traducción de los metadatos. En la raíz de federación no se altera ningún metadato. Tenga en cuenta que la clonación de la raíz de federación con la utilidad Migración de federaciones es una operación que se realiza en un momento específico y cualquier cambio en la raíz de federación o en los mapas de particiones no se reflejará en los demás almacenes de datos respectivos. Si se realizan cambios en la raíz de federación durante las pruebas de las nuevas API, es posible usar la utilidad Migración de federaciones para actualizar los mapas de particiones para que representen el estado actual.

![Migración de la aplicación existente para usar las herramientas de las API de Base de datos elástica][2]

## Modificación de la aplicación existente 

Con el Administrador de mapa de particiones implementado y los intervalos y miembros de federación registrados con el Administrador de mapa de particiones (a través de la utilidad de migración), es posible modificar la aplicación existente de Federaciones a fin de utilizar la biblioteca de cliente de Base de datos elástica. Como se ilustra en la figura anterior, las conexiones de aplicación a través de estas API se enrutarán mediante el Administrador de mapa de particiones a los miembros adecuados de la federación (ahora también una partición). La asignación de miembros de federación al Administrador de mapa de particiones permite que dos versiones de una aplicación, una que usa Federaciones y otra que usa la biblioteca de cliente de Base de datos elástica, se ejecuten en paralelo para comprobar la funcionalidad.

Durante la migración de la aplicación, se deberá realizar dos modificaciones centrales en la aplicación existente.


#### Cambio 1: crear una instancia de un objeto del Administrador de mapa de particiones: 

A diferencia de las federaciones, las API de las herramientas de Base de datos elástica interactúan con el Administrador de asignación de particiones a través de la clase **ShardMapManager**. La creación de instancias de un objeto **ShardMapManager** y un mapa de particiones se puede hacer de la siguiente manera:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Cambio 2: enrutar conexiones a la partición adecuada 

Con Federaciones, se establece una conexión a un miembro de federación en especial con el comando USE FEDERATION de la siguiente manera:

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Con las API de las herramientas de Base de datos elástica, se establece una conexión a una partición determinada a través del [enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) con el método **OpenConnectionForKey** en la clase **RangeShardMap**.

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

Los pasos de esta sección son necesarios, pero es probable que no aborden todos los escenarios de migración que surjan. Para obtener más información, consulte la [información general conceptual de las herramientas de Base de datos elástica](sql-database-elastic-scale-introduction.md) y la [referencia de API](http://go.microsoft.com/?linkid=9862604).

## Desactivación de los miembros de una federación existente 

![Desactivación de los miembros de la federación para las particiones][3]

Una vez que se ha modificado la aplicación con la inclusión de las API de herramientas de Base de datos elástica, el último paso de la migración de una aplicación de Federaciones es emitir **SWITCH OUT** para los miembros de la federación (para obtener más información, consulte la referencia de MSDN para [ALTER FEDERATION (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx). El resultado final de emitir **SWITCH OUT** contra un miembro de federación determinado es la eliminación de todas las restricciones de la federación y los metadatos que presentan al miembro de la federación como una Base de datos SQL de Azure normal, no diferente de ninguna otra Base de datos SQL de Azure.

Tenga en cuenta que la emisión de **SWITCH OUT** contra un miembro de la federación es una operación irreversible y no se puede deshacer. Una vez que se realiza, la base de datos resultante no se puede volver a agrear a una federación y los comandos USE FEDERATION dejarán de funcionar para esta base de datos.

Para realizar el cambio, se ha agregado un argumento adicional al comando ALTER FEDERATION para así poder emitir SWITCH OUT sobre un miembro de una federación. A pesar de que el comando se puede emitir contra miembros individuales de la Federación, la utilidad Migración de federaciones proporciona la funcionalidad para iterar de manera programática a través de cada miembro de federación y realizar la operación de cambio.

Una vez que se ha realizado el cambio sobre todos los miembros existentes de la federación, la migración de la aplicación está lista. Cuando todos los miembros de la federación se han desactivado, se recomienda realizar una operación DROP FEDERATION en la raíz. Esto no afectará a ningún miembro anterior, pero permitirá a la base de datos raíz migrar fácilmente de las edicicones Web y Business Edition.
  
La utilidad Migración de federaciones proporciona capacidades para:

1.    Realizar un clon de la raíz de federación en un Administrador de mapa de particiones. Es posible elegir si poner el Administrador de mapa de particiones existente en una base de datos SQL de Azure (recomendado) o en la base de datos de raíz de federación existente.
2.    Emita el comando SWITCH OUT contra todos los miembros de una federación.


## Comparación de características

Aunque las herramientas de Base de datos elástica ofrece muchas características adicionales (por ejemplo, [consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md), [división y combinación de particiones](sql-database-elastic-scale-overview-split-and-merge.md), [elasticidad de partición](sql-database-elastic-scale-elasticity.md), [almacenamiento en caché del lado cliente](sql-database-elastic-scale-shard-map-management.md), etc.), existen algunas características interesantes de Federaciones que no se admiten en las herramientas de Base de datos elástica.
  
- El uso de **FILTERING=ON**. En su lugar, se recomienda que utilice seguridad de nivel de fila (RLS) para filtrar filas. Al igual que el filtrado de federaciones, RLS agrega automáticamente un predicado a todas las consultas en una tabla de particiones. Para obtener más información, consulte [Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md). 
 
 Además, puede crear la lógica de filtrado en la consulta emitida contra la partición. Por ejemplo:

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 Produce el mismo resultado que:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- La característica **División** de las herramientas de Base de datos elástica no es completamente en línea. Durante una operación de división, cada shardlet individual queda sin conexión durante el desplazamiento.
- La característica División requiere aprovisionamiento de la base de datos y administrador de esquemas manual.

## Consideraciones adicionales

* La ediciones Web y Business, además de Federaciones, quedarán obsoletas en otoño de 2015. Como parte de la migración de una aplicación Federaciones, es altamente recomendable realizar pruebas de rendimiento en las ediciones Basic, Standard y Premium. 

* Realizar la instrucción SWITCH OUT sobre un miembro de la federación permite que la base de datos resultante aproveche todas las características de la base de datos SQL de Azure (es decir, ediciones nuevas, copia de seguridad, PITR, auditoría, etc.).

##¿Necesita más tiempo para la migración? 
Si la aplicación aún no está lista para trabajar sin las federaciones, póngase en contacto con el Soporte técnico de Microsoft siguiendo las instrucciones que se mencionan [aquí](https://support.microsoft.com/kb/3087180).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!----HONumber=August15_HO8-->