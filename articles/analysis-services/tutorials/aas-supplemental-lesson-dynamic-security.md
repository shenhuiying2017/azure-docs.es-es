---
title: "Lección complementaria del tutorial de Azure Analysis Services: seguridad dinámica | Microsoft Docs"
description: "En este artículo se describe cómo usar la seguridad dinámica mediante los filtros de fila del tutorial de Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 7512af39041090db22d4ef45ec46925c14c4adb9
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="supplemental-lesson---dynamic-security"></a>Lección complementaria: Seguridad dinámica

En esta lección complementaria, creará un rol función que implementa la seguridad dinámica. La seguridad dinámica proporciona seguridad de nivel de fila en función del identificador de inicio de sesión o del nombre de usuario de la persona que tenga la sesión iniciada. 
  
Para implementar seguridad dinámica, agregue una tabla al modelo que contiene los nombres de usuario de las personas que pueden conectarse al modelo y examinar los datos y objetos de dicho modelo. El modelo que crea con este tutorial se encuentra en el contexto de Adventure Works; sin embargo, para completar esta lección, debe agregar una tabla que contenga los usuarios de su propio dominio. No se necesitan las contraseñas de los nombres de usuario que se agregan. Para crear una tabla EmployeeSecurity con una pequeña muestra de los usuarios de su propio dominio, pegue los datos de empleados desde una hoja de cálculo de Excel con la característica de pegado. En un escenario real, la tabla que contiene los nombres de usuario normalmente sería una, como origen de datos, de una base de datos real. Por ejemplo, una tabla DimEmployee real.  
  
Para implementar la seguridad dinámica, debe utilizar dos funciones DAX: [USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) y [LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Estas funciones, aplicadas en una fórmula de filtro de fila, se definen en un nuevo rol. Mediante el uso de la función LOOKUPVALUE, la fórmula especifica un valor de la tabla EmployeeSecurity. Después, la fórmula pasa el valor a la función USERNAME, que especifica que el nombre del usuario que ha iniciado la sesión pertenece a este rol. Después, el usuario puede examinar los datos especificados mediante los filtros de fila del rol. En este escenario, puede especificar que los empleados de ventas solo puedan examinar los datos de ventas de Internet de los territorios de ventas en los que son miembros.  
  
Las tareas que solo se realizan en este escenario de modelo tabular de Adventure Works, pero no que se aplicarían necesariamente a un escenario real, se identifican como tales. Cada tarea incluye información adicional que describe el propósito de la tarea.  
  
Tiempo estimado para completar esta lección: **30 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema de la lección complementaria forma parte de un tutorial de modelado tabular, que debe completar en orden. Antes de realizar las tareas de esta lección complementaria, debe haber completado todas las lecciones anteriores.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Incorporación de la tabla DimSalesTerritory al proyecto de modelo tabular de AW Internet Sales  
Para implementar seguridad dinámica en este escenario de Adventure Works, debe agregar dos tablas adicionales al modelo. La primera tabla que agregará es DimSalesTerritory (como SalesTerritory), de la misma base de datos AdventureWorksDW. Posteriormente, se aplica un filtro de fila a la tabla SalesTerritory que define los datos concretos que puede examinar el usuario con la sesión iniciada.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Procedimiento para agregar la tabla DimSalesTerritory  
  
1.  En el Explorador de modelos tabulares, expanda **Orígenes de datos**, haga clic con el botón derecho en la conexión y, luego, haga clic en **Importar nuevas tablas**.  

    Si aparece el cuadro de diálogo Credenciales de suplantación, escriba las credenciales de suplantación que usó en la lección 2 sobre incorporación de datos.
  
2.  En el navegador, seleccione la tabla **DimSalesTerritory** y, luego, haga clic en **Aceptar**.    
  
3.  En el Editor de consultas, haga clic en la consulta **DimSalesTerritory** y, luego, quite la columna **SalesTerritoryAlternateKey**.  
  
7.  Haga clic en **Import**.  
  
    La nueva tabla se agrega al área de trabajo del modelo. Los objetos y datos de la tabla de origen DimSalesTerritory se importan luego en el modelo tabular de ventas de AW Internet Sales.  
  
9. Cuando la tabla se haya importado correctamente, haga clic en **Cerrar**.  

## <a name="add-a-table-with-user-name-data"></a>Incorporación de una tabla con datos de nombres de usuario  
La tabla DimEmployee de la base de datos de ejemplo AdventureWorksDW contiene usuarios del dominio AdventureWorks. Los nombres de usuario no existen en su propio entorno. Debe crear una tabla en el modelo que contenga una pequeña muestra de usuarios reales de su organización (al menos tres). Después, agregue estos usuarios como miembros al nuevo rol. No necesita las contraseñas de los nombres de usuario de ejemplo, pero sí los nombres de usuario de Windows reales de su propio dominio.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Procedimiento para agregar una tabla EmployeeSecurity  
  
1.  Abra Microsoft Excel para crear una hoja de cálculo.  
  
2.  Copie la siguiente tabla, incluida la fila de encabezado y, luego, péguela en la hoja de cálculo.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Reemplace el nombre, los apellidos y dominio\nombre de usuario por los nombres e identificadores de inicio de sesión de tres usuarios de su organización. Coloque el mismo usuario en las dos primeras filas de EmployeeId 1, lo que indica que este usuario pertenece a más de un territorio de ventas. Deje los campos EmployeeId y SalesTerritoryId tal y como están.  
  
4.  Guarde la hoja de cálculo como **SampleEmployee**.  
  
5.  En la hoja de cálculo, seleccione todas las celdas con datos de empleados, incluidos los encabezados. Luego, haga clic con el botón derecho en los datos seleccionados en **Copiar**.  
  
6.  En SSDT, haga clic en el menú **Editar** y, luego, en **Pegar**.  
  
    Si la opción Pegar está atenuada, haga clic en una columna de cualquier tabla en la ventana del Diseñador de modelos e inténtelo de nuevo.  
  
7.  En el cuadro de diálogo **Vista previa de pegado**, en **Nombre de la tabla**, escriba **EmployeeSecurity**.  
  
8.  En **Datos que se van a pegar**, compruebe que los datos incluyen todos los encabezados e información de los usuarios de la hoja de cálculo SampleEmployee.  
  
9. Compruebe que la casilla **Usar primera fila como encabezados de columna** está activada y, luego, haga clic en **Aceptar**.  
  
    Se creará una nueva tabla denominada "EmployeeSecurity" con datos de empleados copiados de la hoja de cálculo SampleEmployee.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Creación de relaciones entre las tablas FactInternetSales, DimGeography y DimSalesTerritory  
Las tablas FactInternetSales, DimGeography y DimSalesTerritory contienen una columna común SalesTerritoryId. La columna SalesTerritoryId de la tabla DimSalesTerritory contiene valores con un identificador distinto para cada territorio de ventas.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Procedimiento para crear relaciones entre las tablas FactInternetSales, DimGeography y DimSalesTerritory  
  
1.  En la Vista de diagrama, en la tabla **DimGeography**, haga clic en la columna **SalesTerritoryId** y mantenga presionado el botón izquierdo del ratón; luego, arrastre el cursor hasta la columna **SalesTerritoryId** de la tabla **DimSalesTerritory**. Finalmente, suelte el botón.  
  
2.  En la tabla **FactInternetSales**, haga clic en la columna **SalesTerritoryId** y mantenga presionado el botón izquierdo del ratón; luego, arrastre el cursor hasta la columna **SalesTerritoryId** de la tabla **DimSalesTerritory**. Finalmente, suelte el botón.  
  
    Observe que la propiedad Active de esta relación es False, lo que significa que está inactiva. La tabla FactInternetSales ya tiene otra relación activa.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Ocultación de la tabla EmployeeSecurity de las aplicaciones cliente  
En esta tarea, ocultará la tabla EmployeeSecurity, de modo que evitará que aparezca en la lista de campos de la aplicación cliente. Tenga en cuenta que ocultar una tabla no significa protegerla. Los usuarios todavía pueden consultar datos de la tabla EmployeeSecurity, si saben cómo. Para proteger los datos de la tabla EmployeeSecurity evitando que los usuarios puedan consultar sus datos, aplique un filtro en una tarea posterior.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Procedimiento para ocultar la tabla EmployeeSecurity de las aplicaciones cliente  
  
-   En el Diseñador de modelos, en la vista de diagrama, haga clic con el botón derecho en el encabezado de tabla **Empleado** y, luego, haga clic en **Ocultar en herramientas cliente**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Creación de un rol de usuario Empleados de ventas por territorio  
En esta tarea, creará un rol de usuario. Este rol incluye un filtro de fila que define qué filas de la tabla DimSalesTerritory serán visibles para los usuarios. Después, se aplica el filtro en la dirección de la relación de uno a muchos a todas las demás tablas relacionadas con DimSalesTerritory. También aplicará un filtro que protege toda la tabla EmployeeSecurity de las consultas que realice cualquier usuario que sea miembro del rol.  
  
> [!NOTE]  
> El rol Empleados de ventas por territorio que creará en esta lección permitirá a los miembros examinar (o consultar) solo los datos de ventas del territorio de ventas al que pertenecen. Si agrega un usuario como miembro al rol Empleados de ventas por territorio que también existe como un miembro de un rol creado en la [lección 11 sobre cómo creación de roles](../tutorials/aas-lesson-11-create-roles.md), obtendrá una combinación de permisos. Cuando un usuario es miembro de varios roles, los permisos y filtros de fila definidos para cada rol son acumulativos. Es decir, la combinación de roles determina los permisos más elevados que tendrá el usuario.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Procedimiento para crear un rol de usuario Empleados de ventas por territorio  
  
1.  En SSDT, haga clic en el menú **Modelo** y en **Roles**.  
  
2.  En **Administrador de roles**, haga clic en **Nuevo**.  
  
    Se agrega a la lista un nuevo rol con el permiso Ninguno.  
  
3.  Haga clic en el nuevo rol y, luego, en la columna **Nombre**, cambie el nombre del rol a **Empleados de ventas por territorio**.  
  
4.  En la columna **Permisos**, haga clic en la lista desplegable y, luego, seleccione el permiso **Lectura**.  
  
5.  Haga clic en la pestaña **Miembros** y, luego, haga clic en **Agregar**.  
  
6.  En el cuadro de diálogo **Seleccionar usuario o grupo** de la opción para **escribir el nombre del objeto que se seleccionará**, escriba el primer nombre de usuario de ejemplo que utilizó al crear la tabla EmployeeSecurity. Haga clic en **Comprobar nombres** para comprobar que el nombre de usuario es válido y, luego, haga clic en **Aceptar**.  
  
    Repita este paso agregando los demás nombres de usuario de ejemplo que utilizó al crear la tabla EmployeeSecurity.  
  
7.  Haga clic en la pestaña **Filtros de fila**.  
  
8.  Para la tabla **EmployeeSecurity**, en la columna **Filtro DAX**, escriba la siguiente fórmula:  
  
    ```
      =FALSE()  
    ```
  
    Esta fórmula especifica que todas las columnas se resuelven en la condición booleana falso. Los empleados de ventas no pueden consultar ninguna columna de la tabla EmployeeSecurity por el rol de usuario Territorio.  
  
9. Para la tabla **DimSalesTerritory**, escriba la siguiente fórmula:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    En esta fórmula, la función LOOKUPVALUE devuelve todos los valores de la columna DimEmployeeSecurity[SalesTerritoryId], donde el valor de EmployeeSecurity[LoginId] es el mismo que el nombre de usuario de Windows que tiene la sesión iniciada, y el de EmployeeSecurity[SalesTerritoryId] es el mismo que el de DimSalesTerritory[SalesTerritoryId].  
  
    El conjunto de identificadores del territorio de ventas que se devuelven mediante LOOKUPVALUE se usa para restringir las filas que se muestran en la tabla DimSalesTerritory. Solo se muestran las filas donde se encuentra el valor de SalesTerritoryID para la fila del conjunto de identificadores devueltos mediante la función LOOKUPVALUE.  
  
10. En el Administrador de roles, haga clic en **Aceptar**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Prueba del rol de usuario Empleados de ventas por territorio  
En esta tarea, usará la característica Analizar en Excel de SSDT para probar la eficacia del rol de usuario Empleados de ventas por territorio. Especifique uno de los nombres de usuario que agregó a la tabla EmployeeSecurity y como miembro del rol. Este nombre de usuario se utiliza como el nombre de usuario efectivo en la conexión creada entre Excel y el modelo.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Procedimiento para probar el rol de usuario Empleados de ventas por territorio  
  
1.  En SSDT, haga clic en el menú **Modelo** y en **Analizar en Excel**.  
  
2.  En el cuadro de diálogo **Analizar en Excel**, en **Especifique el nombre de usuario o rol que se va a usar al conectarse al modelo**, seleccione **Otro usuario de Windows**. Después, haga clic en **Examinar**.  
  
3.  En el cuadro de diálogo **Seleccionar usuario o grupo**, en **Enter the object name to select** (Escribir el nombre de objeto para seleccionar), escriba uno de los nombres de usuario que incluyó en la tabla EmployeeSecurity. Después, haga clic en **Comprobar nombres**.  
  
4.  Haga clic en **Aceptar** para cerrar el **Seleccionar usuario o grupo** cuadro de diálogo y, a continuación, haga clic en **Aceptar** para cerrar el **analizar en Excel** cuadro de diálogo.  
  
    Excel se abre con un nuevo libro. Automáticamente se crea una tabla dinámica. La lista PivotTable Fields incluye la mayoría de los campos de datos disponibles en el nuevo modelo.  
  
    Observe que la tabla EmployeeSecurity no está visible en la lista de campos de la tabla dinámica. Ocultó esta tabla de las herramientas de cliente en una tarea anterior.  
  
5.  En la lista **Campos** de **Ventas por Internet de ∑** (medidas), seleccione la medida **InternetTotalSales**. La medida se escribe en los campos de **Valores**.  
  
6.  Seleccione la columna **SalesTerritoryId** desde la tabla **DimSalesTerritory**. La columna se escribe en los campos **Etiquetas de fila**.  
  
    Tenga en cuenta que las cifras de ventas por Internet solo aparecen para la región a la que pertenece el nombre de usuario efectivo que usó. Si selecciona otra columna, como City, de la tabla DimGeography como campo de etiqueta de fila, solo se mostrarán las ciudades del territorio de ventas al que pertenece el usuario efectivo.  
  
    Este usuario no puede examinar ni consultar los datos de ventas por Internet de territorios distintos al que pertenece. Esta restricción se debe a que el filtro de fila definido para la tabla DimSalesTerritory, del rol de usuario Empleados de ventas por territorio, protege los datos de toda la información relacionada con otros territorios de ventas.  
  
## <a name="see-also"></a>Otras referencias  
[Función USERNAME (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[Función LOOKUPVALUE (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[Función CUSTOMDATA (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  