<properties linkid="manage-services-storage-net-shared-access-signature-part-1" urlDisplayName="" pageTitle="Shared access signatures: Understanding the SAS Model | Microsoft Azure" metaKeywords="Azure blob, Azure table, Azure queue, shared access signatures" description="Learn about delegating access to blob, queue, and table resources with shared access signatures" metaCanonical="" services="storage" documentationCenter="" title="Part 1: Understanding the SAS Model" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido

Usar una firma de acceso compartido (SAS) es una manera eficaz de conceder acceso limitado a los blobs, las tablas y las colas en la cuenta de almacenamiento a otros clientes sin tener que exponer la clave de cuenta. En la parte 1 de este tutorial sobre firmas de acceso compartido, le ofreceremos información general del modelo SAS y una revisión de las prácticas recomendadas de SAS. La [parte 2][parte 2] de este tutorial le guiará a través del proceso de creación de firmas de acceso compartido con el servicio BLOB.

## ¿Qué es una firma de acceso compartido?

Una firma de acceso compartido ofrece acceso delegado a recursos en la cuenta de almacenamiento. Esto significa que puede conceder permisos limitados de los clientes a blobs, colas o tablas durante un período específico y con un conjunto determinado de permisos sin tener que compartir las claves de acceso a las cuentas. La SAS es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con la SAS, el cliente solo tiene que pasar la SAS al método o constructor adecuados.

## ¿Cuándo debe usar una firma de acceso compartido?

Puede usar una SAS cuando desee proporcionar acceso a los recursos en la cuenta de almacenamiento a un cliente al que no se puede confiar la clave de cuenta. Las claves de cuenta de almacenamiento incluyen una clave primaria y secundaria, que conceden acceso administrativo a la cuenta y a todos los recursos contenidos en ella. La exposición de alguna de las claves de cuenta hace que exista la posibilidad de que se haga un uso negligente o malintencionado de la cuenta. Las firmas de acceso compartido ofrecen una alternativa segura que permite a los demás clientes leer, escribir y eliminar datos en la cuenta de almacenamiento según los permisos que se le hayan concedido y sin que sea necesaria la clave de cuenta.

Un escenario común en el que es útil una SAS es un servicio en el que los usuarios leen y escriben sus propios datos en la cuenta de almacenamiento. Existen dos patrones de diseño típicos en los escenarios en los que una cuenta de almacenamiento guarda datos de usuario:

1\.	Los clientes cargan y descargan datos a través de un servicio de proxy front-end que realiza la autenticación. Este servicio de proxy front-end cuenta con la ventaja de permitir la validación de reglas de negocio, pero para grandes cantidades de datos o transacciones de gran volumen, la creación de un servicio que pueda escalarse para satisfacer la demanda puede ser complicada o costosa.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]


2\. Un servicio ligero realiza la autenticación del cliente según sea necesario y, a continuación, genera una SAS. Una vez que el cliente recibe la SAS, puede obtener acceso a los recursos de la cuenta de almacenamiento directamente con los permisos definidos por la SAS y para el intervalo permitido por ella. La SAS mitiga la necesidad de enrutar todos los datos a través del servicio de proxy front-end.

![sas-storage-provider-service][sas-storage-provider-service]

Muchos servicios en tiempo real pueden usar una combinación de estos dos enfoques, según el escenario implicado, con algunos datos procesados y validados a través del proxy front-end mientras se guardan o leen los demás datos directamente mediante la SAS.

## Funcionamiento de una firma de acceso compartido

Una firma de acceso compartido es un URI que hace referencia a un recurso de almacenamiento e incluye un conjunto especial de parámetros de consulta que indican cómo el cliente puede obtener acceso al recurso. Uno de estos parámetros, la firma, se construye a partir de parámetros SAS y se firma con la clave de cuenta. Almacenamiento de Azure usa esa firma para autenticar la SAS.

Las firmas de acceso compartido tienen las siguientes restricciones que las definen, que se representan como un parámetro en el URI:

-   **El recurso de almacenamiento.** Recursos de almacenamiento para los que puede delegar el acceso, incluidos contenedores, blobs, colas, tablas y rangos de entidades de tabla.
-   **Hora de inicio.** Es la hora en la que la SAS comienza a ser válida. La hora de inicio de una firma de acceso compartido es opcional; si se omite, la SAS se activa de inmediato.
-   **Hora de expiración.** Es la hora a partir de la que la SAS deja de ser válida. Las prácticas recomendadas aconsejan que especifique una hora de expiración para una SAS o que la asocie a una directiva de acceso almacenada (puede obtener más información a continuación).
-   **Permisos.** Los permisos especificados en una SAS indican qué operaciones puede realizar el cliente en el recurso de almacenamiento con la SAS.

A continuación se muestra un ejemplo de un URI de SAS que ofrece permisos de lectura y escritura en un blob. En la tabla siguiente se divide cada parte del URI para saber cómo contribuye a la SAS:

<https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D>

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p>URI de blobs</p></td>
<td align="left"><p>https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt</p></td>
<td align="left"><p>La dirección del blob. Tenga en cuenta que se recomienda fehacientemente el uso de HTTPS.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Versión de servicios de almacenamiento</p></td>
<td align="left"><p>sv=2012-02-12</p></td>
<td align="left"><p>En la versión de servicios de almacenamiento 2012-02-12 y superiores, este parámetro indica qué versión usar.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Hora de inicio</p></td>
<td align="left"><p>st=2013-04-29T22%3A18%3A26Z</p></td>
<td align="left"><p>Se especifica en formato ISO 8061. Si desea que la SAS sea válida de inmediato, omita la hora de inicio.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Hora de expiración</p></td>
<td align="left"><p>se=2013-04-30T02%3A23%3A26Z</p></td>
<td align="left"><p>Se especifica en formato ISO 8061.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Recurso</p></td>
<td align="left"><p>sr=b</p></td>
<td align="left"><p>El recurso es un blob.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Permisos</p></td>
<td align="left"><p>sp=rw</p></td>
<td align="left"><p>Los permisos que concede la SAS son de lectura y escritura.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Firma</p></td>
<td align="left"><p>sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D</p></td>
<td align="left"><p>Se usa para autenticar el acceso al blob. La firma es un HMAC que se procesa mediante una cadena para firmar y una clave con el algoritmo SHA256, y que se codifica mediante Base64.</p></td>
</tr>
</tbody>
</table>

## Control de firmas de acceso compartido con una directiva de acceso almacenada

Una firma de acceso compartido puede presentar una de estas dos formas:

-   **SAS ad-hoc:** Cuando cree una SAS ad-hoc, la hora de inicio, la hora de expiración y los permisos para la SAS se especifican en el URI de SAS (o se encuentran implícitos en el caso en el que se omita la hora de inicio). Este tipo de SAS puede crearse en un contenedor, un blob, una tabla o una cola.
-   **SAS con directiva de acceso almacenada:** Se define una directiva de acceso almacenada en un contenedor de recursos (un contenedor de blobs, una tabla o una cola) y se puede usar para administrar las restricciones de una o varias firmas de acceso compartido. Cuando asocia una SAS a una directiva de acceso almacenada, la SAS hereda las restricciones (hora de inicio, hora de expiración y permisos) definidas para la directiva de acceso almacenada.

La diferencia entre las dos formas es importante para un escenario principal, la revocación. Una SAS es una dirección URL, por lo que cualquier persona que obtenga la SAS puede usarla, independientemente de quién la solicitó para comenzar. Si una SAS se encuentra disponible públicamente, cualquier persona del mundo puede usarla. Una SAS distribuida es válida hasta que se produzca una de las cuatro situaciones:

1.  Se alcanza el tiempo de expiración especificado en la SAS.
2.  Se alcanza el tiempo de expiración especificado en la directiva de acceso almacenada al que hace referencia la SAS (si se hace referencia a una directiva de acceso almacenada y si especifica una hora de expiración). Esto puede producirse porque transcurra el intervalo o porque haya modificado la directiva de acceso almacenado para tener una hora de expiración pasada, que es una forma de revocar la SAS.
3.  Se elimina la directiva de acceso almacenada a la que hace referencia la SAS, que es otra forma de revocar la SAS. Tenga en cuenta que si vuelve a crear la directiva de acceso almacenada exactamente con el mismo nombre, todos los tokens de la SAS volverán a ser válidos según los permisos asociados a la directiva de acceso almacenada (si se presupone que la hora de expiración en la SAS no ha pasado). Si prevé revocar la SAS, asegúrese de usar un nombre distinto si vuelve a crear la directiva de acceso con una hora de expiración futura.
4.  Se vuelve a generar la clave de cuenta que se usó para crear la SAS. Tenga en cuenta que la realización de este procedimiento provocará que todos los componentes de la aplicación que usen esa clave de cuenta no puedan autenticarse hasta que se actualicen para usar la otra clave de cuenta válida o la clave de cuenta que se acaba de volver a generar.

## Prácticas recomendadas para el uso de firmas de acceso compartido

Cuando use firmas de acceso compartido en sus aplicaciones, debe tener en cuenta dos posibles riesgos:

-   Si se perdió una SAS, cualquier persona que la consiga puede usarla, lo que puede poner en riesgo su cuenta de almacenamiento.
-   Si una SAS proporcionada para una aplicación cliente expira y la aplicación no puede recuperar una nueva SAS del servicio, la funcionalidad de la aplicación puede verse afectada.

Las siguientes recomendaciones para el uso de firmas de acceso compartido le ayudarán a equilibrar estos riesgos:

1.  **Siempre use HTTPS** para crear una SAS o distribuirla. Si se pasa una SAS a través de HTTP y se intercepta, un atacante que realice un ataque de tipo "Man in the middle" podrá leer la SAS y, a continuación, usarla como lo podría hacer el usuario previsto. Esto puede poner en riesgo los datos confidenciales o permitir que un usuario malintencionado provoque daños en los datos.
2.  **Haga referencia a las directivas de acceso almacenadas cuando sea posible.** Las directivas de acceso almacenadas le ofrecen la posibilidad de revocar permisos sin tener que volver a generar las claves de cuenta de almacenamiento. Establezca la expiración en ellas para que sea un período largo (o infinito) y asegúrese de que se actualiza regularmente para trasladarla a un punto posterior en el tiempo.
3.  **Use horas de expiración a corto plazo en una SAS ad-hoc.** De esta forma, incluso si la SAS está en peligro sin saberlo, será solo viable para una duración breve. Esta práctica es especialmente importante si no puede hacer referencia a una directiva de acceso almacenada. Esta práctica también ayuda a limitar la cantidad de datos que puede escribirse en un blob mediante la limitación del tiempo disponible para cargarlos.
4.  **Haga que los clientes renueven automáticamente la SAS si fuese necesario.** Los clientes deben renovar la SAS correctamente antes de la expiración esperada para que exista tiempo para los reintentos si el servicio que ofrece la SAS no está disponible. Si la SAS se ha creado para usarse en un número reducido de operaciones de corta duración e inmediatas, que se espera que se va a completar dentro del tiempo de expiración determinado, es posible que no sea necesario ese procedimiento, ya que no se espera que la SAS se renueve. Sin embargo, si dispone de un cliente que realice solicitudes de forma rutinaria a través de la SAS, existe la posibilidad de la expiración. La consideración clave es equilibrar la necesidad de que la SAS sea de corta duración (como se estableció anteriormente) para garantizar que el cliente está solicitando la renovación con la suficiente antelación como para evitar la interrupción debido a la expiración de SAS antes de una renovación correcta.
5.  **Tenga cuidado con la hora de inicio de la SAS.** Si establece la hora de inicio de la SAS en **now**, pueden producirse errores intermitentes durante los primeros minutos debido al desplazamiento del reloj (diferencias en la hora actual según las distintas máquinas). En general, establezca la hora de inicio para que sea al menos 15 minutos antes o no la establezca. Si lo hace, será válida de inmediato en todos los casos. Normalmente se aplica lo mismo a la hora de expiración. Recuerde que debe tener en cuenta hasta 15 minutos de desplazamiento del reloj en cualquier dirección en una solicitud. Nota para los clientes con una versión REST anterior a 2012-02-12: la duración máxima de una SAS que no hace referencia a una directiva de acceso almacenada es de 1 hora y se producirá un error en las directivas que especifican un período más largo.
6.  **Sea específico con el recurso al que se va a tener acceso.** Una práctica recomendada de seguridad habitual es proporcionar al usuario los privilegios mínimos necesarios. Si un usuario solo necesita acceso de lectura en una única entidad, concédale acceso de lectura a esa única entidad y no acceso de lectura, escritura o eliminación a todas las entidades. Esto también ayuda a mitigar la amenaza de que la SAS se encuentre en peligro, ya que esta tiene menos poder en las manos de un atacante.
7.  **Comprenda que se le hará un cargo en la cuenta por cualquier uso, incluido el realizado con la SAS.** Si proporciona acceso de escritura a un blob, el usuario puede seleccionar cargar un blob de 200 GB. Si le proporciona también acceso de lectura, puede seleccionar descargarlo 10 veces, lo que le supone 2 TB de costes de salida. Proporcione de nuevo permisos limitados para ayudar a mitigar la posibilidad de usuarios malintencionados. Use una SAS de corta duración para reducir esa amenaza (pero tenga en cuenta el desplazamiento del reloj y la hora final).
8.  **Valide los datos escritos mediante la SAS.** Cuando una aplicación cliente escribe datos en la cuenta de almacenamiento, tenga en cuenta que pueden existir problemas con esos datos. Si la aplicación requiere que se validen o autoricen los datos antes de que estén listos para usar, debe realizar la validación después de que se escriban los datos y antes de que la aplicación los use. Esta práctica también le protege frente a los datos erróneos o malintencionados que se escriben en la cuenta, ya sea mediante un usuario que adquirió correctamente la SAS o un usuario que aproveche una SAS errónea.
9.  **No use siempre la SAS.** En ocasiones, los riesgos asociados a una operación determinada en la cuenta de almacenamiento superan a las ventajas del uso de la SAS. Para esas operaciones, cree un servicio de nivel medio que escriba en la cuenta de almacenamiento después de llevar a cabo una auditoría, autenticación o validación de la regla de negocio. A veces también es más sencillo administrar el acceso de otras formas. Por ejemplo, si desea que todos los blobs de un contenedor puedan leerse públicamente, puede hacer que el contenedor sea público en lugar de proporcionar un SAS a cada cliente para obtener acceso.
10. **Use el análisis de almacenamiento para supervisar la aplicación.** Puede hacer uso de registros y métricas para observar cualquier pico en los errores de autenticación producidos por la interrupción del servicio del proveedor de SAS o la eliminación involuntaria de una directiva de acceso almacenada. Consulte el [blog del equipo de almacenamiento de Azure][blog del equipo de almacenamiento de Azure] (en inglés) para obtener más información.

## Conclusión

Las firmas de acceso compartido son útiles para ofrecer permisos limitados a su cuenta de almacenamiento a clientes que no deben tener la clave de cuenta. Por ese motivo, son una parte fundamental del modelo de seguridad para cualquier aplicación que use Almacenamiento de Azure. Si sigue las prácticas recomendadas descritas aquí, puede usar la SAS para ofrecer una mayor flexibilidad de acceso a los recursos en la cuenta de almacenamiento sin que se ponga en riesgo la seguridad de la aplicación.

## Pasos siguientes

[Firmas de acceso compartido, parte 2: Creación y uso de una firma de acceso compartido con el servicio BLOB][parte 2]

[Administración del acceso a los recursos de almacenamiento de Azure][Administración del acceso a los recursos de almacenamiento de Azure]

[Delegación de acceso con una firma de acceso compartido (API de REST)][Delegación de acceso con una firma de acceso compartido (API de REST)]

[Introducción a SAS (firma de acceso compartido) de Tabla, SAS de Cola y actualización a SAS de Blob][Introducción a SAS (firma de acceso compartido) de Tabla, SAS de Cola y actualización a SAS de Blob]
 [sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png

  [parte 2]: ../storage-dotnet-shared-access-signature-part-2/
  [blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [Administración del acceso a los recursos de almacenamiento de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/ee393343.aspx
