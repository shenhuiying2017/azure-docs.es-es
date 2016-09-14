<properties 
	pageTitle="P+F de Administración de API de Azure | Microsoft Azure" 
	description="Conozca las respuestas a preguntas comunes, patrones y prácticas recomendadas para Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="miaojiang" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="mijiang"/>

# P+F de Administración de API de Azure

Conozca las respuestas a preguntas comunes, patrones y prácticas recomendadas para Administración de API de Azure.

## Preguntas más frecuentes

-	[¿Cómo puedo formular una pregunta al equipo de Administración de API?](#how-can-i-ask-a-question-to-the-api-management-team)
-	[¿Qué significa que una característica se encuentra en su versión preliminar?](#what-does-it-mean-if-a-feature-is-in-preview)
-	[¿Cuáles son las opciones admitidas para proteger la conexión entre la puerta de enlace de Administración de API y mis servicios de back-end?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[¿Cómo se puede copiar una instancia de Administración de API en una nueva instancia?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[¿Es posible administrar mi instancia de Administración de API mediante programación?](#can-i-manage-my-api-management-instance-programmatically)
-	[¿Cómo se puede agregar un usuario al grupo de administradores?](#how-can-i-add-a-user-to-the-administrators-group)
-	[¿Por qué la directiva que deseo agregar no está habilitada en el editor de directivas?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)
-	[¿Cómo se puede conseguir el control de versiones de API con Administración de API?](#how-can-i-achieve-api-versioning-with-api-management)
-	[¿Cómo se pueden configurar varios entornos de API, como Sandbox y Production?](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
-	[¿Se admite SOAP en Administración de API?](#is-soap-supported-in-api-management)
-	[¿Es constante la dirección IP de la puerta de enlace de Administración de API? ¿Puedo usarla en las reglas de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-	[¿Se puede configurar un servidor de autorización de OAUth 2.0 con seguridad ADFS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-	[¿Qué método de enrutamiento utiliza Administración de API cuando se implementa en varias ubicaciones geográficas?](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)
-	[¿Puedo crear una instancia del servicio de Administración de API mediante una plantilla ARM?](#can-i-create-an-api-management-service-instance-using-an-arm-template)
-	[¿Puedo usar un certificado SSL autofirmado para un back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-backend)
-	[¿Por qué recibo el error de autenticación al intentar clonar el repositorio GIT?](#why-am-i-getting-authentication-failure-when-i-try-to-clone-the-git-repository)
-	[¿Funciona la administración de API con ExpressRoute?](#does-api-management-work-with-express-route)


### ¿Cómo puedo formular una pregunta al equipo de Administración de API?

-	Puede publicar sus preguntas en nuestro [foro de MSDN de Administración de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	También nos puede enviar un correo electrónico a la dirección: `apimgmt@microsoft.com`.
-	Puede enviarnos una [solicitud de característica](https://feedback.azure.com/forums/248703-api-management).

### ¿Qué significa que una característica se encuentra en su versión preliminar?

Una característica en versión preliminar es completa funcionalmente, pero se encuentra en ese estado porque buscamos activamente comentarios sobre ella. Es posible que hagamos cambios importantes en respuesta a los comentarios de los clientes, por lo que recomendamos no depender de la característica en entornos de producción. Si tiene cualquier comentario sobre las características de la versión preliminar, háganoslo llegar mediante uno de los mecanismos descritos en [¿Cómo puedo formular una pregunta al equipo de Administración de API?](#how-can-i-ask-a-question-to-the-api-management-team)

### ¿Cuáles son las opciones admitidas para proteger la conexión entre la puerta de enlace de Administración de API y mis servicios de back-end?

Hay varias opciones admitidas.

1. Use la autenticación básica HTTP. Para más información, consulte [Definición de la configuración de la API](api-management-howto-create-apis.md#configure-api-settings).
2. Use la autenticación mutua de SSL como se describe en [Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Administración de API de Azure](api-management-howto-mutual-certificates.md).
3. Utilice la lista blanca IP en su servicio back-end. Si tiene una instancia de Administración de API de nivel Estándar o Premium, la dirección IP de la puerta de enlace permanece constante y puede configurar la lista blanca para permitir esta dirección IP. Puede recuperar la dirección IP de la instancia de Administración de API en el **Panel** del Portal de Azure clásico.
4. Puede conectar la instancia de Administración de API a una Red virtual de Azure (clásica). Para más información, consulte [Configuración de conexiones VPN en Administración de API de Azure](api-management-howto-setup-vpn.md).

### ¿Cómo se puede copiar una instancia de Administración de API en una nueva instancia?

Puede utilizar diferentes opciones para copiar una instancia del servicio de Administración de API a una nueva instancia.

-	Utilice la función de copia de seguridad y restauración de Administración de API. Para más información, consulte [Procedimiento para implementar la recuperación ante desastres mediante copias de seguridad y restauración del servicio en Administración de API de Azure](api-management-howto-disaster-recovery-backup-restore.md).
-	Cree su propia característica de copia de seguridad y restauración mediante la [API de REST de Administración de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) para guardar y restaurar las entidades deseadas desde la instancia de servicio.
-	Descargue la configuración del servicio mediante Git y cárguela a una nueva instancia. Para más información, consulte [Guardado y configuración del servicio Administración de API mediante Git](api-management-configuration-repository-git.md).

### ¿Es posible administrar mi instancia de Administración de API mediante programación?

Sí, se puede administrar mediante la [API de REST de Administración de API](https://msdn.microsoft.com/library/azure/dn776326.aspx), el [SDK de la biblioteca de administración de servicios de Administración de API de Microsoft Azure](http://aka.ms/apimsdk) y los cmdlets de PowerShell de [implementación de servicio](https://msdn.microsoft.com/library/mt619282.aspx) y [administración de servicio](https://msdn.microsoft.com/library/mt613507.aspx).

### ¿Cómo se puede agregar un usuario al grupo de administradores?

Se consigue siguiendo estos pasos:

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).
2. Vaya al grupo de recursos que contiene la instancia deseada de Administración de API.
3. Agregue el usuario deseado al rol Colaborador de Administración de API.

Una vez hecho esto, el colaborador recién agregado puede usar [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx) de Azure PowerShell para iniciar sesión como administrador:

1. Use el cmdlet `Login-AzureRmAccount` para iniciar sesión.
2. Establezca el contexto en la suscripción que contiene el servicio mediante `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obtenga el token de SSO mediante `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Copie y pegue la dirección URL en el explorador; el usuario debería tener acceso administrativo al portal.


### ¿Por qué la directiva que deseo agregar no está habilitada en el editor de directivas?

Si la directiva que desea agregar no está habilitada, asegúrese de que se encuentra en el ámbito correcto para esa directiva. Cada instrucción de la directiva está diseñada para su uso en determinados ámbitos y secciones de la directiva. Para revisar las secciones y los ámbitos de una directiva, compruebe la sección **Uso** de esa directiva en la [Referencia de directivas](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### ¿Cómo se puede conseguir el control de versiones de API con Administración de API?

-	Puede configurar distintas API en Administración de API que representen distintas versiones. Por ejemplo, puede tener `MyAPI v1` y `MyAPI v2` como dos API diferentes y los desarrolladores pueden elegir la versión que quieran usar.
-	También puede configurar su API con una dirección URL del servicio que no incluya un segmento de versión; por ejemplo: `https://my.api`. A continuación, puede configurar un segmento de versión en la plantilla [URL de reescritura](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) de cada operación; por ejemplo puede tener una operación con una [plantilla de URL](api-management-howto-add-operations.md#url-template) de `/resource` y una plantilla [URL de reescritura](api-management-howto-add-operations.md#rewrite-url-template) de `/v1/Resource`. De esta forma, podrá cambiar el valor del segmento de versión en cada operación por separado.
-	Si desea mantener un segmento de versión "predeterminado" en la dirección URL del servicio de la API, puede configurar una directiva que use la directiva [Set backend service](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) (Establecer servicio de back-end) en operaciones seleccionadas para cambiar la ruta de acceso de la solicitud de back-end.

### ¿Cómo se pueden configurar varios entornos de API, como Sandbox y Production?

En este momento, las opciones son:

-	Puede hospedar distintas API en el mismo inquilino
-	Puede hospedar las mismas API en diferentes inquilinos

### ¿Se admite SOAP en Administración de API?

Actualmente, ofrecemos compatibilidad limitada para SOAP dentro de Administración de API de Azure; es una característica que se está investigando actualmente. Estamos muy interesados en obtener cualquier ejemplo de WSDL de sus clientes y una descripción de las características que necesitan, ya que esto nos facilitaría el proceso. Póngase en contacto con nosotros mediante la información de contacto que se menciona en [¿Cómo puedo formular una pregunta al equipo de Administración de API?](#how-can-i-ask-a-question-to-the-api-management-team)

Si necesita ponerse ya en marcha, miembros de la comunidad han sugerido soluciones temporales (consulte [Azure API Management - APIM, consuming a SOAP WCF service over HTTP](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html) (Administración de API de Azure: APIM, consumo de un servicio WCF de SOAP a través de HTTP)).

La implementación de la solución de este modo requiere algunos procesos de configuración manual de las directivas; no admite la importación y exportación de WSDL; y los usuarios deben formar parte del cuerpo de las solicitudes realizadas con la consola de prueba en el portal para desarrolladores.

### ¿Es constante la dirección IP de la puerta de enlace de Administración de API? ¿Puedo usarla en las reglas de firewall?

En los niveles Estándar y Premium, la dirección IP pública (VIP) del inquilino de Administración de API es estática durante la vigencia del inquilino, con varias excepciones que se enumeran a continuación. Tenga en cuenta que a los inquilinos de nivel Premium configurados para la implementación en varias regiones se les asigna una dirección IP pública por región.

La dirección IP cambiará en las siguientes circunstancias:

-	El servicio se elimina y se vuelve a crear.
-	La suscripción al servicio se suspende (por ejemplo, por falta de pago) y luego se reinstaura.
-	Se agrega o se quita una red virtual (la red virtual solo se admite en el nivel Premium).
-	Se producen cambios en la dirección regional si se vacía la información de la región y luego se vuelve a escribir (la implementación en varias regiones solo se admite en el nivel Premium).

La dirección IP (o las direcciones en el caso de la implementación en varias regiones) se puede encontrar en la página del inquilino en el Portal de Azure clásico.

### ¿Se puede configurar un servidor de autorización de OAUth 2.0 con seguridad ADFS?

Para más información sobre la configuración de este escenario, consulte [Using ADFS in API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/) (Uso de ADFS en Administración de API).

### ¿Qué método de enrutamiento utiliza Administración de API cuando se implementa en varias ubicaciones geográficas? 

Administración de API usa el [método de enrutamiento de tráfico de rendimiento](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method). El tráfico entrante se enrutará a la puerta de enlace de API más cercana. Si una región se queda sin conexión, el tráfico entrante se enruta automáticamente a la siguiente puerta de enlace más cercana. Para más información sobre los métodos de enrutamiento, consulte [Métodos de enrutamiento del Administrador de tráfico](../traffic-manager/traffic-manager-routing-methods.md).

### ¿Puedo crear una instancia del servicio de Administración de API mediante una plantilla ARM?

Sí, consulte las plantillas de inicio rápido en [Azure API Management Service](http://aka.ms/apimtemplate) (Servicio de Administración de API de Azure).

### ¿Puedo usar un certificado SSL autofirmado para un back-end?

Sí. Siga estos pasos:

1. Cree una entidad de [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) mediante la API de administración.
2. Establecimiento de la propiedad skipCertificateChainValidation en true.
3. Cuando ya no desea permitir el certificado autofirmado, puede eliminar la entidad de back-end o establecer la propiedad skipCertificateChainValidation en false.

### ¿Por qué recibo el error de autenticación al intentar clonar el repositorio GIT? 

Si está utilizando el Administrador de credenciales de GIT o intenta clonar el repositorio mediante Visual Studio, puede que se tope con un problema conocido con el cuadro de diálogo de credenciales de Windows que limita la longitud de la contraseña a solo 127 caracteres y, por tanto, trunca la contraseña que nosotros generamos. Estamos trabajando para acortar la contraseña. Por ahora, use GIT Bash para la clonación.

### ¿Funciona la administración de API con ExpressRoute?

Sí.

<!---HONumber=AcomDC_0831_2016-->