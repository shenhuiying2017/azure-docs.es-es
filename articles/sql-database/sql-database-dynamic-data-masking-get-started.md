<properties 
   pageTitle="Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de vista previa de Azure)" 
   description="Cómo empezar a usar el enmascaramiento de datos dinámicos de Base de datos SQL en el Portal de vista previa de Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Introducción al enmascaramiento de datos dinámicos de Base de datos SQL (Portal de vista previa de Azure)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Información general

El enmascaramiento datos dinámicos de Base de datos SQL limita la exposición de información confidencial mediante el enmascaramiento a los usuarios sin privilegios. El enmascaramiento de datos dinámicos se encuentra en vista previa para los niveles de servicio Básico, Estándar y Premium en la versión V12 de Base de datos SQL de Azure.

El enmascaramiento de datos dinámicos ayuda a impedir el acceso no autorizado a datos confidenciales permitiendo a los usuarios designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian.

Por ejemplo, una persona encargada del soporte técnico de un centro de llamadas podría identificar a los autores de las llamadas a partir de varios dígitos de su número de seguridad social o el número de tarjeta de crédito: sin embargo, esta es una información que no debería exponerse por completo a esta persona encargada del soporte técnico. Un desarrollador puede definir una regla de enmascaramiento que se aplicará a cada resultado de la consulta que enmascare todo el contenido excepto los cuatro últimos dígitos de un número de seguridad social o un número de tarjeta de crédito en el conjunto de resultados. Para obtener otro ejemplo, mediante el uso de una máscara de datos apropiada para proteger los datos de información de identificación personal, un desarrollador puede consultar los entornos de producción para solucionar problemas sin infringir las reglamentaciones.

## Conceptos básicos del enmascaramiento de datos dinámicos en Base de datos SQL

Para configurar una directiva de enmascaramiento de datos dinámicos en el Portal de vista previa de Azure se selecciona la operación de enmascaramiento de datos dinámicos en la hoja de configuración de Base de datos de SQL. Antes de configurar el enmascaramiento de datos dinámico, compruebe si usa un ["Cliente de nivel inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


### Permisos de enmascaramiento de datos dinámicos

El enmascaramiento de datos dinámicos puede configurarse mediante el administrador de Base de datos de Azure, el administrador del servidor o los roles de autoridad de seguridad.

### Directiva de enmascaramiento de datos dinámicos

* **Inicios de sesión privilegiados**: un conjunto de inicios de sesión que obtendrán datos sin enmascarar en los resultados de la consulta SQL.
  
* **Reglas de enmascaramiento**: un conjunto de reglas que definen los campos designados para el enmascaramiento y la función de enmascaramiento que se va a usar. Los campos designados pueden definirse mediante un nombre de tabla de base de datos y un nombre de columna.

* **Funciones de enmascaramiento**: un conjunto de métodos que controlan la exposición de datos para diferentes escenarios.

| Función de enmascaramiento | Lógica de enmascaramiento |
|----------|---------------|
| **Valor predeterminado** |**Enmascaramiento completo según los tipos de datos de los campos designados**<br/><br/>• Use XXXXXXXX o menos X si el tamaño del campo es inferior a 8 caracteres para los tipos de datos de cadena (nchar, ntext, nvarchar).<br/>• Use un valor de cero para los tipos de datos numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para los tipos de datos de fecha y hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variante SQL, se usa el valor predeterminado del tipo actual.<br/>• Para XML, se usa el documento <masked/>.<br/>• Use un valor vacío para los tipos de datos especiales (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial).
| **Tarjeta de crédito** |**Método de enmascaramiento que expone los últimos cuatro dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de una tarjeta de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Número de seguridad social** |**Método de enmascaramiento que expone los dos últimos dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de un número de seguridad social estadounidense.<br/><br/>XXX-XX-XX12 |
| **Correo electrónico** | **Método de enmascaramiento que expone la primera letra y reemplaza el dominio con XXX.com** con una cadena constante como prefijo en el formato de una dirección de correo electrónico.<br/><br/>aXX@XXXX.com |
| **Número aleatorio** | **Método de enmascaramiento que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función de enmascaramiento será un número constante.<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método de enmascaramiento que expone el primero y el último caracter** y agrega una cadena de relleno personalizada en el medio.<br/>prefijo[relleno]sufijo<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### Cadena de conexión con seguridad habilitada

Si está usando un ["Cliente de nivel inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), tiene que actualizar los clientes existentes (ejemplo: aplicaciones) para usar un formato de cadena de conexión modificada. Haga clic [aquí](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) para obtener información detallada.

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante el Portal de vista previa de Azure

1. Inicie el Portal de vista previa de Azure en [https://portal.azure.com](https://portal.azure.com).
	 
2. Desplácese hasta el módulo de configuración de la base de datos que incluye los datos confidenciales que desea enmascarar.
	
3. Haga clic en el icono **Enmascaramiento de datos dinámicos**, con lo que se iniciará la hoja de configuración del **Enmascaramiento de datos dinámicos**.

	* Como alternativa, puede desplazarse hacia abajo hasta la sección **Operaciones** y hacer clic en **Enmascaramiento de datos dinámicos**.
	 
	![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. En la hoja de configuración **Enmascaramiento de datos dinámicos**, haga clic en **Añadir máscara** para abrir la hoja de configuración **Agregar regla de enmascaramiento**.

	![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. Seleccione la **tabla** y la **columna** para definir los campos designados para enmascararse.

6. Elija un **Formato de campo de enmascaramiento** en la lista de categorías de enmascaramiento de datos confidenciales.

	![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. Haga clic en **Guardar** en la hoja de regla de enmascaramiento de datos para actualizar el conjunto de reglas de enmascaramiento en la directiva de enmascaramiento dinámico.

8. Escriba los inicios de sesión con privilegios que podrán tener acceso a los datos confidenciales sin enmascarar.
 
	![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]Para hacer que el nivel de aplicación pueda mostrar datos confidenciales para los usuarios con privilegios de la aplicación, agregue el inicio de sesión SQL de la aplicación que se usa para consultar la base de datos. Se recomienda que esta lista incluya un número mínimo de inicios de sesión para minimizar la exposición de los datos confidenciales.

9. Haga clic en **Guardar** en la hoja de configuración de enmascaramiento de datos para guardar la directiva de enmascaramiento nueva o actualizada.

10. Si está usando un ["Cliente de nivel inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md), tiene que actualizar los clientes existentes (ejemplo: aplicaciones) para usar un formato de cadena de conexión modificada. Para obtener más información, consulte [Clientes de nivel inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante cmdlets de PowerShell

Consulte [Cmdlets de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt163521.aspx).


## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante la API de REST

Consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=Oct15_HO3-->