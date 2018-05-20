Ahora se ofrece en Azure compatibilidad con dos características de depuración, Salida de la consola y Captura de pantalla, para el modelo de implementación de Resource Manager en Azure Virtual Machines. 

Cuando lleva su propia imagen a Azure o incluso cuando arranca una de las imágenes de la plataforma, hay muchas razones por las que una máquina virtual puede entrar en un estado que no permita el arranque. Estas características le permiten diagnosticar y recuperar fácilmente las máquinas virtuales de los errores de arranque.

En las máquinas virtuales Linux, puede ver fácilmente la salida de su registro de consola desde el portal:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Sin embargo, en las máquinas virtuales Windows y Linux, Azure también le permite ver una captura de pantalla de la máquina virtual desde el hipervisor:

![Error](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Ambas características son compatibles con Azure Virtual Machines en todas las regiones. Tenga en cuenta que las capturas de pantalla y la salida pueden tardar hasta 10 minutos en aparecer en la cuenta de almacenamiento.

## <a name="common-boot-errors"></a>Errores comunes de arranque

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [No se encontró un sistema operativo](https://support.microsoft.com/help/4010142)
- [Error de arranque o INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Habilitación del diagnóstico en una nueva máquina virtual
1. Cuando cree una máquina virtual desde Azure Portal, seleccione **Azure Resource Manager** en la lista desplegable de modelos de implementación:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. En **Configuración**, habilite **Diagnósticos de arranque** y, a continuación, seleccione una cuenta de almacenamiento donde le gustaría colocar estos archivos de diagnóstico.
 
    ![Creación de una máquina virtual](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > La característica Diagnósticos de arranque no es compatible con las cuenta de almacenamiento premium. Si usa la cuenta de almacenamiento premium con Diagnósticos de arranque, podría recibir el error StorageAccountTypeNotSupported al iniciar la máquina virtual.
    >
    > 

3. Si va a realizar la implementación con una plantilla de Azure Resource Manager, vaya al recurso de máquina virtual y anexe la sección de perfil de diagnóstico. Recuerde usar el encabezado de versión de API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. El perfil de diagnóstico le permite seleccionar la cuenta de almacenamiento donde desea colocar estos registros.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Para implementar una máquina virtual de ejemplo con Diagnósticos de arranque habilitado, visite nuestro repositorio aquí.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Habilitación de Diagnósticos de arranque en una máquina virtual existente 

Para habilitar Diagnósticos de arranque en una máquina virtual existente, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y, luego, seleccione la máquina virtual.
2. En **Support + troubleshooting** (Soporte técnico y solución de problemas), seleccione **Diagnósticos de arranque** > **Configuración**, cambie el estado a **Activado** y, luego, seleccione una cuenta de almacenamiento. 
4. Asegúrese de que está seleccionada la opción Diagnósticos de arranque y, a continuación, guarde el cambio.

    ![Actualización de una máquina virtual existente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Reinicie la máquina virtual para que surta efecto.


