---
title: "Protección de la base de datos de Azure SQL | Microsoft Docs"
description: "Conozca las técnicas y características para proteger su base de datos de Azure SQL."
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/28/2017
ms.author: daredis
ms.openlocfilehash: 90c03f1538197e1cd1c90165417a4ec74c9c5961
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="secure-your-azure-sql-database"></a>Protección de Azure SQL Database

SQL Database protege los datos mediante la limitación del acceso a la base de datos a través de reglas de firewall, de mecanismos de autenticación que requieren que los usuarios prueben su identidad y de la autorización a través de pertenencias y permisos basados en roles, así como la seguridad de nivel de fila y el enmascaramiento dinámico de datos.

Con unos pocos pasos sencillos puede mejorar la protección de su base de datos contra usuarios malintencionados o acceso no autorizado. En este tutorial, aprenderá a: 

> [!div class="checklist"]
> * Configurar reglas de firewall de nivel de servidor para el servidor en Azure Portal
> * Configurar reglas de firewall de nivel de base de datos para la base de datos con SSMS
> * Conexión a una base de datos mediante una cadena de conexión segura
> * Administrar el acceso de los usuarios
> * Protección de los datos con cifrado
> * Habilitación de la auditoría de SQL Database
> * Habilitación de la detección de amenazas de SQL Database

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de lo siguiente:

- Se ha instalado la versión más reciente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Se ha instalado Microsoft Excel
- Se ha creado una base de datos y un servidor de SQL Azure: vea [Creación de una base de datos de Azure SQL Database en Azure Portal](sql-database-get-started-portal.md), [Creación de una sola base de datos de Azure SQL Database con la CLI de Azure](sql-database-get-started-cli.md) y [Creación de una sola base de datos de Azure SQL Database con PowerShell](sql-database-get-started-powershell.md). 

## <a name="log-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creación de una regla de firewall de nivel a servidor en Azure Portal

En Azure, las bases de datos SQL están protegidas mediante un firewall. De forma predeterminada, todas las conexiones al servidor y a las bases de datos que contiene se rechazan, excepto las conexiones desde otros servicios de Azure. Para más información, consulte [Reglas de firewall de nivel de base de datos y de nivel de servidor de Azure SQL Database](sql-database-firewall-configure.md).

La opción más segura es seleccionar Desactivado en "Permitir el acceso a servicios de Azure". Si necesita conectarse a la base de datos desde una máquina virtual o un servicio en la nube de Azure, debe crear una [IP reservada](../virtual-network/virtual-networks-reserved-public-ip.md) y permitir que solo la IP reservada acceda a través del firewall. 

Siga estos pasos para crear una [regla de firewall de nivel de servidor de SQL Database](sql-database-firewall-configure.md) para el servidor, a fin de permitir conexiones desde una dirección IP específica. 

> [!NOTE]
> Si ha creado una base de datos de ejemplo en Azure mediante una de las plantillas de inicio rápido o de los tutoriales anteriores, y está siguiendo este en un equipo con la misma dirección IP que tenía cuando seguía esos tutoriales, puede omitir este paso ya que se habrá creado una regla de firewall de nivel de servidor.
>

1. Haga clic en **Bases de datos SQL** en el menú izquierdo y haga clic en la base de datos para la que desea configurar la regla de firewall en la página **Bases de datos SQL**. Se abre la página de información general de la base de datos, que muestra el nombre completo del servidor (por ejemplo, **mynewserver-20170313.database.windows.net**) y proporciona opciones para otras configuraciones.

      ![regla de firewall del servidor](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Haga clic en **Establecer el firewall del servidor** en la barra de herramientas, como se muestra en la imagen anterior. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

3. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP pública del equipo conectado al portal o escriba manualmente la regla de firewall y luego haga clic en **Guardar**.

      ![establecer regla de firewall del servidor](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Haga clic en **Aceptar** y, después, en la **X** para cerrar la página **Configuración de firewall**.

Ahora puede conectarse a cualquier base de datos del servidor con la dirección IP o el intervalo de direcciones IP especificados.

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. Si es así, no podrá conectarse al servidor de Azure SQL Database, a menos que el departamento de TI abra el puerto 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Creación de una regla de firewall de nivel de base de datos con SSMS

Las reglas de firewall de nivel de base de datos le permiten crear una configuración de firewall diferente para bases de datos distintas dentro del mismo servidor lógico y crear reglas de firewall que sean portátiles, lo que significa que siguen la base de datos durante una [conmutación por error](sql-database-geo-replication-overview.md) en lugar de almacenarse en el servidor SQL. Las reglas de firewall de nivel de base de datos solo pueden configurarse mediante instrucciones Transact-SQL y solo después de haber configurado la primera regla de firewall de nivel de servidor. Para más información, consulte [Reglas de firewall de nivel de base de datos y de nivel de servidor de Azure SQL Database](sql-database-firewall-configure.md).

Siga estos pasos para crear una regla de firewall específica de base de datos.

1. Conéctese a la base de datos, para lo que puede usar, por ejemplo, [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. En el Explorador de objetos, haga clic con el botón derecho en la base de datos para la que quiere agregar una regla de firewall y haga clic en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos.

3. En la ventana de consulta, modifique la dirección IP con su dirección IP pública y, a continuación, ejecute la siguiente consulta:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. En la barra de herramientas, haga clic en **Ejecutar** para crear la regla de firewall.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Vea cómo conectar una aplicación a una base de datos con una cadena de conexión segura

Para garantizar una conexión cifrada y segura entre una aplicación cliente y SQL Database, la cadena de conexión debe estar configurada para:

- Solicitar una conexión cifrada y
- No confiar en el certificado de servidor. 

De esta forma se establece una conexión mediante Seguridad de la capa de transporte (TLS) y se reduce el riesgo de ataques de tipo "Man in the middle". Para obtener cadenas de conexión configuradas correctamente para SQL Database para los controladores de cliente admitidos, vaya a Azure Portal, como se muestra en la captura de pantalla para ADO.net.

1. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**.

2. En la página **Overview** (Información general), haga clic en **Mostrar las cadenas de conexión de la base de datos**.

3. Revise la cadena de conexión completa de **ADO.NET**.

    ![Cadena de conexión ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Creación de usuarios de base de datos

Antes de crear los usuarios, primero debe elegir uno de dos tipos de autenticación admitidos por Azure SQL Database: 

**Autenticación de SQL**, que usa el nombre de usuario y la contraseña para inicios de sesión y usuarios que son válidos únicamente en el contexto de una base de datos específica dentro de un servidor lógico. 

**Autenticación de Azure Active Directory**, que usa identidades administradas por Azure Active Directory. 

Si desea usar [Azure Active Directory](./sql-database-aad-authentication.md) para autenticarse en SQL Database, debe existir una instancia de Azure Active Directory rellenada antes de continuar.

Siga estos pasos para crear un usuario mediante la autenticación de SQL:

1. Conéctese a la base de datos, para lo que puede usar, por ejemplo, [SQL Server Management Studio](./sql-database-connect-query-ssms.md) con sus credenciales de administrador de servidor.

2. En el Explorador de objetos, haga clic con el botón derecho en la base de datos a la que quiere agregar un nuevo usuario y haga clic en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos seleccionada.

3. En la ventana Consulta, escriba la consulta siguiente:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. En la barra de herramientas, haga clic en **Ejecutar** para crear el usuario.

5. De forma predeterminada, el usuario puede conectarse a la base de datos, pero no tiene permisos para leer o escribir datos. Para conceder estos permisos al usuario recién creado, ejecute los dos comandos siguientes en una nueva ventana de consulta

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Es aconsejable crear estas cuentas sin privilegios de administrador en el nivel de base de datos para conectarse a la base de datos a menos que necesite ejecutar tareas de administrador, como crear nuevos usuarios. Revise el [tutorial de Azure Active Directory](./sql-database-aad-authentication-configure.md) sobre cómo autenticarse con Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Protección de los datos con cifrado

El cifrado de datos transparente (TDE) de Azure SQL Database cifra automáticamente los datos en reposo, sin exigir ningún cambio en la aplicación que accede a la base de datos cifrada. Para las bases de datos recién creadas, el TDE está activado de forma predeterminada. Para habilitar el TDE para la base de datos o para comprobar que está activado, siga estos pasos:

1. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 

2. Haga clic en **Cifrado de datos transparente** para abrir la página de configuración de TDE.

    ![Cifrado de datos transparente](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Si es necesario, establezca **Cifrado de datos** en Activado y haga clic en **Guardar**.

Se inicia el proceso de cifrado en segundo plano. Para supervisar el progreso, conéctese a SQL Database mediante [SQL Server Management Studio](./sql-database-connect-query-ssms.md) consultando la columna encryption_state de la vista `sys.dm_database_encryption_keys`.

## <a name="enable-sql-database-auditing-if-necessary"></a>Habilitación de la auditoría de SQL Database, si es necesario

Auditoría de Azure SQL Database realiza un seguimiento de eventos de bases de datos y registra los eventos en un registro de auditoría de la cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas y conocer la actividad de las bases de datos, así como las discrepancias y anomalías que pueden indicar la existencia de potenciales infracciones de la seguridad. Siga estos pasos para crear un directiva de auditoría predeterminada para SQL Database:

1. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 

2. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**. Observe que la auditoría de nivel de servidor está deshabilitada y hay un vínculo **Ver configuración de servidor** que le permite ver o modificar la configuración de auditoría del servidor desde este contexto.

    ![Hoja Auditoría](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Si prefiere habilitar un tipo (¿o ubicación?) de auditoría distinto del especificado en el nivel de servidor, **active** la auditoría y elija el tipo de autoría **Blob** . Si está habilitada la auditoría de blobs del servidor, la auditoría configurada de base de datos se producirá de forma paralela a la auditoría de blobs del servidor.

    ![Activar la auditoría](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros, y el período de retención, después del cual se eliminarán los registros antiguos. A continuación, haga clic en **Aceptar** en la parte inferior. 

   > [!TIP]
   > Use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo partido de las plantillas de informes de auditorías.
   > 

5. Haga clic en **Guardar**.

> [!IMPORTANT]
> Si quiere personalizar los eventos auditados, puede hacerlo mediante PowerShell o la API de REST (consulte la [auditoría de base de datos SQL](sql-database-auditing.md) para más información).
>

## <a name="enable-sql-database-threat-detection"></a>Habilitación de la detección de amenazas de SQL Database

Detección de amenazas ofrece un nuevo nivel de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas a medida que se producen, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan. Los usuarios pueden explorar los eventos sospechosos mediante la auditoría de SQL Database para determinar si proceden de un intento de acceder a los datos en la base de datos, infringir su seguridad o aprovecharlos. Detección de amenazas facilita la solución de las posibles amenazas a la base de datos sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de seguridad avanzada.
Por ejemplo, Detección de amenazas detecta determinadas actividades anómalas en la base de datos que sugieren posibles intentos de inyección de código SQL. La inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

1. Vaya a la hoja de configuración de Base de datos SQL que desea supervisar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.

    ![Panel de navegación](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. En la hoja de configuración **Auditoría y detección de amenazas**, **active** la auditoría; se mostrará la configuración de detección de amenazas.

3. **Active** la detección de amenazas.

4. Configure la lista de correos electrónicos que recibirán alertas de seguridad cuando se detecten actividades anómalas en la base de datos.

5. Haga clic en **Guardar** en la hoja **Auditoría y detección de amenazas** para guardar la directiva de auditoría y detección de amenazas nueva o actualizada.

    ![Panel de navegación](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Si se detectan actividades anómalas en la base de datos, recibirá una notificación por correo electrónico. El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor y la hora del evento. Además, se proporcionará información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos. Los siguientes pasos le guían a través de lo que debe hacer si recibe un mensaje de correo electrónico de este tipo:

    ![Correo electrónico de detección de amenazas](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. En el correo electrónico, haga clic en el vínculo **Registro de auditoría SQL de Azure**, lo que iniciará el portal de Azure y mostrará los registros de auditoría pertinentes en torno a la hora del evento sospechoso.

    ![Registros de auditoría](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Haga clic en los registros de auditoría para ver más detalles sobre las actividades sospechosas en la base de datos, como instrucción SQL, motivo del error e IP de cliente.

    ![Detalles del registro](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. En la hoja Registros de auditoría, haga clic en **Abrir en Excel** para abrir una plantilla de Excel ya configurada para importar y ejecutar un análisis más profundo del registro de auditoría en torno a la hora del evento sospechoso.

    > [!NOTE]
    > En Excel 2010 o superior, se requieren Power Query y la opción **Combinación rápida**.

    ![Abrir registros en Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Para definir la configuración **Combinación rápida**: en la pestaña **POWER QUERY** de la cinta, seleccione **Opciones** para mostrar el cuadro de diálogo Opciones. Seleccione la sección Privacidad y elija la segunda opción, "Omitir los niveles de privacidad para mejorar el rendimiento":

    ![Combinación rápida de Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Para cargar registros de auditoría SQL, asegúrese de que los parámetros de la pestaña Configuración sean correctos y después seleccione "Datos" en la cinta y haga clic en el botón "Actualizar todo".

    ![Parámetros de Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Los resultados aparecen en la hoja **Registros de auditoría SQL** , que permite ejecutar un análisis más profundo de las actividades anómalas que se detectaron y mitigar el impacto del evento de seguridad en la aplicación.


## <a name="next-steps"></a>Pasos siguientes
En este tutorial se proporcionó información para mejorar la protección de la base de datos contra usuarios malintencionados o acceso no autorizado con unos pocos pasos sencillos.  Ha aprendido a: 

> [!div class="checklist"]
> * Configurar reglas de firewall para un servidor o una base de datos
> * Conexión a una base de datos mediante una cadena de conexión segura
> * Administrar el acceso de los usuarios
> * Protección de los datos con cifrado
> * Habilitación de la auditoría de SQL Database
> * Habilitación de la detección de amenazas de SQL Database

En el siguiente tutorial aprenderá a implementar una base de datos distribuida geográficamente.

> [!div class="nextstepaction"]
>[Implementación de una base de datos distribuida geográficamente](sql-database-implement-geo-distributed-database.md)

