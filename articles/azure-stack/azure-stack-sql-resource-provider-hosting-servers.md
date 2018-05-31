---
title: Servidores de hospedaje SQL en Azure Stack | Microsoft Docs
description: Procedimientos para agregar instancias SQL para el aprovisionamiento a través del proveedor de recursos de adaptador de SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: jeffgilb
ms.openlocfilehash: e08c0bfd3cbed64f5042e469801e20c913c2f70e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359431"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Incorporación de servidores de hospedaje para el proveedor de recursos SQL
Puede usar instancias SQL en las máquinas virtuales dentro de su instancia de [Azure Stack](azure-stack-poc.md), o una instancia fuera de su entorno de Azure Stack, siempre que el proveedor de recursos se pueda conectar a ella. Los requisitos generales son:

* La instancia de SQL debe estar dedicada para ser usada por las cargas de trabajo de usuario y RP. No puede utilizar una instancia de SQL que esté siendo utilizada por otro consumidor, incluido App Services.
* La máquina virtual del proveedor de recursos SQL no está unida al dominio y solo se puede conectar mediante la autenticación de SQL.
* Debe configurar una cuenta con los privilegios adecuados para que la use el proveedor de recursos.
* El proveedor de recursos y los usuarios, como Web Apps, utilizan la red del usuario, por lo que se requiere conectividad a la instancia SQL en esta red. Este requisito normalmente significa que la dirección IP para las instancias de SQL debe estar en una red pública.
* La administración de las instancias de SQL y sus hosts depende de usted; el proveedor de recursos no realiza la aplicación de revisiones, la copia de seguridad, la rotación de credenciales, etcétera.
* Las SKU pueden utilizarse para crear clases diferentes de capacidades SQL, tales como el rendimiento, la funcionalidad Always On, etcétera.

Varias imágenes de la máquina virtual IaaS de SQL están disponibles a través de la característica Administración de Marketplace. Asegúrese de descargar siempre la versión más reciente de la **extensión IaaS de SQL** antes de implementar una máquina virtual mediante un elemento de Marketplace. Las imágenes de SQL son las mismas que las máquinas virtuales de SQL disponibles en Azure. Para las máquinas virtuales de SQL creadas a partir de estas imágenes, la extensión de IaaS y las correspondientes mejoras del portal proporcionan características como las funcionalidades de copia de seguridad y de revisión automática.

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

> [!IMPORTANT]
> No se admiten caracteres especiales, espacios y puntos incluidos, en los nombres de **familia** o **capa** al crear una SKU para los proveedores de recursos SQL y MySQL.

Por ejemplo:

![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> Las SKU pueden tardar hasta una hora en estar visibles en el portal. Los usuarios no pueden crear una base de datos hasta que se cree totalmente la SKU.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Agregar capacidad a través de grupos de disponibilidad SQL Always On
La configuración de instancias de SQL Always On requiere pasos adicionales e implica al menos tres máquinas virtuales (o máquinas físicas).

> [!NOTE]
> El RP del adaptador de SQL _solo_ admite instancias de SQL 2016 SP1 Enterprise o posterior para Always On, ya que requiere nuevas características de SQL como la propagación automática. Además de la lista anterior común de requisitos:

En concreto, debe habilitar la [propagación automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) en cada grupo de disponibilidad para cada instancia de SQL Server:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Use los siguientes comandos SQL en instancias secundarias:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

Para agregar servidores de hospedaje SQL Always On, siga estos pasos:

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.

2. Haga clic en **Examinar** &gt; **RECURSOS ADMINISTRATIVOS** &gt; **SQL Hosting Servers** (Servidores de hospedaje SQL)&gt; **+Agregar**.

    La hoja **SQL Hosting Servers** (Servidores de hospedaje SQL) es donde puede conectar el proveedor de recursos del servidor SQL a instancias reales de SQL Server que actúan como back-end del proveedor de recursos.

3. Rellene el formulario con los detalles de conexión de su instancia de SQL Server, y asegúrese de usar la dirección FQDN del agente de escucha Always On (y el número de puerto opcional). Proporcione la información de cuenta para la cuenta configurada con los privilegios de administrador del sistema.

4. Active esta casilla para habilitar la compatibilidad con instancias de grupo de disponibilidad SQL Always On.

    ![Servidores de hospedaje](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Agregue la instancia de SQL Always On a una SKU. 

> [!IMPORTANT]
> No puede mezclar servidores independientes con instancias de Always On en la misma SKU. Si se intentan combinar tipos después de agregar los primeros resultados del servidor de hospedaje, se produce un error.


## <a name="making-sql-databases-available-to-users"></a>Bases de datos SQL a disposición de los usuarios

Cree planes y ofertas para poner las bases de datos SQL a disposición de los usuarios. Agregue el servicio Microsoft.SqlAdapter al plan y agregue una cuota existente o cree una nueva. Si crea una cuota, puede especificar la capacidad permitida para el usuario.

![Crear planes y ofertas para incluir las bases de datos](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>Pasos siguientes

[Agregar bases de datos](azure-stack-sql-resource-provider-databases.md)
