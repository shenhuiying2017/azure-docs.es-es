<properties
   pageTitle="Orientación sobre la Red de entrega de contenido (CDN) | Microsoft Azure"
   description="Orientación sobre la Red de entrega de contenido (CDN) para entregar contenido de alto ancho de banda hospedado en Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Orientación sobre Red de entrega de contenido (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Información general

La Red de entrega de contenido (CDN) de Microsoft Azure ofrece a los desarrolladores una solución global para entregar contenido con alto ancho de banda que se hospeda en Azure. La red CDN almacena en caché los objetos disponibles públicamente que se cargan desde el almacenamiento de blobs de Azure o una carpeta de aplicaciones en ubicaciones situadas estratégicamente para proporcionar el ancho de banda máximo para la entrega de contenido a los usuarios. Se suele usar para entregar el contenido estático, como imágenes, hojas de estilo, documentos, archivos, scripts de cliente y páginas HTML.

Las principales ventajas de usar la red CDN son una menor latencia y una entrega más rápida del contenido a los usuarios independientemente de su ubicación geográfica en relación con el centro de datos donde se hospeda la aplicación, así como una reducción de la carga en la aplicación en sí, ya que esta se alivia del procesamiento necesario para acceder al contenido y entregarlo. Esta reducción de carga puede ayudar a mejorar el rendimiento y la escalabilidad de la aplicación, así como a minimizar el costo de hospedaje mediante la reducción de los recursos de procesamiento necesarios para lograr un nivel específico de disponibilidad y rendimiento.

Si la red CDN de Azure no satisface sus necesidades, es posible que pueda usar otros sistemas de red de entrega de contenido que no sean implementados por Azure en sus aplicaciones. Como alternativa, es posible que pueda usar la red CDN de Azure para las aplicaciones hospedadas con otros proveedores si expone el contenido estático en el almacenamiento de Azure o en las instancias de proceso de Azure. ![](media/best-practices-cdn/CDN.png)

## Cómo y por qué se usa la red CDN

Entre los usos típicos de la red CDN se incluyen:

- La entrega de recursos estáticos para aplicaciones cliente, a menudo desde un sitio web. Puede tratarse de imágenes, hojas de estilo, documentos, archivos, scripts de cliente, páginas HTML, fragmentos de código HTML o cualquier otro contenido que el servidor no necesita modificar para cada solicitud. La aplicación puede crear elementos en tiempo de ejecución y ponerlos a disposición de la red CDN (por ejemplo, mediante la creación de una lista de los titulares más recientes), pero no lo hace para cada solicitud.

- La entrega de contenido público estático y compartido en dispositivos como, por ejemplo, teléfonos móviles y tabletas, donde la aplicación en sí es un servicio web que ofrece una API a los clientes. Además de otro contenido que el servidor no necesita modificar para cada solicitud, la red CDN puede ofrecer conjuntos de datos estáticos que el cliente puede usar, quizás para generar la interfaz de usuario cliente. Por ejemplo, se podría usar para entregar documentos JSON o XML.

- La prestación de servicio de sitios web completos que constan únicamente de contenido público estático que se entrega a los clientes, sin necesidad de disponer de recursos de proceso dedicados.

- El streaming de archivos de vídeo en el cliente a petición. El vídeo aprovecha la latencia baja y una conectividad confiable disponible en los centros de datos distribuidos en todo el mundo que ofrecen conexiones de red CDN.

- Una mejora general de la experiencia para los usuarios, especialmente los que se encuentran lejos de la ubicación del centro de datos de la aplicación y que de otro modo sufrirían una latencia mayor. Una gran parte del tamaño total del contenido de una aplicación web suele ser estática y el uso de la red CDN puede ayudar a mantener el rendimiento y la experiencia general del usuario al tiempo que elimina la necesidad de implementar la aplicación en varios centros de datos.

- La capacidad de soportar la creciente carga en las aplicaciones que prestan servicio a dispositivos móviles y fijos que forman parte de Internet de las cosas (IoT). Las grandes cantidades de este tipo de dispositivos y aparatos podrían fácilmente sobrepasar las capacidades de la aplicación si fuera necesario procesar mensajes de difusión y administrar directamente la distribución de actualizaciones de firmware.

- La capacidad de hacer frente a los picos y aumentos repentinos en la demanda sin tener que escalar la aplicación para evitar el consiguiente incremento de los costos. Por ejemplo, cuando se publique una actualización para un sistema operativo, dispositivo de hardware (por ejemplo, un modelo específico de enrutador) o dispositivo de consumidor (por ejemplo, televisor inteligente), habrá un importante pico en la demanda cuando millones de usuarios y dispositivos la descarguen durante un corto período de tiempo.

- En la tabla siguiente se muestran ejemplos de los tiempos medio hasta el primer byte desde distintas ubicaciones geográficas. El rol web de destino se implementa en Azure en la zona oeste de EE. UU. Hay una correlación estrecha entre el mayor aumento debido a la red CDN y su proximidad a un nodo de la red CDN. Encontrará una lista de ubicaciones de nodos de red CDN de Azure en [Ubicaciones POP de la Red de entrega de contenido de Azure (CDN)](http://msdn.microsoft.com/library/azure/gg680302.aspx).



|City |Tiempo hasta el primer byte (origen) |Tiempo hasta el primer byte (red CDN) | % más rápido para red CDN|
|---|---|---|---|
|San José, CA<sub>1</sub> |47,5 |46,5 |2 %|
|Dulles, VA<sub>2</sub> |109 |40,5 |169 %|
|Buenos Aires, Argentina |210 |151 |39 %|
|Londres, Reino Unido<sub>1</sub> |195 |44 |343 %|
|Shangai, China |242 |206 |17 %|
|Singapur<sub>1</sub> |214 |74 |189 %|
|Tokio, Japón<sub>1</sub> |163 |48 |240 %|
|Seúl, Corea del Sur |190 |190 |0 %|

Tiene un nodo de CDN de Azure en la misma ciudad.<sub>1</sub> Tiene un nodo de la red CDN de Azure en una ciudad vecina.<sub>2</sub>


## Desafíos  

Hay varios desafíos que se deben tener en cuenta a la hora de usar la red CDN:

- **Implementación**. Debe decidir dónde cargar el contenido de la red CDN (el origen desde el que la red CDN obtendrá el contenido) y si necesita implementar el contenido en más de un sistema de almacenamiento (por ejemplo, la red CDN y en una ubicación alternativa).

- El mecanismo de implementación de la aplicación debe tener en cuenta la implementación de contenido estático y los recursos, así como la implementación de los archivos de aplicación, como las páginas ASPX. Por ejemplo, puede que necesite un paso independiente para cargar contenido en el almacenamiento de blobs de Azure.

- **Control de versiones y control de caché**. Debe tener en cuenta cómo se actualizará el contenido estático y se implementarán las nuevas versiones. La red CDN actualmente no ofrece un mecanismo para vaciar el contenido para que nuevas versiones estén disponibles. Este es un desafío similar al de la administración del almacenamiento en caché del cliente, como por ejemplo, en un explorador web.

- **Pruebas**. Puede resultar difícil realizar pruebas locales de la configuración de la red CDN a la hora de desarrollar y probar una aplicación localmente o en el almacenamiento provisional.

- **SEO**. El contenido, como imágenes y documentos, se sirve desde un dominio diferente cuando usa la red CDN, lo que tendrá un efecto en la SEO para dicho contenido.

- **Seguridad**. Muchos servicios de la red CDN, como la red CDN de Azure, actualmente no ofrecen ningún tipo de control de acceso para el contenido.

- **Resistencia**. La red CDN es un potencial punto único de error para una aplicación. Tiene un SLA de menor disponibilidad que el de almacenamiento de blobs (que se puede usar para entregar contenido directamente). Por tanto, es posible que tenga que considerar la implementación de un mecanismo de reserva para contenido crítico.

- Los clientes se pueden conectar desde un entorno que no permita el acceso a los recursos en la red CDN. Podría tratarse de un entorno con restricciones de seguridad que limita el acceso a un conjunto de orígenes conocidos o uno que impide la carga de recursos desde cualquier otra cosa que no sea el origen de la página. Por lo tanto, se requerirá una implementación de reserva.

- Debe implementar un mecanismo para supervisar la disponibilidad del contenido a través de la red CDN.

Escenarios donde puede resultar útil incluir la red CDN:

- Cuando el contenido tiene una tasa baja de acierto y, por tanto, se accede a él pocas veces o una sola una vez durante el tiempo de validez de período de vida. La primera vez que un elemento se descarga, se produce un cargo de dos transacciones (desde el origen a la red CDN y luego desde la red CDN al cliente).

- Cuando los datos son privados, como por ejemplo, para las empresas de gran tamaño o los ecosistemas de cadena de suministro.


## Directrices generales y prácticas recomendadas

El uso de la red CDN es una buena manera de minimizar la carga en la aplicación y maximizar la disponibilidad y el rendimiento. Debería tener esto en cuenta para todo el contenido y recursos adecuados que usa la aplicación. Al diseñar la estrategia de uso de la red CDN, tenga en cuenta los siguientes puntos:

- **Origen** La implementación de contenido a través de la red CDN simplemente exige especificar un extremo HTTP (puerto 80) que el servicio de red CDN puede usar para acceder al contenido y almacenarlo en la memoria caché. + El extremo puede especificar un contenedor de almacenamiento de blobs de Azure con el contenido estático que quiera entregar a través de la red CDN. El contenedor debe marcarse como público. Solo los blobs de un contenedor público con acceso de lectura público estarán disponibles a través de la red CDN.

- El extremo puede especificar una carpeta denominada **cdn** en la raíz de una de las capas de proceso de la aplicación (por ejemplo, un rol web o una máquina virtual). Los resultados de las solicitudes de recursos, incluidos los recursos dinámicos, tales como las páginas ASPX, se almacenarán en caché en la red CDN. El período de almacenamiento en caché mínimo es de 300 segundos. Un periodo más corto impedirá que el contenido que se implemente en la red CDN (consulte la sección "<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Control de caché</a>" para más información).

- Si usa Sitios web de Azure, el extremo se establece en la carpeta raíz del sitio al seleccionar el sitio cuando se crea la instancia de red CDN. Todo el contenido del sitio estará disponible a través de la red CDN.

- En la mayoría de los casos, apuntar el extremo de red CDN a una carpeta dentro de una de las capas de proceso de la aplicación ofrece mayor flexibilidad y control. Por ejemplo, resulta más fácil administrar los requisitos de enrutamiento actuales y futuros, así como generar dinámicamente el contenido estático, tales como imágenes en miniatura.

- Puede usar cadenas de consulta para diferenciar los objetos en la memoria caché cuando el contenido se entrega desde orígenes dinámicos, tales como páginas ASPX. Sin embargo, este comportamiento se puede deshabilitar con una opción de configuración en el portal de administración cuando se especifica el extremo de la red CDN. Cuando se entrega el contenido desde el almacenamiento de blobs, las cadenas de consulta se tratan como literales de cadena para que dos elementos que tienen el mismo nombre pero cadenas de consulta diferentes se almacenen como elementos separados en la red CDN.

- Puede utilizar la reescritura de direcciones URL para que los recursos, como scripts y otro contenido, evite mover los archivos a la carpeta de origen de la red CDN.

- Al usar blobs de almacenamiento de Azure para almacenar el contenido de la red CDN, la dirección URL de los recursos de los blobs distingue mayúsculas de minúsculas para el nombre de contenedor y blob.

- Al usar Sitios web de Azure, especifique la ruta de acceso a la instancia de la red CDN en los vínculos a los recursos. Por ejemplo, en el siguiente fragmento se especifica un archivo de imagen en la carpeta **Images** del sitio que se entregarán a través de la red CDN:

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Implementación**. Es posible que sean necesario aprovisionar e implementar el contenido estático de forma independiente de la aplicación si no se incluyen en el paquete de implementación de la aplicación o el proceso. Tenga en cuenta cómo esto afectará al método de control de versiones que se use para administrar tanto los componentes de aplicaciones como el contenido de recursos estáticos.

- Tenga en cuenta cómo se tratará la unión (la combinación de varios archivos en un solo archivo) y la minificación (la eliminación de caracteres innecesarios, como espacios en blanco, caracteres de nueva línea, comentarios y otros caracteres) de los archivos de script y CSS. Estas técnicas habituales pueden reducir los tiempos de carga de los clientes y son compatibles con la entrega de contenido a través de la red CDN. Para más información, consulte el artículo sobre la [unión y minificación](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Si necesita implementar el contenido en una ubicación adicional, será un paso adicional en el proceso de implementación. Si la aplicación actualiza el contenido para la red CDN, quizás a intervalos regulares o en respuesta a un evento, debe almacenar el contenido actualizado en las ubicaciones adicionales, así como el extremo de la red CDN.

- No se puede configurar un extremo de red CDN para una aplicación implementada en el almacenamiento provisional de Azure o en el emulador local de Azure en Visual Studio. Esto afectará a las pruebas unitarias, las pruebas funcionales y las pruebas finales anteriores a la implementación. Debe permitir esto al implementar un mecanismo alternativo. Por ejemplo, podría implementar previamente el contenido en la red CDN con una aplicación o utilidad personalizada temporal y realizar pruebas durante el período en que se almacena en caché. O bien, use directivas de compilación o constantes globales para controlar de dónde la aplicación carga los recursos. Por ejemplo, durante la ejecución en modo de depuración, podría cargar recursos, como paquetes de script de cliente y otro contenido, de una carpeta local y usar la red CDN durante la ejecución en modo de lanzamiento.

- La red CDN no admite capacidades nativas de compresión. Sin embargo, proporcionará contenido que ya están comprimido, como los archivos zip y gzip. Al usar una carpeta de aplicación como el extremo de la red CDN, el servidor puede comprimir parte del contenido de forma predeterminada del mismo modo que cuando lo entrega directamente a un explorador web u otro tipo de cliente. Esto depende del valor **Accept-Encoding** que se envía desde el cliente. En Azure, el comportamiento predeterminado es comprimir automáticamente el contenido cuando el uso de la CPU es inferior al 50 %. Cambiar la configuración podría requerir el uso de una tarea de inicio para activar la compresión de resultados dinámicos en IIS si usa Servicios en la nube para hospedar la aplicación. Para más información, consulte el artículo sobre cómo [habilitar la compresión gzip con la red CDN de Azure a través de un rol web](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx).

- **Enrutamiento y control de versiones**. Es posible que tenga que usar instancias diferentes de la red CDN; por ejemplo, cuando implemente una nueva versión de la aplicación, podría usar una red CDN diferente. Si usa el almacenamiento de blobs de Azure como el origen de contenido, puede simplemente crear una cuenta de almacenamiento independiente o un contenedor independiente y elegir el extremo de la red CDN. Si usa la carpeta raíz **cdn** dentro de la aplicación como el extremo de red CDN, puede usar técnicas de reescritura de direcciones URL para dirigir las solicitudes a otra carpeta.

- No use la cadena de consulta para indicar versiones diferentes de la aplicación en los vínculos a los recursos de la red CDN porque, al dibujar el contenido desde el almacenamiento de blobs de Azure, la cadena de consulta forma parte del nombre del recurso (el nombre del blob). También puede afectar la manera en que el cliente almacena en caché los recursos.

- La implementación de nuevas versiones de contenido estático cuando se actualiza una aplicación puede ser un desafío si los recursos anteriores se almacenan en caché en la red CDN. Para más información, consulte la sección "<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Control de caché</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Control de cache**+ Considere cómo quiere administrar el almacenamiento en caché y en qué capas de la aplicación. Por ejemplo, al usar una carpeta como el origen de la red CDN, puede especificar la capacidad de almacenamiento en caché de las páginas que generan el contenido y la caducidad del contenido para todos los recursos de una carpeta específica. También puede especificar las propiedades de caché para la red CDN y para el cliente con encabezados HTTP estándar. Aunque ya debería estar administrando el almacenamiento en caché en el servidor y cliente, el uso de la red CDN le ayudará a hacer más consciente de cómo y dónde el contenido se almacena en caché.

- Para impedir que los objetos estén disponibles en la red CDN, puede eliminarlos del origen (contenedor de blob o carpeta raíz **cdn** de la aplicación), quitar o eliminar el extremo de la red CDN, o bien, en el caso del almacenamiento de blobs, crear un contenedor o blob privado. Sin embargo, los elementos se quitarán de la red CDN solo cuando expire su período de vida (TTL).

- Si no se especifica ningún período de expiración de caché (por ejemplo, cuando el contenido se carga desde el almacenamiento de blobs), se almacenarán en caché en la red CDN durante hasta 72 horas.

- En una aplicación web, puede establecer el almacenamiento en caché y la fecha de expiración de todo el contenido con el elemento **clientCache** de la sección **system.webServer/staticContent** de un archivo web.config. Puede colocar un archivo web.config en cualquier carpeta para que afecte a los archivos de esa carpeta y los archivos de todas las subcarpetas.

- Si usa una técnica dinámica, como ASP.NET, para crear el contenido de la red CDN, asegúrese de especificar la propiedad **Cache.SetExpires** en cada página. La red CDN no almacenará en caché el resultado de las páginas que usan la configuración predeterminada de almacenamiento en caché "public". Establezca el período de expiración de caché en un valor adecuado para asegurarse de que el contenido no se descarte y se vuelva a cargar desde la aplicación a intervalos muy cortos.

- **Seguridad**. La red CDN puede entregar contenido sobre HTTPS (SSL) con el certificado que proporciona la red CDN, pero también estará disponible a través de HTTP. No se puede bloquear el acceso HTTP a los elementos de la red CDN. Es posible que tenga que usar HTTPS para solicitar el contenido estático que se muestra en las páginas que se cargan a través de HTTPS (por ejemplo, un carro de la compra) para evitar las advertencias de explorador sobre contenido mixto.

- Muchos servicios de red CDN, como la red CDN de Azure, actualmente no ofrecen capacidades de control de acceso para proteger el acceso al contenido. No puede usar firmas de acceso compartido (SAS) con la red CDN.

- Si entrega scripts de cliente con la red CDN, se pueden producir problemas si estos scripts usan una llamada **XMLHttpRequest** para realizar solicitudes HTTP para otros recursos, como datos, imágenes o fuentes en un dominio diferente. Muchos exploradores web impiden el uso compartido de recursos entre orígenes (CORS), a menos que el servidor web se haya configurado para establecer los encabezados de respuesta adecuados. Para más información sobre CORS, consulte la sección "Mitigación de amenazas" en el manual <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Consideraciones de seguridad de API</span>. Puede configurar la red CDN para que admita CORS:+ Si el origen desde el que entrega de contenido es el almacenamiento de blobs de Azure, puede agregar un objeto **CorsRule** a las propiedades del servicio. La regla puede especificar los orígenes permitidos para las solicitudes CORS, los métodos permitidos, como GET, y el tiempo máximo en segundos para la regla (es decir, el período dentro del cual el cliente debe solicitar los recursos vinculados después de cargar el contenido original). Para más información, consulte [Compatibilidad con Uso compartido de recursos entre orígenes (CORS) para los Servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Si el origen desde el que se entrega de contenido es una carpeta dentro de la aplicación, como la carpeta raíz **cdn**, puede configurar reglas de salida en el archivo de configuración para establecer un encabezado **Access-Control-Allow-Origin** en todas las respuestas. Para más información sobre el uso de reglas de reescritura, consulte el artículo sobre el [módulo de reescritura de direcciones URL](http://www.iis.net/learn/extensions/url-rewrite-module). Tenga en cuenta que esta técnica no es posible cuando se usa Sitios web de Azure.

- **Dominios personalizados**+ La mayoría de las redes CDN, incluida la red CDN de Azure, permiten especificar un nombre de dominio personalizado y usar este para acceder a recursos a través de la red CDN. También puede configurar un nombre de subdominio personalizado con un registro **CNAME** en el DNS. El uso de este método puede proporcionar un nivel adicional de abstracción y control.

- Si usa un registro **CNAME**, no se puede (en el momento de la redacción de este manual) usar también SSL porque la red CDN usa su propio certificado SSL único y este no coincidirá con los nombres de dominio o subdominio personalizados.

- **Reserva de red CDN**. Debe tener en cuenta cómo la aplicación hará frente a un error o la no disponibilidad temporal de la red CDN. Es posible que las aplicaciones de cliente pueda usar copias de los recursos almacenados en caché localmente (en el cliente) durante las solicitudes anteriores. O bien, pueden usar código que detecte errores y, en su lugar, solicita recursos del origen (la carpeta de aplicación o el contenedor de blobs de Azure que contiene los recursos) si la red CDN no está disponible.

- **SEO**. Si SEO es una consideración importante en la aplicación, asegúrese de incluir un encabezado **Rel** canonical en cada página o recurso.

- Use un registro de subdominio **CNAME** y acceda a los recursos con este nombre.

- Tenga en cuenta el impacto del hecho de que la dirección IP de la red CDN podría estar en un país o región diferente de la aplicación en sí.

- Cuando se usa el almacenamiento de blobs de Azure como origen, conserve la misma estructura de archivos para los recursos de la red CDN que la de las carpetas de aplicación.


- **Registro y supervisión**. Incluya la red CDN como parte de la estrategia de supervisión de la aplicación para la detección y medición de errores o apariciones de latencia extendida.

- Habilite el registro para la red CDN e inclúyalo como parte de las operaciones diarias.

- **Implicación en el costo**. Se le cobran tanto las transferencias de datos salientes desde la red CDN y como las transacciones de almacenamiento cuando la red CDN carga los datos desde la aplicación. Debe establecer períodos de expiración de caché realistas para el contenido con el fin de garantizar la actualización, pero no tan corto que se produzca la recarga repetida del contenido de la aplicación o el almacenamiento de blobs en la red CDN. No obstante, los períodos de expiración dificultan mucho la eliminación de elementos de la red CDN porque hay que esperar a que expiren.

- Los elementos que raramente se descargan incurrirá en los dos cargos de transacción, sin proporcionar ninguna reducción significativa en la carga del servidor.



## Ejemplo de código
En esta sección se incluyen algunos ejemplos de código y técnicas para trabajar con la red CDN.


## Reescritura de direcciones URL
En el siguiente fragmento de un archivo Web.config de la raíz de una aplicación hospedada en Servicios en la nube se muestra cómo realizar la reescritura de direcciones URL al usar la red CDN. Las solicitudes desde la red CDN para contenido que almacenará en caché se redirigen a carpetas específicas dentro de la raíz de la aplicación según el tipo de recurso (por ejemplo, scripts e imágenes).

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

La adición de las reglas de reescritura realiza las siguientes redirecciones:

- La primera regla permite integrar una versión en el nombre de archivo de un recurso, que luego se omite. Por ejemplo, **Filename\_v123.jpg** se reescribe como **Filename.jpg**.

- Las siguientes cuatro reglas muestran cómo redirigir las solicitudes si no quiere almacenar los recursos en una carpeta denominada **cdn** en la raíz del rol web. Las reglas asignan las direcciones URL **cdn/Images**, **cdn/Content**, **cdn/Scripts** y **cdn/bundles** a sus respectivas carpetas raíz en el rol web. El uso de la reescritura de direcciones URL requiere realizar algunos cambios en la unión de recursos.


## Unión y minificación ##

La unión y minificación los puede controlar ASP.NET. En un proyecto MVC, las uniones se definen en **BundleConfig.cs**. Una referencia a la unión minificada del script se crea al llamar al método **Script.Render**, normalmente en el código de la clase de vista. Esta referencia contiene una cadena de consulta en la que se incluye un valor hash, que se basa en el contenido de la unión. Si cambia el contenido de la unión, también cambiará el código hash generado. De forma predeterminada, las instancias de la red CDN de Azure tienen la opción **Estado de las cadenas de consultas** deshabilitada. Para que las uniones de script actualizadas las controle correctamente la red CDN, debe habilitar la opción **Estado de las cadenas de consultas** para la instancia de la red CDN. Tenga en cuenta que la creación de la red CDN y la aplicación de la configuración pueden tardar una hora o más.


## Más información
+ [Red CDN de Azure](http://azure.microsoft.com/services/cdn/)
+ [Información general de la red de entrega de contenido (CDN) de Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Entrega de contenido desde la red CDN de Azure en su aplicación web](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](cdn-cloud-service-with-cdn.md)
+ [Prácticas recomendadas para la Red de entrega de contenido de Azure](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=August15_HO6-->