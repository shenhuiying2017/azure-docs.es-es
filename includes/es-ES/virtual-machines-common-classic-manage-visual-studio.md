En Azure se pueden crear máquinas virtuales desde el Explorador de servidores en Visual Studio.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>Creación de una máquina virtual de Azure en el Explorador de servidores
Si bien puede crear una máquina virtual en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=253103), también puede hacerlo en Azure con los comandos del Explorador de servidores. Las máquinas virtuales se pueden usar, por ejemplo, para proporcionar un front-end detrás de un extremo público con equilibrio de carga común.

### <a name="to-create-a-new-virtual-machine"></a>Para crear una nueva máquina virtual
1. En el Explorador de servidores, abra el nodo **Azure** y haga clic en **Virtual Machines**.
2. En el menú contextual, haga clic en **Crear máquina virtual**.
   
    Aparecerá el Asistente para **crear una nueva máquina virtual** .
   
    ![El comando Crear máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. En la página **Elija una suscripción**, seleccione la suscripción que va a usar al crear la máquina virtual y haga clic en **Siguiente**.
   
    Si no ha iniciado sesión en Azure, haga clic en **Iniciar sesión** para iniciar una. A continuación, seleccione su suscripción de Azure en el cuadro de lista desplegable, en caso de que no esté seleccionada.
4. En la página **Seleccionar una imagen de máquina virtual**, seleccione un tipo de imagen en el cuadro de lista desplegable **Tipo de imagen** y, después, seleccione una imagen de máquina virtual en el cuadro de lista **Nombre de imagen**. Cuando haya terminado, haga clic en **Siguiente**.
   
    ![Seleccionar una página de imágenes de máquinas virtuales](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Puede elegir los siguientes tipos de imagen:
   
   * **Imágenes públicas** enumera las imágenes de máquinas virtuales de sistemas operativos y software de servidores, como Windows Server y SQL Server.
   * **Imágenes de MSDN** enumera las imágenes de máquinas virtuales de software disponible para los suscriptores de MSDN, como Visual Studio y Microsoft Dynamics.
   * **Imágenes privadas** enumera imágenes que ha creado de máquinas virtuales especializadas y generalizadas.
     
     Para obtener información sobre máquinas virtuales especializadas y generales, consulte [Imagen de VM](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Para obtener información sobre cómo convertir una máquina virtual en una plantilla que puede usar para crear rápidamente máquinas virtuales preconfiguradas, consulte [Cómo capturar una máquina virtual Windows para usarla como imagen](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) .
     
     Para ver información sobre la imagen en el lado derecho de la página, puede hacer clic en el nombre de una imagen de máquina virtual.
     
     > [!NOTE]
     > No se pueden agregar imágenes de máquinas virtuales a las listas **Imágenes públicas** o **Imágenes de MSDN**, ya que son de solo lectura. Todas las máquinas virtuales que cree se agregan a la lista **Imágenes privadas** .
     > 
     > 
     
     Si es suscriptor de MSDN con una suscripción de nivel de Visual Studio, puede crear una máquina virtual de Azure pregenerada que contenga Visual Studio, así como otras imágenes. Para más información, consulte [Create a Virtual Machine in Visual Studio by Using Images Visual Studio 2013 Gallery image for MSDN subscribers (Creación de una máquina virtual en Visual Studio mediante imágenes de la galería de imágenes de Visual Studio 2013 para suscriptores de MSDN)](http://visualstudio2013msdngalleryimage.azurewebsites.net) y [Suscripciones a MSDN](https://www.visualstudio.com/products/msdn-subscriptions-vs).|
5. En la página **Configuración básica de una máquina virtual** , escriba el nombre de un equipo y, a continuación, agregue las especificaciones de la máquina virtual, incluyendo el tamaño, el nombre de usuario y la contraseña. Cuando haya terminado, haga clic en **Siguiente**.
   
    El nombre y la contraseña nuevos se usarán para iniciar sesión en el equipo mediante el escritorio remoto, por lo que se recomienda anotarlos por si se olvidan. Después de crear una máquina virtual de Azure en Visual Studio, puede cambiar su tamaño y otros valores de configuración en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Si elige tamaños mayores para la máquina virtual, es posible que se apliquen cargos adicionales. Para obtener más información, consulte [Detalles de precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) .
   > 
   > 
6. Las máquinas virtuales creadas en Visual Studio requieren un servicio en la nube. En la página **Configuración del servicio en la nube**, seleccione un servicio en la nube para la máquina virtual, o bien haga clic en **<Crear nuevo...>** en la lista desplegable si aún no tiene un servicio en la nube o si desea usar uno nuevo. También se requiere una cuenta de almacenamiento, así que elija una cuenta de almacenamiento (o cree una nueva) en el cuadro de lista desplegable **Cuenta de almacenamiento** . Para obtener más información, consulte [Introducción a Almacenamiento de Microsoft Azure](../articles/storage/common/storage-introduction.md) .
7. Si desea especificar una red virtual (que es opcional), selecciónela en los cuadros de lista desplegable Red virtual y Subred.
   
    Las máquinas virtuales que son miembros de un conjunto de disponibilidad se implementan en distintos dominios de error. Para obtener más información, consulte [Red virtual de Azure](https://azure.microsoft.com/services/virtual-network/) .
8. Si desea que su máquina virtual pertenezca a un conjunto de disponibilidad (también opcional), active la casilla **Especifique un conjunto de disponibilidad** y después elija un conjunto de disponibilidad en la lista desplegable. Cuando haya terminado, elija el botón **Siguiente** .
   
    La adición de la máquina virtual a un conjunto de disponibilidad facilita que la aplicación esté disponible cuando se produzcan fallos de red, fallos del hardware del disco local y cualquier tiempo de inactividad planeado. Debe usar el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=253103) para crear redes virtuales, subredes y conjuntos de disponibilidad. Para obtener más información, consulte [Administración de la disponibilidad de las máquinas virtuales](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) .
9. En la página **Extremos** , especifique los extremos públicos que desee que estén disponibles para los usuarios de la máquina virtual. Por ejemplo, puede optar por habilitar HTTP (puerto 80), además de los extremos Escritorio remoto y PowerShell, que están habilitados de forma predeterminada. Para agregar un punto de conexión, elíjalo en el cuadro de lista desplegable **Nombre de puerto** y haga clic en el botón **Agregar**. Para quitar un extremo, elija la **X** roja que hay junto al nombre en la lista de extremos.
   
    ![La página Extremo del Asistente para máquinas virtuales.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    Los extremos que están disponibles dependen del servicio en la nube que seleccionó para la máquina virtual. Para obtener más información, consulte [Extremos de servicio de Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) .
   
   > [!NOTE]
   > La habilitación de extremos públicos hace que los servicios de la máquina virtual estén disponibles en Internet. Asegúrese de instalar y configurar correctamente los extremos y servicios en la máquina virtual, como las listas de control de acceso (ACL) de los extremos. Para obtener más información, consulte [Configuración de extremos en una máquina virtual](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) .
   > 
   > 
10. Cuando haya terminado la configuración de la máquina virtual, haga clic en el botón **Crear** para crearla.
    
     A medida que Azure crea la máquina virtual, el **Registro de actividad de Azure** muestra el progreso de la operación de creación de la máquina virtual.
    
     ![Registro de actividad de máquina virtual: en curso.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Para ver solo la información de máquina virtual, elija la pestaña **Virtual Machines** en el **Registro de actividad de Azure**.
    
     ![Registro de actividad de máquina virtual: completado.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Si la operación se completa correctamente, la nueva máquina virtual aparecerá en el nodo **Máquinas virtuales** en el Explorador de servidores. Para iniciar sesión en ella, haga clic en el acceso directo **Conectar con Escritorio remoto** .
    
     ![Máquina virtual que aparece en el Explorador de servidores.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>Administración de máquinas virtuales
En la página de configuración de máquina virtual, además de apagar, conectar, actualizar y agregar puntos de comprobación a la máquina virtual seleccionada, puede ver o cambiar la configuración de la máquina virtual. Puede:

* Cambiar el tamaño de la máquina virtual.
* Seleccionar el conjunto de disponibilidad que va a usar con la máquina virtual.
* Agregar, quitar o cambiar la configuración de extremos públicos.
* Agregar, quitar o configurar las extensiones de la máquina virtual.
* Ver información sobre los discos asociados a la máquina virtual.

### <a name="view-or-change-virtual-machine-settings"></a>Visualización o cambio de la configuración de la máquina virtual
1. En el Explorador de servidores, elija la máquina virtual en el nodo **Máquinas virtuales de Azure** .
2. En el menú contextual, elija **Configurar** para ver la página de configuración de la máquina virtual.
   
    ![La página de configuración de máquinas virtuales de Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. Vea la información de la máquina virtual o cámbiela.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Guardado o restauración del estado de una máquina virtual
Al configurar la máquina virtual e instalarle software, es conveniente guardar periódicamente el progreso mediante la creación de puntos de comprobación de la máquina virtual. Un punto de control es una instantánea, o una imagen, del estado actual de la máquina virtual. Si surge algún problema en la máquina virtual o quiere volver a configurarla, puede restaurarla desde un punto de control anterior para ahorrar tiempo, en lugar de comenzar desde cero.

### <a name="to-create-a-virtual-machine-checkpoint"></a>Para un punto de control en una máquina virtual
1. En el Explorador de servidores, elija la máquina virtual en el nodo **Máquinas virtuales de Azure** .
2. En el menú contextual, elija **Configurar** para ver la página de configuración de la máquina virtual.
3. En la página de configuración, elija el botón **Capturar imagen** .
   
    ![Botón de captura de página de configuración de Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    Aparece el cuadro de diálogo **Capturar máquina virtual** .
   
    ![Cuadro de diálogo de máquina virtual de captura de Azure.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Especifique una etiqueta y una descripción de la imagen. Aparecen una etiqueta y una descripción predeterminadas, pero puede sobrescribirlas si lo desea.
5. Si ya ha ejecutado Sysprep en esta máquina virtual, active la casilla **He ejecutado Sysprep en la máquina virtual** .
   
    Sysprep es una herramienta que, entre otras cosas, elimina datos específicos de los sistemas de la versión de Windows de la máquina virtual, lo que la convierte en una plantilla que otros pueden usar. Para obtener más información, consulte [Cómo capturar una máquina virtual Windows para usarla como imagen](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) . Cree una copia de seguridad de la VM antes de ejecutar Sysprep.
6. Cuando haya terminado la configuración de la captura, elija el botón **Capturar** para crear el punto de control.
   
    A medida que Azure crea el punto de control, el **Registro de actividad de Azure** muestra el progreso de la operación.
   
    ![Captura de un punto de control en una máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    Cuando se completa la operación del punto de control, lo verá en el **Registro de actividad de Azure**.
   
    ![Operación de punto de control completada](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Administración de puntos de control de una máquina virtual
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>Para restaurar una máquina virtual a un estado anterior guardado
* Siga los pasos que se detallan en [Paso a paso: realizar restauraciones en la nube de Máquinas virtuales de Microsoft Azure con PowerShell (parte 2)](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Para eliminar un punto de control
1. Vaya al [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
2. En la página de configuración de la máquina virtual, elija la pestaña **Imágenes** situada en la parte superior de la página.
3. Elija el punto de control que desea eliminar y, después, haga clic el botón **Eliminar**, que se encuentra en la parte inferior de la página.

## <a name="shut-down-your-virtual-machine"></a>Apague la máquina virtual.
1. En el Explorador de servidores, elija la máquina virtual que desea apagar en el nodo **Máquinas virtuales de Azure** .
2. En el menú contextual, elija el comando **Apagar**, o bien elija **Configurar** si lo que desea es ver la página de configuración de la máquina virtual y, después, haga clic en el botón **Apagar**.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de la creación de máquinas virtuales, consulte [Creación de una máquina virtual Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una máquina virtual Windows en el Portal de vista previa de Azure](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

