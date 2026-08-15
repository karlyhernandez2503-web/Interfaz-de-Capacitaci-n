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

1. Abre `index.html` con un editor de texto.
2. Busca la línea:
   ```js
   const API_URL = "PEGA_AQUI_TU_URL_DE_APPS_SCRIPT";
   ```
3. Reemplaza el texto entre comillas por la URL que copiaste en el Paso 2
   (la que termina en `/exec`).
4. Guarda el archivo.

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

## Notas técnicas

- El envío de datos (POST) usa `Content-Type: text/plain` a propósito,
  para evitar que el navegador dispare una petición *preflight* CORS que
  Apps Script no maneja bien. Esto es intencional, no un error.
- Las horas acumuladas por empleado se calculan sumando la duración de
  cada curso en el que aparece registrado en la hoja `Participaciones` —
  no hay un total "guardado" en la hoja de Empleados, así que siempre
  refleja el histórico real de `Participaciones`.
- Si necesitas dar de baja a un empleado sin perder su historial de
  capacitación, elimínalo del catálogo (`Empleados`) — sus registros en
  `Participaciones` permanecen intactos para reportes.

## Archivos que debes actualizar si haces cambios

| Cambio | Archivo a editar | Dónde reemplazar |
|---|---|---|
| Lógica de guardado / nuevas columnas | `Code.gs` | Editor de Apps Script del Sheet → Implementar nueva versión |
| Interfaz / estilos / validaciones | `index.html` | Repositorio de GitHub Pages (reemplazar el archivo) |
| Nueva URL de Apps Script | `index.html` | Variable `API_URL` al inicio del `<script>` |
