<properties 
   pageTitle="Introducción al enmascaramiento de datos dinámicos de Base de datos SQL" 
   description="Introducción al enmascaramiento de datos dinámicos de Base de datos SQL" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="02/24/2015"
   ms.author="nadavh; ronmat; v-romcal; v-stste"/>

# Introducción al enmascaramiento de datos dinámicos de Base de datos SQL 

## Información general

El enmascaramiento datos dinámicos de Base de datos SQL limita la exposición de información confidencial mediante el enmascaramiento a los usuarios sin privilegios. El enmascaramiento de datos dinámicos se encuentra en vista previa para los niveles de servicio Básico, Estándar y Premium en la versión V12 de Base de datos SQL de Azure.

El enmascaramiento de datos dinámicos ayuda a impedir el acceso no autorizado a datos confidenciales permitiendo a los usuarios designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian.

Por ejemplo, una persona encargada del soporte técnico de un centro de llamadas podría identificar a los autores de las llamadas a partir de varios dígitos de su número de seguridad social o el número de tarjeta de crédito: sin embargo, esta es una información que no debería exponerse por completo a esta persona encargada del soporte técnico. Un desarrollador puede definir una regla de enmascaramiento que se aplicará a cada resultado de la consulta que enmascare todo el contenido excepto los cuatro últimos dígitos de un número de seguridad social o un número de tarjeta de crédito en el conjunto de resultados. Para obtener otro ejemplo, mediante el uso de una máscara de datos apropiada para proteger los datos de información de identificación personal, un desarrollador puede consultar los entornos de producción para solucionar problemas sin infringir las reglamentaciones.

## Conceptos básicos del enmascaramiento de datos dinámicos en Base de datos SQL

La directiva de enmascaramiento de datos dinámicos se establece en el Portal de administración de Azure, y la instalación se completa mediante la cadena de conexión con seguridad habilitada utilizada por la aplicación u otros clientes que tienen acceso a la base de datos.

### Permisos de enmascaramiento de datos dinámicos

El enmascaramiento de datos dinámicos puede configurarse mediante el administrador de Base de datos de Azure, el administrador del servidor o los roles de autoridad de seguridad.

### Directiva de enmascaramiento de datos dinámicos

* **Inicios de sesión privilegiados**: un conjunto de inicios de sesión que obtendrán los datos sin enmascarar en los resultados de las consultas SQL.
  
* **Reglas de enmascaramiento**: un conjunto de reglas que definen los campos designados para el enmascaramiento y la función de enmascaramiento que se va a utilizar. Los campos designados pueden definirse mediante un nombre de tabla de base de datos y un nombre de columna o mediante un nombre de alias.

* **Enmascaramiento por**: puede hacerse en el origen o el destino. El enmascaramiento puede configurarse a nivel de origen mediante la identificación del nombre de **tabla** y el nombre de **columna**, o a nivel de resultados mediante la identificación del **Alias** que se usa en la consulta. Si está familiarizado con la arquitectura de datos de la base de datos y desea limitar la exposición de todos los resultados de consulta, puede ser preferible una regla de máscara de origen. Puede agregar una regla de máscara de resultados cuando desee limitar la exposición a los resultados de la consulta sin analizar la arquitectura de datos de la base de datos o para un campo que es posible que llegue de orígenes diferentes.  
  
* **Restricción ampliada**: limita la exposición de datos confidenciales, pero puede afectar a la funcionalidad de algunas aplicaciones.

>[AZURE.TIP] Use la opción **Restricción ampliada** para restringir el acceso a los desarrolladores que usan un acceso directo a la base de datos y ejecutan consultas SQL para solucionar problemas.

* **Funciones de enmascaramiento**: un conjunto de métodos que controlan la exposición de datos para diferentes escenarios.

|Función de enmascaramiento | Lógica de enmascaramiento|
|----------|---------------|
|**Predeterminado**|**Enmascaramiento completo según los tipos de datos de los campos designados**<br/><br/>• Use XXXXXXXX o menos X si el tamaño del campo es menor que 8 caracteres para los tipos de datos de cadena (nchar, ntext, nvarchar).<br/>• Use un valor de cero para los tipos de datos numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use la hora actual para los tipos de datos de fecha y hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variant SQL, se utiliza el valor predeterminado del tipo actual.<br/>• Para XML, se utiliza el documento <masked/>.<br/>• Use un valor vacío para los tipos de datos especiales (timestamp  table, hierarchyid, GUID, binary, image, varbinary spatial types).
| **Tarjeta de crédito** |**Método de enmascaramiento que expone los últimos cuatro dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de una tarjeta de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Número de seguridad social** |**Método de enmascaramiento que expone los dos últimos dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de un número de seguridad social estadounidense.<br/><br/>XXX-XX-XX12 |
| **Correo electrónico** | **Método de enmascaramiento que expone la primera letra y el dominio** con una cadena constante como prefijo en el formato de una dirección de correo electrónico.<br/><br/>aXX@XXXX.com |
| **Número aleatorio** | **Método de enmascaramiento que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función de enmascaramiento será un número constante.<br/><br/>![Navigation pane][Image1] |
| **Texto personalizado** | **Método de enmascaramiento que expone la primera y la última letra** y agrega una cadena de relleno personalizada en el medio.<br/>prefijo[relleno]sufijo<br/><br/>![Navigation pane][Image2] |

  
<a name="Anchor1"></a>
### Cadena de conexión con seguridad habilitada

Cuando se configura el enmascaramiento de datos dinámicos, Azure proporciona una cadena de conexión con seguridad habilitada para la base de datos. Los clientes que utilizan esta cadena de conexión son los únicos que tienen sus datos confidenciales enmascarados según la directiva de enmascaramiento de datos dinámicos. También deberá actualizar los clientes existentes (ejemplo: aplicaciones) para utilizar el nuevo formato de cadena de conexión.

* Formato de cadena de conexión original: <*nombre de servidor*>.database.windows.net
* Cadena de conexión con seguridad habilitada: <*nombre de servidor*>.database.**secure**.windows.net

También puede cambiar el ajuste de configuración **ACCESO CON SEGURIDAD HABILITADA** de **OPCIONAL** a **REQUERIDO**, lo que garantiza que no hay ninguna opción de tener acceso a la base de datos con la cadena de conexión original y omitir los directiva de enmascaramiento de datos dinámicos. Mientras experimenta con el enmascaramiento de datos dinámicos mediante clientes específicos (ejemplo, una aplicación de la fase de desarrollo o SSMS), elija **OPCIONAL**. Para la producción, elija **REQUERIDO**.<br/><br/>

![Navigation pane][Image3]<br/><br/>

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante el Portal de administración de Azure

1. Inicie el Portal de vista previa de Azure en [https://portal.azure.com](https://portal.azure.com).
	 
2. Desplácese hasta el módulo de configuración de la base de datos que incluye los datos confidenciales que desea enmascarar.
	
3. Haga clic en el icono **Enmascaramiento de datos dinámicos**, con lo que se iniciará el módulo de configuración del **Enmascaramiento de datos dinámicos**.

	* Como alternativa, puede desplazarse hacia abajo hasta la sección **Operaciones** y hacer clic en **Enmascaramiento de datos dinámicos**.
	 
	![Navigation pane][Image4]<br/><br/>

4. En el módulo de configuración **Enmascaramiento de datos dinámicos**, haga clic en **HABILITADO** para habilitar los característica de enmascaramiento de datos dinámicos.

	![Navigation pane][Image5]<br/><br/>

5. Escriba los inicios de sesión con privilegios que podrán tener acceso a los datos confidenciales sin enmascarar.
 
	![Navigation pane][Image6]

	>[AZURE.TIP] Para hacer que el nivel de aplicación pueda mostrar datos confidenciales para los usuarios con privilegios de la aplicación, agregue el inicio de sesión SQL de la aplicación que se utiliza para consultar la base de datos. Se recomienda que esta lista incluya un número mínimo de inicios de sesión para minimizar la exposición de los datos confidenciales.

6. Haga clic en **Agregar máscara** para abrir el módulo de configuración **Agregar regla de enmascaramiento**.
	
7. Elija **Enmascarar por** para indicar si el enmascaramiento se realiza en el origen o el destino. El enmascaramiento puede configurarse a nivel de origen mediante la identificación del nombre de **tabla** y el nombre de **columna**, o a nivel de resultados mediante la identificación del **Alias** que se usa en la consulta. Si está familiarizado con la arquitectura de datos de la base de datos y desea limitar la exposición de todos los resultados de consulta, puede ser preferible una regla de máscara de origen. Puede agregar una regla de máscara de resultados cuando desee limitar la exposición a los resultados de la consulta sin analizar la arquitectura de datos de la base de datos o para un campo que es posible que llegue de orígenes diferentes.

8. Escriba el nombre de **Tabla** y el nombre de **Columna**, o el nombre de **Alias**, para definir los campos designados para enmascararse.

9. Elija un **Formato de campo de enmascaramiento** en la lista de categorías de enmascaramiento de datos confidenciales.

	![Navigation pane][Image7]<br/><br/>		

10. Haga clic en **Guardar** en el módulo de regla de enmascaramiento de datos para actualizar el conjunto de reglas de enmascaramiento en la directiva de enmascaramiento dinámico.

11. Considere la posibilidad de seleccionar **UTILIZAR RESTRICCIONES AMPLIADAS** para limitar la exposición de datos confidenciales a través de las consultas ad hoc.

12. Haga clic en **Guardar** en el módulo de configuración de enmascaramiento de datos para guardar la regla de enmascaramiento nueva o actualizada.

13. En **Acceso con seguridad habilitada**, haga clic en **OPCIONAL** o **REQUERIDO**. Para obtener más información, consulte [Cadena de conexión con la seguridad habilitada](#Anchor1).

	![Navigation pane][Image8]
	
## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante el Portal de administración de Azure clásico

1. Inicie el Portal de administración de Azure clásico en [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Haga clic en la base de datos que desea enmascarar y, a continuación, haga clic en la pestaña **AUDITORÍA Y SEGURIDAD**.

3. En **Enmascaramiento de datos dinámicos**, haga clic en **HABILITADO** para habilitar la característica de enmascaramiento de datos dinámicos.  

4. Escriba los inicios de sesión con privilegios que podrán tener acceso a los datos confidenciales sin enmascarar.

	>[AZURE.TIP] Para hacer que el nivel de aplicación pueda mostrar datos confidenciales para los usuarios con privilegios de la aplicación, agregue el inicio de sesión de la aplicación que se usa para consultar la base de datos. Se recomienda que esta lista incluya un número mínimo de inicios de sesión para minimizar la exposición de los datos confidenciales.

	![Navigation pane][Image9]

5. En la parte inferior de la página en la barra de menús, haga clic en **Agregar máscara** para abrir la ventana de configuración de la regla de enmascaramiento.

6. Elija **Enmascarar por** para indicar si el enmascaramiento se realiza en el origen o el destino. El enmascaramiento puede configurarse a nivel de origen mediante la identificación del nombre de **tabla** y el nombre de **columna**, o a nivel de resultados mediante la identificación del **Alias** que se usa en la consulta. Si está familiarizado con la arquitectura de datos de la base de datos y desea limitar la exposición de todos los resultados de consulta, puede ser preferible una regla de máscara de origen. Puede agregar una regla de máscara de resultados cuando desee limitar la exposición a los resultados de la consulta sin analizar la arquitectura de datos de la base de datos o para un campo que es posible que llegue de orígenes diferentes.

7. Escriba el nombre de **Tabla** y el nombre de **Columna**, o el nombre de **Alias**, para definir los campos designados para enmascararse.

8. Elija una **FUNCIÓN DE ENMASCARAMIENTO** en la lista de categorías de enmascaramiento de datos confidenciales.

	![Navigation pane][Image10] 
 	
9. Haga clic en **Actualizar** en la ventana de regla de enmascaramiento de datos para actualizar el conjunto de reglas de enmascaramiento en la directiva de enmascaramiento de datos dinámicos.

10. Considere la posibilidad de seleccionar **UTILIZAR RESTRICCIONES AMPLIADAS** para limitar la exposición de datos confidenciales a través de las consultas ad hoc.

11. Haga clic en **GUARDAR** para guardar la regla de enmascaramiento nueva o actualizada.

## Configuración del enmascaramiento de datos dinámicos para la base de datos mediante la API de REST

Consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/dn505719.aspx).

[Image1]: ./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png
[Image2]: ./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png
[Image3]: ./media/sql-database-dynamic-data-masking-get-started/3_DDM_Current_Preview.png
[Image4]: ./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png
[Image5]: ./media/sql-database-dynamic-data-masking-get-started/5_DMM_Policy_Tile.png
[Image6]: ./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png
[Image7]: ./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png
[Image8]: ./media/sql-database-dynamic-data-masking-get-started/8_DDM_Security_Enabled_Access.png
[Image9]: ./media/sql-database-dynamic-data-masking-get-started/9_DMM_Policy_Classic_Portal.png
[Image10]: ./media/sql-database-dynamic-data-masking-get-started/10_DDM_Add_Masking_Rule_Classic_Portal.png
<!--HONumber=47-->
