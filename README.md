# Atividade 08 - Exercícios SQL: BomGosto Cafeteria 📘

Este repositório contém a resolução de **5 exercícios SQL** do curso **Full Stack Jr (+praTi & Codifica)**.  
As consultas foram feitas em **PostgreSQL**.

---

## Estrutura do Projeto 📎

O projeto contém um único **arquivo SQL** (`atividade08_exercicios.sql`) com toda a lógica:

- Criação do banco de dados `bomgosto`.
- Criação das tabelas (`Comanda`, `Cardapio`, `Item_Comanda`).
- População das tabelas com dados de exemplo (incluindo clientes famosos!).
- Queries para resolução dos 5 exercícios propostos.


---

## Execução da Atividade ✏

1. Abra o **pgAdmin** ou outro cliente PostgreSQL de sua preferência.  
2. Execute o script SQL fornecido na íntegra. As tabelas serão criadas e populadas.
3. Execute as queries de cada exercício individualmente para visualizar os resultados.

> ⚠️ Dica: Pela regra de negócio, cada item do cardápio aparece apenas uma vez por comanda.

---

## Exercícios e Resoluções

### **1️⃣ Listagem do cardápio ordenada por nome**

*Esta query lista todos os itens disponíveis na cafeteria em ordem alfabética.*

```sql
SELECT * 
FROM Cardapio
ORDER BY nome_item ASC;
```

| id_cardapio | nome_item        | descricao           | preco_unitario |
|:------------|:-----------------|:--------------------|:---------------|
| 6           | Bolo             | Chocolate           | 10.00          |
| 9           | Brigadeiro       | Meio Amargo         | 4.00           |
| 4           | Café Com Leite   | Com espuma          | 10.00          |
| 3           | Café Preto       | Médio               | 7.00           |
| 10          | Chocolate Quente | Com Avelã           | 8.00           |
| 7           | Cookie           | Gotas de chocolate  | 2.00           |
| 2           | Coxinha          | Frango              | 10.00          |
| 5           | Pão de Queijo    | Muito queijo        | 5.00           |
| 8           | Pizza            | Pepperoni           | 9.00           |
| 1           | Torrada          | Presunto e queijo   | 12.00          |

### **2️⃣ Comandas com itens detalhados e preço total por item**

*Esta query junta as informações de comandas, itens e cardápio para mostrar um detalhamento de cada pedido. O pedido de Michelangelo foi atualizado para Pizza.*

```sql
SELECT 
    c.id_comanda,
    TO_CHAR(c.data, 'DD/MM/YYYY') AS data,
    c.numero_mesa,
    c.nome_cliente,
    i.id_cardapio,
    ca.nome_item,
    ca.descricao,
    i.quantidade,
    ca.preco_unitario,
    (i.quantidade * ca.preco_unitario) AS preco_total
FROM Comanda c
JOIN Item_Comanda i ON c.id_comanda = i.id_comanda
JOIN Cardapio ca ON i.id_cardapio = ca.id_cardapio
ORDER BY c.data, c.id_comanda, ca.nome_item;
```

| id_comanda | data       | numero_mesa | nome_cliente                    | id_cardapio | nome_item      | descricao         | quantidade | preco_unitario | preco_total |
|:-----------|:-----------|:------------|:--------------------------------|:------------|:---------------|:------------------|:-----------|:---------------|:------------|
| 10         | 04/10/2025 | 1           | Michelangelo(Tartarugas Ninja)  | 8           | Pizza          | Pepperoni         | 1          | 9.00           | 9.00        |
| 20         | 04/10/2025 | 1           | Tony Sterko                     | 2           | Coxinha        | Frango            | 1          | 10.00          | 10.00       |
| 20         | 04/10/2025 | 1           | Tony Sterko                     | 5           | Pão de Queijo  | Muito queijo      | 2          | 5.00           | 10.00       |
| 30         | 04/10/2025 | 2           | Obi-Wan Kenobi                  | 4           | Café Com Leite | Com espuma        | 3          | 10.00          | 30.00       |
| 30         | 04/10/2025 | 2           | Obi-Wan Kenobi                  | 7           | Cookie         | Gotas de chocolate| 4          | 2.00           | 8.00        |
| 40         | 04/10/2025 | 3           | Peter Parker                    | 6           | Bolo           | Chocolate         | 1          | 10.00          | 10.00       |
| 50         | 04/10/2025 | 3           | John Connor                     | 8           | Pizza          | Pepperoni         | 2          | 9.00           | 18.00       |

### **3️⃣ Valor total da comanda**

*Calcula o valor final de cada comanda. Note que o total da comanda de Michelangelo foi recalculado.*

```sql
SELECT 
    c.id_comanda,
    TO_CHAR(c.data, 'DD/MM/YYYY') AS data,
    c.numero_mesa,
    c.nome_cliente,
    SUM(i.quantidade * ca.preco_unitario) AS total_comanda
FROM Comanda c
JOIN Item_Comanda i ON c.id_comanda = i.id_comanda
JOIN Cardapio ca ON i.id_cardapio = ca.id_cardapio
GROUP BY c.id_comanda, c.data, c.numero_mesa, c.nome_cliente
ORDER BY c.data;
```

| id_comanda | data       | numero_mesa | nome_cliente                    | total_comanda |
|:-----------|:-----------|:------------|:--------------------------------|:--------------|
| 10         | 04/10/2025 | 1           | Michelangelo(Tartarugas Ninja)  | 9.00          |
| 20         | 04/10/2025 | 1           | Tony Sterko                     | 20.00         |
| 30         | 04/10/2025 | 2           | Obi-Wan Kenobi                  | 38.00         |
| 40         | 04/10/2025 | 3           | Peter Parker                    | 10.00         |
| 50         | 04/10/2025 | 3           | John Connor                     | 18.00         |

### **4️⃣ Comandas com mais de um tipo de item**

*Filtra para mostrar as comandas com múltiplos itens. A comanda de Michelangelo agora tem apenas um item, então ela não aparece mais aqui.*

```sql
SELECT 
    c.id_comanda,
    TO_CHAR(c.data, 'DD/MM/YYYY') AS data,
    c.numero_mesa,
    c.nome_cliente,
    SUM(i.quantidade * ca.preco_unitario) AS total_comanda
FROM Comanda c
JOIN Item_Comanda i ON c.id_comanda = i.id_comanda
JOIN Cardapio ca ON i.id_cardapio = ca.id_cardapio
GROUP BY c.id_comanda, c.data, c.numero_mesa, c.nome_cliente
HAVING COUNT(DISTINCT i.id_cardapio) > 1
ORDER BY c.data;
```

| id_comanda | data       | numero_mesa | nome_cliente   | total_comanda |
|:-----------|:-----------|:------------|:---------------|:--------------|
| 20         | 04/10/2025 | 1           | Tony Sterko    | 20.00         |
| 30         | 04/10/2025 | 2           | Obi-Wan Kenobi | 38.00         |

### **5️⃣ Total de faturamento por data**

*Calcula o faturamento total diário. O valor foi recalculado após a alteração no pedido de Michelangelo.*

```sql
SELECT 
    TO_CHAR(c.data, 'DD/MM/YYYY') AS data,
    SUM(i.quantidade * ca.preco_unitario) AS total_faturamento
FROM Comanda c
JOIN Item_Comanda i ON c.id_comanda = i.id_comanda
JOIN Cardapio ca ON i.id_cardapio = ca.id_cardapio
GROUP BY c.data
ORDER BY c.data;
```

| data       | total_faturamento |
|:-----------|:------------------|
| 04/10/2025 | 95.00             |
