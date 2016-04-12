


## Etiquetado de una máquina virtual mediante plantillas

En primer lugar, veamos el etiquetado a través de plantillas. [Esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) coloca etiquetas en los siguientes recursos: proceso (máquina virtual), almacenamiento (cuenta de almacenamiento) y red (dirección IP pública, red virtual e interfaz de red). (En este ejemplo la plantilla es para una máquina virtual Windows, pero se puede adaptar para máquinas virtuales Linux).

Haga clic en el botón **Implementar en Azure** del [vínculo de la plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Esto le remitirá al [Portal de Azure](https://portal.azure.com/), donde puede implementar esta plantilla.

![Implementación sencilla con etiquetas](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Esta plantilla incluye las siguientes etiquetas: *Department*, *Application* y *Created By*. Estas etiquetas se pueden agregar o modificar directamente en la plantilla si se desea cambiar sus nombres.

![Etiquetas de Azure en una plantilla](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como puede ver, las etiquetas se definen como pares de clave-valor, separados por dos puntos (:). Las etiquetas deben definirse con este formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Guarde el archivo de plantilla cuando termine de modificarlo con las etiquetas que prefiera.

A continuación, en la sección **Editar parámetros**, puede rellenar los valores de las etiquetas.

![Editar etiquetas en el Portal de Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Haga clic en **Crear** para implementar esta plantilla con sus valores de etiqueta.


## Etiquetado a través del portal

Después de crear los recursos con etiquetas, puede ver, agregar y eliminar etiquetas en el portal.

Seleccione el icono de etiquetas para ver las etiquetas:

![Icono de etiquetas en el Portal de Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Para agregar una nueva etiqueta desde el portal, defina su propio par de clave-valor y guárdela.

![Agregar etiqueta nueva en el Portal de Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

La nueva etiqueta debería aparecer en la lista de etiquetas del recurso.

![Etiqueta nueva guardada en el Portal de Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)


## Visualización de etiquetas en los detalles de uso

Las etiquetas colocadas en los recursos de proceso, red y almacenamiento a través del Administrador de recursos de Azure se rellenarán en los detalles de uso del [portal de facturación](https://account.windowsazure.com/).

Haga clic en **Descargar detalles de uso** para ver los detalles de uso de la suscripción.

![Detalles de uso en el Portal de Azure](./media/virtual-machines-common-tag/azure-portal-tags-usage-details.png)

Seleccione el extracto de facturación y los detalles de uso de la **versión 2**:

![Detalles de uso de la vista previa de la versión 2 en el Portal de Azure](./media/virtual-machines-common-tag/azure-portal-version2-usage-details.png)

En los detalles de uso puede ver todas las etiquetas en la columna de **etiquetas**:

![Columna de etiquetas en el Portal de Azure](./media/virtual-machines-common-tag/azure-portal-tags-column.png)

Mediante el análisis de estas junto con el uso, las organizaciones podrán obtener nuevos puntos de vista en sus datos de consumo.

<!---HONumber=AcomDC_0330_2016-->