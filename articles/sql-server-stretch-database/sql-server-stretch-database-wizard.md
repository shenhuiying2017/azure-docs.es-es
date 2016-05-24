<properties
	pageTitle="Ejecución del asistente para habilitar la base de datos para Stretch | Microsoft Azure"
	description="Obtenga información sobre la configuración de una base de datos para Stretch Database ejecutando el asistente para habilitar la base de datos para Stretch."
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
	ms.topic="hero-article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Ejecución del asistente para habilitar la base de datos para Stretch

Para configurar una base de datos para Stretch Database, ejecute el asistente para habilitar la base de datos para Stretch. Este tema describe la información que tiene que especificar y las elecciones que debe tomar en el asistente.

Para obtener más información sobre Stretch Database, vea [Stretch Database](sql-server-stretch-database-overview.md).

## Inicio del asistente

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la base de datos en la que desee habilitar Stretch.

2.  Haga clic con el botón derecho y seleccione **Tareas**, **Stretch** y **Habilitar**para iniciar el asistente.

## <a name="Intro"></a>Introducción
Consulte el propósito del asistente y los requisitos previos.

![Página de introducción del asistente para Stretch Database][StretchWizardImage1]

## <a name="Tables"></a>Selección de tablas
Seleccione las tablas que desee habilitar para Stretch.

![Página de selección de tablas del asistente para Stretch Database][StretchWizardImage2]

|Columna|Descripción|
|----------|---------------|
|(sin título)|Active la casilla de esta columna para habilitar la tabla seleccionada para Stretch.|
|**Name**|Especifica el nombre de la columna en la tabla.|
|(sin título)|Un símbolo en esta columna indica normalmente que no puede habilitar la tabla seleccionada para Stretch debido a un problema de bloqueo. Esto puede deberse a que la tabla usa un tipo de datos no compatible. Mantenga el mouse sobre el símbolo para mostrar más información en una información sobre herramientas. Para más información, vea [Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md).|
|**Extendida**|Indica si la tabla ya está habilitada.|
|**Migrar**|Puede migrar una tabla entera (**Toda la tabla**) o puede especificar un predicado de filtro basado en la fecha en el asistente. Si desea usar un predicado de filtro diferente para seleccionar las filas que se van a migrar, ejecute la instrucción ALTER TABLE para especificar el predicado de filtro después de salir del asistente. Para más información sobre el predicado de filtro, consulte [Uso de un predicado de filtro para seleccionar filas para migrar (Stretch Database)](sql-server-stretch-database-predicate-function.md). Para más información sobre cómo aplicar el predicado, consulte [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Filas**|Especifica el número de filas de la tabla.|
|**Tamaño (KB)**|Especifica el tamaño de la tabla en KB.|

## <a name="Filter"></a>Opcionalmente, proporcionar un predicado de filtro basado en la fecha

Si desea proporcionar un predicado de filtro basado en la fecha para seleccionar las filas que se van a migrar, haga lo siguiente en la página **Seleccionar tablas**.

1.  En la lista **Seleccione las tablas que desea expandir**, haga clic **Toda la tabla** en la fila de la tabla. Se abre el cuadro de diálogo **Seleccionar las filas para expandir**.

    ![Definición de un predicado de filtro basado en la fecha][StretchWizardImage2a]

2.  En el cuadro de diálogo **Seleccionar las filas para expandir**, seleccione **Elegir filas**.

3.  En el campo **Nombre**, proporcione un nombre para el predicado de filtro.

4.  En la cláusula **Where**, elija una columna de fecha de la tabla, seleccione un operador y proporcione un valor de fecha.

5. Haga clic en **Comprobar** para probar el predicado. Si el predicado devuelve resultados de la tabla, es decir, si hay filas para migrar que satisfacen la condición, la prueba indica **Correcto**.

6.  Haga clic en Hecho para volver a la página **Seleccionar tablas**.

    ![Selección de la página Tablas después de definir un predicado de filtro][StretchWizardImage2b]

## <a name="Configure"></a>Configuración de la implementación de Azure

1.  Inicie sesión en Microsoft Azure con una cuenta de Microsoft.

    ![Inicio de sesión en Azure: asistente para Stretch Database][StretchWizardImage3]

2.  Seleccione la suscripción de Azure que usar para Stretch Database.

3.  Seleccione una región de Azure. Si crea un nuevo servidor, el servidor se crea en esta región.

    Para minimizar la latencia, seleccione la región de Azure en la que se encuentra SQL Server. Para más información sobre las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

4.  Especifique si desea usar un servidor existente o crear un nuevo servidor de Azure.

    Si Active Directory en SQL Server está federado con Azure Active Directory, también puede usar una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure. Para más información sobre los requisitos para esta opción, consulte [Opciones de ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Creación de un servidor nuevo**

        1.  Cree un inicio de sesión y una contraseña para el administrador del servidor.

        2.  Opcionalmente, use una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure.

		![Crear nuevo servidor de Azure: asistente para Stretch Database][StretchWizardImage4]

    -   **Servidor existente**

        1.  Seleccione o escriba el nombre del servidor de Azure existente.

        2.  Seleccione el método de autenticación

            -   Si selecciona **Autenticación de SQL Server**, cree un inicio de sesión y una contraseña nuevos.

            -   Seleccione **Autenticación integrada de Active Directory** para usar una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure.

		![Seleccionar servidor de Azure existente: asistente para Stretch Database][StretchWizardImage5]

## <a name="Credentials"></a>Protección de credenciales
Escriba una contraseña segura para crear una clave maestra de base de datos, o si ya existe una clave maestra de base de datos, escriba la contraseña para ella.

Debe tener una clave maestra de base de datos para proteger las credenciales que Stretch Database usa para conectarse a la base de datos remota.

![Página de credenciales seguras del asistente para Stretch Database][StretchWizardImage6]

Para más información sobre la clave maestra de base de datos, consulte [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) y [Crear la clave maestra de una base de datos](https://msdn.microsoft.com/library/aa337551.aspx). Para más información sobre la credencial que crea el asistente, consulte [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selección de la dirección IP
Use la dirección IP pública de SQL Server, o escriba un intervalo de direcciones IP, para crear una regla de firewall en Azure que permita a SQL Server comunicarse con el servidor remoto de Azure.

La dirección o direcciones IP que proporciona en esta página indican al servidor de Azure que permita que los datos entrantes, las consultas y las operaciones de administración iniciadas por SQL Server pasen a través del firewall de Azure. El asistente no cambia nada en la configuración del firewall en SQL Server.

![Página de selección de dirección IP del asistente para Stretch Database][StretchWizardImage7]

## <a name="Summary"></a>Resumen
Consulte los valores que escribió y las opciones que seleccionó en el asistente. Después, seleccione **Finalizar** para habilitar Stretch.

![Página de resumen del asistente para Stretch Database][StretchWizardImage8]

## <a name="Results"></a>Resultados
Consulte los resultados.

Opcionalmente, seleccione **Monitor** para iniciar el monitor del estado de la migración de datos en Stretch Database Monitor. Para más información, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Solución de problemas del asistente
**Error en el asistente de Stretch Database** Si Stretch Database aún no se ha habilitado aún en el nivel de servidor y ejecuta el asistente sin los permisos del administrador del sistema para habilitarlo, se produce un error en el asistente. Solicite al administrador del sistema que habilite Stretch Database en la instancia del servidor local y, a continuación, ejecute de nuevo el asistente. Para más información, consulte [Requisito previo: Permiso para habilitar Stretch Database en el servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Pasos siguientes
Habilitación tablas adicionales para Stretch Database Supervisión de la migración de datos y administración de tablas y bases de datos habilitadas para Stretch

-   [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md) para habilitar tablas adicionales

-   [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md) para ver el estado de la migración de datos

-   [Pausa y reanudación Stretch Database](sql-server-stretch-database-pause.md)

-   [Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

-   [Copia de seguridad y restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

## Consulte también

[Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md)

[Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png

<!---HONumber=AcomDC_0518_2016-->