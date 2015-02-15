<properties 
	pageTitle="Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido (SAS) | Microsoft Azure" 
	description="Aprenda a delegar el acceso a recursos de blob, cola y tabla con firmas de acceso compartido" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>



# Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido

Usar una firma de acceso compartido (SAS) es una manera eficaz de conceder acceso limitado a los blobs, las tablas y las colas en la cuenta de almacenamiento a otros clientes sin tener que exponer la clave de cuenta. En la parte 1 de este tutorial sobre firmas de acceso compartido, le ofreceremos información general del modelo SAS y una revisión de las prácticas recomendadas de SAS. [La parte 2](../storage-dotnet-shared-access-signature-part-2/) de este tutorial le guiará a través del proceso de creación de firmas de acceso compartido con el servicio BLOB.

## ¿Qué es una firma de acceso compartido? ##

Una firma de acceso compartido ofrece acceso delegado a recursos en la cuenta de almacenamiento. Esto significa que puede conceder permisos limitados de los clientes a blobs, colas o tablas durante un período específico y con un conjunto determinado de permisos sin tener que compartir las claves de acceso a las cuentas. La SAS es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con la SAS, el cliente solo tiene que pasar la SAS al método o constructor adecuados.

## ¿Cuándo debe usar una firma de acceso compartido? ##

Puede usar una SAS cuando desee proporcionar acceso a los recursos en la cuenta de almacenamiento a un cliente al que no se puede confiar la clave de cuenta. Las claves de cuenta de almacenamiento incluyen una clave primaria y secundaria, que conceden acceso administrativo a la cuenta y a todos los recursos contenidos en ella. La exposición de alguna de las claves de cuenta hace que exista la posibilidad de que se haga un uso negligente o malintencionado de la cuenta. Las firmas de acceso compartido ofrecen una alternativa segura que permite a los demás clientes leer, escribir y eliminar datos en la cuenta de almacenamiento según los permisos que se le hayan concedido y sin que sea necesaria la clave de cuenta.

Un escenario común en el que es útil una SAS es un servicio en el que los usuarios leen y escriben sus propios datos en la cuenta de almacenamiento. Existen dos patrones de diseño típicos en los escenarios en los que una cuenta de almacenamiento guarda datos de usuario:


1\. Los clientes cargan y descargan datos a través de un servicio de proxy front-end que realiza la autenticación. Este servicio de proxy front-end cuenta con la ventaja de permitir la validación de reglas de negocio, pero para grandes cantidades de datos o transacciones de gran volumen, la creación de un servicio que pueda escalarse para satisfacer la demanda puede ser complicada o costosa.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	Un servicio ligero realiza la autenticación del cliente según sea necesario y, a continuación, genera una SAS. Una vez que el cliente recibe la SAS, puede obtener acceso a los recursos de la cuenta de almacenamiento directamente con los permisos definidos por la SAS y para el intervalo permitido por ella. La SAS mitiga la necesidad de enrutar todos los datos a través del servicio de proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

Muchos servicios en tiempo real pueden usar una combinación de estos dos enfoques, según el escenario implicado, con algunos datos procesados y validados a través del proxy front-end mientras se guardan o leen los demás datos directamente mediante la SAS.

## Funcionamiento de una firma de acceso compartido ##

Una firma de acceso compartido es un URI que hace referencia a un recurso de almacenamiento e incluye un conjunto especial de parámetros de consulta que indican cómo el cliente puede obtener acceso al recurso. Uno de estos parámetros, la firma, se construye a partir de parámetros SAS y se firma con la clave de cuenta. Almacenamiento de Azure usa esa firma para autenticar la SAS.

Las firmas de acceso compartido tienen las siguientes restricciones que las definen, que se representan como un parámetro en el URI:

- **El recurso de almacenamiento.** Los recursos de almacenamiento para los que puede delegar el acceso, incluidos contenedores, blobs, colas, tablas y rangos de entidades de tabla.
- **Hora de inicio.** Se trata de la hora en que la firma de acceso compartido (SAS) es válida. La hora de inicio de una firma de acceso compartido es opcional; Si se omite, la SAS es efectiva inmediatamente. 
- **Hora de expiración.** Es el momento después del cual la firma de acceso compartido ya no es válida. Se recomienda especificar una hora de expiración para una SAS o asociarla a una directiva de acceso almacenada (véase más abajo).
- **Permisos.** Los permisos especificados en una SAS indican las operaciones que puede realizar el cliente en el recurso de almacenamiento con la SAS. 

A continuación se muestra un ejemplo de un URI de SAS que ofrece permisos de lectura y escritura en un blob. En la tabla siguiente se divide cada parte del URI para saber cómo contribuye a la SAS:

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    URI de blobs
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    La dirección del blob. Tenga en cuenta que se recomienda fehacientemente el uso de HTTPS.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Versión de servicios de almacenamiento
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    En la versión de servicios de almacenamiento 2012-02-12 y superiores, este parámetro indica qué versión usar.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Hora de inicio
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Se especifica en formato ISO 8061. Si desea que la SAS sea válida de inmediato, omita la hora de inicio.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Hora de expiración
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Se especifica en formato ISO 8061.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Recurso
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    El recurso es un blob.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Permisos
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Los permisos que concede la SAS son de lectura y escritura.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Firma
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Se usa para autenticar el acceso al blob. La firma es un HMAC que se procesa mediante una cadena para firmar y una clave con el algoritmo SHA256, y que se codifica mediante Base64.
                </p>
            </td>
        </tr>
    </tbody>
</table>


## Control de firmas de acceso compartido con una directiva de acceso almacenada ##

Una firma de acceso compartido puede presentar una de estas dos formas:

- **SAS ad hoc:** cuando se crea una SAS ad hoc, la hora de inicio, la hora de expiración, y los permisos de las SAS se especifican en el URI de la SAS (o de forma implícita, si se omite la hora de inicio). Este tipo de SAS puede crearse en un contenedor, un blob, una tabla o una cola.
- **SAS con directiva de acceso almacenada:** una directiva de acceso almacenada se define en un contenedor de recursos (un contenedor de blobs, una tabla o una cola) y puede usarse para administrar las restricciones de una o más firmas de acceso compartido. Cuando se asocia una SAS a una directiva de acceso almacenada, la SAS hereda las restricciones (la hora de inicio, la hora de expiración y los permisos) definidos para la directiva de acceso almacenada.

La diferencia entre las dos formas es importante para un escenario principal: revocación. Una SAS es una dirección URL, por lo que cualquier persona que obtenga la SAS puede usarla, independientemente de quién la solicitó para comenzar. Si una SAS se encuentra disponible públicamente, cualquier persona del mundo puede usarla. Una SAS distribuida es válida hasta que se produzca una de las cuatro situaciones:

1.	Se alcanza el tiempo de expiración especificado en la SAS.
2.	Se alcanza el tiempo de expiración especificado en la directiva de acceso almacenada al que hace referencia la SAS (si se hace referencia a una directiva de acceso almacenada y si especifica una hora de expiración). Esto puede producirse porque transcurra el intervalo o porque haya modificado la directiva de acceso almacenado para tener una hora de expiración pasada, que es una forma de revocar la SAS.
3.	Se elimina la directiva de acceso almacenada a la que hace referencia la SAS, que es otra forma de revocar la SAS. Tenga en cuenta que si vuelve a crear la directiva de acceso almacenada exactamente con el mismo nombre, todos los tokens de la SAS volverán a ser válidos según los permisos asociados a la directiva de acceso almacenada (si se presupone que la hora de expiración en la SAS no ha pasado). Si prevé revocar la SAS, asegúrese de usar un nombre distinto si vuelve a crear la directiva de acceso con una hora de expiración futura.
4.	Se vuelve a generar la clave de cuenta que se usó para crear la SAS.  Tenga en cuenta que la realización de este procedimiento provocará que todos los componentes de la aplicación que usen esa clave de cuenta no puedan autenticarse hasta que se actualicen para usar la otra clave de cuenta válida o la clave de cuenta que se acaba de volver a generar.
 
## Prácticas recomendadas para el uso de firmas de acceso compartido ##

Cuando use firmas de acceso compartido en sus aplicaciones, debe tener en cuenta dos posibles riesgos:

- Si se perdió una SAS, cualquier persona que la consiga puede usarla, lo que puede poner en riesgo su cuenta de almacenamiento.
- Si una SAS proporcionada para una aplicación cliente expira y la aplicación no puede recuperar una nueva SAS del servicio, la funcionalidad de la aplicación puede verse afectada.  

Las siguientes recomendaciones para el uso de firmas de acceso compartido le ayudarán a equilibrar estos riesgos:

1. **Siempre use HTTPS** para crear una SAS o distribuirla.  Si se pasa una SAS a través de HTTP y se intercepta, un atacante que realice un ataque de tipo "Man in the middle" podrá leer la SAS y, a continuación, usarla como lo podría hacer el usuario previsto. Esto puede poner en riesgo los datos confidenciales o permitir que un usuario malintencionado provoque daños en los datos.
2. **Haga referencia a las directivas de acceso almacenadas siempre que sea posible.** Las directivas de acceso almacenadas le ofrecerán la opción de revocar permisos sin tener que volver a generar las claves de la cuenta de almacenamiento.  Establezca la expiración de ellas con un plazo de tiempo grande (o infinito)  y asegúrese de que se actualiza regularmente para posponerla un poco más hacia el futuro.
3. **Use tiempos de expiración a corto plazo en una SAS ad-hoc.** De este modo, incluso si se pone en peligro una SAS de forma inadvertida, solo será viable durante un período breve. Esta práctica es especialmente importante si no puede hacer referencia a una directiva de acceso almacenada. Esta práctica también ayuda a limitar la cantidad de datos que pueden escribirse en un blob mediante la limitación del tiempo disponible para su carga.
4. **Haga que los clientes renueven automáticamente las SAS si es necesario.** Los clientes deberían renovar las SAS correctamente antes de la expiración esperada, para que haya tiempo para reintentos si el servicio que proporciona las SAS no está disponible.  Si su SAS está pensada para usarse con un número reducido de operaciones inmediatas de corta duración, que deben completarse en el tiempo de expiración determinado, puede que esto no sea necesario, ya que no se espera que las asociaciones de seguridad se renueven.  Sin embargo, si tiene clientes que habitualmente realizan solicitudes a través de SAS, existe la posibilidad de expiración.  La consideración principal es equilibrar la necesidad de que la SAS sea de corta duración (como se indicó anteriormente) con la necesidad de garantizar que el cliente solicite la renovación con tiempo suficiente para evitar las interrupciones debido a la expiración de SAS antes una renovación correcta.
5. **Tenga cuidado con la hora de inicio de la SAS.** Si establece la hora de inicio de una SAS en **Ahora**, debido al desplazamiento del reloj (las diferencias en la hora actual según las distintas máquinas), pueden producirse errores de forma intermitente durante los primeros minutos.  En general, establezca la hora de inicio a al menos 15 minutos antes o no la establezca en absoluto, para que sea inmediatamente válida en todos los casos.  Lo mismo sucede normalmente con la hora de expiración, recuerde que puede haber hasta 15 minutos de desplazamiento del reloj en ambas direcciones para cualquier solicitud.  Tenga en cuenta para los clientes que usan una versión de REST anterior al 12/02/2012, la duración máxima de una SAS que no hace referencia a una directiva de acceso almacenada es de 1 hora y se producirá un error con las directivas que especifiquen un período más largo.
6.	**Sea específico con el recurso al que se va a acceder.** Una práctica recomendada de seguridad habitual es proporcionar al usuario los privilegios mínimos necesarios.  Si un usuario solo necesita acceso de lectura para una sola entidad, otórguele únicamente acceso de lectura a esa entidad, y no acceso de lectura, escritura y eliminación a todas las entidades.  Esto ayuda a mitigar el riesgo de poner en peligro las SAS, ya que las SAS tienen menos poder en manos de un atacante.
7.	**Comprenda que se le cobrará por cualquier uso de su cuenta, incluido el realizado con las SAS.** Si proporciona acceso de escritura a un blob, un usuario puede decidir cargar un blob de 200 GB.  Si le proporciona también acceso de lectura, puede elegir descargarlo 10 veces, lo que supondrá 2 TB en coste de salida.  De nuevo, proporcione permisos limitados para ayudar a mitigar las posibilidades de los usuarios malintencionados.  Use SAS de corta duración para reducir esta amenaza (pero tenga en cuenta el desplazamiento del reloj para la hora de finalización).
8.	**Valide los datos escritos mediante las SAS.** Cuando una aplicación cliente escribe datos en la cuenta de almacenamiento, tenga en cuenta que puede haber problemas con esos datos. Si la aplicación requiere que esos datos se validen o se autoricen antes de que estén listos para poder usarlos, debe llevar a cabo esta validación después de que se escriban los datos y antes de que los use la aplicación. Esta práctica también ofrece protección frente a datos erróneos o malintencionados que se escriban en su cuenta, ya sea por parte de un usuario que adquirió correctamente las asociaciones de seguridad o por un usuario que aproveche una SAS que se haya filtrado.
9. **No use siempre la SAS.** A veces, los riesgos asociados a una operación determinada sobre su cuenta de almacenamiento superan a las ventajas de las SAS.  Para esas operaciones, cree un servicio de nivel intermedio que escriba en su cuenta de almacenamiento tras realizar auditoría, autenticación y validación de reglas de negocios. Además, a veces es más sencillo administrar el acceso de otras maneras. Por ejemplo, si desea que todos los blobs de un contenedor puedan leerse públicamente, puede hacer que el contenedor sea público, en lugar de proporcionar una SAS para que todos los clientes tengan acceso.
10.	**Use el análisis de almacenamiento para supervisar su aplicación.** Puede usar el registro y las métricas para observar cualquier pico de errores de autenticación debido a una interrupción en el servicio de su proveedor de SAS o la eliminación involuntaria de una directiva de acceso almacenada. Consulte el [Blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) para obtener más información.

## Conclusión ##

Las firmas de acceso compartido son útiles para ofrecer permisos limitados a su cuenta de almacenamiento a clientes que no deben tener la clave de cuenta.  Por ese motivo, son una parte fundamental del modelo de seguridad para cualquier aplicación que use Almacenamiento de Azure.  Si sigue las prácticas recomendadas descritas aquí, puede usar la SAS para ofrecer una mayor flexibilidad de acceso a los recursos en la cuenta de almacenamiento sin que se ponga en riesgo la seguridad de la aplicación.

## Next Steps ##

[Shared Access Signatures, Part 2: Create and Use a SAS with the Blob Service](../storage-dotnet-shared-access-signature-part-2/)

[Manage Access to Azure Storage Resources](http://msdn.microsoft.com/es-es/library/windowsazure/ee393343.aspx)

[Delegating Access with a Shared Access Signature (REST API)](http://msdn.microsoft.com/es-es/library/windowsazure/ee395415.aspx)

[Introducing Table and Queue SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png



<!--HONumber=42-->
