<h1 align='center'>O Hospital Fundamental</h1>

<h2>🎲Parte 1 - DER</h2>

<p>Analise a seguinte descrição e extraia dela os requisitos para o banco de dados:
O hospital necessita de um sistema para sua área clínica que ajude a controlar consultas realizadas. Os médicos podem ser generalistas, especialistas ou residentes e têm seus dados pessoais cadastrados em planilhas digitais. Cada médico pode ter uma ou mais especialidades, que podem ser pediatria, clínica geral, gastroenterologia e dermatologia. Alguns registros antigos ainda estão em formulário de papel, mas será necessário incluir esses dados no novo sistema.

Os pacientes também precisam de cadastro, contendo dados pessoais (nome, data de nascimento, endereço, telefone e e-mail), documentos (CPF e RG) e convênio. Para cada convênio, são registrados nome, CNPJ e tempo de carência.

As consultas também têm sido registradas em planilhas, com data e hora de realização, médico responsável, paciente, valor da consulta ou nome do convênio, com o número da carteira. Também é necessário indicar na consulta qual a especialidade buscada pelo paciente.

Deseja-se ainda informatizar a receita do médico, de maneira que, no encerramento da consulta, ele possa registrar os medicamentos receitados, a quantidade e as instruções de uso. A partir disso, espera-se que o sistema imprima um relatório da receita ao paciente ou permita sua visualização via internet.</p>

<br>
<img align='center' src='diagramaparte1.png'/>
<br>

<h2>🎲Parte 2 - DER</h2>

<p>No hospital, as internações têm sido registradas por meio de formulários eletrônicos que gravam os dados em arquivos. 

Para cada internação, são anotadas a data de entrada, a data prevista de alta e a data efetiva de alta, além da descrição textual dos procedimentos a serem realizados. 

As internações precisam ser vinculadas a quartos, com a numeração e o tipo. 

Cada tipo de quarto tem sua descrição e o seu valor diário (a princípio, o hospital trabalha com apartamentos, quartos duplos e enfermaria).

Também é necessário controlar quais profissionais de enfermaria estarão responsáveis por acompanhar o paciente durante sua internação. Para cada enfermeiro(a), é necessário nome, CPF e registro no conselho de enfermagem (CRE).

A internação, obviamente, é vinculada a um paciente – que pode se internar mais de uma vez no hospital – e a um único médico responsável.</p>

<br>
<img align='center' src='diagramaparte2.png'/>
<br>

<h2>🎲Parte 3 - O Prisioneiro dos Dados </h2>
<p>Crie scripts de povoamento das tabelas desenvolvidas na atividade anterior</p>
<ul>
  <li>Inclua ao menos dez médicos de diferentes especialidades.</li>
  <li>Ao menos sete especialidades (considere a afirmação de que “entre as especialidades há pediatria, clínica geral, gastrenterologia e dermatologia”).</li>
  <li>Inclua ao menos 15 pacientes.</li>
  <li>Registre 20 consultas de diferentes pacientes e diferentes médicos (alguns pacientes realizam mais que uma consulta). As consultas devem ter ocorrido entre 01/01/2015 e 01/01/2022. Ao menos dez consultas devem ter receituário com dois ou mais medicamentos.</li>
  <li>Inclua ao menos quatro convênios médicos, associe ao menos cinco pacientes e cinco consultas.</li>
  <li>Criar entidade de relacionamento entre médico e especialidade. </li>
  <li>Criar Entidade de Relacionamento entre internação e enfermeiro. </li>
  <li>Arrumar a chave estrangeira do relacionamento entre convênio e médico.</li>
  <li>Criar entidade entre internação e enfermeiro.</li>
  <li>Colocar chaves estrangeira dentro da internação (Chaves Médico e Paciente).</li>
  <li>Registre ao menos sete internações. Pelo menos dois pacientes devem ter se internado mais de uma vez. Ao menos três quartos devem ser cadastrados. As internações devem ter ocorrido entre 01/01/2015 e 01/01/2022.</li>
  <li>Considerando que “a princípio o hospital trabalha com apartamentos, quartos duplos e enfermaria”, inclua ao menos esses três tipos com valores diferentes.</li>
  <li>Inclua dados de dez profissionais de enfermaria. Associe cada internação a ao menos dois enfermeiros.</li>
  <li>Os dados de tipo de quarto, convênio e especialidade são essenciais para a operação do sistema e, portanto, devem ser povoados assim que o sistema for instalado.</li>

</ul>

<h2>🎲Parte 4 - A Ordem do Alterar </h2>
<p>Pensando no banco que já foi criado para o Projeto do Hospital, realize algumas alterações nas tabelas e nos dados usando comandos de atualização e exclusão:

Crie um script que adicione uma coluna “em_atividade” para os médicos, indicando se ele ainda está atuando no hospital ou não. 

Crie um script para atualizar ao menos dois médicos como inativos e os demais em atividade.
</p>

<br>
<img align='center' src='diagramaparte3.png'/>
<br>

<h2>🎲Parte 5 - A Ordem do Alterar </h2>
<p>1 - Todos os dados e o valor médio das consultas do ano de 2020 e das que foram feitas sob convênio.</p>

```
SELECT *, AVG(valor_consulta) OVER () AS valor_medio_consulta FROM TB_CONSULTA WHERE YEAR(data_hora_consulta) = 2020;
```

<p>2 - Todos os dados das internações que tiveram data de alta maior que a data prevista para a alta.</p>

```
SELECT * FROM TB_INTERNACAO WHERE data_saida > data_prev_alta;
```

<p>3 - Receituário completo da primeira consulta registrada com receituário associado.</p>

```
SELECT 
  r.*
FROM 
  TB_RECEITA r
  INNER JOIN TB_CONSULTA c ON r.id_consulta = c.id_consulta
ORDER BY 
  c.data_hora_consulta ASC
LIMIT 1;
```

<p>4 - Todos os dados da consulta de maior valor e também da de menor valor (ambas as consultas não foram realizadas sob convênio).</p>

```
SELECT 
  c.*
FROM 
  TB_CONSULTA c
WHERE 
  c.valor_consulta = (SELECT MAX(valor_consulta) FROM TB_CONSULTA WHERE id_convenio IS NULL)
  OR c.valor_consulta = (SELECT MIN(valor_consulta) FROM TB_CONSULTA WHERE id_convenio IS NULL);
```

<p>5 - Todos os dados das internações em seus respectivos quartos, calculando o total da internação a partir do valor de diária do quarto e o número de dias entre a entrada e a alta.</p>

```
SELECT 
  i.*,
  tq.valor_diaria_tipo_quarto * DATEDIFF(i.data_saida, i.data_entrada) AS total_internacao
FROM 
  TB_INTERNACAO i
  INNER JOIN TB_QUARTO q ON i.id_quarto = q.id_quarto
  INNER JOIN TB_TIPO_QUARTO tq ON q.id_tipo_quarto = tq.id_tipo_quarto;
```

<p>6 - Data, procedimento e número de quarto de internações em quartos do tipo “apartamento”.</p>

```
SELECT 
  i.data_entrada,
  i.data_saida,
  q.numero_quarto
FROM 
  TB_INTERNACAO i
  INNER JOIN TB_QUARTO q ON i.id_quarto = q.id_quarto
  INNER JOIN TB_TIPO_QUARTO tq ON q.id_tipo_quarto = tq.id_tipo_quarto
WHERE 
  tq.descricao_tipo_quarto = 'Apartamento';
```

<p>7 - Nome do paciente, data da consulta e especialidade de todas as consultas em que os pacientes eram menores de 18 anos na data da consulta e cuja especialidade não seja “pediatria”, ordenando por data de realização da consulta.</p>

```
SELECT 
  p.nome_paciente,
  c.data_hora_consulta,
  me.descricao_especialidade
FROM 
  TB_CONSULTA c
  INNER JOIN TB_PACIENTE p ON c.id_paciente = p.id_paciente
  INNER JOIN TB_MEDICO m ON c.id_medico = m.id_medico
  INNER JOIN TB_MEDICO_ESPECIALIDADE me ON m.id_medico = me.id_medico
WHERE 
  TIMESTAMPDIFF(YEAR, p.data_nascimento, c.data_hora_consulta) < 18
  AND me.descricao_especialidade!= 'Pediatria'
ORDER BY 
  c.data_hora_consulta ASC;
```

<p>8 - Nome do paciente, nome do médico, data da internação e procedimentos das internações realizadas por médicos da especialidade “gastroenterologia”, que tenham acontecido em “enfermaria”.</p>

```
SELECT 
  p.nome_paciente,
  m.nome_medico,
  i.data_entrada,
  i.data_saida
FROM 
  TB_INTERNACAO i
  INNER JOIN TB_PACIENTE p ON i.id_paciente = p.id_paciente
  INNER JOIN TB_MEDICO m ON i.id_medico = m.id_medico
  INNER JOIN TB_MEDICO_ESPECIALIDADE me ON m.id_medico = me.id_medico
  INNER JOIN TB_QUARTO q ON i.id_quarto = q.id_quarto
WHERE 
  me.descricao_especialidade = 'Gastroenterologia'
  AND q.id_tipo_quarto = (SELECT id_tipo_quarto FROM TB_TIPO_QUARTO WHERE descricao_tipo_quarto = "Enfermaria"
```

<p>9 - Os nomes dos médicos, seus CRMs e a quantidade de consultas que cada um realizou.</p>

```
SELECT 
  m.nome_medico,
  m.cpf_medico AS CRM,
  COUNT(c.id_consulta) AS quantidade_consultas
FROM 
  TB_MEDICO m
  LEFT JOIN TB_CONSULTA c ON m.id_medico = c.id_medico
GROUP BY 
  m.id_medico;
```

<p>10 - Todos os médicos que tenham "Gabriel" no nome. </p>

```
SELECT 
  m.nome_medico,
  m.cpf_medico AS CRM
FROM 
  TB_MEDICO m
WHERE 
  m.nome_medico LIKE '%Gabriel%';
```

<p>11 - Os nomes, CREs e número de internações de enfermeiros que participaram de mais de uma internação.</p>

```
SELECT 
  e.nome_enfermeiro,
  COUNT(p.id_plantao) AS quantidade_internacoes
FROM 
  TB_ENFERMEIRO e
  INNER JOIN TB_PLANTAO p ON e.id_enfermeiro = p.id_enfermeiro
GROUP BY 
  e.id_enfermeiro
HAVING 
  COUNT(p.id_plantao) > 1;
```
