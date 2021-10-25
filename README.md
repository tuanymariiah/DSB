# ANÁLISE DE DADOS UTILIZANDO OS DADOS DO TCE-RS 
## INDRODUÇÃO
Os datasets a serem utilizados neste projeto foram retirados do [portal dados abertos](http://dados.tce.rs.gov.br/dataset?groups=licitacoes&q=consolidado) além da consulta do [manual](https://portalnovo.tce.rs.gov.br/repo/cex/licitacon/eValidador-licitacon-manual-layout-1.4.pdf), onde procurei responder as perguntas pedidas no teste bem como extrair outras informações a serem importantes.

## ETAPAS QUE FORAM REALIZADAS DURANTE O PROJETO
1. Como relatado, os dados foram extraídos do [portal dados abertos](http://dados.tce.rs.gov.br/dataset?groups=licitacoes&q=consolidado)
2. Logo após, foi feito um filtro utilizando a coluna TP_OBJETO='COM', ou seja, foi filtrado tudo que do tipo compras e em seguida, foi realizado o merge entre as duas tabelas já concatenadas pelos anos, foram elas, licitacoes.csv e itens.csv (seguindo o [manual](https://portalnovo.tce.rs.gov.br/repo/cex/licitacon/eValidador-licitacon-manual-layout-1.4.pdf) disponibilizado)
3. Um outro filtro também foi feito, porém dessa vez com palavras como serviço|servidores|contrato|contratação|anexo|locação|tabela|storage all|constituído deconforme|aquisição|edital tudo que poderia ser ser considerado um contrato de um serviço que não é considerado um bem material. Logo, todos os dados que possuem as palavras já mencionadas, foram excluídos do dataset. 
4. Números, pontuações, acentos, e stopwords foram removidos, e depois convertidos em letras minúsculas
5. Também foi realizado um processo para deixar apenas a primeira palavra, por exemplo

| DS_ITEM | DS_ITEM|
| ------------- |:-------------:|
| pneu de carro  |pneu     |
| biscoito doce   | biscoito|
| biscoit salgado | biscoito|  

6. Dados que possuíam valores vazios foram excluídos
7. Após isso, uma análise descritiva da estatistica foi feita para poder ver inicialmente ver a presença de outliers, com isso, é possível ver que havia a presença de valores negativos paara a coluna VL_UNITARIO_HOMOLOGADO e igual a zero para a coluna VL_UNITARIO_ESTIMADO com isso, foram considerados apenas os itens que possuiam valores acima de 0, para as respectivas tabelas
8. Duas colunas foram criadas para poder analisar melhor este dataset, a primeira coluna foi a VALOR_BEM que vai rotular os dados como alto, baixo ou médio. O valor foi a ser considerado foi baseado na média. a segunda coluna criada foi, conhecer quais/quantos itens foram comprados acima do valor estimado
```
 def valorBem(registro):
  if registro['VL_UNITARIO_HOMOLOGADO'] < 8.461925e+02:
    return 'baixo'
  elif registro['VL_UNITARIO_HOMOLOGADO'] == 8.461925e+02:
    return 'medio'
  elif registro['VL_UNITARIO_HOMOLOGADO'] > 8.461925e+02:
    return 'alto'
df['VALOR_BEM'] = df.apply(valorBem, axis=1)

def situacao_homologado(registro):
    if registro['VL_UNITARIO_HOMOLOGADO'] > registro['VL_UNITARIO_ESTIMADO']:#ACIMA DO VALOR ESTIMADO?
        return 'acima'
    elif registro['VL_UNITARIO_HOMOLOGADO'] < registro['VL_UNITARIO_ESTIMADO']:#ABAIXO DO VALOR ESTIMADO?
        return 'abaixo'
    else:
      return 'igual'#IGUAL AO VALOR ESTIMADO
df['SITUACAO'] = df.apply(situacao_homologado, axis=1)
```
# Perguntas a serem respondidas
---
1. Quais itens foram comprados **acima** do valor estimado?

	|DS_ITEM  |	QT_ITENS    |   VL_UNITARIO_HOMOLOGADO| VL_UNITARIO_ESTIMADO|SITUACAO|
	| ------------- |:-------------:|:-------------:| ------------- |:-------------:|
	|toalha	|10.00|	6.6|	6.0	|acima|
	|toalha	|10.00|	6.6|    6.0	|acima|
	|toalha	|10.00|	6.6|	6.0	|acima|
	|toalha	|25.00|	6.6|	5.2	|acima|

2. Quais itens foram comprados **abaixo** do valor estimado?

	|DS_ITEM  |	QT_ITENS    |   VL_UNITARIO_HOMOLOGADO| VL_UNITARIO_ESTIMADO|SITUACAO|
	| ------------- |:-------------:|:-------------:| ------------- |:-------------:|
	|biscoito|	72.00|	|5.00	|6.5	|abaixo|
	|biscoito|	72.00|	|3.61	|4.0	|abaixo|
	|farinha|	4.00|	|8.58	|9.0	|abaixo|
	|acar|	4.00|	|12.35	|16.5	|abaixo|
3. Quantos itens foram comprados acima, abaixo ou igual ao valor estimado?

![This is a alt text.]("https://github.com/tuanymariiah/DSB/blob/main/imagens/q3.png?raw=true")

4. Quantos itens foram comprados com valor baixo e alto
5. **Quais são os principais bens materiais comprados pelos órgãos públicos do Rio Grande do Sul?**
6. **É possível identificar algum padrão nas compras? Seja por época do ano ou por tipo de órgão comprador?**
7. Em qual ano houve mais licitação?
8. Orgao que mais fez pedido no ano de 2016
9. Orgao que mais fez pedido no ano de 2017
10. Orgao que mais fez pedido no ano de 2018
11. Orgao que mais fez pedido no ano de 2019
