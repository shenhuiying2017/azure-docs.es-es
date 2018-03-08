---
title: Servidores de hospedaje SQL en Azure Stack | Microsoft Docs
description: "Procedimientos para agregar instancias SQL para el aprovisionamiento a través del proveedor de recursos de adaptador de SQL"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 0a29ef133a045b2828777050f2d7a204c0add4a8
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>Agregar servidores de hospedaje para su uso por el adaptador de SQL

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar instancias SQL en las máquinas virtuales dentro de su instancia de [Azure Stack](azure-stack-poc.md), o una instancia fuera de su entorno de Azure Stack, siempre que el proveedor de recursos se pueda conectar a ella. Los requisitos generales son:

* La instancia de SQL debe estar dedicada para ser usada por las cargas de trabajo de usuario y RP. No puede utilizar una instancia de SQL que esté siendo utilizada por otro consumidor, incluido App Services.
* El adaptador de RP no está unido al dominio y solo se puede conectar mediante la autenticación de SQL.
* Debe configurar una cuenta con los privilegios adecuados para que la use el RP.
* El RP y los usuarios, como Web Apps, utilizan la red del usuario, por lo que se requiere conectividad a la instancia SQL en esta red. Este requisito normalmente significa que la dirección IP para las instancias de SQL debe estar en una red pública.
* La administración de las instancias de SQL y sus hosts depende de usted; el RP no realiza la aplicación de revisiones, la copia de seguridad, la rotación de credenciales, etcétera.
* Las SKU pueden utilizarse para crear clases diferentes de capacidades SQL, tales como el rendimiento, la funcionalidad Always On, etcétera.


Varias imágenes de la máquina virtual IaaS de SQL están disponibles a través de la característica Administración de Marketplace. Asegúrese de descargar siempre la versión más reciente de la extensión IaaS de SQL antes de implementar una máquina virtual mediante un elemento de Marketplace. Las imágenes de SQL son las mismas que las máquinas virtuales de SQL disponibles en Azure. Para las máquinas virtuales de SQL creadas a partir de estas imágenes, la extensión de IaaS y las correspondientes mejoras del portal proporcionan características como las funcionalidades de copia de seguridad y de revisión automática.

Hay otras opciones para implementar máquinas virtuales de SQL, incluidas las plantillas de la [Galería de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Cualquier servidor de hospedaje instalado en una instancia de Azure Stack de varios nodos debe crearse a partir de una suscripción de usuario. No se pueden crear desde la suscripción de proveedor predeterminado. Se deben crear desde el portal de usuario o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias de SQL adecuadas. El administrador de servicios _puede_ ser el propietario de dicha suscripción.


### <a name="required-privileges"></a>Privilegios necesarios

Puede crear un nuevo usuario administrativo con menos privilegios de los que tiene sysadmin completo. Las operaciones específicas que tienen que admitirse son:

- Base de datos: crear, modificar, con contención (solo Always On), eliminar, copia de seguridad
- Grupo de disponibilidad: modificar, combinar, agregar o quitar base de datos
- Inicio de sesión: crear, seleccionar, modificar, eliminar, revocar
- Operaciones Select: \[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[sys\].\[dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[sys\].\[availability_groups\] (AlwaysOn), sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Agregar capacidad a través de la conexión a un servidor SQL de hospedaje independiente
Puede usar servidores SQL (que no sean HA) independientes con cualquier edición de SQL Server 2014 o SQL Server 2016. Asegúrese de que tiene las credenciales de una cuenta con privilegios de administrador del sistema.

Para agregar un servidor de hospedaje independiente que ya se haya aprovisionado, siga estos pasos:

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.

2. Haga clic en **Examinar** &gt; **RECURSOS ADMINISTRATIVOS** &gt; **SQL Hosting Servers** (Servidores de hospedaje SQL).

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  La hoja **SQL Hosting Servers** (Servidores de hospedaje SQL) es donde puede conectar el proveedor de recursos del servidor SQL a instancias reales de SQL Server que actúan como back-end del proveedor de recursos.

  ![Servidores de hospedaje](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Rellene el formulario con los detalles de conexión de la instancia de SQL Server.

  ![Nuevo servidor de hospedaje](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcionalmente, puede incluir un nombre de instancia y puede proporcionar un número de puerto si la instancia no está asignada al puerto predeterminado 1433.

  > [!NOTE]
  > Siempre y cuando el usuario y el administrador en Azure Resource Manager puedan acceder a la instancia de SQL, esta puede ponerse bajo el control del proveedor de recursos. La instancia de SQL __debe__ asignarse exclusivamente al RP.

4. A medida que agregue servidores, debe asignarlos a una SKU nueva o existente para diferenciar las ofertas de servicio. Por ejemplo, puede tener una instancia de SQL Enterprise que proporciona:
  - funcionalidad de base de datos
  - copia de seguridad automática
  - reserva de servidores de alto rendimiento para departamentos individuales
  - etcétera.

  El nombre de SKU debe reflejar las propiedades para que los usuarios puedan colocar sus bases de datos de forma adecuada. Todos los servidores de hospedaje en una SKU deben tener las mismas funcionalidades.

    Por ejemplo:

![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> Las SKU pueden tardar hasta una hora en estar visibles en el portal. Los usuarios no pueden crear una base de datos hasta que se cree totalmente la SKU.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Agregar capacidad a través de grupos de disponibilidad SQL Always On
La configuración de instancias de SQL Always On requiere pasos adicionales e implica al menos tres máquinas virtuales (o máquinas físicas).

> [!NOTE]
> El RP del adaptador de SQL _solo_ admite instancias de SQL 2016 SP1 Enterprise o posterior para Always On, ya que requiere nuevas características de SQL como la propagación automática. Además de la lista anterior común de requisitos:

* Debe proporcionar un servidor de archivos además de los equipos SQL Always On. Hay una [plantilla de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) que puede crear este entorno en su lugar. También puede servir como guía para crear su propia instancia.

* Debe configurar los servidores SQL. En concreto, debe habilitar la [propagación automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) en cada grupo de disponibilidad para cada instancia de SQL Server.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

En las instancias secundarias
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



Para agregar servidores de hospedaje SQL Always On, siga estos pasos:

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.

2. Haga clic en **Examinar** &gt; **RECURSOS ADMINISTRATIVOS** &gt; **SQL Hosting Servers** (Servidores de hospedaje SQL)&gt; **+Agregar**.

    La hoja **SQL Hosting Servers** (Servidores de hospedaje SQL) es donde puede conectar el proveedor de recursos del servidor SQL a instancias reales de SQL Server que actúan como back-end del proveedor de recursos.


3. Rellene el formulario con los detalles de conexión de su instancia de SQL Server, asegurándose de usar la dirección IPv4 o el nombre de dominio completo del agente de escucha Always On (y el número de puerto opcional). Proporcione la información de cuenta para la cuenta configurada con los privilegios de administrador del sistema.

4. Active esta casilla para habilitar la compatibilidad con instancias de grupo de disponibilidad SQL Always On.

    ![Servidores de hospedaje](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Agregue la instancia de SQL Always On a una SKU. No puede mezclar servidores independientes con instancias de Always On en la misma SKU. Que se determinará al agregar el primer servidor de hospedaje. Si se intenta mezclar tipos posteriormente, se producirá un error.


## <a name="making-sql-databases-available-to-users"></a>Bases de datos SQL a disposición de los usuarios

Cree planes y ofertas para poner las bases de datos SQL a disposición de los usuarios. Agregue el servicio Microsoft.SqlAdapter al plan y agregue una cuota existente o cree una nueva. Si crea una cuota, puede especificar la capacidad permitida para el usuario.

![Crear planes y ofertas para incluir las bases de datos](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>Mantenimiento del RP del adaptador de SQL

El mantenimiento de las instancias de SQL no se trata aquí, excepto la información de la rotación de contraseñas. Usted es el responsable de la aplicación de revisiones y de la copia de seguridad o recuperación de los servidores de base de datos utilizados con el adaptador de SQL.

### <a name="patching-and-updating"></a>Revisiones y actualizaciones
 El adaptador de SQL no se suministra como parte de Azure Stack, ya que es un componente complementario. Microsoft proporcionará las actualizaciones para el adaptador de SQL según sea necesario. La instancia del adaptador de SQL se crea en la máquina virtual de un _usuario_, en la suscripción del proveedor predeterminado. Por lo tanto, es necesario proporcionar las revisiones de Windows, las firmas de antivirus, etcétera. Los paquetes de actualización de Windows que se proporcionan como parte del ciclo de revisión y actualización se pueden usar para aplicar actualizaciones a la máquina virtual Windows. Cuando se publica un adaptador actualizado, se proporciona un script para aplicar la actualización. Este script crea una nueva máquina virtual de RP y migra cualquier estado que ya tenga.

 ### <a name="backuprestoredisaster-recovery"></a>Copia de seguridad, restauración o recuperación ante desastres
 La copia de seguridad del adaptador de SQL no se realiza como parte del proceso de copia de seguridad y recuperación ante desastres de Azure Stack, ya que es un componente complementario. Se proporcionarán scripts para facilitar:
- La copia de seguridad de la información de estado necesaria (almacenada en una cuenta de almacenamiento de Azure Stack)
- La restauración del RP en caso de que sea necesario llevar a cabo la recuperación de la pila completa.
Los servidores de base de datos deben recuperarse en primer lugar (si es necesario), hasta que se restaure el RP.

### <a name="updating-sql-credentials"></a>Actualización de las credenciales de SQL

Es responsable de crear y mantener las cuentas de administrador del sistema en los servidores SQL Server. El RP necesita una cuenta con estos privilegios para administrar las bases de datos en nombre de los usuarios, no necesita acceso a los datos de esas bases de datos. Si tiene que actualizar las contraseñas de sa en los servidores SQL Server, puede utilizar la funcionalidad de actualización de la interfaz del administrador de RP para cambiar la contraseña almacenada utilizada por el RP. Estas contraseñas se almacenan en un almacén de claves en la instancia de Azure Stack.

Para modificar la configuración, haga clic en **Examinar** &gt; **RECURSOS ADMINISTRATIVOS** &gt; **Servidores de hospedaje SQL** &gt; **Inicios de sesión SQL** y seleccione un nombre de inicio de sesión. El cambio se debe efectuar en la instancia de SQL en primer lugar (y en cualquier réplica, si es necesario). En el panel **Configuración**, haga clic en **Contraseña**.

![Actualización de la contraseña de administrador](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>Pasos siguientes

[Agregar bases de datos](azure-stack-sql-resource-provider-databases.md)
