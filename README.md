## Instalación

```bash
npm install
```
### Variables de entorno
Crear archivo `.env` en la raíz:
```env
MONGODB_URI=mongodb://localhost:27017/ecommerce
JWT_SECRET=tu-clave-secreta
PORT=8080
NODE_ENV=development
```

## Iniciar servidor
```bash
npm start
```

Servidor disponible en: `http://localhost:8080`

## Endpoints de Mocking
### 1. Generar Mascotas Mock
```http
GET /api/mocks/mockingpets?count=N
```
**Parámetros:**
- `count` (opcional): Cantidad de mascotas a generar. Default: 100, Máximo: 1000
**Respuesta:**
```json
{
  "status": "success",
  "payload": [
    {
      "name": "Max",
      "species": "dog",
      "birthDate": "2020-05-15T00:00:00.000Z",
      "adopted": false,
      "owner": null,
      "image": "https://loremflickr.com/640/480/dog"
    }
  ],
  "count": 100
}
```
**Ejemplo:**
```bash
curl http://localhost:8080/api/mocks/mockingpets?count=50
```


### 2. Generar Usuarios Mock
```http
GET /api/mocks/mockingusers
```
Genera **50 usuarios** con las siguientes características:
- Password: `coder123` (encriptado con bcrypt)
- Role: aleatorio entre `user` y `admin`
- Pets: array vacío `[]`
**Respuesta:**
```json
{
  "status": "success",
  "payload": [
    {
      "first_name": "Juan",
      "last_name": "Pérez",
      "email": "juan.perez@email.com",
      "age": 28,
      "password": "$2b$10$...",
      "role": "user",
      "pets": []
    }
  ],
  "count": 50
}
```
**Ejemplo:**
```bash
curl http://localhost:8080/api/mocks/mockingusers
```

---

### 3. Generar e Insertar Datos en Base de Datos
```http
POST /api/mocks/generateData
```

**Requiere autenticación como administrador**
**Body:**
```json
{
  "users": 10,
  "pets": 20
}
```
**Parámetros:**
- `users`: Cantidad de usuarios a insertar (opcional)
- `pets`: Cantidad de mascotas a insertar (opcional)
**Validaciones:**
- Cantidad mínima: 0
- Cantidad máxima: 1000 por tipo
- Debe proporcionar al menos uno de los parámetros
**Respuesta:**
```json
{
  "status": "success",
  "message": "Datos generados e insertados exitosamente",
  "payload": {
    "generated": {
      "users": {
        "count": 10,
        "success": true
      },
      "pets": {
        "count": 20,
        "success": true
      }
    },
    "currentTotals": {
      "users": 12,
      "pets": 25
    }
  }
}
```
**Ejemplo:**
```bash
# 1. Login como admin
curl -X POST http://localhost:8080/api/sessions/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@ecommerce.com","password":"admin123"}'

# 2. Guardar el token recibido y usarlo en la siguiente petición
curl -X POST http://localhost:8080/api/mocks/generateData \
  -H "Content-Type: application/json" \
  -H "Cookie: token=TU_TOKEN_AQUI" \
  -d '{"users":10,"pets":20}'
```


## Verificación de Datos Insertados
### Listar Usuarios
```http
GET /api/users
```

Requiere autenticación como administrador.

**Ejemplo:**
```bash
curl http://localhost:8080/api/users \
  -H "Cookie: token=TU_TOKEN_AQUI"
```

### Listar Mascotas
```http
GET /api/pets
```
No requiere autenticación.

**Ejemplo:**
```bash
curl http://localhost:8080/api/pets
```

## Pruebas Rápidas Requests manuales
```bash
# Generar usuarios 
curl http://localhost:8080/api/mocks/mockingusers | jq

# Generar mascotas 
curl http://localhost:8080/api/mocks/mockingpets?count=10 | jq

# Ver todas las mascotas
curl http://localhost:8080/api/pets | jq
```

### Opción 2: Script automatizado

```bash
bash test-api.sh
```

## Usuarios de Prueba

El sistema incluye usuarios pre-configurados:

**Administrador:**

Email: admin@ecommerce.com
Password: admin123


**Usuario normal:**

Email: user@ecommerce.com
Password: user123


**Usuarios mock generados:**

Email: [generado por faker]
Password: coder123


## Funcionalidades Adicionales
### Endpoint de desarrollo (solo en modo dev)

POST /api/mocks/generateData-public
Permite insertar datos sin autenticación en modo desarrollo.

### Limpieza de datos de prueba

DELETE /api/mocks/clear
Elimina todos los datos mock generados (requiere admin).

## Notas Importantes

1. *Límites de generación:*
   - Mínimo: 1 registro
   - Máximo: 1000 registros por tipo

2. *Usuarios duplicados:*
   - El sistema verifica emails duplicados
   - Solo inserta usuarios con emails únicos

3. *Carritos:*
   - Se crea un carrito automáticamente por cada usuario insertado

4. *Autenticación:*
   - `/generateData` requiere token JWT de administrador
   - Los tokens se envían en cookies httpOnly

---

Para más información, revisar la documentación en:
- `/api/status` - Estado del servidor y endpoints disponibles

**Backend 3 - CoderHouse**
