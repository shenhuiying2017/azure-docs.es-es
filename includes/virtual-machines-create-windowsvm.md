1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Desde la esquina superior izquierda, haga clic en **Nuevo > Proceso > Windows Server 2016 Datacenter**.

    ![Vaya a las imágenes de máquinas virtuales de Azure en el portal](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. En Windows Server 2016 Datacenter, seleccione el modelo de implementación clásico. Haga clic en Crear.

    ![Captura de pantalla que muestra las imágenes de las máquinas virtuales de Azure disponibles en el portal](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Hoja Básico

La hoja Básico solicita información administrativa para la máquina virtual.

1. Escriba un **nombre** para la máquina virtual. En este ejemplo, _HeroVM_ es el nombre de la máquina virtual. El nombre debe tener de 1 a 15 caracteres de longitud, sin caracteres especiales.

2. Escriba un **nombre de usuario** y una **contraseña** segura que se usarán para crear una cuenta local en la máquina virtual. La cuenta local sirve para iniciar sesión en la máquina virtual y administrarla. En el ejemplo, _azureuser_ es el nombre de usuario.

 La contraseña debe tener entre 8 y 123 caracteres y reunir, al menos, tres de los cuatro requisitos de complejidad siguientes: contener al menos una minúscula, una mayúscula, un número y un carácter especial. Obtenga más información acerca de los [requisitos de usuario y la contraseña](../articles/virtual-machines/windows/faq.md).

3. La **suscripción** es opcional. Una configuración común es "Pago por uso".

4. Seleccione un valor de **Grupo de recursos** existente o escriba el nombre para crear uno. En el ejemplo, _HeroVMRG_ es el nombre del grupo de recursos.

5. Seleccione una **ubicación** del centro de datos de Azure donde desea que se ejecute la máquina virtual. En el ejemplo, la ubicación es **Este de EE. UU**.

6. Cuando termine, haga clic en **Siguiente** para continuar a la hoja siguiente.

    ![Captura de pantalla que muestra la configuración en la hoja Básico para configurar una máquina virtual de Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Hoja Tamaño

La hoja Tamaño identifica los detalles de configuración de la máquina virtual y enumera diversas opciones que incluyen SO, el número de procesadores, el tipo de almacenamiento en disco y costos de uso mensuales estimados.  

Elija un tamaño de máquina virtual y, luego, **Seleccionar** para continuar. En este ejemplo, _DS1_\__V2 estándar_ es el tamaño de máquina virtual.

  ![Captura de pantalla que muestra la hoja Tamaño con los tamaños de máquina virtual de Azure que puede seleccionar](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Hoja Configuración

La hoja Configuración solicita las opciones de almacenamiento y red. Puede aceptar la configuración predeterminada. Azure crea las entradas correspondientes en caso de ser necesario.

Si seleccionó un tamaño de máquina virtual que lo admita, puede probar Azure Premium Storage, para lo que debe seleccionar Premium (SSD) en Tipo de disco.

Cuando haya terminado de realizar los cambios, haga clic en **Aceptar**.

## <a name="4-summary-blade"></a>4. Hoja Resumen

La hoja Resumen enumera la configuración que se especificó en las hojas anteriores. Haga clic en **Aceptar** cuando esté listo para crear la imagen.

 ![Informe de la hoja Resumen que proporciona la configuración especificada de la máquina virtual](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Una vez creada la máquina virtual, en el portal se muestra la máquina virtual nueva en **Todos los recursos** y muestra un icono de la máquina virtual en el panel. También se crean y muestran el servicio en la nube y la cuenta de almacenamiento correspondientes. La máquina virtual y el servicio en la nube se inician automáticamente, y su estado aparece como **En ejecución**.

 ![Configure el agente de máquina virtual y los puntos de conexión de la máquina virtual](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
