# Fase 3: Prompt Maestro y Guía de Validación de Prototipos — Caso 1

**Caso:** Caso 1 — Registro de Tiempos, Novedades y Facturación de Horas  
**Fase Bootcamp:** Fase 3 · Prototipos (`/lowcode-prototype-requirements`)  
**Prototipo Asociado:** `Caso1_Prototipo_Navegable_AuraQuantic.html`  

---

## 1. Prompt Maestro para Generación del Prototipo Navegable

El siguiente **Prompt Maestro** documenta los requisitos exactos de interfaz, datos simulados y reglas de negocio empleados para producir y validar el prototipo navegable antes de construir los formularios definitivos en AuraQuantic:

```text
Actúa como Arquitecto UX/UI y Consultor Senior de AuraQuantic. Construye un prototipo web navegable autocontenido (HTML5 + CSS3 + JavaScript puro, sin dependencias externas ni CDNs) para validar las pantallas y flujos del "Caso 1 — Registro de Tiempos, Novedades y Facturación de Horas" antes de implementarlos en la plataforma AuraQuantic.

1. IDENTIDAD CORPORATIVA Y LINEAMIENTOS TRANSVERSALES (RT-01, RT-02, RT-07, RT-10, RT-14):
- Paleta visual: Navy #00015A (encabezados y botones primarios), Mint #3FF2CF (acentos y acciones clave), fondo #F4F6F8, tipografía Segoe UI.
- Barra superior fija con selector de rol/pantalla y botón "Vista Móvil" que adapte la interfaz a ancho de teléfono (420 px) para validar RT-10 (Uso móvil).
- Cada formulario debe mostrar número de trámite legible (TIEM-2026-000128) y un recuadro de "Ayuda en pantalla" de 2 a 3 líneas (RT-07).

2. PANTALLAS Y REGLAS INTERACTIVAS OBLIGATORIAS POR ROL:

A) ROL 1 — EMPLEADO (`FORM_REGISTRO_DIARIO` + Portal Empleado):
- Selector para alternar entre empleados de prueba (ej. Consultor que SÍ genera horas extra vs Director cuyo cargo NO genera horas extra — RF-T-20).
- Selector de día de la jornada (Lun–Mar 08:00–17:30 = 9.5 h; Mié–Vie 07:00–17:30 = 10.5 h — RF-T-05).
- Botón "Precargar día hábil anterior" (RF-T-10) y botón "Guardar Borrador" con persistencia local sin cerrar trámite (RF-T-09).
- KPIs en vivo (RF-T-08, RF-T-23): Total Actividades, Total Novedades, Diferencia frente a jornada y Saldo de Compensación acumulado.
- Grupo de Campos `GC_ACTIVIDADES`:
  * Columnas: Hora Inicio, Hora Fin, Proyecto (dropdown limitado exclusivamente a asignaciones vigentes del empleado en sesión — oculta asignaciones vencidas RF-T-03), Descripción, Checkbox "¿Fuera de horario?" (deshabilitado si el cargo no genera extras), Duración calculada en vivo.
  * Validaciones al enviar:
    - RN-T-02: Hora Fin > Hora Inicio.
    - RN-T-03 / RF-T-11: Horas fuera de la jornada estándar exigen marca "Fuera de horario".
    - RN-T-05: Detección y bloqueo de traslapes horarios entre actividades del mismo día.
    - RN-T-06: El total registrado (actividades + novedades) no puede superar 16 horas/día.
- Grupo de Campos `GC_NOVEDADES`:
  * Columnas: Tipo de Novedad (Permiso, Incapacidad, Ausencia, Llegada tarde, Vacaciones), Duración, Observaciones, Soporte Adjunto.
  * Disparo condicional (RF-T-13): Si el tipo seleccionado exige soporte (Permiso Remunerado o Incapacidad), marcar el adjunto como obligatorio y bloquear el envío si no se ha cargado archivo.

B) ROL 2 — LÍDER DE ÁREA (`FORM_AMPLIACION` y `FORM_APROBACION_EXTRAS`):
- Bandeja de pendientes con semáforo de plazo (RT-03, RT-04).
- Panel de Ampliación de Horario (RF-T-16, RF-T-17): Motivo obligatorio y Nueva Hora Límite (máx. 22:00).
- Panel de Aprobación de Horas Extra (RF-T-18, RF-T-19): Revisión fila por fila (Aprobar / Rechazar) con comentario obligatorio al rechazar y resumen dinámico de horas aprobadas vs devueltas al empleado.

C) ROL 3 — DIRECTOR DE PROYECTO (`FORM_CIERRE_PROYECTO`):
- Revisión mensual de horas del proyecto (RF-T-26): tabla por fecha, empleado, actividad, horas y selector Facturar / Excluir.
- Si excluye una actividad, exigir justificación obligatoria y mostrar visualmente que las horas excluidas no se facturan al cliente pero sí cuentan al 100 % para nómina (RN-T-08).

D) ROL 4 — CONTACTO CLIENTE EXTERNO (`FORM_CLIENTE_FIRMA`):
- Vista aislada únicamente a los proyectos del cliente autenticado (NIT Banco Nacional S.A. — RT-14).
- Vista previa del Reporte Mensual de Horas PDF (RF-T-27) alimentada en vivo desde las horas aprobadas por el Director.
- Indicador de regla de aprobación tácita (recordatorio día 5 hábil, aprobación tácita día 10 hábil — RF-T-28).
- Botón "Firmar Digitalmente" (genera sello con certificado de pruebas, fecha y hash) y botón "Devolver con Observaciones" (exige comentario).

E) ROL 5 — GERENCIA / ADMINISTRADOR (Tablero Semáforo y Parametrización):
- KPIs de cumplimiento (a tiempo vs con ampliación vs no enviados — 3.6) y tabla de proyectos con semáforo presupuestal (Verde <80 %, Amarillo 80–100 %, Rojo >100 % sobreconsumo — RF-T-24).
- Botón de simulación "+15h aprobadas" para demostrar el disparo único de alerta al cruzar el 80 % y al llegar al 100 %.
- Panel de parametrización editable para % alerta presupuesto y plazos 5/10 días del cliente (RT-15, RN-T-09).
```

---

## 2. Protocolo de Validación Antes de Construir (Checklist Fase 3)

Ejecuta estas 7 pruebas rápidas sobre `Caso1_Prototipo_Navegable_AuraQuantic.html` con el usuario o instructor para dar por validada la Fase 3:

| # | Prueba de Validación en el Prototipo | Resultado Esperado | Requerimiento |
| :---: | :--- | :--- | :--- |
| **1** | Abrir vista **Empleado** con *Juan Pérez* y abrir desplegable de proyectos. | Solo aparecen `PRY-001` y `PRY-002`; `PRY-003` (vencido) no se muestra. | RF-T-03, RN-T-01 |
| **2** | Cambiar empleado a *Andrés Silva (Director)* e intentar marcar `¿Fuera Horario?`. | El checkbox aparece bloqueado y deshabilitado. | RF-T-20 |
| **3** | Ingresar dos actividades que se crucen (ej. `08:00–12:00` y `11:00–14:00`) y pulsar **Enviar**. | El sistema bloquea el envío e informa traslape horario. | RN-T-05 |
| **4** | Agregar novedad *Incapacidad Médica* sin adjuntar archivo y pulsar **Enviar**. | El sistema exige el soporte obligatorio antes de enviar. | RF-T-13 |
| **5** | En vista **Director**, excluir una actividad sin escribir justificación y validar. | Exige justificación; al ingresarla, descuenta horas del PDF del cliente pero mantiene `Cuenta 100%` en nómina. | RF-T-26, RN-T-08 |
| **6** | En vista **Portal Cliente**, pulsar **Firmar Digitalmente**. | Estampa el sello digital con certificado de pruebas y fecha/hora. | RF-T-28 |
| **7** | Activar botón **📱 Vista Móvil** y simular `+15h aprobadas` en vista **Gerencia**. | Las tarjetas colapsan a 1 columna y `PRY-001` cambia a Amarillo disparando alerta única 80 %. | RT-10, RF-T-24 |
