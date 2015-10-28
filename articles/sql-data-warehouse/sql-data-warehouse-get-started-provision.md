<properties
	pageTitle="Creación de una base de datos de almacenamiento de datos SQL en el portal de vista previa de Azure | Microsoft Azure"
	description="Aprenda a crear un almacenamiento de datos SQL de Azure en el portal de vista previa de Azure"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="barbkess"
	manager="jhubbard"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2015"
   ms.author="lodipalm;barbkess"/>

# Creación de un Almacenamiento de datos SQL con el Portal de vista previa de Azure#

> [AZURE.SELECTOR]
- [Azure preview portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este tutorial muestra cómo crear un Almacenamiento de datos SQL de Azure en solo unos minutos con el Portal de vista previa de Azure.

En este tutorial, aprenderá lo siguiente:

- Crear un servidor que hospedará la base de datos.
- Crear una base de datos que contenga la base de datos de ejemplo AdventureWorksDW.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Paso 1: Iniciar sesión y comenzar

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en **Nuevo** > **Datos y almacenamiento** > **Almacenamiento de datos SQL**.

	![Creación del almacenamiento de datos](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

1. Escriba un nombre para la base de datos en la hoja de Almacenamiento de datos SQL. En este ejemplo, llamaremos a la base de datos AdventureWorksDW.

    ![Escriba un nombre de base de datos](./media/sql-data-warehouse-get-started-provision/database-name.png)


## Paso 2: Configurar y crear un servidor
En la base de datos SQL y Almacenamiento de datos SQL, cada base de datos se asigna a un servidor y cada servidor se asigna a una ubicación geográfica. El servidor se llama a un servidor lógico de SQL.

> [AZURE.NOTE]<a name="note"></a>Un servidor lógico de SQL:
  >
  > + proporciona una manera coherente para configurar varias bases de datos dentro de la misma ubicación geográfica.
  > + No es un hardware físico como en un servidor local. Forma parte del software de servicio. Por eso lo llamamos un *servidor lógico*.
  > + Puede hospedar varias bases de datos sin afectar a su rendimiento.
  > + Usa una minúscula *s* en su nombre. Un **s**ervidor SQL es un servidor lógico de Azure, mientras que SQL **S**server es un producto de la base de datos local de Microsoft.

1. Haga clic en **Servidor** > **Crear un nuevo servidor**. No se aplica ningún cargo por el servidor. Si ya dispone de un servidor SQL lógico V12 que desee usar, elija el servidor existente y vaya al paso siguiente. 

    ![Creación de un servidor nuevo](./media/sql-data-warehouse-get-started-provision/create-server.png)

3. Rellene la información del **nuevo servidor**.
    
	- **Nombre del servidor**. Escriba un nombre para el servidor lógico. Debe ser único para cada ubicación geográfica.
	- **Nombre del administrador del servidor**. Escriba un nombre de usuario para la cuenta de administrador del servidor.
	- **Contraseña**. Escriba la contraseña del administrador del servidor. 
	- **Ubicación**. Elija una ubicación geográfica para el servidor. Para reducir el tiempo de transferencia de datos, es mejor ubicar su servidor en proximidad geográfica a otros recursos de datos a los que tenga acceso esta base de datos.
	- **Crear servidor V12**. SÍ, es la opción para Almacenamiento de datos SQL. 
	- **Permitir que los servicios de Azure accedan al servidor**. Esto siempre está seleccionado para Almacenamiento de datos SQL

    >[AZURE.NOTE]Asegúrese de almacenar el nombre del servidor, el nombre del administrador y la contraseña en algún lugar. Necesitará esta información para iniciar sesión en el servidor.

1. Haga clic en **Aceptar** para guardar la configuración del servidor SQL lógico y volver a la hoja Almacenamiento de datos SQL.

    ![Configuración de un servidor nuevo](./media/sql-data-warehouse-get-started-provision/configure-server.png)

## Paso 3: Configurar y crear una base de datos
Ahora que seleccionó el servidor SQL lógico, está listo para terminar de crear la base de datos.
 
2. En la hoja **Almacenamiento de datos SQL**, rellene los campos que quedan. 

    ![Crear base de datos](./media/sql-data-warehouse-get-started-provision/create-database.png)
    
    - **Rendimiento**: se recomienda empezar con 400 DWU. Puede mover el control deslizante hacia la izquierda o la derecha para ajustar el nivel de rendimiento de la base de datos, tanto ahora como después de crearla. 

        > [AZURE.NOTE]El Almacenamiento de datos SQL mide el rendimiento en Unidades de almacenamiento de datos (DWU). A medida que aumentan las DWU, Almacenamiento de datos SQL aumenta los recursos informáticos disponibles para las operaciones de bases de datos. Cuando ejecute la carga de trabajo, podrá ver cómo se relacionan las DWU con el rendimiento de la carga de trabajo.
        > 
        > Puede modificar de manera rápida y sencilla el nivel de rendimiento después de crear la base de datos. Por ejemplo, si no está usando la base de datos, mueva el control deslizante hacia la izquierda para reducir los costes. O bien, puede aumentar el rendimiento cuando sean necesarios más recursos. Para incurrir en costos 0, puede pausar la base de datos. Estas son las ventajas de escalabilidad que ofrece el almacenamiento de datos SQL.

    - **Seleccionar origen**. Haga clic en **Seleccionar origen** > **Ejemplo**. Puesto que hay solo una base de datos de ejemplo disponible en este momento, al seleccionar Ejemplo, Azure rellena automáticamente la opción **Seleccionar ejemplo** con AdventureWorksDW.
  
        ![Seleccionar Ejemplo.](./media/sql-data-warehouse-get-started-provision/select-source.png)

    - **Grupo de recursos**. Puede conservar los valores predeterminados. Los grupos de recursos son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Obtenga más información sobre los [grupos de recursos](../azure-portal/resource-group-portal.md).
    
    - **Suscripción**. Seleccione la suscripción a la que desea facturar esta base de datos.

1. Haga clic en **Crear** para crear la base de datos de Almacenamiento de datos SQL.

1. Espere unos minutos y la base de datos estará lista. Cuando finalice, debería volver al [Portal de vista previa de Azure](https://portal.azure.com). Observe que la base de datos de Almacenamiento de datos SQL se agregó al panel.

    ![Vista del portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)


## Paso 4: Configurar el acceso al servidor de firewall para la dirección IP de cliente
Para conectarse al servidor desde la dirección IP actual, agregue su dirección IP de cliente a las reglas de firewall. Este paso muestra cómo hacerlo.

1. Haga clic en **Examinar** > **Servidores SQL Server** > elija el servidor > **Configuración** > **Firewall**.

    ![Encontrar la configuración de firewall](./media/sql-data-warehouse-get-started-provision/find-firewall-settings.png)

4. Haga clic en **Agregar IP de cliente** para que Azure cree una regla para su dirección IP cliente. Haga clic en **Guardar**.

	![Agregar la dirección IP](./media/sql-data-warehouse-get-started-provision/add-client-ip.png)

1. Crear una regla de firewall con un intervalo de direcciones IP. Puede hacerlo ahora o más tarde.

	>[AZURE.IMPORTANT]Probablemente su dirección IP cambie de vez en cuando, y es posible que no pueda tener acceso al servidor hasta que cree una nueva regla de firewall. Para garantizar un acceso constante, se recomienda agregar un intervalo de direcciones IP. Para obtener información adicional, consulte [Configuración del firewall](../sql-database/sql-database-configure-firewall-settings.md).

    Para crear una regla, escriba un nombre y el intervalo de direcciones IP y haga clic en **Guardar**.

    ![Agregar una regla de firewall](./media/sql-data-warehouse-get-started-provision/add-rule.png)

Una vez configurado el firewall, podrá establecer conexiones desde su escritorio a la base de datos de Almacenamiento de datos SQL que acaba de crear.


## Pasos siguientes

Ahora que creó una base de datos de ejemplo para Almacenamiento de datos SQL, obtenga información sobre cómo usar Almacenamiento de datos SQL en [Conexión y consultas](./sql-data-warehouse-get-started-connect-query.md).

<!----HONumber=Oct15_HO3-->