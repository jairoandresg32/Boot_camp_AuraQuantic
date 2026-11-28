# Fases 3 y 4: Prototipos de Pantallas y Guía de Construcción en AuraQuantic (v2.0 Enterprise)

**Caso 1:** Registro de Tiempos, Novedades y Facturación de Horas  
**Programa:** Bootcamp AuraQuantic  
**Versión:** 2.0 Enterprise (Escalable a Cientos de Empleados)  
**Fecha:** 2026-11-27  

---

## 1. FASE 3: PROTOTIPOS Y ESPECIFICACIÓN DE PANTALLAS (UI & UX)

### 1.1 Lineamientos Transversales
* **Identidad (`RT-01`):** Navy `#00015A`, Mint `#3FF2CF`, Fondo `#F4F6F8`, tipografía *Segoe UI*.
* **Ayuda en Pantalla (`RT-07`):** Bloque superior de 3 líneas en cada formulario.
* **Movilidad (`RT-10`) y Rendimiento (`RT-17`):** Cuadrícula adaptativa y tablas paginadas para soportar cientos de empleados.

---

### 1.2 Especificación de los 5 Formularios Clave

#### Formulario 1: `FORM_REGISTRO_DIARIO` (`TP.01` — Empleado)
* **Controles de cabecera:** `Numero_Tramite` (`TIEM-2026-XXXXXX`), `Jornada` (desde `FAM_JORNADAS`), **Badge de Gracia 15 min (`Cierre 5:30 p. m. + Gracia hasta 5:45 p. m.`)**, **Badge Alerta `-30 min` (`5:00 p. m.`) + Estado Calendario (`Libre / Ocupado en reunión`)** y `Saldo Compensación` (`FAM_SALDO_COMPENSACION`).
* **Botones productivos:** `Precargar día hábil anterior` (`RF-T-10`) y `Guardar Borrador` sin avanzar el flujo (`RF-T-09`).
* **Grupo de Campos `GC_ACTIVIDADES`:**
  * `Inicio`, `Fin`, `Proyecto` (**Término Limitado** a `FAM_ASIGNACIONES` vigentes y `FAM_PROYECTOS` activos), `Descripción`, **`¿Es para cobro?` (`EsParaCobro` Sí/No)**, `¿Fuera de horario?` (bloqueado si `Empleado.GeneraHorasExtra == No`), **`Horas Extra Reales`** (calculado), **`Valor Estimado`** (solo visible internamente para nómina/TH `RT-14`).
  * Validaciones al enviar: `Fin > Inicio` (`RN-T-02`), horas fuera de jornada exigen marca (`RN-T-03`), **cero traslapes** (`RN-T-05`) y **tope ≤ 16 h/día** (`RN-T-06`).
* **Grupo de Campos `GC_NOVEDADES`:** Tipo de novedad (`FAM_TIPOS_NOVEDAD`), Duración, Observaciones y Soporte Adjunto (**obligatorio por disparo** cuando `ExigeSoporte == Sí`, `RF-T-13`).

#### Formulario 2: `FORM_AMPLIACION_HORARIO` (`TP.02` — Líder / Gerente Operaciones)
* Muestra el estado consultado en calendario (`Ocupado en reunión entre 5:00 y 5:45` / `Libre`).
* Exige `Motivo_Ampliacion` obligatorio (`RF-T-17`) y `Nueva_Hora_Limite` (`≤ 22:00`).
* Al autorizar, activa el **Temporizador `ET.03`** que vigila si el empleado volvió a rellenar antes del nuevo límite.

#### Formulario 3: `FORM_APROBACION_EXTRAS` (`TP.03` — Líder Directo vía Contenedor)
* Alimentado por `TS.05` (*Traspasador*) hacia `Contenedor_Extras` (muestra solo líneas fuera de horario).
* El Líder aprueba o rechaza línea por línea, valida **`¿Es para cobro?`** y confirma **`Horas Extra Reales trabajadas`**. Si rechaza, exige comentario y desvía (`TS.06`) 1 sola vez al empleado (`TP.04`).

#### Formulario 4: `FORM_CIERRE_PROYECTO` (`TP.31` — Director de Proyecto)
* Lee `FAM_CIERRE_PROYECTO_MES` del proyecto asignado.
* Permite excluir actividades no facturables (`EsParaCobro = No`) con justificación obligatoria (`RF-T-26`), dejando intactas las horas y valor en `FAM_CIERRE_NOMINA_MES` (`RN-T-08`).

#### Formulario 5: `FORM_CLIENTE_FIRMA` (`TP.32` — Portal Externo Clientes)
* Aislado estrictamente por `Cuenta_Cliente == Cliente_Sesión` (`RT-14`).
* Visor del Reporte PDF único del proyecto, indicador de recordatorio día 5 y aprobación tácita día 10 (`RF-T-28`), botones **Firmar Digitalmente** y **Devolver con Observaciones**.

---

## 2. FASE 4: GUÍA MAESTRA DE CONSTRUCCIÓN EN AURAQUANTIC (4 PROCESOS)

### Paso 1: Organización, Cuentas, 10 Familias y 3 Bibliotecas
1. **Empleados:** Atributos `GeneraHorasExtra`, `LiderDirecto`, `ValorHora` (perfil solo Talento Humano `RT-14`), `SaldoCompensacion`.
2. **Familias Propias:** `FAM_JORNADAS`, `FAM_TIPOS_NOVEDAD`, `FAM_PARAMETROS` (`PctAlerta=80`, `DiasRec=5`, `DiasTacita=10`, `MinGracia=15`, `MinAlertaAntes=30`, `UmbralOCR=90`), `FAM_PROYECTOS`, `FAM_ASIGNACIONES`, `FAM_REGISTROS_DIARIOS`, `FAM_DETALLE_ACTIVIDADES`, `FAM_HORAS_EXTRA`, `FAM_NOVEDADES_EMP`, `FAM_SALDO_COMPENSACION`, `FAM_CIERRE_PROYECTO_MES`, `FAM_CIERRE_NOMINA_MES`.
3. **Mapa de Relación:** `Empleados 1:N FAM_ASIGNACIONES N:1 FAM_PROYECTOS` y `Cuentas 1:N FAM_PROYECTOS`.
4. **Bibliotecas:** `BIB_INFORMES_DIARIOS`, `BIB_SOPORTES_NOVEDADES`, `BIB_REPORTES_PROYECTO`.

### Paso 2: Construcción de `TIEM_REG_DIARIO` (0 Archivos por Empleado)
1. **Inicio Temporizado por Lotes (`EI.01`):** Calendario CO (REST Nager.Date) excluyendo empleados con incapacidad vigente en `FAM_NOVEDADES_EMP`.
2. **`TS.01` + `TP.01`:** Contador `TIEM-@YYYY@-@######@`, formulario con `EsParaCobro`, `Horas_Extra_Reales` y `ET.01` (`5:00 p. m.`, 30 min antes) → **`TS.02C` (Conector REST Calendario)** revisa si está ocupado → `TS.02` notifica Correo + App.
3. **Gracia 15 min (`DX.01` `5:45 p. m.`):** Si vence, pasa a `TP.02` → `TS.04` activa **`ET.03` (Temporizador de Re-diligenciamiento)**.
4. **Cruce y Guardado (`TS.07`):** Lee permisos pendientes en `FAM_NOVEDADES_EMP`, los resta/cruza contra horas extra reales aprobadas, calcula `ValorRealCOP` y **Guarda** en `FAM_REGISTROS_DIARIOS`, `FAM_DETALLE_ACTIVIDADES`, `FAM_HORAS_EXTRA` y `FAM_SALDO_COMPENSACION`.

### Paso 3: Construcción de `TIEM_INFORMES_DIARIOS` (1 Instancia Única/Día)
1. **`EI.10` (`6:15 p. m.`):** Corre **1 sola vez** al día.
2. **`TS.11`:** Lee en lote las Familias del día.
3. **`DA.11+`:** Rama A (`TS.12`) genera **1 único Excel consolidado** en `BIB_INFORMES_DIARIOS` y lo envía a Gerencia; Rama B (`TS.13`) actualiza `FAM_PROYECTOS` y evalúa `DO.11` (80 % / 100 %).

### Paso 4: Construcción de `TIEM_NOV_CORREO` (OCR Zero-Touch)
1. **`EI.20` (Correo IMAP)** → `TS.20` valida remitente (`DX.21`) y adjunto (`DX.22`).
2. **`TS.23` (Conector OCR/IA):** Extrae fechas, CIE-10 y tipo; guarda PDF en `BIB_SOPORTES_NOVEDADES`.
3. **`DX.23` (`¿OCR ≥ 90 %?`):** Si sí, salta directo a `TS.24` (Zero-Touch); si no, pasa por `TP.20` (TH).
4. **`TS.24`:** Guarda rango en `FAM_NOVEDADES_EMP` y pre-marca días hábiles en `FAM_REGISTROS_DIARIOS`.

### Paso 5: Construcción de `TIEM_CIERRE_MENSUAL` (Escalable en Familias)
1. **`TS.30`:** Agrega el mes en `FAM_CIERRE_PROYECTO_MES` y `FAM_CIERRE_NOMINA_MES`.
2. **`SP.31` (Distribuido Causal):** 1 instancia por proyecto facturable → `TP.31` Director → `TS.31` 1 PDF/proyecto → `TP.32` Firma Digital + `ET.31` (5d) / `ET.32` (10d tácita).
3. **Confluencia `CA.31+` → `DA.31+`:** `TS.34` envía 1 resumen a Finanzas; `TS.35` envía **lote JSON REST a Nómina** (con 3 reintentos `DX.33` y contingencia `TP.34`) y guarda recibo en `FAM_CIERRE_NOMINA_MES`. Certificados de empleado disponibles **bajo demanda** desde Portal Empleado.

---

## 3. CHECKLIST DE COMPROBACIÓN POR REQUERIMIENTO EN PLATAFORMA

| Bloque | Qué Comprobar en Vivo (Evidencia Esperada) |
| :--- | :--- |
| **Familias y Relación (`RF-T-01..04`)** | Proyecto vencido `PRY-003` no aparece en `TP.01`; cambio de parámetro (gracia 15m, umbral 80 %) aplica sin recompilar proceso. |
| **Calendario y Gracia (`RF-T-05..06`, `V2-01`)** | 0 tareas en festivos/incapacitados; alerta a las `5:00 p. m.` incluye estado `Ocupado/Libre` del calendario; envíos entre `5:30` y `5:45` pasan sin ampliación. |
| **Registro y Reglas (`RF-T-07..13`, `RN-T-01..06`)** | `EsParaCobro` y `Horas_Extra_Reales` calculadas; bloqueo de traslapes, >16 h, cargos directivos sin extras y novedad sin soporte. |
| **Ampliación y Extras (`RF-T-16..20`, `V2-02/03`)** | `ET.03` vigila re-diligenciamiento; aprobación parcial devuelve solo rechazadas 1 vez; `TS.07` cruza permisos y guarda en `FAM_HORAS_EXTRA` (0 archivos). |
| **Informes Diarios (`RF-T-21, 24`, `V2-05`)** | `TIEM_INFORMES_DIARIOS` produce **1 único Excel** para todos los empleados y alerta única al Director al 80 % y Dir+Gerente al 100 %. |
| **OCR Incapacidades (`RF-T-15`, `V2-07`)** | Correo válido con PDF ≥90 % se radica sin intervención de TH y bloquea tareas diarias; remitente desconocido recibe rechazo automático. |
| **Cierre Mensual y Nómina (`RF-T-25..31`)** | 3 proyectos → 3 trámites independientes y 1 solo correo a Finanzas; exclusión mantiene horas en nómina (`RN-T-08`); lote REST con 3 reintentos y PDF empleado bajo demanda. |
