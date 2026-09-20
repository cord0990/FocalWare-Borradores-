# EP 1.3 · Bocetos de UI/UX y prototipo en Figma

**FocalWare** · Priorización de limpieza por riesgo de incendio en las quebradas de Valparaíso

[← Volver al README](../README.md)
[← Volver a EP 1.1](EP1.1-Requerimientos-del-sistema.md)

---

> **Nota de trabajo, eliminar antes de entregar.**
> Las marcas indican elementos que el documento afirma pero que aún no están dibujados en Figma.
> 🔴 Obligatorio · 🟡 Recomendado · 🔵 Opcional
> Al terminar cada uno, borrar su marca. Si algo 🔵 no alcanza a hacerse, quitar esa frase del texto.

---

## Proceso de diseño

El diseño fue elaborado manualmente por el equipo en Figma, utilizando formas, marcos, componentes, estilos, Auto Layout y conexiones de prototipado. No se utilizó el asistente de inteligencia artificial de Figma ni otras herramientas de IA generativa para crear pantallas, componentes, estilos o estructuras de navegación.

🔴 El trabajo partió con bocetos preliminares de baja fidelidad para definir la distribución de contenido y la jerarquía de información de cada vista. Sobre esos bocetos se construyó el prototipo de alta fidelidad, ajustando espaciado, tipografía y componentes de navegación según el dispositivo.

🟡 Se armaron como componentes maestros reutilizables la tarjeta de reporte, la etiqueta de estado y el indicador de riesgo, por repetirse en al menos cinco pantallas distintas.

**Enlace al prototipo:** [Figma](https://www.figma.com/proto/NMUudu0YPxumGuZedsLVDS/FocalWare?node-id=0-1&t=nKgN063FMb7m3uT6-1)

## Paleta de color

| Color | Hex | Uso |
|---|---|---|
| Blanco | `#FFFFFF` | Fondo base |
| Durazno claro | `#FFE8C9` | Fondo secundario / superficies |
| Rosa claro | `#FFE9E7` | Fondo secundario alternativo |
| Naranja | `#FCB860` | Acento cálido / riesgo medio |
| Coral | `#F45648` | Acento primario / enlaces |
| Rojo | `#E72441` | Botón principal / riesgo alto |
| Mauve gris | `#C6ACAA` | Bordes / texto secundario |
| Gris oscuro | `#423C3D` | Texto primario |

La escala de riesgo no depende únicamente del color: cada nivel se acompaña siempre de su etiqueta textual (Alto, Medio, Bajo), en cumplimiento de RNF-02 sobre accesibilidad.

## Pantallas y su relación con los requerimientos

| Pantalla | Requerimiento | Móvil | Web |
|---|---|---|---|
| Inicio de sesión | Transversal de soporte | Diseñada | Diseñada |
| Crear cuenta | Transversal de soporte | Diseñada | Diseñada |
| Recuperar contraseña (3 pasos) | Transversal de soporte | 🔵 | Diseñada |
| Inicio sin sesión | RF-03 | 🟡 marco vacío | Diseñada |
| Crear reporte, paso 1 (foto y ubicación) | RF-01, RF-02 | Diseñada | 🟡 |
| Crear reporte, paso 2 (categoría, volumen, distancia) | RF-01 | Diseñada | 🟡 |
| Modal de reporte duplicado | RNF-08 | Diseñada | 🔵 |
| Mapa de reportes | RF-03, RF-04, RF-10 | Diseñada | Diseñada |
| Mis reportes | RF-02, RF-08 | Diseñada | 🟡 |
| Bandeja de triage municipal | RF-06, RF-07 | 🟡 | 🔴 |
| Detalle de reporte, vista funcionario | RF-05, RF-08 | 🟡 | 🔴 |
| Asignación y cierre | RF-07 | 🟡 | 🔴 |
| Panel de indicadores | RF-09, RF-10 | 🔵 | 🔴 |

**RF-05 y RF-06**, el cálculo y la aplicación del índice ponderado, son procesos automáticos del sistema sin interfaz propia. Se hacen visibles en dos lugares: el indicador de riesgo con su nivel textual en el mapa y en Mis reportes, y el desglose del puntaje en la vista de detalle del funcionario, donde se muestran los factores que lo componen.

**RNF-08**, la validación de duplicados, se materializa en el modal que aparece antes de confirmar un reporte nuevo, ofreciendo apoyar el existente.

## Arquitectura visual y adaptación entre dispositivos

Ambas plataformas comparten las mismas cuatro secciones: Mapa, Reportar, Mis reportes y Perfil.

**Móvil.** Navegación mediante barra inferior fija de cuatro pestañas. Las vistas ocupan el ancho completo y el contenido se presenta de forma secuencial. El flujo de creación de reporte se divide en pasos para cumplir RNF-01. 🟡

**Web.** Navegación mediante menú lateral fijo con los mismos destinos. El ancho adicional permite mostrar contenido complementario de forma simultánea, por ejemplo el mapa junto al listado de reportes del área, en lugar de alternar entre vistas. La bandeja de triage del funcionario se diseña primero en esta versión, dado que el uso previsto de ese rol es de escritorio. 🟡

## Control de acceso reflejado en el prototipo

El mapa y el nivel de riesgo de los reportes son visibles sin iniciar sesión, decisión coherente con el propósito de transparencia hacia la comunidad y con el perfil de usuarios caracterizado en EP 1.2.

🟡 Crear un reporte o apoyar uno existente requiere sesión iniciada. El prototipo contempla el punto de interacción en que un usuario sin sesión intenta una de esas acciones y es derivado al inicio de sesión.

El inicio de sesión es único para ambos roles. Tras autenticar, el vecino accede al Mapa y el funcionario a la Bandeja municipal. El registro público está disponible solo para el rol Vecino; las cuentas del rol Funcionario son creadas por un administrador, por lo que no se diseña un flujo de registro municipal.

## Formulario de registro

| Campo | Obligatorio | Justificación | En Figma |
|---|---|---|---|
| Nombre para mostrar | Sí | Identifica al vecino dentro de la comunidad sin exponer su nombre legal completo | 🔴 |
| Correo electrónico | Sí | Identificador único de la cuenta y canal de recuperación de contraseña | Está |
| Contraseña | Sí | Seguridad de la cuenta | Está |
| Confirmar contraseña | Sí | Evita errores de tipeo | 🔴 |
| Cerro o unidad vecinal | Sí | Permite ubicar contextualmente los reportes sin solicitar dirección exacta | 🔴 |
| Teléfono | No | Canal alternativo de notificación, a elección del usuario | 🔴 |
| Aceptación de términos y política de privacidad | Sí | Consentimiento informado previo a habilitar la cuenta | 🔴 sin texto |

No se solicita RUT ni dirección exacta. Ninguno de los dos es necesario para el funcionamiento de la aplicación, y omitirlos reduce el riesgo de exponer datos personales del reportante, en línea con RNF-07 sobre disociación y anonimización.

### Representación visual del formulario

El prototipo representa explícitamente:

- 🔴 **Campos obligatorios y opcionales.** Los obligatorios se marcan con asterisco; los opcionales llevan la etiqueta "(opcional)" junto al nombre del campo.
- 🔴 **Formato esperado de los datos.** Cada campo muestra un texto de ayuda con el formato requerido, por ejemplo `nombre@correo.cl` para el correo.
- 🔴 **Validaciones de entrada.** El nombre exige al menos 3 caracteres, el correo debe tener formato válido y la confirmación de contraseña debe coincidir.
- 🔴 **Mensajes de error claros.** Cada campo que falla muestra su propio mensaje bajo el campo, indicando qué corregir.
- 🔴 **Condiciones de seguridad de la contraseña.** Mínimo de 8 caracteres, con indicador visual de robustez que se actualiza mientras el usuario escribe.
- 🟡 **Retroalimentación ante el envío.** El botón principal permanece deshabilitado hasta que se aceptan los términos y muestra un estado de carga durante el envío. 🔵 Al completarse se despliega una confirmación de cuenta creada.

## Formulario de inicio de sesión

Solicita correo y contraseña.

🔴 A diferencia del registro, el mensaje de error es genérico y no indica cuál de los dos campos falló, para no revelar si una cuenta existe en el sistema. 🟡 Incluye un estado de carga en el botón durante la validación 🔵 y la opción de mostrar u ocultar la contraseña. El enlace hacia el flujo de recuperación de contraseña ya está implementado.

## Estados considerados

Además de los flujos exitosos, el prototipo contempla:

- 🔴 **Estados vacíos.** Mis reportes sin reportes creados, y mapa o bandeja sin resultados tras aplicar filtros.
- **Estados de la cola offline.** Reporte sincronizando con porcentaje de avance. Corresponde a RF-02 y RNF-03.
- 🔴 Reporte pendiente de envío y 🟡 reporte con error de envío con opción de reintentar.

---

## Pendientes en Figma

### 🔴 Obligatorio

Lo exige la pauta o su ausencia deja el documento afirmando algo falso.

1. Bandeja de triage municipal, versión web
2. Detalle de reporte vista funcionario, versión web
3. Asignación y cierre, versión web
4. Panel de indicadores, versión web
5. Bocetos de baja fidelidad guardados en un frame de Figma
6. Completar campos del registro: nombre, confirmar contraseña, cerro o unidad vecinal, teléfono
7. Texto real en el checkbox de términos, hoy dice "Label / Description"
8. Asteriscos en campos obligatorios y etiqueta "(opcional)" en teléfono
9. Textos de ayuda con el formato esperado en cada campo
10. Variante del registro con al menos un campo en estado de error
11. Indicador de robustez de contraseña
12. Variante del login con mensaje de error genérico
13. Mis reportes vacío
14. Mapa o bandeja sin resultados tras filtrar
15. Reporte pendiente de envío en la cola offline

### 🟡 Recomendado

Suma puntos en cobertura y coherencia, pero no deja el documento en falso.

16. Versión móvil de las cuatro pantallas del funcionario
17. Crear reporte paso 1 y 2, versión web
18. Mis reportes, versión web
19. Inicio sin sesión móvil, hoy es un marco vacío
20. Barra inferior en "Pantalla principal - Post login" móvil, hoy tiene botones superiores
21. Menú lateral en todas las vistas desktop, hoy solo está en "Reportes"
22. Conexión desde el mapa público hacia el login al intentar reportar o apoyar
23. Botón deshabilitado y estado de carga en el registro
24. Reporte con error de envío y botón de reintentar
25. Componentes maestros de tarjeta de reporte, etiqueta de estado e indicador de riesgo

### 🔵 Opcional

Si no alcanza el tiempo, se quita la frase del documento y no pasa nada.

26. Recuperar contraseña en móvil, 3 pantallas
27. Modal de duplicado, versión web
28. Panel de indicadores, versión móvil
29. Pantalla de confirmación de cuenta creada
30. Ícono de mostrar u ocultar contraseña en login
