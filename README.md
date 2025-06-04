# ProjectSync - Sistema de Gestión de Proyectos

## Proyecto de Desarrollo de Aplicaciones Web (DAW)
**Autor:** Raul Gonzalez  
**Título:** Sistema de Gestión de Proyectos con React y Symfony  
**Centro:** IES Hermenegildo Lanz  
**Módulo:** Desarrollo de Aplicaciones Web  
**Curso:** 2024-2025

## Descripción
ProjectSync es una aplicación web moderna para la gestión de proyectos, desarrollada como proyecto del módulo de Desarrollo de Aplicaciones Web. El sistema permite gestionar usuarios, clientes, proyectos y archivos asociados, ofreciendo una interfaz intuitiva y funcionalidades avanzadas de gestión.

## Estructura del Proyecto
El proyecto está dividido en tres partes principales:
- `ProjectSync-Frontend/`: Aplicación React con Vite
- `TFG_Backend/`: API REST con Symfony
- `nginx/`: Configuración del servidor web y proxy inverso

## Requisitos Previos
- Docker y Docker Compose
  - Docker Engine 24.0 o superior
  - Docker Compose 2.20 o superior

## Instalación y Configuración

### 1. Clonar el Repositorio
```bash
git clone [URL_DEL_REPOSITORIO]
cd Proyecto-DAW
```

### 2. Configuración del Entorno
1. Copiar el archivo de entorno del backend:
```bash
cp TFG_Backend/.env.example TFG_Backend/.env
```

2. Copiar el archivo de entorno del frontend:
```bash
cp ProjectSync-Frontend/.env.example ProjectSync-Frontend/.env
```

### 3. Iniciar con Docker
```bash
docker-compose up -d
```

### 4. Acceso a los Servicios
- Frontend: http://localhost
- Backend API: http://localhost/api
- phpMyAdmin: http://localhost:8080
  - Usuario: symfony
  - Contraseña: symfony

## Frontend (ProjectSync-Frontend)

### Tecnologías Utilizadas
- React 18
- Vite
- Tailwind CSS
- React Router
- Chart.js
- React Icons
- Axios
- React Toastify

### Instalación Manual
```bash
cd ProjectSync-Frontend
npm install
```

### Desarrollo
```bash
npm run dev
```
La aplicación estará disponible en `http://localhost:5173`

### Construcción
```bash
npm run build
```

### Características Implementadas
- Panel de administración con estadísticas en tiempo real
- Gestión de usuarios y roles (Admin/User)
- Gestión de clientes con importación desde CSV
- Gestión de proyectos con fechas de inicio/fin
- Sistema de archivos con subida/descarga individual y ZIP
- Interfaz responsive con tema visual personalizado
- Validación de formularios
- Notificaciones toast para feedback al usuario
- Gráficos estadísticos con Chart.js
- Protección de rutas basada en roles

## Backend (TFG_Backend)

### Tecnologías Utilizadas
- Symfony 7.2
- PHP 8.2
- MySQL 8.0
- JWT Authentication
- API Platform
- Doctrine ORM
- Nelmio CORS Bundle

### Instalación Manual
```bash
cd TFG_Backend
composer install
php bin/console doctrine:migrations:migrate
```

### Desarrollo
```bash
symfony server:start
```
La API estará disponible en `http://localhost:8000`

### Endpoints Implementados

#### Autenticación
- `POST /api/login`: Login de usuario
  - Body: `{ "email": "string", "password": "string" }`
  - Retorna: JWT token y datos del usuario
- `POST /api/register`: Registro de nuevos usuarios
  - Body: `{ "email": "string", "password": "string", "name": "string" }`

#### Usuarios
- `GET /api/users`: Listar usuarios (requiere ROLE_ADMIN)
- `GET /api/users/{id}`: Obtener usuario
- `PUT /api/users/{id}`: Actualizar usuario
- `DELETE /api/users/{id}`: Eliminar usuario (requiere ROLE_ADMIN)

#### Clientes
- `GET /api/clients`: Listar clientes
- `POST /api/clients`: Crear cliente
  - Body: `{ "name": "string", "cif": "string", "email": "string", "phone": "string" }`
- `GET /api/clients/{id}`: Obtener cliente
- `PUT /api/clients/{id}`: Actualizar cliente
- `DELETE /api/clients/{id}`: Eliminar cliente
- `POST /api/clients/import`: Importar clientes desde CSV
  - Body: FormData con archivo CSV
  - Requiere campos: nombre, cif

#### Proyectos
- `GET /api/projects`: Listar proyectos
- `POST /api/projects`: Crear proyecto
  - Body: `{ "title": "string", "description": "string", "fechaInicio": "date", "fechaFin": "date", "client": "id" }`
- `GET /api/projects/{id}`: Obtener proyecto
- `PUT /api/projects/{id}`: Actualizar proyecto
- `DELETE /api/projects/{id}`: Eliminar proyecto

#### Archivos
- `POST /api/projects/{id}/files`: Subir archivo
  - Body: FormData con archivo
- `GET /api/projects/{id}/files`: Listar archivos
- `GET /api/projects/{id}/files/{fileId}`: Descargar archivo
- `DELETE /api/projects/{id}/files/{fileId}`: Eliminar archivo
- `GET /api/projects/{id}/files/download-all`: Descargar todos los archivos (ZIP)

## Roles y Permisos
- `ROLE_ADMIN`: Acceso completo al sistema
  - Gestión de usuarios
  - Gestión de clientes
  - Gestión de proyectos
  - Acceso al panel de administración
- `ROLE_USER`: Acceso básico
  - Ver y gestionar proyectos asignados
  - Ver clientes
  - Gestionar archivos de proyectos

## Características de Seguridad
- Autenticación JWT con expiración de 24 horas
- Validación de datos en frontend y backend
- Protección CORS configurada
- Cifrado de contraseñas con bcrypt
- Control de acceso basado en roles
- Validación de tokens JWT
- Sanitización de entradas
- Protección contra ataques XSS y CSRF

## Estructura de Docker
El proyecto utiliza Docker Compose para orquestar los siguientes servicios:

### Servicios
1. **nginx** (Puerto 80)
   - Servidor web y proxy inverso
   - Configuración en `nginx/default.conf`
   - Redirige el tráfico al frontend y backend
   - Maneja las cabeceras CORS

2. **frontend** (Interno)
   - Aplicación React construida con Node.js
   - Construcción en dos etapas (build y producción)
   - Volúmenes:
     - `./ProjectSync-Frontend:/app`: Código fuente
     - `/app/node_modules`: Dependencias

3. **symfony-backend** (Interno, puerto 9000)
   - API Symfony con PHP-FPM 8.2
   - Volúmenes:
     - `./TFG_Backend:/var/www`: Código fuente
     - `/var/www/var/cache`: Caché de Symfony
     - `/var/www/var/log`: Logs de Symfony
   - Variables de entorno:
     - `DATABASE_URL`: Conexión MySQL
     - `APP_ENV`: Entorno (dev/prod)
     - `JWT_SECRET_KEY`: Clave privada JWT
     - `JWT_PUBLIC_KEY`: Clave pública JWT
     - `JWT_PASSPHRASE`: Frase de paso JWT

4. **mysql** (Puerto 3306)
   - Base de datos MySQL 8.0
   - Volúmenes:
     - `db_data:/var/lib/mysql`: Datos persistentes
   - Variables de entorno:
     - `MYSQL_DATABASE`: symfony
     - `MYSQL_USER`: symfony
     - `MYSQL_PASSWORD`: symfony
     - `MYSQL_ROOT_PASSWORD`: root

5. **phpmyadmin** (Puerto 8080)
   - Interfaz web para gestionar MySQL
   - Acceso: http://localhost:8080
   - Credenciales:
     - Usuario: symfony
     - Contraseña: symfony

### Variables de Entorno
El proyecto utiliza las siguientes variables de entorno (valores por defecto):
```env
# MySQL
MYSQL_DATABASE=symfony
MYSQL_USER=symfony
MYSQL_PASSWORD=symfony
MYSQL_ROOT_PASSWORD=root

# Symfony
APP_ENV=dev
APP_DEBUG=1
JWT_PASSPHRASE=projectsync
APP_SECRET=projectsync_secret_key
CORS_ALLOW_ORIGIN='^https?://(localhost|127\.0\.0\.1)(:[0-9]+)?$'

# Frontend
VITE_URL_API=http://localhost
```

### Comandos Docker Útiles
```bash
# Iniciar todos los servicios
docker-compose up --build -d

# Ver logs de todos los servicios
docker-compose logs -f

# Ver logs de un servicio específico
docker-compose logs -f [servicio]

# Detener todos los servicios
docker-compose down

# Reconstruir un servicio
docker-compose up -d --build [servicio]

# Acceder al shell de un contenedor
docker-compose exec [servicio] sh

# Ver estado de los contenedores
docker-compose ps
```

### Volúmenes
- `db_data`: Persistencia de la base de datos MySQL
- Volúmenes de código fuente para desarrollo en caliente
- Volúmenes de caché y logs para Symfony

### Redes
- Red por defecto de Docker Compose
- Todos los servicios pueden comunicarse entre sí usando el nombre del servicio como hostname

### Puertos Expuestos
- `80`: Nginx (Frontend + API)
- `8080`: phpMyAdmin
- `3306`: MySQL (solo desarrollo)

## Contribución
Este proyecto es parte del módulo de Desarrollo de Aplicaciones Web y no está abierto a contribuciones externas.

## Licencia
Todos los derechos reservados © 2025 Raul Gonzalez

## Troubleshooting (Solución de Problemas)

### Problemas Comunes y Soluciones

#### 1. Problemas de Inicio
- **Error**: "Port 80 is already in use"
  - **Solución**: 
    ```bash
    # Ver qué proceso usa el puerto 80
    netstat -ano | findstr :80
    # Detener el proceso (reemplazar PID con el número de proceso)
    taskkill /PID <PID> /F
    ```

- **Error**: "Cannot connect to MySQL"
  - **Solución**: 
    ```bash
    # Reiniciar solo el servicio de MySQL
    docker-compose restart mysql
    # Ver logs de MySQL
    docker-compose logs mysql
    ```

#### 2. Problemas de Desarrollo
- **Error**: "Changes in frontend not reflecting"
  - **Solución**: 
    ```bash
    # Reconstruir el frontend
    docker-compose up -d --build frontend
    # Ver logs del frontend
    docker-compose logs -f frontend
    ```

- **Error**: "API not responding"
  - **Solución**:
    ```bash
    # Verificar que el backend está corriendo
    docker-compose ps
    # Reiniciar el backend
    docker-compose restart symfony-backend
    # Ver logs del backend
    docker-compose logs -f symfony-backend
    ```

#### 3. Problemas de Base de Datos
- **Error**: "Database connection failed"
  - **Solución**:
    ```bash
    # Verificar credenciales en .env
    cat TFG_Backend/.env | grep DATABASE_URL
    # Reiniciar MySQL
    docker-compose restart mysql
    ```

- **Error**: "Cannot access phpMyAdmin"
  - **Solución**:
    - Verificar que el puerto 8080 no está en uso
    - Comprobar credenciales en docker-compose.yml
    - Reiniciar el servicio: `docker-compose restart phpmyadmin`

#### 4. Problemas de Autenticación
- **Error**: "JWT Token not found"
  - **Solución**:
    - Verificar que las claves JWT existen:
      ```bash
      docker-compose exec symfony-backend ls -l config/jwt/
      ```
    - Regenerar claves JWT:
      ```bash
      docker-compose exec symfony-backend rm config/jwt/*.pem
      docker-compose restart symfony-backend
      ```

- **Error**: "Invalid credentials"
  - **Solución**:
    - Verificar que el usuario existe en la base de datos
    - Comprobar que la contraseña es correcta
    - Intentar resetear la contraseña desde phpMyAdmin

#### 5. Problemas de Archivos
- **Error**: "Cannot upload files"
  - **Solución**:
    - Verificar permisos del directorio de uploads:
      ```bash
      docker-compose exec symfony-backend ls -l public/FilesRepos
      ```
    - Ajustar permisos si es necesario:
      ```bash
      docker-compose exec symfony-backend chmod -R 777 public/FilesRepos
      ```

- **Error**: "Cannot download files"
  - **Solución**:
    - Verificar que el archivo existe en el servidor
    - Comprobar permisos del archivo
    - Verificar que el token JWT es válido

#### 6. Problemas de CORS
- **Error**: "CORS error in browser console"
  - **Solución**:
    - Verificar configuración CORS en nginx/default.conf
    - Comprobar CORS_ALLOW_ORIGIN en .env
    - Reiniciar nginx: `docker-compose restart nginx`

### Comandos Útiles para Diagnóstico
```bash
# Ver estado de todos los contenedores
docker-compose ps

# Ver logs en tiempo real
docker-compose logs -f

# Ver uso de recursos
docker stats

# Limpiar contenedores no utilizados
docker system prune

# Verificar red de Docker
docker network ls
docker network inspect proyectosync_default

# Verificar volúmenes
docker volume ls
docker volume inspect proyectosync_db_data
```

### Contacto y Soporte
Si encuentras algún problema no listado aquí o necesitas ayuda adicional:
1. Revisa los logs del servicio específico
2. Verifica la configuración en los archivos .env
3. Comprueba que todos los servicios están corriendo
4. Asegúrate de que los puertos necesarios están disponibles

## Documentación de Entrega

### Credenciales de Acceso

#### Usuarios Predeterminados
1. **Administrador**
   - Email: admin@projectsync.com
   - Contraseña: admin123
   - Rol: ROLE_ADMIN
   - Acceso completo al sistema

2. **Usuario Normal**
   - Email: user@projectsync.com
   - Contraseña: user123
   - Rol: ROLE_USER
   - Acceso básico a proyectos

#### Acceso a Servicios
1. **Frontend**
   - URL: http://localhost
   - No requiere credenciales adicionales

2. **Backend API**
   - URL: http://localhost/api
   - Requiere token JWT (se obtiene al hacer login)

3. **phpMyAdmin**
   - URL: http://localhost:8080
   - Usuario: symfony
   - Contraseña: symfony

### Puertos Utilizados
| Servicio | Puerto | Descripción |
|----------|---------|-------------|
| Nginx | 80 | Frontend + API |
| MySQL | 3306 | Base de datos |
| phpMyAdmin | 8080 | Gestión de base de datos |
| Symfony Backend | 9000 | Interno (no expuesto) |
| Frontend | 5173 | Solo desarrollo local |

### Instrucciones de Uso

#### 1. Entorno Local con Docker
```bash
# 1. Clonar el repositorio
git clone [URL_DEL_REPOSITORIO]
cd Proyecto-DAW

# 2. Configurar variables de entorno
cp TFG_Backend/.env.example TFG_Backend/.env
cp ProjectSync-Frontend/.env.example ProjectSync-Frontend/.env

# 3. Iniciar servicios
docker-compose up --build -d

# 4. Verificar que todos los servicios están corriendo
docker-compose ps
```

#### 2. Probar la Aplicación
1. **Acceder al Frontend**
   - Abrir http://localhost en el navegador
   - Iniciar sesión con las credenciales de administrador
   - Verificar que se puede acceder al panel de administración

2. **Probar Funcionalidades Básicas**
   - Crear un nuevo cliente
   - Crear un nuevo proyecto
   - Subir archivos al proyecto
   - Descargar archivos
   - Importar clientes desde CSV

3. **Probar Roles de Usuario**
   - Cerrar sesión
   - Iniciar sesión como usuario normal
   - Verificar restricciones de acceso

### Datos de Prueba

#### 1. Clientes de Ejemplo
```csv
nombre,cif,email,phone
Empresa A,12345678A,empresaA@example.com,912345678
Empresa B,87654321B,empresaB@example.com,987654321
```

#### 2. Proyectos de Ejemplo
```json
{
  "title": "Proyecto de Prueba",
  "description": "Proyecto para testing",
  "fechaInicio": "2024-03-01",
  "fechaFin": "2024-06-30",
  "client": 1
}
```

#### 3. Archivos de Prueba
- Tipos permitidos: .pdf, .doc, .docx, .xls, .xlsx, .txt
- Tamaño máximo: 10MB por archivo
- Ubicación de prueba: `TFG_Backend/public/FilesRepos`

### Notas Adicionales

#### 1. Requisitos del Sistema
- Docker Engine 24.0 o superior
- Docker Compose 2.20 o superior
- 4GB de RAM mínimo
- 10GB de espacio en disco

#### 2. Consideraciones de Seguridad
- Las credenciales por defecto deben cambiarse en producción
- El token JWT expira en 24 horas
- Los archivos subidos se validan por tipo y tamaño
- Las contraseñas se almacenan hasheadas con bcrypt

#### 3. Desarrollo Local
- El frontend se reconstruye automáticamente al modificar archivos
- Los cambios en el backend requieren reinicio del contenedor
- Los logs se pueden ver con `docker-compose logs -f [servicio]`

#### 4. Solución de Problemas
- Ver sección de [Troubleshooting](#troubleshooting-solución-de-problemas)
- Los logs de cada servicio están disponibles en sus respectivos contenedores
- La base de datos se puede gestionar a través de phpMyAdmin

#### 5. Mantenimiento
- Realizar backups periódicos de la base de datos
- Limpiar archivos temporales y logs
- Actualizar las dependencias regularmente
- Monitorear el uso de recursos