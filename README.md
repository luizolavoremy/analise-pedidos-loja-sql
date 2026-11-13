# Análise de Pedidos em SQL (SQLite)

Reescrita em SQL das 9 perguntas de negócio do [projeto 1](https://github.com/luizolavoremy/analise-pedidos-loja), que usou pandas. Mesma base, mesmas perguntas, outra ferramenta: cada resultado foi conferido contra o que o pandas devolveu.

> **Sobre este projeto:** é um projeto de estudo, feito com apoio de IA (Claude) como ferramenta de aprendizado. A base é **sintética**, gerada para estudo (detalhes no projeto 1), e os números não representam nenhuma empresa real.

**Base:** `pedidos_limpos.csv` com 4.871 pedidos e 14 colunas (as 13 originais mais `receita`), já limpa no projeto 1.

## Como funciona

O notebook carrega o CSV num banco SQLite (`pedidos.db`) com `df.to_sql` e, a partir daí, toda a análise é escrita em SQL e executada com `pd.read_sql`. O pandas serve só de ponte: carrega os dados e mostra o resultado em forma de tabela. O arquivo `pedidos.db` é gerado ao rodar o notebook e não vai para o Git.

## Perguntas respondidas

| # | Pergunta | Técnicas de SQL |
|---|---|---|
| 1 | Produtos mais e menos vendidos (unidades e receita) | `GROUP BY`, `SUM`, `ORDER BY` |
| 2 | Categorias mais e menos vendidas | `GROUP BY`, `SUM`, `ORDER BY` |
| 3 | Taxa de cancelamento geral e por categoria | `CASE WHEN`, `COUNT`, `ROUND` |
| 4 | Estados que mais e menos compram | `GROUP BY`, `SUM` |
| 5 | Canais que mais e menos vendem | `GROUP BY`, `SUM` |
| 6 | Vendas mês a mês | `strftime`, `GROUP BY` |
| 7 | Produto mais e menos vendido em cada estado | `WITH` (CTE), `ROW_NUMBER() OVER (PARTITION BY ...)` |
| 8 | Produto mais e menos vendido em cada canal | `WITH` (CTE), `ROW_NUMBER() OVER (PARTITION BY ...)` |
| 9 | Produtos no mês de pico (nov/2024) e no de vale (fev/2023) | `WHERE` com `strftime`, `GROUP BY` |

## Conferência com o pandas

| Verificação | Resultado em SQL |
|---|---|
| Total de pedidos | 4.871, igual ao `df.shape` do projeto 1 |
| Receita do Smartwatch Fit (1º em receita) | R$ 328.193,83 |
| Taxa geral de cancelamento | 7,92% (386 pedidos) |
| Unidades vendidas em SP (1º estado) | 3.405 |
| Mês de pico | nov/2024, com 825 unidades |

## Equivalência pandas → SQL

| pandas | SQL |
|---|---|
| `df.groupby("produto")["quantidade"].sum()` | `SELECT produto, SUM(quantidade) ... GROUP BY produto` |
| `df[df["status"] == "Cancelado"]` | `WHERE status = 'Cancelado'` |
| `.sort_values(ascending=False)` | `ORDER BY ... DESC` |
| `.head(5)` | `LIMIT 5` |
| `.dt.to_period("M")` | `strftime('%Y-%m', data_pedido)` |
| `pivot_table` com `idxmax` / `idxmin` | CTE com `ROW_NUMBER() OVER (PARTITION BY ...)` |

## Limitações

- A limpeza foi feita no projeto 1: aqui o SQL parte de dados já limpos.
- Funções de data como `strftime` são específicas do SQLite. Em PostgreSQL, MySQL ou SQL Server a sintaxe muda.
- Nos empates (por exemplo, MG na pergunta 7), o desempate é pela ordem alfabética do produto.
- Os resultados descrevem o que aconteceu, sem testar causas. As conclusões de negócio estão no projeto 1.
- A base é sintética.

## Tecnologias

Python 3.14 · SQLite (módulo `sqlite3`) · pandas · Jupyter · Git/GitHub · VS Code

## Como rodar

```bash
git clone https://github.com/luizolavoremy/analise-pedidos-loja-sql.git
cd analise-pedidos-loja-sql
python -m venv venv
venv\Scripts\activate        # Windows (Mac/Linux: source venv/bin/activate)
pip install -r requirements.txt
```

Depois, abra `analise_sql.ipynb`, selecione o kernel do `venv` e rode todas as células. O banco `pedidos.db` é criado automaticamente.

## Estrutura do repositório

```
analise-pedidos-loja-sql/
├── analise_sql.ipynb      # notebook com as 9 perguntas em SQL
├── pedidos_limpos.csv     # base já limpa no projeto 1
├── requirements.txt
└── README.md
```

O arquivo `pedidos.db` é gerado ao rodar o notebook e fica fora do Git.

## Projetos relacionados

- Análise e limpeza com pandas: [analise-pedidos-loja](https://github.com/luizolavoremy/analise-pedidos-loja)
- Dashboard em Power BI sobre os mesmos dados: [analise-pedidos-loja-powerbi](https://github.com/luizolavoremy/analise-pedidos-loja-powerbi)

## Autor

Luiz Olavo Remy Firmino · https://www.linkedin.com/in/luiz-olavo-remy · luiz.olavo.remy@gmail.com
