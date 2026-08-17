# Dashboard de Capacitación — Metelmex

Aplicación web para registrar cursos de capacitación, controlar vencimientos
y llevar el acumulado de horas por empleado. Datos guardados en Google
Sheets; interfaz publicada en GitHub Pages.

Archivos:
- `index.html` → interfaz (frontend). Esto es lo que se sube a GitHub Pages.
- `Code.gs` → backend (Google Apps Script). Esto se pega en el editor de
  Apps Script del Google Sheet.

---

## Paso 1 — Crear el Google Sheet

1. Crea un Google Sheet nuevo (puede llamarse, por ejemplo,
   **"Capacitación Metelmex — BD"**).
2. No necesitas crear las pestañas a mano: el script las crea solas
   (`Empleados`, `Cursos`, `Participaciones`) la primera vez que corre.

## Paso 2 — Publicar el backend (Apps Script)

1. En el Sheet: **Extensiones > Apps Script**.
2. Borra el contenido de `Código.gs` y pega todo el contenido de
   `Code.gs` (incluido en este paquete).
3. Guarda (ícono de disco o Ctrl+S).
4. Click en **Implementar > Nueva implementación**.
5. En "Seleccionar tipo", elige **Aplicación web**.
6. Configura:
   - **Ejecutar como:** Yo (tu cuenta)
   - **Quién tiene acceso:** Cualquier usuario (o "Cualquier usuario con
     Google" si quieres restringirlo, aunque con "Cualquier usuario" es
     lo que suele funcionar mejor con GitHub Pages)
7. Click en **Implementar**. Google pedirá autorizar permisos la primera
   vez (acepta, es tu propio script accediendo a tu propio Sheet).
8. Copia la **URL de la aplicación web** que te da (termina en `/exec`).

> Si más adelante modificas `Code.gs`, debes volver a
> **Implementar > Gestionar implementaciones > ✏️ Editar > Nueva
> versión > Implementar** para que los cambios se reflejen en la URL.

## Paso 3 — Conectar el frontend con tu Sheet

No necesitas editar `index.html`. Una vez publicado el dashboard (Paso 4):

1. Abre el dashboard en el navegador.
2. Click en **⚙️ Configuración** (arriba a la derecha).
3. Pega la URL que copiaste en el Paso 2 (la que termina en `/exec`).
4. Click en **Guardar y conectar**.

La URL queda guardada en el navegador (localStorage), así que solo la
configuras una vez por dispositivo/navegador. Si algún día cambias de
implementación de Apps Script, vuelve a abrir Configuración y actualiza
la URL ahí.

## Paso 4 — Subir a GitHub Pages

1. Crea un repositorio nuevo en GitHub (puede ser público o privado con
   GitHub Pro/Team para Pages privado).
2. Sube `index.html` a la raíz del repositorio (arrastra el archivo desde
   la interfaz web de GitHub, o usa `git push` si prefieres línea de
   comandos).
3. Ve a **Settings > Pages** del repositorio.
4. En "Source", selecciona la rama (`main`) y carpeta `/ (root)`.
5. Guarda. En 1-2 minutos tu dashboard estará disponible en:
   `https://<tu-usuario>.github.io/<nombre-del-repo>/`

---

## Cargar el catálogo de empleados desde Excel

En la pestaña **Empleados y horas** hay un botón **📤 Subir catálogo** que
acepta el archivo con las columnas `CODIGO`, `NOMBRE_COMPLETO`, `NOMBRE`,
`APELLIDOPATERNO`, `APELLIDOMATERNO`, `DEPARTAMENTO`, `PUESTO`, `CURP`,
`ESTATUS`, `ESCOLARIDAD` y, sin encabezado, Ciudad / Estado / Tipo de
escuela / Documento de escolaridad (en ese orden, justo después de
`ESCOLARIDAD`).

- Solo se usa `NOMBRE_COMPLETO` para el nombre — las columnas `NOMBRE`,
  `APELLIDOPATERNO` y `APELLIDOMATERNO` por separado se ignoran, no se
  guardan ni se muestran en ningún lado.
- Todo lo demás (CURP, Escolaridad, Ciudad, Estado, Tipo de escuela,
  Documento de escolaridad, Estatus) sí se guarda y se muestra en la
  tabla de "Empleados y horas" y en el Excel exportado.
- Al subir el catálogo se muestra una vista previa con el total de filas
  leídas y cuántas tienen estatus `ALTA`.
- Por default solo se cargan los empleados en `ALTA` (puedes destildar la
  casilla para cargar todos).
- Al confirmar, el `CODIGO` se usa como identificador: si el empleado ya
  existe se actualiza (todos sus campos); si es nuevo, se agrega. El
  historial de horas/cursos de un empleado ya existente nunca se toca ni
  se borra.
- Catálogos grandes se suben en lotes automáticamente, así que puedes
  subir el archivo completo (por ejemplo 3,000+ filas) de una vez.

## Filtros y exportación a Excel

Tanto en **Cursos** como en **Empleados y horas** hay una barra de filtros:

- **Mes** — mes en que se impartió el curso (o en que el empleado participó).
- **Curso** — nombre del curso.
- **Planta / Departamento** — usa el campo `Departamento` del catálogo de
  empleados como planta (por ejemplo `CORPORATIVO`).

En **Empleados y horas**, cuando filtras por mes y/o curso, las horas
acumuladas y el número de cursos que se muestran corresponden solo a lo
que cae dentro de ese filtro (no al histórico completo) — un aviso arriba
de la tabla lo indica.

El botón **📊 Exportar a Excel** de cada pestaña descarga exactamente lo
que está filtrado en ese momento (si no aplicaste filtros, exporta todo).
No requiere conexión a internet aparte de la librería de lectura/escritura
de Excel que ya carga la página.

## Cómo usar el dashboard

- **Registrar curso:** captura nombre, fecha en que se impartió, duración
  en horas, marca si tiene vencimiento (y su fecha), y selecciona a los
  empleados que participaron de la lista con buscador. Al guardar, cada
  empleado seleccionado acumula automáticamente esas horas.
- **Alertas de vencimiento:** el Resumen muestra un aviso cuando un curso
  con vencimiento está a 30 días o menos de vencer.
- **Cursos:** lista completa con estado (Vigente / Por vencer / Vencido /
  Sin vencimiento) y participantes por curso.
- **Empleados y horas:** catálogo de empleados con el total de horas
  acumuladas y cuántos cursos ha tomado cada uno. Puedes agregar nuevos
  empleados directamente desde la pestaña "Registrar curso".

## Reporte SIRCE (STPS)

La pestaña **SIRCE (STPS)** genera el archivo en el formato oficial de la
Plantilla SIRCE (hojas `CONSTANCIAS` y `NORMAS`) a partir de lo que ya
capturaste en Cursos y Empleados, cruzado contra los 12 catálogos
oficiales de la STPS (estado, municipio, ocupación, escolaridad, documento
probatorio, institución, área temática, modalidad, tipo de agente,
objetivo de capacitación y normas de competencia).

**Qué se calcula automáticamente al subir el catálogo de empleados:**
- Clave de estado y municipio (a partir de Ciudad/Estado del catálogo).
- Clave de nivel de estudios (a partir de Escolaridad).
- Clave de documento probatorio (a partir del documento de escolaridad).
- Clave de institución (pública/privada, a partir del tipo de escuela).
- Clave de ocupación (a partir del Puesto) — este es un cruce por
  coincidencia de palabras contra ~4,700 ocupaciones oficiales de la STPS,
  que son clasificaciones muy específicas y no siempre coinciden
  exactamente con los puestos internos de la empresa. Cuando la
  coincidencia no es clara, la fila se marca "Ocupación (verificar)" en
  la vista previa de SIRCE — no se descarta, pero conviene revisarla antes
  de subir el reporte oficial.
- El nombre completo se sigue mostrando en todo el dashboard, pero
  internamente también se guarda el nombre separado en Nombre / Primer
  apellido / Segundo apellido (oculto en las tablas) porque así lo pide
  el formato SIRCE.

**Qué tienes que capturar tú, por cada curso** (sección desplegable "Datos
para el reporte SIRCE" al registrar un curso — todo opcional al guardar,
pero se marca como faltante en la vista previa de SIRCE si no lo llenas):
Área temática, Modalidad (presencial/en línea/mixta), Tipo de agente
capacitador (interno/externo/otro), RFC del agente ante la STPS, Objetivo
de la capacitación, y —si el curso otorga una norma de competencia
CONOCER— la clave de la norma y la fecha de emisión del certificado (esto
último alimenta la hoja `NORMAS`, que solo incluye los cursos donde
llenaste esos dos campos).

**Qué tienes que capturar tú, una sola vez:** la **Clave de
establecimiento** asignada por la STPS, en ⚙️ Configuración — es la misma
para todas las filas del reporte.

**Para exportar:** ve a la pestaña SIRCE, filtra por mes y/o curso si
quieres un subconjunto, revisa el resumen de filas completas vs. con
avisos, y dale **Descargar plantilla SIRCE (.xlsx)**. El archivo respeta
exactamente el orden de columnas del formato oficial.

## Notas técnicas

- **Lectura de datos (JSONP):** Google Apps Script bloquea la lectura de su
  respuesta cuando se llama con `fetch()` desde otro dominio (GitHub Pages).
  Por eso el dashboard usa JSONP (una etiqueta `<script>` dinámica) para
  traer los datos — es la forma confiable de leer datos de Apps Script
  desde un sitio externo.
- **Guardado de datos (formulario oculto):** por la misma razón, guardar
  (nuevo curso, nuevo empleado, carga de catálogo, eliminar) se hace con
  un formulario HTML enviado a un iframe oculto, no con `fetch()`. Esto
  significa que el dashboard no puede leer el mensaje de error exacto que
  regrese Apps Script si algo falla del lado del servidor — por eso valida
  los campos obligatorios antes de enviar, y siempre recarga los datos
  después de guardar para reflejar el estado real de la hoja.
- Las horas acumuladas por empleado se calculan sumando la duración de
  cada curso en el que aparece registrado en la hoja `Participaciones` —
  no hay un total "guardado" en la hoja de Empleados, así que siempre
  refleja el histórico real de `Participaciones`.
- Si necesitas dar de baja a un empleado sin perder su historial de
  capacitación, elimínalo del catálogo (`Empleados`) — sus registros en
  `Participaciones` permanecen intactos para reportes.
- `sirce-catalogos.js` contiene los 12 catálogos oficiales de la STPS
  (~420 KB). Debe subirse a GitHub Pages en la misma carpeta que
  `index.html` — si falta, la pestaña SIRCE no podrá calcular ninguna
  clave.

## Archivos que debes actualizar si haces cambios

| Cambio | Archivo a editar | Dónde reemplazar |
|---|---|---|
| Lógica de guardado / nuevas columnas | `Code.gs` | Editor de Apps Script del Sheet → Implementar nueva versión |
| Interfaz / estilos / validaciones | `index.html` | Repositorio de GitHub Pages (reemplazar el archivo) |
| Catálogos oficiales SIRCE | `sirce-catalogos.js` | Repositorio de GitHub Pages (reemplazar el archivo) |
| URL de Apps Script o clave de establecimiento | — | Se configuran desde el botón ⚙️ Configuración dentro del dashboard, no en el código |
