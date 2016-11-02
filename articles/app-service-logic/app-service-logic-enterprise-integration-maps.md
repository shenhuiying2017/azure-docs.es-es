<properties 
	pageTitle="Información general sobre las asignaciones y Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Sepa cómo usar las asignaciones con las Aplicaciones lógicas y Enterprise Integration Pack." 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Información sobre las asignaciones y Enterprise Integration Pack

## Información general
Enterprise Integration Pack utiliza las asignaciones para transformar datos XML de un formato a otro.

## ¿Qué son las asignaciones?
Una asignación es un documento XML que define qué datos de un documento deben transformarse en otro formato.

## ¿Por qué se utilizan las asignaciones?
Supongamos que recibe periódicamente pedidos o facturas B2B de clientes que usen el formato de fecha AAAMMDD. Sin embargo, en su organización, las fechas se almacenan en el formato MMDDAAA. Puede usar una asignación para *transformar* el formato de fecha AAAMMDD en MMDDAAA antes de almacenar los detalles de los pedidos o las facturas en la base de datos de actividades de clientes.

## ¿Cómo se crea una asignación?
[Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") for Visual Studio 2015 permite crear proyectos de integración de Biztalk. Al crear un archivo de asignación de integración, podrá asignar visualmente elementos entre dos archivos de esquema XML. Después de compilar este proyecto, se genera un documento XSLT.

## ¿Cómo se cargan asignaciones?
En el Portal de Azure:
1. Seleccione **Examinar**. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Seleccione la **cuenta integración** en la que vaya a agregar la asignación. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Seleccione el icono de **Asignaciones** ![](./media/app-service-logic-enterprise-integration-maps/map-1.png).
5. Haga clic en el botón **Agregar** de la hoja Asignaciones que se abre. ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. Escriba un **nombre** para la asignación. Después, cargue el archivo de asignación y seleccione el icono de carpeta en el lado derecho del cuadro de texto **Asignación**. Cuando termine el proceso de carga, haga clic en el botón **Aceptar**. ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. Ahora, la asignación se agrega a la cuenta de integración. Recibirá una notificación en la pantalla que indica que el proceso de agregar el archivo de asignación se ha realizado correctamente o con errores. Cuando reciba la notificación, seleccione el icono de **Asignaciones** y verá la asignación que acaba de agregar en la hoja Asignaciones: ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## ¿Cómo se editan asignaciones?
Para editar una asignación, debe cargar un nuevo archivo de asignación con los cambios que desee. En primer lugar, puede descargar la asignación y editarla.

Siga estos pasos para cargar una nueva asignación que reemplace una que ya exista:
1. Seleccione el icono de **Asignaciones**.
2. Elija la asignación que quiera editar cuando se abra la hoja Asignaciones.
3. En la hoja **Asignaciones**, haga clic en el vínculo **Actualizar**. ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. Seleccione el archivo de asignación que quiera cargar mediante el cuadro de diálogo selector de archivos que se abre. Después, haga clic en la opción **Abrir** del selector de archivos ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. Recibirá una notificación emergente cuando se cargue la asignación.

## ¿Cómo se eliminan asignaciones?
1. Seleccione el icono de **Asignaciones**.
2. Elija la asignación que quiera eliminar cuando se abra la hoja Asignaciones.
3. Haga clic en el vínculo **Eliminar**. ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. Confirme que quiere eliminar realmente la asignación. ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)

## Pasos siguientes
- [Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")
- [Más información sobre los contratos](./app-service-logic-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")
- [Más información sobre las transformaciones](./app-service-logic-enterprise-integration-transform.md "Información sobre las transformaciones de integración de empresas")

<!---HONumber=AcomDC_0810_2016-->