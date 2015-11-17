<properties
	pageTitle="Creación de un servicio Búsqueda de Azure en el Portal | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Agregue una Búsqueda de Azure gratis o estándar a una suscripción existente mediante el portal de Azure Búsqueda de Azure es un servicio de búsqueda hospedado en la nube para aplicaciones personalizadas."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Creación de un servicio Búsqueda de Azure en el portal de Azure

Búsqueda de Microsoft Azure es un servicio de búsqueda hospedado en la nube que le permite insertar funcionalidad de búsqueda en aplicaciones personalizadas. Proporciona un motor de búsqueda y almacenamiento para sus datos de búsqueda, a los que puede acceder y administrarlos mediante el Portal de Azure, un SDK de .NET o una API de REST. Entre las funciones clave se incluyen consultas de autocompletar, la coincidencia aproximada, la navegación con facetas, el resaltado de resultados, los perfiles de puntuación y la compatibilidad con varios idiomas. Para obtener más información acerca del funcionamiento de Búsqueda de Azure, consulte [Qué es Búsqueda de Azure](seach-what-is-search.md).

## Incorporación de Búsqueda de Azure a una suscripción de forma gratuita

Como administrador, puede agregar Búsqueda de Azure a una suscripción de Azure existente sin costo alguno al seleccionar el servicio compartido (o a una tarifa estándar cuando se opta por recursos específicos).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En la barra de salto, haga clic en **Nuevo** > **Datos + almacenamiento** > **Búsqueda**.

     ![][1]

3. Configure el nombre del servicio, el plan de tarifas, el grupo de recursos, la suscripción y la ubicación. Estos valores son necesarios y no se puede cambiar una vez que se aprovisiona el servicio.

     ![][2]

	- El **Nombre de servicio** debe ser único, estar en minúscula, tener un máximo de 15 caracteres y no incluir espacios. Este nombre se convierte en parte del extremo del servicio Búsqueda de Azure. Consulte [Reglas de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obtener más información acerca de las convenciones de nomenclatura.

	- **Nivel de precios** determina la capacidad y la facturación. Los dos niveles proporcionan las mismas características, pero con niveles de recursos distintos.

		- **Gratuito** se ejecuta en clústeres que se comparten con otros suscriptores. Ofrece suficiente capacidad para probar tutoriales y escribir código de prueba de concepto, pero no está destinado a aplicaciones de producción. Implementar un servicio gratuito suele llevar pocos minutos.
		- **Estándar** se ejecuta en recursos dedicados y es altamente escalable. Inicialmente, se aprovisiona un servicio estándar con una réplica y una partición, pero se puede ajustar la capacidad una vez creado el servicio. Implementar un servicio estándar lleva más tiempo, normalmente unos 15 minutos.

	- Los **grupos de recursos** son contenedores para servicios y recursos que se usan para un objetivo común. Por ejemplo, si va a compilar una aplicación de búsqueda personalizada basada en Búsqueda de Azure, la función Aplicaciones web en Servicio de aplicaciones de Azure y el almacenamiento de blobs de Azure, puede crear un grupo de recursos que mantenga estos servicios juntos en las páginas de administración del portal.

	- **Suscripción** permite elegir entre varias suscripciones, si tiene más de una.

	- **Ubicación** es la región del centro de datos. Actualmente, todos los recursos se deben ejecutar en el mismo centro de datos. No se admite la distribución de recursos entre varios centros de datos.

4. Haga clic en **Crear** para realizar el aprovisionamiento del servicio.

Preste atención a las notificaciones de la barra de salto. Cuando el servicio esté listo para su uso, aparecerá un aviso.

<a id="sub-3"></a>
## Incorporación de un servicio de búsqueda de nivel Estándar para obtener recursos dedicados

Muchos clientes comienzan con el servicio gratuito y después cambian al nivel Estándar para dar cabida a cargas de trabajo mayores. El nivel Estándar ofrece recursos dedicados en un centro de datos de Azure que solo usted puede usar.

Las operaciones de Búsqueda de Azure requieren réplicas de almacenamiento y servicio. A diferencia del servicio gratuito, que no tiene ninguna opción para agregar recursos, el nivel Estándar permite escalar verticalmente para agregar más almacenamiento o soporte de consultas al incrementar el recurso que sea más importante en su escenario.

Para usar el nivel Estándar, debe crear un servicio de búsqueda con ese nivel de precios. Puede repetir los pasos anteriores de este artículo para crear un servicio Búsqueda de Azure. Tenga en cuenta que la configuración de recursos dedicados puede tardar unos minutos (hasta 15 minutos o más).

No hay ninguna actualización de la versión gratuita. Cambiar al modo estándar, con su potencial de escalación, requiere un servicio nuevo. Tendrá que volver a cargar los índices y documentos usados por su aplicación de búsqueda.

El servicio Búsqueda de Azure a nivel estándar se crea con una réplica y una partición, pero se puede escalar fácilmente a niveles de recursos más altos.

1.	Después de que se haya creado el servicio, vuelva al panel del servicio.

2.	Haga clic en el icono **Escalar**.

3.	Use los controles deslizantes para agregar réplicas, particiones o ambos.

Las réplicas y las particiones adicionales se cobran en unidades de búsqueda. El total de unidades de búsqueda necesario para admitir una configuración de recursos en particular se muestra en la página, a medida que agrega recursos.

Puede consultar [Detalles de precios](http://go.microsoft.com/fwlink/p/?LinkID=509792) para obtener la información de facturación por unidad. Consulte [Límites y restricciones](search-limits-quotas-capacity.md) para obtener ayuda para decidir cómo configurar las combinaciones de partición y de réplica.

<a id="sub-2"></a>
## Buscar el nombre del servicio y las claves de API del servicio Búsqueda de Azure

Después de crear el servicio, puede volver al portal de Azure para obtener la dirección URL o la `api-key`. Las conexiones con el servicio de Búsqueda de Azure requieren que tenga la URL y una `api-key` para autenticar la llamada.

1. En la barra de salto, haga clic en **Inicio** y, a continuación, haga clic en el servicio Búsqueda de Azure para abrir el panel del servicio.

2. En el panel del servicio verá mosaicos con información esencial, así como el icono de llave para tener acceso a las claves de administrador.

  	![][3]

3. Copie la dirección URL del servicio y una clave de administración. Los necesitará para la tarea siguiente, [Probar las operaciones de servicio](#sub-4).


<a id="sub-4"></a>
## Prueba de operaciones del servicio

La confirmación de que su servicio está operativo y que puede obtener acceso a él desde una aplicación cliente es el paso final en la configuración de la Búsqueda de Azure. Puede usar cualquiera de los siguientes vínculos para un enfoque sin código para comprobar la disponibilidad del servicio.

- [Cómo usar Chrome Postman con Búsqueda de Azure](search-chrome-postman.md)
- [Cómo usar Telerik Fiddler con Búsqueda de Azure](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
## Pasos siguientes

En la siguiente información adicional, se explica cómo compilar y administrar las aplicaciones de búsqueda que usan Búsqueda de Azure.

- [Cómo usar la Búsqueda de Azure en .NET](search-howto-dotnet-sdk.md)

- [Administración de la solución de búsqueda en Microsoft Azure](search-manage.md)

- [Búsqueda de Azure en MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Vídeo del canal 9: Introducción a Búsqueda de Azure](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=Nov15_HO3-->