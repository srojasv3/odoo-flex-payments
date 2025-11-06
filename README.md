# Horeca - Niveles, Plazos y Pagos

## Descripción

Módulo de Odoo 16 para gestión avanzada de plazos de pago basados en niveles de usuario. Este módulo permite definir diferentes niveles de clientes y aplicar automáticamente cuotas iniciales y términos de pago personalizados según el nivel asignado.

## Características Principales

### 1. Niveles de Usuario
- Define tres niveles de usuario: Nivel 1, Nivel 2 y Nivel 3
- Cada nivel tiene configuraciones de pago diferenciadas
- Los niveles se asignan directamente a los contactos/clientes

### 2. Plazos de Pago Inteligentes
- Asocia plazos de pago específicos a cada nivel de usuario
- Cálculo automático de cuotas iniciales según el nivel:
  - **Nivel 1**: 60% de cuota inicial
  - **Nivel 2**: 50% de cuota inicial
  - **Nivel 3**: 40% de cuota inicial
- El resto del monto se distribuye según los términos de pago configurados

### 3. Seguimiento de Estados de Pago
- Vista SQL personalizada que rastrea cambios en estados de pago
- Gráfico visual de evolución de estados de pago
- Estados monitoreados:
  - No pagadas
  - En proceso de pago
  - Pagado
  - Pagado parcialmente
  - Revertido
  - Indefinido

## Instalación

### Requisitos Previos
- Odoo 16
- Módulos dependientes:
  - `sale_management`
  - `account_accountant`
  - `account`
  - `mail`
  - `base`

### Pasos de Instalación

1. Copie el módulo `odoo-flex-payments` en el directorio de addons de Odoo:
   ```bash
   cp -r odoo-flex-payments /ruta/a/odoo/addons/
   ```

2. Actualice la lista de aplicaciones en Odoo:
   - Modo desarrollador: Configuración → Actualizar lista de aplicaciones

3. Busque "Horeca - Niveles, Plazos y Pagos" en Aplicaciones e instale el módulo

4. Los datos iniciales (3 niveles de usuario) se cargarán automáticamente

## Configuración

### 1. Configurar Niveles de Usuario

Los niveles se crean automáticamente durante la instalación, pero puede personalizarlos:

- Navegue a: **Contactos → Configuración → Niveles de Usuario**
- Defina o edite los niveles según sus necesidades de negocio

### 2. Asignar Niveles a Contactos

Para asignar un nivel a un cliente:

1. Vaya a **Contactos**
2. Seleccione o cree un contacto
3. En la pestaña del contacto, encontrará el campo **"Nivel de Usuario"**
4. Seleccione el nivel apropiado (Nivel 1, 2 o 3)

### 3. Configurar Plazos de Pago

Para asociar plazos de pago con niveles:

1. Vaya a **Contabilidad → Configuración → Plazos de Pago**
2. Cree o edite un plazo de pago
3. En el campo **"Niveles Permitidos"**, seleccione los niveles de usuario que pueden usar este plazo
4. Configure las condiciones de pago (porcentajes, días, etc.)

## Uso

### Creación de Facturas con Plazos Personalizados

Cuando cree una factura para un cliente con nivel asignado:

1. El sistema detectará automáticamente el nivel del cliente
2. Aplicará la cuota inicial correspondiente:
   - Nivel 1: 60% al momento de la factura
   - Nivel 2: 50% al momento de la factura
   - Nivel 3: 40% al momento de la factura
3. El monto restante se distribuirá según el plazo de pago configurado

### Ejemplo Práctico

**Cliente con Nivel 2 - Factura de $1,000**

- Cuota inicial (50%): $500 - Fecha: Fecha de factura
- Plazo configurado al 30 días: $500 - Fecha: +30 días

**Resultado**: El cliente paga $500 inmediatamente y $500 en 30 días

### Visualización de Estados de Pago

Para ver el gráfico de evolución de estados de pago:

1. Vaya a **Contabilidad → Reportes → Estados de Pago**
2. Visualice el gráfico con la distribución de estados a lo largo del tiempo
3. Use los filtros para analizar períodos específicos

## Estructura del Módulo

```
odoo-flex-payments/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   ├── horeca_user_level.py          # Modelo de niveles de usuario
│   ├── res_partner.py                 # Extensión de contactos
│   ├── account_payment_term.py        # Lógica de plazos de pago
│   └── payment_status_tracking.py     # Vista SQL de seguimiento
├── views/
│   ├── res_partner_view.xml           # Vista de contactos
│   ├── account_payment_term.xml       # Vista de plazos de pago
│   └── graph.xml                      # Vista de gráficos
├── data/
│   └── horeca_user_levels.xml         # Datos iniciales de niveles
├── security/
│   └── ir.model.access.csv            # Permisos de acceso
└── README.md
```

## Modelos

### horeca.user.level
Modelo para definir niveles de usuario.

**Campos:**
- `name`: Nombre del nivel (Char)
- `code`: Código del nivel (Selection: nivel_1, nivel_2, nivel_3)

### res.partner (Heredado)
Extiende el modelo de contactos.

**Campos añadidos:**
- `user_level_id`: Relación Many2one con horeca.user.level

### account.payment.term (Heredado)
Extiende el modelo de plazos de pago con lógica personalizada.

**Campos añadidos:**
- `applicable_user_levels`: Relación Many2many con horeca.user.level

**Métodos sobrescritos:**
- `_compute_terms()`: Calcula la distribución de pagos con cuota inicial según nivel

### payment.status.tracking
Vista SQL para seguimiento de cambios en estados de pago.

**Campos:**
- `fecha`: Fecha del cambio
- `estado_pago`: Estado del pago
- `cantidad`: Cantidad de cambios


## Versión

**Versión actual:** 2.5