## Pasos rápidos 

En el artículo se supone que ha iniciado sesión en su suscripción en el portal y que ha creado una máquina virtual con las imágenes disponibles mediante el modelo de implementación de Resource Manager. Cuando comience a ejecutarse la máquina virtual, siga estos pasos.

1.  Vea la configuración de la máquina virtual en el portal y haga clic en la dirección IP pública.

    ![buscar recurso de ip](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  Tenga en cuenta que el nombre DNS de la dirección IP pública está en blanco. Haga clic en **Configuración** en la hoja Dirección IP pública.

    ![configuración de ip](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  Escriba la etiqueta de nombre DNS deseada y haga clic en **Guardar**.

    ![escribir etiqueta de nombre dns](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    El recurso de dirección IP pública ahora muestra esta nueva etiqueta DNS en su hoja.

4.  Cierre las hojas de la dirección IP pública y vuelva a la hoja de la máquina virtual del portal. Compruebe que el nombre DNS o FQDN aparece junto a la dirección IP para el recurso de dirección IP pública.

    ![Se crea el FQDN](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->