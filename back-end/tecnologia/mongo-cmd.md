<!-- TITLE: Mongo Cmd -->
<!-- SUBTITLE: A quick summary of Mongo Cmd -->

\[[Voltar](../index.html)]

# Lista de Comandos #

#### Casos reais ####
[https://bitbucket.org/eguru/game-api-2.0/wiki/Casos%20Reais%20(Exemplos)](https://bitbucket.org/eguru/game-api-2.0/wiki/Casos%20Reais%20(Exemplos))


#### Backup / executar via linha de comando ####
```

//O -u e -p não são obrigatórios, só se existir um usuário configurado

mongodump --host ecpreprodapp1.cloudapp.net -d NOME_DO_BANCO[eguru-dev] --port PORTA[27017] 
    -o CAMINHO['C:/backups'] -u NOMEUSUARIO[user1] -p SENHA[user1&q1w2]

//ex.:
mongodump --host ecpreprodapp1.cloudapp.net -d eguru-dev --port 27017 -o C:/backups
```


#### Restore de backup / executar via linha de comando ####
```

mongorestore (--collection NOME_DA_COLECAO[people](não obrigatório, não usar se for restaurar banco completo))
    --db NOME_DO_BANCO[eguru-dev] CAMINHO(C:/backups/eguru-dev)

//ex.:
mongorestore --db eguru-dev C:/backups/eguru-dev
```


#### Update em coleção interna ####

* Um nível de array interna:
```

db.Processos.updateMany({'Atividades.NomeDoGame':'loja-de-herois'},
                        { $set: {'Atividades.$.UrlDaAtividade': 'teste.com.br'}},
                        {upsert:false})

```

* Mais do que um nível de arrays internas:
```

db.Candidatos.find({},{IdUsuario:0}).forEach(function(candidato){     
            
    for(var y = 0; y < candidato.Processos.length; y++){             
        
        for(var x = 0; x < candidato.Processos[y].Atividades.length; x++){                   
            candidato.Processos[y].Atividades[x].ValidadeDaAtividadeEmMeses = 8;                           
        }         
    }        
    db.Candidatos.update({_id: candidato._id}, candidato, {upsert:false});     

});
```


#### Valor de coleção interna como lista ####
```

var teste = db.getCollection('UsuariosGames').aggregate([
                     { $match: {'Rodadas.Tempo' : {$gte:6000}, 'Rodadas.Status': 2 } },
                     { $project: { IdDoUsuario : 1, _id:0 }}]);
var result = [];
teste.forEach(function(el){
    result.push(el.IdDoUsuario)
    });
result;
```


#### Helpers para lidar com UUID ####

Copiar o conteúdo do link abaixo, e colar no arquivo .mongorc.js que normalmente se encontra em C:/Usuarios/{NomeDousuario}/.mongorc.js . Em seguida, com o Robomongo aberto clicar em "Options" e selecionar a opção "load .mongorc.js". Agora nos novos shells abertos será possivel realizar buscas por NUUID conforme exemplo:

```

 db.getCollection('Candidatos').find({_id: NUUID("5d94e9b2-c5a6-4326-b22b-2f723d0dae08")}) 
```


Versão com NUUID:
[https://gist.githubusercontent.com/rafaeldigenova/c8f272ea5ac7406eb5a019f418417197/raw/6699328bb561cf589c4223c4453588ef9864b261/uuidHelper.js](https://gist.githubusercontent.com/rafaeldigenova/c8f272ea5ac7406eb5a019f418417197/raw/6699328bb561cf589c4223c4453588ef9864b261/uuidHelper.js)

Fonte:
[https://github.com/mongodb/mongo-csharp-driver/blob/master/uuidhelpers.js](https://github.com/mongodb/mongo-csharp-driver/blob/master/uuidhelpers.js)


#### Atualizar múltiplos itens com array ####
```

db.getCollection('Candidatos').update(
        {'Atividades.ClientIdDeAcessoAGameApi': NUUID("ff120b90-dc52-458c-b415-0de366846e78")},
        {$set: {'Atividades.$.ClientIdDeAcessoAGameApi': NUUID("29d850a5-40cb-40c6-aa1c-a97af495c154")}},
        {upsert: true, multi: true }
   )

```


#### Filtro com não igual ####
```

db.getCollection('Candidatos').find(
       {'Atividades.ClientIdDeAcessoAGameApi': {$ne:NUUID("29d850a5-40cb-40c6-aa1c-a97af495c154")}}
)

```