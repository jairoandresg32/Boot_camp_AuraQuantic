# Fase 3: Prompt Maestro y Guía de Validación de Prototipos — Caso 1 (v2.0)

**Caso:** Caso 1 — Registro de Tiempos, Novedades y Facturación de Horas  
**Fase Bootcamp:** Fase 3 · Prototipos (`/lowcode-prototype-requirements`)  
**Prototipo Asociado:** `Caso1_Prototipo_Navegable_AuraQuantic.html`  

---

## 1. Prompt Maestro para Generación del Prototipo Navegable (v2.0 Enterprise)

```text
Actúa como Arquitecto UX/UI y Consultor Senior de AuraQuantic. Construye un prototipo web navegable autocontenido (HTML5 + CSS3 + JavaScript puro, sin dependencias externas ni CDNs) para validar todas las pantallas, arquitectura escalable en Familias y la comprobación de los 68 requerimientos del "Caso 1 — Registro de Tiempos, Novedades y Facturación de Horas" (v2.0 Enterprise).

1. IDENTIDAD CORPORATIVA Y LINEAMIENTOS TRANSVERSALES (RT-01 a RT-17):
- Paleta: Navy #00015A, Mint #3FF2CF, fondo #F4F6F8, Segoe UI.
- Barra superior fija con selector de 6 pestañas (5 roles + pestaña de Comprobación de 68 Requisitos) y botón "Vista Móvil" (420 px) para validar RT-10.

2. PANTALLAS Y REGLAS INTERACTIVAS OBLIGATORIAS:
A) EMPLEADO (`FORM_REGISTRO_DIARIO`):
- Selector de empleado (Consultor que SÍ genera extras vs Director que NO genera extras — RF-T-20) y selector de día (Lun–Mar 9.5 h; Mié–Vie 10.5 h).
- Indicadores de tiempo de gracia 15 min (cierre 5:30 + gracia hasta 5:45 p. m.) y alerta previa 30 min antes (5:00 p. m.) con simulador de Calendario (Ocupado en reunión / Libre).
- Botones "Precargar día hábil anterior" (RF-T-10) y "Guardar Borrador" (RF-T-09).
- Grupo de Campos `GC_ACTIVIDADES`: Inicio, Fin, Proyecto limitado por asignación vigente (oculta vencidos RF-T-03), Descripción, selector `¿Para Cobro?` (`EsParaCobro`), checkbox `¿Fuera Horario?`, `Horas Extra Reales` y Duración calculada en vivo.
- Validaciones al enviar: Fin > Inicio (RN-T-02), horas fuera de jornada exigen marca (RN-T-03), bloqueo de traslapes (RN-T-05), tope <= 16 h (RN-T-06) y soporte obligatorio en incapacidades/permisos (RF-T-13).
- Simulador de Cruce en vivo al enviar: descuenta horas de permiso pendientes (`FAM_NOVEDADES_EMP`) contra las horas extra aprobadas y muestra el registro persistido en `FAM_HORAS_EXTRA` + `FAM_SALDO_COMPENSACION` (0 archivos por empleado).

B) LÍDER DE ÁREA (`FORM_AMPLIACION_HORARIO` y `FORM_APROBACION_EXTRAS`):
- Panel Ampliación: muestra el estado de calendario del empleado, exige motivo obligatorio (RF-T-17), nueva hora límite (<= 22:00) y activa aviso del Temporizador de control `ET.03` de re-diligenciamiento.
- Panel Aprobación Extras: revisión actividad por actividad validando `¿Para Cobro?`, `Horas Extra Reales`, Aprobar/Rechazar con comentario obligatorio al rechazar (RF-T-18/19).

C) DIRECTOR DE PROYECTO (`FORM_CIERRE_PROYECTO`):
- Revisión desde `FAM_CIERRE_PROYECTO_MES`: exclusión de actividades no facturables con justificación obligatoria (RF-T-26) demostrando que permanecen al 100 % en `FAM_CIERRE_NOMINA_MES` (RN-T-08).

D) PORTAL CLIENTE EXTERNO (`FORM_CLIENTE_FIRMA`):
- Aislamiento por NIT (RT-14), visor del único PDF generado por proyecto facturable (RF-T-27), control de recordatorio día 5 / aprobación tácita día 10 (RF-T-28) y firma digital / observaciones.

E) GERENCIA, PARAMETRIZACIÓN Y BAJO DEMANDA:
- Tablero de cumplimiento, semáforo presupuestal con simulación "+15h aprobadas" (alerta única 80 % y 100 % sobreconsumo — RF-T-24).
- Parametrización editable en vivo (RT-15, RN-T-09): % alerta 80, días 5/10, gracia 15 min, alerta -30 min y umbral OCR 90 %.
- Botón "Descargar Certificado PDF del Mes (Bajo Demanda)" leyendo `FAM_CIERRE_NOMINA_MES` para evitar cientos de PDFs mensuales.

F) PESTAÑA DE COMPROBACIÓN DE REQUISITOS (68 ítems):
- Listado interactivo con filtro por tipo (`RF-T`, `RN-T`, `RT`, `V2`) y buscador, mostrando por cada requerimiento su Familia/Objeto y el procedimiento exacto de comprobación.
```

---

## 2. Protocolo de Validación Antes de Construir (10 Pruebas Clave)

| # | Prueba en el Prototipo | Resultado Esperado | Requerimiento |
| :---: | :--- | :--- | :--- |
| **1** | Abrir vista **Empleado** con *Juan Pérez* y revisar desplegable de proyectos. | Solo aparecen `PRY-001` y `PRY-002`; `PRY-003` (vencido) queda oculto. | RF-T-03, RN-T-01 |
| **2** | Cambiar empleado a *Andrés Silva (Director)* e intentar marcar `¿Fuera Horario?`. | Checkbox bloqueado y deshabilitado. | RF-T-20 |
| **3** | Ingresar actividades que se crucen u horas >16 h y pulsar **Enviar**. | Bloquea el envío detallando la regla violada. | RN-T-05, RN-T-06 |
| **4** | Agregar *Incapacidad Médica* sin adjunto y enviar. | Exige soporte obligatorio. | RF-T-13 |
| **5** | Simular **Calendario Ocupado** + **Cruce con 1.0 h de permiso pendiente** y enviar con 2.0 h extra. | Calcula 1.0 h extra neta tras cruce y muestra guardado en `FAM_HORAS_EXTRA` sin generar archivos. | V2-01, V2-03, RF-T-22 |
| **6** | En vista **Líder**, autorizar ampliación viendo agenda y aprobar/rechazar extras. | Exige motivo, muestra activación de `ET.03` y comentario al rechazar. | RF-T-16..19, V2-02 |
| **7** | En vista **Director**, excluir una actividad con motivo. | Descuenta del PDF del cliente pero mantiene `100 %` en nómina. | RF-T-26, RN-T-08 |
| **8** | En vista **Portal Cliente**, pulsar **Firmar Digitalmente**. | Estampa sello criptográfico simulado con certificado de pruebas. | RF-T-28 |
| **9** | En **Gerencia**, simular `+15h`, editar gracia/OCR y descargar certificado bajo demanda. | `PRY-001` dispara alerta única 80 % y genera certificado leyendo `FAM_CIERRE_NOMINA_MES`. | RF-T-24, RT-15, RT-17 |
| **10** | Abrir pestaña **6. ✅ Comprobación Requisitos (68)** y filtrar por `V2` o `RN-T`. | Muestra los 68 ítems con familia asociada y método de comprobación. | Auditoría QA |
