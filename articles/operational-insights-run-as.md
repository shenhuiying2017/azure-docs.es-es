<properties 
   pageTitle="Cuentas de ejecución de Operations Manager para Operational Insights"
   description="Aprenda a configurar cuentas de ejecución de Operations Manager para usar con Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Cuentas de ejecución de Operations Manager para Operational Insights

Microsoft Azure Operational Insights usa el agente y el grupo de administración de Operations Manager para recopilar y enviar datos al servicio de Operational Insights. Operational Insights se basa en paquetes de administración de cargas de trabajo para proporcionar servicios de valor añadido. Cada carga de trabajo requiere privilegios específicos de carga de trabajo para ejecutar paquetes de administración en un contexto de seguridad diferente, como una cuenta de dominio. Debe proporcionar información de credenciales mediante la configuración de una cuenta de ejecución de Operations Manager.

En las siguientes secciones se describe cómo definir cuentas de ejecución de Operations Manager para las siguientes cargas de trabajo:

- Evaluación de SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

## Definición de la cuenta de ejecución para la evaluación de SQL

 Si ya está usando el paquete de administración de SQL Server, debe utilizar esa cuenta de ejecución.

### Para configurar la cuenta de ejecución de SQL en la consola de Operations, siga estos pasos:

1. En Operations Manager, abra la consola de Operations y luego haga clic en **Administración**.

2. En **Configuración de ejecución**, haga clic en **Perfiles** y abra **Perfil de ejecución de evaluación de SQL para Operational Insights**.

3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.

4. Seleccione una cuenta de ejecución de Windows que contenga las credenciales necesarias para SQL o haga clic en **Nuevo** para crear una.
	>[AZURE.NOTE] El tipo de cuenta de ejecución debe ser Windows. La cuenta de ejecución también debe ser parte del grupo de administradores locales en todos los servidores de Windows que hospedan instancias de SQL Server.

5. Haga clic en **Guardar**.

6. Modifique y luego ejecute el siguiente ejemplo de T-SQL en cada instancia de SQL Server para conceder los permisos mínimos que necesita la cuenta de ejecución para realizar la evaluación de SQL. Sin embargo, este paso no es necesario si una cuenta de ejecución ya forma parte del rol de servidor sysadmin en las instancias de SQL Server.

---
    -- Sustituya <UserName> por el nombre de usuario real usado como cuenta de ejecución.
    USE master
    
    -- Cree un inicio de sesión para el usuario, comente esta línea si el inicio de sesión ya se ha creado.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- Conceda permisos al usuario.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- Agregue el usuario de base de datos para todas las bases de datos de instancias de SQL Server; esto es necesario para conectarse a bases de datos individuales.
    -- NOTA: Este comando se debe ejecutar cada vez que se agreguen nuevas bases de datos a instancias de SQL Server.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### Para configurar la cuenta de ejecución de SQL mediante Windows PowerShell, siga estos pasos:

Abra una ventana de PowerShell y ejecute el siguiente script después de actualizarlo con su información:

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Definición de la cuenta de ejecución para Virtual Machine Manager

Asegúrese de que la cuenta de ejecución tenga privilegios para las siguientes acciones:

- Usar el módulo VMM Windows PowerShell

- Consultar la base de datos de VMM

- Administrar de forma remota los agentes de VMM que se ejecutan en los hosts de virtualización

Lleve a cabo los siguientes pasos para definir la cuenta cuando conecte Operational Insights a Operations Manager.

### Para definir credenciales para VMM, siga estos pasos:

1. En Operations Manager, abra la consola de Operations y luego haga clic en **Administración**.

2. En **Configuración de ejecución**, haga clic en **Perfiles** y abra **Cuenta de ejecución de Operational Insights VMM**.

3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.

4. Seleccione una cuenta de ejecución de Windows que contenga las credenciales necesarias para VMM o haga clic en **Nuevo** para crear una.
	>[AZURE.NOTE] El tipo de cuenta de ejecución debe ser Windows.

5. Haga clic en **Save**.


## Definición de la cuenta de ejecución para Lync Server

 La cuenta de ejecución debe ser miembro de los grupos de seguridad de administradores locales y RTCUniversalUserAdmins de Lync.

### Para establecer las credenciales para una cuenta de Lync, siga estos pasos:

1. En Operations Manager, abra la consola de Operations y luego haga clic en **Administración**.

2. En **Configuración de ejecución**, haga clic en **Perfiles** y abra **Cuenta de ejecución de Operational Insights Lync**.

3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.

4. Seleccione una cuenta de ejecución de Windows que sea miembro de los grupos de seguridad de administradores locales y RTCUniversalUserAdmins de Lync. 
	>[AZURE.NOTE] El tipo de cuenta de ejecución debe ser Windows.

5. Haga clic en **Save**.


## Definición de la cuenta de ejecución para SharePoint


### Para establecer las credenciales para una cuenta de SharePoint, siga estos pasos:

1. En Operations Manager, abra la consola de Operations y luego haga clic en **Administración**.

2. En **Configuración de ejecución**, haga clic en **Perfiles** y abra **Cuenta de ejecución de Operational Insights SharePoint**.

3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.

4. Seleccione una cuenta de ejecución de Windows que contenga las credenciales necesarias para SharePoint o haga clic en **Nuevo** para crear una. 
	>[AZURE.NOTE] El tipo de cuenta de ejecución debe ser Windows.

5. Haga clic en **Save**.



<!--HONumber=52-->