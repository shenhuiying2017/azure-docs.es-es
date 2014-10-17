<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Cómo asegurar servicios back-end con la autenticación mutua de certificados en Administración de API de Azure

Administración de API permite acceder de forma segura al servicio back-end de una API con certificados mutuos. Esta guía muestra cómo administrar certificados en la consola de Administración de API y cómo configurar una API para acceder al servicio back-end correspondiente con un certificado.

> Para obtener más información sobre cómo administrar certificados con la API de REST de Administración de API, consulte este artículo sobre la [entidad de certificados de la API de REST de Administración de API de Azure][entidad de certificados de la API de REST de Administración de API de Azure].

## En este tema

-   [Requisitos previos][Requisitos previos]
-   [Cargar un certificado de cliente][Cargar un certificado de cliente]
-   [Eliminar un certificado de cliente][Eliminar un certificado de cliente]
-   [Configurar una API para realizar la autenticación proxy con un certificado mutuo][Configurar una API para realizar la autenticación proxy con un certificado mutuo]

## <a name="prerequisites"> </a>Requisitos previos

Esta guía muestra cómo configurar la instancia de servicio de Administración de API para acceder al servicio back-end de una API con la autenticación mutua de certificados. Antes de realizar los pasos de este tema, configure el servicio back-end para la autenticación mutua de certificados y asegúrese de disponer de acceso al certificado y a la contraseña correspondiente para realizar la carga en la consola de Administración de API.

## <a name="step1"> </a>Cargar un certificado de cliente

Para comenzar, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

![API Management console][API Management console]

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

Haga clic en **Seguridad** en el menú **Administración de API** de la izquierda y en **Certificados de cliente**.

![Certificados de cliente][Certificados de cliente]

Para cargar un certificado nuevo, haga clic en **Cargar certificado**.

![Cargar certificado][Cargar certificado]

Examine el certificado y escriba la contraseña correspondiente.

> El certificado debe estar en formato **.pfx**. Se admiten los certificados autofirmados.

![Cargar certificado][1]

Haga clic en **Cargar** para cargar el certificado.

> En ese momento se validará la contraseña del certificado. Si es incorrecta, aparecerá un mensaje de error.

![Certificado cargado][Certificado cargado]

Cuando el certificado se carga, aparece en la pestaña **Certificados de cliente**. Si cuenta con varios certificados, anote el asunto o los cuatro últimos caracteres de la huella digital con los que se selecciona el certificado al configurar una API para usar certificados (conforme a la sección [Configurar una API para realizar la autenticación proxy con un certificado mutuo][Configurar una API para realizar la autenticación proxy con un certificado mutuo] que aparece más abajo).

## <a name="step1a"> </a>Eliminar un certificado de cliente

Para eliminar un certificado, haga clic en **Eliminar** junto a este.

![Eliminar certificado][Eliminar certificado]

Haga clic en **Sí, eliminar** para confirmar.

![Confirmar eliminación][Confirmar eliminación]

Si alguna API está usando el certificado, aparecerá una pantalla de advertencia. Para eliminar el certificado, primero quítelo de todas las API que se hayan configurado para usarlo.

![Confirmar eliminación][2]

## <a name="step2"> </a>Configurar una API para realizar la autenticación proxy con un certificado mutuo

Haga clic en **API** en el menú **Administración de API** de la izquierda, en el nombre de la API en cuestión y en la pestaña **Seguridad**.

![Seguridad de API][Seguridad de API]

Seleccione **Certificados mutuos** en la lista desplegable **Con credenciales**.

![Certificados mutuos][Certificados mutuos]

Seleccione el certificado que desea en la lista desplegable **Certificado de cliente**. Si aparecen varios certificados, revise el asunto o los últimos cuatro caracteres de la huella digital (conforme a la sección anterior) para determinar cuál es el certificado correcto.

![Seleccionar certificado][Seleccionar certificado]

Haga clic en **Guardar** para guardar el cambio de configuración de la API.

> Este cambio se hace efectivo de forma inmediata y llama a las operaciones de la API que realizarán la autenticación en el servidor back-end con el certificado.

![Guardar cambios de API][Guardar cambios de API]

> Cuando se especifica un certificado para la autenticación proxy del servicio back-end de una API, el certificado se integra en la directiva de dicha API y puede verse en el editor de directivas.

![Directiva de certificados][Directiva de certificados]

  [entidad de certificados de la API de REST de Administración de API de Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [Requisitos previos]: #prerequisites
  [Cargar un certificado de cliente]: #step1
  [Eliminar un certificado de cliente]: #step1a
  [Configurar una API para realizar la autenticación proxy con un certificado mutuo]: #step2
  [API Management console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [Certificados de cliente]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [Cargar certificado]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [1]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [Certificado cargado]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [Eliminar certificado]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [Confirmar eliminación]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [2]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [Seguridad de API]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [Certificados mutuos]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [Seleccionar certificado]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [Guardar cambios de API]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [Directiva de certificados]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
