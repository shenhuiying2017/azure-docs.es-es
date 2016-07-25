<properties 
	pageTitle="Información general sobre los esquemas y Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Sepa cómo usar los esquemas con las Aplicaciones lógicas y Enterprise Integration Pack." 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Información sobre los esquemas y Enterprise Integration Pack  

## ¿Por qué se utilizan los esquemas?
Utilice los esquemas para confirmar que los documentos XML recibidos son válidos; es decir, que contienen los datos esperados en un formato predefinido.

## ¿Cómo se agregan esquemas?
En el Portal de Azure:
1. Seleccione **Examinar**. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Seleccione la **cuenta integración** en la que vaya a agregar el esquema. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Seleccione el icono de **Esquemas**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. Haga clic en el botón **Agregar** de la hoja Esquemas que se abre. ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. Escriba un **nombre** para el esquema. Después, cargue el archivo de esquema y seleccione el icono de carpeta en el lado derecho del cuadro de texto **Esquema**. Cuando termine el proceso de carga, haga clic en el botón **Aceptar**. ![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. Seleccione el icono de notificación de *campana* para ver el progreso del proceso de carga de esquemas. ![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. Seleccione el icono de **Esquemas**. Con esto, se actualiza el icono y debería ver que el número de esquemas aumenta, lo que significa que se ha agregado correctamente el nuevo esquema. Cuando haya seleccionado el icono de **Esquemas**, también verá que el partner que acaba de agregar aparece en la hoja Esquemas, a la derecha. ![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## ¿Cómo se utilizan esquemas?
- Los esquemas se utilizan para validar los mensajes que se intercambian en un escenario B2B.

## ¿Cómo se editan esquemas?
1. Seleccione el icono de **Esquemas**.
2. Elija el esquema que quiera modificar en la hoja Esquemas que se abre.
3. Haga clic en el vínculo **Cargar** de la hoja Esquemas. ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. Seleccione el archivo de esquema que quiera cargar mediante el cuadro de diálogo selector de archivos que se abre.
5. Haga clic en la opción **Abrir** del selector de archivos. ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. Recibirá una notificación que indica que la carga se ha realizado correctamente. ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## ¿Cómo se eliminan esquemas?
1. Seleccione el icono de **Esquemas**.
2. Elija el esquema que quiera eliminar en la hoja Esquemas que se abre.
3. Haga clic en el vínculo **Eliminar** de la barra de menús de la hoja Esquemas. ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. Si realmente quiere eliminar el esquema seleccionado, seleccione **Sí** en el cuadro de diálogo Eliminar esquema para confirmar la acción. ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. Finalmente, observe que la lista de esquemas de la hoja Esquemas se actualiza y que el esquema que ha eliminado ya no aparece. ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## Pasos siguientes

- [Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

      

<!---HONumber=AcomDC_0713_2016-->