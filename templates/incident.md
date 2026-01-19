# INC-XXX – [Título descriptivo del incidente]

**Marco de referencia**: NIST SP 800-61 Rev. 3 (2025) - Computer Security Incident Handling Guide

---

## 1. Información del Incidente

### Detalles Básicos
- **ID del Incidente**: INC-XXX
- **Fecha/Hora de Detección**: [Fecha completa con hora]
- **Analista Asignado**: moabdib2000
- **Categoría NIST CSF**: [IDENTIFY / PROTECT / DETECT / RESPOND / RECOVER]
- **Severidad**: [Baja / Media / Alta / Crítica]
- **Estado**: [Nuevo / En análisis / En remediación / Resuelto / Cerrado]

### Clasificación del Incidente
- **Tipo**: [Malware / Phishing / Vulnerabilidad / Acceso no autorizado / DoS / Fuga de datos / Otro]
- **Vector**: [Red / Email / USB / Web / Local / Otro]
- **Fuente de Detección**: [Wazuh / SIEM / IDS/IPS / Usuario / Antivirus / Otro]
- **Entorno**: [Laboratorio / Producción simulada]

---

## 2. Activo(s) Afectado(s)

| Campo | Valor |
|-------|-------|
| Hostname | [Nombre del equipo] |
| Sistema Operativo | [Windows 11 / Ubuntu 22.04 / etc] |
| Dirección IP | [192.168.x.x] |
| Software Afectado | [Nombre y versión si aplica] |
| Ubicación | [Red interna / DMZ / etc] |
| Criticidad del Activo | [Baja / Media / Alta / Crítica] |

---

## 3. Descripción del Incidente

### Resumen Ejecutivo
[Descripción breve de qué ocurrió, cuándo y cómo se detectó. 2-3 frases máximo.]

### Detalles Técnicos
**[Nombre del problema/CVE/Malware/etc]**

**Descripción del problema**:  
[Explicación técnica de qué es el problema, cómo funciona, qué componente afecta]

**Impacto potencial**:
- [Qué podría pasar si se explota]
- [Consecuencias posibles]
- [Alcance del daño potencial]

**Datos técnicos relevantes**:
- **[CVE ID / Firma / Hash]**: [Valor]
- **[CVSS Score / Severidad]**: [Puntuación]
- **[Vector de Ataque]**: [Cómo se ejecuta]
- **[Complejidad]**: [Baja / Media / Alta]
- **[Privilegios Requeridos]**: [Usuario / Administrador / Ninguno]

---

## 4. Evidencias

### Capturas de Pantalla

![Evidencia principal](incidents/INC-XXX-nombre-descriptivo/evidencia-principal.jpg)

**Elementos visibles en la evidencia**:
- [Describir qué muestra la captura]
- [Elementos clave identificados]
- [Datos relevantes capturados]

### Validación Manual Realizada
```bash
# Comandos ejecutados para verificar
[comando 1]
[comando 2]
```
**Resultado**: [Qué se encontró]

### Artefactos Adicionales
- [Logs relevantes]
- [Archivos relacionados]
- [Otras evidencias]

---

## 5. Análisis y Evaluación (IDENTIFY & DETECT)

### Contexto del Hallazgo
- **Método de detección**: [Cómo se descubrió]
- **Indicadores de Compromiso (IoC)**: [IPs, dominios, hashes, etc. o "Ninguno"]
- **Actividad maliciosa observada**: [Sí/No y detalles]

### Verificaciones Realizadas

**Logs revisados**:
- [Sistema/Aplicación]: [Qué se encontró]
- [Otro log]: [Resultado]
- [Otro log]: [Resultado]

**Comandos ejecutados**:
```bash
# Listado de verificaciones realizadas
[comando 1]
[comando 2]
[comando 3]
```

**Resultados obtenidos**:
- [Hallazgo 1]
- [Hallazgo 2]
- [Hallazgo 3]

### Evaluación de Riesgo Contextual

**Factores que afectan el riesgo**:
- [Factor 1 que aumenta o reduce el riesgo]
- [Factor 2]
- [Factor 3]
- [Factor 4]

**Análisis del entorno**:
- [Aislamiento de red]
- [Exposición pública]
- [Datos sensibles presentes]
- [Criticidad del sistema]

### Conclusión del Análisis L1
[Tu evaluación final sobre qué está pasando, si hay compromiso activo, nivel de urgencia, necesidad de escalamiento]

---

## 6. Evaluación de Impacto

### Nivel de Impacto Actual
- [ ] Bajo
- [ ] Medio
- [ ] Alto
- [ ] Crítico

### Justificación del Nivel de Impacto

**Por qué se considera [Nivel] en este contexto**:
- [Razón 1]
- [Razón 2]
- [Razón 3]

**Impacto potencial en otros escenarios**:

| Escenario | Impacto | Justificación |
|-----------|---------|---------------|
| [Tipo de entorno 1] | [Nivel] | [Por qué] |
| [Tipo de entorno 2] | [Nivel] | [Por qué] |
| [Tipo de entorno 3] | [Nivel] | [Por qué] |
| [Actual] | [Nivel] | [Por qué] |

---

## 7. Acciones de Respuesta (RESPOND)

### Contención
**Estado**: [Aplicada / No requerida / En proceso]

**Medidas de contención tomadas**:
- [Acción 1]
- [Acción 2]
- [Acción 3]

### Erradicación y Remediación

**Plan de remediación**:

1. **[Paso 1]**
   - [Detalle de qué hacer]
   - [Cómo validar que funcionó]
   - [Tiempo estimado]

2. **[Paso 2]**
   - [Detalle]
   - [Validación]
   - [Tiempo]

3. **[Paso 3]**
   - [Detalle]
   - [Validación]
   - [Tiempo]

**Consideraciones importantes**:
- [Punto crítico 1]
- [Punto crítico 2]
- [Dependencias o riesgos]

### Medidas de Mitigación Temporal

Mientras se aplica la remediación definitiva:
- [Medida temporal 1]
- [Medida temporal 2]
- [Medida temporal 3]

---

## 8. Recuperación (RECOVER)

### Estado de Recuperación
**[Completada / No requerida / En proceso]**

### Acciones de Recuperación Realizadas
- [Acción 1 con resultado]
- [Acción 2 con resultado]
- [Acción 3 con resultado]

### Plan de Recuperación (si aplica)
1. [Paso de recuperación 1]
2. [Paso de recuperación 2]
3. [Paso de recuperación 3]

### Validación Post-Recuperación
- [Verificación 1]
- [Verificación 2]
- [Verificación 3]

---

## 9. Actividades Post-Incidente

### Lecciones Aprendidas

**Conocimientos técnicos adquiridos**:
- [Aprendizaje 1]
- [Aprendizaje 2]
- [Aprendizaje 3]
- [Aprendizaje 4]

**Habilidades desarrolladas**:
- [Habilidad práctica 1]
- [Habilidad práctica 2]
- [Habilidad práctica 3]

**Mejoras para el laboratorio/entorno**:
- [Mejora propuesta 1]
- [Mejora propuesta 2]
- [Mejora propuesta 3]

### Reflexión del Analista

[Escribe aquí tu reflexión personal sobre el caso: qué te sorprendió, qué fue difícil, qué harías diferente la próxima vez, cómo este caso te prepara mejor para un SOC real, etc. 2-4 párrafos.]

---

## 10. Cronología del Incidente

| Fecha/Hora | Evento | Acción Tomada |
|------------|--------|---------------|
| [DD/MM/YYYY HH:MM] | [Qué pasó] | [Qué hiciste] |
| [DD/MM/YYYY HH:MM] | [Qué pasó] | [Qué hiciste] |
| [DD/MM/YYYY HH:MM] | [Qué pasó] | [Qué hiciste] |
| [DD/MM/YYYY HH:MM] | [Qué pasó] | [Qué hiciste] |
| Pendiente | [Próxima acción] | [Qué falta hacer] |

---

## 11. Información de Contacto y Referencias

### Analista Responsable
- **Nombre**: moabdib2000
- **Rol**: Analista SOC L1 (Laboratorio de formación)
- **Fecha del informe**: [DD/MM/YYYY - YYYYMMDD-HHMM]

### Referencias Consultadas
- NIST SP 800-61 Rev. 3 - Computer Security Incident Handling Guide (2025)
- [Base de datos de CVE si aplica]
- [Documentación del fabricante]
- [MITRE ATT&CK Framework si aplica]
- [Otra referencia]

### Próximas Acciones
- **Fecha de revisión**: [Cuándo revisarás el caso]
- **Responsable**: moabdib2000
- **Criterio de cierre**: [Qué debe pasar para cerrar el incidente]

---

**Estado del Documento**: Versión 1.0 - [Estado del incidente]  
**Clasificación**: Uso interno - Laboratorio de formación  
**Última actualización**: [DD/MM/YYYY]