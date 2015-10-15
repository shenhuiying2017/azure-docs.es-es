<properties
	pageTitle="Creación de una base de datos de almacenamiento de datos SQL en el portal de vista previa de Azure | Microsoft Azure"
	description="Aprenda a crear un almacenamiento de datos SQL de Azure en el portal de vista previa de Azure"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2015"
   ms.author="lodipalm;barbkess"/>

# Crear un almacén de datos de SQL en el portal de vista previa de Azure#

Este tutorial muestra lo fácil que resulta crear un almacén de datos de SQL de Azure en solo unos minutos en el Portal de vista previa de Azure.

En este tutorial realizará lo siguiente:

- Creación de una nueva base de datos SQL.
- Creación de un servidor para la base de datos.
- Carga de AdventureWorksDW en la nueva base de datos.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Inicio de sesión y búsqueda del almacén de datos de SQL

1. Inicie sesión en el [Portal de vista previa](https://portal.azure.com).

2. En el menú de concentrador, haga clic en **Nuevo** > **Datos y almacenamiento** > **Almacenamiento de datos SQL**.

	![Creación del almacenamiento de datos](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

## Configuración del rendimiento y otras opciones básicas

En el panel **Almacenamiento de datos SQL**, rellene los campos siguientes. **Servidor** y **Origen** se configuran en las secciones siguientes.

1. **Nombre de base de datos**: escriba un nombre para la base de datos de almacenamiento de datos SQL.

2. **Rendimiento**: puede ajustar el rendimiento con el que se iniciará la instancia durante el aprovisionamiento. Se recomienda comenzar con 400 DWU, ya que ello le permitirá ver más de las ventajas MPP que ofrece el almacenamiento de datos SQL.
     
    ![Nombre y DWU](./media/sql-data-warehouse-get-started-provision/name-and-dwu.png)

    > [AZURE.NOTE] El rendimiento se mide en unidades de almacenamiento de datos (DWU). A medida que aumentan las DWU, el almacenamiento de datos SQL aumenta los recursos informáticos disponibles para las operaciones de bases de datos de almacenamiento de datos.

	> [AZURE.NOTE] Puede modificar de manera rápida y sencilla el nivel de rendimiento después de crear la base de datos. Por ejemplo, si no está usando la base de datos, mueva el control deslizante hacia la izquierda para reducir los costes. O bien, puede aumentar el rendimiento cuando sean necesarios más recursos. Estas son las ventajas de escalabilidad que ofrece el almacenamiento de datos SQL.
	

2. **Grupos de recursos** Mantenga los valores predeterminados. Los grupos de recursos son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Obtenga más información sobre los [grupos de recursos](../azure-portal/resource-group-portal.md).
3. **Suscripción**. Seleccione la suscripción a la que desea facturar esta base de datos.


## Configuración de un servidor lógico

3. Haga clic en **Servidor** > **Crear un servidor nuevo**. Esto crea un servidor lógico que se asociará a la base de datos. Si ya dispone de un servidor V12 que desee usar, elija el servidor existente y vaya a la sección siguiente.

    ![Creación de un servidor nuevo](./media/sql-data-warehouse-get-started-provision/create-new-server.png)

    >[AZURE.NOTE] En el Almacenamiento de datos SQL y en la base de datos SQL, un servidor proporciona una forma coherente de configurar las bases de datos en la nube. En Azure, aunque el servidor se asocia a un solo centro de datos, este no forma parte del hardware físico como en una instancia local de SQL Server, sino que forma parte del software de servicio. Por ello se llama servidor lógico. Tenga en cuenta que, a diferencia de la vida real, las cargas de trabajo que ejecutan las bases de datos y los almacenes de datos en el mismo servidor no tendrán un impacto en el rendimiento entre sí.

1. En la ventana **Nuevo servidor**, rellene la información solicitada.

    Asegúrese de almacenar el nombre del servidor, el nombre de administrador y la contraseña en algún lugar. Necesitará esta información para iniciar sesión en el servidor. 
	- **Nombre del servidor**. Escriba un nombre para el servidor lógico. 
	- **Nombre de administrador del servidor**. Escriba un nombre de usuario para la cuenta de administrador del servidor. 
	- **Contraseña**. Escriba la contraseña del administrador del servidor. 
	- **Ubicación**. Elija una ubicación geográfica cercana a usted o a los demás recursos de Azure. Esto reducirá la latencia de red, ya que todas las bases de datos y recursos que pertenecen a su servidor lógico se encontrarán físicamente en la misma región.

    ![Configuración de un servidor nuevo](./media/sql-data-warehouse-get-started-provision/configure-new-server.png)

1. Haga clic en **Aceptar** para guardar la configuración del servidor.

## Carga de la base de datos de muestra

1. Elija **Origen** > **Muestra** para inicializar la nueva base de datos con la base de datos de muestra AdventureWorksDW. 

    ![Creación del almacenamiento de datos](./media/sql-data-warehouse-get-started-provision/create-data-warehouse.png)

## Finalización de la creación de la base de datos

1. Haga clic en **Crear** para crear la base de datos del almacenamiento de datos SQL. 

1. Ahora, lo único que tiene que hacer es esperar unos minutos. Cuando termine, se mostrará la base de datos de muestra en su página principal.

    ![Vista del portal del almacenamiento de datos SQL](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

## Pasos siguientes

Ahora que ha creado una base de datos de muestra para el almacenamiento de datos SQL, obtenga información sobre cómo usar el almacenamiento de datos SQL en el siguiente tutorial.

- [Conexión y consulta](./sql-data-warehouse-get-started-connect-query.md).

	> [AZURE.NOTE] Queremos mejorar este artículo. Si elige responder que no a la pregunta de si le resultó útil este artículo, incluya una sugerencia breve sobre lo que falta o cómo piensa que se podría mejorar el artículo. Gracias de antemano.

<!-----HONumber=Oct15_HO1-->