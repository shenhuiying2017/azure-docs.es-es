<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="How to configure" pageTitle="How to configure a SQL Database - Azure" metaKeywords="Azure creating SQL Server, Azure configuring SQL Server" description="Learn how to create and configure a logical server using SQL Server in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure SQL Database" authors="" solutions="" manager="" editor="" />

Creación y configuración de una base de datos SQL
=================================================

Este tema le llevará por los pasos para crear y configurar el servidor lógico. En el nuevo Portal de administración de Azure (Vista previa), los flujos de trabajo revisados le permiten crear primero una base de datos y, a continuación, crear un servidor.

Sin embargo, en este tema, creará primero el servidor. Es posible que prefiera este enfoque si dispone de bases de datos SQL Server existentes que quiera cargar.

Tabla de contenido
------------------

-   [Creación de un servidor lógico](#createLogical)
-   [Configuración del firewall para el servidor lógico](#configFWLogical)

Creación de un servidor lógico
------------------------------

1.  Inicie sesión en el [Portal de administración](http://manage.windowsazure.com).

2.  Haga clic en **Base de datos SQL** y, a continuación, en **SERVERS** en la página principal de Base de datos SQL.

3.  Haga clic en **Add** en la parte inferior de la página.

4.  En Server Settings, especifique un nombre de administrador con una sola palabra sin espacios.

    Base de datos SQL usa la autenticación de SQL a través de una conexión cifrada. Se creará un nuevo inicio de sesión de autenticación de SQL Server asignado al rol del servidor fijo sysadmin con el nombre que proporcione.

    El inicio de sesión no puede ser una dirección de correo electrónico, una cuenta de usuario de Windows o un Windows Live ID. Las reclamaciones y la autenticación de Windows no son compatibles con Base de datos SQL.

5.  Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo.

6.  Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación que le sea más próxima. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.

7.  Asegúrese de mantener la opción **Allow Services** seleccionada de manera que pueda conectarse a esta base de datos con el Portal de administración para Base de datos SQL, servicios de almacenamiento y otros servicios en Azure.

8.  Haga clic en la marca de verificación en la parte inferior de la página cuando haya finalizado.

Tenga en cuenta que no especificó un nombre de servidor. Base de datos SQL genera automáticamente el nombre del servidor para garantizar que no existen entradas DNS duplicadas. El nombre del servidor es una cadena alfanumérica de 10 caracteres. No puede cambiar el nombre del servidor de Base de datos SQL.

En el próximo paso, configurará el firewall de manera que se permita el acceso a las conexiones de las aplicaciones que se ejecuten en la red.

Configuración del firewall para el servidor lógico
--------------------------------------------------

1.  En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Bases de datos SQL**, en **Servidores** y, a continuación, haga clic en el servidor que acaba de crear.

2.  Haga clic en **Configure**.

3.  Copie la dirección IP del cliente actual. Si está conectado desde una red, esta es la dirección IP que está escuchando el servidor proxy o el enrutador. Base de datos SQL detecta la dirección IP que usa la conexión actual para que pueda crear una regla de firewall para aceptar solicitudes de conexión desde ese dispositivo.

4.  Pegue la dirección IP en el intervalo de inicio y finalización. Después, si se producen errores de conexión que indican que el intervalo es demasiado estrecho, puede editar la regla para ampliarlo.

    Si los equipos cliente usan direcciones IP asignadas dinámicamente, debe especificar un intervalo que sea lo suficientemente amplio para que incluya direcciones IP asignadas a equipos en la red. Comience por un intervalo reducido y, a continuación, expándalo solo si lo necesita.

5.  Especifique un nombre para la regla de firewall, como el nombre del equipo o la compañía.

6.  Haga clic en la marca de verificación para guardar la regla.

7.  Haga clic en **Save** en la parte inferior de la página para completar el paso. Si no consigue ver **Save**, actualice la página del explorador.

Ahora dispone de un servidor lógico, una regla de firewall que permite conexiones entrantes desde la dirección IP, y un inicio de sesión de administrador. En el siguiente paso, cambiará al equipo local para completar los pasos de configuración restantes.

**Nota:** el servidor lógico que acaba de crear es transitorio y se hospedará dinámicamente en los servidores físicos de un centro de datos. Si elimina el servidor, debe saber antes que se trata de una acción irreversible. Asegúrese de realizar una copia de seguridad de las bases de datos que cargue posteriormente en el servidor.

