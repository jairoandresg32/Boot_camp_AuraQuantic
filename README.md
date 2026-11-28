# Bootcamp AuraQuantic — Caso 1: Registro de Tiempos, Novedades y Facturación de Horas

Repositorio de entregables técnicos y prototipos correspondientes al **Caso 1** del Bootcamp de **AuraQuantic** (Cursos 2, 3 y 4: *Creación de Aplicaciones*, *Automatización Avanzada* y *Diseño de Interfaces*).

---

## 📋 Resumen de la Solución

La arquitectura cubre de punta a punta el ciclo operativo, de aprobación y facturación de tiempos para una firma de consultoría:

* **Capa de Datos y Organización:** Árbol organizacional con atributos de empleado (`Genera_Horas_Extra`, `Lider_Directo`, `Valor_Hora`, `Saldo_Compensacion`), Cuentas y Contactos externos, Bibliotecas documentales y Familias Propias (`Proyectos`, `Asignaciones`, `Tipos_Novedad`, `Jornadas` y `Parametros`).
* **Tres Clases de Proceso BPMN:**
  1. `TIEM_REG_DIARIO`: Registro diario según calendario laboral sin festivos, alerta a las 4:30 p. m., compuerta de ampliación de horario a las 5:30 p. m. y aprobación de horas extra actividad por actividad por el Líder.
  2. `TIEM_CIERRE_MENSUAL`: Tratamiento distribuido causal por cada proyecto facturable, generación de reporte PDF con Documento Base, firma digital del cliente en portal externo (con recordatorio al día 5 y aprobación tácita al día 10 hábil) y confluencia final hacia Finanzas y Nómina.
  3. `TIEM_NOV_CORREO`: Proceso por evento de mensaje (conector de correo entrante IMAP) para radicar novedades e incapacidades enviadas por correo.
* **Integraciones REST y Monitoreo:** Conectores REST para Festivos de Colombia (Nager.Date), sincronización nocturna de proyectos y transmisión a nómina, además de tablero semáforo con *Universal Watcher* (alertas al 80 % y 100 % del presupuesto).

---

## 📂 Índice de Entregables por Fase

| Fase | Entregable | Formato Markdown / Web | Formato Office |
| :--- | :--- | :--- | :--- |
| **Fases 1 y 2** | Análisis del Proceso, Matriz RACI, Supuestos, Arquitectura TO-BE, BPMN y Estimación | [Análisis y Propuesta Técnica (MD con diagramas Mermaid)](./Caso_1_Fase1_Fase2_Analisis_y_Propuesta_Tecnica_AuraQuantic.md) | [Documento Word (.docx)](./Caso_1_Fase1_Fase2_Analisis_y_Propuesta_Tecnica_AuraQuantic.docx) |
| **Fase 2** | Estimación Calibrada (80 h), Matriz de Cobertura (`RF-T`, `RN-T`, `RT`) y Datos Maestros de Prueba | — | [Excel de Estimación y Datos (.xlsx)](./Caso1_Estimacion_Horas_AuraQuantic.xlsx) |
| **Fase 3** | Prototipo Navegable Interactivo de los 5 Roles y Pantallas (con Vista Móvil) | [Prototipo Navegable HTML](./Caso1_Prototipo_Navegable_AuraQuantic.html) | — |
| **Fase 3** | Prompt Maestro y Protocolo de Validación de Prototipos | [Prompt Maestro (MD)](./Caso1_Prompt_Maestro_Prototipos.md) | — |
| **Fases 3 y 4** | Especificación Campo a Campo y Guía de Construcción Paso a Paso en AuraQuantic | [Prototipos e Implementación (MD)](./Caso_1_Fase3_Fase4_Prototipos_e_Implementacion_AuraQuantic.md) | [Documento Word (.docx)](./Caso_1_Fase3_Fase4_Prototipos_e_Implementacion_AuraQuantic.docx) |
| **Presentación** | Diapositivas de Avance del Taller (Uso de IA, Retos, Arquitectura y Guion) | — | [Presentación PowerPoint (.pptx)](./Caso1_Avance_Bootcamp_AuraQuantic.pptx) |
| **Presentación** | Mockup Interactivo de Recorrido End-to-End (8 Estaciones + Camino Alterno + Mapa Global) | [Mockup Recorrido del Proceso (HTML)](./Caso1_Recorrido_Proceso_Mockup.html) | — |
| **Lógica & BPMN** | Diagrama de Lógica Clásico (Óvalos, Rectángulos, Rombos SÍ/NO, Exportación SVG/PDF) | [Diagrama de Lógica de Flujo (HTML)](./Caso1_Diagrama_Logica_Flujo.html) | — |
| **Modelo AuraQuantic** | Modelador Senior Oficial AuraQuantic v2.0 (4 procesos con `TIEM_INFORMES_DIARIOS`, cuadros `FAM_` de lectura/guardado, gracia 15m, calendario, OCR y botón `[+]` Junior) | [Modelador Senior AuraQuantic (HTML)](./Caso1_Modelo_AuraQuantic_Senior.html) | — |

---

## 🖥️ Cómo Visualizar los Prototipos y Modelos Web (`.html`)

Todos los archivos `.html` (`Caso1_Prototipo_Navegable_AuraQuantic.html`, `Caso1_Modelo_AuraQuantic_Senior.html`, `Caso1_Recorrido_Proceso_Mockup.html` y `Caso1_Diagrama_Logica_Flujo.html`) son aplicaciones web autocontenidas (HTML5 + CSS + SVG + JavaScript puro, sin dependencias externas):

1. **En local:** Descarga o clona el repositorio y abre cualquier `.html` directamente en tu navegador (Chrome, Edge, Firefox).
2. **En línea con GitHub Pages (Opcional):**
   * Si el repositorio es público, ve a **Settings → Pages → Source: `Deploy from a branch` (`main` / `/(root)`)**.
   * Una vez publicado, podrás abrirlos directamente desde la URL de GitHub Pages en escritorio o celular:  
     * Modelador Oficial AuraQuantic: `https://jairoandresg32.github.io/Boot_camp_AuraQuantic/Caso1_Modelo_AuraQuantic_Senior.html`
     * Prototipo Navegable: `https://jairoandresg32.github.io/Boot_camp_AuraQuantic/Caso1_Prototipo_Navegable_AuraQuantic.html`
     * Recorrido del Proceso: `https://jairoandresg32.github.io/Boot_camp_AuraQuantic/Caso1_Recorrido_Proceso_Mockup.html`

---

## ⚙️ Calibración del Alcance (Sección 0.1 Bootcamp)

* **Total Estimado Alcance Mínimo (`[Original]` + `[Nuevo]`):** `80.0 horas` (desviación `0.0 %` respecto a las `80 horas guía`, dentro de la tolerancia `±25 %`).
* **Retos Opcionales (`[Reto]`):** Estimados por separado (`RF-T-31` y `RF-T-32`).
