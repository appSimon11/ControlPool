# Pool Console

Dashboard privado para capturar ganancias diarias por pool, detectar resets, consultar estadísticas, forecast e importar/exportar CSV.

## Usuarios iniciales

| Usuario | Contraseña | Pools iniciales |
| --- | --- | --- |
| Andres | `Andres$` | `rojo`, `negro` |
| Sandra | `Sandra$` | `UNI` |

## Reglas de negocio

- El dato capturado es el acumulado visible del pool.
- Si el acumulado de hoy es menor que el registro anterior del mismo pool, se marca como pool modificado/reset.
- En reset, la ganancia diaria es el valor visible nuevo.
- El acumulado ajustado sigue sumando históricamente. Ejemplo: ayer `100`, hoy `2` => ganancia diaria `2`, acumulado ajustado `102`.
- Cada usuario ve solamente sus propios datos.
- Todos los valores están en dólares.

## Estructura

```txt
pool-console-railway/
  public/          Interfaz web
  sql/schema.sql   Esquema MySQL
  server.js        API, login y conexión MySQL
  package.json     Scripts y dependencias
  .env.example     Variables de entorno ejemplo
```

## Desarrollo local

1. Instala Node.js 20 o superior.
2. Crea una base MySQL local.
3. Copia `.env.example` a `.env`.
4. Llena las variables de MySQL en `.env`.
5. Instala dependencias:

```bash
npm install
```

6. Arranca la app:

```bash
npm run dev
```

7. Abre:

```txt
http://localhost:3000
```

El servidor crea automáticamente las tablas y usuarios iniciales al arrancar. El archivo `sql/schema.sql` queda como referencia o para correrlo manualmente si prefieres.

## Subir a GitHub

1. Entra a la carpeta del proyecto:

```bash
cd pool-console-railway
```

2. Inicializa Git:

```bash
git init
```

3. Agrega los archivos:

```bash
git add .
```

4. Crea el primer commit:

```bash
git commit -m "Initial Pool Console app"
```

5. Crea un repositorio vacío en GitHub.

6. Conecta tu repo local con GitHub:

```bash
git remote add origin https://github.com/TU-USUARIO/TU-REPO.git
```

7. Sube la rama principal:

```bash
git branch -M main
git push -u origin main
```

## Crear Railway con MySQL

1. Entra a Railway.
2. Crea un nuevo proyecto.
3. Elige `Deploy from GitHub repo`.
4. Selecciona el repositorio que acabas de subir.
5. Agrega una base de datos MySQL dentro del mismo proyecto.
6. En el servicio web de la app, abre la sección `Variables`.
7. Agrega las variables de conexión tomando los valores del servicio MySQL.

Railway documenta que el servicio MySQL expone variables como `MYSQLHOST`, `MYSQLPORT`, `MYSQLUSER`, `MYSQLPASSWORD`, `MYSQLDATABASE` y `MYSQL_URL`.

## Variables para Railway

En el servicio web de la app, configura:

```txt
NODE_ENV=production
SESSION_SECRET=pon-aqui-un-texto-largo-privado
MYSQLHOST=${{ MySQL.MYSQLHOST }}
MYSQLPORT=${{ MySQL.MYSQLPORT }}
MYSQLUSER=${{ MySQL.MYSQLUSER }}
MYSQLPASSWORD=${{ MySQL.MYSQLPASSWORD }}
MYSQLDATABASE=${{ MySQL.MYSQLDATABASE }}
```

Alternativa: si Railway te muestra `MYSQL_URL`, también puedes usar solo:

```txt
NODE_ENV=production
SESSION_SECRET=pon-aqui-un-texto-largo-privado
MYSQL_URL=${{ MySQL.MYSQL_URL }}
```

No configures `PORT` en Railway salvo que tengas una razón específica. Railway suele inyectarlo automáticamente.

## Deploy

Railway detecta `package.json` y ejecuta:

```bash
npm install
npm start
```

Al arrancar, la app:

- crea las tablas si no existen
- crea los usuarios `Andres` y `Sandra`
- crea los pools iniciales `rojo`, `negro` y `UNI`

## CSV

Para importar datos en batch, puedes usar archivo o pegar el contenido directamente en la cuarta sección.

Formato recomendado:

```csv
date,pool,total
2026-05-23,rojo,1000
2026-05-23,negro,850
```

- `date`: formato `AAAA-MM-DD`
- `pool`: nombre del pool
- `total`: acumulado visible del pool ese día

También acepta:

- columnas `fecha,pool,acumulado`
- separador por coma, punto y coma o tabulador
- fechas `AAAA-MM-DD`, `AAAA/MM/DD`, `DD/MM/AAAA` o `DD-MM-AAAA`
- números con punto o coma decimal

La app previsualiza nuevos registros, reemplazos y errores antes de guardar.

## Fuentes Railway

- [Railway MySQL Docs](https://docs.railway.com/databases/mysql)
- [Railway Variables Docs](https://docs.railway.com/variables)
