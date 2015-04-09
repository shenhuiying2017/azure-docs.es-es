<properties 
   pageTitle="Supervisión del Administrador de tráfico"
   description="Este artículo le ayudará a comprender y configurar la supervisión del Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Acerca de la supervisión del Administrador de tráfico

El Administrador de tráfico de Azure supervisa los extremos, incluidos los servicios en la nube y los sitios web, para garantizar que están disponibles. Para que la supervisión funcione correctamente, debe configurarla del mismo modo para todos los extremos que especifique en el perfil del Administrador de tráfico. Después de configurar la supervisión, el Administrador de tráfico mostrará el estado de los extremos y el perfil en el Portal de administración. Puede configurar opciones de supervisión en el Portal de administración, en la página Configurar del perfil del Administrador de tráfico. Puede especificar las siguientes opciones:

- **Protocolo**: elija HTTP o HTTPS. Es importante que tenga en cuenta que la supervisión HTTPS no comprueba si el certificado SSL es válido, solo comprueba que está presente.

- **Puerto**: elija el puerto que se usa para la solicitud. Los puertos HTTP estándar y HTTPS se encuentran entre las opciones.

- **Ruta de acceso relativa y nombre de archivo**: asigne la ruta y el nombre del archivo al que el sistema de supervisión intentará tener acceso. Tenga en cuenta que una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado). 

## Acerca de la supervisión del estado de mantenimiento

El Administrador de tráfico de Azure muestra el estado del servicio de perfiles y de extremos en el Portal de administración. La columna de estado del perfil y el extremo muestran el estado más reciente de supervisión. Puede usar este estado para comprender el estado del perfil de acuerdo con la configuración de supervisión del Administrador de tráfico. Cuando el estado del perfil es correcto, las consultas DNS se distribuirán a los servicios en función de la configuración de equilibrio de carga (round robin, rendimiento o conmutación por error) del perfil. Cuando el sistema de supervisión de Administrador de tráfico detecta un cambio en el estado de supervisión, actualiza la entrada de estado en el Portal de administración. Se puede tardar hasta cinco minutos en actualizar el cambio de estado.

### Estado de supervisión de extremos

El estado de supervisión de extremos de la tabla siguiente es el resultado de una combinación de los resultados del sondeo de mantenimiento de los extremos y de las configuraciones del perfil y los extremos.

|Estado del perfil|Estado de extremos|Estado de supervisión de extremos (API y Portal)|Notas|
|---|---|---|---|
|Deshabilitado|Habilitado|Inactivo|Los perfiles deshabilitados no se supervisan. Sin embargo, todavía se puede administrar el estado de los extremos en perfiles deshabilitados.|
|&lt;cualquier&gt;|Deshabilitado|Deshabilitado|Los perfiles deshabilitados no se supervisan. Sin embargo, todavía se puede administrar el estado de los extremos en perfiles deshabilitados.|
|Habilitado|Habilitado|En línea|El extremo se supervisa y su estado es correcto
|Habilitado|Habilitado|Degradado|El extremo se supervisa y su estado es incorrecto
|Habilitado|Habilitado|CheckingEndpoint|El extremo se supervisa pero los resultados del primer sondeo no se han recibido todavía. Este estado es temporal cuando se acaba de agregar un nuevo extremo al perfil o se acaba de habilitar un extremo o perfil.|
|Habilitado|Habilitado|Detenido|El servicio en la nube o sitio web subyacente no se está ejecutando.|

### Estado de supervisión de perfiles

El estado de supervisión de perfiles de la tabla siguiente es el resultado de la combinación del estado de supervisión del extremo y el estado del perfil configurado.

|Estado del perfil (según se haya configurado)|Estado de supervisión de extremos|Estado de supervisión de perfiles (API y Portal)|Notas|
|---|---|---|---|
|Deshabilitado|&lt;cualquiera&gt; o un perfil sin extremos definidos.|Deshabilitado|Los extremos no se supervisan.|
|Habilitado|El estado de al menos un extremo es "Degradado".|Degradado|Esta marca indica que es necesaria la acción del cliente.|
|Habilitado|]El estado de al menos un extremo es "En línea". No hay extremos en estado "Degradado".|En línea|El servicio acepta tráfico y no es necesaria la acción del cliente.|
|Habilitado|]El estado de al menos un extremo es "CheckingEndpoint". No hay extremos en estado "En línea" o "Degradado".|CheckingEndpoints|Estado de transición. Esto suele ocurrir cuando se acaba de habilitar un perfil y se sondea el mantenimiento del extremo. |
|Habilitado|El estado de todos los extremos definidos en el perfil es "Deshabilitado" o "Detenido", o el perfil no tiene ningún extremo definido.|Inactivo|No hay ningún extremo activo, pero el perfil sigue habilitado.|

## Cómo funciona la supervisión

A continuación se muestra una escala de tiempo de ejemplo que ilustra el proceso de supervisión con un solo servicio en la nube . Este escenario muestra lo siguiente:

- El servicio en la nube está disponible y SOLO recibe tráfico a través este perfil del Administrador de tráfico.

- El servicio en la nube deja de estar disponible.

- El servicio en la nube sigue sin estar disponible durante un tiempo mucho mayor que el Período de vida (TTL) de DNS.

- El servicio en la nube vuelve a estar disponible.

- El servicio en la nube reanuda la recepción de tráfico SOLO a través de este perfil del Administrador de tráfico.

![Secuencia de supervisión del Administrador de tráfico](./media/traffic-manager-monitoring/IC697947.jpg)

**Figura 1**: Ejemplo de secuencia de supervisión. Los números del diagrama se corresponden con la explicación numerada que se ofrece a continuación.

1. **GET** -: el sistema de supervisión del Administrador de tráfico realiza una operación GET en la ruta y el archivo que se especifiquen en la configuración de supervisión.
2. **200 Correcto** -: el sistema de supervisión espera un mensaje HTTP 200 Correcto en 10 segundos. Cuando recibe esta respuesta, supone que el servicio en la nube está disponible. 
>[AZURE.NOTE]: el Administrador de tráfico solo considera que un extremo está En línea si el mensaje devuelto es 200 Correcto. Si se recibe una respuesta distinta a 200, supondrá que el extremo no está disponible y contará la comprobación como incorrecta.

3. **30 segundos entre comprobaciones** -: Esta comprobación se realizará cada 30 segundos.
4. **Servicio en la nube no disponible** -: el servicio en la nube deja de estar disponible. El Administrador de tráfico no lo sabrá hasta la siguiente comprobación de supervisión.
5. **Intentos de acceso al archivo de supervisión (cuatro intentos)** - : el sistema de supervisión realiza una operación GET, pero no recibe respuesta en 10 segundos o menos. A continuación realiza tres intentos más a intervalos de 30 segundos. Esto significa que, como máximo, se tarda aproximadamente 1,5 minutos para que el sistema de supervisión detecte que un servicio deja de estar disponible. Si uno de los intentos anteriores es correcto, se restablece el número de intentos. Aunque no se muestra en el diagrama, si los mensajes 200 Correcto vuelven más de 10 segundos después de la operación GET, el sistema de supervisión seguirá contando esta comprobación como incorrecta.
6. **Marcado como degradado** -: después del cuarto error seguido, el sistema de supervisión marcará el servicio en la nube no disponible como degradado
7. **El tráfico hacia el servicio en la nube disminuye** - : es posible que el tráfico hacia el servicio en la nube no disponible siga fluyendo. Los clientes experimentarán errores porque el servicio no está disponible. Los clientes y servidores DNS secundarios han almacenado en la memoria caché el registro DNS para la dirección IP del servicio en la nube no disponible. Seguirán hasta resolver el nombre DNS del dominio de la empresa para la dirección IP del servicio. Además, los servidores DNS secundarios pueden seguir distribuyendo la información de DNS del servicio no disponible. A medida que los clientes y servidores DNS secundarios se actualicen, se ralentizará el tráfico hacia la dirección IP del servicio no disponible. El sistema de supervisión seguirá realizando comprobaciones a intervalos de 30 segundos. En este ejemplo, el servicio no responde y permanece no disponible.
8. **El tráfico hacia el servicio en la nube se detiene** -: en este momento, la mayoría de los clientes y servidores DNS deben actualizarse y el tráfico hacia el servicio no disponible se detiene. La máxima cantidad de tiempo antes de que el tráfico se detenga por completo depende del tiempo TTL. El TTL de DNS predeterminado es de 300 segundos (cinco minutos). Con este valor, los clientes dejan de usar el servicio después de cinco minutos. El sistema de supervisión sigue realizando comprobaciones a intervalos de 30 segundos y el servicio en la nube no responde.
9. **El servicio en la nube vuelve a estar en línea y recibe tráfico** -: el servicio pasa a estar disponible, pero el Administrador de tráfico no lo sabe hasta que el sistema de supervisión realiza una comprobación.
10. **El tráfico hacia el servicio se reanuda** -: el Administrador de tráfico envía una operación GET y recibe un mensaje 200 Correcto en 10 segundos. Luego comienza a distribuir el nombre DNS del servicio en la nube a los servidores DNS a medida que solicitan actualizaciones. En consecuencia, el tráfico comienza a fluir hacia el servicio una vez más.

## Estado de los extremos principal y secundarios de perfiles anidados

En la tabla siguiente se describe el comportamiento de la supervisión del Administrador de tráfico para los perfiles principales y secundarios de un perfil anidado y la configuración de minChildEndpoints. Para obtener más información, consulte [Información general sobre el Administrador de tráfico](traffic-manager-overview.md).

|Estado de supervisión de perfiles secundarios|Estado de supervisión del extremo principal|Notas|
|---|---|---|
|Deshabilitado|Esto se debe a que el usuario ha deshabilitado el perfil.|Detenido|El estado del extremo principal es Detenido, no Deshabilitado. El estado Deshabilitado se reserva para indicar que el usuario ha deshabilitado el extremo en el perfil principal.|
|Degradado|Al menos un extremo secundario se encuentra en estado Degradado.|Estado En línea, si el número de extremos En línea en el perfil secundario es como mínimo el valor de minChildEndpoints. Estado CheckingEndpoint, si el número de extremos en estado En línea, más en estado CheckingEndpoint del perfil secundario es como mínimo el valor de minChildEndpoints. De lo contrario, se encuentra en estado Degradado.|El tráfico se enruta hacia un extremo de estado CheckingEndpoint. Si minChildEndpoints se establece en un valor demasiado alto, el extremo principal siempre estará degradado.|
|En línea|Como mínimo, hay un elemento secundario en estado En línea y ninguno se encuentra en estado Degradado.|Igual que el anterior.||
|CheckingEndpoints|Como mínimo, un elemento se encuentra en estado 'CheckingEndpoint'; ninguno está 'En línea' ni 'Degradado'|Igual que el anterior.||
|Inactivo|Todos los extremos se encuentran en estado Deshabilitado o Detenido, o bien, se trata de un perfil sin ningún extremo|Detenido||

## Para configurar la supervisión para una ruta de acceso y un nombre de archivo específicos

1. Cree un archivo con el mismo nombre en cada extremo que piense incluir en el perfil.
2. Para cada extremo, use un explorador web para probar el acceso al archivo. La dirección URL consta del nombre de dominio del extremo específico (el servicio en la nube o el sitio web), la ruta de acceso al archivo y el nombre del archivo. 
3. En el Portal de administración, en **Configuración de supervisión**, en el campo **Ruta de acceso relativa y nombre de archivo**, especifique la ruta de acceso y el nombre del archivo.
4. Cuando haya terminado de realizar los cambios de configuración, haga clic en **Guardar** en la parte inferior de la página.

## Otras referencias

[Información general sobre el Administrador de tráfico](traffic-manager-overview.md)

[Acerca de los métodos de equilibrio de carga del Administrador de tráfico](traffic-manager-load-balancing-methods.md)

[Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Entrada de blog: Solución de problemas de estado degradado del Administrador de tráfico](http://go.microsoft.com/fwlink/p/?LinkId=328689)

<!--HONumber=49-->