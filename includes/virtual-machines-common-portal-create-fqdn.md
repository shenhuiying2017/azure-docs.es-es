## <a name="quick-steps"></a>Pasos rápidos
En el artículo se supone que ha iniciado sesión en su suscripción en el portal y que ha creado una máquina virtual con las imágenes disponibles mediante el modelo de implementación de Resource Manager. Cuando comience a ejecutarse la máquina virtual, siga estos pasos.

1. Seleccione su máquina virtual en el portal. El nombre DNS está en blanco. Haga clic en **Dirección IP pública**:
   
   ![Haga clic en el recurso de IP pública en el portal](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2. Escriba la etiqueta de nombre DNS deseada y haga clic en **Guardar**.
   
   ![Escriba una etiqueta de nombre DNS del recurso de IP pública](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   El recurso de dirección IP pública ahora muestra esta nueva etiqueta DNS en su hoja.

3. Cierre las hojas de IP pública y vuelva a la hoja de información general de la máquina virtual del portal. Después de unos segundos, el portal debería actualizar la configuración. Compruebe que el nombre DNS o FQDN aparecen junto a la dirección IP del recurso de **Dirección IP pública**.
   
   ![Confirme que la nueva etiqueta DNS está configurada](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

