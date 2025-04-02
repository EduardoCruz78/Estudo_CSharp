## 1. A Caixa Principal: `EntityBase`

Imagine que você tem muitas caixas para guardar coisas diferentes, como brinquedos, livros e roupas. Todas essas caixas precisam de algumas coisas em comum: um número para identificar cada uma, a data em que foram feitas e quem as fez. A `EntityBase` é a "caixa mãe" que dá essas coisas básicas para todas as outras.

### Código
```csharp
namespace Qualyteam.Core.Domain.Base;

public abstract class EntityBase : IEquatable<EntityBase>
{
    public Guid Id { get; set; }
    public DateTime CreatedAt { get; set; }
    public Guid CreatedBy { get; set; }
    public DateTime? UpdatedAt { get; set; }
    public Guid? UpdatedBy { get; set; }

    public virtual bool Equals(EntityBase? other)
        => other != null && Id == other.Id;
}
```

### Explicação Passo a Passo
1. **`namespace Qualyteam.Core.Domain.Base;`**
   - Isso é como o endereço da rua onde a caixa mora. Ajuda o programa a encontrar e organizar tudo da forma correta.

2. **`public abstract class EntityBase : IEquatable<EntityBase>`**
   - Aqui criamos a caixa principal. Ela é "abstrata", ou seja, não podemos usá-la sozinha, mas outras caixas podem copiá-la. O `IEquatable` é uma regra que ensina a comparar duas caixas para ver se são iguais.

3. **`public Guid Id { get; set; }`**
   - Toda caixa ganha um número especial, chamado `Guid`. É como um código de identificação que nunca se repete, para sabermos qual caixa é qual.

4. **`public DateTime CreatedAt { get; set; }` e `public Guid CreatedBy { get; set; }`**
   - Esses são como carimbos: um diz a data em que a caixa foi feita (ex.: "2 de abril de 2025"), e o outro diz quem a fez (outro código especial).

5. **`public DateTime? UpdatedAt { get; set; }` e `public Guid? UpdatedBy { get; set; }`**
   - Esses carimbos são opcionais (o `?` significa que podem estar vazios). Eles mostram se alguém mudou a caixa depois e quem foi.

6. **`public virtual bool Equals(EntityBase? other)`**
   - Essa parte ensina o programa a olhar o número da caixa (`Id`) para decidir se duas caixas são a mesma coisa.

### Por Que Isso É Útil?
A `EntityBase` é como o modelo de uma caixa perfeita. Qualquer outra caixa que criarmos pode usar essas regras básicas sem precisar escrevê-las de novo.

---

## 2. A Caixa que Não Some: `EntitySoftDeletedBase`

Agora, vamos fazer uma caixa que não some de verdade quando "jogamos fora". Em vez de apagar, só marcamos que ela foi guardada num canto.

### Código
```csharp
namespace Qualyteam.Core.Domain.Base;

public abstract class EntitySoftDeletedBase : EntityBase
{
    public bool IsDeleted { get; set; }
    public DateTime? DeletedAt { get; set; }
    public Guid? DeletedBy { get; set; }
}
```

### Explicação Passo a Passo
1. **`public abstract class EntitySoftDeletedBase : EntityBase`**
   - Essa caixa nova pega tudo da `EntityBase` (número, datas, etc.) e adiciona mais coisas. Ela também é um modelo, não uma caixa pronta.

2. **`public bool IsDeleted { get; set; }`**
   - Isso é como um interruptor: se estiver "ligado" (`true`), a caixa está "guardada". Se estiver "desligado" (`false`), ainda usamos ela.

3. **`public DateTime? DeletedAt { get; set; }` e `public Guid? DeletedBy { get; set; }`**
   - Esses carimbos mostram quando e quem guardou a caixa. Se não foi guardada, ficam vazios.

### Por Que Isso É Bom?
Se você guardar uma caixa por engano, pode pegá-la de volta mudando o interruptor. É mais seguro que jogar fora de verdade!

---

## 3. A Caixa com Dono: `IHasCompanyId`

Imagine que várias pessoas usam o mesmo programa, mas cada uma tem suas próprias caixas. Essa parte ajuda a dizer "essa caixa é de tal pessoa".

### Código
```csharp
namespace Qualyteam.Core.Domain.Base;

public interface IHasCompanyId
{ 
    Guid CompanyId { get; set; }
}
```

### Explicação Passo a Passo
1. **`public interface IHasCompanyId`**
   - Uma "interface" é como uma promessa: qualquer caixa que assinar essa promessa precisa ter um dono.

2. **`Guid CompanyId { get; set; }`**
   - Esse é o número especial do dono da caixa (ex.: uma empresa). Assim, as caixas de uma pessoa não se misturam com as de outra.

### Por Que Isso Ajuda?
É como colocar etiquetas nas suas coisas numa escola. Cada empresa tem suas caixas separadas, mesmo usando o mesmo programa.

---

## 4. Uma Caixa de Verdade: `RiskType`

Agora, vamos criar uma caixa real para guardar tipos de risco, como "Risco de Chuva" ou "Risco de Falta de Energia".

### Código
```csharp
namespace Qualyteam.Risks.Domain.Entities;

public sealed class RiskType : EntitySoftDeletedBase, IHasCompanyId
{
    public const int NameMaxLength = 150;
    public string Name { get; protected set; } = null!;
    public bool IsActive { get; protected set; }
    
    public Guid CompanyId { get; set; }

    private RiskType() { }

    public RiskType(string name)
    {
        Guard.IsNotWhiteSpace(name);
        Guard.IsLessThanOrEqualTo(name.Length, NameMaxLength, nameof(name));
        
        Id = Guid.NewGuid();
        Name = name;
        IsActive = true;
    }
    
    public void UpdateName(string name)
    {
        Guard.IsNotWhiteSpace(name);
        Guard.IsLessThanOrEqualTo(name.Length, NameMaxLength, nameof(name));
        
        Name = name;
    }

    public void Deactivate() => IsActive = false;

    public void Reactivate() => IsActive = true;
}
```

### Explicação Passo a Passo
1. **`namespace Qualyteam.Risks.Domain.Entities;`**
   - O endereço dessa caixa, que fica na área dos riscos.

2. **`public sealed class RiskType : EntitySoftDeletedBase, IHasCompanyId`**
   - Essa é a caixa pronta! Ela pega as regras da `EntitySoftDeletedBase` (número, datas, "guardar") e do `IHasCompanyId` (dono). O `sealed` significa que ninguém pode mudar esse modelo.

3. **`public const int NameMaxLength = 150;`**
   - Um número fixo que diz: "o nome da caixa não pode ser maior que 150 letras".

4. **`public string Name { get; protected set; } = null!;`**
   - O nome da caixa (ex.: "Risco de Chuva"). Só a própria caixa pode mudar isso, por causa do `protected`.

5. **`public bool IsActive { get; protected set; }`**
   - Um interruptor que diz se a caixa está sendo usada (`true`) ou não (`false`).

6. **`public Guid CompanyId { get; set; }`**
   - O número do dono da caixa, como prometido no `IHasCompanyId`.

7. **`private RiskType() { }`**
   - Uma entrada secreta que o programa usa para criar a caixa sem nome (usada por ferramentas especiais).

8. **`public RiskType(string name)`**
   - A entrada principal! Cria a caixa com um nome, checa se ele é válido, dá um número novo (`Id`), define o nome e liga o interruptor (`IsActive = true`).

9. **`public void UpdateName(string name)`**
   - Uma maneira de trocar o nome da caixa, mas só se seguir as regras (não vazio, até 150 letras).

10. **`public void Deactivate()` e `public void Reactivate()`**
    - Dois botões: um desliga a caixa (`IsActive = false`), e o outro liga de novo (`IsActive = true`).

### O Que Essa Caixa Faz?
Ela guarda um tipo de risco com um nome, um dono, e pode ser ligada ou desligada. Se for "guardada" (`IsDeleted = true`), ainda fica no sistema.

---

Entendido! Vou atualizar a seção 5 do README com o código correto do `ClearDataTrialIntegrationEventHandler` do módulo de riscos. Vou manter o mesmo estilo didático e claro, explicando cada parte de forma simples e organizada, como nas seções anteriores. Aqui está a versão revisada:

---

## 5. Limpando as Caixas: `ClearDataTrialIntegrationEventHandler`

Agora, vamos aprender como limpar todas as caixas de uma empresa no módulo de riscos, como se fosse uma grande faxina. Esse código apaga tudo relacionado a uma empresa específica, mas faz isso com cuidado para não deixar bagunça.

### Código
```csharp
namespace Qualyteam.Risks.Application.IntegrationEvents.DataTrials.ClearDataTrial;

public sealed class ClearDataTrialIntegrationEventHandler : IIntegrationEventHandler<ClearDataTrialIntegrationEvent>
{
    private readonly IRisksDbContext _context;
    private readonly IQualyteamLogger _logger;

    public ClearDataTrialIntegrationEventHandler(
        IRisksDbContext context,
        IQualyteamLogger logger
    )
    {
        _context = context;
        _logger = logger;
    }

    public async Task Handle(ClearDataTrialIntegrationEvent @event)
    {
        var companyId = @event.Context.CompanyId;
        await using var transaction = await _context.Database.BeginTransactionAsync();

        try
        {
            await _context.Products 
                .Where(p => p.CompanyId == companyId)
                .ExecuteDeleteAsync();
            
            await _context.RiskReevaluationUserTasks
                .Where(t => t.CompanyId == companyId)
                .ExecuteDeleteAsync();
            
            await _context.Risks
                .Where(r => r.CompanyId == companyId)
                .ExecuteDeleteAsync();

            await _context.RiskTypes
                .Where(rt => rt.CompanyId == companyId)
                .ExecuteDeleteAsync();

            await _context.CustomFields
                .Where(cf => cf.CompanyId == companyId)
                .ExecuteDeleteAsync();

            await _context.CreatedCustomFieldMessageControls
                .Where(cc => cc.CompanyId == companyId)
                .ExecuteDeleteAsync();

            await _context.OrganizationalUnits
                .Where(ou => ou.CompanyId == companyId)
                .ExecuteDeleteAsync();

            await _context.Processes
                .Where(p => p.CompanyId == companyId)
                .ExecuteDeleteAsync();

            await transaction.CommitAsync();
        }
        catch (Exception e)
        {
            _logger.LogError(
                loggingEvent: LoggingEvents.ClearDataTrialRisksError,
                tags: null,
                extras: new Dictionary<string, object> { { "Exception", e.Message } }
            );

            await transaction.RollbackAsync();
        }
    }
}
```

### Explicação Passo a Passo
1. **`namespace Qualyteam.Risks.Application.IntegrationEvents.DataTrials.ClearDataTrial;`**
   - Esse é o endereço da faxina, que fica na área dos riscos. Ele diz ao programa onde encontrar essa ferramenta de limpeza.

2. **`public sealed class ClearDataTrialIntegrationEventHandler : IIntegrationEventHandler<ClearDataTrialIntegrationEvent>`**
   - Aqui criamos a ferramenta de limpeza. Ela é "selada" (`sealed`), ou seja, ninguém pode mudá-la, e segue uma regra (`IIntegrationEventHandler`) para saber como limpar.

3. **`private readonly IRisksDbContext _context;` e `private readonly IQualyteamLogger _logger;`**
   - Essas são as ajudantes da ferramenta: `_context` é como um mapa que mostra onde estão as caixas no banco de dados, e `_logger` é um caderno para anotar se algo der errado. O `readonly` significa que elas não mudam depois de escolhidas.

4. **`public ClearDataTrialIntegrationEventHandler(IRisksDbContext context, IQualyteamLogger logger)`**
   - Essa é a entrada da ferramenta. Quando ela é criada, recebe o mapa (`context`) e o caderno (`logger`), e guarda eles para usar depois.

5. **`public async Task Handle(ClearDataTrialIntegrationEvent @event)`**
   - Esse é o botão que começa a faxina. O `async` significa que ela trabalha com calma, esperando cada passo terminar antes de seguir.

6. **`var companyId = @event.Context.CompanyId;`**
   - Aqui pegamos o número da empresa que queremos limpar. É como escolher qual quarto da casa vamos arrumar.

7. **`await using var transaction = await _context.Database.BeginTransactionAsync();`**
   - Antes de começar, trancamos tudo com uma chave especial chamada "transação". Isso garante que, se algo der errado, podemos desfazer a faxina e deixar tudo como estava.

8. **`await _context.[Nome].Where(...).ExecuteDeleteAsync();`**
   - Essa parte é a vassoura! Ela limpa várias caixas, como:
     - `Products`: Produtos da empresa.
     - `RiskReevaluationUserTasks`: Tarefas de reavaliação de riscos.
     - `Risks`: Riscos em si.
     - `RiskTypes`: Tipos de riscos (como a nossa caixa do item 4).
     - `CustomFields`: Campos personalizados.
     - `CreatedCustomFieldMessageControls`: Controles de mensagens.
     - `OrganizationalUnits`: Unidades da empresa.
     - `Processes`: Processos.
   - O `Where` olha o `CompanyId` para limpar só as caixas dessa empresa.

9. **`await transaction.CommitAsync();`**
   - Se tudo foi limpo direitinho, guardamos a faxina e dizemos "pronto!". Isso confirma que as caixas foram apagadas.

10. **`catch (Exception e)`**
    - Se algo quebrar (como a vassoura falhar), anotamos o problema no caderno (`_logger.LogError`) com detalhes do erro e desfazemos tudo (`RollbackAsync`) para não deixar bagunça.

### Por Que Isso É Importante?
Essa faxina apaga todas as caixas de uma empresa no módulo de riscos de uma vez só. A transação é como um cinto de segurança: se algo der errado, nada se perde, e tudo volta ao normal.


## 6. Criando Exemplos com `RiskTypeMother`

Imagine que você quer testar como a caixa `RiskType` funciona. A classe `RiskTypeMother` é como uma fábrica de caixas prontas, que já vêm com nomes prontos para ser usado de forma automática.

### Código
```csharp
namespace Qualyteam.Risks.Tests.Utils.ObjectMothers;

public static class RiskTypeMother
{
    public const string EconomicName = "Economic";
    public const string HealthAndSafetyName = "Health and Safety";
    public const string EnvironmentalName = "Environmental";
    
    public static RiskType Economic() 
        => Create(name: EconomicName);
    
    public static RiskType HealthAndSafety() 
        => Create(name: HealthAndSafetyName);
    
    public static RiskType Environmental() 
        => Create(name: EnvironmentalName);
    
    public static RiskType Create(string name = "Default") 
        => new(name: name);
    
    public static IEnumerable<RiskType> GetAll()
    {
        yield return Economic();
        yield return HealthAndSafety();
        yield return Environmental();
    }
}
```

### Explicação Passo a Passo
1. **`namespace Qualyteam.Risks.Tests.Utils.ObjectMothers;`**
   - Esse é o endereço da fábrica, que fica na área de testes do módulo de riscos.

2. **`public static class RiskTypeMother`**
   - Aqui criamos a fábrica. Ela é "estática", ou seja, não precisa ser construída para funcionar, é só usar de maneira direta.

3. **`public const string EconomicName = "Economic";` (e outros nomes)**
   - Esses são os nomes prontos que a fábrica pode usar: "Economic" (econômico), "Health and Safety" (saúde e segurança) e "Environmental" (ambiental).

4. **`public static RiskType Economic() => Create(name: EconomicName);` (e outros métodos)**
   - Esses são os botões da fábrica. Cada um faz uma caixa `RiskType` com um nome específico, chamando a função `Create`.

5. **`public static RiskType Create(string name = "Default") => new(name: name);`**
   - Esse é o coração da fábrica! Ele cria uma nova caixa `RiskType` com o nome que você escolher. Se não escolher nada, usa "Default" (padrão).

6. **`public static IEnumerable<RiskType> GetAll()`**
   - Esse botão entrega uma lista com todas as caixas prontas: uma econômica, uma de saúde e segurança, e uma ambiental. O `yield return` é como uma esteira que manda uma caixa de cada vez.

### Como Isso Se Conecta com `RiskType`?
A `RiskTypeMother` é uma ajudante que faz caixas `RiskType` para testes. Ela usa o construtor da `RiskType` (o `new(name: name)`) para criar exemplos com nomes como "Economic". Isso ajuda a testar se a `RiskType` funciona direitinho sem precisar inventar nomes toda hora.

---

## 7. Organizando os Riscos com `RiskEntityTypeConfiguration`

Agora, vamos ver como os riscos (outra caixa chamada `Risk`) se conectam com a `RiskType`. A classe `RiskEntityTypeConfiguration` é como um mapa que diz ao programa onde guardar os riscos e como eles se ligam ao tipo de risco.

### Código
```csharp
namespace Qualyteam.Risks.Infrastructure.Persistence.EntityConfigurations;

public class RiskEntityTypeConfiguration : IEntityTypeConfiguration<Risk>
{
    public void Configure(EntityTypeBuilder<Risk> builder)
    {
        builder.ToTable("Risks");

        builder.ConfigureEntityConventions();

        builder.Property(r => r.CompanyId)
            .ValueGeneratedNever()
            .HasColumnName(nameof(EntityCompanyBase.CompanyId))
            .IsRequired();

        builder.OwnsOne(r => r.Code, code => { /* Configurações do código */ });

        builder.Property(r => r.Identification)
            .HasMaxLength(Risk.IdentificationMaxLength)
            .IsRequired();

        builder.Property(r => r.Origin)
            .IsRequired();

        builder.Property(r => r.RiskTypeId)
            .IsRequired();

        builder.HasOne(r => r.RiskType)
            .WithMany()
            .HasForeignKey(r => r.RiskTypeId)
            .OnDelete(DeleteBehavior.Restrict);

        builder.Property(r => r.ProcessId)
            .IsRequired();

        builder.HasOne(r => r.Process)
            .WithMany()
            .HasForeignKey(r => r.ProcessId)
            .OnDelete(DeleteBehavior.Restrict);

        builder.Property(r => r.OrganizationalUnitConfigurationId)
            .IsRequired();

        builder.HasOne(r => r.OrganizationalUnitConfiguration)
            .WithMany()
            .HasForeignKey(r => r.OrganizationalUnitConfigurationId)
            .OnDelete(DeleteBehavior.Restrict);

        builder.Property(r => r.CurrentRiskManagementCycleId)
            .IsRequired();

        builder.Property(r => r.CurrentCriterionId)
            .IsRequired(false);

        builder.HasOne(r => r.CurrentCriterion)
            .WithMany()
            .HasForeignKey(r => r.CurrentCriterionId)
            .OnDelete(DeleteBehavior.Restrict);

        builder.Property(r => r.LastRiskManagementCycleResult)
            .IsRequired(false);
    }
}
```

### Explicação Passo a Passo
1. **`namespace Qualyteam.Risks.Infrastructure.Persistence.EntityConfigurations;`**
   - O endereço dessa classe, que fica na área de organização do banco de dados.

2. **`public class RiskEntityTypeConfiguration : IEntityTypeConfiguration<Risk>`**
   - Essa é a pessoa que organiza a caixa `Risk`, dizendo como ela deve ser guardada no banco.

3. **`builder.ToTable("Risks");`**
   - Isso diz que os riscos vão para uma gaveta chamada "Risks" no banco.

4. **`builder.Property(r => r.RiskTypeId).IsRequired();`**
   - Cada risco precisa de um número especial chamado `RiskTypeId`. Esse número é o `Id` de uma caixa `RiskType`, como "Economic" ou "Environmental".

5. **`builder.HasOne(r => r.RiskType).WithMany().HasForeignKey(r => r.RiskTypeId).OnDelete(DeleteBehavior.Restrict);`**
   - Aqui está a conexão! Cada risco (`Risk`) tem um tipo (`RiskType`), como se fosse um adesivo dizendo "eu sou um risco econômico". O `Restrict` significa que você não pode apagar um tipo de risco se ele ainda está sendo usado por um risco.

### Como Isso Se Conecta com `RiskType`?
A `RiskType` é como uma etiqueta que dá nome aos riscos. Por exemplo, um risco chamado "Falta de Dinheiro" usa o `RiskTypeId` para apontar para a caixa `RiskType` com nome "Economic". O mapa `RiskEntityTypeConfiguration` garante que todo risco tenha um tipo e que eles fiquem bem ligados no banco.

---

## 8. Ações para os Riscos com `RiskActionEntityTypeConfiguration`

Por fim, temos a caixa `RiskAction`, que guarda as ações para lidar com os riscos. Essa classe organiza como essas ações são guardadas, mas ela não se conecta diretamente com `RiskType` — ela trabalha com os riscos que já têm um tipo.

### Código
```csharp
namespace Qualyteam.Risks.Infrastructure.Persistence.EntityConfigurations;

public sealed class RiskActionEntityTypeConfiguration : IEntityTypeConfiguration<RiskAction>
{
    public void Configure(EntityTypeBuilder<RiskAction> builder)
    {
        builder.ToTable("RiskActions");
        
        builder.ConfigureEntityConventions();

        builder.OwnsOne(ra => ra.Planning, riskActionPlanning => { /* Configurações do planejamento */ });
        
        builder.OwnsOne(ra => ra.Implementation, riskActionImplementation => { /* Configurações da implementação */ });

        builder.Property(ra => ra.RiskManagementCycleId)
            .IsRequired();

        builder.HasOne(ra => ra.RiskManagementCycle)
            .WithMany(rmc => rmc.Actions)
            .HasForeignKey(ra => ra.RiskManagementCycleId)
            .OnDelete(DeleteBehavior.Cascade);

        builder.Property(ra => ra.Status)
            .IsRequired();
        
        builder.Property(ra => ra.EmailStatus)
            .IsRequired();
    }
}
```

### Explicação Passo a Passo
1. **`namespace Qualyteam.Risks.Infrastructure.Persistence.EntityConfigurations;`**
   - O mesmo endereço do mapa dos riscos, agora para as ações.

2. **`public sealed class RiskActionEntityTypeConfiguration : IEntityTypeConfiguration<RiskAction>`**
   - Essa é a organizadora da caixa `RiskAction`, que diz como guardar as ações no banco.

3. **`builder.ToTable("RiskActions");`**
   - As ações vão para uma gaveta chamada "RiskActions".

4. **`builder.HasOne(ra => ra.RiskManagementCycle).WithMany(rmc => rmc.Actions).HasForeignKey(ra => ra.RiskManagementCycleId);`**
   - Cada ação está ligada a um ciclo de gerenciamento de riscos, que por sua vez está ligado a um risco (do item 7). O `Cascade` significa que, se o ciclo for apagado, as ações vão junto.

### Como Isso Se Conecta com `RiskType`?
A conexão aqui é indireta. A `RiskAction` ajuda a resolver um risco (`Risk`), e esse risco tem um tipo (`RiskType`). Por exemplo, se o risco "Falta de Dinheiro" é do tipo "Economic", a ação pode ser "Economizar mais". A `RiskActionEntityTypeConfiguration` organiza as ações, mas depende do risco já ter um `RiskType` definido.

---
