<properties 
	pageTitle="Directivas de Administración de API de Azure" 
	description="Obtenga información acerca de cómo crear, editar y configurar directivas en Administración de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="sdanie"/>


#Directivas de Administración de API de Azure

En Administración de API de Azure, las directivas constituyen una eficaz funcionalidad del sistema que permite al editor cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Entre las declaraciones más usadas se encuentran la conversión de formato de XML a JSON y la limitación de tasa de llamadas para restringir la cantidad de llamadas entrantes de un desarrollador. Hay muchas más directivas disponibles y listas para usar.

Consulte la [Referencia de directivas][] para obtener una lista de declaraciones de directiva con su configuración.

Las directivas se aplican en el proxy que se encuentra entre el consumidor de la API y la API administrada. El proxy recibe todas las solicitudes y normalmente las reenvía sin modificar a la API subyacente. Sin embargo, una directiva puede aplicar cambios a la solicitud de entrada y a la respuesta de salida.

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control][] y [Establecer variable][] se basan en expresiones de directiva. Para obtener más información, consulte [Directivas avanzadas][] y [Expresiones de directiva][].

## <a name="scopes"> </a>Configuración de directivas
Las directivas se pueden configurar globalmente o en el ámbito de un [producto][], una [API][] o una [operación][]. Para configurar una directiva, vaya al editor de directivas del portal de publicadores.

![Policies menu][policies-menu]

El editor de directivas consta de tres secciones principales: el ámbito de la directiva (superior), la definición de la directiva donde se editan las directivas (izquierda) y la lista de instrucciones (derecha):

![Policies editor][policies-editor]

Para comenzar a configurar una directiva, se debe seleccionar en primer lugar el ámbito en el que se debe aplicar. En la captura de pantalla siguiente está seleccionado el producto Starter. Tenga en cuenta que el símbolo de recuadro junto al nombre de la directiva indica que ya se aplica una directiva en este nivel.

![Scope][policies-scope]

Puesto que ya se ha aplicado una directiva, la configuración se muestra en la vista de definición.

![Configuración][policies-configure]

La directiva aparece como de solo lectura al principio. Para editar la definición, haga clic en la acción **Configurar directiva**.

![Edit][policies-edit]

La definición de la directiva es un documento XML simple que describe una secuencia de declaraciones de entrada y de salida. El XML se puede editar directamente en la ventana de definición. Se ofrece una lista de declaraciones a la derecha y las declaraciones aplicables al ámbito actual están habilitadas y resaltadas; como demuestra la declaración Límite de tasa de llamadas de la captura de pantalla anterior.

Al hacer clic en una declaración habilitada se agregará el XML correspondiente en la ubicación del cursor en la vista de definición.

Hay una lista completa de instrucciones de directivas y su configuración disponible en la [Referencia de directivas][].

Por ejemplo, para agregar una nueva declaración con objeto de restringir las solicitudes de entrada a las direcciones IP especificadas, sitúe el cursor exactamente dentro del contenido del elemento XLM "inbound" y haga clic en la declaración Restringir IP del autor de llamada.

![Restriction policies][policies-restrict]

Así, se agregará un fragmento de código XML en el elemento "inbound" que proporciona orientación sobre cómo configurar la declaración.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

Para limitar las solicitudes de entrada y aceptar solo las procedentes de una dirección IP de 1.2.3.4, modifique el XML de la manera siguiente:

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Save][policies-save]

Cuando complete la configuración de las declaraciones de la directiva, haga clic en Guardar; los cambios se propagarán inmediatamente al proxy de Administración de API.

##<a name="sections"> </a>Descripción de la configuración de directivas

Una directiva es una serie de declaraciones que se ejecutan en orden para una solicitud y una respuesta. La configuración se divide adecuadamente en un elemento de entrada (solicitud) y un elemento de salida (directiva), tal como se muestra en la configuración.

	<policies>
		<inbound>
			<!-- statements to be applied to the request go here -->
		</inbound>
		<outbound>
			<!-- statements to be applied to the response go here -->
		</outbound>
	</policies>

Dado que las directivas se pueden especificar en diferentes niveles (global, por producto, por API y por operación), la configuración ofrece una manera de que se especifique el orden en el que se ejecutan las declaraciones de esta definición con respecto a la directiva principal.

Por ejemplo, si tiene una directiva de nivel global y una directiva configurada para una API, siempre que se use esa directiva en particular, se aplicarán ambas directivas. Administración de API tiene en cuenta el orden determinista de declaraciones de directiva combinadas mediante el elemento base.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

En la definición de directiva del ejemplo anterior, la declaración entre dominios se ejecutaría antes de las directivas superiores que, a su vez, irían seguidas de la directiva de búsqueda y reemplazo.

Nota: una directiva global no tiene una directiva principal y el uso del elemento `<base>` en ella no surte efecto.

## Pasos siguientes

Visualice el siguiente vídeo acerca de expresiones de directivas.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Referencia de directivas]: api-management-policy-reference.md
[producto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis
[operación]: api-management-howto-add-operations.md

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Establecer variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!---HONumber=July15_HO4-->