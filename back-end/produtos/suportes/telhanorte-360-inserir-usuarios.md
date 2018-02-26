<!-- TITLE: Telhanorte 360 Inserir Usuarios -->
<!-- SUBTITLE: A quick summary of Telhanorte 360 Inserir Usuarios -->

# Importação Usuários - telhanorte-360
## Edição do Excel
  Faça download do [template](/uploads/telhanorte-360/importacao-telhanorte-template.csv "Importacao Telhanorte Template") para inserção dos usuários e preencha-o com a tabela de novos usuários.
	**Importante**
	os campos devem ser preenchidos da seguinte forma:
	* Nomes em caixa alta e sem acentos ou ç
	* CPFs com 11 digitos(formatar campos)
	* branch deve estar preenchido como *null*

-----

## Backup do banco
  Após editar o template com os novos usuários, faça um backup do banco por precaução. Siga os passos indicados abaixo:
	
> Exportar banco como sql
> 
> ![Telhanorte 01](/uploads/telhanorte-360/telhanorte-01.png "Telhanorte 01")

> Configurações para exportação
> 
> ![Telhanorte 02](/uploads/telhanorte-360/telhanorte-02.png "Telhanorte 02")

## Checagens da importação
  Com o backup feito, é necessário realizar algumas checagens antes de subir os novos usuários para a tabela final.
	
> Importar o .csv na tabela insertUsers
>
> ![Telhanorte 03](/uploads/telhanorte-360/telhanorte-03.png "Telhanorte 03")

> Configurações da importação
> 
> ![Telhanorte 04](/uploads/telhanorte-360/telhanorte-04.png "Telhanorte 04")

> acessar a aba consultas
> 
> ![Telhanorte 05](/uploads/telhanorte-360/telhanorte-05.png "Telhanorte 05")

> realizar os seguintes updates
> 
> ```sql
> update insertUsers set branch = NULL;
> ```
> 
> ```sql
> update insertUsers set branch = (select branches.id from branches where branches.name = insertUsers.loja);
> ```
> 
> e depois realizar as consultas
> 
> Verificar se os usuários tem branch ID(o retorno deve ser 0)
> 
> ```sql
> Select * from insertUsers where branch is null;
> ```
> 
> Verificar se existem matriculas repetidas(o retorno deve ser 0)
> 
> ```sql
> SELECT matricula, COUNT(*) c FROM insertUsers GROUP BY matricula HAVING c > 1;
> ```
> 
> Checar se o usuário ja existe(o retorno deve ser 0)
> 
> ```sql
> select insertUsers.* from insertUsers, registrations where insertUsers.matricula = registrations.registration;
> 
> select insertUsers.* from insertUsers, registrations where insertUsers.cpf = registrations.cpf;
> ```
> 
> ```sql
> INSERT INTO `registrations`(`registration`, `name`, `cpf`, `branch`) 
> (select matricula, nome, cpf, branch from insertUsers)
> ```
>
> Checagem final(deve retornar 0)
>
> ```sql
> select registrations.* from insertUsers, registrations where insertUsers.matricula = registrations.registration; 
> ```
> 


