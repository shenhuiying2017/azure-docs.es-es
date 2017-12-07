---
title: Uso de Visual Studio y .NET para consultar Azure SQL Database | Microsoft Docs
description: "En este tema se muestra cómo usar Visual Studio para crear un programa que se conecta a Azure SQL Database y realiza consultas mediante instrucciones Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: devcenter
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: carlrab
ms.openlocfilehash: 913055fca46580d870dc2e015f442b431c9ff6e0
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>Uso de .NET (C#) con Visual Studio para conectarse y consultar una base de datos SQL de Azure

Este tutorial de inicio rápido muestra cómo usar [.NET Framework](https://www.microsoft.com/net/) para crear un programa de C# con Visual Studio que se conecta a una base de datos SQL de Azure y utiliza instrucciones Transact-SQL para consultar los datos.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de que dispone de lo siguiente:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Una [regla de firewall de nivel de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para la dirección IP pública del equipo que usa para seguir este tutorial de inicio rápido.

- Una instalación de [Visual Studio Community 2017, Visual Studio Professional 2017 o Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="for-adonet"></a>Para ADO.NET

1. Continúe haciendo clic en **Mostrar las cadenas de conexión de la base de datos**.

2. Revise la cadena de conexión completa de **ADO.NET**.

    ![Cadena de conexión ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Debe tener una regla de firewall activa para la dirección IP pública del equipo en el que sigue este tutorial. Si se encuentra en un equipo diferente o tiene una dirección IP pública diferente, cree una [regla de firewall de nivel de servidor mediante Azure Portal](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-visual-studio-project"></a>Creación de un nuevo proyecto de Visual Studio

1. En Visual Studio, seleccione **Archivo**, **Nuevo**, **Proyecto**. 
2. En el cuadro de diálogo **Nuevo proyecto** expanda **Visual C#**.
3. Seleccione **Aplicación de consola** y escriba *sqltest* como nombre del proyecto.
4. Haga clic en **Aceptar** para crear y abrir el nuevo proyecto en Visual Studio
4. En el Explorador de soluciones, haga clic con el botón derecho en **sqltest** y haga clic en **Administrar paquetes de NuGet**. 
5. En **Examinar**, busque ```System.Data.SqlClient``` y selecciónelo.
6. En la página **System.Data.SqlClient**, haga clic en **Instalar**.
7. Cuando finalice la instalación, revise los cambios y, a continuación, haga clic en **Aceptar** para cerrar la ventana **Vista previa**. 
8. Si aparece una ventana de **Aceptación de licencia**, haga clic en **Acepto**.

## <a name="insert-code-to-query-sql-database"></a>Inserción de código para consultar la base de datos SQL
1. Cambie a (o abra si es necesario) **Program.cs**

2. Reemplace el contenido de **Program.cs** con el siguiente código y agregue los valores apropiados para el servidor, la base de datos, el usuario y la contraseña.

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadLine();
        }
    }
}
```

## <a name="run-the-code"></a>Ejecución del código

1. Presione **F5** para ejecutar la aplicación.
2. Compruebe que se han devuelto las primeras 20 filas y, a continuación, cierre la ventana de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de cómo [conectarse y consultar una base de datos SQL de Azure mediante .NET Core](sql-database-connect-query-dotnet-core.md) en Windows, Linux y macOS.  
- Para más información, consulte [Introducción a .NET Core en Windows, Linux y macOS mediante la línea de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Obtenga información acerca de cómo [diseñar la primera base de datos SQL de Azure con SSMS](sql-database-design-first-database.md) o [diseñar la primera base de datos SQL de Azure mediante .NET](sql-database-design-first-database-csharp.md).
- Para más información acerca de. NET, consulte la [Documentación de .NET](https://docs.microsoft.com/dotnet/).
- [Ejemplo de lógica de reintento: conexión resistente a SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

