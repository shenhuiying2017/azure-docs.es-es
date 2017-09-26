#### <a name="to-create-a-cloud-appliance"></a>Creación de una aplicación en la nube

1. En Azure Portal, vaya al servicio **Administrador de dispositivos de StorSimple**.
2. Vaya a la hoja **Dispositivos**. En la barra de comandos en la hoja de resumen de servicios, haga clic en **Crear aplicación de nube**.
    ![Creación de dispositivo de nube de StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. En la hoja **Crear aplicación de nube**, especifique los detalles siguientes.
   
    ![Creación de dispositivo de nube de StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Nombre**: un nombre único para la aplicación de nube.
   2. **Modelo**: elija el modelo de la aplicación de nube. Un dispositivo 8010 ofrece 30 TB de Standard Storage, mientras que 8020 tiene 64 TB de Premium Storage. Especifique 8010 para implementar escenarios de recuperación de nivel de elemento a partir de copias de seguridad. Seleccione 8020 para implementar cargas de trabajo de baja latencia y alto rendimiento o de uso como un dispositivo secundario para recuperación ante desastres.
   3. **Versión**: elija la versión de la aplicación en la nube. La versión corresponde a la versión de la imagen de disco virtual que se utiliza para crear el dispositivo en la nube. Dado que la versión de la aplicación en la nube determinará desde qué dispositivo físico puede efectuar la conmutación por error o la clonación, es importante que cree una versión adecuada de esta.
   4. **Red virtual**: especifique la red virtual que desea usar con esta aplicación en la nube. Si utiliza Premium Storage, debe seleccionar una red virtual que sea compatible con la cuenta de Premium Storage. Las redes virtuales no compatibles aparecerán atenuadas en la lista desplegable. Se le avisará si selecciona una red virtual no compatible.
   5. **Subred**: en función de la red virtual seleccionada, la lista desplegable muestra las subredes asociadas. Asigne una subred a la aplicación en la nube.
   6. **Cuenta de almacenamiento**: seleccione una cuenta de almacenamiento para almacenar la imagen de la aplicación en la nube durante el aprovisionamiento. Esta cuenta de almacenamiento debe estar en la misma región que la aplicación en la nube y la red virtual. Ni el dispositivo físico ni la aplicación en la nube deben utilizarla para almacenamiento de datos. De forma predeterminada, para ello se crea una nueva cuenta de almacenamiento. Sin embargo, si sabe que ya tiene una cuenta de almacenamiento que es adecuada para este uso, puede seleccionarla en la lista. Si crea una aplicación en la nube premium, la lista desplegable solo mostrará las cuentas de Premium Storage.
      
      > [!NOTE]
      > La aplicación en la nube solo puede funcionar con cuentas de Azure Storage.
    
   7. Active la casilla para indicar que sabe que los datos almacenados en la aplicación en la nube estarán hospedados en un centro de datos de Microsoft.
       * Cuando se utiliza solo un dispositivo físico, la clave de cifrado se mantiene con el dispositivo; por lo tanto, Microsoft no podrá descifrarla.

       * Cuando se utiliza una aplicación en la nube, la clave de cifrado y la clave de descifrado se almacenan en Microsoft Azure. Para más información, consulte las [consideraciones de seguridad para utilizar una aplicación en la nube](../articles/storsimple/storsimple-security.md).
   8. Haga clic en **Crear** para aprovisionar la aplicación en la nube. El dispositivo puede tardar unos 30 minutos en aprovisionar. Recibirá una notificación cuando la aplicación en la nube se haya creado correctamente. Vaya a la hoja Dispositivos y la lista de dispositivos se actualizará y mostrará la aplicación en la nube. El estado de la aplicación es **Listo para configurar**.
      
      ![Estado listo para configurar de StorSimple Cloud Appliance](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)

