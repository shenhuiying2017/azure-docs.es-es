---
title: "Implementar una solución distribuida geográficamente de Azure SQL Database| Microsoft Docs"
description: "Aprenda a configurar Azure SQL Database y su aplicación para realizar conmutación por error a una base de datos replicada y probar una conmutación por error."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,business continuity
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: 910be8ff5f9a882c7bb8ae875b8bf5fc74d1fb9a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="implement-a-geo-distributed-database"></a>Implementar una base de datos distribuida geográficamente

En este tutorial, configurará una instancia de Azure SQL Database y una aplicación para realizar conmutación por error a una región remota y, luego, probar el plan de conmutación por error. Aprenderá a: 

> [!div class="checklist"]
> * Crear usuarios de base de datos y concederles permisos
> * Configurar una regla de firewall de nivel de base de datos
> * Crear un [grupo de conmutación por error de replicación geográfica](sql-database-geo-replication-overview.md)
> * Crear y compilar una aplicación de Java para consultar una instancia de Azure SQL Database
> * Obtener detalles de recuperación ante desastres

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

- Versión más reciente de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs) instalada. 
- Instancia de Azure SQL Database instalada. En este tutorial se usa la base de datos de ejemplo AdventureWorksLT con el nombre **mySampleDatabase** proveniente de uno de estos inicios rápidos:

   - [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
   - [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
   - [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

- Si ha identificado un método para ejecutar scripts SQL en la base de datos, puede usar una de las herramientas de consulta siguientes:
   - El editor de consultas de [Azure Portal](https://portal.azure.com). Para más información sobre el empleo del editor de consultas de Azure Portal, vea [Connect and query using Query Editor](sql-database-get-started-portal.md#query-the-sql-database) (Conectar y consultar mediante el editor de consultas).
   - La versión más reciente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), que es un entorno integrado para administrar cualquier infraestructura de SQL, desde SQL Server hasta SQL Database para Microsoft Windows.
   - La versión más reciente de [Visual Studio Code](https://code.visualstudio.com/docs), que es un editor de código gráfico para Linux, macOS y Windows que admite extensiones, incluida la [extensión mssql](https://aka.ms/mssql-marketplace) para realizar consultas en Microsoft SQL Server, Azure SQL Database y SQL Data Warehouse. Para más información sobre el empleo de esta herramienta con Azure SQL Database, vea [Connect and query with VS Code](sql-database-connect-query-vscode.md) (Conectar y consultar con VS Code). 

## <a name="create-database-users-and-grant-permissions"></a>Crear usuarios de base de datos y concederles permisos

Conéctese a la base de datos y cree cuentas de usuario con una de las herramientas de consulta siguientes:

- El editor de consultas de Azure Portal
- SQL Server Management Studio
- Visual Studio Code

Estas cuentas de usuario se replican automáticamente en el servidor secundario (y se mantienen sincronizadas). Para usar SQL Server Management Studio o Visual Studio Code, es posible que tenga que configurar una regla de firewall si se conecta desde un cliente en una dirección IP para la que todavía no tiene configurado un firewall. Para consultar los pasos detallados, vea [Create a server-level firewall rule](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) (Crear una regla de firewall de nivel de servidor).

- En una ventana de consulta, ejecute la consulta siguiente para crear dos cuentas de usuario en la base de datos. Este script concede permisos **db_owner** a la cuenta **app_admin** y permisos **SELECT** y **UPDATE** a la cuenta **app_user**. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Creación de firewall de nivel de base de datos

Cree una [regla de firewall de nivel de base de datos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) para la base de datos SQL. Esta regla de firewall de nivel de base de datos se replica automáticamente en el servidor secundario que se crea en este tutorial. Para simplificar (en este tutorial), use la dirección IP pública del equipo en el que está realizando los pasos de este tutorial. Para determinar la dirección IP usada para la regla de firewall de nivel de servidor del equipo actual, vea [Create a server-level firewall](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) (Crear un firewall de nivel de servidor).  

- En la ventana de consulta abierta, reemplace la consulta anterior por la consulta siguiente y reemplace las direcciones IP por las direcciones IP adecuadas para el entorno.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Crear un grupo de conmutación por error automático de replicación geográfica activa 

Con Azure PowerShell, cree un [grupo de conmutación por error automático de replicación geográfica activa](sql-database-geo-replication-overview.md) entre la instancia existente de Azure SQL Server y la nueva instancia vacía de Azure SQL Server en una región de Azure y luego agregue la base de datos de ejemplo al grupo de conmutación por error.

> [!IMPORTANT]
> Estos cmdlets necesitan Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Rellene las variables de los scripts de PowerShell con los valores del servidor existente y la base de datos de ejemplo y proporcione un valor único global para el nombre del grupo de conmutación por error.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Cree un servidor de copia de seguridad vacío en la región de conmutación por error.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Cree un grupo de conmutación por error entre los dos servidores.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Agregue la base de datos al grupo de conmutación por error.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Instalación del software de Java

En esta sección se da por hecho que está familiarizado con el desarrollo mediante Java y que nunca ha utilizado Azure SQL Database. 

### <a name="mac-os"></a>**Mac OS**
Abra el terminal y desplácese hasta el directorio donde planea crear su proyecto de Java. Escriba los siguientes comandos para instalar **brew** y **Maven**: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Para obtener instrucciones detalladas sobre la instalación y la configuración del entorno de Java y Maven, vaya a [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Java**, **MacOS** y luego siga las instrucciones detalladas para configurar Java y Maven del paso 1.2 y 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear su proyecto de Java. Escriba los siguientes comandos para instalar **Maven**:

```bash
sudo apt-get install maven
```

Para obtener instrucciones detalladas sobre la instalación y la configuración del entorno de Java y Maven, vaya a [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Java**, **Ubuntu** y luego siga las instrucciones detalladas para configurar Java y Maven del paso 1.2, 1.3 y 1.4.

### <a name="windows"></a>**Windows**
Instale [Maven](https://maven.apache.org/download.cgi) con el instalador oficial. Use Maven para ayudar a administrar dependencias, compilar, probar y ejecutar el proyecto de Java. Para obtener instrucciones detalladas sobre la instalación y la configuración del entorno de Java y Maven, vaya a [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Java**, Windows y luego siga las instrucciones detalladas para configurar Java y Maven del paso 1.2 y 1.3.

## <a name="create-sqldbsample-project"></a>Crear proyecto SqlDbSample

1. En la consola de comandos (por ejemplo, Bash), cree un proyecto de Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Escriba **Y** y haga clic en **Entrar**.
3. Cambie el directorio al proyecto recién creado.

   ```bash
   cd SqlDbSamples
   ```

4. Con el editor que prefiera, abra el archivo pom.xml de la carpeta del proyecto. 

5. Agregue la dependencia Microsoft JDBC Driver para SQL Server al proyecto de Maven. Para ello, abra el editor de texto preferido y copie y pegue las líneas siguientes en el archivo pom.xml. No sobrescriba los valores existentes ya rellenados en el archivo. La dependencia JDBC se debe pegar en la sección "dependencias" de mayor tamaño ( ).   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Especifique la versión de Java con la que compilar el proyecto. Para ello, agregue la siguiente sección "propiedades" al archivo pom.xml tras la sección "dependencias". 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Agregue la siguiente sección "compilación" al archivo pom.xml tras la sección "propiedades" para admitir archivos de manifiesto en archivos JAR.       

   ```xml
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
8. Guarde y cierre el archivo pom.xml.
9. Abra el archivo App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) y sustituya el contenido por el siguiente. Sustituya el nombre del grupo de conmutación por error por el nombre de su grupo de conmutación por error. Si ha cambiado los valores de nombre de base de datos, usuario o contraseña, cambie también esos valores.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Guarde y cierre el archivo App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Compilar y ejecutar el proyecto SqlDbSample

1. En la consola de comandos, ejecute el siguiente comando.

   ```bash
   mvn package
   ```
2. Cuando termine, ejecute el siguiente comando para ejecutar la aplicación (se ejecuta durante aproximadamente una hora a menos que detenga manualmente):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Obtener detalles de recuperación ante desastres

1. Llame a la conmutación por error manual del grupo de conmutación por error. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Observe los resultados de la aplicación durante la conmutación por error. Se producirá un error en algunas inserciones mientras se actualiza la caché DNS.     

3. Averigüe qué rol realiza el servidor de recuperación ante desastres.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Conmutación por recuperación.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Observe los resultados de la aplicación durante la conmutación por recuperación. Se producirá un error en algunas inserciones mientras se actualiza la caché DNS.     

6. Averigüe qué rol realiza el servidor de recuperación ante desastres.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Pasos siguientes

Para más información, vea [Grupos de conmutación por error y replicación geográfica activa](sql-database-geo-replication-overview.md).
