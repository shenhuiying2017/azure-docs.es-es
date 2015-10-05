<properties
   pageTitle="Introducción a la conexión a Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Introducción a la conexión a Almacenamiento de datos SQL y ejecución de algunas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Introducción a la conexión a Almacenamiento de datos SQL de Azure
En este artículo de inicio rápido aprenderá a conectarse a una instancia aprovisionada de Almacenamiento de datos SQL y a consultarla con dos herramientas distintas:

- **Visual Studio**: SQL Server Data Tools, el depurador y editor de código integrado de Visual Studio, es completamente compatible con Almacenamiento de datos SQL, por lo que podrá conectarse fácilmente a él y consultarlo y administrarlo.  

> [AZURE.NOTE]Almacenamiento de datos SQL requiere al menos la versión de vista previa de SSDT 12.0.50623 o posterior.

- **sqlcmd**: sqlcmd es una herramienta de línea de comandos que ofrece sencillas capacidades de consulta y conexión.  

Cuando complete este artículo, habrá finalizado las siguientes operaciones:

1. Instalación y actualización de Visual Studio 2013.
2. Creación de una conexión a Almacenamiento de datos SQL en SSDT.
3. Ejecución de consultas en la base de datos de Almacenamiento de datos SQL.

>[AZURE.NOTE]Se supone que ya completó la guía de aprovisionamiento o que tiene disponible una instancia de Almacenamiento de datos SQL. Si todavía no ha aprovisionado la instancia de Almacenamiento de datos SQL, vuelva a consultar la [guía de inicio rápido sobre aprovisionamiento](sql-data-warehouse-get-started-provision.md).

## Configuración de Visual Studio para desarrollo##
En el caso del desarrollo, el equipo de Almacenamiento de datos SQL recomienda usar Visual Studio 2013 o superior junto con SQL Server Data Tools. A continuación, se explica cómo descargar y actualizar Visual Studio 2013 si todavía no tiene instalada una versión viable de Visual Studio.

Si desea obtener más información, es posible resolver las preguntas generales sobre SSDT con la [documentación completa de SSDT](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Descarga de Visual Studio 2013 ###
Vaya al sitio web de Visual Studio 2013 para descargar e instalar una copia de Visual Studio. Recuerde que cualquiera de las ediciones gratis es perfectamente adecuada para Almacenamiento de datos SQL. Elija la que satisfaga sus necesidades.

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Descargar Visual Studio 2013</a>

### Actualización de Visual Studio 2013 ###
¿Ya tiene instalado Visual Studio 2013? Estupendo. Simplemente siga los pasos a continuación para asegurarse de que esté actualizado. Puede ir al paso siguiente.

1. Abra Visual Studio 2013.
2. Elija el menú **Herramientas** y seleccione **Extensiones y actualizaciones**.
3. Navegue por el control de árbol hasta **Actualizaciones** y **Actualizaciones de producto**.
4. Si no hay ninguna actualización disponible, puede cerrar la ventana **Extensiones y actualizaciones** y pasar a la siguiente tarea de esta guía de inicio rápido.

Sin embargo, si existe una actualización para Visual Studio, debe hacer clic en el botón **Actualizar**. Se iniciará la descarga de la actualización más reciente de Visual Studio 2013.

Antes de continuar, cierre la ventana **Extensiones y actualizaciones** y también Visual Studio 2013.

5. Haga clic en el botón **Ejecutar** para instalar la actualización más reciente de Visual Studio 2013.

6. Acepte los términos de la licencia y haga clic en el botón **Instalar**, y acepte todas las indicaciones del Control de cuentas de usuario (UAC).

7. Haga clic en el botón **Iniciar** para completar la instalación.

Con esto finaliza la actualización de Visual Studio 2013.

### Actualización de SSDT
> [AZURE.IMPORTANT]Almacenamiento de datos SQL requiere al menos la versión de vista previa de SSDT 12.0.50623 o posterior.

Los ingenieros de SSDT actualizan frecuentemente el complemento con características nuevas, por lo que deberá actualizar SSDT cada cierto tiempo. Nuevamente, se trata de un proceso muy sencillo. Siga los pasos a continuación para comprobar si es necesario actualizar SSDT:

1. Abra Visual Studio 2013.  
2. Elija el menú **Herramientas** y seleccione **Extensiones y actualizaciones**.
3. Navegue por el control de árbol hasta **Actualizaciones** y **Actualizaciones de producto**.
4. Si no hay ninguna actualización disponible, puede cerrar la ventana **Extensiones y actualizaciones** y pasar a la siguiente tarea de esta guía de inicio rápido.

Sin embargo, si existe una actualización llamada "Actualización de Microsoft SQL Server para herramientas de base de datos", haga clic en el botón **Actualizar**. Con esto se iniciará la descarga de la versión más reciente de SSDT.

5. Haga clic en el botón **Ejecutar** para instalar la actualización más reciente de SSDT.

6. Acepte los términos de la licencia y haga clic en el botón **Instalar**.

7. Haga clic en el botón **Cerrar** para completar la actualización de SSDT.

8. Cierre la ventana **Extensiones y actualizaciones**.

Ahora tiene una versión actualizada de Visual Studio 2013 instalada en el escritorio, con una extensión actualizada de SSDT.

> [AZURE.NOTE]Actualmente, se recomienda usar la [vista previa de SSDT para Visual Studio 2013, versión 12.0.50623 o posterior](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409).

## Conexión a Visual Studio 2013
Si ejecuta la versión deseada de Visual Studio, podrá conectarse a la instancia de Almacenamiento de datos SQL de dos maneras distintas:

### Conexión desde Visual Studio
Para establecer la conexión, solo es necesario abrir el Explorador de objetos de SQL Server y pasar la información de conexión.

1. Abra Visual Studio.
2. Elija el menú **Ver** y seleccione **Explorador de objetos de SQL Server** en la lista desplegable.

> [AZURE.NOTE]Asegúrese de elegir el Explorador de objetos de SQL Server y *no* el Explorador de servidores. Los nombres son similares, pero son controles muy distintos. Ambos se encuentran juntos, por lo que debe tener cuidado y asegurarse de seleccionar el explorador correcto.

Ahora puede ver el Explorador de objetos de SQL Server:


3. En el Explorador de objetos de SQL Server, haga clic en el botón **Agregar servidor**.

4. Rellene el cuadro de diálogo **Conectar a servidor** con los valores que eligió al crear el servidor lógico. Además, haga clic en el botón Opciones y especifique la base de datos a la que se va a conectar (su instancia de Almacenamiento de datos SQL) antes de conectarse.

Si lo desea, active la casilla **Recordar contraseña**. Puede ayudarle a ahorrar tiempo, pero recuerde que, al activar la casilla, cualquier persona que tenga acceso físico a su perfil podría ejecutar consultas con esta cuenta.

> [AZURE.NOTE]Recuerde que el nombre del servidor debe ser un nombre completo. Por lo tanto, el valor del nombre del servidor debe seguir esta convención: *NombreDelServidor*.database.windows.net, donde *NombreDelServidor* es el nombre del servidor lógico.

Una vez que rellene las credenciales, haga clic en **Conectar**.

Ahora ha realizado una conexión y ha registrado su servidor lógico de Almacenamiento de datos SQL en el Explorador de objetos de SQL Server.

### Conexión desde el Portal de Azure
Vaya a Visual Studio directamente desde el Portal de Azure.

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).
2. Seleccione la instancia deseada de Almacenamiento de datos SQL en la hoja **Examinar**.
3. En la parte superior de la hoja de Almacenamiento de datos SQL, seleccione **Abrir en Visual**.
4. Si todavía no ha configurado el firewall con la dirección IP del equipo cliente, seleccione **Configurar el firewall**.

	a. Escriba un **Nombre de regla**, una **Dirección IP inicial** y una **Dirección IP final**.

	b. Haga clic en **Guardar** en la parte superior de la hoja.
5. Haga clic en **Abrir en Visual Studio**.
6. Se abrirá Visual Studio y se le pedirán sus credenciales.
7. Después de escribirlas y conectarse, el servidor y las instancias de Almacenamiento de datos aparecerán en el panel del Explorador de objetos de SQL Server.  

## Conexión a Almacenamiento de datos SQL con sqlcmd

También puede conectarse a Almacenamiento de datos SQL con la utilidad de símbolo del sistema [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) incluida en SQL Server o las [Utilidades de línea de comandos 11 de Microsoft para SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501). La utilidad sqlcmd le permite introducir instrucciones Transact-SQL, procedimientos del sistema y archivos de script en el símbolo del sistema.

Para conectarse a una instancia específica de Almacenamiento de datos SQL cuando use sqlcmd, deberá abrir el símbolo del sistema y escribir **sqlcmd**, seguido de la cadena de conexión de la base de datos de Almacenamiento de datos SQL. La cadena de conexión deberá tener los siguientes parámetros:

+ **Usuario (-U):** usuario del servidor con formato `<`Usuario`>`@`<`Nombre del servidor`>`.database.windows.net.
+ **Contraseña (-P):** la contraseña asociada con el usuario.
+ **Servidor (-S):** servidor con el formato `<`Nombre del servidor`>`.database.windows.net.
+ **Base de datos (-D):** el nombre de la base de datos.
+ **Habilitar identificadores entre comillas (-I):** los identificadores entre comillas deben estar habilitados para poder conectarse a una instancia de Almacenamiento de datos SQL.

Por lo tanto, para ello, debe escribir lo siguiente:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

Después de la conexión, puede emitir cualquier instrucción Transact-SQL en la instancia. Por ejemplo, la instrucción que aparece a continuación usa la instrucción [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) para crear una tabla.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```

Para obtener información adicional sobre sqlcmd, consulte la [documentación de sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Ejecutar consultas de ejemplo ##

Ahora que registramos el servidor, continuemos y escribamos una consulta.

1. Haga clic en la base de datos de usuario en SSDT.

2. Haga clic en el botón **Nueva consulta**. Se abre una nueva ventana.

3. Escriba una consulta. Escriba el siguiente código en la ventana de consulta:

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Ejecute la consulta.

	Para ejecutar la consulta, haga clic en la flecha verde o use la combinación de teclas `CTRL`+`SHIFT`+`F5`.

## Pasos siguientes ##
Ahora que puede conectarse y realizar consultas, intente [cargar datos de ejemplo][] o [desarrollar código][].

[cargar datos de ejemplo]: ./sql-data-warehouse-get-started-load-samples.md
[desarrollar código]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=Sept15_HO4-->