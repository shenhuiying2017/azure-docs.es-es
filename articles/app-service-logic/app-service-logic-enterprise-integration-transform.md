<properties 
	pageTitle="Información general sobre Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Utilice las características de Enterprise Integration Pack para posibilitar escenarios de integración y proceso empresariales mediante el Servicio de aplicaciones de Microsoft Azure." 
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

# Integración de empresas con transformaciones XML

## Información general
El conector de transformación de integración de empresas convierte los datos de un formato a otro. Por ejemplo, puede darse el caso de que tenga un mensaje entrante en el que la fecha actual tenga el formato añoMesDía. Puede utilizar una transformación para cambiar el formato de la fecha a mesDíaAño.

## ¿Para qué sirve una transformación?
Una Transformación, que se conoce también como asignación, está formada por un esquema XML de origen (la entrada) y un esquema XML de destino (la salida). Puede utilizar las diferentes funciones integradas para administrar o controlar los datos, incluidos aspectos como las manipulaciones de cadenas, las asignaciones condicionales, las expresiones aritméticas, los formateadores de tiempo y fecha e, incluso, las construcciones en bucle.

## ¿Cómo se crea una transformación?
Puede crear una asignación o una transformación mediante el [SDK de integración de empresas](https://aka.ms/vsmapsandschemas) de Visual Studio. Cuando haya terminado de crear y probar la transformación, cargue la transformación en la cuenta de integración.

## Procedimiento para utilizar una transformación
Cuando cargue la transformación en la cuenta de integración, podrá emplearla para crear una Aplicación lógica. Esta ejecutará las transformaciones siempre que se desencadena la Aplicación lógica (y que haya contenido de entrada que deba transformarse).

**Estos son los pasos para utilizar una transformación**:

### Requisitos previos 
En la versión preliminar, tendrá que llevar a cabo estos pasos:

-  [Crear un contenedor de Funciones de Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Creación de un contenedor de Funciones de Azure").
-  [Agregar una función al contenedor de Funciones de Azure.](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Esta plantilla crea una función de Azure de C# basada en webhooks con funcionalidades de transformación que se utilizarán en escenarios de integración de Aplicaciones lógicas.")
-  Crear una cuenta de integración y agregarle una asignación.

>[AZURE.TIP] Anote el nombre del contenedor de Funciones de Azure y el de la función de Azure, ya que los necesitará en el paso siguiente.

Ahora que ha tenido en cuenta los requisitos previos, tendrá que crear la Aplicación lógica:

1. Cree una aplicación lógica y [vincúlela a la cuenta de integración](./app-service-logic-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una Aplicación lógica.") que contenga la asignación.
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica.![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)
3. Agregue la acción **Transform XML** (Transformar XML) seleccionando primero **Agregar una acción**.![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)
4. Escriba la palabra *transform* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar.![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)
5. Seleccione la acción **Transform XML** (Transformar XML).![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)
6. Seleccione el **CONTENEDOR DE FUNCIONES** que incluya la función que vaya a utilizar. Este es el nombre del contenedor de Funciones de Azure que creó anteriormente en estos pasos.
7. Seleccione la **FUNCIÓN** que quiera utilizar. Este es el nombre de la Función de Azure que creó anteriormente.
8. Agregue el **CONTENIDO** XML que vaya a transformar. Tenga en cuenta que puede usar cualquier dato XML que reciba en la solicitud HTTP como valor de **CONTENIDO**. En este ejemplo, seleccione el cuerpo de la solicitud HTTP que desencadenó la Aplicación lógica.
9. Seleccione el nombre de la **ASIGNACIÓN** que quiera usar para realizar la transformación. La asignación ya debe estar en la cuenta de integración. En el paso anterior, ya concedió a la Aplicación lógica acceso a la cuenta de integración que contiene la asignación.
10. Guarde el trabajo. ![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png)

En este momento, ya ha terminado de configurar su asignación. En una aplicación real, puede almacenar los datos transformados en una aplicación LOB como SalesForce. Puede agregar fácilmente una acción para enviar el resultado de la transformación a Salesforce.

Ahora puede probar la transformación realizando una solicitud al punto de conexión HTTP.

## Características y casos de uso

- La transformación creada en una asignación puede ser simple, como copiar un nombre y una dirección de un documento a otro. O bien puede crear transformaciones más complejas mediante las operaciones de asignación integradas.
- Hay varias operaciones de asignación o funciones a las que se puede acceder fácilmente, por ejemplo, cadenas, funciones de fecha hora, etc.
- Puede realizar una copia de datos directa entre los esquemas. En el Asignador que incluye el SDK, es tan sencillo como dibujar una línea que conecte los elementos del esquema de origen con sus equivalentes en el de destino.
- Al crear una asignación, verá una representación gráfica de esta, que muestra todos los vínculos y relaciones que cree.
- Utilice la característica Comprobar asignación para agregar un mensaje XML de ejemplo. Con un solo clic, puede probar la asignación creada y ver el resultado generado.
- Cargue asignaciones que ya existan.
- Es compatible con el formato XML.


## Más información
- [Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")
- [Más información sobre las asignaciones](./app-service-logic-enterprise-integration-maps.md "Información sobre las asignaciones de integración de empresas")
 

<!---HONumber=AcomDC_0713_2016-->