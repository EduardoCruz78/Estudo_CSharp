# 🚀 Guia Completo de Sintaxes e Conceitos em C#

## 📋 Índice

1. [Fundamentos da Linguagem](#fundamentos-da-linguagem)
2. [Tipos de Dados](#tipos-de-dados)
3. [Operadores e Expressões](#operadores-e-expressões)
4. [Estruturas de Controle](#estruturas-de-controle)
5. [Manipulação de Strings](#manipulação-de-strings)
6. [Coleções e Estruturas de Dados](#coleções-e-estruturas-de-dados)
7. [LINQ e Consultas](#linq-e-consultas)
8. [Programação Orientada a Objetos](#programação-orientada-a-objetos)
9. [Métodos Especiais](#métodos-especiais)
10. [Sintaxes Avançadas](#sintaxes-avançadas)
11. [Recursos Avançados do .NET](#recursos-avançados-do-net)

# 🔍 Fundamentos da Linguagem C#

## Estrutura Básica de um Programa

Todo programa em C# tem uma estrutura básica que funciona como o esqueleto de um projeto. É como a planta de uma casa: define onde tudo começa e como as coisas se organizam. Aqui está um exemplo simples:

```csharp
namespace VendasProdutos;

public class Program
{
    public static void Main(string[] args) 
    {
        Console.WriteLine("Bem-vindo ao Sistema de Vendas!");
    }
}
```

Vamos desmontar esse código pedaço por pedaço pra entender o que cada parte faz.

### Dissecando a Estrutura

1. **`namespace VendasProdutos;`**
   - **O que é?** Um `namespace` é como uma pasta que organiza seu código. Ele ajuda a evitar confusão se você tiver várias partes no projeto com nomes parecidos.
   - **Pra que serve?** Imagine que você tem dois arquivos chamados `Program`. O `namespace` diz "esse `Program` é do sistema de vendas, não do estoque". No exemplo, `VendasProdutos` é o nome da "pasta".
   - **Detalhe:** O ponto e vírgula `;` no final é como um "pronto, acabou essa linha".

2. **`public class Program`**
   - **O que é?** Uma `class` é como uma caixinha que guarda pedaços de código relacionados. Aqui, criamos uma chamada `Program`.
   - **Pra que serve?** É o lugar onde colocamos as instruções do nosso programa. O `public` significa que essa caixinha pode ser vista e usada por outras partes do código (se precisar).
   - **Curiosidade:** O nome `Program` é comum, mas poderia ser qualquer coisa, tipo `MinhaApp`.

3. **`{ }` (chaves)**
   - **O que é?** As chaves `{` e `}` marcam o começo e o fim de um bloco de código. Tudo que tá dentro delas pertence à `class Program`.
   - **Pra que serve?** É como dizer "o que tá aqui dentro é meu". Elas organizam o código e evitam bagunça.

4. **`public static void Main(string[] args)`**
   - **O que é?** Essa é a função principal, o "botão de start" do programa. Todo programa em C# precisa de um `Main` pra começar a rodar.
   - **Pra que serve?** Quando você executa o programa, o computador procura o `Main` e começa a seguir as instruções que estão dentro dele.
   - **Dissecando os termos:**
     - `public`: Outras partes do código podem achar esse `Main`.
     - `static`: Significa que o `Main` pertence à classe `Program`, não a um objeto específico (não precisa "criar" um `Program` pra usar).
     - `void`: Diz que essa função não devolve nada (só faz algo, não entrega um resultado).
     - `string[] args`: É uma lista de textos que o programa pode receber quando é iniciado (como argumentos na linha de comando). Nesse exemplo, não usamos, mas tá aí pra opções futuras.

5. **`Console.WriteLine("Bem-vindo ao Sistema de Vendas!");`**
   - **O que é?** Um comando que escreve algo na tela.
   - **Pra que serve?** É como o programa dizer "oi" pra você. O `Console` é a janela preta onde o texto aparece, e `WriteLine` escreve uma linha (com quebra no final).
   - **Exemplo:** Quando você roda esse código, vai ver "Bem-vindo ao Sistema de Vendas!" no console.

### Como isso funciona na prática?
Quando você executa esse programa:
1. O computador acha o `namespace VendasProdutos`.
2. Dentro dele, localiza a classe `Program`.
3. Procura o método `Main` (o ponto de entrada).
4. Executa o que tá dentro do `Main`, que neste caso só escreve uma mensagem.

É o "Olá, mundo!" básico do C#. A partir daí, você pode adicionar mais código dentro do `Main` pra fazer coisas legais, como cálculos ou interações.

### Analogia pra fixar
Pensa no `namespace` como o nome da sua rua, a `class Program` como sua casa, e o `Main` como a porta de entrada. O `Console.WriteLine` é você gritando da janela pra quem passa na rua. Simples, mas é o começo de tudo!

## Resumo
Essa estrutura é o mínimo que um programa C# precisa pra rodar. O `namespace` organiza, a `class` guarda o código, e o `Main` é onde a ação começa. Quer testar? Copie esse código, rode num compilador C# (como o Visual Studio), e veja a mensagem aparecer!


## 📊 Tipos de Dados

### Tipos Primitivos
| Tipo     | Descrição                          | Exemplo de Uso                     |
|----------|------------------------------------|------------------------------------|
| `int`    | Números inteiros (32 bits)         | `int quantidade = 10;`             |
| `long`   | Números inteiros (64 bits)         | `long codigoGrande = 9223372036854775807L;` |
| `double` | Ponto flutuante de precisão dupla  | `double preco = 19.99;`            |
| `float`  | Ponto flutuante de precisão simples| `float desconto = 0.15f;`          |
| `decimal`| Precisão decimal para valores financeiros | `decimal valorTotal = 1234.56m;` |
| `bool`   | Valores lógicos                    | `bool estaAtivo = true;`           |
| `char`   | Caractere único                    | `char inicial = 'A';`              |


# 🧩 Operadores e Expressões em C#

## Operadores Especiais

C# possui alguns operadores bem úteis. Eles ajudam a lidar com valores nulos (quando algo não existe ou não foi definido) de um jeito simples e elegante. Vamos explorar dois deles: o `??` (null-coalescing) e o `?.` (null-conditional).

### Null-Coalescing `??`

#### Exemplo:
```csharp
string nome = nomeOriginal ?? "Padrão";
```

#### O que é?
O `??` é como um plano B. Ele diz: "Se o valor da esquerda for nulo, usa o valor da direita". No exemplo, se `nomeOriginal` for `null`, a variável `nome` vai receber `"Padrão"`. Se `nomeOriginal` tiver algo (tipo `"João"`), ele usa esse valor mesmo.

#### Como funciona?
- Pensa assim: você tá esperando um presente (`nomeOriginal`). Se o presente não chegar (`null`), você pega um reserva que já deixou pronto (`"Padrão"`).
- O resultado é sempre o primeiro valor não nulo que ele encontra, da esquerda pra direita.

#### Quando usar?
- **Definir um valor padrão:** Perfeito pra quando você quer garantir que uma variável nunca fique nula. Tipo, se o usuário não digitou um nome, você coloca "Visitante" automaticamente.
- Exemplo prático:
  ```csharp
  string usuario = entradaDoUsuario ?? "Visitante";
  ```
  Se `entradaDoUsuario` for nulo, `usuario` vira `"Visitante"`.

#### Quando não usar?
- Se você precisa de uma verificação mais complicada, como "se for nulo, faz isso, mas se não for, faz aquilo dependendo de outra coisa". Nesse caso, um `if` é mais claro:
  ```csharp
  string nome;
  if (nomeOriginal == null)
  {
      nome = "Padrão";
  }
  else
  {
      nome = nomeOriginal.ToUpper();
  }
  ```
  Aqui o `??` não daria conta, porque a lógica é mais elaborada.

---

### Null-Conditional `?.`

#### Exemplo:
```csharp
int? tamanho = objeto?.PropiedadeNula?.Tamanho;
```

#### O que é?
O `?.` é como um detetive cauteloso. Ele checa se algo existe antes de tentar usar. Se qualquer parte da "corrente" for nula, ele para tudo e devolve `null`, sem quebrar o programa com erros.

#### Como funciona?
- No exemplo, imagine que `objeto` é uma caixa. O `?.` pergunta: "A caixa tá vazia (`null`)?". Se sim, não tenta abrir e já devolve `null`.
- Se `objeto` existe, ele vai pra próxima parte: `PropiedadeNula`. "Essa propriedade é nula?" Se for, para e devolve `null`.
- Só chega em `.Tamanho` se tudo antes existir. O `int?` (com o `?`) significa que `tamanho` pode ser um número ou `null`.

#### Quando usar?
- **Evitar NullReferenceException:** Usa pra navegar por objetos sem medo de um erro chato aparecer. Tipo, você não sabe se o objeto ou suas propriedades existem, mas quer tentar acessar mesmo assim.
- Exemplo prático:
  ```csharp
  string nome = pessoa?.Endereco?.Rua;
  ```
  Se `pessoa` ou `Endereco` for nulo, `nome` vira `null` sem drama.

#### Quando não usar?
- Se o fato de algo ser nulo muda muito o que o programa deve fazer (lógica de negócio), o `?.` pode esconder isso. Nesses casos, um `if` explícito é melhor:
  ```csharp
  if (objeto != null && objeto.PropiedadeNula != null)
  {
      tamanho = objeto.PropiedadeNula.Tamanho;
  }
  else
  {
      Console.WriteLine("Algo deu errado!");
  }
  ```
  Aqui, você quer reagir ao `null`, não só ignorar.

---

### Analogia pra fixar
- **`??`**: É como ter um plano B, caso o plano a não de certo (`null`), você pega um valor reserva.
- **`?.`**: É como explorar uma caverna com uma lanterna. Se o caminho está bloqueado (`null`), você para e volta, sem cair no buraco.

## Resumo
- **`??`**: Dá um valor padrão quando algo é nulo. Simples e direto.
- **`?.`**: Protege contra erros ao acessar coisas que podem não existir. Seguro e esperto.

## O que é uma Expressão Lambda `=>`?

Uma expressão lambda é como um atalho esperto no código: ela permite escrever instruções curtas e rápidas sem precisar criar uma função inteira com nome. É marcada pelo sinal `=>`, que separa o "o que você quer olhar" (os parâmetros) do "o que fazer com isso" (a regra). Vamos ver um exemplo prático:

```csharp
var produtosCaros = produtos.Where(p => p.Preco > 100);
```

Aqui, `p => p.Preco > 100` é a expressão lambda. Ela diz: "Pegue cada produto `p` da lista `produtos` e veja se o preço dele é maior que 100". O resultado? Uma nova lista só com os produtos caros.

### Dissecando a Expressão Lambda
- **`p`**: É um apelido temporário pra cada item da lista `produtos`. Pense como se você estivesse tirando um produto de uma caixa pra olhar. Pode ser qualquer nome, mas `p` é curto e comum.
- **`=>`**: O "faça isso". Divide o apelido (`p`) da regra que vem depois.
- **`p.Preco > 100`**: A regra. Diz pro computador: "Checa se o preço desse produto `p` é maior que 100". O `.Preco` é uma propriedade do objeto, como uma etiqueta com o valor.

O `Where` é um filtro que usa essa lambda pra decidir quais itens da lista `produtos` entram na nova lista `produtosCaros`.

## Quando Usar?

Expressões lambda são perfeitas pra tarefas simples e rápidas. Aqui estão os principais usos:

- **Funções Anônimas**: Quando você precisa de uma função curtinha sem nome. Em vez de criar algo separado, escreve tudo ali no `=>`.
- **Filtros em LINQ**: Pra filtrar listas em C# (como o `Where`), ordenar (`OrderBy`) ou transformar dados. Exemplo: "Quero só os produtos acima de 100 reais".
- **Delegates Simples**: Pra passar instruções curtas pra outros métodos, como um mensageiro levando uma mensagem rápida.

Exemplos práticos:
```csharp
var baratos = produtos.Where(p => p.Preco < 50); // Filtra produtos baratos
var ordenados = produtos.OrderBy(p => p.Nome);   // Ordena por nome
```

## Quando Não Usar?

Se a lógica fica complicada, com muitas condições ou passos, a lambda pode virar um emaranhado difícil de ler. Por exemplo:

```csharp
var confusos = produtos.Where(p => p.Preco > 100 && p.Estoque < 10 || p.Nome.Contains("X"));
```

Nesse caso, é melhor criar uma função normal pra manter o código claro:

```csharp
bool FiltrarProduto(Produto p)
{
    return p.Preco > 100 && p.Estoque < 10;
}
var produtosFiltrados = produtos.Where(FiltrarProduto);
```

## Resumo
Pense na lambda como um pedido rápido no drive-thru: "Me dá só o que custa mais de 10 reais". É prático e direto. Mas se o pedido vira uma receita complexa, melhor chamar um chef (ou seja, escrever uma função completa).


## 🔤 Manipulação de Strings
String são variáveis que armazenam textos, e para manipular essas variáveis existem alguns métodos:

### Métodos de String
```csharp
string texto = "  Produto Exemplo  ";
string nome = "Notebook";

Console.WriteLine(texto.Trim());              // Remove os espaços desnecessários antes e depois do texto
Console.WriteLine(texto.Replace(" ", "_"));   // Substitui um caractere por outro, nesse caso vai substituir os espaços por underlines _
Console.WriteLine(nome.Contains("Note"));     // Verifica se um certo texto está presente dentro do texto da variável e Retorna verdadeiro ou falso
Console.WriteLine(nome.ToUpper());            // Torna todas as letras Maiúsculas, de forma semelhante a um caps lock
Console.WriteLine(nome.ToLower());            // Torna todas as letrasMinúsculas
Console.WriteLine(nome.Length);               // Retorna a quantidade de caracteres da String, incluindo espaços, virgulas e etc. nesse caso: 8 caracteres, já que o texto contem 8 letras
Console.WriteLine(nome.StartsWith("N"));      // Retorna Verdadeiro ou faslo se começar com o texto especificado, nesse caso N
```


# Guia Básico de C#

## Interpolação de Strings

### O que é?
Interpolação de strings é um jeito moderno e prático de misturar variáveis com texto em C#. Em vez de juntar pedaços com `+` ou usar métodos complicados, você coloca tudo direto na string usando um `$` e `{}`.

#### Exemplo:
```csharp
string mensagem = $"Produto: {nome}, Preço: {preco:C2}";
```

#### Como funciona?
- O `$` na frente da string diz pro C#: "Ei, vou misturar coisas aqui dentro".
- As chaves `{}` são como janelinhas onde você coloca variáveis ou expressões. No exemplo:
  - `{nome}`: Substitui pelo valor da variável `nome` (ex.: "Camiseta").
  - `{preco:C2}`: Mostra o valor de `preco` como moeda (`C`) com 2 casas decimais (`2`). Se `preco` for `29.9`, vira "R$ 29,90" (dependendo da configuração local).
- Resultado? Se `nome = "Camiseta"` e `preco = 29.9`, a `mensagem` fica: `"Produto: Camiseta, Preço: R$ 29,90"`.

#### Pra que serve?
- Deixa o código mais limpo e fácil de ler. Compare:
  ```csharp
  // Sem interpolação
  string antiga = "Produto: " + nome + ", Preço: " + preco.ToString("C2");
  // Com interpolação
  string nova = $"Produto: {nome}, Preço: {preco:C2}";
  ```
- Você pode até fazer cálculos dentro das chaves, tipo: `$"Total: {quantidade * preco}"`.

#### Analogia
É como escrever uma carta com espaços pra preencher: "Oi, [nome], sua conta é [valor]". A interpolação preenche os espaços automaticamente.

---

## 🔀 Estruturas de Controle

Estruturas de controle são como as regras de um jogo: elas decidem o que acontece e quando. Vamos ver dois tipos: **condicionais** (decisões) e **laços de repetição** (repetições).

### Condicionais

#### Exemplo:
```csharp
if (preco > 100) 
{
    Console.WriteLine("Produto caro");
} 
else 
{
    Console.WriteLine("Produto barato");
}
```

#### O que é?
O `if` é uma pergunta: "Isso é verdade?". Se for, ele faz o que tá dentro das chaves `{}`. Se não, pula pro `else` (se tiver).

#### Como funciona?
- `preco > 100`: Checa se o valor de `preco` é maior que 100.
- Se sim (verdadeiro), escreve "Produto caro".
- Se não (falso), escreve "Produto barato".
- Exemplo: Se `preco = 150`, aparece "Produto caro". Se `preco = 50`, aparece "Produto barato".

#### Pra que serve?
- Tomar decisões no código. Tipo: "Se tá chovendo, pega o guarda-chuva; se não, usa óculos de sol".

---

### Laços de Repetição

Laços são como um robô que repete tarefas pra você. C# tem vários tipos, cada um com seu jeitinho. Vamos ver os principais:

#### For Tradicional
```csharp
for (int i = 0; i < produtos.Count; i++) 
{
    Console.WriteLine(produtos[i]);
}
```
- **O que é?** Um contador que repete uma ação um número específico de vezes.
- **Como funciona?**
  - `int i = 0`: Começa com `i` em 0 (o contador).
  - `i < produtos.Count`: Repete enquanto `i` for menor que o tamanho da lista `produtos`.
  - `i++`: Aumenta `i` em 1 a cada volta.
  - Dentro das chaves: Mostra cada item da lista `produtos` (ex.: `produtos[0]`, `produtos[1]`, etc.).
- **Pra que serve?** Perfeito quando você sabe quantas vezes quer repetir e precisa do índice (posição).

#### Foreach
```csharp
foreach (var produto in produtos) 
{
    Console.WriteLine(produto.Nome);
}
```
- **O que é?** Um jeito simples de pegar cada item de uma lista sem se preocupar com índices.
- **Como funciona?**
  - `var produto`: Dá um apelido temporário pra cada item de `produtos`.
  - `in produtos`: Diz de onde vêm os itens.
  - Dentro das chaves: Usa `produto` direto (ex.: `produto.Nome` mostra o nome de cada um).
- **Pra que serve?** Ideal pra listas quando você só quer os itens, não as posições.

#### While
```csharp
while (estoque > 0) 
{
    VenderProduto();
    estoque--;
}
```
- **O que é?** Repete enquanto uma condição for verdadeira.
- **Como funciona?**
  - `estoque > 0`: Checa se o estoque é maior que 0.
  - Se sim, vende um produto e diminui o estoque (`estoque--`).
  - Para quando `estoque` chega a 0.
- **Pra que serve?** Bom quando você não sabe quantas vezes vai repetir, só quer continuar enquanto algo é verdade.

#### Do-While
```csharp
do 
{
    ProcessarPedido();
} while (filaProcessamento.Count > 0);
```
- **O que é?** Igual o `while`, mas garante que o código roda pelo menos uma vez.
- **Como funciona?**
  - Primeiro faz o que tá nas chaves (processa um pedido).
  - Depois checa `filaProcessamento.Count > 0`. Se for verdade, repete.
  - Para quando a fila fica vazia.
- **Pra que serve?** Útil quando você precisa executar algo antes de testar a condição.

#### Analogia pra laços
- **For**: Como contar as páginas de um livro, uma por uma.
- **Foreach**: Como ler os títulos de uma estante sem contar as posições.
- **While**: Como comer pipoca enquanto tem no balde.
- **Do-While**: Como abrir a geladeira pra pegar algo, aí decidir se continua.

## Resumo
- **Interpolação de Strings**: Mistura texto e variáveis com `$` e `{}`, fácil e elegante.
- **Condicionais**: Decide o que fazer com `if` e `else`.
- **Laços**: Repete ações com `for`, `foreach`, `while` e `do-while`, cada um pro seu momento.

Teste esses exemplos num código C# pra ver na prática! Se precisar de mais detalhes, é só pedir!
```

Esse README tá bem explicado, com exemplos práticos e analogias pra fixar os conceitos. Ele é perfeito pra quem tá começando ou quer um guia rápido. Se quiser ajustar ou adicionar algo, é só avisar!

## 📦 Coleções e Estruturas de Dados

### Listas
```csharp
List<Produto> produtos = new List<Produto>();
produtos.Add(novoProduto);
produtos.Count;  // Quantidade
produtos.Sort(); // Ordenar
produtos.IndexOf(produto); // Encontrar índice
```

### Arrays e Operações Avançadas
```csharp
// Declaração de array
int[] numeros = { 1, 2, 3, 4, 5 };

// Acessando último elemento
int ultimoElemento = numeros[^1];

// Obtendo subarray
int[] subArray = numeros[1..4];
```

## 🔍 LINQ e Consultas

### Consultas LINQ
```csharp
// Sintaxe de método
var produtosCaros = produtos
    .Where(p => p.Preco > 100)
    .OrderByDescending(p => p.Preco);

// Sintaxe de consulta
var resultado = from p in produtos
                where p.Preco > 100
                orderby p.Preco descending
                select p;
```

## 🆕 Sintaxes Avançadas

### Checked e Overflow
```csharp
// Verifica estouro de capacidade de tipo
try 
{
    checked 
    {
        int maxInt = int.MaxValue;
        int resultado = maxInt + 1;  // Lançará uma exceção
    }
}
catch (OverflowException ex)
{
    Console.WriteLine("Estouro de capacidade detectado");
}
```

### DateOnly (C# 10+)
```csharp
DateOnly dataEntrega = new DateOnly(2023, 12, 25);
Console.WriteLine(dataEntrega.DayOfWeek);  // Dia da semana
Console.WriteLine(dataEntrega.Year);       // Ano
```

### Operações de Linha e Coluna
```csharp
// Exemplo em DataGridView ou operações de matriz
int[,] matriz = new int[3, 3];
for (int row = 0; row < matriz.GetLength(0); row++)
{
    for (int column = 0; column < matriz.GetLength(1); column++)
    {
        Console.WriteLine($"Valor em [{row},{column}]: {matriz[row, column]}");
    }
}
```

## 🏗️ Programação Orientada a Objetos

### Classes e Herança
```csharp
public abstract class ItemVenda 
{
    public abstract decimal CalcularTotal();
}

public class Produto : ItemVenda 
{
    public string Nome { get; set; }
    public decimal Preco { get; set; }

    public override decimal CalcularTotal() 
    {
        return Preco;
    }
}
```

## 🚨 Tratamento de Exceções

```csharp
try 
{
    decimal total = CalcularTotal(produtos);
} 
catch (ArgumentException ex) 
{
    Console.WriteLine($"Erro nos argumentos: {ex.Message}");
} 
catch (Exception ex) 
{
    Console.WriteLine($"Erro inesperado: {ex.Message}");
} 
finally 
{
    LimparRecursos();
}
```

## 📊 Recursos Avançados do .NET

### 🔧 Assemblies no .NET
```csharp
// Referenciando e trabalhando com Assemblies
using System.Reflection;

// Carregando um assembly dinamicamente
Assembly assembly = Assembly.LoadFrom("MinhaLibraria.dll");

// Obtendo tipos do assembly
Type[] tipos = assembly.GetTypes();

// Criando instância de um tipo
object instancia = Activator.CreateInstance(assembly.GetType("NomeClasse"));
```

### 🔀 Programação Assíncrona com async e await
```csharp
public async Task<string> BuscarDadosAsync()
{
    using (HttpClient client = new HttpClient())
    {
        // Operação assíncrona
        string resultado = await client.GetStringAsync("https://api.exemplo.com/dados");
        return resultado;
    }
}

// Chamando método assíncrono
await BuscarDadosAsync();
```

### 📌 Atributos em C#
```csharp
// Definindo um atributo personalizado
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method)]
public class MinhaDescricaoAttribute : Attribute
{
    public string Descricao { get; }
    
    public MinhaDescricaoAttribute(string descricao)
    {
        Descricao = descricao;
    }
}

// Utilizando o atributo
[MinhaDescricao("Classe de processamento de dados")]
public class ProcessadorDados
{
    [MinhaDescricao("Método principal de processamento")]
    public void Processar() { }
}
```

### 🗃️ Coleções Avançadas em C#
```csharp
// Dictionary com métodos avançados
Dictionary<string, Produto> produtos = new Dictionary<string, Produto>();
produtos.TryAdd("Notebook", novoProduto);  // Adiciona se chave não existir

// HashSet para conjuntos únicos
HashSet<string> categorias = new HashSet<string> 
{
    "Eletrônicos", 
    "Roupas", 
    "Alimentos"
};

// Fila e pilha
Queue<Pedido> filaPedidos = new Queue<Pedido>();
Stack<Transacao> pilhaTransacoes = new Stack<Transacao>();
```

### 🔄 Covariância e Contravariância
```csharp
// Covariância
IEnumerable<Produto> produtos = new List<ProdutoEletronico>();

// Contravariância
Action<Produto> processarProduto = (p) => { /* ... */ };
Action<ProdutoEletronico> processarEletronico = processarProduto;
```

### 🌳 Árvores de Expressão
```csharp
// Criando uma árvore de expressão
Expression<Func<int, bool>> ehPar = x => x % 2 == 0;

// Compilando e executando
Func<int, bool> funcaoCompiladaEhPar = ehPar.Compile();
bool resultado = funcaoCompiladaEhPar(10);  // true
```

### 🔁 Iteradores em C#
```csharp
public class GeradorSequencia
{
    public IEnumerable<int> GerarSequenciaFibonacci()
    {
        int a = 0, b = 1;
        while (true)
        {
            yield return a;
            int temp = a;
            a = b;
            b = temp + b;
        }
    }
}
```

### 🔬 Reflexão em C#
```csharp
Type tipoClasse = typeof(Produto);

// Obtendo informações sobre membros
MethodInfo[] metodos = tipoClasse.GetMethods();
PropertyInfo[] propriedades = tipoClasse.GetProperties();

// Criando instância dinamicamente
object instancia = Activator.CreateInstance(tipoClasse);
```

### 💾 Serialização em C#
```csharp
// Serialização JSON
string jsonProduto = JsonSerializer.Serialize(produto);

// Serialização XML
XmlSerializer serializer = new XmlSerializer(typeof(Produto));
using (FileStream stream = new FileStream("produto.xml", FileMode.Create))
{
    serializer.Serialize(stream, produto);
}
```

### Sintaxes Adicionais

#### Palavra-chave `new`
```csharp
// Inicialização simplificada
List<int> numeros = new() { 1, 2, 3, 4, 5 };

// Construtor com parâmetros
public class Produto 
{
    public Produto(string nome) => Nome = nome;
}
```

#### Modificador `params`
```csharp
public void ProcessarProdutos(params Produto[] produtos)
{
    foreach (var produto in produtos)
    {
        // Processar cada produto
    }
}

// Chamada com múltiplos argumentos
ProcessarProdutos(prod1, prod2, prod3);
```

#### Buffer e Span
```csharp
Span<byte> buffer = stackalloc byte[100];
buffer.Clear();
```

#### Campos privados com `_`
```csharp
public class Produto 
{
    private string _nome;
    private decimal _preco;

    public string Nome 
    {
        get => _nome;
        set => _nome = value;
    }
}
```

#### Classe Parcial `partial`
```csharp
// Arquivo Produto.cs
public partial class Produto 
{
    public string Nome { get; set; }
}

// Arquivo Produto.Validacao.cs
public partial class Produto 
{
    public bool Validar() 
    {
        return !string.IsNullOrEmpty(Nome);
    }
}
```

## 🎓 Mini Projeto: Sistema de Vendas

### Simulação de API de Vendas
```csharp
public class SistemaVendas 
{
    private List<Produto> produtos = new List<Produto>();
    
    public void CadastrarProduto(Produto produto) 
    {
        if (produto.Preco > 0) 
        {
            produtos.Add(produto);
            Console.WriteLine($"Produto {produto.Nome} cadastrado.");
        }
    }
    
    public IEnumerable<Produto> ListarProdutosCaros() 
    {
        return produtos
            .Where(p => p.Preco > 100)
            .OrderByDescending(p => p.Preco);
    }
}
```

## 🚀 Exercícios Práticos

### Exercício 1: Gerenciamento de Estoque
1. Crie uma classe `Produto` com propriedades: Nome, Preço, Quantidade
2. Implemente métodos para:
   - Adicionar produto ao estoque
   - Remover produto do estoque
   - Verificar produtos com baixo estoque

### Exercício 2: Sistema de Descontos
1. Crie uma lógica de desconto baseada em:
   - Valor total da compra
   - Quantidade de itens
   - Produtos específicos

## 🏆 Dicas Finais

1. Use `checked` para verificar estouro aritmético em cenários críticos
2. Prefira `DateOnly` para representar datas sem hora
3. Operadores de índice `^` e intervalo `..` simplificam manipulação de coleções
4. LINQ é poderoso para manipulação de dados, mas use com moderação em grandes conjuntos

## 📚 Referências
- [Documentação Oficial da Microsoft](https://docs.microsoft.com/pt-br/dotnet/csharp/)
- [Microsoft .Net Learn C#](https://dotnet.microsoft.com/en-us/learn/csharp)
