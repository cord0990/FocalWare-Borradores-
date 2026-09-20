# EP 1.4 · Arquitectura de Navegación y Experiencia de Usuario

**FocalWare** · Priorización de limpieza por riesgo de incendio en las quebradas de Valparaíso

[← Volver al README](../README.md)
[← Volver a EP 1.3](EP1.3-diseno-ui-ux.md)

---

## 1. Estructura de rutas

La aplicación organiza sus rutas en cuatro grupos según el nivel de acceso requerido.

### 1.1 Rutas públicas

Accesibles sin sesión iniciada.

| Ruta | Vista | Descripción |
|---|---|---|
| `/` | Inicio | Presentación del proyecto. Si existe sesión activa, redirige según el rol |
| `/mapa` | Mapa de reportes | Visualización de reportes y su nivel de riesgo (RF-03, RF-04) |
| `/login` | Inicio de sesión | Autenticación única para ambos roles |
| `/registro` | Crear cuenta | Registro disponible solo para el rol Vecino |
| `/recuperar` | Recuperar contraseña | Solicitud de código por correo |
| `/recuperar/codigo` | Validación de código | Ingreso del código recibido |
| `/recuperar/nueva` | Nueva contraseña | Definición de la nueva credencial |

La ruta `/mapa` es pública en todos los casos. Lo que cambia según el estado de sesión no es el acceso a la vista, sino las acciones disponibles dentro de ella: sin sesión solo se consulta, con sesión de Vecino se habilita apoyar y reportar, y con sesión de Funcionario se habilitan los filtros de gestión.

### 1.2 Rutas protegidas del rol Vecino

Requieren sesión activa con rol `vecino`.

| Ruta | Vista | Requerimiento |
|---|---|---|
| `/reportar` | Crear reporte, paso 1 | RF-01, RF-02 |
| `/reportar/detalle` | Crear reporte, paso 2 | RF-01 |
| `/mis-reportes` | Listado propio y cola de sincronización | RF-02, RF-08 |
| `/mis-reportes/:id` | Detalle e historial de un reporte | RF-08 |

La verificación de duplicados de RNF-08 no constituye una ruta. Se resuelve mediante un componente modal que se despliega sobre el paso 2 antes de confirmar el envío, por lo que no modifica la URL ni el historial de navegación.

### 1.3 Rutas protegidas del rol Funcionario

Requieren sesión activa con rol `funcionario`.

| Ruta | Vista | Requerimiento |
|---|---|---|
| `/municipal/bandeja` | Cola priorizada por índice de riesgo | RF-06, RF-07 |
| `/municipal/reporte/:id` | Detalle con desglose del índice | RF-05, RF-08 |
| `/municipal/reporte/:id/gestion` | Asignación de cuadrilla y cierre | RF-07 |
| `/municipal/indicadores` | Panel de métricas y puntos críticos | RF-09, RF-10 |

El prefijo `/municipal` agrupa las rutas del rol Funcionario bajo un mismo espacio de nombres, lo que permite aplicar la verificación de rol a nivel de rama completa en lugar de ruta por ruta.

### 1.4 Rutas compartidas

Requieren sesión activa, sin distinción de rol.

| Ruta | Vista | Descripción |
|---|---|---|
| `/perfil` | Perfil de usuario | Datos de cuenta y preferencias. El contenido varía según el rol de la sesión |

## 2. Jerarquía de vistas

```
Raíz
│
├── Zona pública
│   ├── Inicio
│   ├── Mapa de reportes
│   └── Autenticación
│       ├── Inicio de sesión
│       ├── Crear cuenta
│       └── Recuperar contraseña
│           ├── Solicitud de código
│           ├── Validación de código
│           └── Nueva contraseña
│
├── Zona Vecino
│   ├── Crear reporte
│   │   ├── Paso 1: foto y ubicación
│   │   └── Paso 2: categoría, volumen y distancia
│   │       └── Modal de duplicado (superpuesto)
│   └── Mis reportes
│       └── Detalle de reporte propio
│
├── Zona Funcionario
│   ├── Bandeja de triage
│   │   └── Detalle de reporte
│   │       └── Asignación y cierre
│   └── Panel de indicadores
│
└── Zona compartida
    └── Perfil
```

La recuperación de contraseña es una secuencia lineal de tres niveles porque cada paso depende de la validación del anterior. La creación de reporte es una secuencia de dos pasos, decisión que responde a RNF-01, que limita el flujo a un máximo de tres pasos.

## 3. Diferenciación de acceso según roles

### 3.1 Autenticación unificada

Existe un único formulario de inicio de sesión para ambos roles. No se presenta al usuario una elección previa de perfil.

La decisión responde a dos criterios. El primero es de seguridad. Un selector de rol previo revelaría a un atacante qué tipos de cuenta existen en el sistema, y un mensaje de error diferenciado permitiría enumerar cuentas válidas. El segundo es de usabilidad. Elimina un paso de decisión innecesario, dado que el usuario ya sabe qué perfil tiene y el sistema puede determinarlo a partir de sus credenciales.

Tras validar las credenciales, el backend devuelve el rol dentro del token y el frontend redirige según corresponda.

| Rol | Destino tras autenticar |
|---|---|
| `vecino` | `/mapa` |
| `funcionario` | `/municipal/bandeja` |

### 3.2 Creación de cuentas

El registro público está disponible únicamente para el rol Vecino. Las cuentas del rol Funcionario son creadas por un administrador.

La justificación es de verificación de identidad. El sistema no dispone de un mecanismo confiable para comprobar que quien declara ser funcionario municipal efectivamente lo es. Permitir el auto-registro con rol elevado habilitaría que cualquier persona accediera a datos personales de reportantes y modificara el estado de reportes. La creación administrada traslada esa verificación a un proceso externo al sistema.

### 3.3 Acceso público parcial

El mapa y el nivel de riesgo de los reportes son visibles sin sesión. Crear un reporte o apoyar uno existente requiere autenticación.

Esta división responde al propósito del proyecto y al perfil de usuarios caracterizado en EP 1.2. La transparencia hacia la comunidad forma parte del valor de la plataforma, y exigir registro solo para consultar información elevaría la barrera de entrada en un público con alfabetización digital heterogénea. La autenticación se exige únicamente en las acciones que escriben datos o que inciden en la priorización municipal.

### 3.4 Matriz de acceso por vista

| Vista | Sin sesión | Vecino | Funcionario |
|---|---|---|---|
| Inicio | Accede | Redirige a `/mapa` | Redirige a `/municipal/bandeja` |
| Mapa de reportes | Solo consulta | Consulta, apoya y reporta | Consulta con filtros de gestión |
| Detalle de reporte | No | Solo los propios | Todos, con datos del reportante |
| Crear reporte | No | Sí | No |
| Mis reportes | No | Sí | No |
| Bandeja de triage | No | No | Sí |
| Asignación y cierre | No | No | Sí |
| Panel de indicadores | No | No | Sí |
| Perfil | No | Sí | Sí |

El rol Funcionario no puede crear reportes. La restricción preserva la separación entre quien aporta la información territorial y quien decide sobre su atención, y evita el conflicto de que un mismo usuario origine y gestione un mismo caso.

## 4. Flujos de tareas principales

### 4.1 Crear un reporte (Vecino)

```
Mapa → botón flotante Reportar
     → Paso 1: capturar foto y confirmar ubicación GPS
     → Paso 2: categoría, volumen y distancia a viviendas
     → Verificación automática de duplicados
        ├── Existe reporte cercano → se despliega el modal
        │      ├── Apoyar el existente → Mis reportes
        │      └── Crear uno nuevo → continúa
        └── No existe → continúa
     → Envío
        ├── Con conexión → confirmación → Mis reportes
        └── Sin conexión → guardado local → cola de sincronización
```

El flujo cumple RNF-01 con dos pasos de captura más la confirmación, dentro del límite de tres.

### 4.2 Atender un reporte (Funcionario)

```
Bandeja de triage (ordenada por índice de riesgo)
     → Seleccionar reporte
     → Detalle con desglose del índice
     → Gestión
        ├── Asignar cuadrilla y programar fecha → estado Programado
        ├── Cerrar con evidencia fotográfica → estado Resuelto
        └── Rechazar con motivo obligatorio → estado Rechazado
     → Notificación automática al reportante (RF-08)
     → Retorno a la bandeja
```

### 4.3 Consultar el mapa sin sesión

```
Inicio → Mapa público
     → Seleccionar marcador → resumen con nivel de riesgo
     → Intentar apoyar o reportar
        → Redirección a Inicio de sesión
        → Tras autenticar, retorno a la acción pendiente
```

## 5. Puntos críticos de interacción

Se identifican los momentos del flujo donde una falla de diseño produce abandono o pérdida de datos.

| Punto crítico | Riesgo | Tratamiento |
|---|---|---|
| Captura de ubicación GPS | Señal imprecisa o ausente en quebradas | Permitir ajuste manual del pin y mostrar la precisión estimada |
| Envío sin conectividad | Pérdida del reporte y del esfuerzo del usuario | Guardado local automático y cola de sincronización visible (RF-02) |
| Detección de duplicado | El usuario crea un reporte redundante o abandona | Modal que explica la consecuencia de cada opción y muestra el reporte existente |
| Acción restringida sin sesión | Abandono al encontrarse con un muro de registro | Redirección al login conservando la acción pendiente, y retorno automático |
| Rechazo de un reporte | El vecino no entiende por qué se descartó | Motivo obligatorio para el funcionario, visible en el historial del reporte |
| Cierre de un reporte | Falta de evidencia de que el trabajo se realizó | Fotografía de cierre obligatoria para marcar como Resuelto |
| Sesión expirada durante la gestión | Pérdida de datos ingresados en el formulario | Aviso previo a la expiración y conservación del formulario tras reautenticar |

## 6. Coherencia de experiencia entre dispositivos

Ambas plataformas exponen las mismas cuatro secciones principales, con componentes de navegación distintos según las convenciones de cada entorno.

| Aspecto | Móvil | Web |
|---|---|---|
| Componente de navegación | Barra inferior fija (`IonTabs`) | Menú lateral fijo (`IonMenu`) |
| Secciones | Mapa, Reportar, Mis reportes, Perfil | Las mismas cuatro |
| Densidad de información | Una vista a la vez, contenido secuencial | Vistas simultáneas, por ejemplo mapa junto al listado |
| Bandeja de triage | Tarjetas apiladas con datos esenciales | Tabla densa con todas las columnas |
| Creación de reporte | Flujo por pasos con indicador de progreso | Formulario en columna única |
| Detección de duplicado | Modal a pantalla completa | Diálogo centrado sobre la vista |

Las rutas son idénticas en ambas plataformas. La presentación varía según el dispositivo; la estructura de navegación se mantiene. De este modo, un enlace compartido conduce a la misma vista en cualquier dispositivo.

La bandeja de triage se diseña primero en la versión web, dado que el contexto de uso del rol Funcionario es de escritorio en oficina, según la caracterización de EP 1.2.

## 7. Justificación técnica de las decisiones

### 7.1 Usabilidad

El flujo de creación de reporte se divide en pasos porque el contexto de uso es en terreno, de pie y con una sola mano disponible. Presentar todos los campos en una vista única exigiría desplazamiento vertical y aumentaría la tasa de abandono. El límite de tres pasos de RNF-01 responde a esa misma restricción.

La barra inferior en móvil sitúa los destinos principales dentro del área alcanzable con el pulgar, criterio relevante dado que una parte del público objetivo utiliza el teléfono al aire libre y en movimiento.

### 7.2 Eficiencia de interacción

La bandeja de triage ordena por índice de riesgo en lugar de por fecha de ingreso, lo que reduce el trabajo de decisión del funcionario. La información relevante para priorizar ya está calculada y ordenada al abrir la vista.

La detección de duplicados se ejecuta antes de confirmar el envío y no después, evitando que el usuario complete el flujo para luego descubrir que su aporte era redundante.

### 7.3 Claridad estructural

La separación de rutas mediante el prefijo `/municipal` permite que la verificación de rol se aplique a una rama completa del árbol de rutas, en lugar de repetirse en cada ruta individual. Esto reduce la posibilidad de que una vista quede desprotegida por omisión.

La correspondencia entre las cuatro secciones de navegación y las cuatro tareas principales del usuario mantiene un modelo mental estable, donde cada sección cumple una función distinta y no se superpone con las demás.

### 7.4 Escalabilidad de la arquitectura frontend

La estructura modular en `pages`, `components`, `routes` y `services` permite incorporar nuevas vistas sin modificar la configuración de rutas existente.

La organización de `pages` en subcarpetas por rol permite incorporar nuevas secciones dentro de un rol existente, o un rol adicional, sin reestructurar los ya implementados.

La lógica de verificación de rol se concentra en un componente de ruta protegida reutilizable, de modo que el criterio de acceso se define en un solo lugar y se aplica por composición.
