---
title: "Ejecución del asistente para habilitar la base de datos para Stretch | Microsoft Docs"
description: "Obtenga información sobre la configuración de una base de datos para Stretch Database ejecutando el asistente para habilitar la base de datos para Stretch."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 1189ab95-ba84-459c-bfb1-57cdf36ee111
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c171da72bbdbfc8c15c6e39fcc8d5000f6be087


---
# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Ejecución del asistente para habilitar la base de datos para Stretch
Para configurar una base de datos para Stretch Database, ejecute el asistente para habilitar la base de datos para Stretch.  Este tema describe la información que tiene que especificar y las elecciones que debe tomar en el asistente.

Para obtener más información sobre Stretch Database, vea [Stretch Database](sql-server-stretch-database-overview.md).

> [!NOTE]
> Posteriormente, al deshabilitar Stretch Database, no olvide que si lo hace en una tabla o una base de datos, no se elimina el objeto remoto. Si quiere eliminar la tabla remota o la base de datos remota, debe hacerlo mediante el Portal de administración de Azure. Los objetos remotos seguirán generando costos de Azure hasta que se eliminan manualmente. 
> 
> 

## <a name="launch-the-wizard"></a>Inicio del asistente
1. En SQL Server Management Studio, en el Explorador de objetos, seleccione la base de datos en la que desee habilitar Stretch.
2. Haga clic con el botón derecho y seleccione **Tareas**, **Stretch** y **Habilitar** para iniciar el asistente.

## <a name="a-nameintroaintroduction"></a><a name="Intro"></a>Introducción
Consulte el propósito del asistente y los requisitos previos.

Entre los requisitos previos importantes se incluyen los siguientes:

* Debe ser administrador para cambiar la configuración de la base de datos.
* Necesita una suscripción a Microsoft Azure.
* Es necesario que su servidor SQL Server pueda comunicarse con el servidor remoto de Azure.

![Página de introducción del asistente para Stretch Database][StretchWizardImage1]

## <a name="a-nametablesaselect-tables"></a><a name="Tables"></a>Selección de tablas
Seleccione las tablas que desee habilitar para Stretch.

Las tablas con muchas filas aparecen en la parte superior de la lista ordenada. Antes de mostrar la lista de tablas, el asistente las analiza para comprobar si hay tipos de datos que no sean compatibles actualmente con Stretch Database.

![Página de selección de tablas del asistente para Stretch Database][StretchWizardImage2]

| Columna | Descripción |
| --- | --- |
| (sin título) |Active la casilla de esta columna para habilitar la tabla seleccionada para Stretch. |
| **Name** |Especifica el nombre de la columna en la tabla. |
| (sin título) |Un símbolo en esta columna puede representar una advertencia que no le impide habilitar la tabla seleccionada para Stretch. También puede representar un problema de bloqueo que impide que se habilite la tabla seleccionada para Stretch, por ejemplo, porque la tabla usa un tipo de datos no admitido. Mantenga el mouse sobre el símbolo para mostrar más información en una información sobre herramientas. Para más información, consulte [Limitaciones del área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md). |
| **Extendida** |Indica si la tabla ya está habilitada para Stretch. |
| **Migrar** |Puede migrar toda una tabla (**Toda la tabla**) o puede especificar un filtro en una columna existente de la tabla. Si desea usar otra función de filtro para seleccionar las filas que se van a migrar, ejecute la instrucción ALTER TABLE para especificar dicha función después de salir del asistente. Para más información sobre la función de filtro, consulte [Select rows to migrate by using a filter function](sql-server-stretch-database-predicate-function.md)(Uso de una función de filtro para seleccionar las filas que se migran). Para más información acerca de cómo aplicar la función, consulte [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md) (Habilitación de Stretch Database en una tabla) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx). |
| **Filas** |Especifica el número de filas de la tabla. |
| **Tamaño (KB)** |Especifica el tamaño de la tabla en KB. |

## <a name="a-namefilteraoptionally-provide-a-row-filter"></a><a name="Filter"></a>Disponibilidad opcional de un filtro de fila
Si desea proporcionar una función de filtro para seleccionar las filas que se van a migrar, realice las siguientes operaciones en la página **Seleccionar tablas** .

1. En la lista **Seleccione las tablas que quiere ajustar**, haga clic **Toda la tabla** en la fila de la tabla. Se abre el cuadro de diálogo **Seleccionar filas para ajustar** .
   
   ![Definición de una función de filtro][StretchWizardImage2a]
2. En el cuadro de diálogo **Seleccionar filas para ajustar**, seleccione **Elegir filas**.
3. En el campo **Nombre**, especifique el nombre de la función de filtro.
4. Para la cláusula **Where** , elija una columna de la tabla y un operador, y especifique un valor.
5. Haga clic en **Comprobar** para probar la función. Si la función devuelve resultados de la tabla (es decir, si hay filas para migrar que cumplan la condición), la prueba indica **Correcto**.
   
   > [!NOTE]
   > El cuadro de texto que muestra la consulta de filtro es de solo lectura. No se puede editar la consulta en él.
   > 
   > 
6. Haga clic en Listo para volver a la página **Seleccionar tablas** .

La función de filtro se crea en SQL Server solo cuando finalice el asistente. Hasta entonces, puede volver a la página **Seleccionar tablas** para cambiar la función de filtro o modificar su nombre.

![Selección de la página Tablas después de definir una función de filtro][StretchWizardImage2b]

Si quiere usar otro tipo de función de filtro para seleccionar las filas que se van a migrar, realice una de las siguientes acciones.  

* Salga del asistente y ejecute la instrucción ALTER TABLE para habilitar Stretch en la tabla y especificar una función de filtro. Para más información, consulte [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md)(Habilitación de Stretch Database en una tabla).  
* Ejecute la instrucción ALTER TABLE para especificar una función de filtro después de salir del asistente. Para conocer los pasos requeridos, consulte [Add a filter function after running the Wizard](sql-server-stretch-database-predicate-function.md#addafterwiz)(Adición de una función de filtro después de ejecutar el asistente).

## <a name="a-nameconfigureaconfigure-azure-deployment"></a><a name="Configure"></a>Configuración de la implementación de Azure
1. Inicie sesión en Microsoft Azure con una cuenta de Microsoft.
   
   ![Inicio de sesión en Azure: asistente para Stretch Database][StretchWizardImage3]
2. Seleccione la suscripción a Azure existente que vaya a usar para Stretch Database.
3. Seleccione una región de Azure.
   
   * Si crea un nuevo servidor, el servidor se crea en esta región.  
   * Si ya hay servidores en la región seleccionada, el asistente los enumera al elegir **Servidor existente**.
   
   Para minimizar la latencia, seleccione la región de Azure en la que se encuentra SQL Server. Para más información sobre las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).
4. Especifique si desea usar un servidor existente o crear un nuevo servidor de Azure.
   
   Si Active Directory en SQL Server está federado con Azure Active Directory, también puede usar una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure. Para más información acerca de los requisitos de esta opción, consulte [Opciones de ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).
   
   * **Creación de un servidor nuevo**
     
     1. Cree un inicio de sesión y una contraseña para el administrador del servidor.
     2. Opcionalmente, use una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure.
     
     ![Crear nuevo servidor de Azure: asistente para Stretch Database][StretchWizardImage4]
   * **Servidor existente**
     
     1. Seleccione el servidor de Azure existente.
     2. Seleccione el método de autenticación
        
        * Si selecciona **Autenticación de SQL Server**, especifique el inicio de sesión y la contraseña de administrador.
        * Seleccione **Autenticación integrada de Active Directory** para usar una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure. Si el servidor seleccionado no está integrado con Azure Active Directory, esta opción no aparece.
     
     ![Seleccionar servidor de Azure existente: asistente para Stretch Database][StretchWizardImage5]

## <a name="a-namecredentialsasecure-credentials"></a><a name="Credentials"></a>Protección de credenciales
Debe tener una clave maestra de base de datos para proteger las credenciales que Stretch Database usa para conectarse a la base de datos remota.  

Si ya existe una clave maestra de base de datos, escriba su contraseña.  

![Página de credenciales seguras del asistente para Stretch Database][StretchWizardImage6b]

Si la base de datos carece de clave maestra, escriba una contraseña segura para crearla.  

![Página de credenciales seguras del asistente para Stretch Database][StretchWizardImage6]

Para obtener más información sobre la clave maestra de base de datos, consulte [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) y [Crear la clave maestra de una base de datos](https://msdn.microsoft.com/library/aa337551.aspx). Para más información sobre la credencial que crea el asistente, consulte [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="a-namenetworkaselect-ip-address"></a><a name="Network"></a>Selección de la dirección IP
Use el intervalo de direcciones IP de la subred (se recomienda), o la dirección IP pública de SQL Server, para crear una regla de firewall en Azure que permita a SQL Server comunicarse con el servidor remoto de Azure.

La dirección o direcciones IP que proporciona en esta página indican al servidor de Azure que permita que los datos entrantes, las consultas y las operaciones de administración iniciadas por SQL Server pasen a través del firewall de Azure. El asistente no cambia nada en la configuración del firewall en SQL Server.

![Página de selección de dirección IP del asistente para Stretch Database][StretchWizardImage7]

## <a name="a-namesummaryasummary"></a><a name="Summary"></a>Resumen
Consulte los valores que escribió y las opciones que seleccionó en el asistente, así como los costos estimados en Azure. Después, seleccione **Finalizar** para habilitar Stretch.

![Página de resumen del asistente para Stretch Database][StretchWizardImage8]

## <a name="a-nameresultsaresults"></a><a name="Results"></a>Resultados
Consulte los resultados.

Para supervisar el estado de la migración de los datos, consulte [Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md)[Supervisión y solución de problemas de migración de datos (Stretch Database)].

![Página de resultados del asistente para Stretch Database][StretchWizardImage9]

## <a name="a-nameknownissuesatroubleshooting-the-wizard"></a><a name="KnownIssues"></a>Solución de problemas del asistente
**Error en el asistente de Stretch Database**
 Si Stretch Database aún no se ha habilitado aún en el nivel de servidor y ejecuta el asistente sin los permisos del administrador del sistema para habilitarlo, se produce un error en el asistente. Solicite al administrador del sistema que habilite Stretch Database en la instancia del servidor local y, a continuación, ejecute de nuevo el asistente. Para más información, consulte [Prerequisite: Permission to enable Stretch Database on the server](sql-server-stretch-database-enable-database.md#EnableTSQLServer)(Requisito previo: permiso para habilitar Stretch Database en el servidor).

## <a name="next-steps"></a>Pasos siguientes
Habilitación tablas adicionales para Stretch Database Supervisión de la migración de datos y administración de tablas y bases de datos habilitadas para Stretch

* [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md) (Habilitación de Stretch Database para una tabla) para habilitar tablas adicionales.
* [Monitor and troubleshoot data migration](sql-server-stretch-database-monitor.md) [Supervisión y solución de problemas de migración de datos (Stretch Database)] para ver el estado de la migración de los datos.
* [Pausa y reanudación Stretch Database](sql-server-stretch-database-pause.md)
* [Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)
* [Copia de seguridad y restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Consulte también
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
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png



<!--HONumber=Nov16_HO2-->


