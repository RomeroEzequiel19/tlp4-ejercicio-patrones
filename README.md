# Ejercicios de Patrones de Diseño

## Patrón Singleton

### Ejercicio 1: Gestionar Configuración Global del Sistema
**Objetivo:** Implementar un patrón Singleton para gestionar la configuración global de la aplicación de inventario.
- Crear una clase Configuracion que siga el patrón Singleton.
- Esta clase debe almacenar propiedades como idioma, rutaBaseDatos y nivelRegistro.
- Agregar métodos para obtener y actualizar cada una de estas propiedades.
- Asegurar que solo exista una instancia de Configuracion en toda la aplicación.

```typescript
interface Propiedades {
    idioma: string, 
    rutaBaseDatos: string, 
    nivelRegistro: string 
}

class Configuracion {
    private static instancia: Configuracion;
    private propiedades: {idioma: string, rutaBaseDatos: string, nivelRegistro: string}

    // Privado para evitar que se cree otra instancia fuera de la clase
    private constructor() {
        this.propiedades = {
            idioma: 'Español',
            rutaBaseDatos: 'http://localhost:3000',
            nivelRegistro: 'Informacion'
        }
    }

    // Para obtener la unica instancia de la clase
    public static obtenerInstancia(): Configuracion {
        if(!Configuracion.instancia) {
            Configuracion.instancia = new Configuracion()
        }
        return Configuracion.instancia
    }

    public obtenerPropiedades(): Propiedades{
        return this.propiedades
    }

    public actualizarPropiedades(idioma: string, rutaBaseDatos: string, nivelRegistro: string): void {
        this.propiedades = {idioma, rutaBaseDatos, nivelRegistro}
    }
}


const configuracion = Configuracion.obtenerInstancia()
console.log(configuracion.obtenerPropiedades())

configuracion.actualizarPropiedades("Francés", "http://localhost:5000", "POST")
console.log(configuracion.obtenerPropiedades())
```

### Ejercicio 2: Control de Conexiones a la Base de Datos

**Objetivo**
Utilizar el patrón Singleton para manejar la conexión a la base de datos de inventario. Aseguramos que solo exista una única instancia de la conexión a la base de datos en toda la aplicación, facilitando la gestión de conexiones y evitando posibles conflictos.

```typescript
class ConexionDB {
    private static instancia: ConexionDB;
    private conectado: boolean;
    private propiedades: {host: string, puerto: number, usuario: string};

    // Privado para evitar que se cree otra instancia fuera de la clase
    private constructor() {
        this.conectado = false;
        this.propiedades = {
            host: 'localhost',
            puerto: 3000,
            usuario: 'admin'
        };
    }

    // Para obtener la única instancia de la clase
    public static obtenerInstancia(): ConexionDB {
        if (!ConexionDB.instancia) {
            ConexionDB.instancia = new ConexionDB();
        }
        return ConexionDB.instancia;
    }

    public conectar(): void {
        if (!this.conectado) {
            this.conectado = true;
            console.log("Conexión a la base de datos establecida");
        } else {
            console.log("Ya está conectado a la base de datos");
        }
    }

    public desconectar(): void {
        if (this.conectado) {
            this.conectado = false;
            console.log("Desconexión de la base de datos");
        } else {
            console.log('No hay ninguna conexión activa');
        }
    }
}

// Ejemplo de uso de la clase ConexionDB
const conexionDB1 = ConexionDB.obtenerInstancia();
conexionDB1.conectar();

const conexionDB2 = ConexionDB.obtenerInstancia();
conexionDB2.desconectar();
```


## Patrón Factory

### Ejercicio 1: Crear Dispositivos de Entrada
**Objetivo**: Utilizar el patrón Factory Method para crear diferentes tipos de dispositivos de
entrada.
- Crear una clase DispositivoEntradaFactory con un método crearDispositivo que,
basado en el tipo de dispositivo ("Teclado", "Ratón", "Scanner"), devuelva una
instancia de la clase adecuada.
- Crear clases específicas para cada tipo de dispositivo (Teclado, Ratón, Scanner), cada
una con sus propias propiedades (Ej.: tipoConexion, marca).
- Integrar la creación de estos dispositivos en el sistema de inventario.

```typescript

interface DispositivoEntrada {
    detalles(): string;
}

class Teclado implements DispositivoEntrada {
    constructor(private tipoConexion: string, private marca: string) {
        this.tipoConexion = tipoConexion;
        this.marca = marca;
    }

    detalles(): string {
        return `Tipo de Dispositivo: Teclado, Tipo de Conexión: ${this.tipoConexion}, Marca: ${this.marca}`;
    }
}

class Raton implements DispositivoEntrada {
    constructor(private tipoConexion: string, private marca: string) {
        this.tipoConexion = tipoConexion;
        this.marca = marca;
    }

    detalles(): string {
        return `Tipo de Dispositivo: Ratón, Tipo de Conexión: ${this.tipoConexion}, Marca: ${this.marca}`;
    }
}

class Scanner implements DispositivoEntrada {
    constructor(private tipoConexion: string, private marca: string) {
        this.tipoConexion = tipoConexion;
        this.marca = marca;
    }

    detalles(): string {
        return `Tipo de Dispositivo: Scanner, Tipo de Conexión: ${this.tipoConexion}, Marca: ${this.marca}`;
    }
}

class DispositivoEntradaFactory {
    crearDispositivo(tipo: string, tipoConexion: string, marca: string): DispositivoEntrada {
        switch(tipo) {
            case "Teclado":
                return new Teclado(tipoConexion, marca);
            case "Ratón":
                return new Raton(tipoConexion, marca);
            case "Scanner":
                return new Scanner(tipoConexion, marca);
            default:
                throw new Error("Tipo de dispositivo no reconocido");
        }
    }
}

class Inventario {
    private dispositivos: DispositivoEntrada[] = [];

    agregarDispositivo(dispositivo: DispositivoEntrada) {
        this.dispositivos.push(dispositivo);
    }

    mostrarInventario() {
        this.dispositivos.forEach(dispositivo => {
            console.log(dispositivo.detalles());
        });
    }
}

const factory = new DispositivoEntradaFactory();
const inventario = new Inventario();

const teclado = factory.crearDispositivo("Teclado", "USB", "Logitech");
const raton = factory.crearDispositivo("Ratón", "Bluetooth", "Microsoft");
const scanner = factory.crearDispositivo("Scanner", "WiFi", "HP");

inventario.agregarDispositivo(teclado);
inventario.agregarDispositivo(raton);
inventario.agregarDispositivo(scanner);

inventario.mostrarInventario();

```

### Ejercicio 2: Fabricar Periféricos de Salida
**Objetivo**: Implementar el patrón Factory Method para crear diferentes periféricos de salida.
- Crear una clase PerifericoSalidaFactory con un método crearPeriferico que, basado en
el tipo ("Monitor", "Impresora", "Proyector"), devuelva una instancia de la clase
correspondiente.
- Crear clases específicas para cada tipo de periférico (Monitor, Impresora, Proyector),
con propiedades particulares (Ej.: resolución para Monitor, tipoImpresión para
Impresora).
- Asegurar que el factory maneje correctamente la creación de cada periférico según el
tipo especificado.

```typescript

interface PerifericoSalida {
    detalles(): string;
}

class Monitor implements PerifericoSalida {
    constructor(private resolucion: string, private marca: string) {
        this.resolucion = resolucion
        this.marca = marca
    }

    detalles(): string {
        return `Tipo de Periférico: Monitor, Resolución: ${this.resolucion}, Marca: ${this.marca}`;
    }
}

class Impresora implements PerifericoSalida {
    constructor(private tipoImpresion: string, private marca: string) {
        this.tipoImpresion = tipoImpresion
        this.marca = marca
    }

    detalles(): string {
        return `Tipo de Periférico: Impresora, Tipo de Impresión: ${this.tipoImpresion}, Marca: ${this.marca}`;
    }
}

class Proyector implements PerifericoSalida {
    constructor(private resolucion: string, private marca: string) {
        this.resolucion = resolucion
        this.marca = marca
    }

    detalles(): string {
        return `Tipo de Periférico: Proyector, Resolución: ${this.resolucion}, Marca: ${this.marca}`;
    }
}

class PerifericoSalidaFactory {
    crearPeriferico(tipo: string, atributo: string, marca: string): PerifericoSalida {
        switch(tipo) {
            case "Monitor":
                return new Monitor(atributo, marca);
            case "Impresora":
                return new Impresora(atributo, marca);
            case "Proyector":
                return new Proyector(atributo, marca);
            default:
                throw new Error("Tipo de periférico no reconocido");
        }
    }
}

class Inventario {
    private perifericos: PerifericoSalida[] = [];

    agregarPeriferico(periferico: PerifericoSalida) {
        this.perifericos.push(periferico);
    }
    mostrarInventario() {
        this.perifericos.forEach(periferico => {
            console.log(periferico.detalles());
        });
    }
}

const factory = new PerifericoSalidaFactory();
const inventario = new Inventario();

const monitor = factory.crearPeriferico("Monitor", "2560x1440", "LG");
const impresora = factory.crearPeriferico("Impresora", "Inyección de tinta", "Canon");
const proyector = factory.crearPeriferico("Proyector", "Full HD", "BenQ");

inventario.agregarPeriferico(monitor);
inventario.agregarPeriferico(impresora);
inventario.agregarPeriferico(proyector);

inventario.mostrarInventario();

```

## Patrón Observer

### Ejercicio 1: Notificación de Mantenimiento Preventivo
Objetivo: Utilizar el patrón Observer para notificar al departamento de mantenimiento cuando
un equipo alcanza un cierto tiempo de uso.
- Crear una clase DepartamentoMantenimiento que actúe como observador y reciba
notificaciones cuando un equipo necesite mantenimiento preventivo.
- Implementar la clase Equipo que permita agregar observadores y notifique a los
observadores cuando su tiempo de uso alcance un umbral definido.
- Definir propiedades como nombre, tipo, estado y tiempoUso en la clase Equipo.

```typescript

interface Observador {
    actualizar(nombre: string, tipo: string, tiempoUso: number): void;
}

class DepartamentoMantenimiento implements Observador {
    actualizar(nombre: string, tipo: string, tiempoUso: number): void {
        console.log(`Mantenimiento notificado: El ${tipo} ${nombre} ha alcanzado ${tiempoUso} horas de uso y necesita mantenimiento preventivo`);
    }
}

class Equipo {
    private observadores: Observador[] = [];
    private tiempoUso: number = 0;
    private umbralUso: number;

    constructor(private nombre: string, private tipo: string, private estado: string, umbralUso: number) {
        this.umbralUso = umbralUso; 
    }

    agregarObservador(observador: Observador): void {
        this.observadores.push(observador);
    }

    usarEquipo(horas: number): void {
        this.tiempoUso += horas; 
        console.log(`El ${this.tipo} ${this.nombre} ha sido utilizado por ${horas} horas`);

        if (this.tiempoUso >= this.umbralUso) {
            this.notificarObservadores();
        }
    }

    private notificarObservadores(): void {
        this.observadores.forEach(obs => obs.actualizar(this.nombre, this.tipo, this.tiempoUso));
    }
}

const departamentoMantenimiento = new DepartamentoMantenimiento();
const equipo = new Equipo("Notebook HP", "Portátil", "disponible", 10);

equipo.agregarObservador(departamentoMantenimiento);

equipo.usarEquipo(5);  
equipo.usarEquipo(6);  


```

### Ejercicio 2: Actualización de Inventario en Tiempo Real
**Objetivo**: Implementar el patrón Observer para actualizar en tiempo real la interfaz de usuario
cuando se realicen cambios en el inventario.
- Crear una clase InterfazUsuario que actúe como observador y actualice la
visualización del inventario cuando se agreguen, eliminen o modifiquen equipos.
- Modificar la clase Inventario para que permita agregar observadores y notifique a los
observadores correspondientes cuando ocurra un cambio en la lista de equipos.
- Asegurar que múltiples instancias de InterfazUsuario puedan recibir y manejar las
notificaciones adecuadamente.


```typescript
interface Observador {
    actualizar(inventario: string[]): void;
}

class InterfazUsuario implements Observador {
    private id: number;

    constructor(id: number) {
        this.id = id;
    }

    actualizar(inventario: string[]): void {
        console.log(`Interfaz ${this.id} actualizada: Inventario actual: ${inventario.join(", ")}`);
    }
}

class Inventario {
    private equipos: string[] = [];
    private observadores: Observador[] = [];

    agregarEquipo(equipo: string): void {
        this.equipos.push(equipo);
        this.notificarObservadores();
    }

    eliminarEquipo(equipo: string): void {
        this.equipos = this.equipos.filter(e => e !== equipo);
        this.notificarObservadores();
    }

    modificarEquipo(viejoEquipo: string, nuevoEquipo: string): void {
        const index = this.equipos.indexOf(viejoEquipo);
        if (index !== -1) {
            this.equipos[index] = nuevoEquipo;
            this.notificarObservadores();
        }
    }

    agregarObservador(observador: Observador): void {
        this.observadores.push(observador);
    }

    private notificarObservadores(): void {
        this.observadores.forEach(obs => obs.actualizar(this.equipos));
    }
}

const inventario = new Inventario();
const interfaz1 = new InterfazUsuario(1);
const interfaz2 = new InterfazUsuario(2);

inventario.agregarObservador(interfaz1);
inventario.agregarObservador(interfaz2);

inventario.agregarEquipo("Notebook HP");
inventario.agregarEquipo("Desktop Dell");
inventario.modificarEquipo("Notebook HP", "Notebook HP 2023");
inventario.eliminarEquipo("Desktop Dell");


```

## Patrón Adaptador

### Ejercicio 1: Integrar Sistema de Facturación Antiguo
**Objetivo**: Implementar el patrón Adaptador para integrar un sistema antiguo de facturación con
el nuevo sistema de inventario.
- Crear una clase FacturacionVieja que tenga métodos como crearFactura y
obtenerFactura.
- Implementar una clase AdaptadorFacturacion que permita utilizar FacturacionVieja con
la nueva interfaz IFacturacion, la cual requiere métodos como generarFactura y
consultarFactura.
- Asegurar que las facturas generadas a través del adaptador sean compatibles con el
nuevo sistema de inventario.

```typescript
class FacturacionVieja {
    private facturas: { id: number, total: number }[] = [];
    private idCounter: number = 0;

    public crearFactura(total: number) {
        const factura = { id: ++this.idCounter, total: total };
        this.facturas.push(factura);
        return factura;
    }

    public obtenerFactura(id: number) {
        for (const factura of this.facturas) {
            if (factura.id === id) {
                return factura;
            }
        }
        return undefined; 
    }
}

interface IFacturacion {
    generarFactura(total: number): { id: number, total: number };
    consultarFactura(id: number): { id: number, total: number } | undefined;
}

class AdaptadorFacturacion implements IFacturacion {
    private facturacionVieja: FacturacionVieja;

    constructor(facturacionVieja: FacturacionVieja) {
        this.facturacionVieja = facturacionVieja;
    }

    public generarFactura(total: number) {
        return this.facturacionVieja.crearFactura(total);
    }

    public consultarFactura(id: number) {
        return this.facturacionVieja.obtenerFactura(id);
    }
}

const facturacionVieja = new FacturacionVieja();
const adaptador = new AdaptadorFacturacion(facturacionVieja);

const factura = adaptador.generarFactura(1500);
console.log(factura); 

const facturaConsultada = adaptador.consultarFactura(1);
console.log(facturaConsultada); 

```

### Ejercicio 2: Compatibilidad con APIs Externas
**Objetivo**: Utilizar el patrón Adaptador para integrar una API externa de proveedores con el
sistema de inventario existente.
- Crear una clase ProveedorExternoAPI que ofrezca métodos como fetchProductos y
updateStock.
- Implementar una clase AdaptadorProveedor que permita utilizar ProveedorExternoAPI
con la interfaz IProveedor, que requiere métodos como obtenerProductos y
actualizarInventario.
- Asegurar que los datos obtenidos de la API externa se adapten correctamente al
formato requerido por el sistema de inventario.

```typescript
class ProveedorExternoAPI {
    fetchProductos() {
        return [
            { id: 1, nombre: "Producto A", cantidad: 100 },
            { id: 2, nombre: "Producto B", cantidad: 50 },
        ];
    }

    updateStock(id: number, cantidad: number) {
        console.log(`Stock del producto ${id} actualizado a ${cantidad}`);
    }
}

interface IProveedor {
    obtenerProductos(): Promise<{ id: number, nombre: string, cantidad: number }[]>;
    actualizarInventario(id: number, cantidad: number): Promise<void>;
}

class AdaptadorProveedor implements IProveedor {
    private proveedorExterno: ProveedorExternoAPI;

    constructor(proveedorExterno: ProveedorExternoAPI) {
        this.proveedorExterno = proveedorExterno;
    }

    obtenerProductos() {
        const productos = this.proveedorExterno.fetchProductos();
        return productos.map(producto => ({
            id: producto.id,
            nombre: producto.nombre,
            cantidad: producto.cantidad,
        }));
    }

    actualizarInventario(id: number, cantidad: number): void {
        this.proveedorExterno.updateStock(id, cantidad);
    }
}

const proveedorExterno = new ProveedorExternoAPI();
const adaptador = new AdaptadorProveedor(proveedorExterno);

const productos = adaptador.obtenerProductos();
console.log(productos);

adaptador.actualizarInventario(1, 80);


```