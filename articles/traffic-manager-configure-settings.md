<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Configure Traffic Manager Settings" authors="" solutions="" manager="" editor="" />

<tags ms.service="traffic-manager" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Configuración de los ajustes del Administrador de tráfico

El Administrador de tráfico de Azure le permite controlar la distribución del tráfico de usuario en los servicios hospedados de Azure.

El Administrador de tráfico funciona mediante la aplicación de un motor inteligente de directivas para las consultas de DNS en el nombre de dominio de la empresa principal. Actualice los registros de recursos DNS que posea la empresa para apuntar a los dominios del Administrador de tráfico. Las directivas del Administrador de tráfico que se adjuntan a esos dominios resuelven a continuación las consultas de DNS en el nombre de dominio de la empresa principal para las direcciones IP de los servicios hospedados de Azure contenidos en las directivas del Administrador de tráfico. Para obtener más información, consulte [Información general sobre Traffic Manager][].

## Tabla de contenido

-   [Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico][]
-   [Prueba de una directiva][]
-   [Deshabilitación temporal de directivas y servicios hospedados][]
-   [Edición de una directiva][]
-   [Equilibrio de carga de tráfico de forma equitativa en un grupo de servicios hospedados][]
-   [Creación de una directiva de conmutación por error][]
-   [Direccionamiento del tráfico entrante a servicios hospedados basados en el rendimiento de la red][]

## <span id="howto_point_company"></span></a>Redireccionamiento un dominio de Internet de la compañía a un dominio del Administrador de tráfico

Para redireccionar el nombre de dominio de la compañía a un dominio del Administrador de tráfico, edite el registro de recursos DNS en el servidor DNS con CNAME.

Por ejemplo, para indicar el dominio principal de la empresa **www.contoso.com** en un dominio del Administrador de tráfico llamado **contoso.trafficmanager.net**, actualice el registro del recurso DNS como se muestra a continuación:
`www.contoso.com IN CNAME contoso.trafficmanager.net`

Todo el tráfico que se dirige a *www.contoso.com* se redirigirá ahora a *contoso.trafficmanager.net*. Asegúrese de que usa un dominio en el que desee que se redirija todo el tráfico al Administrador de tráfico.

## <span id="howto_test"></span></a>Prueba de una directiva

La mejor forma de probar la directiva es configurar un número de clientes y, a continuación, reducir los servicios de la directiva de uno en uno. Las siguientes sugerencias le ayudarán a probar la directiva del Administrador de tráfico:

-   **Establezca el período de vida de DNS muy bajo**, de manera que los cambios se propaguen rápido (por ejemplo, 30 segundos).

-   **Conozca las direcciones IP de los servicios hospedados de Azure** en la directiva que está probando. Puede obtener esta información en el Portal de administración de Azure. Haga clic en la implementación de producción del servicio. En el panel de propiedades de la derecha, la última entrada será VIP, que es la dirección IP virtual de ese servicio hospedado.

    ![Ubicación de IP del servicio hospedado][]

    **Figura 1**: ubicación de IP del servicio hospedado

-   **Use herramientas que le permitan resolver un nombre de DNS en una dirección IP** y muestren esa dirección. Está realizando la comprobación para ver si el nombre de dominio de la empresa resuelve las direcciones IP de los servicios hospedados en la directiva. Deben resolverse de manera coherente con el método de equilibrio de carga de la directiva del Administrador de tráfico. Si usa un sistema Windows, puede usar nslookup desde una ventana de CMD (se explica a continuación). También dispone en Internet de otras herramientas disponibles públicamente que le permiten "profundizar" en la dirección IP.

-   **Compruebe la directiva del Administrador de tráfico** mediante *nslookup*.

> **Para comprobar la directiva del Administrador de tráfico mediante nslookup**

> 1.  Inicie una ventana de CMD; para ello, haga clic en **Start-Run** y escriba **CMD**.

> 1.  Para vaciar la memoria caché del solucionador de DNS, escriba `ipconfig /flushdns`.

> 1.  Escriba el comando `nslookup <your traffic manager domain>`.
>      Por ejemplo, el siguiente comando comprueba el dominio con el prefijo *myapp.contoso* `nslookup myapp.contoso.trafficmanager.net`

> > A continuación se muestra un resultado típico:

> > -   El nombre de DNS y la dirección IP del servidor DNS al que se obtiene acceso para resolver este dominio del Administrador de tráfico.

> > -   El nombre de dominio del Administrador de tráfico que especificó en la línea de comandos después de "nslookup" y la dirección IP que el dominio del Administrador de tráfico resuelve.
> >     La segunda dirección IP es la que es importante comprobar. Debe coincidir con una VIP para uno de los servicios hospedados en la directiva del Administrador de tráfico del que está realizando la prueba.

> > ![Ejemplo del comando nslookup][]

> > **Figura 2**: ejemplo del comando nslookup

-   **Use uno de los métodos de prueba adicionales que aparecen a continuación.** Seleccione el método correspondiente para el tipo de equilibrio de carga que esté probando.

### Directivas de rendimiento

Necesitará clientes de distintas partes del mundo para probar el dominio de forma eficaz. Puede crear clientes en Azure que intentarán llamar a los servicios a través del nombre de dominio de la compañía. Si su empresa es internacional, también puede iniciar sesión de forma remota en los clientes en otros lugares del país y realizar la prueba desde esos clientes.

Hay disponibles servicios dig y nslookup gratuitos basados en Web. Algunos de estos proporcionan la capacidad de comprobar la resolución de nombres DNS desde varias ubicaciones. Realice una búsqueda en nslookup para ver ejemplos. Otra opción es usar una solución de terceros como Gomez o Keynote para confirmar que las directivas distribuyen el tráfico según lo esperado.

### Directivas de conmutación por error

1.  Deje todos los servicios activados.

2.  Use un único cliente.

3.  Solicite la resolución DNS para el dominio de la compañía utilizando la utilidad de hacer ping o una similar.

4.  Asegúrese de que la dirección IP que obtiene es para el servicio principal hospedado.

5.  Reduzca el servicio principal o quite el archivo de supervisión de manera que el Administrador de tráfico piense que está desactivado.

6.  Espere al menos dos minutos.

7.  Solicite la resolución DNS.

8.  Asegúrese de que la dirección IP obtenida sea para el servicio hospedado secundario como se muestra en el orden de los servicios en el cuadro de diálogo Edit Traffic Manager Policy.

9.  Repita el proceso reduciendo el servicio secundario y, a continuación, el terciario, y así sucesivamente. Asegúrese siempre de que la resolución DNS devuelve la dirección IP del próximo servicio en la lista. Cuando estén todos los servicios desactivados, debe obtener la dirección IP del servicio principal hospedado de nuevo.

### Directivas round robin

1.  Deje todos los servicios activados.

2.  Use un único cliente.

3.  Solicite una resolución DNS para el dominio de nivel superior.

4.  Asegúrese de que la dirección IP que obtiene sea una de las que aparecen en la lista.

5.  Repita el proceso dejando que el período de vida de DNS expire de manera que reciba una nueva dirección IP. Debe ver direcciones IP devueltas para cada uno de los servicios hospedados. A continuación, se repetirá el proceso.

## <span id="howto_temp_disable"></span></a>Deshabilitación temporal de directivas y servicios hospedados

Puede deshabilitar las directivas del Administrador de tráfico creadas anteriormente de manera que no redirijan el tráfico. Cuando deshabilite la directiva del Administrador de tráfico, la información de la directiva permanecerá intacta y editable en la interfaz del Administrador de tráfico. Puede habilitar fácilmente la directiva de nuevo y el redireccionamiento se reanudará.

También puede deshabilitar los servicios individuales hospedados que forman parte de una directiva del Administrador de tráfico. Esta acción deja de forma eficaz el servicio hospedado como parte de la directiva, pero esta funciona como si el servicio hospedado no estuviera incluido en ella. Esta acción resulta muy útil para quitar temporalmente un servicio hospedado que se encuentre en el modo de mantenimiento o que se vuelva a implementar y sea inestable. Después de que el servicio hospedado se encuentre activado y en funcionamiento de nuevo, puede volver a habilitarse.

**Nota**

 La deshabilitación de un servicio hospedado no afecta al estado de implementación en Azure. Seguirá activo un servicio correcto que podrá recibir tráfico incluso cuando se deshabilite en el Administrador de tráfico. Además, la deshabilitación de un servicio hospedado en una directiva no afecta al estado en otra directiva.

### Para deshabilitar una directiva

1.  Seleccione una directiva habilitada en el árbol de la interfaz del Administrador de tráfico.

2.  Haga clic en **Disable Policy** en la barra de herramientas superior. Tenga en cuenta que el botón se atenuará si resalta una directiva que ya está deshabilitada.

### Para habilitar una directiva

1.  Seleccione una directiva deshabilitada en el árbol de la interfaz del Administrador de tráfico.

2.  Haga clic en **Enable Policy** en la barra de herramientas superior. Tenga en cuenta que el botón se atenuará si resalta una directiva que ya está deshabilitada.

### Para deshabilitar un servicio hospedado

1.  Seleccione un servicio hospedado que esté habilitado en una directiva en la interfaz del Administrador de tráfico. Tendrá que expandir una directiva para ver los servicios hospedados que contiene.

2.  Haga clic en **Disable Service Policy** en la barra de herramientas superior. Tenga en cuenta que el botón se atenuará si resalta un servicio hospedado que ya está deshabilitado.

3.  El tráfico dejará de fluir para el servicio hospedado basado en el período de vida de DNS establecido para el dominio del Administrador de tráfico que forma parte de la directiva. Para obtener más información, consulte [Información general sobre Traffic Manager][1] y vaya a la sección "Acerca de la supervisión de Traffic Manager".

### Para habilitar un servicio hospedado

1.  Seleccione un servicio hospedado que esté deshabilitado en una directiva en la interfaz del Administrador de tráfico. Tendrá que expandir una directiva para ver los servicios hospedados que contiene.

2.  Haga clic en **Enable Service Policy** en la barra de herramientas superior. Tenga en cuenta que el botón se atenuará si resalta un servicio hospedado que ya está habilitado.

3.  El tráfico comenzará a fluir al servicio hospedado de nuevo como indica la directiva.

## <span id="howto_edit_policy"></span></a>Edición de una directiva

Si tiene que desactivar temporalmente una directiva o servicios hospedados determinados de la directiva, puede deshabilitarlos temporalmente sin cambiar la directiva.

Para cambiar una directiva a un tipo diferente, siga estos pasos:

1.  **Inicie sesión en el área del Administrador de tráfico en el Portal de administración** en [http://manage.windowsazure.com](http://manage.windowsazure.com). Haga clic en **Red virtual** en la parte inferior izquierda de las páginas del portal y seleccione **Administrador de tráfico** en las opciones del panel izquierdo.

2.  **Seleccione la directiva** que desee cambiar en la pantalla del Administrador de tráfico en el Portal de administración.

3.  **Haga clic en "Configure"** en la barra de menú superior.

4.  **Realice los cambios deseados en la directiva.** Tenga en cuenta que si cambia el método de equilibrio de carga, según la opción seleccionada, el orden de los servicios hospedados en la lista **Selected hosted services** puede ser o no importante.

5.  Haga clic en **Save**.

## <span id="howto_load_balance"></span></a>Equilibrio de carga de tráfico de forma equitativa en un grupo de servicios hospedados

Un patrón común de equilibrio de carga es proporcionar un conjunto de servicios hospedados idénticos y enviar tráfico a cada uno con el método round robin. En este artículo se describen los pasos para configurar una directiva y un dominio del Administrador de tráfico para realizar este tipo de equilibrio de carga.

Para obtener más información acerca de los distintos métodos de equilibrio de carga que proporciona el Administrador de tráfico, consulte [Información general sobre Traffic Manager][] y vaya a la sección de "Acerca de los métodos de equilibrio de carga de Traffic Manager".

1.  **Implemente los servicios hospedados** en el entorno de producción. Para obtener información acerca del desarrollo y la implementación, consulte los [servicios hospedados de Azure][].

2.  **Inicie sesión en el área del Administrador de tráfico en el Portal de administración** en [http://manage.windowsazure.com](http://manage.windowsazure.com). Haga clic en **Red virtual** en la parte inferior izquierda de las páginas del portal y seleccione **Administrador de tráfico** en las opciones del panel izquierdo.

3.  **Seleccione Policies y haga clic en "Create".** Seleccione la carpeta **Policies** en el árbol de navegación de la izquierda para habilitar **Create** en la barra de herramientas superior. Seleccione **Create**. Aparecerá el cuadro de diálogo **Create Traffic Manager policy**.

    ![Botón de creación de directivas][]

    **Figura 1**: botón de creación de directivas

4.  **Seleccione una suscripción.** Las directivas y los dominios se asocian a una única suscripción.

5.  **Seleccione el método de equilibrio de carga round robin.** Para obtener más información acerca de los distintos métodos de equilibrio de carga que proporciona el Administrador de tráfico, consulte [Información general sobre Traffic Manager][] y vaya a la sección de "Acerca de los métodos de equilibrio de carga de Traffic Manager".

6.  **Busque los servicios hospedados y agréguelos a la directiva.** Use el cuadro de filtro para buscar los servicios hospedados que contienen la cadena que escriba en la casilla. Desactive la casilla para mostrar todos los servicios hospedados en producción para la suscripción que seleccionó en el paso 4. Use los botones de flecha para agregarlos a la directiva. El orden en la casilla **Selected DNS names** no influye en este método de equilibrio de carga.

7.  **Configure la supervisión.** La supervisión asegura que los servicios hospedados que están desconectados no son tráfico enviado. Debe especificar una configuración de nombre de archivo y ruta de acceso determinada.
    Para obtener más información, consulte [Información general sobre Traffic Manager][1] y vaya a la sección "Acerca de la supervisión de Traffic Manager".

8.  **Ponga un nombre al dominio del Administrador de tráfico.** Proporcione al dominio un nombre exclusivo. Solo puede especificar el prefijo para el dominio. Deje el período de vida de DNS en el tiempo predeterminado.
    Para obtener más información sobre el efecto de esta configuración, consulte [Introducción a Traffic Manager][1] y vaya a "Procedimientos recomendados".

    El cuadro de diálogo **Create Traffic Manager policy** debe ser similar al siguiente ejemplo.

    ![Cuadro de diálogo para el método de equilibrio de carga round robin][]

    **Figura 2**: cuadro de diálogo para el método de equilibrio de carga round robin

9.  **Realice la prueba de la directiva y el dominio del Administrador de tráfico.** Para conocer las instrucciones, consulte [Prueba de una directiva del Administrador de tráfico de Azure][Prueba de una directiva].

10. **Redireccione el servidor DNS al dominio del Administrador de tráfico.** Una vez que el dominio del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el dominio de la empresa al dominio del Administrador de tráfico.
    Por ejemplo, el siguiente comando redirige todo el tráfico que va de **www.contoso.com** al dominio del Administrador de tráfico **contoso.trafficmanager.net**:
    `www.contoso.com IN CNAME contoso.trafficmanager.net`
    Para obtener más información, consulte [Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico de Azure][Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico].

## <span id="howto_create_failover"></span></a>Creación de una directiva de conmutación por error

En ocasiones, una organización desea proporcionar confiabilidad en sus servicios. Puede hacerlo mediante servicios de copia de seguridad en caso de que el servicio principal esté desactivado. Un patrón común para la conmutación por error del servicio es proporcionar un conjunto de servicios hospedados idénticos y enviar tráfico a un servicio principal con una lista de una o varias copias de seguridad. En este artículo se describen los pasos para configurar una directiva del Administrador de tráfico para realizar este tipo de copia de seguridad de conmutación por error.

Para obtener más información acerca de los distintos métodos de equilibrio de carga que proporciona el Administrador de tráfico, consulte [Información general sobre Traffic Manager][] y vaya a la sección de "Acerca de los métodos de equilibrio de carga de Traffic Manager".

1.  **Implemente los servicios hospedados** en el entorno de producción. Para obtener información acerca del desarrollo y la implementación de servicios hospedados, consulte los [servicios hospedados de Azure][].

2.  **Inicie sesión en el área del Administrador de tráfico en el Portal de administración** en [http://manage.windowsazure.com](http://manage.windowsazure.com). Haga clic en **Red virtual** en la parte inferior izquierda de las páginas del portal y seleccione **Administrador de tráfico** en las opciones del panel izquierdo.

3.  **Seleccione Policies y haga clic en "Create".** Seleccione la carpeta **Policies** en el árbol de navegación de la izquierda para habilitar **Create** en la barra de herramientas superior. Seleccione **Create**. Aparecerá el cuadro de diálogo **Create Traffic Manager policy**.

    ![Botón de creación de directivas][]

    **Figura 1**: botón de creación de directivas

4.  **Seleccione una suscripción.** Las directivas y los dominios se asocian a una única suscripción.

5.  **Seleccione el método de equilibrio de carga de directiva por conmutación por error.** Para obtener más información acerca de los distintos métodos de equilibrio de carga que proporciona el Administrador de tráfico, consulte [Información general sobre Traffic Manager][] y vaya a la sección de "Acerca de los métodos de equilibrio de carga de Traffic Manager".

6.  **Busque los servicios hospedados y agréguelos a la directiva.** Use el cuadro de filtro para buscar los servicios hospedados que contienen la cadena que escriba en la casilla. Desactive la casilla para mostrar todos los servicios hospedados en producción para la suscripción que seleccionó en el paso 4. Use los botones de flecha para agregarlos a la directiva. Cuando seleccione el método de equilibrio de carga por **conmutación por error**, el orden de los servicios seleccionados es importante. El servicio hospedado principal aparece en la parte superior. Use las flechas arriba y abajo para cambiar el orden según sea necesario.

7.  La supervisión asegura que los servicios hospedados que están desconectados no son tráfico enviado. El uso de una directiva de conmutación por error sin configurar la supervisión no es útil, puesto que el tráfico se enviará al servicio principal hospedado incluso si ese servicio hospedado se muestra como desconectado. Para supervisar los servicios hospedados, debe especificar un nombre de archivo y ruta de acceso determinados.
    Para obtener más información, consulte [Información general sobre Traffic Manager][1] y vaya a la sección "Acerca de la supervisión de Traffic Manager".

8.  **Ponga un nombre al dominio del Administrador de tráfico.** Proporcione al dominio un nombre exclusivo. Solo puede especificar el prefijo para el dominio. Deje el **período de vida de DNS (TTL)** en el tiempo predeterminado.
    Para obtener más información acerca del efecto de esta configuración, consulte [Introdución a Traffic Manager][1] y vaya a "Procedimientos recomendados".

    El cuadro de diálogo **Create Traffic Manager policy** debe ser similar al siguiente ejemplo.

    ![Cuadro de diálogo para el método de equilibrio de carga de conmutación por error][]

    **Figura 2**: cuadro de diálogo para el método de equilibrio de carga de conmutación por error

9.  **Realice la prueba de la directiva y el dominio del Administrador de tráfico.** Para obtener más información, consulte [Prueba de una directiva del Administrador de tráfico de Azure][Prueba de una directiva].

10. **Redireccione el servidor DNS al dominio del Administrador de tráfico.** Una vez que el dominio del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el dominio de la empresa al dominio del Administrador de tráfico.
    Para obtener más información, consulte [Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico][].
    Por ejemplo, el siguiente comando redirige todo el tráfico que va de **www.contoso.com** al dominio del Administrador de tráfico **contoso.trafficmanager.net**
    `www.contoso.com IN CNAME contoso.trafficmanager.net`

## <span id="howto_direct"></span></a>Direccionamiento del tráfico entrante a servicios hospedados basados en el rendimiento de la red

Para cargar el servicio hospedado del equilibrio de carga que se encuentra en distintos centros de datos en todo el mundo, puede dirigir el tráfico entrante al servicio hospedado más próximo. Aunque "más cercano" puede corresponderse directamente con la distancia geográfica, también hace referencia a la ubicación con la latencia más baja para atender la solicitud. El método de equilibrio de carga de rendimiento le permitirá realizar la distribución según la ubicación y la latencia, pero no puede tener en cuenta los cambios en tiempo real de la cuenta en la carga o configuración de red. Para obtener más información acerca de los distintos métodos de equilibrio de carga que proporciona el Administrador de tráfico, consulte [Información general sobre Traffic Manager][] y vaya a la sección de "Acerca de los métodos de equilibrio de carga de Traffic Manager".

Los siguientes pasos le guían a lo largo del proceso:

1.  **Implemente los servicios hospedados** en el entorno de producción. Para obtener más información, consulte [Creación de un servicio hospedado de Azure][].
    Consulte también "Procedimientos recomendados" en [Introducción a Traffic Manager][1].

2.  **Inicie sesión en el área del Administrador de tráfico en el Portal de administración** en [http://manage.windowsazure.com](http://manage.windowsazure.com). Haga clic en **Red virtual** en la parte inferior izquierda de las páginas del portal y seleccione **Administrador de tráfico** en las opciones del panel izquierdo.

3.  **Seleccione Policies y haga clic en "Create".** Seleccione la carpeta **Policies** en el árbol de navegación de la izquierda para habilitar **Create** en la barra de herramientas superior. Seleccione **Create**. Aparecerá el cuadro de diálogo **Create Traffic Manager policy**.

    ![Botón de creación de directivas][]

    **Figura 1**: botón de creación de directivas

4.  **Seleccione una suscripción.** Las directivas y los dominios se asocian a una única suscripción.

5.  **Seleccione el método de equilibrio de carga de rendimiento.** Para obtener más información acerca de los distintos métodos de equilibrio de carga que proporciona el Administrador de tráfico, consulte [Información general sobre Traffic Manager][] y vaya a la sección de "Acerca de los métodos de equilibrio de carga de Traffic Manager".

6.  **Busque los servicios hospedados y agréguelos a la directiva.** Use el cuadro de filtro para buscar los servicios hospedados que contienen la cadena que escriba en la casilla. Desactive la casilla para mostrar todos los servicios hospedados en producción para la suscripción que seleccionó en el paso 4. Use los botones de flecha para agregarlos a la directiva. El orden en la casilla **Selected DNS names** no influye en este método de equilibrio de carga.

7.  **Configure la supervisión.** La supervisión asegura que los servicios hospedados que están desconectados no son tráfico enviado. Debe especificar una configuración de nombre de archivo y ruta de acceso determinada.
    Para obtener más información, consulte [Información general sobre Traffic Manager][1] y vaya a la sección "Acerca de la supervisión de Traffic Manager".

8.  **Ponga un nombre al dominio del Administrador de tráfico.** Proporcione al dominio un nombre exclusivo. Solo puede especificar el prefijo para el dominio. Deje el **período de vida de DNS (TTL)** en el tiempo predeterminado.
    Para obtener más información acerca del efecto de esta configuración, consulte [Introdución a Traffic Manager][1] y vaya a "Procedimientos recomendados".

    El cuadro de diálogo **Create Traffic Manager policy** debe tener un aspecto similar al del siguiente ejemplo.

    ![Cuadro de diálogo para el método de equilibrio de carga de rendimiento][]

    **Figura 2**: cuadro de diálogo para el método de equilibrio de carga de rendimiento

9.  **Realice la prueba de la directiva y el dominio del Administrador de tráfico.** Para obtener más información acerca de la prueba, consulte [Prueba de una directiva del Administrador de tráfico de Azure][Prueba de una directiva].

10. **Redireccione el servidor DNS al dominio del Administrador de tráfico.** Una vez que el dominio del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el dominio de la empresa al dominio del Administrador de tráfico.
    Por ejemplo, el siguiente comando redirige todo el tráfico que va de **www.contoso.com** al dominio del Administrador de tráfico **contoso.trafficmanager.net**.
    `www.contoso.com IN CNAME contoso.trafficmanager.net`
    Para obtener más información, consulte [Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico de Azure][Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico].

  [Información general sobre Traffic Manager]: http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx
  [Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico]: #howto_point_company
  [Prueba de una directiva]: #howto_test
  [Deshabilitación temporal de directivas y servicios hospedados]: #howto_temp_disable
  [Edición de una directiva]: #howto_edit_policy
  [Equilibrio de carga de tráfico de forma equitativa en un grupo de servicios hospedados]: #howto_load_balance
  [Creación de una directiva de conmutación por error]: #howto_create_failover
  [Direccionamiento del tráfico entrante a servicios hospedados basados en el rendimiento de la red]: #howto_direct
  [Ubicación de IP del servicio hospedado]: ./media/traffic-manager-configure-settings/hosted_service_IP_location.png
  [Ejemplo del comando nslookup]: ./media/traffic-manager-configure-settings/nslookup_command_example.png
  [1]: http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring
    [servicios hospedados de Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Botón de creación de directivas]: ./media/traffic-manager-configure-settings/Create_button_for_policies.png
  [Cuadro de diálogo para el método de equilibrio de carga round robin]: ./media/traffic-manager-configure-settings/Dialog_box_for_Round_Robin_load_balancing_method.png
  [Cuadro de diálogo para el método de equilibrio de carga de conmutación por error]: ./media/traffic-manager-configure-settings/Dialog_box_for_Failover_load_balancing_method.png
  [Creación de un servicio hospedado de Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg432967.aspx
  [Cuadro de diálogo para el método de equilibrio de carga de rendimiento]: ./media/traffic-manager-configure-settings/Dialog_box_for_Performance_load_balancing_method.png
