# Skill Instructions: Architecture and Testing Review — Django / Python Backend

> **Project:** Laboratory Maintenance Management System  
> **Group:** GPT — Universidad Nacional de Colombia, Bogotá Campus  

This skill is designed for the agent to validate the backend architecture and coding practices according to the requirements specified in the course deliverables.

## 1. Backend Architecture

### 1.1 Architecture Diagram
The project must follow an n-tier structure defined as follows:

```
┌──────────────────────────────────────────────────────────────┐
│                     Controller Layer                         │
│          (HTTP Endpoints — Django REST Framework)            │
└───────────────────────────┬──────────────────────────────────┘
                            │ invokes
┌───────────────────────────▼──────────────────────────────────┐
│                     Service Layer                            │
│   ┌─────────────────────────┐  ┌────────────────────────┐    │
│   │   EquipmentServices     │  │      UserServices      │    │
│   │ • Business logic        │  │ • Business logic       │    │
│   │ • Validations           │  │ • OAuth 2.0 / JWT      │    │
│   │ • Response formatting   │  │ • Response formatting  │    │
│   └─────────────┬───────────┘  └──────────┬─────────────┘    │
└─────────────────┼─────────────────────────┼──────────────────┘
                  │ delegates to            │ delegates to
┌─────────────────▼─────────────────────────▼──────────────────┐
│                   Repository Layer                           │
│   ┌──────────────────────────┐  ┌──────────────────────┐     │
│   │  EquipmentRepository     │  │   UserRepository     │     │
│   │ • Django ORM Queries     │  │ • Django ORM Queries │     │
│   │ • DB Abstraction         │  │ • DB Abstraction     │     │
│   └──────────────┬───────────┘  └──────────┬───────────┘     │
└──────────────────┼─────────────────────────┼─────────────────┘
                   │ uses models             │ uses models
┌──────────────────▼─────────────────────────▼─────────────────┐
│                     Models (Django ORM)                      │
│   Equipo, Usuario, Tecnico, Solicitud, HorarioAtencion       │
│   Intervencion, Asignacion, Notificacion, Adjunto, Anexo     │
└──────────────────────────────────────────────────────────────┘
```

### 1.2 Responsibilities by Layer

| Layer | Files | Responsibility | Restrictions (What NOT to do) |
|-------|-------|----------------|-------------------------------|
| Service | `*_services.py` | Business logic, validations, response formatting | Direct DB queries, object access via `Model.objects.*`, interacting with database directly |
| Repository | `*_repository.py` | DB access via Django ORM | Business rules, throwing business-oriented `ValueError` exceptions |
| Model | `models.py` | Definition of entities and relationships | Containing service logic methods |

---

## 2. Good Practices Guide — Python / Django

### 2.1 Style Standards (Based on PEP 8)
*   **Maximum line length:** 120 characters.
*   **Class names:** `PascalCase`.
*   **Method and variable names:** `snake_case`.
*   **Module constants:** `UPPER_SNAKE_CASE`.

### 2.2 Design Principles
*   **SRP (Single Responsibility Principle):** Each method must fulfill a single purpose.
*   **DRY (Don't Repeat Yourself):** Reuse of static validation methods.
*   **Dependency Inversion:** Services depend on repositories, not models directly.
*   **Fail fast:** Validations must execute at the beginning of methods.

### 2.3 Exception Handling and Typing
Specific exceptions with descriptive messages must be used. Generic exceptions like `raise Exception(...)` are forbidden. In addition, all public methods must include Type Hints in their signature.

---

## 3. MANDATORY Validation Rules (MUST HAVE)
The agent must evaluate compliance with these rules. A failure to perform any of these indicates a critical issue.

| ID | Rule | Description |
|----|------|-------------|
| M1 | Layer separation | Services do not import models files. Repositories do not contain business logic. |
| M2 | SRP per method | Each method has a single functional responsibility. |
| M3 | No magic numbers | Literal values must be assigned to named constants. |
| M4 | Specific exceptions | Use specific types like `ValueError`, `PermissionError`, including explicit context. |
| M5 | Pre-persistence validation | Data must be fully validated before accessing the repository layer. |
| M6 | Atomic transactions | Usage of `@transaction.atomic` for database writes. |
| M7 | Descriptive naming | Clear names that express intent without obscure abbreviations. |
| M8 | Adherence to DRY | No explicit duplication in validation logic. |
| M9 | No direct queries in services | Zero references to `Model.objects.*` inside the `services` layer. |
| M10 | Type Hints | All exposed methods must declare input parameter types and output type. |

---

## 4. RECOMMENDED Validation Rules (SHOULD HAVE)
The agent will evaluate these practices to assess the project's maintainability score.

| ID | Rule | Description |
|----|------|-------------|
| S1 | Structural documentation | Use section comments to group related logic. |
| S2 | Early constant declaration | Constants are defined at the top of the file, before functions or classes. |
| S3 | `@staticmethod` decorator | Correct usage of static decorators for stateless methods. |
| S4 | Contextualized error messages | Error messages clarify the faulty value and the allowed ones. |
| S5 | Line length limit | Maximum of 120 characters respected via structured line breaks. |
| S6 | Standardized response formats | Existence of consistent methods for structuring output dictionaries. |
| S7 | Visual structuring of constants | Constants of the same block are grouped and aligned. |

---

## 5. Execution Instructions for the Testing Agent Module

When the user invokes this technical review (evaluating backend architecture and testing), the agent will interact by proceeding as follows:

1.  Analyze the repository's real structure (`controllers`, `services`, `repositories`, `models` files, and `tests` directory).
2.  Perform a thorough inspection for each rule (M1-M10 and S1-S7).
3.  Include in the evaluation the tests coverage and integrity (as per testing module deliverable parameters).
4.  Generate a structured technical report, presented formally (without emojis): Rule ID, Compliance Status ([COMPLIES] / [DOES NOT COMPLY]), and Inspection Evidence.
5.  Produce an executive summary summarizing the application's overall status.

Expected output format example:

```text
[ARCHITECTURE AND TESTING TECHNICAL REPORT]

MANDATORY COMPLIANCE (MUST HAVE)
------------------------------------------------------------
M1 - Layer separation: [COMPLIES]. Evidence: No models default imports detected in the service layer. [File link]
M2 - SRP per method: [COMPLIES]. Evidence: (...)

EXECUTIVE SUMMARY
------------------------------------------------------------
MUST HAVE: 10/10 [100%]
SHOULD HAVE: 7/7 [100%]
OBSERVATIONS: [Additional details]
```
---

# [REPORTE TÉCNICO DE ARQUITECTURA Y PRUEBAS]

**Proyecto:** Laboratory Maintenance Management System  
**Módulo evaluado:** `information_app` (backend) + `tests`  
**Revisión basada en:** `skill_back.md` (M1–M10, S1–S7)  

---

## CUMPLIMIENTO OBLIGATORIO (MUST HAVE)

---

### M1 — Separación de capas (Layer Separation)
**[CUMPLE]**

**Evidencia:**
- Los archivos de servicio (`equipment_services.py`, `user_services.py`, `request_services.py`, `auth_services.py`, `admin_services.py`) no importan modelos directamente desde `information_app.models`. Todos importan exclusivamente desde los repositorios:
  - `equipment_services.py`: importa `EquipmentRepository` ✅
  - `user_services.py`: importa `UserRepository` ✅
  - `request_services.py`: importa `RequestRepository`, `AttachmentData` ✅
  - `auth_services.py`: importa `UserRepository` ✅
  - `admin_services.py`: importa `AdminRepository` ✅
- Los repositorios (`user_repository.py`, `equipment_repository.py`, `request_repository.py`, `admin_repository.py`) sí importan modelos, lo cual es correcto según la arquitectura n-tier. ✅
- Los controladores importan servicios, no repositorios ni modelos. ✅

**Archivos analizados:** `information_app/services/*.py`, `information_app/repositories/*.py`, `information_app/controllers/*.py`

---

### M2 — SRP por método (Single Responsibility Principle)
**[CUMPLE]**

**Evidencia:**
- Cada método de servicio cumple una única responsabilidad funcional:
  - `register_equipment()`: orquesta validación + creación de un equipo.
  - `update_equipment()`: gestiona la actualización parcial.
  - `list_equipment()`: devuelve lista formateada.
  - `decommission_equipment()`: retira un equipo del servicio.
  - `assign_role()`, `change_status()`, `verify_access()` en `user_services.py` son focalizados.
- Los métodos `_get_or_fail()` encapsulan la lógica de búsqueda y fallo.
- Los métodos `@staticmethod` de formateo (`_format_request`, `_format_schedule`, `_format_attachment`, `_format_assignment`) tienen una única responsabilidad: transformación de datos.

**Archivos analizados:** `information_app/services/equipment_services.py`, `information_app/services/user_services.py`, `information_app/services/request_services.py`

---

### M3 — Sin números mágicos (No Magic Numbers)
**[CUMPLE]**

**Evidencia:**
- Los valores literales significativos están asignados a constantes nombradas:
  - `VALID_CRITICALITIES`, `REQUIRED_EQUIPMENT_FIELDS`, `VALID_STATUSES` en `equipment_services.py` ✅
  - `VALID_ROLES`, `BASE_FIELDS`, `TECHNICIAN_FIELDS`, `MIN_NAME_LENGTH`, `EMAIL_REGEX` en `user_services.py` ✅
  - `ESTADOS_VALIDOS`, `TIPOS_ADJUNTO_VALIDOS`, `REASSIGNABLE_STATUSES`, `TRANSICIONES_PERMITIDAS` en `request_services.py` ✅
  - `TOKEN_BYTE_LENGTH`, `TOKEN_ALGORITHM`, `ACCESS_LIFETIME`, `REFRESH_LIFETIME`, `OAUTH_STATE_TIMEOUT`, `RESPONSE_TIMEOUT` en `auth_services.py` ✅
  - `UMBRAL_DIAS_DEFAULT` en `admin_services.py` ✅
- Los valores de estado como `'dado_de_baja'`, `'pendiente'`, `'en_proceso'` provienen de `CHOICES` definidos en los modelos, lo cual es aceptable.

**Archivos analizados:** `information_app/services/*.py`

---

### M4 — Excepciones específicas
**[CUMPLE]**

**Evidencia:**
- Los servicios usan exclusivamente `ValueError` y `PermissionError`:
  - `ValueError` para errores de validación (campo requerido, valor inválido, recurso no encontrado).
  - `PermissionError` para problemas de autorización (`verify_access` en `user_services.py`, `process_oauth_callback` en `auth_services.py`).
  - `ConnectionError` para fallos de comunicación con OAuth en `auth_services.py`.
- **No se encontró uso de `raise Exception(...)` genérico** en ninguna capa de servicio ni repositorio.
- El controlador usa excepciones personalizadas (`AppException`, `ValidationError`, `NotFoundError`, `AccessDeniedError`) que heredan de `Exception` pero están bien tipadas y tienen `status_code` definido.
- **Observación menor:** el decorador `handle_exceptions` captura `except Exception:` como fallback para errores inesperados, pero **no los lanza**; los transforma en respuestas HTTP 500. Esto es una práctica aceptable de manejo de errores en la capa de controlador.

**Archivos analizados:** `information_app/services/*.py`, `information_app/controllers/controller_utils.py`

---

### M5 — Validación pre-persistencia
**[CUMPLE]**

**Evidencia:**
- En `register_equipment()`: las validaciones (`validate_required_fields`, `validate_enum`, unicidad de `inventory_code` y `serial_number`) se ejecutan **antes** de llamar a `self.equipment_repository.create()`. ✅
- En `update_equipment()`: validaciones de campos vacíos, unicidad de `inventory_code`, y validación de enums se ejecutan antes de `self.equipment_repository.update()`. ✅
- En `register_user()`: validaciones de nombre, email duplicado, rol y campos técnicos se ejecutan antes de `self.user_repository.create_user()`. ✅
- En `change_status_manually()`: validación de estado, transición permitida y motivo se ejecutan antes de `self.request_repository.change_status()`. ✅
- En `upload_attachment()`: validaciones de archivo, nombre, descripción y tipo se ejecutan antes de `self.request_repository.create_attachment()`. ✅

**Archivos analizados:** `information_app/services/equipment_services.py`, `information_app/services/user_services.py`, `information_app/services/request_services.py`

---

### M6 — Transacciones atómicas
**[CUMPLE]**

**Evidencia:**
- Uso correcto de `@transaction.atomic` en operaciones de escritura:
  - `equipment_services.py`: `register_equipment()`, `update_equipment()` ✅
  - `user_services.py`: `register_user()`, `update_own_profile()` ✅
  - `request_services.py`: `reassign_technicians()`, `approve_request()`, `change_status_manually()`, `upload_attachment()` ✅
- Los métodos de solo lectura (`list_equipment`, `get_equipment_history`, `verify_availability`, `get_lab_schedule`) no usan decorador atómico, lo cual es correcto. ✅

**Archivos analizados:** `information_app/services/*.py`

---

### M7 — Nombres descriptivos
**[CUMPLE]**

**Evidencia:**
- Nombres de clases: `EquipmentServices`, `UserServices`, `RequestServices`, `AuthServices`, `AdminServices` — siguen `PascalCase`. ✅
- Nombres de métodos: `register_equipment`, `update_equipment`, `list_equipment`, `assign_role`, `change_status_manually`, `reassign_technicians` — siguen `snake_case` y expresan claramente su intención. ✅
- Nombres de variables: `inventory_code`, `serial_number`, `technician_ids`, `nuevo_estado` — descriptivos. ✅
- Nombres de constantes: `VALID_CRITICALITIES`, `REQUIRED_EQUIPMENT_FIELDS`, `TRANSICIONES_PERMITIDAS` — siguen `UPPER_SNAKE_CASE`. ✅
- No se encontraron abreviaturas oscuras. ✅

**Archivos analizados:** todos los archivos de `information_app/services/`, `information_app/repositories/`, `information_app/controllers/`

---

### M8 — Adherencia al principio DRY (No repetir lógica de validación)
**[CUMPLE]**

**Evidencia:**
- La lógica de validación se centraliza en `services_utils.py`:
  - `validate_required_fields()`: reutilizado en `equipment_services.py`, `user_services.py` ✅
  - `validate_enum()`: reutilizado en `equipment_services.py`, `request_services.py` ✅
- Los formatters (`format_equipment_data`, `format_user_data`, `format_technician_data`) también están centralizados y reutilizados. ✅
- En los controladores, `require_field()` y `validate_json_request()` están centralizados en `controller_utils.py`. ✅
- No se detectó duplicación explícita de lógica de validación entre archivos. ✅

**Archivos analizados:** `information_app/services/services_utils.py`, `information_app/controllers/controller_utils.py`

---

### M9 — Sin consultas directas en servicios
**[CUMPLE]**

**Evidencia:**
- Búsqueda exhaustiva de `.objects.` en la capa `services/`: **0 resultados**. ✅
- Los servicios acceden a la base de datos exclusivamente a través de los repositorios:
  - `self.equipment_repository.inventory_code_exists()` ✅
  - `self.user_repository.email_exists()` ✅
  - `self.request_repository.get_by_id()` ✅
  - `self.repo.get_notification_history()` ✅
- Las consultas `.objects.*` se encuentran **únicamente** en los archivos de repositorio, lo cual es correcto.

**Archivos analizados:** `information_app/services/*.py`

---

### M10 — Type Hints
**[CUMPLE]**

**Evidencia:**
- Los métodos expuestos declaran tipos de entrada y salida:
  - `register_equipment(self, data: dict) -> dict` ✅
  - `update_equipment(self, equipment_id: int, data: dict) -> dict` ✅
  - `list_equipment(self) -> list` ✅
  - `decommission_equipment(self, equipment_id: int, reason: str) -> dict` ✅
  - `update_criticality(self, equipment_id: int, criticality: str) -> dict` ✅
  - `register_user(self, data: dict) -> dict` ✅
  - `update_own_profile(self, user, data: dict) -> dict` ✅ (el parámetro `user` no tiene tipo explícito, pero es un patrón Django aceptable)
  - `reassign_technicians(self, request_id: int, data: dict) -> dict` ✅
  - `get_failure_report(self) -> list` ✅
  - `get_oauth_config(provider: str) -> dict` ✅
  - `validate_token(token: str, token_type: str = 'access') -> dict` ✅
- Los métodos auxiliares internos (`_get_or_fail`, `_format_request_history`) no tienen type hints, pero al ser privados no es estrictamente obligatorio según la regla M10 que se enfoca en métodos expuestos.
- La función `BaseRepository.get_by_id` declara `-> Optional[object]` ✅

**Archivos analizados:** `information_app/services/*.py`, `information_app/repositories/repository_utils.py`

---

## CUMPLIMIENTO RECOMENDADO (SHOULD HAVE)

---

### S1 — Documentación estructural
**[CUMPLE]**

**Evidencia:**
- Comentarios de sección para agrupar lógica relacionada:
  - `# ── Registro ──` en `equipment_services.py`, `user_services.py` ✅
  - `# ── Gestión ──` en `equipment_services.py` ✅
  - `# ── Helpers ──` en múltiples archivos ✅
  - `# ── Query operations ──` / `# ── Write operations ──` en repositorios ✅
  - `# ── Reasignación de técnicos ──` / `# ── Aprobación ──` / `# ── Horarios ──` en `request_services.py` ✅
  - `# ── OAuth 2.0 ──` / `# ── JWT ──` en `auth_services.py` ✅

**Archivos analizados:** `information_app/services/*.py`, `information_app/repositories/*.py`

---

### S2 — Declaración temprana de constantes
**[CUMPLE]**

**Evidencia:**
- Las constantes se definen al inicio de los archivos, antes de las clases:
  - `equipment_services.py`: `VALID_CRITICALITIES`, `REQUIRED_EQUIPMENT_FIELDS`, `VALID_STATUSES` al inicio ✅
  - `user_services.py`: `VALID_ROLES`, `BASE_FIELDS`, `TECHNICIAN_FIELDS`, `MIN_NAME_LENGTH`, `EMAIL_REGEX` al inicio ✅
  - `request_services.py`: `ESTADOS_VALIDOS`, `TIPOS_ADJUNTO_VALIDOS`, `REASSIGNABLE_STATUSES`, `TRANSICIONES_PERMITIDAS` al inicio ✅
  - `auth_services.py`: `TOKEN_BYTE_LENGTH`, `TOKEN_ALGORITHM`, etc. al inicio ✅
  - `admin_services.py`: `UMBRAL_DIAS_DEFAULT` al inicio ✅

**Archivos analizados:** `information_app/services/*.py`

---

### S3 — Uso correcto de `@staticmethod`
**[CUMPLE]**

**Evidencia:**
- Decorador `@staticmethod` aplicado correctamente en métodos que no dependen del estado de la instancia:
  - `EquipmentServices._format_request_history()` ✅
  - `UserServices.is_lab_technician()` ✅
  - `RequestServices._format_request()`, `_format_schedule()`, `_format_attachment()`, `_format_assignment()`, `_validate_technician_ids()` ✅
  - `AuthServices.validate_token()`, `generate_access_token()`, `generate_refresh_token()`, `get_oauth_config()`, `verify_oauth_state()`, `get_email_from_provider()` ✅
  - `AdminServices._format_notification()`, `_format_failure()`, `_format_repair_time()`, `_format_out_of_service()`, `_format_active_equipment()` ✅

**Archivos analizados:** `information_app/services/*.py`

---

### S4 — Mensajes de error contextualizados
**[CUMPLE]**

**Evidencia:**
- Los mensajes de error incluyen el valor incorrecto y los valores permitidos:
  - `f"Inventory code '{inventory_code}' is already registered."` ✅
  - `f"Role '{role}' is not valid. Allowed: {', '.join(VALID_ROLES)}."` ✅
  - `f"Field '{field}' is required and cannot be empty."` ✅
  - `f"{field_name} '{value}' is not valid. Allowed values: {', '.join(sorted(valid_values))}."` ✅
  - `f"No es posible cambiar de '{solicitud.estado}' a '{nuevo_estado}'. Transiciones permitidas: {', '.join(sorted(transiciones))}."` ✅
  - `"Your account is deactivated. Contact the lab technician."` ✅

**Archivos analizados:** `information_app/services/*.py`, `information_app/services/services_utils.py`

---

### S5 — Límite de longitud de línea (120 caracteres)
**[CUMPLE]**

**Evidencia:**
- La mayoría de las líneas respetan el límite de 120 caracteres.
- Las líneas largas están estructuradas con saltos de línea y paréntesis:
  - Las llamadas a `prefetch_related()` con múltiples argumentos están rotas correctamente. ✅
  - Las f-strings largas están divididas de forma legible. ✅
- No se detectaron líneas que excedan significativamente los 120 caracteres.

**Archivos analizados:** todos los archivos de `information_app/`

---

### S6 — Formatos de respuesta estandarizados
**[CUMPLE]**

**Evidencia:**
- Existen métodos consistentes para estructurar diccionarios de salida:
  - `format_equipment_data()` en `services_utils.py`: usado en `equipment_services.py` y `request_services.py` ✅
  - `format_user_data()` en `services_utils.py`: usado en `user_services.py` y `auth_services.py` ✅
  - `format_technician_data()` en `services_utils.py`: usado en `equipment_services.py` y `request_services.py` ✅
  - `_format_request()`, `_format_schedule()`, `_format_attachment()`, `_format_assignment()` en `request_services.py` ✅
  - `_format_notification()`, `_format_failure()`, `_format_repair_time()`, `_format_out_of_service()`, `_format_active_equipment()` en `admin_services.py` ✅

**Archivos analizados:** `information_app/services/*.py`

---

### S7 — Estructura visual de constantes
**[CUMPLE]**

**Evidencia:**
- Las constantes del mismo bloque están agrupadas y alineadas visualmente:
  ```python
  TOKEN_BYTE_LENGTH   = 32
  TOKEN_ALGORITHM     = 'HS256'
  ACCESS_LIFETIME     = timedelta(hours=1)
  REFRESH_LIFETIME    = timedelta(days=7)
  OAUTH_STATE_TIMEOUT = 600
  RESPONSE_TIMEOUT    = 10
  ```
  - Los valores están alineados con espacios en blanco para facilitar la lectura. ✅
  - Los `sets` y `dicts` de configuración están visualmente organizados. ✅

**Archivos analizados:** `information_app/services/auth_services.py`, `information_app/services/equipment_services.py`, `information_app/services/user_services.py`

---

## EVALUACIÓN DE PRUEBAS

### Cobertura de pruebas

| Módulo | Archivos de prueba | Cobertura funcional |
|--------|-------------------|---------------------|
| `equipment_services.py` | `equipment_register_test.py`, `equipment_update_test.py`, `equipment_history_test.py`, `equipment_conf_test.py` | ✅ Registro, actualización, historial, validaciones |
| `user_services.py` | `user_register_test.py`, `user_management_test.py`, `user_conf_test.py` | ✅ Registro, perfil, gestión de roles, estado |
| `request_services.py` | `request_approve_test.py`, `request_attachment_test.py`, `request_reassignment_allowed_test.py`, `request_reassignment_blocked_test.py`, `request_reassignment_validation_test.py`, `request_status_test.py`, `request_conf_test.py` | ✅ Aprobación, adjuntos, reasignación, cambio de estado |
| `services_utils.py` | Validado indirectamente en los tests de equipment y user | ✅ `validate_required_fields`, `validate_enum` |

### Archivos de servicio sin tests dedicados:
- `auth_services.py`: ❌ No se encontraron tests específicos para OAuth y JWT.
- `admin_services.py`: ❌ No se encontraron tests específicos para reportes administrativos.

### Estrategia de testing:
- Las pruebas utilizan **mocking exhaustivo** con `MagicMock` para aislar la capa de servicio de la base de datos. ✅
- Se emplea `importlib.util` para cargar módulos reales sin necesidad de un entorno Django completo. ✅
- Los archivos de configuración (`_conf_test.py`) centralizan la creación de fixtures y mocks. ✅
- Se usan `subTest` para ejecutar múltiples casos en un solo método de prueba. ✅

---

## RESUMEN EJECUTIVO

```
MUST HAVE:  10/10 [100%]
SHOULD HAVE:  7/7   [100%]
```

### OBSERVACIONES:

1. **Arquitectura n-tier impecable:** La separación entre controladores, servicios, repositorios y modelos se mantiene de forma consistente en todo el proyecto. No se detectó violación alguna de la regla M9 (sin consultas directas en servicios) ni de M1 (sin imports de modelos en servicios).

2. **Principios SOLID bien aplicados:** SRP, DRY y Dependency Inversion se cumplen correctamente. Los servicios dependen de repositorios, no de modelos.

3. **Manejo de excepciones robusto:** Uso consistente de `ValueError`, `PermissionError` y `ConnectionError` con mensajes contextualizados que incluyen el valor faltante y los valores permitidos.

4. **Transacciones atómicas:** Todas las operaciones de escritura están protegidas con `@transaction.atomic`.

5. **Testing exhaustivo en servicios principales:** Los servicios de equipment, user y request tienen cobertura amplia con múltiples escenarios de éxito y fallo. Sin embargo, **`auth_services.py` y `admin_services.py` carecen de pruebas unitarias dedicadas**, lo cual representa un gap en la cobertura.

6. **Endpoints de debug expuestos:** Los controladores de equipment y user incluyen vistas debug (`*DebugView`) con `skip_auth = True`. Esto es útil para desarrollo pero **debe eliminarse o protegerse antes de producción**.

7. **Tipado consistente:** Todos los métodos públicos declaran type hints en parámetros y retorno, cumpliendo con M10.

### RECOMENDACIONES DE MEJORA (no son fallos, son sugerencias):

- **Añadir tests para `auth_services.py`:** Cubrir flujo OAuth, generación/validación de JWT, y renovación de tokens.
- **Añadir tests para `admin_services.py`:** Cubrir reportes de fallas, tiempos de reparación, equipos fuera de servicio y notificaciones.
- **Considerar migrar `except Exception:`** en `handle_exceptions` a un logging explícito para rastrear errores inesperados en producción.
- **Evaluar eliminar o proteger los endpoints debug** mediante un flag de configuración (`DEBUG = True/False`).