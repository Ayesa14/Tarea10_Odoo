# Instalación Odoo Community v.17

Guía que muestra los pasos para instalar Odoo, PostgreSQL y PgAdmin

## Creación del archivo compose

```bash
# Montar una carpeta para almacenar el archivo compose.yml
mkdir compose_Odoo

# Colocarse en la carpeta recien creada
cd compose_Odoo

# Creación del archivo compose.yml
nano docker-compose.yml
```

Contenido del archivo

```yml
services:
  odoo:                                    # Define el servicio Odoo
    image: odoo:17.0                       # Especifica la imagen de Docker para Odoo, versión 17.0.
    depends_on:                            # Asegura que el servicio de base de datos se inicie antes que el servicio Odoo
      - db
    restart: unless-stopped                # El contenedor se reiniciara automáticamente a menos que se detenga explícitamente
    ports:
      - "8055:8069"                        # Asigna el puerto predeterminado de Odoo al puerto 8055
    environment:
      HOST: db                             # El nombre de host para el servicio de base de datos
      USER: admin                          # El nombre de usuario para la base de datos
      PASSWORD: pswd                       # La contraseña para la base de datos

  db:                                      # Define el servicio de base de datos PostgreSQL
    image: postgres:latest                 # Especifica la imagen de Docker para PostgreSQL, ultima versión
    restart: unless-stopped                # El contenedor se reiniciara automáticamente a menos que se detenga explícitamente
    environment:                 
      POSTGRES_DB: odoo                    # El nombre de la base de datos predeterminada
      POSTGRES_PASSWORD: pswd              # La contraseña para el usuario de PostgreSQL
      POSTGRES_USER: admin                 # El nombre de usuario de PostgreSQL
      PGDATA: /var/lib/postgresql/data/pgdata      # Especifica la ruta dónde PostgreSQL almacena los datos dentro del contenedor
    volumes:                               # Monta un volumen para conservar los datos de la base de datos
      - odoo-db-data:/var/lib/postgresql/data/pgdata

  pgadmin:                                 # Define el servicio pgAdmin
    image: dpage/pgadmin4                  # Especifica la imagen de Docker para pgAdmin
    restart: unless-stopped                # El contenedor se reiniciara automáticamente a menos que se detenga explícitamente
    ports:                                 # Asigna el puerto predeterminado de interfaz web pgAdmin al puerto 8065
      - "8065:80"      
    environment:        
      PGADMIN_DEFAULT_EMAIL: admin@example.com     # El correo electrónico de inicio de sesión para pgAdmin
      PGADMIN_DEFAULT_PASSWORD: admin      # La contraseña para el usuario pgAdmin
    depends_on:                            # Asegura que el servicio de base de datos se inicie antes que el servicio pgAdmin
      - db
    volumes:                               # Monta un volumen para conservar los datos de pgAdmin
      - pgadmin-data:/var/lib/pgadmin

volumes:                                   # Define los volúmenes declarados en los servicios
  odoo-db-data:
  pgadmin-data:
```
Puesta en marcha
    
```bash
# Ejecución en segundo plano (deja libre la terminal)
docker compose up -d

# Ejecución en primer plano  (muestra el log de los contenedores)
docker compose up 
```