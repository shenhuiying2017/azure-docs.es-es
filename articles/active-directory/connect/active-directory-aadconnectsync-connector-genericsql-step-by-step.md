---
title: "Conector de SQL genérico paso a paso | Microsoft Docs"
description: "Este artículo le guía paso a paso por un sencillo sistema de Recursos Humanos con el Conector de SQL genérico."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3fdc1b405b95180d031aa4ad45b406f7fc149d8f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="generic-sql-connector-step-by-step"></a>Conector de SQL genérico paso a paso
Este tema es una guía paso a paso. Crea una sencilla base de datos de ejemplo de Recursos Humanos y la usa para importar algunos usuarios y su pertenencia a grupos.

## <a name="prepare-the-sample-database"></a>Preparación de la base de datos de ejemplo
En un servidor que esté ejecutando SQL Server, ejecute el script de SQL que encontrará en el [Apéndice A](#appendix-a). Este script crea una base de datos de ejemplo con el nombre GSQLDEMO. El modelo de objetos de la base de datos creada es similar al de esta imagen:   
![Modelo de objetos](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

Cree también el usuario que quiera usar para conectarse a la base de datos. En este tutorial, el usuario se llama FABRIKAM\SQLUser y está ubicado en el dominio.

## <a name="create-the-odbc-connection-file"></a>Creación del archivo de conexión de ODBC
El conector de SQL genérico usa ODBC para conectarse al servidor remoto. Primero debemos crear un archivo con la información de conexión de ODBC.

1. Inicie la utilidad de administración de ODBC en el servidor:   
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. Seleccione la pestaña **DSN de archivo**. Haga clic en **Agregar...**.  
   ![ODBC1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. El controlador integrado funciona correctamente, así que selecciónelo y haga clic en **Siguiente>**.  
   ![ODBC2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. Asigne un nombre al archivo, como **GenericSQL**.  
   ![ODBC3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. Haga clic en **Finalizar**.  
   ![ODBC4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. Ahora configuraremos la conexión. Escriba una buena descripción para el origen de datos, así como el nombre del servidor que ejecuta SQL Server.  
   ![ODBC5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. Seleccione la autenticación de SQL. En este caso, usaremos Autenticación de Windows.  
   ![ODBC6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. Proporcione el nombre de la base de datos de ejemplo, **GSQLDEMO**.  
   ![ODBC7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. Mantenga las opciones predeterminadas de esta pantalla. Haga clic en **Finalizar**.  
   ![ODBC8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. Para comprobar que todo funciona según lo esperado, haga clic en **Probar origen de datos**.  
    ![ODBC9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. Asegúrese de que la prueba se ha realizado correctamente.  
    ![ODBC10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. Ahora, el archivo de configuración de ODBC debe de aparecer en DSN de archivo.  
    ![ODBC11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

Ya tenemos el archivo que necesitamos y podemos comenzar a crear el conector.

## <a name="create-the-generic-sql-connector"></a>Creación del conector de SQL genérico
1. En la interfaz de usuario de Synchronization Service Manager, seleccione **Conectores** y **Crear**. Seleccione **SQL genérico (Microsoft)** y asígnele un nombre descriptivo.  
   ![Conector1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. Busque el archivo DSN que ha creado en la sección anterior y cárguelo en el servidor. Proporcione las credenciales para conectarse a la base de datos.  
   ![Conector2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. En este tutorial, hemos optado por una opción sencilla y hemos tenido en cuenta que hay dos tipos de objetos, **User** y **Group**.
   ![Conector3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. Para encontrar los atributos, queremos que el conector los detecte examinando la propia tabla. Puesto que **Users** es una palabra reservada en SQL, debemos escribirla entre corchetes [].  
   ![Conector4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. Ahora definiremos el atributo de delimitador y el atributo DN. Para **Users**, usamos la combinación de los atributos username y EmployeeID. Para **Group**, usamos GroupName (aunque no sea muy realista para la vida real, para este tutorial funciona).
   ![Conector5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. No todos los tipos de atributo se pueden detectar en una base de datos SQL, como sucede con el tipo de atributo de referencia. Para el tipo de objeto de grupo, necesitamos cambiar OwnerID y MemberID de referencia.  
   ![Conector6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. Los atributos que hemos seleccionado como atributos de referencia en el paso anterior requieren el tipo de objeto al que hacen referencia estos valores. En nuestro caso, el tipo de objeto de usuario.  
   ![Conector7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. En la página Parámetros globales, seleccione **Marca de agua** como estrategia diferencial. Escriba también en el formato de fecha y hora **aaaa-MM-dd HH:mm:ss**.
   ![Conector8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. En la página **Configurar particiones y jerarquías** , seleccione ambos tipos de objeto.
   ![Conector9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. En **Seleccionar tipos de objeto** y **Seleccionar atributos**, seleccione los dos tipos de objeto y todos los atributos. En la página **Configurar delimitadores**, haga clic en **Finalizar**.

## <a name="create-run-profiles"></a>Creación de perfiles de ejecución
1. En la interfaz de usuario de Synchronization Service Manager, seleccione **Connectors** (Conectores) y **Configure Run Profiles** (Configurar perfiles de ejecución). Haga clic en **New Profile**(Nuevo perfil). Comenzaremos por **Full Import**(Importación completa).  
   ![Runprofile1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. Seleccione el tipo **Full Import (Stage Only)**(Importación completa [solo fase]).  
   ![Runprofile2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. Seleccione la partición **OBJECT=User**.  
   ![Runprofile3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. Seleccione **Table** (Tabla) y escriba **[USERS]**. Desplácese hacia abajo hasta la sección de tipo de objeto con varios valores y escriba los datos como en la imagen siguiente. Seleccione **Finish** (Finalizar) para guardar la fase.  
   ![Runprofile4a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Runprofile4b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. Seleccione **New step**(Nuevo paso). Esta vez, seleccione **OBJECT=Group**. En la última página, use la configuración como se muestra en la imagen siguiente. Haga clic en **Finalizar**.  
   ![Runprofile5a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Runprofile5b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. Opcional: si lo desea, puede configurar perfiles de ejecución adicionales. En este tutorial se usa solo la importación completa.
7. Haga clic en **OK** (Aceptar) para terminar de cambiar los perfiles de ejecución.

## <a name="add-some-test-data-and-test-the-import"></a>Adición de algunos datos de prueba y prueba de la importación
Rellene algunos datos de prueba en la base de datos de ejemplo. Cuando haya terminado, seleccione **Run** (Ejecutar) y **Full import** (Importación completa).

Este usuario tiene dos números de teléfono y un grupo con algunos miembros.  
![cs1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![cs2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>Apéndice A
**Script de SQL para crear la base de datos de ejemplo**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
