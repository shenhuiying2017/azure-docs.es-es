<properties 
	pageTitle="Uso del servicio de correo electrónico SendGrid (Java) - Azure" 
	description="Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Ejemplos de código escritos con la API de Node.js." 
	services="" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="mwasson"/>





# Envío de correo electrónico con SendGrid desde Node.js

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos usando la API Node.js. Entre los escenarios descritos se incluyen **creación de correo electrónico**, **envío de correo electrónico**, **incorporación de archivos adjuntos**, **uso de filtros** y **actualización de propiedades**. Para obtener más información sobre SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes][].

## Tabla de contenido

* [¿Qué es el servicio de correo electrónico SendGrid?][]   
* [Creación de una cuenta de SendGrid][]   
* [Referencia al módulo SendGrid para Node.js][]   
* [Creación de un correo electrónico][]   
* [Envío de un correo electrónico][]   
* [Incorporación de un archivo adjunto][]   
* [Uso de filtros para habilitar pies de página, seguimiento y análisis][]   
* [Actualización de las propiedades del correo electrónico][]   
* [Uso de servicios adicionales de SendGrid][]   
* [Pasos siguientes][1]

## <a name="whatis"> </a>¿Qué es el servicio de correo electrónico SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona un sistema fiable de [entrega de correos electrónicos transaccional], escalabilidad y análisis en tiempo real junto con API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de diversas métricas en tiempo real, como las direcciones de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
-   Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte [https://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Creación de una cuenta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Referencia al módulo SendGrid para Node.js

El módulo SendGrid para Node.js se puede instalar a través del administrador de paquetes de Node (npm) mediante el siguiente comando:

    npm install sendgrid

Después de la instalación, puede solicitar la incorporación del módulo en su aplicación mediante el siguiente código:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

El módulo SendGrid exporta las funciones **SendGrid** y **Email**. **SendGrid** es responsable del envío de correo electrónico a través de la API web, mientras que **Email** contiene un mensaje de correo electrónico.

## <a name="createemail"> </a>Creación de un correo electrónico

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

## <a name="sendemail"> </a>Envío de un correo electrónico

Después de crear un mensaje de correo electrónico mediante la función Email, puede enviarlo a través de la API web proporcionada por SendGrid.

### API Web

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE]Mientras que los ejemplos anteriores muestran la transferencia de un objeto de correo electrónico y la función de devolución de llamada, también es posible invocar la función de envío mediante la especificación directa de las propiedades del correo electrónico. Por ejemplo:
>
>`````
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## <a name="addattachment"> </a>Incorporación de un archivo adjunto

Los archivos adjuntos se pueden agregar a un mensaje especificando los nombres y las rutas de archivo en la propiedad **files**. El siguiente ejemplo demuestra el envío de un archivo adjunto:

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

> [AZURE.NOTE]Al usar la propiedad **files**, el archivo debe ser accesible a través de [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Si el archivo que desea adjuntar está hospedado en el almacenamiento de Azure, por ejemplo, en un contenedor de blob, debe copiar el archivo en primer lugar en un almacenamiento local o en una unidad Azure antes de enviarlo como archivo adjunto con la propiedad **files**.

## <a name="usefilters"> </a>Uso de filtros para habilitar pies de página y seguimiento

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de filtros. Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings][].

Los filtros se pueden aplicar a un mensaje usando la propiedad **filters**. Cada filtro se especifica mediante un hash que contiene la configuración específica del filtro. Los siguientes ejemplos demuestran el uso de los filtros de pie de página y seguimiento por clics:

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

## <a name="updateproperties"> </a>Actualización de las propiedades del correo electrónico

Es posible sobrescribir algunas propiedades de correo electrónico con **set*Property*** o anexarlas con **add*Property***. Por ejemplo, puede agregar destinatarios adicionales mediante

    email.addTo('jeff@contoso.com');

o configurar un filtro mediante

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Para obtener más información, consulte [sendgrid-nodejs][].

## <a name="useservices"> </a>Uso de servicios adicionales de SendGrid

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte la [Documentación sobre la API de SendGrid][].

## <a name="nextsteps"> </a>Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

-   Repositorio del módulo SendGrid para Node.js: [sendgrid-nodejs][]
-   Documentación sobre la API de SendGrid: <https://sendgrid.com/docs>
-   Oferta especial de SendGrid para clientes de Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [Pasos siguientes]: http://www.windowsazure.com/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [¿Qué es el servicio de correo electrónico SendGrid?]: #whatis
  [Creación de una cuenta de SendGrid]: #createaccount
  [Referencia al módulo SendGrid para Node.js]: #reference
  [Creación de un correo electrónico]: #createemail
  [Envío de un correo electrónico]: #sendemail
  [Incorporación de un archivo adjunto]: #addattachment
  [Uso de filtros para habilitar pies de página, seguimiento y análisis]: #usefilters
  [Actualización de las propiedades del correo electrónico]: #updateproperties
  [Uso de servicios adicionales de SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [special offer]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentación sobre la API de SendGrid]: https://sendgrid.com/docs
  [servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
  [entrega de correos electrónicos transaccional]: https://sendgrid.com/transactional-email

<!---HONumber=July15_HO3-->