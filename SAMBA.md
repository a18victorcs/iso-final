# SAMBA
## Instalación de samba en el servidor
- **apt install samba**
- **nano /etc/samba/smb.conf** editamos este fichero y añadimos al final de todo:
  - [Carpeta_compartida]
    - comment = "algún comentario que querramos hacer sobre la carpeta"
    - path = "dirección donde está la carpeta compartida"
    - guest ok = "permitir el acceso de invitados o usuarios no autenticados"
    - read only = "solo lectura, o si tienen permisos de escritura"
    - browseable = yes "para ver si es oculta o no, yes no es oculta"
    - create mask = "para establecer los permisos de creación de archivos y carpetas"
    - directoy mask = "para especificar los permisos de creación de directorios"
- salimos del archivo y comprobamos con **testparm** que todo va bien.
- reiniciamos samba con
  - **/etc/init.d/smbd restart**
  - **/etc/init.d/nmbd restart**
- **apt install smbclient** instalamos este paquete
- **smbclient -L servidor.ejemplo.local** ejecutamos este comando para ver que carpetas están compartidas. Cuando pide la contraseña del root no hace falta introducir la contraseña.
- Hay que añadir usuarios a samba (se crea uno nuevo):
  - **smbpasswd -a usuario**
  - **smbpasswd -e usuario**
  - una vez añadido a la base de datos de samba para ver los usuarios es con **pdbedit -L**, para borrar algún usuario es **smbpasswd -x usuario** y para desabilitar usuarios es **smbpasswd -d usuario**.
## Desde un cliente:
- **apt install samba-common smbclient cifs-utils**
- **smbclient -L //ip** para ver los recursos compartidos
- En samba tenemos que montar a mano las carpetas:
  - vamos al archivo **/etc/fstab**
  - *//ip/carpeta_compartida /carpeta_en_local cifs defaults,username=usuario,password=contraseña 0 0*
  - *//ip/carpeta_compartida /carpeta_en_local cifs defaults,credentials=archivo_con_credenciales.txt 0 0* en el archivo_con_credenciales.txt hay que introducir el usuario y la contraseña en líneas diferentes, username=usuario y passwd=contraseña. cambiamos los permisos a 600 en el fichero.
  - **mount -a** para comprobar que no hay ningún error
  - **mount** para comprobar que está montado
- Abrimos el nautilus, nos desplazamos a otras ubicaciones, en la parte inferior sale la opción para conectarnos al servidor. introducimos: **smb://ip/** para acceder al servidor. De esta forma nos aparece una ventana para autenticarnos, tenemos que introducir las credenciales que pusimos en el fichero archivo_con_credenciales.txt
## Montar las carpetas sin tener que hacer los pasos del nautilus
- **apt install libpam-mount**
- **nano /etc/security/pam_mount.conf.xml** editamos este fichero y añadimos después de "volume definitions": 
- ```<volume server="ip" gid="10000" fstype="cifs" path="carpeta_compartida (solo se indica el nombre del recurso compartido, no toda la ruta)" mountpoint="carpeta_en_cliente"/>```
- (hay que hacerlo con las carpetas que querramos compartir)
## Comprobación que todo está bien
- *Comprobación desde Linux*
  - Entramos en el cliente con un usuario del LDAP y vamos al nautilus para comprobar que montó las carpetas.
- *Comprobación desde Windows*
  - Introducimos la ip en el buscador **\\ip**
  - Nos pide credenciales, hay que introducir los usuarios que tenemos en la base de datos de samba.