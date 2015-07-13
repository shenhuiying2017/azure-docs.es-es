<properties
   pageTitle="Introducción al aprovisionamiento de una instancia de Almacenamiento de datos SQL | Microsoft Azure"
   description="Aprovisione una instancia de Almacenamiento de datos SQL con estos pasos e instrucciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Introducción al aprovisionamiento de una instancia de Almacenamiento de datos SQL #

Este artículo es una guía acelerada para ayudarle a aprovisionar una instancia de Almacenamiento de datos SQL en Azure. Si sigue esta guía, realizará las siguientes tareas:

1. Creación de una nueva base de datos de Almacenamiento de datos SQL.
2. Configuración de un servidor lógico nuevo.
3. Definición de una regla del firewall de Azure para habilitar el acceso de cliente externo.

## Evaluación gratuita de Azure ##
Para completar las tareas que aparecen a continuación, debe tener una suscripción a Azure. Si todavía no tiene acceso a una suscripción a Azure, el primer paso debe ser solucionar esto.

Puede obtener acceso a una [evaluación gratuita][] que le permite probar cualquiera de los servicios de Azure, incluido Almacenamiento de datos SQL.


## Inicio de sesión en el Portal de Azure ##

Una vez que tenga una suscripción, puede iniciar sesión en el [Portal de Azure][]. Continúe e inicie sesión ahora.

En la siguiente serie de pasos, estableceremos rápidamente un servidor lógico nuevo y crearemos una base de datos de Almacenamiento de datos SQL.

## Ubicación del servicio de Almacenamiento de datos SQL

Lo primero que debemos hacer es ubicar el servicio de Almacenamiento de datos SQL en el Portal de Azure.

El botón Nuevo está en la esquina inferior izquierda del Portal de Azure. El botón Nuevo es el punto de partida para crear cualquier servicio en Azure.

- Haga clic en el botón Nuevo ahora.

### Datos + almacenamiento

Cuando hizo clic en el botón Nuevo se abrieron todas las categorías de servicio dentro de Azure. Almacenamiento de datos SQL reside en la categoría "Datos + almacenamiento".

- Haga clic en "Datos + almacenamiento" para obtener detalles sobre los servicios que Azure ofrece en esta categoría.

### Almacenamiento de datos SQL

Como puede ver, Azure ofrece grandes cantidades de datos y motores de almacenamiento. Sin embargo, esta guía de introducción es para SQLDW.

- Continúe y seleccione Almacenamiento de datos SQL.

## Configuración de Almacenamiento de datos SQL

Para completar el proceso de aprovisionamiento, solo falta configurar Almacenamiento de datos SQL.


### Nombre de la base de datos

La primera configuración es poner nombre a la base de datos.



- Para esta guía de inicio rápido, el nombre de la base de datos será "MySQLDW".


> [AZURE.NOTE]Por supuesto, cuando cree su propia base de datos podrá ponerle el nombre que desee. De todos modos, el nombre debe cumplir con los requisitos de nomenclatura básicos.

### Rendimiento

La opción del rendimiento es **importante**. Almacenamiento de datos SQL proporciona su potencia escalable mediante este control deslizante. Puede aumentar o disminuir el rendimiento en cualquier momento, no solo cuando configure el clúster. Cuanto más se desliza a la derecha, mayor es la cantidad de recursos a su disposición. Si esos recursos ya no son necesarios, puede mover el control deslizante en la otra dirección, para ahorrar costos. Almacenamiento de datos SQL le permite cambiar su perfil de rendimiento a petición, sin que sea necesario volver a crear el clúster o mover los datos.

- Use ahora el control deslizante para ver cómo las unidades de almacenamiento de datos aumentan a medida que se desliza a la derecha y disminuyen cuando mueve el control deslizante a la izquierda.

- Antes de dejar este paso, asegúrese de que el control deslizante esté nuevamente a la izquierda. La base de datos nueva es solo un almacenamiento de datos pequeño, por lo que no necesitaremos demasiado; ahorre recursos para el resto de la evaluación.

### Seleccionar origen

Esta opción ofrece la posibilidad de comenzar con una base de datos vacía. Elija la nueva base de datos como punto de partida.

> [AZURE.NOTE]Hay disponible también otra opción. También se puede crear la base de datos a partir de un punto de restauración preexistente; una opción de restauración.

### Servidor lógico

La base de datos de Almacenamiento de datos SQL reside en un servidor lógico. El servidor lógico aporta coherencia de la configuración en el nivel de instancia para varias bases de datos y ubica el servicio en un centro de datos de Azure.

Las opciones que se deben establecer son: 1. Nombre del servidor 2. Nombre del administrador del servidor 3. Contraseña 4. Ubicación del centro de datos 5. Permiso para que los servicios de Azure obtengan acceso al servidor.

Puede establecer estos valores como considere oportuno. El nombre del servidor debe ser único. Se recomienda elegir un centro de datos cercano para reducir la latencia de red. Almacenamiento de datos SQL también contiene características eficaces que aprovechan los otros servicios de Azure. Por lo tanto, se recomienda dejar habilitada la casilla para el acceso a los servicios de Azure.

> [AZURE.NOTE]Almacenamiento de datos SQL debe usar un servidor V12. Asegúrese de que esta opción esté establecida en SÍ. Además, las bases de datos de Almacenamiento de datos SQL y Bases de datos SQL pueden compartir el servidor lógico. Sin embargo, debe ser un servidor V12.

> [AZURE.NOTE]Anote el nombre del servidor, el nombre del administrador del servidor y la contraseña y guárdelos en un lugar seguro. Necesitará esta información para conectarse a la base de datos de Almacenamiento de datos SQL.

### El grupos de recursos
Los grupos de recursos son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure.

En esta guía de inicio rápido, el grupo de recursos puede estar configurado en sus valores predeterminados.

Obtenga más información acerca de los [grupos de recursos].

### La suscripción
Un usuario único podría tener una o más suscripciones a Azure. Si tiene más de una suscripción asociada con su inicio de sesión, puede elegir la suscripción que desea utilizar.

Sin embargo, en esta guía es posible usar los valores predeterminados.

Sigamos y creemos la instancia de Almacenamiento de datos SQL.

## Creación del almacenamiento de datos ##
Para crear el almacenamiento de datos, solo falta hacer clic en el botón Crear.

¡Enhorabuena! Creó su primera base de datos de Almacenamiento de datos SQL.

Ahora debería volver a la página principal del [Portal de Azure][]. Observe que la base de datos de Almacenamiento de datos SQL se agregó a la página.


En este punto, nadie tiene acceso a la base de datos de Almacenamiento de datos SQL. De manera predeterminada y por motivos de seguridad, el acceso de clientes a la base de datos todavía no está configurado.

Por lo tanto, el último paso del proceso de aprovisionamiento es configurar el servicio para el acceso externo.

## Configuración del firewall de Azure ##

Haga lo siguiente para configurar por primera vez el firewall de Azure:

1. Haga clic en Examinar en el panel de navegación de la izquierda.

2. Elija Servidores SQL Server.

3. Seleccione el servidor SQL Server lógico.

4. Elija la configuración.

5. Haga clic en el firewall.

6. Establezca la regla del firewall.

    Aquí debe hacer un par de cosas: - Ponerle nombre a la regla del firewall - Proporcionar un intervalo de IP

    > [AZURE.NOTE]El intervalo de direcciones IP del cliente que debe incluir es su dirección IP externa o pública. Para conocer su dirección IP externa, puede usar varios sitios web, como <a href="http://www.whatismyip.com" target="_blank">www.whatismyip.com</a>

7. Guarde la regla del firewall.


Ahora que configuró el firewall, podría establecer conexiones desde su escritorio a la instancia de Almacenamiento de datos SQL que acaba de crear.

## Pasos siguientes

Ahora que se aprovisionó correctamente el servicio SQLDW, podemos aprender a usarlo.

Por lo tanto, los pasos siguientes sirven para realizar la: 1. [Conexión y consulta] de la base de datos SQLDW 2. Exportación de datos desde la base de datos SQLDW a Almacenamiento de blobs de Azure 3. Carga de más datos en la base de datos SQLDW.


<!--Image references-->


<!-- Articles -->
[Conexión y consulta]: ./sql-data-warehouse-get-started-connect-query/
[grupos de recursos]: ./azure-preview-portal-using-resource-groups/

<!--External links-->
[evaluación gratuita]: https://azure.microsoft.com/es-es/pricing/free-trial/
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=July15_HO1-->