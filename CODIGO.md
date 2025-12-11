Program.cs
```
using ProyectoAutobuses.Pool;
using ProyectoAutobuses.Composite;
using ProyectoAutobuses.Decorator;
using ProyectoAutobuses.State;

class Program
{
    static void Main(string[] args)
    {
        // ---------------------------
        // 1. POOL PATTERN
        // ---------------------------
        BusPool pool = new BusPool();

        Console.Write("Ingresa una hora (formato HH:mm): ");
        string hora = Console.ReadLine();

        pool.UpdateBusStates(hora);
        pool.DisplayAll();

        // ---------------------------
        // 2. COMPOSITE PATTERN
        // ---------------------------
        Console.WriteLine("\n--- RUTA TIJUANA - ENSENADA ---");

        BaseRoute ruta = new BaseRoute("Tijuana - Ensenada", 100);

        Console.WriteLine("Agrega paradas (1 a 3). Escribe: 1 2 3 o solo 1 etc:");
        Console.WriteLine("1) La Misión (25 km)");
        Console.WriteLine("2) Rosarito (15 km)");
        Console.WriteLine("3) Chapultepec (30 km)");

        string input = Console.ReadLine();
        string[] stops = input.Split(' ');

        foreach (string s in stops)
        {
            if (s == "1") ruta.Add(new Stop("La Misión", 25));
            if (s == "2") ruta.Add(new Stop("Rosarito", 15));
            if (s == "3") ruta.Add(new Stop("Chapultepec", 30));
        }

        Console.WriteLine($"Distancia total: {ruta.GetDistance()} km\n");

        // ---------------------------
        // 3. DECORATOR + STATE PATTERN
        // ---------------------------
        Console.WriteLine("\n--- COMPRA DE BOLETOS ---");

        Console.WriteLine("Selecciona Autobús:");
        Console.WriteLine("1) Autobús Tijuana-Ensenada (6:00pm)");
        Console.WriteLine("2) Autobús Tijuana-Ensenada (6:30pm)");
        Console.WriteLine("3) Autobús Tijuana-Ensenada (7:00pm)");

        int opcionBus = int.Parse(Console.ReadLine());
        Bus autobús = pool.GetBus(opcionBus);

        ITicket ticket = new BasicTicket();

        Console.WriteLine("\nOpciones extra:");
        Console.WriteLine("1) WiFi (+20)");
        Console.WriteLine("2) Snack (+30)");
        Console.WriteLine("3) Asiento reclinable (+40)");
        Console.WriteLine("Ingresa números separados por espacio:");

        string deco = Console.ReadLine();
        foreach (string d in deco.Split(' '))
        {
            if (d == "1") ticket = new WifiDecorator(ticket);
            if (d == "2") ticket = new SnackDecorator(ticket);
            if (d == "3") ticket = new LuxurySeatDecorator(ticket);
        }

        Console.WriteLine($"\nCosto total del boleto: {ticket.GetCost()}");
        Console.Write("Ingresa dinero: ");
        double dinero = double.Parse(Console.ReadLine());

        PurchaseContext ctx = new PurchaseContext(ticket.GetCost(), autobús);
        ctx.InsertMoney(dinero);

        Console.WriteLine(ctx.StateMessage);

        Console.WriteLine($"Asientos restantes: {autobús.Seats}");
    }
}

```

POOL PATTERN
Bus.cs
```
namespace ProyectoAutobuses.Pool
{
    public enum BusState { Disponible, EnTransito, EnDestino }

    public class Bus
    {
        public string Name { get; set; }
        public string Departure { get; set; }
        public int TravelHours { get; set; }
        public int Seats { get; set; }
        public BusState State { get; set; }

        public Bus(string name, string departure)
        {
            Name = name;
            Departure = departure;
            TravelHours = 2;
            Seats = 22;
            State = BusState.Disponible;
        }

        public void Update(string horaActual)
        {
            TimeSpan horaBus = TimeSpan.Parse(Departure);
            TimeSpan horaAct = TimeSpan.Parse(horaActual);

            if (horaAct < horaBus)
                State = BusState.Disponible;
            else if (horaAct >= horaBus && horaAct < horaBus.Add(TimeSpan.FromHours(TravelHours)))
                State = BusState.EnTransito;
            else
                State = BusState.EnDestino;
        }
    }
}
```

BusPool.cs
```
namespace ProyectoAutobuses.Pool
{
    public class BusPool
    {
        private List<Bus> buses = new List<Bus>();

        public BusPool()
        {
            buses.Add(new Bus("Autobús 1", "18:00"));
            buses.Add(new Bus("Autobús 2", "18:30"));
            buses.Add(new Bus("Autobús 3", "19:00"));
        }

        public void UpdateBusStates(string hora)
        {
            foreach (var bus in buses)
                bus.Update(hora);
        }

        public Bus GetBus(int opcion)
        {
            return buses[opcion - 1];
        }

        public void DisplayAll()
        {
            foreach (var b in buses)
            {
                Console.WriteLine($"{b.Name} | Salida: {b.Departure} | Estado: {b.State}");
            }
        }
    }
}

```

COMPOSITE PATTERN
IRouteComponent.cs
```
namespace ProyectoAutobuses.Composite
{
    public interface IRouteComponent
    {
        string Name { get; }
        double GetDistance();
    }
}

```

BaseRoute.cs
```
namespace ProyectoAutobuses.Composite
{
    public class BaseRoute : IRouteComponent
    {
        public string Name { get; }
        public double Distance { get; }

        private List<IRouteComponent> _components = new List<IRouteComponent>();

        public BaseRoute(string name, double distance)
        {
            Name = name;
            Distance = distance;
        }

        public void Add(IRouteComponent component)
        {
            _components.Add(component);
        }

        public double GetDistance()
        {
            double total = Distance;

            foreach (var c in _components)
                total += c.GetDistance();

            return total;
        }
    }
}

```
Stop.cs
```
namespace ProyectoAutobuses.Composite
{
    public class Stop : IRouteComponent
    {
        public string Name { get; }
        public double Distance { get; }

        public Stop(string name, double distance)
        {
            Name = name;
            Distance = distance;
        }

        public double GetDistance() => Distance;
    }
}

```
BasicTicket.cs
```
namespace ProyectoAutobuses.Decorator
{
    public class BasicTicket : ITicket
    {
        public double GetCost() => 200;
        public string GetDescription() => "Boleto básico";
    }
}

```
