# Optimización de operaciones de soporte mediante automatización y reglas de negocio en SQL Server
---
## 🇲🇽 Español

### Resumen

Este caso de estudio documenta una serie de soluciones internas diseñadas para reducir fricción operativa y eliminar tareas repetitivas de soporte en sistemas empresariales fiscales y administrativos.
El enfoque principal no fue el desarrollo de nuevas funcionalidades, sino la optimización de procesos existentes mediante control de acceso a información fiscal, automatización y aplicación de reglas de negocio directamente en SQL Server.

Las soluciones descritas surgieron a partir de la atención diaria de incidencias reales en entornos productivos, donde la correcta segregación de información fiscal y la reducción de dependencias operativas del equipo de soporte eran críticas.

### Contexto y problema

Los sistemas empresariales utilizados por clientes corporativos manejaban información fiscal sensible, incluyendo facturas emitidas, facturas recibidas y documentos de nómina.
Aunque estos sistemas contaban con esquemas de permisos por rol, la validación de estos permisos se omitía en ciertos casos. 
Adicionalmente, múltiples operaciones dependían completamente de la intervención manual del equipo de soporte.

Durante la operación diaria se identificaron problemas como:

- Consultas SQL utilizadas para reportes fiscales sin validación efectiva de permisos
- Solicitudes urgentes y repetitivas para ajustes administrativos menores
- Reprocesamientos fiscales simples que requerían intervención directa del soporte

Estos factores generaban una carga operativa constante, aumentaban el riesgo de exposición de información sensible y reducían la capacidad del equipo para enfocarse en incidencias de mayor valor.

### Problemas operativos identificados

A partir del análisis de tickets recurrentes y la operación diaria, se identificaron tres patrones principales que impactaban directamente la eficiencia del soporte y la seguridad operativa.

1. Reportes fiscales basados en SQL sin control de permisos
El sistema permitía generar reportes mediante consultas SQL directas sobre información fiscal.
Sin embargo, estas consultas no aplicaban las restricciones de permisos definidas por rol, lo que permitía que usuarios con acceso limitado pudieran consultar información fiscal fuera de su alcance, como facturas de nómina o documentos de proveedores.

2. Solicitudes urgentes repetitivas para ajustes administrativos
Algunos clientes requerían con frecuencia ajustes menores en numeración interna utilizada para procesos fiscales.
Aunque la acción técnica era simple, la urgencia y recurrencia generaban interrupciones constantes al equipo de soporte.

3. Reprocesamiento manual de documentos fiscales enviados
Ciertas operaciones requerían permitir el reprocesamiento de documentos fiscales ya enviados.
A pesar de ser una acción trivial a nivel técnico, su ejecución manual generaba tickets recurrentes y dependencia innecesaria del soporte.

### Análisis técnico

Para abordar estos problemas, se realizó un análisis detallado de la base de datos en SQL Server con el objetivo de comprender:

- La estructura y relaciones del modelo de datos fiscal
- La forma en que los permisos de usuario estaban definidos y relacionados
- La clasificación de documentos fiscales por tipo (emitidos, recibidos, nómina)
- Reglas de negocio implícitas no documentadas formalmente
- Riesgos asociados a automatizar procesos sin validaciones estrictas

El análisis implicó exploración de tablas, identificación de relaciones no evidentes y validación de variables de entorno disponibles durante la ejecución de reportes y procesos administrativos.

### Diseño de la solución

Las soluciones fueron implementadas principalmente mediante procedimientos almacenados, funciones y validaciones a nivel de base de datos en SQL Server, permitiendo aplicar reglas de negocio fiscales directamente en la capa de datos.

Este enfoque garantizó consistencia, control de acceso y reutilización, reduciendo la dependencia del equipo de soporte para tareas operativas de bajo valor.

#### 1. Control de permisos en reportes SQL

Se implementó un mecanismo para validar dinámicamente los permisos del usuario que ejecuta reportes fiscales:

- Identificación del usuario mediante variables de entorno
- Resolución de permisos según rol y tipo de documento fiscal
- Encapsulación de la lógica en funciones reutilizables
- Filtrado de resultados para permitir solo facturas emitidas, recibidas o de nómina según autorización

Esto permitió mantener la flexibilidad de los reportes sin comprometer la confidencialidad de la información fiscal.

#### 2. Autonomía del cliente para tareas operativas

Se diseñó una opción de autoservicio que permitió a los clientes realizar ajustes administrativos sin intervención directa del soporte:

- Interfaz con acciones limitadas y controladas
- Validaciones estrictas en procedimientos almacenados
- Prevención de estados fiscales inválidos
- Bitácora de auditoría de cada ajuste
- Límites operativos para evitar uso indebido

Esta solución eliminó solicitudes urgentes recurrentes, manteniendo el control fiscal del sistema.

#### 3. Reprocesamiento administrativo controlado

Se habilitó una opción para permitir el reprocesamiento de documentos fiscales bajo condiciones seguras:

- Selección acotada por periodo fiscal
- Ejecución controlada mediante procedimientos almacenados
- Sin impacto en integridad fiscal ni registros históricos

Esto eliminó por completo una categoría específica de tickets recurrentes.

### Impacto y resultados

La implementación de estas soluciones generó mejoras medibles:

- Eliminación total de ciertos tickets recurrentes
- Reducción aproximada del volumen mensual de soporte entre un 6% y 8%
- Menor presión operativa en periodos críticos
- Mayor autonomía de los usuarios
- Mejora en la seguridad y control del acceso a información fiscal
  
### Aprendizajes clave
- Los problemas fiscales críticos suelen originarse por falta de control en el acceso a datos
- SQL Server es una herramienta efectiva para aplicar reglas fiscales cuando se diseña con criterio
- Reducir dependencia del soporte genera más valor que añadir nuevas funcionalidades
- La validación y auditoría son fundamentales en sistemas fiscales
  
### Notas de confidencialidad

Este repositorio documenta el enfoque, análisis y diseño de las soluciones implementadas.
Por razones de confidencialidad, no se incluyen esquemas reales, consultas SQL completas ni fragmentos de código productivo.

Cualquier referencia técnica se describe a nivel conceptual y ha sido generalizada para evitar la exposición de información sensible, estructuras propietarias o datos fiscales reales.

---
## 🇺🇸 English

### Overview

This case study documents a set of internal solutions designed to reduce operational friction and eliminate repetitive support tasks in enterprise fiscal and administrative systems.
The primary focus was not feature development, but process optimization through controlled access to fiscal data, automation, and business rule enforcement directly at the SQL Server level.

The solutions emerged from real production support scenarios, where proper segregation of fiscal information and reduced dependency on the support team were critical requirements.

### Context and Problem Statement

Enterprise systems used by corporate clients handled sensitive fiscal data, including issued invoices, received invoices, and payroll-related documents.
Although role-based permission models existed, in some cases, the validation of these permissions was ommited.
Also, several operational processes still relied entirely on manual support intervention.

Daily operations revealed issues such as:

- SQL-based fiscal reports executed without enforcing user permissions
- Recurrent urgent requests for minor administrative adjustments
- Simple fiscal reprocessing tasks requiring direct support involvement

These factors created constant operational load, increased the risk of sensitive data exposure, and limited the team’s ability to focus on higher-value issues.

### Identified Operational Issues

Through the analysis of recurring support tickets and daily operations, three main inefficiency patterns were identified, directly impacting both support efficiency and operational security.

1. SQL-Based Fiscal Reports Without Permission Enforcement
The system allowed SQL-based reporting over fiscal data.
However, these queries did not enforce role-based permission restrictions, enabling users with limited access to view fiscal information beyond their scope, such as payroll invoices or supplier documents.

2. Recurrent Urgent Requests for Administrative Adjustments
Some clients frequently required minor adjustments to internal numbering used in fiscal processes.
Although technically simple, the urgency and frequency of these requests caused constant support interruptions.

3. Manual Reprocessing of Previously Sent Fiscal Documents
Certain operations required enabling reprocessing of already issued fiscal documents.
Despite being technically trivial, these actions generated recurring tickets due to lack of controlled self-service mechanisms.

### Technical Analysis

To address these issues, a detailed analysis of the SQL Server database was conducted to understand:

- The structure and relationships of the fiscal data model
- How user permissions were defined and linked
- Classification of fiscal documents (issued, received, payroll)
- Implicit business rules not formally documented
- Risks associated with automation without strict validation

This required exploring database tables, identifying non-obvious relationships, and validating environment variables available during report execution and administrative processes.

### Solution Design

The solutions were implemented primarily using SQL Server stored procedures, functions, and data-level validations, enforcing fiscal business rules directly at the database layer.

This approach ensured consistency, access control, and reusability, reducing reliance on support for low-value operational tasks.

#### 1. Secure Access to Fiscal Data in SQL Reports

A mechanism was implemented to dynamically validate the permissions of users executing fiscal reports:

- User identification through environment variables
- Permission resolution based on role and fiscal document type
- Encapsulation of logic into reusable functions
- Result filtering to allow access only to issued, received, or payroll invoices as authorized

This preserved reporting flexibility while maintaining fiscal data confidentiality.

#### 2. Controlled Self-Service for Administrative Adjustments

A controlled self-service option was designed to allow clients to perform administrative adjustments without direct support intervention:

- Limited and controlled actions
- Strict validation via stored procedures
- Prevention of invalid fiscal states
- Audit logging for all adjustments
- Operational limits to prevent misuse

This eliminated recurring urgent requests while preserving fiscal control.

#### 3. Controlled Reprocessing of Fiscal Documents

An option was introduced to allow reprocessing of fiscal documents under controlled conditions:

- Time-bound fiscal period selection
- Execution via controlled stored procedures
- No impact on fiscal integrity or historical records

This fully eliminated a specific category of recurring support tickets.

### Impact and Results

These solutions produced measurable improvements:

- Complete elimination of specific recurring support tickets
- Approximate 6–8% monthly reduction in support volume
- Reduced operational pressure during critical periods
- Increased user autonomy
- Improved security and control over fiscal data access

### Key Learnings
- Critical fiscal issues often stem from insufficient data access control
- SQL Server is effective for enforcing fiscal rules when designed thoughtfully
- Reducing support dependency delivers more value than adding features
- Validation and auditability are essential in fiscal systems

### Notes on Confidentiality
This repository documents the approach, analysis, and design of the implemented solutions.
For confidentiality reasons, real schemas, full SQL queries, and production code snippets are not included.

All technical references are described at a conceptual level and have been generalized to prevent exposure of sensitive data, proprietary structures, or real fiscal information.
