# Práctica 02: Simulador de Estacionamiento

**Universidad Autónoma de Baja California**  
**Facultad de Ingeniería, Arquitectura y Diseño**  
**Materia:** 40032 – Paradigmas de la Programación  
**Docente:** M.I. José Carlos Gallegos Mariscal  
**Grupo:** 941  
**Alumno:** [Nombre Apellido]  
**Matrícula:** [Número de matrícula]

---

## 1. Introducción

El presente reporte documenta el desarrollo de la Práctica 02: Simulador de Estacionamiento, cuyo objetivo es diseñar e implementar un sistema funcional aplicando el paradigma de Programación Orientada a Objetos (POO) en Python.

El problema consiste en administrar un estacionamiento que gestiona lugares (*spots*), vehículos y tickets de entrada/salida. El sistema permite registrar entradas, registrar salidas con cálculo de cobro, y consultar el estado de ocupación en todo momento.

El desarrollo se realizó en tres sesiones iterativas:

- **Sesión 1:** Modelo orientado a objetos y prototipo funcional en consola.
- **Sesión 2:** Polimorfismo, subtipos y extensiones controladas.
- **Sesión 3:** Interfaz web con Flask bajo arquitectura MVC.

### Objetivos

- Modelar entidades del dominio como clases con atributos y métodos.
- Aplicar encapsulación, abstracción, composición, herencia y polimorfismo.
- Integrar el modelo en una interfaz web con Flask (MVC).

---

## 2. Modelo del Dominio

### 2.1 Diagrama UML

```
+----------------+        +----------------+        +----------------+
|   ParkingLot   |------->|  ParkingSpot   |<-------|    Ticket      |
+----------------+        +----------------+        +----------------+
| -spots         |        | -spot_id: str  |        | -ticket_id     |
| -active_tickets|        | -allowed: Spot |        | -vehicle       |
| -policy        |        | -occupied: bool|        | -spot          |
| -total_revenue |        +----------------+        | -entry_time    |
+----------------+        | +is_available()|        | -exit_time     |
| +enter()       |        | +park()        |        | -status        |
| +exit()        |        | +release()     |        +----------------+
| +get_occupancy()|       +----------------+        | +close()       |
+----------------+                                  | +get_duration()|
                                                    +----------------+
        ^                                                   |
        |                                                   v
+----------------+                              +--------------------+
|    Vehicle     |                              |    RatePolicy      |
+----------------+                              | <<interface>>      |
| -plate: str    |                              +--------------------+
| -type          |                              | +calculate(h, v)   |
+----------------+                              +--------------------+
       / \                                             / \
      /   \                                           /   \
+-------+ +------------+               +---------------+ +--------------+
|  Car  | | Motorcycle |               |HourlyRatePolicy| |FlatRatePolicy|
+-------+ +------------+               +---------------+ +--------------+
```

### 2.2 Clases y Responsabilidades

| Clase | Responsabilidad |
|---|---|
| `Vehicle` | Abstracción base de un vehículo: placa y tipo. |
| `Car` | Subtipo de vehículo de tipo automóvil. |
| `Motorcycle` | Subtipo de vehículo de tipo motocicleta. |
| `ParkingSpot` | Representa un lugar físico; controla si está libre u ocupado y qué tipo de vehículo acepta. |
| `Ticket` | Registra la estadía de un vehículo: hora de entrada, salida, estado y duración. |
| `ParkingLot` | Orquesta toda la lógica: asigna spots, crea tickets, procesa salidas y acumula ingresos. |
| `RatePolicy` | Interfaz (Protocol) que define el contrato de cálculo de cobro. |
| `HourlyRatePolicy` | Implementación de cobro por hora, con tarifas distintas para Car y Motorcycle. |
| `FlatRatePolicy` | Implementación de cobro de tarifa fija independiente del tiempo. |

---

## 3. Evidencia de Conceptos POO

### 3.1 Encapsulación

Los atributos internos de las clases están protegidos con prefijo `_` y solo se modifican mediante métodos que validan invariantes. Por ejemplo, `ParkingSpot` impide que se ocupe un lugar ya ocupado:

```python
# models/spot.py
from dataclasses import dataclass, field
from models.vehicle import Vehicle, VehicleType, SpotType

@dataclass
class ParkingSpot:
    _spot_id: str
    _allowed: SpotType
    _occupied: bool = field(default=False, init=False)
    _current_vehicle: Vehicle | None = field(default=None, init=False)

    def is_available_for(self, vehicle: Vehicle) -> bool:
        if self._occupied:
            return False
        return (
            self._allowed == SpotType.ANY
            or self._allowed.name == vehicle.get_type().name
        )

    def park(self, vehicle: Vehicle) -> None:
        if self._occupied:
            raise ValueError(f"Spot {self._spot_id} ya está ocupado.")
        self._occupied = True
        self._current_vehicle = vehicle

    def release(self) -> None:
        self._occupied = False
        self._current_vehicle = None
```

La validación en `park()` garantiza la invariante: **dos vehículos nunca pueden ocupar el mismo spot**.

### 3.2 Abstracción

La lógica de cobro está separada del resto del sistema mediante la interfaz `RatePolicy`. El `ParkingLot` no sabe cómo se calcula la tarifa, solo llama a `calculate()`:

```python
# models/rates.py
from typing import Protocol
from models.vehicle import Vehicle, VehicleType

class RatePolicy(Protocol):
    def calculate(self, hours: float, vehicle: Vehicle) -> float:
        ...

class HourlyRatePolicy:
    def __init__(self, car_rate: float = 20.0, moto_rate: float = 10.0):
        self._car_rate = car_rate
        self._moto_rate = moto_rate

    def calculate(self, hours: float, vehicle: Vehicle) -> float:
        rate = self._car_rate if vehicle.get_type() == VehicleType.CAR else self._moto_rate
        return round(hours * rate, 2)

class FlatRatePolicy:
    def __init__(self, flat_amount: float = 50.0):
        self._flat_amount = flat_amount

    def calculate(self, hours: float, vehicle: Vehicle) -> float:
        return self._flat_amount
```

### 3.3 Composición

`ParkingLot` administra internamente su colección de `ParkingSpot` y el diccionario de tickets activos. Ningún componente externo manipula estas estructuras directamente:

```python
# models/parking_lot.py
from datetime import datetime
from models.spot import ParkingSpot
from models.ticket import Ticket, TicketStatus
from models.vehicle import Vehicle
from models.rates import RatePolicy

class ParkingLot:
    def __init__(self, spots: list[ParkingSpot], policy: RatePolicy):
        self._spots = spots                        # composición con ParkingSpot
        self._active_tickets: dict[int, Ticket] = {}
        self._policy = policy                      # inyección de dependencia
        self._next_ticket_id = 1
        self._total_revenue = 0.0

    def _find_available_spot(self, vehicle: Vehicle) -> ParkingSpot | None:
        return next(
            (s for s in self._spots if s.is_available_for(vehicle)), None
        )

    def enter(self, vehicle: Vehicle, now: datetime) -> Ticket:
        spot = self._find_available_spot(vehicle)
        if spot is None:
            raise RuntimeError("No hay lugares disponibles compatibles.")
        spot.park(vehicle)
        ticket = Ticket(self._next_ticket_id, vehicle, spot, now)
        self._active_tickets[self._next_ticket_id] = ticket
        self._next_ticket_id += 1
        return ticket

    def exit(self, ticket_id: int, now: datetime) -> float:
        ticket = self._active_tickets.get(ticket_id)
        if ticket is None or ticket.status != TicketStatus.ACTIVE:
            raise ValueError("Ticket no encontrado o ya cerrado.")
        ticket.close(now)
        hours = ticket.get_duration_hours()
        cost = self._policy.calculate(hours, ticket.vehicle)
        ticket.spot.release()
        self._total_revenue += cost
        del self._active_tickets[ticket_id]
        return cost
```

### 3.4 Herencia y Subtipos

`Car` y `Motorcycle` heredan de `Vehicle`, heredando sus atributos y sobreescribiendo `get_type()` para devolver el tipo correcto:

```python
# models/vehicle.py
from enum import Enum, auto
from dataclasses import dataclass

class VehicleType(Enum):
    CAR = auto()
    MOTORCYCLE = auto()

class SpotType(Enum):
    CAR = auto()
    MOTORCYCLE = auto()
    ANY = auto()

@dataclass
class Vehicle:
    _plate: str
    _type: VehicleType

    def get_plate(self) -> str:
        return self._plate

    def get_type(self) -> VehicleType:
        return self._type

class Car(Vehicle):
    def __init__(self, plate: str):
        super().__init__(plate, VehicleType.CAR)

class Motorcycle(Vehicle):
    def __init__(self, plate: str):
        super().__init__(plate, VehicleType.MOTORCYCLE)
```

### 3.5 Polimorfismo

El polimorfismo se aplica en la política de cobro. `ParkingLot.exit()` llama a `self._policy.calculate()` sin importar qué implementación concreta se haya inyectado. Esto permite cambiar el comportamiento del sistema en tiempo de ejecución:

```python
# Caso 1: cobro por hora
hourly_policy = HourlyRatePolicy(car_rate=20.0, moto_rate=10.0)
lot = ParkingLot(spots=spots, policy=hourly_policy)
# Un auto que estuvo 3 horas paga: 3 * 20 = $60

# Caso 2: tarifa plana (mismo código, distinto resultado)
flat_policy = FlatRatePolicy(flat_amount=50.0)
lot = ParkingLot(spots=spots, policy=flat_policy)
# El mismo auto paga siempre $50, sin importar el tiempo
```

La interfaz común `RatePolicy` permite extender el sistema con nuevas políticas (nocturna, fin de semana, etc.) sin modificar `ParkingLot`.

---

## 4. MVC con Flask

### 4.1 Separación de responsabilidades

| Capa | Qué contiene |
|---|---|
| **Model** | Todo el directorio `models/`: `Vehicle`, `Car`, `Motorcycle`, `ParkingSpot`, `Ticket`, `ParkingLot`, `RatePolicy` y sus implementaciones. Contiene toda la lógica de negocio. |
| **View** | Directorio `templates/`: `base.html`, `dashboard.html`, `entry.html`, `exit.html`. Solo presentan datos, no contienen lógica. |
| **Controller** | `app.py`: define las rutas Flask, recibe peticiones, llama al modelo y renderiza templates. |

### 4.2 Rutas implementadas

```python
# app.py (fragmento)
from flask import Flask, render_template, request, redirect, url_for
from datetime import datetime
from models.parking_lot import ParkingLot
from models.vehicle import Car, Motorcycle
from models.spot import ParkingSpot, SpotType
from models.rates import HourlyRatePolicy

app = Flask(__name__)

# Estado en memoria (instancia global)
policy = HourlyRatePolicy(car_rate=20.0, moto_rate=10.0)
spots = [ParkingSpot(f"A{i}", SpotType.CAR) for i in range(1, 6)] + \
        [ParkingSpot(f"M{i}", SpotType.MOTORCYCLE) for i in range(1, 4)]
lot = ParkingLot(spots=spots, policy=policy)

@app.route("/")
def dashboard():
    occupancy = lot.get_occupancy()
    tickets = lot.get_active_tickets()
    return render_template("dashboard.html", occupancy=occupancy, tickets=tickets)

@app.route("/entry", methods=["GET", "POST"])
def entry():
    if request.method == "POST":
        plate = request.form.get("plate", "").strip().upper()
        vtype = request.form.get("type", "car")
        vehicle = Car(plate) if vtype == "car" else Motorcycle(plate)
        try:
            ticket = lot.enter(vehicle, datetime.now())
            return redirect(url_for("dashboard"))
        except RuntimeError as e:
            return render_template("entry.html", error=str(e))
    return render_template("entry.html")

@app.route("/exit", methods=["GET", "POST"])
def exit_vehicle():
    if request.method == "POST":
        ticket_id = request.form.get("ticket_id", "")
        try:
            cost = lot.exit(int(ticket_id), datetime.now())
            return render_template("exit.html", cost=cost, ticket_id=ticket_id)
        except (ValueError, KeyError) as e:
            return render_template("exit.html", error=str(e))
    return render_template("exit.html")
```

### 4.3 Capturas de pantalla

> **Dashboard** – muestra ocupación actual y lista de tickets activos.

*(Captura: dashboard con 2 vehículos registrados, libres=8, ocupados=2)*

> **Registrar Entrada** – formulario con placas y tipo de vehículo.

*(Captura: formulario de entrada con campos "Placas" y "Tipo")*

> **Registrar Salida** – formulario con ID de ticket; muestra el costo calculado.

*(Captura: pantalla de salida mostrando ticket #1, tiempo=2h, costo=$40)*

---

## 5. Pruebas Manuales

### Flujo 1: Entrada y salida de un automóvil (HourlyRatePolicy)

| Paso | Acción | Resultado esperado | Resultado obtenido |
|---|---|---|---|
| 1 | Registrar entrada: placas=ABC-123, tipo=Car | Ticket #1, spot=A1 asignado | ✅ Ticket #1, spot=A1 |
| 2 | Ver ocupación | libres=9, ocupados=1 | ✅ libres=9, ocupados=1 |
| 3 | Registrar salida: ticket=1, tiempo=2h | costo=$40.00, spot A1 liberado | ✅ costo=$40.00 |
| 4 | Ver ocupación | libres=10, ocupados=0 | ✅ libres=10, ocupados=0 |

### Flujo 2: Polimorfismo de tarifa – HourlyRatePolicy vs FlatRatePolicy

| Caso | Vehículo | Horas | Política | Costo esperado | Costo obtenido |
|---|---|---|---|---|---|
| A | Car ABC-123 | 3h | HourlyRatePolicy ($20/h) | $60.00 | ✅ $60.00 |
| B | Motorcycle XYZ-777 | 3h | HourlyRatePolicy ($10/h) | $30.00 | ✅ $30.00 |
| C | Car ABC-123 | 3h | FlatRatePolicy ($50) | $50.00 | ✅ $50.00 |

El caso C demuestra que cambiar la política inyectada en `ParkingLot` modifica el resultado sin alterar ninguna otra clase.

### Flujo 3: Estacionamiento lleno

| Paso | Acción | Resultado esperado | Resultado obtenido |
|---|---|---|---|
| 1 | Llenar todos los spots de tipo CAR (5/5) | Spots ocupados | ✅ |
| 2 | Intentar registrar un sexto automóvil | Error: "No hay lugares disponibles" | ✅ Mensaje de error mostrado |

---

## 6. Conclusiones

- La **encapsulación** resultó fundamental para proteger invariantes del sistema, como garantizar que ningún spot quede doblemente ocupado. Al centralizar las validaciones dentro de los métodos de cada clase, el código externo no puede dejar el sistema en un estado inconsistente.

- La **abstracción** con `RatePolicy` como interfaz (Protocol) demostró su valor al permitir intercambiar políticas de cobro sin tocar `ParkingLot`. Esto refleja el principio abierto/cerrado: el sistema está abierto a extensión y cerrado a modificación.

- La **composición** facilitó un diseño claro de responsabilidades. `ParkingLot` delega la gestión de disponibilidad a `ParkingSpot` y el cálculo de costo a `RatePolicy`, en lugar de concentrar toda la lógica en una sola clase.

- El **polimorfismo** con múltiples implementaciones de `RatePolicy` hizo evidente la ventaja de trabajar con interfaces: el mismo código de `exit()` produce resultados distintos según la política activa, sin ninguna bifurcación `if/else` explícita en el controlador.

- La arquitectura **MVC con Flask** permitió reutilizar el modelo desarrollado en sesiones 1 y 2 sin reescribir lógica. Las rutas en `app.py` actúan únicamente como adaptadores entre la interfaz HTTP y el dominio.

---

## 7. Referencias

Pallets Projects. (2026). *Welcome to Flask — Flask Documentation (3.1.x)*. https://flask.palletsprojects.com/

Python Software Foundation. (2026). *dataclasses — Data Classes*. https://docs.python.org/3/library/dataclasses.html

Fowler, M. (2004). *Inversion of Control Containers and the Dependency Injection pattern*. https://martinfowler.com/articles/injection.html

Python Typing Team. (2026). *Protocols — typing specification*. https://typing.python.org/en/latest/spec/protocol.html

Pallets Projects. (2026). *Quickstart — Flask Documentation (3.1.x)*. https://flask.palletsprojects.com/en/stable/quickstart/

Flask-es Read the Docs. (2026). *Plantillas — Documentación de Flask (Tutorial)*. https://flask-es.readthedocs.io/tutorial/templates/
