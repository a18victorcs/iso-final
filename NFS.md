# NFS
# Instalación NFS
- **apt install nfs-kernel-server** para instalar el NFS

## Configuración del servidor
- **nano /etc/exports** en este archivo tenemos que añadir:
  - /mnt/comun 172.16.0.0/255.255.0.0(sync, rw, no_subtree_check,root_squash) "hay que añadir una línea como esta para cada carpeta que queramos compartir. para que no de fallos hay que poner no_root_squash en la línea de comun.
- **systemctl restart nfs-kernel-server.service** para reinciar el servicio nfs
- **showmount -e** para ver las carpetas que estamos compartiendo.
- **exportfs -ra** para que el servidor NFS tenga conocimiento de las exportaciones configuradas.
- **systemctl restart nfs-kernel-server.service** volvemos a reiniciar.

## Configuración del cliente
- **apt install nfs-common** instalamos este paquete
- **showmount -e servidor.ejemplo.local** para ver que carpetas se están compartiendo
- el montar la carpeta en el cliente dentro del fichero fstab no es obligatorio hacerlo.
- **mount -t nfs servidor.ejemplo.local:/mnt/carpeta_compartida /mnt/carpeta_en_cliente**
- **nano /etc/fstab** vamos al fichero e introducimos:
  - **servidor.ejemplo.local:/mnt/carpeta_compartida /mnt/carpeta_en_cliente nfs defaults 0 0**
- **mount -a** para comprobar que el fichero fstab está bien


## Permisos en el servidor
- **apt install libpam-ldapd** instalamos este paquete en el servidor como hicimos en el cliente y reiniciamos el servidor. cuando nos aparece para introducir la dirección/dominio, tenemos que quitarle i a **ldapi**
- **chgrp grupo /mnt/carpeta_compartida/carpeta_compartida** cambiamos los propietarios de las carpetas.
- **chmod 700 /mnt/carpeta_compartida** cambiamos los permisos a las carpetas.
- hacemos lo mismo con las subcarpetas
- **apt install acl** instalamos las acl
- **setfacl -m g:grupoLDAP:permisos /mnt/carpeta_compartida** para añadir permisos de otros grupos o grupos secundarios.
- **setfacl -d -m g:grupoLDAP:permisos /mnt/carpeta_compartida** hay que poner también con -d.
- **getfacl /mnt/carpeta_compartida1** para obtener y mostrar la lista de control de acceso
- **chown usuarioLDAP.grupoLDAP /mnt/carpeta_compartirda** para cambiar el usuario y el grupo de una carpeta
- [Calculadora permisos]([URL del enlace](https://chmod-calculator.com/))
- **Ejemplos:**
```
    -chgrp Galumnos /mnt/Comun/Alumnos
    -chmod 770 /mnt/Comun/Alumnos
    -setfacl -m g:Gprofesores:rwx /mnt/Comun/Alumnos
    -setfacl -d -m g:Gprofesores:rwx /mnt/Comun/Alumnos
    -chown alumnoasir1:GLDAP /mnt/Personales/AlumnoASIR1
```

## En el cliente
- **apt install libpam-mount** para montar los archivos
- **nano /etc/security/pam_mount.conf.xml** editamos este fichero e introducimos después de "volume definitions"
  - ```<volume server="servidor.ejemplo.local" fstype="nfs" sgrp="grupoLDAP" path"/mnt/carpeta_compartida" mountpoint="/home/ejemplo.local/½(USER)/carpeta_en_cliente"/>```
- **Ejemplo**
- ![pam_mount_2](/img/pam_mount_2.png)