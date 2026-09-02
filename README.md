# EP 1.1 - Diseño y Estructura Inicial

**FocalWare** · Priorización de limpieza por riesgo de incendio en las quebradas de Valparaíso

[← Volver al README](../README.md)
[← Volver a EP 1.2](EP1.2-usuarios-y-protopersonas.md)

---

## 1.1 Requerimientos del Sistema

El presente apartado detalla la especificación formal de requisitos del sistema FocalWare, estableciendo tanto las capacidades funcionales esperadas por los diferentes perfiles de usuario como las directrices de rendimiento, seguridad y accesibilidad que sustentan su despliegue.

La columna Usuario indica el perfil que ejecuta el requerimiento. Cuando corresponde a **Sistema**, la acción se dispara de forma automática sin intervención directa de un usuario.

> **1.1.1 Requerimientos Funcionales**

| ID | Nombre | Requerimiento | Tipo | Dependencias con otro Requerimiento | Usuario |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RF-01** | Registro de reporte georreferenciado | El sistema debe permitir al vecino crear un reporte con ubicación GPS, fotografías, categoría de residuo, volumen estimado y descripción. | Transaccional | Ninguna | Vecino |
| **RF-02** | Almacenamiento local offline | El sistema debe permitir guardar localmente los reportes creados sin conexión a internet y enviarlos automáticamente al servidor cuando se restablezca la conectividad, sin generar duplicados. | Transaccional | RF-01 | Vecino / Sistema |
| **RF-03** | Visualización en mapa interactivo | El sistema debe desplegar un mapa interactivo con localizaciones asociadas a los reportes existentes en el sistema. | Interfaces externas | RF-01 | Vecino / Funcionario |
| **RF-04** | Filtrado del mapa interactivo | El sistema debe permitir filtrar el mapa interactivo por estado, categoría, riesgo, sector y fecha. | Requisito de búsqueda y reportes | RF-03 | Vecino / Funcionario |
| **RF-05** | Cálculo de índice ponderado | El sistema debe calcular un índice ponderado según categoría, volumen, apoyos recibidos, reportes cercanos, antigüedad y condiciones meteorológicas externas. | Algoritmo | RF-01, RF-11 | Sistema |
| **RF-06** | Aplicación del índice ponderado | El sistema utilizará el índice ponderado para priorizar la cola de atención municipal de los reportes recibidos. | Regla de negocio | RF-05 | Sistema |
| **RF-07** | Gestión municipal de incidentes | El sistema debe permitir a los funcionarios ver la cola priorizada, asignar cuadrilla, programar atención, cambiar estado y adjuntar evidencia de cierre. El rechazo de un reporte requiere motivo obligatorio. | Niveles de autorización | RF-06 | Funcionario |
| **RF-08** | Notificación y trazabilidad de reportes | El sistema debe notificar al autor de cada reporte los cambios de estado realizados y exponer el historial completo de transiciones con fecha, estado y responsable. | Auditoría | RF-07 | Sistema |
| **RF-09** | Panel y métricas de reportes | El sistema debe mostrar reportes por estado y categoría, tiempo promedio de resolución por sector y evolución mensual, permitiendo exportar el conjunto filtrado en formato CSV. | Requisito de búsqueda y reportes | RF-07 | Funcionario |
| **RF-10** | Identificación de puntos críticos recurrentes | El sistema debe marcar las ubicaciones con un número configurable de reportes cerrados dentro de una ventana temporal configurable. | Regla de negocio | RF-01, RF-07 | Sistema |
| **RF-11** | Validación de duplicados y orden de prioridad | El sistema deberá validar que los reportes subidos por los usuarios no se encuentren ya en existencia en la base de datos dentro de un radio configurable. Si existe, enviará el mensaje "Ya existe un antecedente previo de este reporte" al usuario, incorporando un sistema de apoyos que incide en el orden de prioridad. | Integridad de los datos | RF-01, RF-02 | Sistema / Vecino |

---

> **1.1.2 Requerimientos No Funcionales**

| ID | Nombre | Requerimiento | Tipo | Dependencias | Usuario |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RNF-01** | Límite de pasos de reporte | El usuario deberá crear un reporte en máximo 3 pasos y en menos de 60 segundos en condiciones normales de operación. | Usabilidad | RF-01 | Vecino |
| **RNF-02** | Dimensiones mínimas y accesibilidad | El sistema debe tener controles de al menos 44x44 px y texto base de 16 px respetando las normas de WCAG 2.1 Nivel AA. | Accesibilidad | Ninguna | Vecino / Funcionario |
| **RNF-03** | Disponibilidad sin conexión | El sistema deberá permanecer operativo para la creación de reportes en ausencia de conectividad a internet, garantizando que no se pierdan datos ante el cierre de la aplicación. | Disponibilidad / Integridad de los datos | RF-02 | Vecino |
| **RNF-04** | Cifrado de credenciales | El sistema debe realizar bcrypt con mínimo 10 rondas. | Seguridad | Ninguna | Sistema |
| **RNF-05** | Expiración y rotación de tokens | El sistema debe aplicar JWT con expiración de 15 minutos, algoritmo asimétrico y mecanismo de rotación. | Seguridad | Ninguna | Sistema |
| **RNF-06** | Restricción CORS | La API del sistema debe restringir el intercambio de recursos de origen cruzado (CORS) mediante una lista blanca (allowlist) explícita de dominios autorizados, denegando el uso de comodines (*) en endpoints autenticados y restringiendo los métodos y cabeceras HTTP a los estrictamente necesarios. | Seguridad | Ninguna | Sistema |
| **RNF-07** | Disociación y anonimización | El sistema debe asegurar que la generación y publicación de reportes de acceso público garantice la disociación total de la identidad de los autores, impidiendo su reidentificación directa o indirecta, en conformidad con la Ley N° 19.628, el Decreto Supremo N° 779 (2000) del Ministerio de Justicia y la norma técnica establecida en el Decreto Supremo N° 1 (2015) del Ministerio Secretaría General de la Presidencia. | Privacidad | RF-01, RF-03 | Vecino |
| **RNF-08** | Tiempo de respuesta de la API | Los endpoints del sistema deben responder en menos de 500 ms en el percentil 95 con una base de al menos 5.000 reportes registrados, aplicando paginación e índices sobre los campos de estado, coordenadas y fecha de creación. | Rendimiento | RF-04, RF-09 | Sistema |
| **RNF-09** | Compatibilidad móvil | El sistema debe ser compatible con Android 9 o superior, iOS 14 o superior. | Portabilidad | Ninguna | Vecino |
| **RNF-10** | Compatibilidad web | El sistema debe ser compatible con navegadores web Google Chrome, Mozilla Firefox, Microsoft Edge y Safari en sus versiones actuales y anteriores (N-2). | Portabilidad | Ninguna | Funcionario |
| **RNF-11** | Contenerización y despliegue | La arquitectura del sistema debe estar completamente contenerizada, requiriendo únicamente Docker y Docker Compose para su despliegue y desacoplando la configuración del código fuente mediante variables de entorno. | Arquitectónico | Ninguna | Sistema |
| **RNF-12** | Rendimiento de renderizado cartográfico | El mapa interactivo debe renderizar hasta 1.000 marcadores en menos de 2 segundos mediante agrupación en cliente, y las imágenes deben comprimirse a un máximo de 300 KB antes de su transmisión. | Rendimiento | RF-03 | Vecino / Funcionario |

---

## Notas de la especificación

**Requerimientos excluidos.** El inicio de sesión y el registro de usuarios no se contabilizan como requerimientos funcionales, ya que corresponden a funcionalidades transversales de soporte y no a capacidades propias del problema abordado.

**Creación de reportes.** La creación de reportes está restringida al rol Vecino. El rol Funcionario accede a los reportes en modo lectura y gestión, sin poder originarlos. Esta decisión preserva la separación de responsabilidades entre quien aporta la información territorial y quien decide sobre su atención.
