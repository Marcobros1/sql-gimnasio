# sql-gimnasio

Diseño y consultas de una base de datos para el gimnasio FitVal, en PostgreSQL 17.

## Contenido

- `gimnasio.sql`: script completo, ejecutable de una vez. Incluye:
  - **B1.** Creación de las 8 tablas.
  - **B2.** Carga de datos de prueba.
  - **B3.** Instrucciones que rompen la base a propósito para comprobar las claves.
  - **B4.** Consultas con `JOIN`, `LEFT JOIN`, `GROUP BY`.
  - **B5.** Actualizaciones y borrados.
- `respuestas.md`: respuestas a la parte A (modelado), B3 (resultados de los errores), B4.11 y B6.

## Modelo

```text
planes 1 ──── N socios 1 ──── 1 tarjetas
                   │
                   1
                   │
                   N
               reservas
                   N
                   │
                   1
monitores 1 ──── N clases

monitores 1 ──── N monitor_especialidades N ──── 1 especialidades
```

## Ejecución

Con PostgreSQL en Docker (puerto 5433 en el host):

```bash
docker exec -i postgres-sql psql -U alumno -d gimnasio -f - < gimnasio.sql
```

En PowerShell, copia el archivo dentro del contenedor y ejecútalo desde allí, para que los acentos no se corrompan:

```powershell
docker cp .\gimnasio.sql postgres-sql:/tmp/gimnasio.sql
docker exec postgres-sql psql -U alumno -d gimnasio -f /tmp/gimnasio.sql
```

Antes, crea la base de datos si no existe:

```bash
docker exec postgres-sql psql -U alumno -d academia -c "CREATE DATABASE gimnasio;"
```

Algunas sentencias de B3 y B5 fallan a propósito. Los errores que aparecen en la salida son esperados.