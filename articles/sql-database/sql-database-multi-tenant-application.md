---
title: "Implementación de aplicaciones SaaS multiinquilino con Azure SQL Database | Microsoft Docs"
description: "Implementación de aplicaciones SaaS multiinquilino con Azure SQL Database."
services: sql-database
documentationcenter: 
author: AyoOlubeko
manager: jhubbard
editor: monicar
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/08/2017
ms.author: AyoOlubek
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 80df7b504d13fe1b3be9806eb95e3980d7790970
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="implement-a-multi-tenant-saas-application-using-azure-sql-database"></a>Implementación de una aplicación SaaS multiinquilino con Azure SQL Database

Una aplicación multiinquilino es una aplicación que se hospeda en un entorno de nube y proporciona el mismo conjunto de servicios a cientos o miles de inquilinos que no comparten ni ven los datos de los demás. Un ejemplo es una aplicación SaaS que proporciona servicios a los inquilinos en un entorno hospedado en la nube. Este modelo aísla los datos de cada inquilino y se optimiza la distribución de los recursos para el costo. 

En este tutorial se muestra cómo crear una aplicación SaaS multiinquilino con Azure SQL Database.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Configurar un entorno de base de datos para admitir una aplicación SaaS multiinquilino con el patrón de base de datos por inquilino
> * Crear un inquilino
> * Aprovisionar una base de datos de inquilino y su registro en el catálogo de inquilino
> * Configurar una aplicación Java de ejemplo 
> * Acceder a bases de datos de inquilino y una aplicación de consola Java de ejemplo
> * Eliminar un inquilino

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para completar este tutorial, asegúrese de disponer de los siguientes elementos:
* PowerShell instalado en la máquina y el [SDK de Azure PowerShell más reciente](http://azure.microsoft.com/downloads/)

* La versión más reciente de [SQL Server Management Studio](http://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Al instalar SQL Server Management Studio, también se instala la versión más reciente de SQLPackage, una utilidad de línea de comandos que puede usarse para automatizar diversas tareas de desarrollo de bases de datos.

* [Java Runtime Environment (JRE) 8](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) y el [Kit de desarrollo de JAVA (JDK) más reciente](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) instalados en la máquina. 

* [Apache Maven](https://maven.apache.org/download.cgi) instalado en la máquina. Maven se usará para ayudar a administrar las dependencias, compilar, probar y ejecutar el proyecto Java de ejemplo

## <a name="set-up-data-environment"></a>Configuración del entorno de datos

Aprovisionará una base de datos por inquilino. El modelo de base de datos por inquilino proporciona el mayor grado de aislamiento entre los inquilinos, con un pequeño costo de DevOps. Para optimizar el costo de los recursos de nube, también aprovisionará las bases de datos de inquilino en un grupo elástico, lo que le permitirá optimizar el rendimiento del precio de un grupo de bases de datos. Para información sobre otros modelos de aprovisionamiento de bases de datos, [haga clic aquí](sql-database-design-patterns-multi-tenancy-saas-applications.md#multitenant-data-models). 

Siga estos pasos para crear una instancia de SQL Server y un grupo elástico que hospedará todas las bases de datos de inquilino. 

1. Cree variables para almacenar valores que se usarán en el resto del tutorial. Asegúrese de modificar la variable de dirección IP para incluir su dirección IP 
   
   ```PowerShell 
   # Set an admin login and password for your database
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   
   # Create random unique names for logical server and tenants
   $servername = "server-$(Get-Random)"
   $tenant1 = "geolamice"
   $tenant2 = "ranplex"
   
   # Store current client IP address (modify to include your IP address)
   $startIpAddress = 0.0.0.0 
   $endIpAddress = 0.0.0.1
   ```
   
2. Inicie sesión en Azure y cree una instancia de SQL Server y un grupo elástico 
   
   ```PowerShell
   # Login to Azure 
   Login-AzureRmAccount
   
   # Create resource group 
   New-AzureRmResourceGroup -Name "myResourceGroup" -Location "northcentralus"
   
   # Create logical SQL Server with firewall rules 
   New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
       -ServerName $servername `
       -Location "northcentralus" `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   
   New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
       -ServerName $servername `
       -FirewallRuleName "singleAddress" -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
   
   # Create elastic pool 
   New-AzureRmSqlElasticPool -ResourceGroupName "myResourceGroup"
       -ServerName $servername `
       -ElasticPoolName "myElasticPool" `
       -Edition "Standard" `
       -Dtu 50 `
       -DatabaseDtuMin 10 `
       -DatabaseDtuMax 20
   ```
   
## <a name="create-tenant-catalog"></a>Creación de un catálogo de inquilino 

En una aplicación SaaS multiinquilino, es importante saber dónde se almacena la información de un inquilino. Habitualmente se almacena en una base de datos de catálogo. Esta base de datos de catálogo se usa para contener una asignación entre un inquilino y una base de datos donde se almacenan los datos de ese inquilino.  El patrón básico se aplica ya sea que se use una base de datos de multiinquilino o de inquilino único.

Siga estos pasos para crear una base de datos de catálogo para la aplicación SaaS de ejemplo.

```PowerShell
# Create empty database in pool
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "tenantCatalog" `
    -ElasticPoolName "myElasticPool"

# Create table to track mapping between tenants and their databases
$commandText = "
CREATE TABLE Tenants
(
   TenantId         INT IDENTITY PRIMARY KEY,
   TenantName       NVARCHAR(128) NOT NULL,
   TenantDatabase   NVARCHAR(128) NOT NULL
);

CREATE INDEX IX_TenantName ON Tenants (TenantName);"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant1-and-register-in-tenant-catalog"></a>Aprovisionamiento de la base de datos para "tenant1" y su registro en el catálogo de inquilino 
Use PowerShell para aprovisionar una base de datos para un inquilino "tenant1" nuevo y registre este inquilino en el catálogo. 

```PowerShell
# Create empty database in pool for 'tenant1'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant1');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant1 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register 'tenant1' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant1', '$tenant1');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant2-and-register-in-tenant-catalog"></a>Aprovisionamiento de la base de datos para "tenant2" y su registro en el catálogo de inquilino
Use PowerShell para aprovisionar una base de datos para un inquilino "tenant2" nuevo y registre este inquilino en el catálogo. 

```PowerShell
# Create empty database in pool for 'tenant2'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant2 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant2');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant2 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register tenant 'tenant2' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant2', '$tenant2');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="set-up-sample-java-application"></a>Configuración de una aplicación Java de ejemplo 

1. Cree un proyecto de Maven. Escriba lo siguiente en una ventana del símbolo del sistema:
   
   ```
   mvn archetype:generate -DgroupId=com.microsoft.sqlserver -DartifactId=mssql-jdbc -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```
   
2. Agregue esta dependencia, nivel de lenguaje y la opción de compilación para admitir archivos de manifiesto en archivos JAR al archivo pom.xml:
   
   ```XML
   <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
   </dependency>
   
   <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   
   <build>
        <plugins>
           <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-jar-plugin</artifactId>
              <version>3.0.0</version>
              <configuration>
                 <archive>
                    <manifest>
                       <mainClass>com.sqldbsamples.App</mainClass>
                    </manifest>
                 </archive>
              </configuration>
           </plugin>
        </plugins>
   </build>
   ```

3. Agregue lo siguiente al archivo App.java:

   ```java 
   package com.sqldbsamples;
   
   import java.util.Map;
   
   import java.util.HashMap;
   
   import java.io.BufferedReader;
   
   import java.io.InputStreamReader;
   
   import java.sql.Connection;
   
   import java.sql.Statement;
   
   import java.sql.PreparedStatement;
   
   import java.sql.ResultSet;
   
   import java.sql.DriverManager;
   
   public class App {
   
   private static final String SERVER_NAME = "your-server-name";
   
   private static final String CATALOG_DB_NAME = "tenantCatalog";
   
   private static final String USER = "ServerAdmin";
   
   private static final String PASSWORD = "ChangeYourAdminPassword1";
   
   private static final String CATALOG_DB_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, CATALOG_DB_NAME, USER, PASSWORD);
   
   private static final String CMD_LIST = "LIST";

   private static final String CMD_QUERY = "QUERY";

   private static final String CMD_QUIT = "QUIT";
   
   public static void main(String[] args) {
   
   System.out.println("\n############################");
   
   System.out.println("## SAAS DATABASE TUTORIAL ##");
   
   System.out.println("############################\n");
   
   System.out.println("OPTIONS");
   
   System.out.println(" " + CMD_LIST + " - list tenants");
   
   System.out.println(" " + CMD_QUERY + " <NAME> - connect and tenant query tenant <NAME>");
   
   System.out.println(" " + CMD_QUIT + " - quit the application\n");
   
   try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
   
   while(true) {
   
   String[] input = br.readLine().split("\\s");
   
   if (null != input && input.length > 0) {
   
   if (input[0].equalsIgnoreCase(CMD_LIST)) {
   
   listTenants();
   
   } else if (input[0].toLowerCase().startsWith(CMD_QUERY.toLowerCase()) && input.length == 2) {
   
   queryTenant(input[1].trim());
   
   } else if (input[0].equalsIgnoreCase(CMD_QUIT)) {
   
   break;
   
   } else {
   
   System.out.println(" -> Command not supported");
   
   }
   
   }
   
   System.out.println("");
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void listTenants() {
   
   // List all tenants that currently exist in the system
   
   String sql = "SELECT TenantName FROM Tenants";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   Statement stmt = connection.createStatement();
   
   ResultSet resultSet = stmt.executeQuery(sql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void queryTenant(String name) {
   
   // Query the data that was previously inserted into the primary database from the geo replicated database
   
   String url = null;
   
   String sql = "SELECT TenantDatabase FROM Tenants WHERE TenantName = ?";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   PreparedStatement pstmt = connection.prepareStatement(sql)) {
   
   pstmt.setString(1, name);
   
   try (ResultSet resultSet = pstmt.executeQuery()) {
   
   if (resultSet.next()) {
   
   url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, resultSet.getString(1), USER, PASSWORD);
   
   }
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   if (null != url) {
   
   String tenantSql = "SELECT TenantName FROM WhoAmI";
   
   try (Connection connection = DriverManager.getConnection(url);
   
   Statement stmt = connection.createStatement();

   ResultSet resultSet = stmt.executeQuery(tenantSql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> Entry in table WhoAmI in tenant " + name + " is: " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   } else {
   
   System.out.println(" -> Tenant " + name + " not found");
   
   }
   
   }
   
   }
   ```

4. Guarde el archivo .

5. Vaya a la consola de comandos y ejecute lo siguiente

   ```bash
   mvn package
   ```

6. Cuando termine, ejecute lo siguiente para ejecutar la aplicación 
   
   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```
   
Si la ejecución se realiza correctamente, el resultado será similar al siguiente:

```
############################

## SAAS DATABASE TUTORIAL ##

############################

OPTIONS

LIST - list tenants

QUERY <NAME> - connect and tenant query tenant <NAME>

QUIT - quit the application

* List the tenants

* Query tenants you created
```

## <a name="delete-first-tenant"></a>Eliminación del primer inquilino 
Use PowerShell para eliminar la entrada de base de datos y catálogo de inquilino del primer inquilino.

```PowerShell
# Remove 'tenant1' from catalog 
$commandText = "DELETE FROM Tenants WHERE TenantName = '$tenant1';"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Delete database 
Remove-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1
```

Intente conectarse a "tenant1" con la aplicación Java. Recibirá un error que indica que el inquilino no existe.

## <a name="next-steps"></a>Pasos siguientes 
En este tutorial aprendió a:
> [!div class="checklist"]
> * Configurar un entorno de base de datos para admitir una aplicación SaaS multiinquilino con el patrón de base de datos por inquilino
> * Crear un inquilino
> * Aprovisionar una base de datos de inquilino y su registro en el catálogo de inquilino
> * Configurar una aplicación Java de ejemplo 
> * Acceder a bases de datos de inquilino y una aplicación de consola Java de ejemplo
> * Eliminar un inquilino

* Para ejemplos de tareas comunes de PowerShell, consulte los [ejemplos de PowerShell de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-powershell-samples)

* Para patrones de diseño de aplicaciones SaaS multiinquilino, consulte los [patrones de diseño](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

* Para ejemplos de Java de tareas comunes de Azure, consulte el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/)




