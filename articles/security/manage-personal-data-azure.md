---
title: "Administración de datos personales en Microsoft Azure | Microsoft Docs"
description: "Guía sobre cómo corregir, actualizar, eliminar y exportar datos personales en Azure Active Directory y Azure SQL Database"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 97e8d23d90821489575b3ac6e8f1e810b7ce25a9
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Administración de datos personales en Microsoft Azure

En este artículo se ofrece una guía sobre cómo corregir, actualizar, eliminar y exportar datos personales en Azure Active Directory y Azure SQL Database.

## <a name="scenario"></a>Escenario

Una empresa de Dublín ofrece una plataforma de venta centralizada para bodas con destinos exclusivos en Irlanda y en todo el mundo orientada a una cartera de clientes local e internacional. Tienen oficinas, clientes, empleados y proveedores ubicados en todo el mundo para una cobertura total de los centros que ofrece.

Entre otros muchos elementos, la empresa realiza un seguimiento de RSVP, como las alergias alimentarias y las preferencias dietéticas. Los invitados de la boda pueden registrarse en distintas actividades, como montar a caballo, practicar surf, paseos en barco, etc., e incluso interactuar entre ellos en una página web central durante los meses previos al evento. La empresa recopila información personal de los empleados, los proveedores, los clientes y los invitados de la boda. Debido a la naturaleza internacional de la empresa, la compañía debe cumplir varios niveles de regulación.

## <a name="problem-statement"></a>Declaración del problema

- Los administradores de datos deben tener la capacidad de corregir información personal poco precisa y de actualizar los datos incompletos o realizar modificaciones.

- Asimismo, deben tener la posibilidad de eliminar información personal cuando así lo solicite la persona registrada.

- También deben tener competencias para exportar datos y proporcionarlos a una persona registrada con un formato común y estructurado cuando los solicite.

## <a name="company-goals"></a>Objetivos de la empresa

- La información personal poco precisa o incompleta de clientes, invitados, empleados y proveedores debe corregirse o actualizarse en Azure Active Directory y Azure SQL Database.

- La información personal debe eliminarse de Azure Active Directory y Azure SQL Database a petición de una persona registrada.

- Los datos personales deben exportarse en un formato común y estructurado a petición de una persona registrada.

## <a name="solutions"></a>Soluciones

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: rectificación o corrección de datos personales poco precisos o incompletos y eliminación o borrado de perfiles de usuario o datos personales.

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft.
Puede corregir, actualizar o eliminar los perfiles de usuario de clientes y empleados y la información de trabajo de usuario que contienen datos personales, como el nombre de usuario, el cargo, la dirección o el número de teléfono, en el entorno de [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) mediante [Azure Portal](https://portal.azure.com/).

Tiene que iniciar sesión con una cuenta que sea administrador global en el directorio.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>¿Cómo puedo corregir o actualizar el perfil de usuario y la información laboral en Azure Active Directory?

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. En la hoja **Usuarios y grupos**, seleccione **Usuarios**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. En la hoja **Usuarios y grupos - Usuarios**, seleccione un usuario de la lista y después, en la hoja del usuario seleccionado, seleccione **Perfil** para ver la información de perfil de usuario que hay que corregir o actualizar.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Corrija o actualice la información y, después, en la barra de comandos, seleccione **Guardar**.

6.  En la hoja del usuario seleccionado, seleccione **Información laboral** para ver la información laboral del usuario que debe corregirse o actualizarse.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Corrija o actualice la información laboral del usuario y, después, en la barra de comandos, seleccione **Guardar**.

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>¿Cómo elimino un perfil de usuario en Azure Active Directory?

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

    ![](media/manage-personal-data-azure/image001.png)

3. En la hoja **Usuarios y grupos**, seleccione **Usuarios**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. En la hoja **Usuarios y grupos - Usuarios** , seleccione un usuario de la lista.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. En la hoja del usuario seleccionado, seleccione **Información general** y, después, en la barra de comandos, seleccione **Eliminar**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database: rectificación o corrección de datos personales poco precisos o incompletos, eliminación o borrado de datos personales y exportación de información personal 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) es una base de datos en la nube que ayuda a los desarrolladores a crear y mantener aplicaciones.

Los datos personales pueden actualizarse en [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) mediante consultas SQL estándares, y también se pueden eliminar. Además, los datos personales se pueden exportar desde SQL Database con diferentes métodos, como el Asistente para importación y exportación de Azure SQL Server, y en distintos formatos, como un archivo BACPAC.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>¿Cómo puedo corregir, actualizar o borrar datos personales en SQL Database?

Para obtener información sobre cómo corregir o actualizar datos personales en SQL Database, consulte la documentación sobre [Update (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [Update Text](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [Update With Common Table Expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) o [Update Write Text](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql).

Para obtener información sobre cómo eliminar datos personales en SQL Database, consulte la documentación sobre [Delete (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>¿Cómo puedo exportar datos personales a un archivo BACPAC en SQL Database?

Un archivo BACPAC incluye los metadatos y datos de SQL Database y es un archivo ZIP con una extensión BACPAC. Esto puede hacerse con [Azure Portal](https://portal.azure.com/), la utilidad de línea de comandos SQLPackage, SQL Server Management Studio (SSMS) o PowerShell.

Para obtener información sobre cómo exportar datos a un archivo BACPAC, visite la página [Exportación de una base de datos de Azure SQL Database a un archivo BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export), que incluye instrucciones detalladas para cada método mencionado anteriormente.

¿Cómo puedo exportar datos personales de SQL Database con el Asistente para importación y exportación de SQL Server?

Este asistente ayuda a copiar datos desde un origen a un destino. Para obtener una introducción al asistente, incluida la información sobre cómo obtenerlo, datos sobre permisos y cómo obtener ayuda con la herramienta, visite la página web [Importar y exportar datos con el Asistente para importación y exportación de SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard).

Para obtener información general de los pasos del asistente, visite la página web [Steps in the SQL Server Import and Export Wizard](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) (Pasos del Asistente para importación y exportación de SQL Server).

## <a name="next-steps"></a>Pasos siguientes:

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

