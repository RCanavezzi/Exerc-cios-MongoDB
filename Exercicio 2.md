**1. Modelagem**
Decida quais dados devem ser embutidos (embedded) e quais devem ser referenciados.
**Entidades:**
- Cliente: Escolha para Referenciado, pois é reutilizável e pode ter muitos pedidos.
- Entregador: Escolha para Referenciado, pois pode haver histórico, avaliações ou múltipla entregas.
- Produto: Escolha para Embutido, por ser mais simples, podendo conter somente o nome e o preço do produto. Lembrando que se houver atualizações/variações, por exemplo no preço do produto, recomenda-se o referenciado.
- Pedido: Escolha para Referenciado, por ser o documento principal, ou seja, a base, portanto referencia as outras coleções.

**2. Consultas**

Dado para as consultas
```javascript
db.pedidos.insertOne({
  cliente_id: ObjectId("64b3f"),
  entregador_id: ObjectId("64b3e..."),
  nome:"Carlos Mendes"
  data: ISODate("2024-04-01T00:00:00Z"),
  status: "entregue",
  local: {
    type: "Point",
    coordinates: [-46.6333, -23.5505]
  },
  produtos: [
    { nome: "Pizza Calabresa", preco: 35 },
    { nome: "Coca-Cola 2L", preco: 10 }
  ]
})
```
- Escreva as seguintes consultas:

a -  Retorne todos os pedidos entregues por "Carlos Mendes".
```javascript
db.pedidos.find({ nome_entregador: "Carlos Mendes" });
```

b -  Liste os nomes dos produtos e os totais de vendas (quantidade vendida) agrupados.
```javascript
db.pedidos.aggregate([
  { $unwind: "$produtos" },
  { $group: { _id: "$produtos.nome", total: { $sum: 1 } } }
]);
```

c -  Encontre pedidos entregues perto da coordenada [-46.634, -23.551] com raio de 1 km.
```javascript

db.pedidos.createIndex({ local: "2dsphere" });


db.pedidos.find({
  local: {
    $near: {
      $geometry: { type: "Point", coordinates: [-46.634, -23.551] },
      $maxDistance: 1000
    }
  }
});
```

d -  Retorne todos os pedidos que contenham o item "Pizza Calabresa".
```javascript
db.pedidos.find({ "produtos.nome":"Pizza Calabresa" });
```

e -  Projete apenas o nome do cliente e os nomes dos produtos do pedido.
```javascript
db.pedidos.aggregate({
    $project: { nome_cliente:1, "produtos.nome":1 }
    });
```