<properties 
	pageTitle="P+F de Administración de API de Azure | Microsoft Azure" 
	description="Conozca las respuestas a preguntas comunes, patrones y prácticas recomendadas para Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="sdanie"/>

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

### ¿Cómo puedo formular una pregunta al equipo de Administración de API?

-	Puede publicar sus preguntas en nuestro [foro de MSDN de Administración de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	También nos puede enviar un correo electrónico a la dirección `apimgmt@microsoft.com`.

### ¿Qué significa que una característica se encuentra en su versión preliminar?

Una característica en versión preliminar es completa funcionalmente, pero se encuentra en ese estado porque buscamos activamente comentarios sobre ella. Es posible que hagamos cambios importantes en respuesta a los comentarios de los clientes, por lo que recomendamos no depender de la característica en entornos de producción. Si tiene cualquier comentario sobre las funciones en versión preliminar, infórmenos mediante uno de los mecanismos descritos en [¿Cómo puedo formular una pregunta al equipo de Administración de API?](#how-can-i-ask-a-question-to-the-api-management-team)

### ¿Cuáles son las opciones admitidas para proteger la conexión entre la puerta de enlace de Administración de API y mis servicios de back-end?

Hay varias opciones admitidas.

1. Use la autenticación básica HTTP. Para obtener más información, consulte [Definición de la configuración de la API](api-management-howto-create-apis.md#configure-api-settings).
2. Use la autenticación mutua de SSL como se describe en [Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Administración de API de Azure](api-management-howto-mutual-certificates.md).
3. Utilice la lista blanca IP en su servicio back-end. Si tiene una instancia de Administración de API de nivel Estándar o Premium, la dirección IP de la puerta de enlace permanece constante y puede configurar la lista blanca para permitir esta dirección IP. Puede recuperar la dirección IP de la instancia de Administración de API en el **Panel** en el Portal de Azure clásico.
4. Puede conectar la instancia de Administración de API a una Red virtual de Azure (clásica). Para obtener más información, consulte [Configuración de conexiones VPN en Administración de API de Azure](api-management-howto-setup-vpn.md).

### ¿Cómo se puede copiar una instancia de Administración de API en una nueva instancia?

Puede utilizar diferentes opciones para copiar una instancia del servicio de Administración de API a una nueva instancia.

-	Utilice la función de copia de seguridad y restauración de Administración de API. Para obtener más información, consulte [Procedimiento para implementar la recuperación ante desastres mediante copias de seguridad y restauración del servicio en Administración de API de Azure](api-management-howto-disaster-recovery-backup-restore.md).
-	Cree su propia característica de copia de seguridad y restauración mediante la [API de REST de Administración de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) para guardar y restaurar las entidades deseadas desde la instancia de servicio.
-	Descargue la configuración del servicio mediante Git y cárguela a una nueva instancia. Para obtener más información, consulte [Guardado y configuración del servicio Administración de API mediante Git](api-management-configuration-repository-git.md).

### ¿Es posible administrar mi instancia de Administración de API mediante programación?

Sí, puede administrarla mediante la [API de REST de Administración de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) y los cmdlets de PowerShell [Implementación de servicio](https://msdn.microsoft.com/library/mt619282.aspx) y [Administración de servicio](https://msdn.microsoft.com/library/mt613507.aspx).

### ¿Cómo se puede agregar un usuario al grupo de administradores?

En este momento, solo pueden ser administradores los usuarios que inician sesión a través del Portal de Azure clásico como administradores o coadministradores de la suscripción de Azure que contiene la instancia de Administración de API. Los usuarios creados en el portal para editores no pueden designarse administradores ni agregarse al grupo de administradores.


### ¿Por qué la directiva que deseo agregar no está habilitada en el editor de directivas?

Si la directiva que desea agregar no está habilitada, asegúrese de que se encuentra en el ámbito correcto para esa directiva. Cada instrucción de la directiva está diseñada para su uso en determinados ámbitos y secciones de la directiva. Para revisar las secciones y los ámbitos de una directiva, compruebe la sección **Uso** de esa directiva en la [Referencia de directivas](https://msdn.microsoft.com/library/azure/dn894080.aspx).

<!---HONumber=AcomDC_0413_2016-->