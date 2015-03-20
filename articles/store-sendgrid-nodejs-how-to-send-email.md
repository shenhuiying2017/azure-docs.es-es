<properties 
	pageTitle="Uso del servicio de correo electrónico SendGrid (Java) - Azure" 
	description="Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Ejemplos de código escritos con la API de Node.js." 
	services="" 
	documentationCenter="nodejs" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>





# Envío de correo electrónico con SendGrid desde Node.js

Esta guía muestra cómo realizar tareas comunes de programación con el
servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos usando la API Node.js. Entre los escenarios descritos se incluyen **construir correo electrónico**,
**envío de correo electrónico**, **agregar archivos adjuntos**, **uso de filtros** y
**actualización de propiedades**. Para obtener más información acerca de SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes][].

## Tabla de contenido

* [¿Qué es el servicio de correo electrónico SendGrid?][]   
* [Creación de una cuenta de SendGrid][]   
* [Hacer referencia al módulo SendGrid para Node.js][]   
* [Procedimiento: un correo electrónico][]   
* [Procedimiento: un correo electrónico][]   
* [Procedimiento: un archivo adjunto][]   
* [Procedimiento: filtros para habilitar pies de página, seguimiento y Twitter][]   
* [Codificación las propiedades del correo electrónico][]   
* [Procedimiento: servicios adicionales de SendGrid][]   
* [Pasos siguientes][1]

## <a name="whatis"> </a>¿Qué es el servicio de correo electrónico SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona entrega confiable de
[correo electrónico transaccional], escalabilidad y análisis en tiempo real junto con API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de métricas en tiempo real para correo electrónico bloqueado, por ejemplo, y la capacidad de respuesta del cliente
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
-   Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte [https://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Creación de una cuenta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Hacer referencia al módulo SendGrid para Node.js

El módulo SendGrid para Node.js se puede instalar a través del administrador de paquetes de Node (npm) mediante el siguiente comando:

    npm install sendgrid

Después de la instalación, puede solicitar la incorporación del módulo en su aplicación mediante el siguiente código:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

El módulo SendGrid exporta las funciones **SendGrid** y **Email**.
**SendGrid** es responsable del envío de correo electrónico a través de la API web, mientras que **Email** contiene un mensaje de correo electrónico.

## <a name="createemail"></a> Procedimiento: un correo electrónico

La creación de un mensaje de correo electrónico mediante el módulo SendGrid conlleva en primer lugar la creación de un mensaje de correo electrónico usando la función Email y, a continuación, el envío de dicho mensaje usando la función SendGrid. Este es un ejemplo de creación de un nuevo mensaje usando la función Email:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

También es posible especificar un mensaje de HTML para clientes que lo admitan mediante la configuración de la propiedad html. Por ejemplo:

    html: This is a sample <b>HTML<b> email message.

La configuración de las propiedades de texto y html ofrece la posibilidad de enriquecer el contenido de texto en los clientes que no admiten mensajes HTML.

Para obtener más información acerca de todas las propiedades admitidas por la función Email, consulte [sendgrid-nodejs][].

## <a name="sendemail"></a> Procedimiento: un correo electrónico

Después de crear un mensaje de correo electrónico mediante la función Email, puede enviarlo a través de la API web proporcionada por SendGrid. 

### API Web

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE] Mientras que los ejemplos anteriores muestran la transferencia de un objeto de correo electrónico y la función de devolución de llamada, también es posible invocar la función de envío mediante la especificación directa de las propiedades del correo electrónico. Por ejemplo:  
>
>`````
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## <a name="addattachment"></a> Procedimiento: un archivo adjunto

Los archivos adjuntos se pueden agregar a un mensaje mediante la especificación de los nombres y las rutas de archivo en la propiedad **files**. El siguiente ejemplo demuestra el envío de un archivo adjunto:

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [AZURE.NOTE] Al utilizar la propiedad **files**, el archivo debe ser accesible a través de [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Si el archivo que desea adjuntar está hospedado en el almacenamiento de Azure, por ejemplo, en un contenedor de blob, debe copiar el archivo en primer lugar en un almacenamiento local o en una unidad Azure antes de enviarlo como archivo adjunto con la propiedad **files**.

## <a name="usefilters"></a> Procedimiento: Usar filtros para habilitar pies de página y seguimiento

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de filtros. Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings][].

Los filtros se pueden aplicar a un mensaje usando la propiedad **filters**.
Cada filtro se especifica mediante un hash que contiene la configuración específica del filtro.
Los siguientes ejemplos demuestran el uso de los filtros de pie de página y seguimiento por clics:

### Pie de página

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### Seguimiento por clics

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });
     sendgrid.send(email);

## <a name="updateproperties"></a> Procedimiento: las propiedades del correo electrónico

Es posible sobrescribir algunas propiedades de correo electrónico con **set*Property*** o anexarlas con **add*Property***. Por ejemplo, puede agregar destinatarios adicionales mediante

    email.addTo('jeff@contoso.com');
    or set a filter by using

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Para obtener más información, consulte [sendgrid-nodejs][].

## <a name="useservices"></a> Procedimiento: servicios adicionales de SendGrid

SendGrid ofrece API basadas en web que puede usar para aprovechar la
funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte [SendGrid API documentation][].

## <a name="nextsteps"> </a>Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

-   Repositorio del módulo SendGrid para Node.js: [sendgrid-nodejs][]
-   Documentación sobre la API de SendGrid <https://sendgrid.com/docs>
-   Oferta especial de SendGrid para clientes de Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [Pasos siguientes]: http://www.windowsazure.com/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [¿Qué es el servicio de correo electrónico SendGrid?]: #whatis
  [Creación de una cuenta de SendGrid]: #createaccount
  [Hacer referencia al módulo SendGrid para Node.js]: #reference
  [Procedimiento: un correo electrónico]: #createemail
  [Procedimiento: un correo electrónico]: #sendemail
  [Procedimiento: un archivo adjunto]: #addattachment
  [Procedimiento: filtros para habilitar pies de página, seguimiento y Twitter]: #usefilters
  [Procedimiento: las propiedades del correo electrónico]: #updateproperties
  [Procedimiento: servicios adicionales de SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [oferta especial]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  
  [Configuración del filtro]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentación sobre la API de SendGrid]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [entrega de correo electrónico transaccional]: https://sendgrid.com/transactional-email

<!--HONumber=47-->
