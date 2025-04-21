# 🧹 Projeto de Limpeza de Dados e Análise de Padrões da MegaSuper Vendas

## 📋 Descrição do Projeto

Este projeto representa um desafio enfrentado pela startup fictícia **MegaSuper Vendas**, que necessitava reorganizar um banco de dados desorganizado e realizar uma análise aprofundada dos padrões de consumo. A base de dados continha diversas inconsistências, e o objetivo principal foi prepará-la para a aplicação do algoritmo FP-Growth para identificação de **regras de associação**.

## 🧠 Etapas do Projeto

### 1. 🔧 Limpeza e Preparação dos Dados

#### Padronização de Formatos
- Datas: Convertidas para o padrão universal `YYYY-MM-DD`
- Horários: Formatados como `HH:MM:SS`
- Valores numéricos: Colunas `valor`, `quantidade`, `frete` e `total` convertidas para tipos numéricos apropriados

#### Padronização com Dicionários
- **Status**: Correção e padronização dos valores de status utilizando dicionário de mapeamento
- **Pagamentos**: Padronização das diversas nomenclaturas para formas de pagamento
- **Produtos**: Correção de inconsistências nos nomes dos produtos
- **CEPs**: Padronização de CEPs por cidade

#### Tratamento de Inconsistências
- Remoção de registros com valores nulos
- Eliminação de registros duplicados
- Correção da coluna `total` utilizando a fórmula:
  ```
  total = valor * quantidade + frete
  ```

### 2. 📊 Mineração de Regras de Associação

#### 💡 Abordagem Estratégica
Implementamos o algoritmo **FP-Growth** para identificar padrões relevantes entre **produtos e formas de pagamento**, transformando os dados em um formato apropriado para análise.

#### 🔁 Etapas de Pré-processamento

```python
# Criação de identificadores de itens combinando produto e forma de pagamento
df['item'] = df['produto'].astype(str).str.strip() + ' | ' + df['pagamento'].astype(str).str.strip()

# Agrupamento por transação
transacoes = df.groupby('id_da_compra')['item'].apply(list).tolist()
```

#### ➕ Codificação Binária para o FP-Growth

```python
# Importação e aplicação do TransactionEncoder
from mlxtend.preprocessing import TransactionEncoder
te = TransactionEncoder()
df_bin = pd.DataFrame(te.fit(transacoes).transform(transacoes), columns=te.columns_)
```

#### ⚙️ Aplicação do Algoritmo FP-Growth

```python
# Importação das funções necessárias
from mlxtend.frequent_patterns import fpgrowth, association_rules

# Identificação de padrões frequentes
frequentes = fpgrowth(df_bin, min_support=0.02, use_colnames=True)

# Geração de regras de associação
regras = association_rules(frequentes, metric="confidence", min_threshold=0.5)

# Ordenação das regras mais relevantes
regras_ordenadas = regras[['antecedents', 'consequents', 'support', 'confidence', 'lift']].sort_values(by='lift', ascending=False)
print(regras_ordenadas.head(10))
```

## 🛠️ Ferramentas e Bibliotecas Utilizadas

- **pandas**: Manipulação e limpeza dos dados
- **mlxtend**: Implementação do algoritmo FP-Growth
- **numpy**: Operações numéricas (quando necessário)
- **matplotlib/seaborn**: Visualizações de dados (opcionais)

## 📈 Resultados Obtidos

| Antecedente | Consequente | Confiança | Lift |
|-------------|-------------|-----------|------|
| (refrigerante \| pix) | (suco de laranja \| pix) | 0.72 | 3.8 |
| (papel toalha \| cartão de crédito) | (desinfetante \| cartão de crédito) | 0.65 | 3.2 |
| (desinfetante \| transferência bancária) | (papel toalha \| transferência bancária) | 0.58 | 2.9 |

Estes padrões revelam associações significativas entre produtos específicos e métodos de pagamento, informações valiosas para personalização de campanhas, criação de combos promocionais ou otimização logística.

## 📝 Resumo de Problemas Solucionados

| Problema Encontrado | Solução Aplicada |
|---------------------|------------------|
| Datas em múltiplos formatos | Conversão com pd.to_datetime() |
| Horas inconsistentes | Parsing com dt.time |
| Números como string ou com vírgula | Conversão via pd.to_numeric() |
| Dados faltantes (nulos) | Remoção com dropna() |
| Cálculo incorreto do total | Recalculado com valor * quantidade + frete |
| Status inconsistentes | Padronização com dicionário de mapeamento |
| Nomenclaturas variadas de pagamento | Uniformização com dicionário |
| Inconsistências nos nomes de produtos | Padronização com dicionário de correção |
| CEPs incorretos | Mapeamento por cidade e correção |


## 👨‍💼 Contato

**[Mateus Werneck]**  
GitHub: [github.com/Mwrnk]  
LinkedIn: [linkedin.com/in/mateuswerneck]  
E-mail: mateuswerneckelizeu@gmail.com

> *"Limpeza de dados é como organizar um armário: parece trabalhoso, mas quando terminamos, tudo fica mais acessível e útil."* 🧹📊