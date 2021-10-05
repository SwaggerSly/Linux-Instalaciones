# Fedora 33+ | Instalación de Postgresql 13

```
$ sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/F-34-x86_64/pgdg-fedora-repo-latest.noarch.rpm
$ sudo dnf install -y postgresql13-server
$ sudo /usr/pgsql-13/bin/postgresql-13-setup initdb
$ sudo systemctl enable postgresql-13
$ sudo systemctl start postgresql-13
```

## Post-installation

```
# Cambiar contraseña del usuario Postgres
$ sudo passwd postgres
```

## Entrar al usuario Postgres

```
$ su - postgres

# Ejecutar la herramienta de administracion por consola para cambiar contraseña

$ psql
$ ALTER USER postgres WITH password 'xxxx';
```


## Instalación de PGAdmin

```
$ sudo rpm -i https://ftp.postgresql.org/pub/pgadmin/pgadmin4/yum/pgadmin4-fedora-repo-2-1.noarch.rpm
$ sudo yum install pgadmin4-desktop
```

## Post-installation de PgAdmin

Para tener acceso remoto a PostgreSQL es necesario editar dos archivos de configuración: postgresql.conf y pg_hba.conf

```
# Configuración del archivo "postgresql.conf" mediante "nano"
$ sudo nano /var/lib/pgsql/13/data/postgresql.conf

# Se buscará la linea con la  siguiente instrucción  "  #listen_addresses=’listen’  "
# Se modificara de la siguiente manera
listen_addresses = '*'

# Configuración del archivo "pg_hba.conf" mediante "nano"
$ sudo nano /var/lib/pgsql/13/data/pg_hba.conf

# Se modificara la linea que contiene la conexion de "IPv4"
# host         all         all         127.0.0.1/32        scram-sha-256 
# A 
host         all         all         127.0.0.1/32         md5

# Además se agregará una nueva conexión debajo de la linea anterior con la IP del Servidor Postgresql
host         all         all         xxx.xxx.xxx.xxx/32   md5
```
## psql: error: FATAL:  la autentificación Peer falló para el usuario «nuevo_usuario»

Para arreglar este error es necesario entrar al archivo de configuración `sudo nano /var/lib/pgsql/`tu_version_postgres`/data/pg_hba.conf`.

El cual mostrará algo como esto:
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host    all             all             xxx.xxx.x.xx/24         md5
# IPv6 local connections:
host    all             all             ::1/128                 ident
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     md5
host    replication     all             127.0.0.1/32            ident
host    replication     all             ::1/128                 ident
```
Por lo tanto es recomendable realizar los siguientes cambios:
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host    all             all             xxx.xxx.x.xx/24         md5
# IPv6 local connections:
host    all             all             ::1/128                 ident
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     md5
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5
```
Por último queda reiniciar los servicios de postgres con el siguiente comando:
```
sudo service postgresql-12 restart
```
