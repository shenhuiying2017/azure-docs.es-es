<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="Node.js" title="How to Send Email Using SendGrid from Node.js" authors="" solutions="" manager="" editor="" />

Envío de correo electrónico con SendGrid desde Node.js
======================================================

Esta guía describe cómo realizar tareas de comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos usando la API Node.js. Los escenarios cubiertos incluyen **crear un correo electrónico**, **enviar un correo electrónico**, **agregar archivos adjuntos**, **utilizar filtros** y **actualizar propiedades**. Para obtener más información acerca de SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/blob-storage/#next-steps).

Tabla de contenido
------------------

-   [¿Qué es el servicio de correo electrónico SendGrid?](#whatis)
-   [Creación de una cuenta de SendGrid](#createaccount)
-   [Hacer referencia al módulo SendGrid para Node.js](#reference)
-   [Creación de un correo electrónico](#createemail)
-   [Envío de un correo electrónico](#sendemail)
-   [Incorporación de un archivo adjunto](#addattachment)
-   [Uso de filtros para habilitar pies de página, seguimiento y Twitter](#usefilters)
-   [Actualización de las propiedades del correo electrónico](#updateproperties)
-   [Uso de servicios adicionales de SendGrid](#useservices)
-   [Pasos siguientes](#nextsteps)

¿Qué es el servicio de correo electrónico SendGrid?
---------------------------------------------------

SendGrid es un [servicio de correo electrónico basado en la nube](http://sendgrid.com/solutions) (en inglés) que proporciona un sistema fiable de [entrega de correos electrónicos transaccional](http://sendgrid.com/transactional-email) (en inglés), escalabilidad y análisis en tiempo real, además de API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de métricas en tiempo real para correo electrónico bloqueado, por ejemplo, y la capacidad de respuesta del cliente
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
-   Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte <http://sendgrid.com> (en inglés).

Creación de una cuenta de SendGrid
----------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Hacer referencia al módulo SendGrid para Node.js
------------------------------------------------

El módulo SendGrid para Node.js se puede instalar a través del administrador de paquetes de Node (npm) mediante el siguiente comando:

    npm install sendgrid

Después de la instalación, puede solicitar la incorporación del módulo en su aplicación mediante el siguiente código:

    var SendGrid = require('sendgrid')

El módulo SendGrid exporta las funciones **SendGrid** y **Email**. **SendGrid** es responsable del envío de correo electrónico a través de SMTP o de la API web, mientras que **Email** contiene un mensaje de correo electrónico.

Creación de un correo electrónico
---------------------------------

La creación de un mensaje de correo electrónico mediante el módulo SendGrid conlleva en primer lugar la creación de un mensaje de correo electrónico usando la función Email y, a continuación, el envío de dicho mensaje usando la función SendGrid. Este es un ejemplo de creación de un nuevo mensaje usando la función Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

También es posible especificar un mensaje de HTML para clientes que lo admitan mediante la configuración de la propiedad html. Por ejemplo:

    html: Esto es un ejemplo de mensaje de correo electrónico <b>HTML<b>.

La configuración de las propiedades de texto y html ofrece la posibilidad de enriquecer el contenido de texto en los clientes que no admiten mensajes HTML.

Para obtener más información acerca de todas las propiedades admitidas por la función Email, consulte [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Envío de un correo electrónico
------------------------------

Después de crear un mensaje de correo electrónico con la función Email, puede enviarlo con SMTP o la API web que proporciona SendGrid. Para obtener más información acerca de las ventajas y diferencias de cada API, consulte [SMTP vs. API Web](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/) en la documentación de SendGrid.

El uso de la API SMTP o de la API web requiere en primer lugar que inicialice la función SendGrid usando el usuario y la clave de su cuenta de SendGrid como se muestra a continuación:

    var sender = new SendGrid.SendGrid('user','key');

El mensaje ya se puede enviar con SMTP o la API web. Las llamadas son virtualmente idénticas, transfieren el mensaje de correo electrónico y una función de devolución de llamada opcional; la devolución de llamada se utiliza para determinar el éxito o el error de la operación. Los siguientes ejemplos muestran la forma de enviar un mensaje con SMTP y la API Web.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### API Web

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

**Nota:**

Mientras que los ejemplos anteriores muestran la transferencia de un objeto de correo electrónico y la función de devolución de llamada, también es posible invocar las funciones de envío y SMTP mediante la especificación directa de las propiedades del correo electrónico. Por ejemplo:

``` {.prettyprint}
sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Incorporación de un archivo adjunto
-----------------------------------

Los archivos adjuntos se pueden agregar a un mensaje mediante la especificación de los nombre y las rutas de archivo en la propiedad **files**. El siguiente ejemplo demuestra el envío de un archivo adjunto:

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

**Nota:**

Al utilizar la propiedad **files**, el archivo debe ser accesible a través de [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Si el archivo que desea adjuntar está hospedado en el almacenamiento de Azure, por ejemplo, en un contenedor de blob, debe copiar el archivo en primer lugar en un almacenamiento local o en una unidad Azure antes de enviarlo como archivo adjunto con la propiedad **files**.

Uso de filtros para habilitar pies de página, seguimiento y Twitter
-------------------------------------------------------------------

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de filtros. Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

Los filtros se pueden aplicar a un mensaje mediante la propiedad **filters**. Cada filtro se especifica mediante un hash que contiene la configuración específica del filtro. Los siguientes ejemplos demuestran el uso de los filtros de pie de página, seguimiento por clics y Twitter:

### Pie de página

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Seguimiento por clics

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

Actualización de las propiedades del correo electrónico
-------------------------------------------------------

Es posible sobrescribir algunas propiedades de correo electrónico con **set*propiedad*** o anexarlas con **add*propiedad***. Por ejemplo, puede agregar destinatarios adicionales mediante

    email.addTo('jeff@contoso.com');

o configurar un filtro mediante

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Para obtener más información, consulte [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Uso de servicios adicionales de SendGrid
----------------------------------------

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte [SendGrid API documentation](http://docs.sendgrid.com/documentation/api/).

Pasos siguientes
----------------

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

-   Repositorio del módulo SendGrid para Node.js: [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)
-   Documentación sobre la API de SendGrid <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial de SendGrid para clientes de Azure: <http://sendgrid.com/azure.html>

