# 📊 Dashboard Comparativo Estadístico — Banco Unión S.A

Dashboard web (HTML + JS) que procesa archivos ZIP con CSV de transacciones y códigos de error, genera gráficos (Chart.js), KPIs, comparativos y un **análisis técnico automático** en todas las pestañas.

Incluye **sincronización multi‑usuario**: el administrador publica la data en GitHub y **todos los usuarios ven los mismos datos**, con **auto-actualización periódica**.

---

## ✨ Características principales

- ✅ **Carga de datos vía ZIP** (CSV internos) para:
  - Transacciones y errores diarios
  - Códigos de error por transacción
  - H03 / H23 por fecha y por TRX
- ✅ **Gráficas y KPIs** (Chart.js):
  - Evolución completa (trx / errores / tasa)
  - Vista diaria (trx vs errores, tasa)
  - Vista semanal (comparativa + tasa)
  - Comparativos por día de semana
  - Top 10 errores
  - Análisis H03/H23 (pie, serie, top TRX)
- ✅ **Análisis técnico automático en TODAS las pestañas**:
  - Umbrales (Target/Warn/Critical)
  - Estadísticos: media (μ), desviación (σ), CV, percentiles p50/p90/p95
  - Detección de anomalías (μ + Nσ)
  - Rachas sobre umbral (Warn/Critical)
  - Cambios Week-over-week (Δpp)
  - Concentración de errores: Top1, Top3 y HHI
  - Cobertura H03/H23 por fecha
- ✅ **Sincronización para todos los usuarios**
  - **Lectura pública desde GitHub (sin token)** usando `raw.githubusercontent.com`
  - **Escritura solo ADMIN** usando GitHub API + PAT
  - **Auto-sync** (polling) cada 60s + al volver a la pestaña/ventana
- ✅ **Modo Admin protegido por URL**
  - Panel de token aparece solo con `?admin=1`

---

## 🧩 Arquitectura de sincronización (cómo funciona)

### Lectura (Todos los usuarios)
- El dashboard intenta cargar `dashboard-data.json` desde GitHub:
  - URL RAW:  
    `https://raw.githubusercontent.com/<user>/<repo>/<branch>/dashboard-data.json`
- Si existe, se carga y renderiza automáticamente para todos.
- Si no existe, se mostrará el panel de carga (y el usuario verá un mensaje informativo).

### Escritura (Solo ADMIN)
- El admin configura un **Personal Access Token (PAT)** en el panel (modo admin).
- Al cargar y procesar ZIP:
  - Se guarda localmente como backup (localStorage)
  - Se publica `dashboard-data.json` al repo vía GitHub API (PUT)
- Todos los usuarios se actualizan automáticamente por polling.

> 🔒 **Seguridad**: el token **NO** se guarda en el repositorio; queda en `localStorage` del navegador del admin.  
> No compartas el equipo/navegador con el token cargado.

---

## 🚀 Instalación y uso (GitHub Pages)

### 1) Subir el `index.html` al repositorio
- Asegúrate de tener el archivo `index.html` en la raíz del repo.

### 2) Configurar lectura pública (OBLIGATORIO)
En `index.html`, busca:

```js
const GITHUB_PUBLIC_READ = {
  user: "TU_USUARIO_GITHUB",
  repo: "TU_REPO_GITHUB",
  branch: "main"
};
