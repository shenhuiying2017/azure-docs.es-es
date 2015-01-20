<properties title="How to Create a DocumentDB Account" pageTitle="Crear una cuenta de base de datos | Azure" description="Aprenda a crear una cuenta de base de datos de documentos NoSQL de Base de datos de documentos y elija la configuración de la cuenta en el Portal de vista previa de Azure."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="mimig" />

#Creación de una cuenta de base de datos
Para usar Base de datos de documentos de Microsoft Azure, debe crear una cuenta de Base de datos de documentos.  En este tema se describe cómo crear una cuenta de Base de datos de documentos en el portal de administración de vista previa de Azure.  


1.	Inicie sesión en el [portal de administración de vista previa](https://portal.azure.com/#gallery/Microsoft.DocumentDB).
2.	Haga clic en Nuevo -> Cuenta de Base de datos de documentos.  
	![][1]  

	O bien, en el panel de inicio, puede examinar el Azure Marketplace, seleccionar la categoría "Datos + análisis", elegir **Base de datos de documentos** y, a continuación, haga clic en **Crear**.  

	![][2]   

3. En la hoja **Nueva Base de datos de documentos (vista previa)**, especifique la configuración para la cuenta. 
 
	![][3] 


	- En el cuadro **Id.**, escriba un nombre para identificar la cuenta de Base de datos de documentos. Este valor se convierte en el nombre de host dentro del URI. El id. puede contener solo letras en minúsculas, números y el carácter '-' y debe tener entre 3 y 50 caracteres. Tenga en cuenta que documents.azure.com se anexa al nombre de extremo que elija; su resultado se convertirá en el extremo de la cuenta de Base de datos de documentos.

	- La lente **Nivel de precios** lente está bloqueada porque la vista previa de Base de datos de documentos admite un nivel de precios estándar único. Para obtener más información, vea [Precios de Base de datos de documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- El modo **Configuración opcional** se usa para especificar la capacidad inicial asignada a su cuenta de Base de datos de documentos.  Base de datos de documentos aprovecha las unidades de capacidad para permitirle escalar su cuenta de Base de datos de documentos, donde cada unidad de capacidad incluye almacenamiento y rendimiento de base de datos reservados.  De manera predeterminada, se aprovisiona una unidad de capacidad.  Puede ajustar el número de unidades de capacidad disponibles para su cuenta de Base de datos de documentos en cualquier momento mediante el [portal de administración de vista previa](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Para obtener detalles acerca de la capacidad y el rendimiento de la cuenta de Base de datos de documentos, vea el artículo [Administración de capacidad y rendimiento de Base de datos de documentos][documentdb-manage].

	- En **Grupo de recursos**, seleccione o cree un grupo de recursos para su cuenta de Base de datos de documentos.  De forma predeterminada, se creará un nuevo grupo de recursos.  Puede, sin embargo, optar por seleccionar un grupo de recursos existente al que agregar su cuenta de Base de datos de documentos. Para obtener más información, vea [Uso de los grupos de recursos para administrar los recursos de Azure](http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups/).

	- En **Suscripción**, seleccione la suscripción a Azure que quiere usar para la cuenta de Base de datos de documentos. Si la cuenta tiene solo una suscripción, dicha cuenta se seleccionará automáticamente.*
 
	- Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará su cuenta de Base de datos de documentos.   

3.	Una vez que las nuevas opciones de cuenta de Base de datos de documentos estén configuradas, haga clic en **Crear**.  La creación de la cuenta puede tardar unos minutos.  Para comprobar el estado, puede supervisar el progreso en el panel de inicio.  
	![][4]  
  
	O bien, puede supervisar su progreso en el centro de notificaciones.  

	![][5]  

	![][6]

4.	Después de crear la cuenta de Base de datos de documentos, ya se puede usar con la configuración predeterminada.

	*Tenga en cuenta que la coherencia predeterminada de la cuenta de Base de datos de documentos se establecerá en Sesión.  Puede ajustar la configuración de coherencia predeterminada a través del [portal de administración de vista previa](https://portal.azure.com/#gallery/Microsoft.DocumentDB). *  
	![][7]  

5.	También puede acceder a las cuentas de Base de datos de documentos existentes en la hoja **Examinar**.  
	![][8]

##<a id="NextSteps"></a>Pasos siguientes
Para comenzar con la Base de datos de documentos de Azure, explore los siguientes recursos:

-	[Modelo de recursos y conceptos de la Base de datos de documentos](/documentation/articles/documentdb-resources/)
-	[Interacción con recursos de Base de datos de documentos](/documentation/articles/documentdb-interactions-with-resources/)
-	[Creación de una cuenta de Base de datos de documentos](/documentation/articles/documentdb-create-account/)
-	[Primeros pasos con una cuenta de Base de datos de documentos](/documentation/articles/documentdb-get-started/)

Para obtener más información sobre Base de datos de documentos, vea la documentación correspondiente en [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319)

[Cómo de una cuenta de Base de datos de documentos]: #Howto
[Pasos siguientes]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->
