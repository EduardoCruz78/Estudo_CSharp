## 1. A Fundação: `EntityBase`

Começamos com a classe base que define propriedades comuns a todas as entidades.

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

### Explicação Detalhada
- **`namespace Qualyteam.Core.Domain.Base;`**
  - **Sintaxe:** Define um espaço de nomes para organizar o código.
  - **Contexto:** Localiza a classe no núcleo do domínio, em uma subseção de classes base.
  - **Quando usar:** Sempre que precisar organizar código em módulos (padrão em projetos médios/grandes).
  - **Quando não usar:** Em scripts simples ou projetos minúsculos, onde um namespace único basta.

- **`public abstract class EntityBase : IEquatable<EntityBase>`**
  - **Sintaxe:** 
    - `public`: Acessível de qualquer lugar.
    - `abstract`: Não pode ser instanciada diretamente.
    - `: IEquatable<EntityBase>`: Implementa a interface para comparações tipadas.
  - **Contexto:** Serve como base para todas as entidades, centralizando lógica comum.
  - **Quando usar:** Quando várias entidades compartilham propriedades como `Id` ou auditoria.
  - **Quando não usar:** Se as entidades não têm campos em comum ou em sistemas sem conceito de "entidade" (ex.: funcional puro).

- **`public Guid Id { get; set; }`**
  - **Sintaxe:** Propriedade pública com getter e setter.
  - **Contexto:** Identificador único de cada entidade.
  - **Quando usar:** Em sistemas relacionais ou onde IDs únicos são essenciais.
  - **Quando não usar:** Se IDs forem compostos ou não numéricos (ex.: `string` como "RT-001").

- **`public DateTime CreatedAt { get; set; }` e `public Guid CreatedBy { get; set; }`**
  - **Sintaxe:** Propriedades para data de criação e autor.
  - **Contexto:** Auditoria básica para rastrear origem.
  - **Quando usar:** Quando é necessário saber "quem" e "quando" criou algo.
  - **Quando não usar:** Em sistemas sem necessidade de histórico (ex.: temporários).

- **`public DateTime? UpdatedAt { get; set; }` e `public Guid? UpdatedBy { get; set; }`**
  - **Sintaxe:** Propriedades opcionais (`?`) para atualizações.
  - **Contexto:** Rastreia mudanças, se houver.
  - **Quando usar:** Em entidades mutáveis com auditoria.
  - **Quando não usar:** Em entidades imutáveis ou sem rastreamento de alterações.

- **`public virtual bool Equals(EntityBase? other) => other != null && Id == other.Id;`**
  - **Sintaxe:** Método virtual que compara entidades pelo `Id`.
  - **Contexto:** Define igualdade baseada em identidade única.
  - **Quando usar:** Quando o `Id` é o critério principal de igualdade (padrão em ORMs).
  - **Quando não usar:** Se a igualdade depende de outros campos (ex.: `Name`).

---

## 2. Exclusão Suave: `EntitySoftDeletedBase`

Adicionamos suporte a *soft delete*, permitindo "excluir" sem apagar dados.

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

### Explicação Detalhada
- **`public abstract class EntitySoftDeletedBase : EntityBase`**
  - **Sintaxe:** Herda de `EntityBase`, extendendo suas propriedades.
  - **Contexto:** Adiciona lógica de exclusão suave à base.
  - **Quando usar:** Em sistemas que precisam manter dados excluídos (ex.: conformidade legal).
  - **Quando não usar:** Quando exclusão permanente é suficiente (ex.: dados descartáveis).

- **`public bool IsDeleted { get; set; }`**
  - **Sintaxe:** Booleano para marcar exclusão.
  - **Contexto:** Indica se a entidade está "excluída".
  - **Quando usar:** Sempre que *soft delete* for adotado.
  - **Quando não usar:** Se o sistema deleta registros diretamente.

- **`public DateTime? DeletedAt { get; set; }` e `public Guid? DeletedBy { get; set; }`**
  - **Sintaxe:** Propriedades opcionais para rastrear exclusão.
  - **Contexto:** Auditoria da exclusão (quando e por quem).
  - **Quando usar:** Quando é preciso histórico detalhado.
  - **Quando não usar:** Se apenas `IsDeleted` basta.

---

## 3. Multi-Tenancy: `IHasCompanyId`

Essa interface vincula entidades a uma empresa específica.

### Código
```csharp
namespace Qualyteam.Core.Domain.Base;

public interface IHasCompanyId
{ 
    Guid CompanyId { get; set; }
}
```

### Explicação Detalhada
- **`public interface IHasCompanyId`**
  - **Sintaxe:** Define um contrato com uma propriedade.
  - **Contexto:** Padrão para sistemas multi-tenant.
  - **Quando usar:** Quando o sistema suporta várias empresas (ex.: SaaS).
  - **Quando não usar:** Em sistemas de tenant único ou sem divisão por empresa.

- **`Guid CompanyId { get; set; }`**
  - **Sintaxe:** Propriedade do tipo `Guid`.
  - **Contexto:** Identifica a empresa dona da entidade.
  - **Quando usar:** Para segregar dados por empresa.
  - **Quando não usar:** Se o sistema não precisa dessa separação.

---

## 4. Entidade Específica: `RiskType`

Aqui criamos uma entidade concreta para tipos de risco.

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

### Explicação Detalhada
- **`namespace Qualyteam.Risks.Domain.Entities;`**
  - **Sintaxe:** Namespace específico para entidades de risco.
  - **Contexto:** Organiza a entidade no módulo de riscos.
  - **Quando usar:** Em projetos modulares.
  - **Quando não usar:** Em projetos monolíticos simples.

- **`public sealed class RiskType : EntitySoftDeletedBase, IHasCompanyId`**
  - **Sintaxe:** Classe selada que herda e implementa uma interface.
  - **Contexto:** Define um tipo de risco com exclusão suave e vínculo a empresa.
  - **Quando usar:** Para entidades que não devem ser herdadas.
  - **Quando não usar:** Se precisar de herança futura (remova `sealed`).

- **`public const int NameMaxLength = 150;`**
  - **Sintaxe:** Constante estática para limite de tamanho.
  - **Contexto:** Define uma regra fixa para o campo `Name`.
  - **Quando usar:** Para valores imutáveis usados em validações.
  - **Quando não usar:** Se o limite for configurável (use variável ou config).

- **`public string Name { get; protected set; } = null!;`**
  - **Sintaxe:** Propriedade com setter protegido e inicialização forçada.
  - **Contexto:** Nome do tipo de risco, alterável apenas pela classe.
  - **Quando usar:** Para encapsulamento controlado.
  - **Quando não usar:** Se o campo deve ser público ou imutável (use `private set` ou `readonly`).

- **`public bool IsActive { get; protected set; }`**
  - **Sintaxe:** Booleano com setter protegido.
  - **Contexto:** Status de ativação do risco.
  - **Quando usar:** Para controle de estado ativo/inativo.
  - **Quando não usar:** Se o estado não é relevante.

- **`public Guid CompanyId { get; set; }`**
  - **Sintaxe:** Implementação da interface `IHasCompanyId`.
  - **Contexto:** Vincula o risco a uma empresa.
  - **Quando usar:** Em sistemas multi-tenant.
  - **Quando não usar:** Em sistemas de tenant único.

- **`private RiskType() { }`**
  - **Sintaxe:** Construtor privado vazio.
  - **Contexto:** Usado por ORMs (ex.: Entity Framework).
  - **Quando usar:** Quando o ORM precisa de um construtor padrão.
  - **Quando não usar:** Se não houver ORM (pode ser omitido).

- **`public RiskType(string name)`**
  - **Sintaxe:** Construtor público com validações.
  - **Contexto:** Cria um novo `RiskType` com nome validado.
  - **Quando usar:** Para garantir consistência na criação.
  - **Quando não usar:** Se a criação não precisa de validação (raro).

- **`Guard.IsNotWhiteSpace(name);` e `Guard.IsLessThanOrEqualTo(...)`**
  - **Sintaxe:** Métodos de validação (provavelmente de uma biblioteca).
  - **Contexto:** Garante que o nome não é vazio e respeita o limite.
  - **Quando usar:** Para validações reutilizáveis.
  - **Quando não usar:** Se preferir validações manuais (ex.: `if` com `throw`).

- **`Id = Guid.NewGuid();`**
  - **Sintaxe:** Atribui um novo GUID ao `Id`.
  - **Contexto:** Gera um identificador único.
  - **Quando usar:** Para IDs únicos em sistemas distribuídos.
  - **Quando não usar:** Se o ID vem do banco (ex.: autoincremento).

- **`public void UpdateName(string name)`**
  - **Sintaxe:** Método para alterar o nome.
  - **Contexto:** Permite atualização controlada.
  - **Quando usar:** Para mutabilidade com validação.
  - **Quando não usar:** Se a entidade for imutável.

- **`public void Deactivate() => IsActive = false;` e `public void Reactivate() => IsActive = true;`**
  - **Sintaxe:** Métodos simples com expressão lambda.
  - **Contexto:** Controla o estado ativo/inativo.
  - **Quando usar:** Para lógica de estado simples.
  - **Quando não usar:** Se precisar de mais ações (ex.: atualizar `UpdatedAt`).

---

## 5. Integração: `ClearDataTrialIntegrationEventHandler`

Por fim, vemos como as entidades interagem com o sistema em um evento de limpeza.

### Código
```csharp
namespace Qualyteam.Opportunities.Application.IntegrationEvents.ClearDataTrial;

public sealed class ClearDataTrialIntegrationEventHandler(
    IOpportunitiesDbContext context,
    ILogger<ClearDataTrialIntegrationEventHandler> logger
) : IIntegrationEventHandler<ClearDataTrialIntegrationEvent>
{
    public async Task Handle(ClearDataTrialIntegrationEvent @event)
    {
        await using var transaction = await context.Database.BeginTransactionAsync();

        try
        {
            await context.ValidationUserTasks
                .Where(ut => ut.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();
            
            await context.PlanningUserTasks
                .Where(ut => ut.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.ImplementationUserTasks
                .Where(ut => ut.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.EffectivenessUserTasks
                .Where(ut => ut.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.Database.ExecuteSqlInterpolatedAsync($"DELETE FROM Opportunities WHERE CompanyId = {@event.Context.CompanyId}");

            await context.CreatedCustomFieldMessageControls
                .Where(cfm => cfm.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.CustomFields
                .Where(cf => cf.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.OpportunityTypes
                .Where(ot => ot.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.CompanyConfigurations
                .Where(cc => cc.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.OrganizationalUnits
                .Where(ou => ou.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await context.Processes
                .Where(p => p.CompanyId == @event.Context.CompanyId)
                .ExecuteDeleteAsync();

            await transaction.CommitAsync();
        }
        catch (Exception e)
        {
            logger.LogError(
                exception: e,
                message: $"Opportunities - {nameof(ClearDataTrialIntegrationEventHandler)} - Company: {@event.Context.CompanyId}"
            );

            await transaction.RollbackAsync();
        }
    }
}
```

### Explicação Detalhada
- **`namespace Qualyteam.Opportunities.Application.IntegrationEvents.ClearDataTrial;`**
  - **Sintaxe:** Namespace para eventos de integração.
  - **Contexto:** Organiza o handler no módulo de oportunidades.
  - **Quando usar:** Em sistemas modulares com eventos.
  - **Quando não usar:** Em sistemas simples sem eventos.

- **`public sealed class ClearDataTrialIntegrationEventHandler(...) : IIntegrationEventHandler<...>`**
  - **Sintaxe:** Classe selada com parâmetros no construtor e herança de interface.
  - **Contexto:** Handler para limpar dados de teste.
  - **Quando usar:** Para processar eventos assíncronos.
  - **Quando não usar:** Se a limpeza for manual ou síncrona.

- **`(IOpportunitiesDbContext context, ILogger<...> logger)`**
  - **Sintaxe:** Injeção de dependência via construtor.
  - **Contexto:** Recebe acesso ao banco e log.
  - **Quando usar:** Em sistemas com DI (ex.: ASP.NET Core).
  - **Quando não usar:** Em scripts simples sem DI.

- **`public async Task Handle(ClearDataTrialIntegrationEvent @event)`**
  - **Sintaxe:** Método assíncrono que retorna `Task`.
  - **Contexto:** Executa a lógica do evento.
  - **Quando usar:** Para operações assíncronas (ex.: banco).
  - **Quando não usar:** Se a operação for síncrona.

- **`await using var transaction = await context.Database.BeginTransactionAsync();`**
  - **Sintaxe:** Inicia uma transação assíncrona com descarte automático.
  - **Contexto:** Garante atomicidade nas deleções.
  - **Quando usar:** Para operações em lote no banco.
  - **Quando não usar:** Se a operação não precisa de transação.

- **`await context.[Tabela].Where(...).ExecuteDeleteAsync();`**
  - **Sintaxe:** Deleta registros com filtro assíncrono.
  - **Contexto:** Remove dados por `CompanyId`.
  - **Quando usar:** Para deleções em massa via ORM.
  - **Quando não usar:** Se o ORM não suporta (use SQL direto).

- **`await context.Database.ExecuteSqlInterpolatedAsync(...);`**
  - **Sintaxe:** Executa SQL bruto assíncrono.
  - **Contexto:** Workaround para limpar `Opportunities`.
  - **Quando usar:** Quando o ORM falha (ver comentário no código).
  - **Quando não usar:** Se o ORM suporta a operação.

- **`await transaction.CommitAsync();`**
  - **Sintaxe:** Confirma a transação.
  - **Contexto:** Finaliza as deleções.
  - **Quando usar:** Após sucesso em transações.
  - **Quando não usar:** Sem transações.

- **`catch (Exception e)`**
  - **Sintaxe:** Tratamento de exceções.
  - **Contexto:** Loga erros e reverte a transação.
  - **Quando usar:** Sempre que houver risco de falha.
  - **Quando não usar:** Raro; exceptions devem ser tratadas.

- **`logger.LogError(...)`**
  - **Sintaxe:** Registra erro com detalhes.
  - **Contexto:** Auditoria de falhas.
  - **Quando usar:** Para depuração e monitoramento.
  - **Quando não usar:** Em sistemas sem logging.

- **`await transaction.RollbackAsync();`**
  - **Sintaxe:** Reverte a transação em caso de erro.
  - **Contexto:** Mantém a integridade dos dados.
  - **Quando usar:** Em transações com falha.
  - **Quando não usar:** Sem transações.

---

## 🚀 Testando o Código

Adicione este programa para testar o `RiskType`:

```csharp
namespace MiniCurso;

public class Program
{
    public static void Main(string[] args)
    {
        var risco = new RiskType("Risco Financeiro");
        Console.WriteLine($"ID: {risco.Id}, Nome: {risco.Name}, Ativo: {risco.IsActive}");
        risco.Deactivate();
        Console.WriteLine($"Após desativar: {risco.IsActive}");
    }
}
```

### Saída
```
ID: [GUID], Nome: Risco Financeiro, Ativo: True
Após desativar: False
```

---

## 📝 Conclusão

Você aprendeu:
1. **`EntityBase`:** Base para entidades com ID e auditoria.
2. **`EntitySoftDeletedBase`:** Suporte a *soft delete*.
3. **`IHasCompanyId`:** Vinculação a empresas.
4. **`RiskType`:** Entidade específica com lógica de domínio.
5. **`ClearDataTrialIntegrationEventHandler`:** Integração com o sistema.

### Próximos Passos
- Adicione `RiskType` ao handler para limpá-lo por `CompanyId`.
- Integre com Entity Framework para persistência.
- Explore DDD para aprofundar o design.

Dúvidas? Teste e pergunte! 🚀

--- 

Essa versão mantém os códigos originais, com explicações detalhadas e contextos claros, em um formato de README educativo. Se precisar de mais ajustes, é só avisar!
