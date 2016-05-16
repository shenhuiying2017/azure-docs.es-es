<properties 
    pageTitle="Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure | Microsoft Azure" 
    description="Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


En este artículo se muestra cómo configurar la replicación geográfica para Base de datos SQL con el [Portal de Azure](http://portal.azure.com).

Para iniciar la conmutación por error, vea [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] La replicación geográfica activa (bases de datos secundarias legibles) está ahora disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no legible y las bases de datos no legibles existentes se actualizarán automáticamente a secundarias legibles.

Puede configurar hasta cuatro bases de datos secundarias legibles en las mismas ubicaciones de centros de datos o en otras (regiones). Las bases de datos secundarias están disponibles en caso de una interrupción del centro de datos o de imposibilidad para conectarse a la base de datos principal.

Para configurar la replicación geográfica, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una base de datos de Base de datos SQL de Azure: la base de datos principal que quiere replicar en una región geográfica diferente.



## Agregar una base de datos secundaria

Los pasos siguientes crean otra base de datos secundaria en una asociación de replicación geográfica.

Para agregar una base de datos secundaria debe ser el propietario o copropietario de la suscripción.

La base de datos secundaria tendrá el mismo nombre que la base de datos principal y, de forma predeterminada, tienen el mismo nivel de servicio. La base de datos secundaria puede ser legible o no legible, y puede ser una base de datos única o elástica. Para más información, vea [Niveles de servicio](sql-database-service-tiers.md). Después de crear e inicializar la base de datos secundaria, los datos comenzarán a replicarse desde la base de datos principal a la nueva base de datos secundaria.

> [AZURE.NOTE] Si la base de datos del asociado ya existe (por ejemplo, como resultado de la terminación de una relación de replicación geográfica anterior) se producirá un error en el comando.




### Adición de una secundaria

1. En el [Portal de Azure](http://portal.azure.com), vaya a la base de datos que desea configurar para replicación geográfica.
2. En la hoja Base de datos SQL, seleccione **Toda la configuración** > **Replicación geográfica**.
3. Seleccione la región para crear la base de datos secundaria.


    ![Adición de una secundaria][1]


4. Configure el **Tipo secundario** (**Legible**, o **No legible**).
5. Seleccione o configure el servidor de la base de datos secundaria.

    ![Creación de una secundaria][3]

5. También puede agregar una base de datos secundaria a un grupo de bases de datos elásticas:

       - Haga clic en **Grupo de bases de datos elásticas** y seleccione un grupo en el servidor de destino en el que crear la base de datos secundaria. Ya debe existir un grupo en el servidor de destino ya que este flujo de trabajo no crea ninguno.

6. Haga clic en **Crear** para agregar la base de datos secundaria.
 
6. Se crea la base de datos secundaria y comienza el proceso de inicialización.
 
    ![inicialización][6]

7. Cuando se completa el proceso de inicialización la base de datos secundaria muestra su estado (no legible).

    ![secundaria lista][9]



## Elimine una base de datos secundaria

La operación termina de forma permanente la replicación en la base de datos secundaria y el rol de la base de datos secundaria cambia al de una base de datos de lectura y escritura normal. Si se interrumpe la conectividad a la base de datos secundaria, el comando se ejecuta correctamente pero la base de datos secundaria no pasará a ser de lectura y escritura hasta después de restaurarse la conectividad.

1. En el [Portal de Azure](http://portal.azure.com), vaya a la base de datos principal de la asociación de replicación geográfica.
2. En la hoja Base de datos SQL, seleccione **Toda la configuración** > **Replicación geográfica**.
3. En la lista **SECUNDARIAS**, seleccione la base de datos que quiere quitar de la asociación de replicación geográfica.
4. Haga clic en **Detener replicación**.

    ![quitar secundaria][7]


5. Al hacer clic en **Detener replicación** se abre una ventana de confirmación, haga clic en **Sí** para quitar la base de datos de la asociación de replicación geográfica (establézcala en una base de datos de lectura y escritura que no forma parte de ninguna replicación).


    ![confirmar eliminación][8]



   

## Pasos siguientes

- [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure](sql-database-geo-replication-failover-portal.md)
- [Diseño de aplicaciones de nube para la continuidad del negocio mediante replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)


## Recursos adicionales

- [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md)
- [Lo más destacado de las nuevas funcionalidades de replicación geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0504_2016-->