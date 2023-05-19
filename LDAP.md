# Configuración LDAP
## Archivos a configurar
- **nano /etc/network/interfaces**
  - aquí tenemos que añadir la address que es la ip del equipo y la gateway.
- **nano /etc/hosts**
  - aquí tenemos que añadir la ip del equipo, seguido del nombre del equipo con el dominio (servidor.ejemplo.local) y después el nombre solo del equipo. en el equipo cliente también añadimos una línea para poder poner en la url la dirección, sin tener que poner la ip
- **nano /etc/hostname**
  - aquí tenemos que añadir el nombre del equipo con el dominio (servidor.ejemplo.local)
- **nano /etc/apt/sources.list**
  - aquí tenemos que comentar las líneas de siempre
- **nano /etc/resolv.conf**
  - aquí tenemos que añadir el domain y search con el nombre del dominio (ejemplo.local) y en nameserve las ip para la comprobación que todo funciona bien.
  - **systemctl restart networking.service** (para reiniciar el servicio de internet)
  - **apt update**
- Instalamos el ssh >> **apt install openssh-server**. Instalamos las guest additions: introducimos el disco, **mount /media/cdrom, cp /media/cdrom/VBoxLinux /root, ./VBoxLinux**
- reiniciamos el equipo
- **apt install slapd ldap-utils** (para instalar el ldap en el servidor)
- **slapcat** (para comprobar que todo se ha instalado bien) "dpkg-reconfigure **slapd**" (para comprobar que está todo bien configurado.)
- **apt install ldap-account-manager** (para poder configurar el LDAP desde el navegador del cliente.)
- vamos al navegador del cliente y en la barra de búsqueda introducimos la dirección (servidor.ejemplo.local/lam)
- vamos a **LAM configuration** y **Edit server profiles**. introducimos la contraseña **lam**
- Dentro de **edit server profiles** cambiamos:
  - en **General settings**
    - **time zone** por madrid
    - **tree suffix** dc=ejemplo,dc=local
    - **list of valid users** cn=admin,dc=ejemplo,dc=local
  - en **Account types**
    - **LDAP suffix** ou=Usuarios,dc=ejemplo,dc=local y también el los grupos
- Una vez configurado eso, le damos a save
- introducimos la contraseña del administrador de LDAP.
- pulsamos el botón de create.
- en **Account**, creamos los grupos y los usuarios. cuando creamos los grupos, en el apartado unix, tenemos que modificar también el apartado **home directoy** y añadir antes del nombre del usuario el nombre del dominio (/home/ejemplo.local/$user)
- una vez añadimos los usuarios y los grupos, podemos salir del navegador web.
- en el cliente instalamos **apt install libpam-ldapd** para hacer que el cliente se pueda validar contra el servidor LDAP.
  - cuando nos pide una ip podemos introducir la ip del servidor o la dirección del dominio.
  - tenemos que marcar las opciones **passwd, group, shadow**
- para ver los usuarios es **getent passwd** y para ver los grupos es **getent group**
- para reconfigurar es con los comandos: **dpkg-reconfigure nslcd y dpkg-reconfigure libnss-ldapd**
- vamos al archivo **nano /usr/share/pam-configs/mkhomedir** y en la última fila al final de todo añadimos: **umask=0077**
- salimos del archivo e introducimos **pam-auth-update --enable mkhomedir** y después **pam-auth-update** y reiniciamos el equipo cliente.