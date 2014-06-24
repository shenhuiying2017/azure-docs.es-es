<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />
# Administración de la disponibilidad de las máquinas virtuales

Para ayudar a asegurar la disponibilidad de su aplicación, recomendamos
encarecidamente el uso de varias máquinas virtuales que proporcionen
redundancia. Para conseguirlo, configure más de una máquina virtual para
que realice la misma función o rol. Esta redundancia es necesaria para
recibir un nivel de servicio garantizado. Asegúrese también de
considerar las dependencias. Por ejemplo, si la máquina virtual "IIS1"
depende de los servicios ofrecidos por la máquina virtual "SQL1," la
redundancia de la máquina virtual "SQL1" se proporciona mediante la
máquina virtual "SQL2" para ayudar a evitar interrupciones del servicio.
Para obtener más información sobre los contratos de nivel de servicio,
consulte la sección "Servicios en la nube, máquinas virtuales y red
virtual" en [Contratos de nivel de servicio][1].

Este método puede ayudar a asegurar que su aplicación se mantenga
disponible durante los errores de la red local, los errores de hardware
del disco local y todo tiempo de inactividad que la plataforma pueda
requerir.

Puede administrar la disponibilidad de una aplicación que usa varias
máquinas virtuales agregando las máquinas virtuales a un conjunto de
disponibilidad. Los conjuntos de disponibilidad están directamente
relacionados con los dominios de errores y los dominios de
actualización. Un dominio de error en Azure se define al evitar puntos
simples de error, como el conmutador de red o la unidad de potencia de
un bastidor de servidores. De hecho, un dominio de error equivale casi
por completo a un bastidor de servidores físicos. Cuando hay varias
máquinas virtuales conectadas en un servicio en la nube, un conjunto de
disponibilidad coloca las máquinas virtuales en diferentes dominios de
errores. El siguiente diagrama muestra dos conjuntos de disponibilidad
con dos máquinas virtuales en cada conjunto.

![Dominios de
actualización](./media/manage-vm-availability/UpdateDomains.png)

Azure actualiza periódicamente el sistema operativo que hospeda las
instancias de una aplicación. Una máquina virtual se apaga cuando se
aplica una actualización. Se usa un dominio de actualización para
asegurar que no se actualicen al mismo tiempo todas las instancias de
máquinas virtuales. Cuando se asignan varias máquinas virtuales a un
conjunto de disponibilidad, Azure se asegura de que las máquinas se
asignen a diferentes dominios de actualización. El diagrama anterior
muestra dos máquinas virtuales que ejecutan los Internet Information
Services (IIS) en dominios de actualización separados y dos máquinas
virtuales que ejecutan SQL Server también en dominios de actualización
separados.

Debe usar una combinación de conjuntos de disponibilidad y extremos de
equilibrio de carga para ayudar a asegurar de que su aplicación está
siempre disponible y ejecutándose de forma eficaz. Para obtener más
información sobre el uso de extremos equilibrados por carga, consulte
[Equilibrio de carga de máquinas
virtuales](../load-balance-virtual-machines).

Esta tarea incluye los siguientes pasos:

* [Paso 1: Crear una máquina virtual y un conjunto de
  disponibilidad](#createset)
* [Paso 2: Agregar una máquina virtual al servicio en la nube y
  asignación al conjunto de disponibilidad durante el proceso de
  creación](#addmachine)
* [Paso 3: (Opcional) Crear un conjunto de disponibilidad para máquinas
  virtuales que se crearon con anterioridad](#previousmachine)
* [Paso 4: (Opcional) Agregar una máquina virtual creada anteriormente a
  un conjunto de disponibilidad](#existingset)
## <a  id="createset"> </a>Paso 1: Crear una máquina virtual y un conjunto de disponibilidad

Para crear un conjunto de disponibilidad que contenga máquinas
virtuales, puede crear la primera máquina virtual y el conjunto de
disponibilidad al mismo tiempo y, a continuación, puede agregar máquinas
virtuales al conjunto de disponibilidad a medida que las crea. Puede
también crear máquinas virtuales, crear un conjunto de disponibilidad y,
a continuación, agregar todas las máquinas al conjunto.

**Para crear una máquina virtual y un conjunto de disponibilidad**

1.  Si no lo ha hecho todavía, inicie sesión en el Portal de
    administración de Azure.

2.  En la barra de comandos, haga clic en **New**.
    
    ![Crear una máquina
    virtual](./media/manage-vm-availability/Create.png)

3.  Haga clic en **Máquina virtual** y, a continuación, haga clic en
    **From Gallery**.
    
    Aparece el cuadro de diálogo **Select the virtual machine operating
    system**.

4.  En **Platform Images**, seleccione una imagen y, a continuación,
    haga clic en la flecha para continuar.
    
    Aparece el cuadro de diálogo **Virtual machine configuration**.

5.  En **Virtual Machine Name**, escriba el nombre que desea usar para
    la máquina virtual.

6.  En **New User Name**, escriba un nombre para la cuenta
    administrativa que desea usar para administrar el servidor.

7.  En **New Password**, escriba una contraseña segura para la cuenta
    administrativa. En **Confirm Password**, vuelva a escribir la
    contraseña que escribió anteriormente.

8.  En **Size**, seleccione el tamaño que desea usar para la máquina
    virtual. El tamaño que seleccione depende de la cantidad de núcleos
    que se necesitan para su aplicación.

9.  Haga clic en la flecha para continuar.
    
    Aparece el cuadro de diálogo **Virtual machine mode**.

10. Seleccione **Stand-alone virtual machine**.

11. En **DNS Name**, escriba un nombre para el servicio de nube que se
    crea para la máquina. El nombre puede tener desde 3 hasta 24 letras
    minúscula y números.

12. En **Cuenta de almacenamiento**, seleccione una cuenta de
    almacenamiento donde se almacena el archivo .vhd, o puede
    seleccionar que se cree una cuenta de almacenamiento
    automáticamente. Solo se crea una cuenta de almacenamiento por
    región de manera automática. Todas las demás máquinas virtuales que
    crea con esta configuración se ubican en esta cuenta de
    almacenamiento. Tiene un límite de 20 cuentas de almacenamiento.

13. En **Region/Affinity Group/Virtual Network**, seleccione la región,
    el grupo de afinidad o la red virtual que desea que contenga la
    máquina virtual. Para obtener más información sobre los grupos de
    afinidad, consulte [Acerca de los grupos de afinidad para la red
    virtual][2].

14. Haga clic en la flecha para continuar.
    
    Aparece el cuadro de diálogo **Virtual machine options**.

15. En **Conjunto de disponibilidad**, seleccione **Create availability
    set**.

16. En **Availability Set Name**, escriba el nombre para el conjunto de
    disponibilidad.

17. Haga clic en la fecha para crear la máquina virtual y el conjunto de
    disponibilidad.
    
    En el panel de la máquina virtual nueva, puede hacer clic en
    **Configure** y ver que la máquina virtual es un miembro del
    conjunto de disponibilidad nuevo.
## <a  id="addmachine"> </a>Paso 2: Agregar una máquina virtual al servicio en la nube y asignación al conjunto de disponibilidad durante el proceso de creación

El paso anterior le mostró cómo crear una máquina virtual y el conjunto
de disponibilidad al mismo tiempo. Ahora puede crear una máquina virtual
nueva, conectarla al servicio en la nube de la primera máquina virtual
y, a continuación, agregarla al conjunto de disponibilidad que creó
anteriormente.

**Para conectar una máquina virtual nueva y agregarla al conjunto de
disponibilidad**

1.  Si no lo ha hecho todavía, inicie sesión en el Portal de
    administración de Azure.

2.  En la barra de comandos, haga clic en **New**.
    
    ![Crear una máquina
    virtual](./media/manage-vm-availability/Create.png)

3.  Haga clic en **Máquina virtual** y, a continuación, haga clic en
    **From Gallery**.
    
    ![Crear desde la
    galería](./media/manage-vm-availability/CreateNew.png)
    
    Aparece el cuadro de diálogo **Select the virtual machine operating
    system**. Ahora puede seleccionar una imagen de la Galería de
    imágenes.

4.  Haga clic en **Platform Images**, seleccione la imagen de plataforma
    que desea usar y, a continuación, haga clic en la flecha para
    continuar.
    
    Aparece el cuadro de diálogo **Virtual machine configuration**.

5.  En **Virtual Machine Name**, escriba el nombre que desea usar para
    la máquina virtual.

6.  6\. En **New User Name**, escriba un nombre para la cuenta
    administrativa que desea usar para administrar el servidor.

7.  En **New Password**, escriba una contraseña segura para la cuenta
    administrativa en la máquina virtual. En **Confirm Password**,
    vuelva a escribir la contraseña.

8.  En **Size**, seleccione el tamaño que desea usar para la máquina
    virtual. El tamaño que seleccione depende de la cantidad de núcleos
    que se necesitan para su aplicación.

9.  Puede proteger la máquina con una clave SSH en una máquina virtual
    que ejecute el sistema operativo Linux.

10. Haga clic en la flecha para continuar.
    
    Aparece el cuadro de diálogo **Virtual machine mode**.

11. Seleccione **Connect to existing Virtual Machine** para crear una
    máquina virtual nueva que se conectará con la primera máquina
    virtual del conjunto de disponibilidad. Seleccione el servicio en la
    nube que contiene la máquina virtual en el conjunto de
    disponibilidad.

12. En **Cuenta de almacenamiento**, seleccione una cuenta de
    almacenamiento donde se almacena el archivo VHD.

13. En **Region/Affinity Group/Virtual Network**, seleccione la región
    que desea que contenga la máquina virtual.

14. Haga clic en la flecha para continuar.
    
    Aparece el cuadro de diálogo **Virtual machine options**.

15. Seleccione el conjunto de disponibilidad que se creó cuando creó la
    primera máquina virtual.

16. Haga clic en la marca de verificación para crear la máquina virtual
    conectada y agregarla al conjunto de disponibilidad.
    
    En el panel de la máquina virtual nueva, puede hacer clic en
    **Configure** y ver que la máquina virtual es un miembro del
    conjunto de disponibilidad nuevo.
## <a  id="previousmachine"> </a>Paso 3: (Opcional) Crear un conjunto de disponibilidad para máquinas virtuales que se crearon con anterioridad

Puede crear un conjunto de disponibilidad y agregarle una máquina
virtual después de crear la máquina. Después de crear una máquina
virtual, puede configurar el tamaño de la máquina y su pertenencia a un
miembro de un conjunto de disponibilidad.

**Para crear un conjunto de disponibilidad nuevo**

1.  Si no lo ha hecho todavía, inicie sesión en el Portal de
    administración de Azure.

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la
    máquina virtual que desea configurar.

3.  Haga clic en **Configure**.

4.  En la sección **Conjunto de disponibilidad**, seleccione **Create
    Availability Set** y, a continuación, escriba un nombre para el
    conjunto.

5.  Haga clic en **Save**.
    
    **Nota:** esto puede tener como resultado que la máquina virtual se
    reinicie para finalizar la pertenencia al conjunto de
    disponibilidad.
## <a  id="existingset"> </a>Paso 4: (Opcional) Agregar una máquina virtual creada anteriormente a un conjunto de disponibilidad

Puede agregar fácilmente una máquina virtual existente a un conjunto de
disponibilidad que se creó anteriormente. Para agregar una máquina
virtual a un conjunto de disponibilidad, debe estar conectada al mismo
servicio en la nube que las demás máquinas virtuales del conjunto. Para
obtener más información sobre la conexión de máquinas virtuales,
consulte [Conexión de máquinas virtuales en un Servicio en la
nube](../virtual-machines-connect-cloud-service).

**Para agregar una máquina virtual existente a un conjunto de
disponibilidad**

1.  Si no lo ha hecho todavía, inicie sesión en el Portal de
    administración de Azure.

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la
    máquina virtual que desea agregar al conjunto de disponibilidad.

3.  Haga clic en **Configure**.

4.  En la sección **Conjunto de disponibilidad**, seleccione el conjunto
    de disponibilidad que creó anteriormente.

5.  Haga clic en **Save**.
    
    **Nota:** esto puede tener como resultado que la máquina virtual se
    reinicie para finalizar la pertenencia al conjunto de
    disponibilidad.

<!-- LINKS -->



[1]: http://www.windowsazure.com/en-us/support/legal/sla/
[2]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
