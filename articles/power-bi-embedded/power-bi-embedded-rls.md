<properties
   pageTitle="Seguridad de nivel de fila con Power BI Embedded"
   description="Más información sobre la seguridad de nivel de fila con Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Seguridad de nivel de fila con Power BI Embedded

Seguridad de nivel de fila (RLS) puede utilizarse para restringir el acceso de usuario a datos determinados dentro de un informe o un conjunto de datos, lo que permite a los distintos usuarios utilizar el mismo informe mientras todos ven datos diferentes. Power BI Embedded ahora admite conjuntos de datos configurados con RLS.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Para sacar el máximo partido de RLS, es importante comprender tres conceptos principales: usuarios, roles y reglas. Profundicemos en cada uno de ellos:

**Usuarios**: estos son los usuarios finales reales que ven los informes. En Power BI Embedded, los usuarios se identifican por la propiedad de nombre de usuario en un token de la aplicación.

**Roles**: los usuarios pertenecen a roles. Un rol es un contenedor de reglas y se puede adoptar un nombre como "Administrador de ventas" o "Representante de ventas". En Power BI Embedded, los usuarios se identifican por la propiedad de roles en un token de la aplicación.

**Reglas**: los roles tienen reglas y dichas reglas son los filtros reales que se van a aplicar a los datos. Esto podría ser tan simple como "Country = USA" o algo mucho más dinámico.

### Ejemplo

Durante el resto de este artículo, presentaremos un ejemplo de la creación de un RLS y después su consumo dentro de una aplicación incrustada. Nuestro ejemplo utiliza el archivo PBIX del [ejemplo de análisis de venta minorista](http://go.microsoft.com/fwlink/?LinkID=780547).

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Nuestro ejemplo de análisis de venta minorista muestra las ventas de todas las tiendas de una determinada cadena minorista. Sin RLS, no importa qué administrador de distrito inicia sesión y ve el informe, todos verán los mismos datos. Los directivos han determinado que cada administrador de distrito solo debe ver las ventas de los almacenes que administra y, para ello, podemos usar RLS.

RLS se ha creado en Power BI Desktop. Cuando se abren el conjunto de datos y el informe, podemos cambiar a la vista de diagrama para ver el esquema:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Estas son algunas cosas a tener en cuenta con este esquema:

-	Todas las medidas, como **Total Sales**, se almacenan en la tabla de hechos **Sales**.
-	Hay cuatro tablas de dimensiones adicionales relacionadas: **Item**, **Time**, **Store** y **District**.
-	Las flechas en las líneas de relación indican de qué forma los filtros pueden fluir de una tabla a otra. Por ejemplo, si se coloca un filtro en **Time[Date]**, en el esquema actual solo se deberían filtrar valores de la tabla **Sales**. Ninguna otra tabla se vería afectada por este filtro, ya que todas las flechas en las líneas de relación apuntan a la tabla de ventas y ninguna a otro lado.
-	La tabla **District** indica quién es el administrador de cada distrito:

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

En función de este esquema, si se aplica un filtro a la columna **District Manager** de la tabla District y si el filtro coincide con el usuario que está viendo el informe, dicho filtro también filtrará las tablas **Store** y **Sales** solo para mostrar los datos de ese administrador de distrito determinado.

Este es el procedimiento:

1.	En la pestaña Modelado, haga clic en **Administrar roles**. ![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.	Cree un nuevo rol denominado **Manager**. ![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.	En la tabla **District** escriba la siguiente expresión de DAX:**[District Manager] = USERNAME()** ![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.	Para asegurarse de que funcionan las reglas, en la pestaña **Modelado** haga clic en la pestaña **Ver como roles** y, después, escriba lo siguiente: ![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Los informes mostrarán ahora los datos como si hubiera iniciado sesión como **Andrew Ma**.

Al aplicar el filtro, tal como hicimos aquí, se filtrarán todos los registros de las tablas **District**, **Store** y **Sales**. Sin embargo, debido a la dirección del filtro en las relaciones entre las tablas **Sales** y **Time**, **Sales** y **Item**, y **Item** y **Time** no se filtrarán.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Puede que sea correcto para este requisito, sin embargo, si no queremos que los administradores vean aquellos elementos para los cuales no tengan ninguna venta, podríamos activar el filtrado cruzado bidireccional para la relación y dirigir el filtro de seguridad en ambas direcciones. Esto puede hacerse mediante la edición de la relación entre **Sales** e **Items**, de la forma siguiente:

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

Ahora, los filtros también pueden fluir de la tabla Sales a la tabla **Items**:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Nota** Si utiliza el modo DirectQuery para los datos, debe habilitar el filtrado cruzado bidireccional mediante la selección de estas dos opciones:

1.	**Archivo** -> **Opciones y configuración** -> **Características de vista previa** -> **Enable cross filtering in both directions for DirectQuery** (Habilitar filtrado cruzado en ambas direcciones para DirectQuery).
2.	**Archivo** -> **Opciones y configuración** -> **DirectQuery** -> **Allow unrestricted measure in DirectQuery mode** (Permitir medida sin restricciones en el modo DirectQuery).


Para más información acerca de filtrado cruzado bidireccional, descargue el documento [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) ([Filtrado cruzado bidireccional en SQL Server Analysis Services 2016 y Power BI Desktop]).

Esto concluye todo el trabajo que debe hacerse en Power BI Desktop, pero hay una más parte del trabajo que debe hacerse para que funcionen las reglas de RLS que definimos en Power BI Embedded. Los usuarios se autentican y autorizan por su aplicación y se utilizan tokens de la aplicación para conceder a ese usuario acceso a un informe de Power BI Embedded específico. Power BI Embedded no tiene información específica en quién es el usuario. Para que RLS funcione, debe pasar contexto adicional como parte de su token de aplicación:
-	**username** (opcional): se utiliza con RLS y es una cadena que puede usarse para ayudar a identificar el usuario cuando se aplican las reglas RLS. Consulte el uso de la seguridad de nivel de fila con Power BI Embedded
-	**roles**: una cadena que contiene las funciones que seleccione al aplicar las reglas de seguridad de nivel de fila. Si se pasa más de un rol, se deben pasar como una matriz de cadenas.

Si la propiedad username está presente, también debe pasar al menos un valor en los roles.

El token de la aplicación completo tendrá un aspecto similar al siguiente:

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Ahora, con todas las piezas juntas, cuando un usuario inicie sesión en nuestra aplicación para ver este informe, solo podrá ver los datos que tenga permiso para ver, tal como se ha definido en nuestra seguridad de nivel de fila.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## Consulte también
[Seguridad de nivel de fila](https://powerbi.microsoft.com/es-ES/documentation/powerbi-admin-rls/)

<!---HONumber=AcomDC_0713_2016-->