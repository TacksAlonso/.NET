El patrón **Event Sourcing** es una técnica de diseño donde el estado de una aplicación se deriva de una secuencia de eventos. Cada cambio en el estado se representa como un evento y estos eventos se almacenan de manera secuencial. Cuando se necesita reconstruir el estado, los eventos se reproducen en el orden en que ocurrieron.

### Implementación de Event Sourcing en .NET Core

Vamos a implementar un ejemplo básico de Event Sourcing en .NET Core utilizando una aplicación de gestión de cuentas bancarias. En este ejemplo, las operaciones de depósito y retiro se registrarán como eventos, y el estado de la cuenta se reconstruirá a partir de estos eventos.

### 1. Configurar el Proyecto

1. **Crear un Nuevo Proyecto**:

   ```bash
   dotnet new webapi -n BankingService
   cd BankingService
   ```

2. **Agregar Paquetes Necesarios**:

   ```bash
   dotnet add package MediatR
   dotnet add package MediatR.Extensions.Microsoft.DependencyInjection
   dotnet add package Microsoft.EntityFrameworkCore
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

### 2. Definir el Modelo de Evento

Crea una carpeta `Events` y define los eventos `DepositEvent` y `WithdrawalEvent`.

**Events/BankEvent.cs**:

```csharp
namespace BankingService.Events
{
    public abstract class BankEvent
    {
        public DateTime Timestamp { get; private set; } = DateTime.UtcNow;
    }
}
```

**Events/DepositEvent.cs**:

```csharp
namespace BankingService.Events
{
    public class DepositEvent : BankEvent
    {
        public Guid AccountId { get; set; }
        public decimal Amount { get; set; }

        public DepositEvent(Guid accountId, decimal amount)
        {
            AccountId = accountId;
            Amount = amount;
        }
    }
}
```

**Events/WithdrawalEvent.cs**:

```csharp
namespace BankingService.Events
{
    public class WithdrawalEvent : BankEvent
    {
        public Guid AccountId { get; set; }
        public decimal Amount { get; set; }

        public WithdrawalEvent(Guid accountId, decimal amount)
        {
            AccountId = accountId;
            Amount = amount;
        }
    }
}
```

### 3. Definir el Modelo de Agregación

Crea una carpeta `Models` y define el agregado `BankAccount`.

**Models/BankAccount.cs**:

```csharp
using BankingService.Events;
using System;
using System.Collections.Generic;

namespace BankingService.Models
{
    public class BankAccount
    {
        public Guid Id { get; private set; }
        public decimal Balance { get; private set; }
        private readonly List<BankEvent> _changes = new List<BankEvent>();

        public BankAccount(Guid id)
        {
            Id = id;
        }

        public void Deposit(decimal amount)
        {
            if (amount <= 0)
                throw new InvalidOperationException("Deposit amount must be positive.");

            ApplyChange(new DepositEvent(Id, amount));
        }

        public void Withdraw(decimal amount)
        {
            if (amount <= 0)
                throw new InvalidOperationException("Withdrawal amount must be positive.");

            if (Balance < amount)
                throw new InvalidOperationException("Insufficient funds.");

            ApplyChange(new WithdrawalEvent(Id, amount));
        }

        private void ApplyChange(BankEvent @event)
        {
            Mutate(@event);
            _changes.Add(@event);
        }

        public void Mutate(BankEvent @event)
        {
            switch (@event)
            {
                case DepositEvent deposit:
                    Balance += deposit.Amount;
                    break;
                case WithdrawalEvent withdrawal:
                    Balance -= withdrawal.Amount;
                    break;
            }
        }

        public IEnumerable<BankEvent> GetUncommittedChanges()
        {
            return _changes;
        }

        public void LoadsFromHistory(IEnumerable<BankEvent> history)
        {
            foreach (var e in history)
            {
                Mutate(e);
            }
        }
    }
}
```

### 4. Configurar el DbContext

Crea una carpeta `Data` y define el contexto `EventStoreContext`.

**Data/EventStoreContext.cs**:

```csharp
using Microsoft.EntityFrameworkCore;
using BankingService.Events;

namespace BankingService.Data
{
    public class EventStoreContext : DbContext
    {
        public DbSet<BankEvent> Events { get; set; }

        public EventStoreContext(DbContextOptions<EventStoreContext> options) : base(options) { }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<BankEvent>().HasDiscriminator<string>("EventType")
                .HasValue<DepositEvent>("Deposit")
                .HasValue<WithdrawalEvent>("Withdrawal");

            base.OnModelCreating(modelBuilder);
        }
    }
}
```

### 5. Configurar el `Startup.cs`

Configura los servicios de MediatR y Entity Framework Core.

```csharp
using MediatR;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using BankingService.Data;

public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        services.AddDbContext<EventStoreContext>(options =>
            options.UseInMemoryDatabase("EventStore"));

        services.AddMediatR(typeof(Startup).Assembly);
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
}
```

### 6. Crear los Manejadores de Comandos

Crea una carpeta `Handlers`.

**Handlers/DepositCommandHandler.cs**:

```csharp
using MediatR;
using BankingService.Data;
using BankingService.Events;
using BankingService.Models;
using System.Threading;
using System.Threading.Tasks;

namespace BankingService.Handlers
{
    public class DepositCommand : IRequest
    {
        public Guid AccountId { get; set; }
        public decimal Amount { get; set; }

        public DepositCommand(Guid accountId, decimal amount)
        {
            AccountId = accountId;
            Amount = amount;
        }
    }

    public class DepositCommandHandler : IRequestHandler<DepositCommand>
    {
        private readonly EventStoreContext _context;

        public DepositCommandHandler(EventStoreContext context)
        {
            _context = context;
        }

        public async Task<Unit> Handle(DepositCommand request, CancellationToken cancellationToken)
        {
            var account = await _context.FindAsync<BankAccount>(request.AccountId);
            if (account == null)
            {
                account = new BankAccount(request.AccountId);
                _context.Add(account);
            }

            account.Deposit(request.Amount);
            await _context.SaveChangesAsync(cancellationToken);

            return Unit.Value;
        }
    }
}
```

**Handlers/WithdrawCommandHandler.cs**:

```csharp
using MediatR;
using BankingService.Data;
using BankingService.Events;
using BankingService.Models;
using System.Threading;
using System.Threading.Tasks;

namespace BankingService.Handlers
{
    public class WithdrawCommand : IRequest
    {
        public Guid AccountId { get; set; }
        public decimal Amount { get; set; }

        public WithdrawCommand(Guid accountId, decimal amount)
        {
            AccountId = accountId;
            Amount = amount;
        }
    }

    public class WithdrawCommandHandler : IRequestHandler<WithdrawCommand>
    {
        private readonly EventStoreContext _context;

        public WithdrawCommandHandler(EventStoreContext context)
        {
            _context = context;
        }

        public async Task<Unit> Handle(WithdrawCommand request, CancellationToken cancellationToken)
        {
            var account = await _context.FindAsync<BankAccount>(request.AccountId);
            if (account == null)
            {
                account = new BankAccount(request.AccountId);
                _context.Add(account);
            }

            account.Withdraw(request.Amount);
            await _context.SaveChangesAsync(cancellationToken);

            return Unit.Value;
        }
    }
}
```

### 7. Crear el Controlador

Añade un controlador para manejar las solicitudes HTTP.

```csharp
using MediatR;
using Microsoft.AspNetCore.Mvc;
using BankingService.Handlers;
using System;
using System.Threading.Tasks;

namespace BankingService.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class AccountsController : ControllerBase
    {
        private readonly IMediator _mediator;

        public AccountsController(IMediator mediator)
        {
            _mediator = mediator;
        }

        [HttpPost("deposit")]
        public async Task<IActionResult> Deposit([FromBody] DepositCommand command)
        {
            await _mediator.Send(command);
            return Ok();
        }

        [HttpPost("withdraw")]
        public async Task<IActionResult> Withdraw([FromBody] WithdrawCommand command)
        {
            await _mediator.Send(command);
            return Ok();
        }
    }
}
```

### Resumen

En este ejemplo, hemos implementado el patrón de Event Sourcing en un proyecto .NET Core para una aplicación de gestión de cuentas bancarias. Utilizamos MediatR para manejar los comandos de depósito y retiro, y Entity Framework Core para almacenar los eventos en una base de datos en memoria. La cuenta bancaria se reconstruye a partir de la secuencia de eventos, lo que garantiza que el estado de la cuenta siempre sea consistente con los eventos que ocurrieron.