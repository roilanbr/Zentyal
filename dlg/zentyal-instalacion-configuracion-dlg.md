# Instalación de Zentyal #
## Datos para la copnfiguración ##
### Dominio ###
`dlgXX.ltu.minag.gob.cu`


### IPs a útilizar en la configuración ###
**Local Interno**

| Descripción      | Servidor    | Proto   | Port |
| ---------------- | ----------- | ------- | ---- |
| Servidor         | 192.168.0.3 |         |      |

**Local Externo**

| Descripción      | Servidor    | Proto   | Port |
| ---------------- | ----------- | ------- | ---- |
| Servidor NTP     | 172.17.xx.3 | UDP/TCP | 123  |
| Servidor correo  | 172.17.xx.3 | TCP     | 26   |
| HTTP Proxy       | 172.17.xx.3 | TCP     | 3128 |
| Webmail SOGo     | 172.17.xx.3 | TCP     | 443  |


**Remoto**

| Descripción      | Servidor     | Proto   | Port |
| ---------------- | ------------ | ------- | ---- |
| Servidor NTP     | 172.16.65.54 | UDP/TCP | 123  |
| Relay de correo  | 172.16.65.4  | TCP     | 26   |
| Proxy padre      | 172.16.65.54 | TCP     | 3128 |

## Hacer una copia de los paquetes ##
En el primer inicio de sección de Zentyal se abre automaticamente el navegador en **Webadmin zentyal**, para instalar los paquetes. Por lo que abriremos una consola y haremos una copia de los paquetes y del archivo `sources.list`.

```bash
cp -r /var/tmp/zentyal-packages /home/zen/ 
cp /etc/apt/sources.list /home/zen/zentyal-packages
```

Luego remplazaremos la configuración del repositorio en el archivo `sources.list`

```bash
sudo echo "deb [trusted=yes] file:/home/zen/zentyal-packages ./" > /etc/apt/sources.list
```


## Habilitar SSH root ##
Esto no se recomienda, pero por comodidad habilitaremos al usuario root para acceder por ssh a zentyal. Buscamos el parametro `#PermitRootLogin` descomentamos y cambiamos `prohibit-password` por `yes`.

```bash
sudo nano /etc/ssh/sshd_config
```

    [...]
    permitRootLogin yes
    [...]

Reiniciamos
```bash
sudo systemctl restart sshd
```

## Licencia Zentyal ##
#### Datos para la licencia ####

* Serial: `TRZMM-3HGTF-PPWLG-I5P9P`  
* Expiración: `2050-12-31`  
* Tipo: `PR` (Premiun)  
* Usuarios" `9999`  
* Estado: `{ "id": 2, "code": "L_ACTIVE", "label": "Active", "created_at": "2020-05-31 11:51:37", "updated_at": "2020-05-31 11:51:37" }`

En la terminal ejecutar los siguientes comandos.


```bash
echo "TRZMM-3HGTF-PPWLG-I5P9P" > /var/lib/zentyal/.license  
echo "2050-12-31" > /var/lib/zentyal/.license_expiration  
echo "PR" > /var/lib/zentyal/.license_type  
echo "9999" > /var/lib/zentyal/.license_users  
echo "{ \"id\": 2, \"code\": \"L_ACTIVE\", \"label\": \"Active\", \"created_at\": \"2020-05-31 11:51:37\", \"updated_at\": \"2020-05-31 11:51:37\" }" > /var/lib/zentyal/.license_status
```

## Configuración inicial ##
Cuando se accede a la interfaz web por primera vez, encontraremos un asistente de configuración.

## Instalación de los módulos ##
Marcamos los siguientes módulos:

* `Domain Controler and File Sharing`  
* `Firewall`  
* `Mail and Groupware`  
* `DHCP Server`  
* `HTTP Proxy`

Al terminar la instalación de los modulos continuaremos con la configuración.

## Asistente de configuración inicial ##
### Configurar tipos de interfaces ###
Se solicitará información sobre la configuración de red, en primer lugar debemos definir qué interfaces de red son:

* `Externas` - (utilizadas para conectarse a Internet u otras redes externas)  
* `Internas` - (conectadas a la red local).

Esto afectará las políticas predeterminadas del cortafuegos, mascaras de red, las interfaces de escucha predeterminadas para otros módulos, etc.

### Red: ###
Despues configuramos la IP asignada por DHCP o estática, IP asociada, etc.

| Interfaz   | Metodo   | IP            | Netmask           |
| ---------- | -------- | ------------- | ----------------- |
| `eth0`     | `Static` | `172.17.xx.3` | `255.255.255.248` |
| `eth1`     | `Static` | `192.168.0.3` | `255.255.255.0`   |

### Usuarios y grupos ###
Tenemos que configurar nuestro dominio y el tipo de ‘Domain Controller’ que queremos desplegar. 
Configuraremos el servidor como `stand-alone` (como primer controlador de dominio). Para configurar este modo, tan sólo hay que especificar el nombre de dominio de tu entidad.

Marcamos: `stand-alone`  
Escribimos el Dominio: `dlgXX.ltu.minag.gob.cu`


### Dominio virtual de correo ###
* Dominio virtual de correo: `dlgXX.ltu.minag.gob.cu`

Al finalizar el asistente igresaremos al `Dashboard`

## Otras configuraciones ##
### Fecha ###
**Hora:** Eliminar todos y agregar el siguiente.

* Servidores NTP: `172.16.65.54`

### Red
* Puerta de enlace: `172.17.xx.1`  
* Servidor proxy  : `172.16.65.54`  
* Puerto del proxy: `3128`


###### Objetos ######

| Nombre    | Mienbros                                |
| --------- | --------------------------------------- |
| Relay     | `172.16.65.4` `172.16.64.7-172.16.64.9` |
| Localhost | `192.168.0.3` `127.0.0.1`               |

###### Servicios ######
Añadir el siguiente servicio.

| Nombre              | Port                    |
| ------------------- | ----------------------- |
| Mail Secure Port    | `587` `465` `993` `995` |

### Registros ###
Configurar la rotación de los registros.

| Servicio   | Tiempo    |
| ---------- | --------- |
| Correo     | `1 año`   |
| Proxy HTTP | `1 año`   |


### Correo ###
###### General ######

| Parametro | Valor                       |
| --------- | --------------------------- |
| Smarthost | `172.16.65.4:26`            |
| FQDN      | `mx.dlgXX.ltu.minag.gob.cu` |
| Buzón     | `500 MB`                    |
| Mensaje   | `5 MB`                      |
| Papelera  | `10 Días`                   |
| Spam      | `5 Días`                    |

###### Dominios virtuales ######
| Parametro           | Valor                         |
| ---------           | ----------------------------- |
| Configuración (BCC) | `copy@dlgXX.ltu.minag.gob.cu` |


### DNS ###
###### Redireccionadores
 1. `172.16.65.2`  
 2. `172.16.64.1`

###### Dominios (Nombres de maquina) ######

| Parametro    | Valor                     |
| ------------ | ------------------------- |
| Alias (dc) | `mx` `correo` `webmail` `mail` `smtp` `imap` `pop` `proxy` `ldap` `xmpp`



### DHCP ###
Desabilitar dhcp en la interfaz WAN `eth0`

Configurar la interfaz LAN `eth1`
| Parametro           | Valor                         |
| ------------------- | ----------------------------- |
| Dominio de búsqueda | `Dominio de Zentyal`          |
| Rangos              | `192.168.0.25 - 192.168.0.50` |


### Reglas Firewal ###
#### Filtrado de paquetes ####
###### Desde redes internas hacia Zentyal ######

Editar los servicio  y cambiar origen `Culaquiera` por el `Objeto` `Localhost` creado.

| Decisión  | Origen      | Servicio        |
| --------- | ----------- | --------------- |
| `Aceptar` | `Localhost` | Envío de Correo |
| `Aceptar` | `Localhost` | Correo Entrante |
| `Aceptar` | `Localhost` | SMTP            |

Añadir una nueva regla y en servicio escoger `Mail Secure Port` creado.

| Decisión  | Origen      | Servicio        |
| --------- | ----------- | --------------- |
| `Aceptar` | `Cualquiera` | Mail Secure Port

###### Desde redes externas hacia Zentyal ######

| Decisión  | Origen           | Servicio     | Descripción      |
| --------- | ---------------- | ------------ | ---------------- |
| `Aceptar` | `172.16.xx.0/24` | `Cualquiera` | Admin-Eicmaltu   |
| `Aceptar` | `Cualquiera`     | `Cualquiera` | SOGo             |
| `Aceptar` | Objeto > `Relay` | `SMTP`       | Relays d Correos |



### Usuarios y Equipos ###
#### Gestionar ####
Crear Grupos y Usuarios y agregarlos a sus respectivos grupos:

###### Grupos ######

| Grupo     | Descripción                                  |
| ----------- | -------------------------------------------- |
| `nav-full`  | Grupo para navegación full sin restrinciones |
| `nav-int`   | Grupo para navegación  internacional         |
| `nav-nac`   | Grupo para navegación nacional               |
| `nav-segav` | Grupo para actualizar el antivirus           |

###### Usuarios ######

| Usuario  | Grupo       | Descripción                              |
| ------- | ----------- | ----------------------------------------- |
| `copy`  |             | Usuario para las copias de los correos    |
| `full`  | `nav-full`  | Usuario para testear proxy                |
| `int`   | `nav-int`   | Usuario para testear proxy y correo       |
| `nac`   | `nav-nac`   | Usuario para testear proxy y correo       | 
| `segav` | `nav-segav` | Usuario para la actualización segurmatica |


En la terminal ejecutar lo siguiente para eliminar la expiración de contraseña.
 ``` bash
samba-tool user setexpiry copy --noexpiry
samba-tool user setexpiry full --noexpiry
samba-tool user setexpiry int --noexpiry
samba-tool user setexpiry nac --noexpiry
samba-tool user setexpiry segav --noexpiry
```

### Proxy HTTP ###
* Marcar `Single Sign-On (Kerberos)`

#### Perfiles de Filtrado ####
Crear los siguientes perfiles: 

* `nav-full`  - Perfil para navegación sin restricciones.
* `nav-int`   - Perfil para navegacion con algunas restricciones.
* `nav-nac`   - Perfil para navegación nacional.
* `segav`     - Perfil para actualizar antivirus.

#### Reglas de acceso ####
Eliminar la regla por defecto y crear 3 nuevas reglsas.

| Origen (grupo de usuario) | Decisión (Aplicar perfil de filtrado) |
| ------------------------- | ------------------------------------- |
| `nav-full`	            | `nav-full`                            |
| `nav-int`			        | `nav-int`                             |
| `nav-nac`			        | `nav-nac`                             |
| `segav`		            | `segav`                               |


## Configuración por la terminal ##
### Proxy HTTP ###
Modificar archivo `squid.conf.mas`, pero antes le hacemos una copia.

```bash
cd /usr/share/zentyal/stubs/proxy/
cp squid.conf.mas{,.ori}
```

    nano squid.conf.mas

Buscamos la siguiente linea `## Access` y debajo agregar lo siguiente:

```bash
## Access
# Navegacion directa sin pasar por los proxy padre
acl dom-dir dstdomain -n .eicma.cu .minag.cu .minag.gob.cu
acl dom-dir dstdomain -n .geg.cu .gag.cu .ffauna.cu .gaf.cu
acl ip-dir dst 127.0.0.1 192.168.0.0/16 172.0.0.0/8
always_direct allow dom-dir
always_direct allow ip-dir
never_direct allow all

# Otras ACL
########################################

# ACL con dominio o palabras a bloquear
acl url-no url_regex -i pornohub.com xvideo.com xnxx.com amateur.tv xhamster.com redtube.com
acl url-no url_regex -i livejasmin.com serviporno.com bongacams.com teatroporno.com
acl dom-no dstdomain .dominio.a.bloquear

# ACL dominio segurmatica.cu y dominios cubano
acl dom-segav dstdomain .segurmatica.cu
acl dom-nac dstdomain .cu

# ACL con los grupos a verificar los usuarios
acl nav-segav external ldapgroup nav-segav
acl nav-nac external ldapgroup nav-nac
acl nav-int external ldapgroup nav-int
acl nav-full external ldapgroup nav-full

# Accesos
########################################

# Bloquear sin miedo a la ACL "url-no"
http_access deny url-no

# Permitir estos accesos
http_access allow nav-segav dom-segav
http_access allow nav-nac dom-nac
http_access allow nav-int !dom-no
http_access allow nav-full

# Denegar todo lo demas
http_access deny all
```

> ***No estoy seguro de esto:***  
Luego desabilitar `Single Sign-On (Kerberos)` en webadmin de Zentyal (Proxy HTTP)

### Correo ###
Modificamos archivo `main.conf.mas`, pero antes le hacemos una copia.

```bash
cd /usr/share/zentyal/stubs/mail/
cp main.cf.mas{,.ori}
```

    nano main.cf.mas

Buscamos y comentamos la línea `mynetworks = <% $allowed %>` y agregamos otra.

```bash
#mynetworks = <% $allowed %>
mynetworks = 127.0.0.0/8 192.168.0.0/24 172.16.0.0/16 172.17.0.0/16 172.18.0.0/16
```

### Repositorios ###

Por la terminal editamos el archivo `sources.list`

    nano /etc/apt/sources.list

Eliminamos todo y pegamos lo siguiente:

```bash
deb [trusted=yes] file:/home/zen/zentyal-packages ./
deb http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse
#deb [trusted=yes] http://ppa.launchpad.net/oisf/suricata-stable/ubuntu focal main
deb http://security.ubuntu.com/ubuntu focal-security main restricted universe multiverse
deb http://packages.zentyal.org/zentyal 7.0 main extra
```

### Notas ###

```bash
auth_param negotiate program /usr/lib/squid/negotiate_kerberos_auth -i -s HTTP/zentyal.dlglt.ltu.minag.gob.cu@DLGLT.LTU.MINAG.GOB.CU
auth_param negotiate children 10
auth_param negotiate keep_alive on

-v3 LDAP version
-b  base "dn" dónde buscar grupos
-p  Puerto
-D  DN a bind DN para realizar búsquedas
-w  leer la contraseña para binddn del archivo secretfile
-P  Conexión persistente a LDAP
-F  patrón de filtro de búsqueda de usuario. %s = login
-f  patrón de filtro de búsqueda de grupo. %u = user, %v = group

external_acl_type ldapgroup ipv4 %LOGIN /usr/lib/squid/ext_ldap_group_acl -v3 -b DC=dlglt,DC=ltu,DC=minag,DC=gob,DC=cu -p 3268 -D CN=zentyal-squid-zentyal,CN=Users,DC=dlglt,DC=ltu,DC=minag,DC=gob,DC=cu -w AueZ2thPZiTt@ZVkUotf -P -F "(&(userPrincipalName=%s)(objectclass=user))" -f "(&(samAccountName=%g)(objectclass=group)(member=%u))"
```