---
title: "Replicación y conmutación por error de la solución Azure SQL Database | Microsoft Docs"
description: "Aprenda a configurar Azure SQL Database y su aplicación para realizar conmutación por error a una base de datos replicada y probar una conmutación por error."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>Replicación y conmutación por error de una solución Azure SQL Database

En este tutorial, configurará una instancia de Azure SQL Database y una aplicación para realizar conmutación por error a una región remota y, luego, probar el plan de conmutación por error. 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para completar este tutorial, asegúrese de disponer de los siguientes elementos:

- La versión más reciente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Al instalar SSMS, también se instala la versión más reciente de SQLPackage, una utilidad de línea de comandos que puede utilizarse para automatizar diversas tareas de desarrollo de bases de datos. 
- Una base de datos de Azure que desee migrar. En este tutorial se usa la base de datos de ejemplo AdventureWorksLT con el nombre **mySampleDatabase proveniente de uno de estos inicios rápidos:

   - [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
   - [Creación de la base de datos: CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>Creación de usuarios de Azure Active Directory (opcional)

En este paso, creará o identificará usuarios de Azure Active Directory para agregarlos como usuarios a la base de datos de ejemplo y al servidor lógico de Azure SQL Database.
- Si la suscripción es parte de un entorno corporativo de Azure Active Directory con cuentas de usuario existentes, identifique 3 cuentas de usuario para usarlas como el usuario administrativo de Active Directory, el administrativo de la aplicación y el usuario de la aplicación en este tutorial y siga al paso 3: Creación de inicios de sesión y usuarios de SQL Database. 
- Si la suscripción no es parte de un entorno corporativo de Azure Active Directory o forma parte de uno sin cuentas de usuario existentes (y tiene los permisos para crear nuevas cuentas de usuario de Azure Active Directory).

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2. Haga clic en **Más servicios** en el menú de la izquierda.
3. En el cuadro de texto de filtro, escriba **Azure** y, luego, seleccione **Azure Active Directory**.
4. En el panel **Tareas rápidas** de la página **Azure Active Directory**, haga clic en **Agregar un usuario**.
5. En el formulario **Usuario**, cree el usuario siguiente.
   - Nombre: **ad-admin**
   - Nombre de usuario: **ad-admin@yourdomain** (Yopu4708)
6. Active la casilla **Mostrar contraseña** y anote la contraseña de esta cuenta de usuario para usarla más adelante.
7. Haga clic en **Crear**.
8. Repita los 3 pasos anteriores para crear los siguientes 2 nuevos usuarios.
   - Nombre: **app-admin**
   - Nombre de usuario: **app-admin@yourdomain** (Buju4319)
   - Nombre: **app-user**
   - Nombre de usuario: **app-user@yourdomain** (Nonu4001).

9. Abra una nueva ventana del explorador e inicie sesión en Azure Portal con la cuenta **ad-admin** recién creada.
10. En la página **Actualizar contraseña**, escriba la contraseña generada por el sistema en el cuadro **Contraseña actual**. 
11. En los cuadros **Contraseña nueva** y **Confirmar contraseña**, escriba una contraseña de su preferencia.
12. Haga clic en **Actualizar contraseña e iniciar sesión**.

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>Configuración de la integración de SQL Database con Azure Active Directory

1. Haga clic en **Más servicios** en el menú de la izquierda, escriba **sql** en el cuadro de texto del filtro y, luego, seleccione **Servidores SQL Server**.
2. En la página **Servidores SQL Server**, haga clic en su servidor de SQL Database.
3. En el panel Essentials de la página **Información general** del servidor, haga clic en **No configurado** en **Administrador de Active Directory**.
4. En la página **Administrador de Active Directory**, haga clic en **Establecer administrador**.
5. Seleccione la cuenta **ad-admin** de Azure Active Directory (u otra cuenta preexistente, como su propia cuenta) para que sea el administrador del servidor de Azure SQL Database.
6. Haga clic en **Seleccionar**.
7. Haga clic en **Guardar**.

## <a name="create-users-with-permissions-for-your-database"></a>Creación de usuarios con permisos para la base de datos

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

Use SQL Server Management Studio para conectarse a la base de datos y crear cuentas de usuario. Estas cuentas de usuario se replicarán automáticamente al servidor secundario. Es posible que tenga que configurar una regla de firewall si se conecta desde un cliente en una dirección IP para la que todavía no tiene configurado un firewall. Consulte [Creación de SQL DB con Azure Portal](sql-database-get-started-portal.md) para ver los pasos.

1. Abra SQL Server Management Studio.
2. Cambie el modo de **autenticación** a **Autenticación de contraseña de Active Directory**.
3. Conéctese al servidor con la cuenta de administrador de servidor de Azure Active Directory recién diseñada. 
4. En el Explorador de objetos, expanda **Base de datos del sistema**, haga clic con el botón derecho en **mySampleDatabase** y, luego, haga clic en **Nueva consulta**.
5. En la ventana de consulta, ejecute la consulta siguiente para crear una cuenta de usuario en la base de datos y conceder permisos de **db_owner** a las dos cuentas administrativas. Reemplace el marcador de posición del nombre de dominio con su dominio.

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>Creación de firewall de nivel de base de datos

Use SQL Server Management Studio para crear una regla de firewall a nivel de base de datos para su base de datos. Esta regla de firewall a nivel de base de datos se replicará automáticamente al servidor secundario. Con fines de prueba, puede crear una regla de firewall para todas las direcciones IP (0.0.0.0 y 255.255.255.255), puede crear una regla de firewall para la dirección IP única con la que creó la regla de firewall de servidor, o bien puede configurar una o varias reglas de firewall para las direcciones IP de los equipos que desea usar para las pruebas de este tutorial.  

- En la ventana de consulta abierta, reemplace la consulta anterior por la consulta siguiente y reemplace las direcciones IP por las direcciones IP adecuadas para el entorno. 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>Creación de un grupo de conmutación por error 

Elija una región de conmutación por error, cree un servidor vacío en esa región y, luego, cree un grupo de conmutación por error entre el servidor existente y el servidor vacío nuevo.

1. Rellene las variables.

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. Cree un servidor de copia de seguridad vacío en la región de conmutación por error.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. Cree un grupo de conmutación por error.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. Agregue la base de datos al grupo de conmutación por error

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>Incorporación de un servidor de copia de seguridad vacío al dominio

1. En Azure Portal, haga clic en **Más servicios** en el menú de la izquierda, escriba **sql** en el cuadro de texto del filtro y, luego, seleccione **Servidores SQL Server**.
2. En la página **Servidores SQL Server**, haga clic en el nuevo servidor de recuperación ante desastres de SQL Database.
3. En el panel Essentials de la página **Información general** del servidor, haga clic en **No configurado** en **Administrador de Active Directory**.
4. En la página **Administrador de Active Directory**, haga clic en **Establecer administrador**.
5. Seleccione la cuenta **ad-admin** de Azure Active Directory (u otra cuenta preexistente, como su propia cuenta) para que sea el administrador del nuevo servidor de recuperación ante desastres de Azure SQL Database.
6. Haga clic en **Seleccionar**.
7. Haga clic en **Guardar**.

## <a name="prepare-client-tier"></a>Preparación de la capa cliente

1. Cree un perfil de TM con el perfil de conmutación por error AWProfile.
2. Configuración de la supervisión

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>Implementación de la aplicación Java y conexión con la base de datos

<In progress> Consulte [Conexión con Java](sql-database-connect-query-java.md).

<TO DO: change user to app-user>

1. Instale Java 8.
2. Instale Maven.
3. Cree el proyecto de Maven.
4. Agregue lo siguiente a pom.xml 

   - Dependencia

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - Nivel de lenguaje

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - Opción de compilación para admitir archivos de manifiesto en archivos JAR

      ```java
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
5. Agregue lo siguiente al archivo App.java:

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

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
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

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
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

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
6. Guarde el archivo .

## <a name="compile-and-run"></a>Compilación y ejecución

1. Vaya a la consola y ejecute lo siguiente

   ```java
   mvn package
   ```
2. Cuando termine, ejecute la aplicación (se ejecutará aproximadamente durante una hora, a menos de que la detenga manualmente):

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   Si la ejecución se realiza correctamente, el resultado será similar al siguiente:

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful

## <a name="perform-dr-drill"></a>Ejecución de la exploración de la recuperación ante desastres

1. Llame a la conmutación por error manual de FG con conmutación por error forzada. Si la pérdida de datos durante la exploración no es aceptable, debe quitar -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    Deshabilite el punto de conexión principal en el perfil de TM (para desencadenar la conmutación por error del punto de conexión) 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Vuelva a ejecutar la aplicación.


## <a name="relocate-application-to-primary-region"></a>Reubicación de la aplicación en la región primaria

1.    Llame a la conmutación por error sencilla manual de FG. No especifique -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    Deshabilite el punto de conexión secundario (webapp2ep) en el perfil de TM (para desencadenar la conmutación por error del punto de conexión) 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Vuelva a ejecutar la aplicación.

## <a name="troubleshoot-failover"></a>Solución de problemas de conmutación por error 

Sepa cuál es la región primaria ahora para asegurarse de que se produjo la conmutación por error. Se mostraría el rol si se tratara de la base de datos secundaria principal.

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>Pasos siguientes 

- próximamente en un cine cercano
