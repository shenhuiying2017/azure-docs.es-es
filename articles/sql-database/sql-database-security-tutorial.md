---
title: "Protección de la base de datos de Azure SQL | Microsoft Docs"
description: "Conozca las técnicas y características para proteger su base de datos de Azure SQL."
services: sql-database
documentationcenter: 
author: DRediske
manager: johammer
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-secure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/03/2017
ms.author: daredis
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: eb2c8ec946a08ed3b538d613199706779b80bd1f
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---
# <a name="secure-your-azure-sql-database"></a>Protección de la base de datos de Azure SQL

Este tutorial le guía por los aspectos básicos de la protección de su base de datos SQL. Con unos cuantos sencillos pasos, puede tomar cualquier base de datos y mejorar enormemente su protección contra usuarios malintencionados o acceso no autorizado.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para realizar este tutorial, asegúrese de tener instalado Excel y la última versión de [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS).



## <a name="set-up-firewall-rules-for-your-database"></a>Configuración de reglas de firewall para la base de datos

Las bases de datos de Azure SQL están protegidas con un firewall. De forma predeterminada, todas las conexiones al servidor y a las bases de datos que contiene se rechazan, excepto las conexiones desde otros servicios de Azure. La opción más segura es establecer "Permitir el acceso a servicios de Azure" en Desactivado. Si necesita conectarse a la base de datos desde una máquina virtual o un servicio en la nube de Azure, debe crear una [IP reservada](../virtual-network/virtual-networks-reserved-public-ip.md) y permitir que solo la IP reservada acceda a través del firewall. 

Siga estos pasos para crear una [regla de firewall de nivel de servidor de SQL Database](sql-database-firewall-configure.md) para el servidor, a fin de permitir conexiones desde una dirección IP específica. 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
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

Si necesita una configuración de firewall diferente para distintas bases de datos del mismo servidor lógico, debe crear una regla de nivel de base de datos para cada una. Las reglas de firewall de nivel de base de datos solo pueden configurarse mediante instrucciones Transact-SQL y solo después de haber configurado la primera regla de firewall de nivel de servidor. Siga estos pasos para crear una regla de firewall específica de base de datos.

1. Conéctese a la base de datos, mediante [SSMS](./sql-database-connect-query-ssms.md), por ejemplo.

2. En el Explorador de objetos, haga clic con el botón derecho en la base de datos para la que quiere agregar una regla de firewall y haga clic en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos.

3. En la ventana Consulta, escriba la consulta siguiente:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. En la barra de herramientas, haga clic en **Ejecutar** para crear la regla de firewall.

## <a name="connect-to-the-database-using-a-secure-connection-string"></a>Conexión a la base de datos mediante una cadena de conexión segura

Para garantizar una conexión cifrada segura entre el cliente y SQL Database, la cadena de conexión se debe configurar para 1) solicitar una conexión cifrada y 2) no confiar en el certificado de servidor. De esta forma se establece una conexión mediante Seguridad de la capa de transporte (TLS) y se reduce el riesgo de ataques de tipo "Man in the middle". Para obtener las cadenas de conexión configuradas correctamente para su base de datos de Azure SQL para los controladores de cliente admitidos, vaya al portal de Azure, como se muestra en la captura de pantalla para ADO.net.

1. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**.

2. En la página **Overview** (Información general), haga clic en **Mostrar las cadenas de conexión de la base de datos**.

3. Revise la cadena de conexión completa de **ADO.NET**.

    ![Cadena de conexión ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)


## <a name="user-management"></a>Administración de usuarios

Antes de crear los usuarios, primero debe elegir uno de dos tipos de autenticación admitidos por Azure SQL Database: 

**Autenticación de SQL**, que usa el nombre de usuario y la contraseña para inicios de sesión y usuarios que son válidos únicamente en el contexto de una base de datos específica dentro de un servidor lógico. 

**Autenticación de Azure Active Directory**, que usa identidades administradas por Azure Active Directory. 

Si desea usar [Azure Active Directory](./sql-database-aad-authentication.md) para autenticarse en SQL Database, debe existir una instancia de Azure Active Directory rellenada antes de continuar.

Siga estos pasos para crear un usuario mediante la autenticación de SQL:

1. Conéctese a la base de datos mediante [SSMS](./sql-database-connect-query-ssms.md), por ejemplo, con sus credenciales de administrador de servidor.

2. En el Explorador de objetos, haga clic con el botón derecho en la base de datos a la que quiere agregar un nuevo usuario y haga clic en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos seleccionada.

3. En la ventana Consulta, escriba la consulta siguiente:

    ```sql
    CREATE USER ApplicationUserUser WITH PASSWORD = 'strong_password';
    ```

4. En la barra de herramientas, haga clic en **Ejecutar** para crear el usuario.

5. De forma predeterminada, el usuario puede conectarse a la base de datos, pero no tiene permisos para leer o escribir datos. Para conceder estos permisos al usuario recién creado, ejecute los dos comandos siguientes en una nueva ventana de consulta

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUserUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUserUser;
    ```

Es aconsejable crear estas cuentas sin privilegios de administrador en el nivel de base de datos para conectarse a la base de datos a menos que necesite ejecutar tareas de administrador, como crear nuevos usuarios. Revise el [tutorial de Azure Active Directory](./sql-database-aad-authentication-configure.md) sobre cómo autenticarse con Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Protección de los datos con cifrado

El cifrado de datos transparente (TDE) de Azure SQL Database cifra automáticamente los datos en reposo, sin exigir ningún cambio en la aplicación que accede a la base de datos cifrada. Para habilitar TDE para su base de datos, siga estos pasos:

1. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 

2. Haga clic en **Cifrado de datos transparente** para abrir la página de configuración de TDE.

    ![Cifrado de datos transparente](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Establezca **Cifrado de datos** en Activado y haga clic en **Guardar**.

Se inicia el proceso de cifrado en segundo plano. Puede supervisar el progreso mediante la conexión a SQL Database con, por ejemplo, [SSMS](./sql-database-connect-query-ssms.md) como base de datos y consultar la columna encryption_state de la vista sys.dm_database_encryption_keys.

## <a name="enable-sql-database-auditing"></a>Habilitación de la auditoría de SQL Database

Auditoría de Azure SQL Database realiza un seguimiento de eventos de bases de datos y registra los eventos en un registro de auditoría de la cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. Siga estos pasos para crear un directiva de auditoría predeterminada para su base de datos:

1. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**.

2. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.

    ![Hoja Auditoría](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. En la hoja de configuración de la auditoría de base de datos, puede activar la casilla **Heredar la configuración del servidor** para indicar que esta base de datos se auditará según la configuración de su servidor. Si esta opción está activada, verá un vínculo **Ver configuración de auditoría de servidor** que le permite ver o modificar la configuración de auditoría del servidor desde este contexto.

    ![Heredar la configuración](./media/sql-database-security-tutorial/auditing-get-started-server-inherit.png)

4. Si prefiere habilitar la auditoría de blobs en el nivel de base de datos (además, o en lugar, del nivel de servidor) **desactive** la opción **Heredar la configuración de auditoría del servidor**, **active** Auditoría y elija el tipo **Blob**.

    > Si está habilitada la auditoría de blobs del servidor, la auditoría configurada de base de datos se producirá de forma paralela a la auditoría de blobs del servidor.

    ![Activar la auditoría](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

5. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros, y el período de retención, después del cual se eliminarán los registros antiguos. A continuación, haga clic en **Aceptar** en la parte inferior. **Sugerencia:** use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes de auditorías.

    ![Panel de navegación](./media/sql-database-security-tutorial/auditing-get-started-storage-details.png)

6. Si quiere personalizar los eventos auditados, puede hacerlo mediante PowerShell o la API de REST (consulte la sección [Automation (PowerShell/API de REST)](#subheading-7) para más información.

7. Haga clic en **Guardar**.


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

    Recibirá una notificación por correo electrónico tras la detección de actividades anómalas en la base de datos. El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor y la hora del evento. Además, se proporcionará información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos. Los siguientes pasos le guían a través de lo que debe hacer si recibe un mensaje de correo electrónico de este tipo:

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

* Para una información general de las características de seguridad de SQL Database, consulte [Introducción a la seguridad de SQL Database](sql-database-security-overview.md).
* Para el cifrado adicional de columnas confidenciales en la base de datos, considere el uso de cifrado del lado cliente con [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).
* En cuanto a funcionalidades de control de acceso adicionales, [Seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) le permite restringir el acceso a filas de una base de datos en función de la pertenencia a grupos del usuario o del contexto de ejecución, y [Enmascaramiento dinámico de datos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de datos confidenciales mediante su enmascaramiento para los usuarios sin privilegios en la capa de aplicación. 

