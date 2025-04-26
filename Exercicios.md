#EXERCÍCIOS MONGODB

##EXERCÍCIO 1:

Clientes: Insira os seguintes clientes na coleção **client**:

```javascript
db.client.insertOne({
  "full_name": "Maria Silva",
  "cpf": "12345678901",
  "email": "maria.silva@email.com",
  "phone": "11987654321",
  "address": "Rua das Flores, 123",
  "city": "São Paulo",
  "state": "SP",
  "zip_code": "01001000",
  "created_by": "user_id_do_admin_1",
  "enterprise": null,
  "cnpj_enterprise": null,
  "description": "Cliente individual"
})

db.client.insertOne({
  "full_name": "Empresa Soluções Ltda",
  "cpf": null,
  "email": "contato@solucoes.com.br",
  "phone": "21998765432",
  "address": "Avenida Principal, 456",
  "city": "Rio de Janeiro",
  "state": "RJ",
  "zip_code": "20010020",
  "created_by": "user_id_do_manager_2",
  "enterprise": "Soluções Ltda",
  "cnpj_enterprise": "12345678000190",
  "description": "Cliente corporativo"
})

```

Processos: Insira os seguintes processos para os clientes na coleção **client_processes**:
```javascript
db.client_processes.insertOne({ "client_id": "id_do_cliente_maria_silva",
  "number": "PROC-2023-001",
  "value": 1500.00,
  "status": "ativo",
  "class": "Cobrança",
  "description": "Processo de cobrança referente a fatura em aberto.",
  "created_at": ISODate("2023-10-26T10:00:00Z")
  })

  db.client_processes.insertOne({
  "client_id": "id_do_cliente_empresa_solucoes",
  "number": "PROC-2023-002",
  "value": 5500.50,
  "status": "em análise",
  "class": "Contratual",
  "description": "Análise de contrato de prestação de serviços.",
  "created_at": ISODate("2023-11-15T14:30:00Z")
})


```

Eventos: Insira os seguintes eventos na coleção **events**:
```javascript
db.events.insertMany([{
  "client_id": "id_do_cliente_maria_silva",
  "enterprise": null,
  "cnpj_enterprise": null,
  "freight": 50.00,
  "amount_of_cleaning": 2,
  "cleaning_date": "2023-12-10",
  "cost_of_each_cleanin": 200.00,
  "proposal_doc": "PROP-MS-001.pdf",
  "number_proposal": "PROP-2023-001-MS",
  "proposal_expiration_date": ISODate("2023-11-30T23:59:59Z"),
  "created_proposal_by": "user_id_do_sales_3",
  "address": "Rua das Camélias, 789",
  "city": "São Paulo",
  "state": "SP",
  "zip_code": "02002000",
  "proposal_status": "accepted"
}, {
  "client_id": "id_do_cliente_empresa_solucoes",
  "enterprise": "Soluções Ltda",
  "cnpj_enterprise": "12345678000190",
  "freight": 120.00,
  "amount_of_cleaning": 5,
  "cleaning_date": "2024-01-15",
  "cost_of_each_cleanin": 150.00,
  "proposal_doc": "PROP-ESL-002.pdf",
  "number_proposal": "PROP-2023-002-ESL",
  "proposal_expiration_date": ISODate("2023-12-20T23:59:59Z"),
  "created_proposal_by": "user_id_do_sales_3",
  "address": "Avenida das Palmeiras, 1011",
  "city": "Rio de Janeiro",
  "state": "RJ",
  "zip_code": "22020030",
  "proposal_status": "pending accepted"
}])


```
## Exercício 2:

Clientes em São Paulo: Encontrando todos os clientes que estão localizados na cidade de "São Paulo".
```javascript
db.client.find({city:"São Paulo"});
```

Processos com Valor Superior a: Listando todos os processos na coleção client_processes cujo valor (value) seja maior que 2000.
```javascript
db.client_processes.find({value:{$gt:2000}});
```

Eventos com Proposta Pendente ou Aceita: Encontrando todos os eventos onde o proposal_status seja "pending accepted" ou "accepted".
```javascript
db.events.find(
  {
    "$or": [
      { "proposal_status":  "pending accepted"  }, { "proposal_status": "accepted"}
    ]
  });
```

Clientes Corporativos: Listando todos os clientes onde o campo enterprise não seja null. E exibindo apenas os campos full_name e cnpj_enterprise.
```javascript
db.client.find({enterprise:{$ne:null}},{ "full_name": 1, "cnpj_enterprise": 1, "_id": 0 })
```

Processos de Cobrança: Encontrando todos os processos cuja class seja "Cobrança" e ordenando-os por valor em ordem decrescente.
```javascript
db.client_processes.find({class:"Cobrança"}).sort({"value":-1});
```

## Exercício 3: Atualizando Dados

Atualizar Status do Processo: Alterando o status do processo com number "PROC-2023-001" para "concluído".
```javascript
db.client_processes.updateOne(
  {number:"PROC-2023-001"},
  {$set:{status:"concluído"}}
);
```

Adicionar Observação ao Evento: Adicionando o campo note_doc com o valor "OBS-MS-001.txt" ao evento de Maria Silva.
```javascript
db.events.updateOne({client_id:"id_do_cliente_maria_silva"},{$set:{"note_doc": "OBS-MS-001.txt"}})
```

Incrementar Quantidade de Limpezas: Aumentando em 1 a amount_of_cleaning para o evento da Empresa Soluções Ltda.
```javascript
db.events.updateOne(
  {client_id:'id_do_cliente_empresa_solucoes'},
  {$set:{amount_of_cleaning:5+1}}
);
```

## Exercício 4: Excluindo Dados

Remover Processo: Removendo o processo com number "PROC-2023-002".
```javascript
db.client_processes.deleteOne({number:"PROC-2023-002"});
```

Remover Clientes sem CNPJ: Removendo todos os clientes onde o campo cnpj_enterprise seja null.
```javascript
db.events.deleteMany({cnpj_enterprise:null});
```

## Exercício 5: Criando Índices

Índice no Nome do Cliente: Criando um índice no campo full_name da coleção client.
```javascript
db.client.createIndex({"full_name":1});
```

Visualizar Índices: Listando todos os índices da coleção client.
```javascript
db.client.getIndexes();
```