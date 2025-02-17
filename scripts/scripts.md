### Importação de bibliotecas 

```python
import pandas as pd
import matplotlib.pyplot as plt

# Carregar os dados
df = pd.read_excel('Meganium_Sales_data.xlsx', sheet_name='consolidate')

df.info()
```

### Contar o número de vendas por SKU

```python
# Exibir os produtos mais vendidos de forma gráfica
plt.figure(figsize=(10, 6))
bars = plt.bar(sales_by_sku.index, sales_by_sku.values)
plt.title('Produtos Mais Vendidos')
plt.xlabel('SKU')
plt.ylabel('Número de Vendas')

# Adicionar os rótulos de número total de vendas em cima das barras
for bar in bars:
    yval = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2, yval, int(yval), ha='center', va='bottom')

plt.show()
```

### Contar o número de transações por plataforma

```python
transactions_by_site = df['site'].value_counts()

# Exibir os produtos mais vendidos de forma gráfica
plt.figure(figsize=(10, 6))
bars = plt.bar(transactions_by_site.index, transactions_by_site.values)
plt.title('Quantidade de Produtos Vendidos Por Site')
plt.xlabel('Site')
plt.ylabel('Número de Vendas')

# Adicionar os rótulos de número total de vendas em cima das barras
for bar in bars:
    yval = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2, yval, int(yval), ha='center', va='bottom')

plt.show()
```

### Contar o número de transações por país

```python
transactions_by_country = df['delivery_country'].value_counts()

# Exibir os produtos mais vendidos de forma gráfica
plt.figure(figsize=(10, 6))
bars = plt.bar(transactions_by_country.index, transactions_by_country.values)
plt.title('Países com Maior Volume de Vendas')
plt.xlabel('Países')
plt.ylabel('Número de Vendas')

# Adicionar os rótulos de número total de vendas em cima das barras
for bar in bars:
    yval = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2, yval, int(yval), ha='center', va='bottom')

plt.show()
```
### Tendencias temporais

```python
import calendar

# Converter a coluna de data para o tipo datetime
df['date_sold'] = pd.to_datetime(df['date_sold'])

# Extrair o mês e o ano da data
df['month'] = df['date_sold'].dt.to_period('M')
df['month_name'] = df['date_sold'].dt.month.apply(lambda x: calendar.month_name[x])

# Contar o número de transações por mês
transactions_by_month = df['month_name'].value_counts().sort_index()

plt.figure(figsize=(10, 6))
bars = plt.bar(transactions_by_month.index, transactions_by_month.values)
plt.title('Tendências Temporais Vendas por Mês')
plt.xlabel('Mês')
plt.ylabel('Número de Vendas')

# Adicionar os rótulos de número total de vendas em cima das barras
for bar in bars:
    yval = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2, yval, int(yval), ha='center', va='bottom')

plt.show()
```

### Relação entre a média de idade, países e produto vendido
```python
# Remover a string "new meganium" da coluna de produtos
df['product_sold'] = df['product_sold'].str.replace('NEW MEGANIUM', '')

# Calcular a idade do comprador com base na data de nascimento
df['buyer_birth_date'] = pd.to_datetime(df['buyer_birth_date'])
df['buyer_age'] = (pd.to_datetime('today') - df['buyer_birth_date']).dt.days // 365

# Agrupar por tipo de produto e país, calculando a média da idade dos compradores
grouped_df = df.groupby(['product_sold', 'delivery_country'])['buyer_age'].mean().reset_index()

# Plotar o gráfico de dispersão
plt.figure(figsize=(12, 8))
scatter = sns.scatterplot(data=grouped_df, x='product_sold', y='buyer_age', hue='delivery_country', s=100, palette='viridis')
plt.title('Média da Idade dos Compradores por Tipo de Produto e País')
plt.xlabel('Tipo de Produto')
plt.ylabel('Média da Idade dos Compradores')
plt.legend(title='País', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.xticks(rotation=45)

# Adicionar os rótulos com a média da idade em cima dos pontos
for line in range(0, grouped_df.shape[0]):
     scatter.text(grouped_df.product_sold[line], grouped_df.buyer_age[line], round(grouped_df.buyer_age[line], 1), horizontalalignment='left', size='medium', color='black', weight='semibold')

plt.tight_layout()
plt.show()
```

### Relação entre a média de idade, países e produto vendido
```python
# Remover a string "NEW MEGANIUM" da coluna de produtos
df['product_sold'] = df['product_sold'].str.replace('NEW MEGANIUM', '')

# Calcular a idade do comprador com base na data de nascimento
df['buyer_birth_date'] = pd.to_datetime(df['buyer_birth_date'])
df['buyer_age'] = (pd.to_datetime('today') - df['buyer_birth_date']).dt.days // 365

# Agrupar por tipo de produto e país, calculando a média da idade dos compradores
grouped_df = df.groupby(['product_sold', 'delivery_country'])['buyer_age'].mean().reset_index()

# Exibir a saída em forma de tabela
print(grouped_df.to_string(index=False))
```

### Relação de produto, país, valor total, unidades vendidas e valor por unidade
```python
# Calculando a receita total, quantidade vendida e valor por unidade por produto, país e moeda
dados_agrupados = df.groupby(['product_sold', 'delivery_country', 'currency']).agg({
    'total_price': 'sum',
    'quantity': 'sum',
    'unit_price': 'mean'
}).reset_index()

# Renomeando as colunas para algo mais claro
dados_agrupados = dados_agrupados.rename(columns={'total_price': 'total_revenue', 'quantity': 'total_quantity', 'unit_price': 'average_unit_price'})

# Exibindo os resultados como tabela
print(dados_agrupados.to_string(index=False))
```
