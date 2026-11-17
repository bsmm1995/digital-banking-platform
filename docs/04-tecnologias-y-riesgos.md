# 04. Tecnologías, versiones y riesgos

Para cada tecnología explico **por qué la elegí frente a las alternativas**, qué riesgo trae y cómo lo mitigaría.

## Versiones de referencia (noviembre de 2026)

La regla es usar la última versión LTS cuando el proyecto la publica, y la última estable cuando no. El número exacto se confirma al arrancar el proyecto; lo que importa más es no quedarse nunca más de una versión atrás.

| Tecnología | Versión | Soporte |
|---|---|---|
| Java | 25 LTS | La próxima LTS (29) se espera para septiembre de 2027. |
| Spring Boot (WebFlux) | 4.1, sobre Spring Framework 7 | No tiene LTS comunitario; cada versión menor tiene unos 12 meses de soporte. Se pasa a 4.2 cuando salga estable. |
| Quarkus | 3.40 LTS | Una LTS cada seis meses, con doce meses de parches. |
| GraalVM / Mandrel | 25 | Sigue la versión de Java. |
| Angular | 22 | Desde esta versión sale una mayor por año, con 12 meses de soporte activo y 12 de LTS (hasta junio de 2028). |
| Flutter | 3.47 o la estable más reciente | No tiene LTS: hay una estable cada trimestre y solo la última recibe correcciones. |
| Keycloak | 26.8 | Tampoco tiene LTS; solo la última versión menor recibe parches. Si el banco necesita soporte extendido existe Red Hat build of Keycloak. |

## Riesgos generales

| Riesgo | Cómo lo mitigaría |
|---|---|
| El equipo no domina WebFlux, Kubernetes o FAPI. | Arquetipos de proyecto y lineamientos comunes, capacitación al inicio y un equipo de plataforma pequeño que dé soporte a los equipos de producto. |
| Dependencia de AWS. | La nube queda en los adaptadores (arquitectura hexagonal), PostgreSQL y Valkey existen en otras nubes, la infraestructura está como código y los respaldos búnker están fuera de AWS. |
| Costo que crece sin control. | Presupuestos y alertas por cuenta, etiquetas por servicio, instancias Graviton y revisión mensual. |
| Datos personales fuera de Ecuador. | Cifrado con llaves del banco, consentimiento explícito, cláusulas contractuales con AWS y validación previa con la Superintendencia. |
| Latencia hacia us-east-1. | CloudFront, caché para lecturas frecuentes y llamadas en paralelo en los BFF. |

## Backend

| Tecnología | Por qué | Riesgo | Mitigación |
|---|---|---|---|
| Java 25 | Es el lenguaje de facto en la banca: soporte largo, talento disponible y librerías de seguridad maduras. Todo el backend usa el mismo lenguaje. | En la JVM, los servicios pequeños consumen más memoria de la necesaria. | Quarkus nativo para los servicios livianos y Graviton para bajar costo. |
| Spring Boot 4.1 WebFlux (BFF y dominio) | Reactivo: atiende más solicitudes con menos hilos, que es justo lo que hace un BFF que llama a varios servicios a la vez. Su ecosistema (seguridad, Resilience4j, OpenTelemetry) es el más amplio. | Curva de aprendizaje alta y depuración más difícil. Una sola llamada bloqueante, por ejemplo JDBC, degrada todo el servicio. | R2DBC y clientes no bloqueantes, BlockHound en las pruebas y revisión de código. Para servicios simples, los hilos virtuales de Java 25 son una opción. |
| Quarkus 3.40 nativo (Notificaciones y Auditoría) | Arranca en milisegundos y usa poca memoria, con el mismo lenguaje y las mismas herramientas que el resto del backend. | La compilación nativa es lenta, la reflexión hay que declararla y no todas las librerías son compatibles. | Usarlo solo en servicios simples, probar el binario nativo en el pipeline y mantener el modo JVM como respaldo. |

## Integración y eventos

| Tecnología | Por qué | Riesgo | Mitigación |
|---|---|---|---|
| Amazon API Gateway (público y privado) | Administrado: throttling, cuotas, validación de JWT y WAF sin operar servidores. Frente a Kong o Apigee, menos operación y menor costo inicial. | Límites de tiempo de espera y de tamaño de payload, costo por millón de llamadas a gran volumen y dependencia del proveedor. | Operaciones largas por eventos. Como la lógica está en los BFF, el gateway se puede reemplazar. |
| EventBridge + SQS | Sin clúster que dimensionar; para un banco mediano, Kafka es más de lo necesario. | EventBridge no garantiza orden y entrega al menos una vez; hay límite de tamaño de evento y cuotas por región. | Consumidores idempotentes, SQS FIFO para órdenes de pago, eventos pequeños con referencia a S3 y monitoreo de cuotas. |
| AWS DMS (CDC del core) | Captura los cambios sin modificar el core. Frente a Debezium, no hay que operar Kafka Connect. | Necesita acceso a los logs de la base del core, puede acumular retraso y las tareas a veces se detienen. | Validarlo con el proveedor del core antes de diseñar, alarmas de retraso y reanudación desde el último punto. Debezium queda como plan B. |

## Datos

| Tecnología | Por qué | Riesgo | Mitigación |
|---|---|---|---|
| Aurora PostgreSQL Global | PostgreSQL estándar y portable, con réplica a otra región en menos de un segundo y failover administrado. Sin licencias como Oracle o SQL Server. | Más caro que RDS simple. El RPO es casi cero, pero no cero. | Graviton y lectores según uso; el outbox evita perder eventos durante el failover. |
| ElastiCache Valkey | Compatible con Redis, de código abierto y más barato en AWS. | Mostrar datos desactualizados. | TTL corto, invalidación por eventos del CDC y **el saldo nunca en caché**. |
| DynamoDB (notificaciones y almacén de llaves) | Escala sin administración y replica entre regiones con Global Tables. | Modelo de consultas rígido y particiones calientes. | Diseñar las claves según las consultas conocidas; lo relacional queda en Aurora. |
| S3 Object Lock | Evidencia inmutable para auditoría y KYC a bajo costo. | Una retención mal configurada no se puede corregir después. | Definir los plazos con Cumplimiento antes de activar el modo compliance. |

## Identidad y seguridad

| Tecnología | Por qué | Riesgo | Mitigación |
|---|---|---|---|
| Keycloak 26.8 (clientes) | Código abierto, soporta FAPI 2.0 y passkeys y corre en la nube del banco. Frente a Cognito da más control; frente a Auth0 no cobra por usuario. | Lo opera el banco: actualizaciones frecuentes, alta disponibilidad y parches son responsabilidad propia. | Operador de Keycloak en EKS, clúster en varias zonas y actualizaciones planificadas. Soporte comercial si hace falta. |
| Microsoft Entra ID (empleados) | Es el estándar corporativo en la banca del país, con SSO, MFA y acceso condicional. | Licencias (el acceso condicional requiere planes superiores) y dependencia del proveedor. | Usar el tenant que el banco ya tiene; la integración es OIDC estándar. |
| Passkeys con biometría del teléfono | Resisten phishing y la biometría nunca sale del dispositivo. | Pérdida o cambio de teléfono, y usuarios poco acostumbrados. | Recuperación con una nueva prueba de vida; OTP como respaldo temporal. |
| Proveedor de KYC (Facephi, FaceTec, iProov) | Prueba de vida certificada y detección de deepfakes; construirlo internamente no es viable. | Costo por verificación, dependencia del proveedor y disponibilidad de SDK para Flutter. | Adaptador propio para poder cambiar de proveedor y verificar el plugin de Flutter durante la evaluación. |

## Frontend

| Tecnología | Por qué | Riesgo | Mitigación |
|---|---|---|---|
| Angular 22 (web) | Estructura definida, TypeScript obligatorio y protección contra XSS integrada. Muy usado en banca. Frente a React, menos decisiones por equipo y más consistencia. | Migraciones obligatorias: desde la versión 22 sale una mayor por año con unos 24 meses de soporte. Si se acumulan versiones, actualizar se vuelve un proyecto. También está en pleno cambio a signals y sin zone.js. | Actualizar con `ng update` en cada versión mayor, como parte del ciclo normal, y una librería de componentes propia. |
| Flutter 3.47 (móvil) | Un solo código para Android e iOS con rendimiento cercano al nativo; buena presencia en fintech. Frente a React Native, la interfaz es más consistente entre plataformas. | Sin LTS, hay que seguir las versiones trimestrales. Menos talento en Dart, dependencia de Google, apps más pesadas, y los SDK nativos (KYC, atestación) necesitan plugins. | Contract-first para generar los clientes, platform channels para los SDK nativos y capacitación del equipo. |

## Infraestructura

| Tecnología | Por qué | Riesgo | Mitigación |
|---|---|---|---|
| EKS | Kubernetes es portable y permite service mesh con mTLS, políticas de red y escalado fino. Frente a ECS, más portable y con más ecosistema. | Complejidad operativa: necesita un equipo de plataforma y actualizar Kubernetes seguido. | Add-ons administrados, infraestructura como código y actualizaciones planificadas. Si el equipo es muy pequeño, ECS Fargate es una alternativa. |
| Respaldos en Oracle Cloud Bogotá | Copia inmutable fuera de AWS ante una falla total o un ataque a la cuenta, en una región cercana. | Costo de salida de datos y un proveedor más que administrar. | Solo respaldos, nada en operación, y una restauración de prueba al menos una vez al año. |
