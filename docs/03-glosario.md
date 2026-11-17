# 03. Glosario

Siglas y términos que aparecen en la propuesta y que no son de uso diario.

## Negocio y regulación

| Término | Significado |
|---|---|
| BCE | Banco Central del Ecuador, administra los pagos entre entidades financieras. |
| SPI | Sistema de Pagos Interbancarios del BCE. Liquida por cortes durante el día y no tiene límite de monto. SPI1 es el formato de archivo con el que cada banco envía sus órdenes. |
| Red de Pagos Instantáneos | Transferencias entre entidades en segundos, todos los días, con llaves o QR y un monto máximo regulado. |
| ISO 20022 | Estándar de mensajería financiera. pacs.008 es la orden de pago y pacs.002 la confirmación o el rechazo. |
| Prospecto | Persona que todavía no es cliente y está haciendo su registro. |
| KYC | Know Your Customer: verificar la identidad antes de abrir una cuenta. |
| LAFT / UAFE | Prevención de lavado de activos y financiamiento del terrorismo. La UAFE emite las listas de control locales. |
| PEP | Persona expuesta políticamente, requiere debida diligencia reforzada. |
| LOPDP / SPDP | Ley Orgánica de Protección de Datos Personales y la Superintendencia que la supervisa. |
| SLA | Acuerdo de nivel de servicio: la disponibilidad y los tiempos de respuesta que un proveedor se compromete a cumplir por contrato. |

## Arquitectura

| Término | Significado |
|---|---|
| BFF | Backend for Frontend: un backend por canal que orquesta y adapta las respuestas para ese canal. |
| ACL | Anti-Corruption Layer: traduce el modelo del sistema legado para que no contamine el resto. |
| Saga | Secuencia de pasos con compensación: si uno falla, se deshacen los anteriores (por ejemplo, con un reverso). |
| Outbox | Guardar el evento en la misma transacción que el cambio de datos y publicarlo después, para no perder eventos. |
| Idempotency-Key | Identificador de la solicitud que evita ejecutar dos veces la misma operación ante un reintento. |
| CDC | Change Data Capture: leer los cambios de una base de datos y publicarlos como eventos sin tocar la aplicación origen. |
| LFU | Least Frequently Used: la caché descarta primero lo que menos se consulta. |
| DLQ | Cola donde quedan los mensajes que fallaron después de varios reintentos. |
| WORM | Write Once, Read Many: almacenamiento que no permite modificar ni borrar. |
| Envelope encryption | Los datos se cifran con una llave por cliente, y esa llave se cifra con una llave maestra de KMS. Así bastan pocas llaves en KMS para millones de clientes. |
| Crypto-shredding | Borrar datos destruyendo su llave: quedan ilegibles incluso en los respaldos. |
| RTO / RPO | RTO es el tiempo máximo para recuperar el servicio; RPO, la cantidad máxima de datos que se puede perder. |
| T0 a T3 | Niveles de criticidad para DR. T0 es lo más crítico y se recupera más rápido. |
| Warm standby / pilot light | Estrategias de DR: una réplica mínima siempre encendida, o solo los datos replicados con el cómputo levantándose al conmutar. |
| Imagen nativa | Compilar una aplicación Java a un ejecutable sin JVM (GraalVM o Mandrel). Arranca en milisegundos y usa menos memoria. |

## Seguridad e identidad

| Término | Significado |
|---|---|
| FAPI 2.0 | Perfil de seguridad de OAuth para banca y Open Finance. |
| PKCE | Extensión de OAuth que impide reutilizar un código de autorización robado. |
| PAR | Los parámetros del login se envían servidor a servidor, no por el navegador. |
| DPoP | Liga el token a una llave del dispositivo; robado, no sirve en otro equipo. |
| Step-up | Pedir una autenticación más fuerte para operaciones sensibles. |
| Passkey (FIDO2) | Credencial sin contraseña que se desbloquea con la biometría del teléfono. |
| ISO 30107-3 / CEN/TS 18099 | Certificaciones de prueba de vida: la primera contra fotos y máscaras, la segunda contra ataques de inyección como deepfakes. |
| Atestación de app | Play Integrity (Android) y App Attest (iOS) confirman que la app es la original y corre en un dispositivo real. |
| mTLS | TLS en el que cliente y servidor se autentican con certificados. |

## Infraestructura AWS

| Término | Significado |
|---|---|
| EKS / Karpenter | Kubernetes administrado de AWS; Karpenter agrega o quita nodos según la carga. |
| ALB / NLB | Balanceadores: el ALB entiende HTTP (se usa para el login); el NLB trabaja a nivel de red y es el que conecta el API Gateway con EKS por VPC Link. |
| Route 53 ARC | Control de conmutación entre regiones. |
| Aurora Global Database | PostgreSQL administrado con réplica a otra región en menos de un segundo. |
| DMS / Kinesis / Pipes | DMS captura los cambios del core, Kinesis los transporta y EventBridge Pipes los publica en el bus. |
| KMS / Secrets Manager | Llaves de cifrado / credenciales que usan los servicios (con rotación automática). |
| Direct Connect / DX Gateway | Enlace dedicado entre el data center del banco y AWS; el DX Gateway permite usarlo desde ambas regiones. |
| Respaldo búnker | Copia inmutable de los respaldos fuera de AWS, en Oracle Cloud Bogotá. |
