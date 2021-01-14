<p align="center">
  <img src="mongoDB.png" alt="mongo" width=300 height=150>
</p>

<h1 align="center">
    Curso de MongoDB
</h1>
 

## :notebook_with_decorative_cover: Sobre o curso

Curso "MongoDB: Uma alternativa aos bancos relacionais tradicionais" da plataforma Alura.
 - Aprenda sobre um dos principais bancos de dados NoSQL do mercado
 - Realize operações de CRUD com MongoDB
 - Filtre dados com o MongoDB
 - Busque por proximidade com o Mongo DB
 - Compare o Mongo DB com o SQL

### Instalação 

```
$ sudo apt install mongodb
$ sudo systemctl status mongodb
$ sudo mongo
```

### :arrow_right: Criando e inserindo em uma coleção

 - Criar uma coleção:
 ```
 db.createCollection("alunos")
 ```
 
 - Inserir valores:
 ```
 db.alunos.insert(
  {
  "nome" : "Amanda",
  "data_nascimento" : new Date(1994,02,26)
  }
)
 ```
 
 ```
 db.alunos.insert(
  { 
  "nome" : "Amanda",
  "data_nascimento" : new Date(1994,02,26),
  "curso" : {
  "nome" : "Sistemas de Informação" 
  },
  "notas" : [10.0, 9.0, 5.0],
  "habilidades" : [ 
  { 
  "nome" : "inglês", 
  "nível" : "avançado" 
  }, 
  { 
  "nome" : "dança",
  "nível" : "básico" 
  } 
  ] 
  } 
)
 ```
 
 - Fazendo uma pesquisa:
 ```
 db.alunos.find()
 ```
 
 - Remover dados:
 ```
 db.alunos.remove({
  "_id" : ObjectId("5fb19d41335f5fe9b09f37b4")
  }
 )
 ```
 
 ### :arrow_right: Consultando os dados
 
 - Para ver os resultados de uma forma mais organizada:
 ```
 db.alunos.find().pretty()
 ```
 - *O find( ) é como um SELECT não filtrado de um banco relacional*
 
 - Para achar dados específicos: 
 ```
 db.alunos.find(
  {
  nome : "Amanda"
  }
  ).pretty()
 ```
 
 ```
 db.alunos.find({ "habilidades.nome" : "dança"}). pretty()
 ```
 
 - Consultas com OR, AND e IN
 - *Com o OR, traz qualquer uma que seja verdadeira*
 ```
 db.alunos.find(
  {
  $or : [
  {"curso.nome" : "Sistemas de Informação"},  
  {"curso.nome" : "Letras"}
  ]
  }
)
 ```
 
 ```
 db.alunos.find(
  {
  $or : [
  {"curso.nome" : "Sistemas de Informação"},
  {"curso.nome" : "Letras"}
  ],
  "nome" : "Daniela"
  }
)
 ```

```
db.alunos.find(
  {
  "curso.nome" : { $in : ["Sistemas de Informação", "Letras"
  ]}
  })
```

### :arrow_right: Atualizando dados

```
db.alunos.update{
  {"curso.nome" : "Sistema de Informação"}, #o que eu quero alterar
  {
  $set : {
  "curso.nome" : Sistemas de Informação" #para o que eu quero alterar
  }
  }
)
```
- *O update, por padrão, só vai atualizar o primeiro documento que ele encontra*

- Para alterar múltiplas linhas:
```
db.alunos.update{
  {"curso.nome" : "Sistema de Informação"},
  {
  $set : {
  "curso.nome" : Sistemas de Informação"
  }
  },
  {
  multi : true #default é false
  }
)
```

- Para atualizar somente um campo
- Adicionar um valor no array:
```
db.alunos.update(
  {"id_" : {id do objeto}},
  {
  $push: {
    notas : 8.5
  }
  }
)
```
- *$addToSet : não permite valores repitidos. Ele só adiciona o valor se não tiver um valor igual já no array*
- *o $push permite usar valores repitidos*

- Colocar dois valores de uma vez: 
```
db.alunos.update(
{"id_" : {id do objeto}},
{
  $push: {
    notas : {$each : [8.5, 9] }
  }
}
)
```
### :arrow_right: Ordenando e limitando os dados

- Para achar, por exemplo, alunos com uma determinada nota:
```
db.alunos.find(
  {
  "notas" : 8.5
  }
)
```

- *O find() busca dentro do array*

- Maior que:
```
db.alunos.find(
  {
  "notas" : {$gt : 5} //maior que 5 
  }
)
```
- *Basta uma nota do array ser maior que 5*

- Menor que:
```
db.alunos.find({"notas":{$lt:5}})
```

- Se quiser só um registro, usar o findOne:
```
db.alunos.findOne(
  {
  "notas" : {$gt : 5} //maior que 5
  }
)
```

- Ordenar pelo campo nome, de modo crescente:
```
db.alunos.find().sort({"nome" : 1}) //-1 é decrescente
db.alunos.find().sort({"nome" : 1}).limit(3) //só três registros
```

### :arrow_right: Busca por proximidade

- Inserir uma posição do mapa em um aluno:
```
db.alunos.update(
  { "_id" : {id do objeto} },
  {
  $set: {
  localizacao : {
  endereco : "Rua exemplo, 222",
  cidade : "São Paulo",
  coordenadas : [-23.3232323,
		-32.3232323],
  type : "Point"		
}}})
```
- *"Coordinates" é o padrão em inglês, para poder fazer buscas. Assim como o "type"*

- Busca por posicionamento 
```
db.alunos.aggregate([
  {
  $geoNear : {
  near {
  coordinates: [-32.323232, -32,323232],
  type: "Point"
  },
  distanceField : "distancia.calculada",
  spherical : true,
  num : 3 //só traz três resultados
  }
  },
  { $skip : 1 } //ignorar o primeiro resultado, que é a própria pessoa
])
```
- Buscar a partir do campo "localização":
```
db.alunos.createIndex({
localizacao : "2dsphere"
})
```
- *2d : longitude e latitude*

- Importar json com dados:
```
$ mongoimport -c alunos --jsonArray < alunos.json
```




