Actúa como un Security Architect, AppSec Lead y experto en OWASP ASVS, OWASP Top 10, OWASP SAMM, NIST SSDF y Secure SDLC.

Tu objetivo es transformar este repositorio Spec Kit para que adopte un enfoque "Security By Design" y "Secure By Default".

Analiza completamente la estructura del repositorio y modifica las plantillas, constitución, workflows, checklists y artefactos del proceso de Spec-Driven Development.

# Objetivos

1. Mantener el flujo original de Spec Kit:
   - Specify
   - Plan
   - Tasks
   - Implement
   - Converge

2. Inyectar controles de seguridad obligatorios en cada etapa.

3. Generar cambios compatibles con proyectos:
   - Web
   - APIs
   - Microservicios
   - Aplicaciones móviles
   - Sistemas empresariales

# Principios obligatorios

Agregar los siguientes principios a la Constitution:

## Security by Design

Toda funcionalidad debe definir requisitos de seguridad antes de implementarse.

## Least Privilege

Todo acceso debe otorgarse con el mínimo privilegio necesario.

## Secure Default Configuration

Las configuraciones por defecto deben ser seguras.

## Defense in Depth

La seguridad debe existir en múltiples capas.

## Zero Trust

Nunca confiar implícitamente en usuarios, sistemas o redes.

## Traceability

Todo requisito de seguridad debe poder rastrearse desde la especificación hasta la implementación y validación.

# OWASP Requirements

Incorporar verificaciones basadas en:

- OWASP Top 10
- OWASP ASVS
- OWASP API Security Top 10
- OWASP Proactive Controls

# Cambios en la fase Specify

Agregar secciones obligatorias:

## Security Requirements

- Autenticación
- Autorización
- Gestión de sesiones
- Protección de datos
- Auditoría
- Logging
- Disponibilidad
- Protección contra abuso

## Data Classification

Clasificar los datos:

- Público
- Interno
- Confidencial
- Restringido

## Threat Modeling

Identificar:

- Activos
- Actores
- Trust Boundaries
- Riesgos
- Amenazas STRIDE

## Security Acceptance Criteria

Definir criterios verificables.

Ejemplo:

- Todas las entradas son validadas.
- No existen secretos en código.
- Autorización aplicada en cada endpoint.
- Datos sensibles cifrados.

# Cambios en la fase Plan

Agregar:

## Security Architecture Review

Validar:

- Arquitectura
- Dependencias
- Integraciones externas
- Gestión de secretos

## Security Controls Matrix

Mapear:

Requisito
↓
Control
↓
Implementación
↓
Prueba

## OWASP Review

Identificar impactos respecto a:

- A01 Broken Access Control
- A02 Cryptographic Failures
- A03 Injection
- A04 Insecure Design
- A05 Security Misconfiguration
- A06 Vulnerable Components
- A07 Authentication Failures
- A08 Software Integrity Failures
- A09 Logging Failures
- A10 SSRF

# Cambios en la fase Tasks

Generar tareas automáticas de seguridad:

- Threat Modeling
- Security Review
- Dependency Review
- Secret Scanning
- SAST
- Security Testing
- Logging Validation
- Authorization Testing
- Hardening Review

Toda historia debe incluir tareas de seguridad asociadas.

# Cambios en la fase Implement

Agregar reglas obligatorias:

## Secure Coding

- Validar toda entrada
- Sanitizar salida
- Consultas parametrizadas
- No usar secretos hardcodeados
- Manejo seguro de errores
- Uso de criptografía moderna

## Secrets

- Nunca almacenar claves en código
- Uso de Secret Manager
- Rotación de credenciales

## Dependencies

- Dependencias actualizadas
- Prohibir librerías críticas vulnerables

# Cambios en la fase Converge

Agregar checklist obligatoria:

## Security Verification

- SAST sin hallazgos críticos
- Secret Scan sin hallazgos
- Dependencias revisadas
- Threat Model actualizado
- Logs auditables
- Role Based Access Control validado

## Gate de aprobación

Bloquear el cierre de una feature si:

- Existe una vulnerabilidad crítica
- Existe una vulnerabilidad High sin mitigación aprobada
- Falta evidencia de pruebas de seguridad

# Artefactos nuevos

Crear:

security-requirements-template.md

threat-model-template.md

security-review-checklist.md

secure-design-review.md

owasp-asvs-checklist.md

api-security-checklist.md

# Resultado esperado

Entregar:

1. Archivos modificados.
2. Nuevas plantillas.
3. Cambios en la Constitution.
4. Cambios en workflows.
5. Ejemplos de uso.
6. Justificación de cada control incorporado.
7. Compatibilidad con futuras actualizaciones de Spec Kit.

Priorizar OWASP ASVS como estándar principal y OWASP Top 10 como mecanismo de verificación.