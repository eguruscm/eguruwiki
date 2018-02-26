<!-- TITLE: Api 1 -->
<!-- SUBTITLE: A quick summary of Api 1 -->

\[[Voltar](../home)]

# Games BackEnd - Suporte - API 1.0

## consultas
### Respostas do usuário
#### Query realizada no banco de administração
	select * from Administrador.UsuarioSistema where Email = 'email para consulta'
	--id = idUsuario

	select * from Administrador.UsuarioAtividade where IdUsuario = --idUsuario
	--id = idAtividade
#### Query realizada no banco da API
	select * from GameAPI.UsuarioGame where IdUsuario = --idAtividade

	select qMAT.* from GameAPI.Tela t 
	join GameAPI.Exercicio e on t.Id = e.IdTela
	join GameAPI.Questao q on q.IdExercicio = e.Id
	left join GameAPI.RespostaExercicio rE on e.Id = rE.IdExercicio and rE.IdUsuario = --idAtividade
	left join GameAPI.RespostaQuestao rQ on rQ.IdQuestao = q.Id and rQ.IdRespostaExercicio = rE.Id
	left join GameAPI.RespostaMultiplaEscolha rME on rME.IdRespostaQuestao = rQ.Id and rME.IdQuestaoMultiplaEscolha = q.IdMultiplaEscolha
	left join GameAPI.AlternativaSelecionada altS on altS.IdRespostaMultiplaEscolha = rME.Id
	left join GameAPI.QuestaoMultAlternativaTraducao qMAT on qMAT.IdQuestaoAlternativa = altS.IdAlternativa


Após realizar essa busca, você receberá listado todas as respostas fornecidas por aquele usuário
