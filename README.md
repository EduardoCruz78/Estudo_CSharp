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

## 🔍 Fundamentos da Linguagem

### Estrutura Básica
```csharp
namespace VendasProdutos 
{
    public class Program 
    {
        public static void Main(string[] args) 
        {
            Console.WriteLine("Bem-vindo ao Sistema de Vendas!");
        }
    }
}
```

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

## 🧩 Operadores e Expressões

### Operadores Especiais
- **Null-Coalescing `??`**: 
  ```csharp
  string nome = nomeOriginal ?? "Padrão";
  ```
  - **Quando usar**: Definir um valor padrão caso o original seja nulo
  - **Quando não usar**: Quando precisar de lógica de verificação mais complexa

- **Null-Conditional `?.`**:
  ```csharp
  int? tamanho = objeto?.PropiedadeNula?.Tamanho;
  ```
  - **Quando usar**: Evitar NullReferenceException em chamadas de métodos ou acesso a propriedades
  - **Quando não usar**: Quando a verificação nula pode alterar significativamente a lógica de negócio

- **Expressão Lambda `=>`**:
  ```csharp
  var produtosCaros = produtos.Where(p => p.Preco > 100);
  ```
  - **Quando usar**: Funções anônimas, filtros em LINQ, delegates simples
  - **Quando não usar**: Lógicas complexas que prejudicam a legibilidade

## 🔤 Manipulação de Strings

### Métodos de String
```csharp
string texto = "  Produto Exemplo  ";
string nome = "Notebook";

Console.WriteLine(texto.Trim());              // Remove espaços
Console.WriteLine(texto.Replace(" ", "_"));   // Substituição
Console.WriteLine(nome.Contains("Note"));     // Verificação
Console.WriteLine(nome.ToUpper());            // Maiúsculas
Console.WriteLine(nome.ToLower());            // Minúsculas
Console.WriteLine(nome.Length);               // Tamanho
Console.WriteLine(nome.StartsWith("N"));      // Início
```

### Interpolação de Strings
```csharp
string mensagem = $"Produto: {nome}, Preço: {preco:C2}";
```

## 🔀 Estruturas de Controle

### Condicionais
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

### Laços de Repetição
```csharp
// For tradicional
for (int i = 0; i < produtos.Count; i++) 
{
    Console.WriteLine(produtos[i]);
}

// Foreach
foreach (var produto in produtos) 
{
    Console.WriteLine(produto.Nome);
}

// While
while (estoque > 0) 
{
    VenderProduto();
    estoque--;
}

// Do-While
do 
{
    ProcessarPedido();
} while (filaProcessamento.Count > 0);
```

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

## 📚 Recursos Adicionais
- [Documentação Oficial da Microsoft](https://docs.microsoft.com/pt-br/dotnet/csharp/)
- Cursos online
- Comunidades de desenvolvedores
