# Questão 1 : 
## Prepare um relatório que mostre a média salarial dos funcionários de cada departamento.
#### Com o comando : "select avg(salario) from funcionario;" conseguimos ver a média salarial dos funcionários que é de: 35125.00

# Questão 2 :

## Prepare um relatório que mostre a média salarial dos homens e das mulheres.
#### Com o comando : "select (sum(salario)/count(*)) as media from funcionario where sexo = 'F';" conseguimos ver a média salarial dos funcionários mulheres da empresa onde a média salarial das mulheres é: 31.000,00.
#### Agora para saber a média salarial dos homens basta apenas trocar a variável de valor 'F' para 'M'. O comando ficará : "select (sum(salario)/count(*)) as media from funcionario where sexo = 'M';" e a média será : 37.600,00.


# Questões 3 :

## Prepare um relatório que liste o nome dos departamentos e, para cada departamento, inclua as seguintes informações de seus funcionários: o nome completo, a data de nascimento, a idade em anos completos e o salário.

#### Analisando as informações que temos no banco de dados do pset1 para realizar essa pergunta noto que na tabela funcionário falta uma coluna "idade", então antes de colocar o comando para calcular a idade em anos completos criei a tabela (idade integer), após fazer isso calculei a diferença das datas do dia de hoje e a data de nascimento de cada funcionário e isso nos traz a idade dos funcionários que seria com esse comando:

SELECT datediff('2022-05-02','1965-01-09')/365 AS 'idade_joao';

Colocando esse comando ele nos retorna uma idade quebrada igual a 57.3479 que nao seria o certo por nao ser um numero inteiro, portanto para resolver isso 
inserimos outro comando pra arredondar a idade do funcionario que é:

SELECT ceil(57.3479) FROM funcionario WHERE primeiro_nome = 'joao'; --- que retornaria 58 anos redondo 

Iserindo essa informacão na tabela idade que acabou de ser criada o comando ficaria:

UPDATE funcionario SET idade ='58' WHERE primeiro_nome='joao';

Lembrando que esses dois comandos devem ser realizados para cada funcionário da tabela para saber a idade completa de cada um. Apos realizar os comandos basta juntar as tabelas do enunciado usando o 'inner join':

SELECT CONCAT(funcionario.primeiro_nome," ",funcionario.nome_meio," ",funcionario.ultimo_nome) AS "nome_completo", departamento.nome_departamento, funcionario.data_nascimento, funcionario.idade, funcionario.salario FROM departamento INNER JOIN funcionario ON departamento.numero_departamento = funcionario.numero_departamento;

#### Após isso o MARIADB trara uma projeção com todas as informaçõaes pedidas no enunciado.
 
# Questão 4: 

## Prepare um relatório que mostre o nome completo dos funcionários, a idade em anos completos, o salário atual e o salário com um reajuste que obedece ao seguinte critério: se o salário atual do funcionário é inferior a 35.000 o reajuste deve ser de 20%, e se o salário atual do funcionário for igual ou superior a 35.000 o reajuste deve ser de 15%.

#### Para a realização da questão é necessário criar uma coluna dentro da tabela funcionário para o salário reajustado dos funcionários, com:

ALTER TABLE funcionario ADD COLUMN salario_reajustado decimal(10,2);

Agora é preciso inserir as informaçães dentro da tabela criada usando o comando:

UPDATE funcionario SET salario_reajustado= CASE WHEN salario < 35000 THEN salario * 1.20 WHEN salario >= 35000 THEN salario *1.15 END;

Apenas lembrando que o UPDATE ele atualiza a tabela e o CASE cria condiçoes, nesse caso ele esta falando no comando que WHEN = "quando" o salário for menor que 35000 tera um reajuste de 20% e quando o salário for igual o maior que 35000 tera um reajuste de 15%, isso matematicamente vai direto para a tabela ja calculado. Agora realizando o resto do enunciado fazemos um SELECT CONCAT:

SELECT CONCAT(primeiro_nome," ",nome_meio," ",ultimo_nome) AS "nome_completo" , idade, salario, salario_reajustado FROM funcionarios;

e retornara projetada a tabela no MARIADB.

# Questao 5:

## Prepare um relatório que liste, para cada departamento, o nome do gerente e o nome dos funcionários. Ordene esse relatório por nome do departamento (em ordem crescente) e por salário dos funcionários (em ordem decrescente).

#### Primeiro pegamos os gerentes de cada departamento e fazemos isso com uma INNER JOIN especifica: chamando de primeira seleção

SELECT * FROM (SELECT CONCAT("Gerente do departamento", departamento.nome_departamento) AS nome_departamento,
CONCAT(funcionario.primeiro_nome," ", funcionario.nome_meio," ",funcionario.ultimo_nome) AS nome_completo_funcionario FROM departamento INNER JOIN funcionario ON departamento.numero_departamento = funcionario.numero_departamento WHERE cpf_gerente = cpf BY nome_departamento asc) AS gerente 

Após fazer a primeira seleção, vamos fazer a segunda que é pegar o cpf dos que nao são gerentes ficando:

SELECT * FROM(SELECT departamento.nome_departamento, CONCAT(funcionario.primeiro_nome," ",funcionario.nome_meio," ",funcionario.ultimo_nome) AS nome_completo_funcionario FROM departamento INNER JOIN funcionario ON departamento.numero_departamento = funcionario.numero_departamento WHERE NOT cpf_gerente = cpf ORDER BY salario desc) AS funcionario; 

Agora juntamos as duas seleções com o **UNION** ficando:

SELECT * FROM (SELECT CONCAT("Gerente do departamento", departamento.nome_departamento) AS nome_departamento,
CONCAT(funcionario.primeiro_nome," ", funcionario.nome_meio," ",funcionario.ultimo_nome) AS nome_completo_funcionario FROM departamento INNER JOIN funcionario ON departamento.numero_departamento = funcionario.numero_departamento WHERE cpf_gerente = cpf BY nome_departamento asc) AS gerente **UNION** <--- SELECT * FROM(SELECT departamento.nome_departamento, CONCAT(funcionario.primeiro_nome," ",funcionario.nome_meio," ",funcionario.ultimo_nome) AS nome_completo_funcionario FROM departamento INNER JOIN funcionario ON departamento.numero_departamento = funcionario.numero_departamento WHERE NOT cpf_gerente = cpf ORDER BY salario desc) AS funcionario; 


# Questao 6:

## Prepare um relatório que mostre o nome completo dos funcionários que têm dependentes, o departamento onde eles trabalham e, para cada funcionário, também liste o nome completo dos dependentes, a idade em anos de cada dependente e o sexo (o sexo NÃO DEVE aparecer como M ou F, deve aparecer como “Masculino” ou “Feminino”).

#### Nessa questão usamos todos os princípios q usamos nas questões anteriores porém com o LEFT JOIN ficando:

SELECT departamento.nome_departamento, CONCAT(funcionario.primeiro_nome, " ",funcionario.nome_meio, " ",funcionario.ultimo_nome) AS nome_completo_funcionario, CONCAT(dependente.nome_dependente, " ",funcionario.nome_meio, " ",funcionario.ultimo_nome) AS nome_completo_dependente, year(curdate()) - year(dependente.data_nascimento) AS idade_dependente, CASE dependente.sexo WHEN 'M' THEN 'Masculino' WHEN 'F' THEN 'Feminino' END AS sexo_dependente FROM dependente LEFT JOIN funcionario ON (dependente.cpf_funcionario=funcionario.cpf) INNER JOIN departamento ON (funcionario.numero_departamento=departamento.numero_departamento);


# Questao 7:

## Prepare um relatório que mostre, para cada funcionário que NÃO TEM dependente, seu nome completo, departamento e salário.

SELECT departamento.nome_departamento, CONCAT(funcionario.primeiro_nome, " ", funcionario.nome_meio, " ", funcionario.ultimo_nome) AS nome_completo_funcionario, funcionario.salario FROM funcionario LEFT JOIN dependente ON funcionario.cpf = dependente.cpf_funcionario INNER JOIN departamento ON funcionario.numero_departamento = departamento.numero_departamento WHERE dependente.cpf_funcionario IS null;

# Questao 8:

## Prepare um relatório que mostre, para cada departamento, os projetos desse departamento e o nome completo dos funcionários que estão alocados em cada projeto. Além disso inclua o número de horas trabalhadas por cada funcionário, em cada projeto.

SELECT CONCAT("Departamento ", departamento.numero_departamento, " ", departamento.nome_departamento) AS departamento_e_nome, projeto.nome_projeto, CONCAT(funcionario.primeiro_nome, " ", funcionario.nome_meio, " ", funcionario.ultimo_nome) AS nome_completo_funcionario, trabalha_em.horas FROM funcionario INNER JOIN departamento ON funcionario.numero_departamento = departamento.numero_departamento INNER JOIN projeto AS p ON departamento.numero_departamento = p.numero_departamento INNER JOIN trabalha_em ON funcionario.cpf = trabalha_em.cpf_funcionario INNER JOIN projeto ON trabalha_em.numero_projeto = projeto.numero_projeto WHERE funcionario.numero_departamento = projeto.numero_departamento GROUP BY departamento_e_nome, nome_projeto, nome_completo_funcionario, horas;


# Questao 9:

## Prepare um relatório que mostre a soma total das horas de cadaprojeto em cada departamento. Obs: o relatório deve exibir o nome do departamento, o nome do projeto e a soma total das horas

SELECT DISTINCT departamento.nome_departamento, projeto.nome_projeto, sum(trabalha_em.horas) AS soma_das_horas FROM (departamento, projeto, trabalha_em) INNER JOIN departamento AS d ON (projeto.numero_departamento = d.numero_departamento) INNER JOIN trabalha_em AS tb ON (projeto.numero_projeto = tb.numero_projeto) GROUP BY nome_projeto, nome_departamento;

# Questao 10:

## Prepare um relatório que mostre a média salarial dos funcionários de cada departamento.

Nesta questão o comando é o mesmo da questão 1 logo sendo:

select avg(salario) from funcionario;

# Questao 11: 

## Considerando que o valor pago por hora trabalhada em um projetoé de 50 reais, prepare um relatório que mostre o nome completo do funcionário, o nome do projeto e o valor total que o funcionário receberá referente às horas trabalhadas naquele projeto.

select concat(funcionario.primeiro_nome, " ", funcionario.nome_meio, " ", funcionario.ultimo_nome) as nome_completo_funcionario, projeto.nome_projeto, trabalha_em.horas, sum(trabalha_em.horas)*0.50 as recebimento_horas from (funcionario, projeto, trabalha_em) inner join departamento as d on (projeto.numero_departamento = d.numero_departamento) inner join trabalha_em as tb on (projeto.numero_projeto = tb.numero_projeto) where funcionario.cpf = trabalha_em.cpf_funcionario group by nome_completo_funcionario, nome_projeto, nome_departamento, horas;

# Questao 12: 

## Seu chefe está verificando as horas trabalhadas pelos funcionários nos projetos e percebeu que alguns funcionários, mesmo estando alocadas à algum projeto, não registraram nenhuma hora trabalhada. Sua tarefa é preparar um relatório que liste o nome do departamento, o nome do projeto e o nome dos funcionários que, mesmo estando alocados a algum projeto, não registraram nenhuma hora trabalhada.

select concat(funcionario.primeiro_nome, " ", funcionario.nome_meio, " ", funcionario.ultimo_nome) as nome_completo, departamento.nome_departamento, projeto.nome_projeto from (funcionario, departamento, projeto) inner join funcionario as f on (departamento.cpf_gerente = f.cpf) inner join projeto as p on (departamento.numero_departamento = p.numero_departamento) inner join trabalha_em as tb on (p.numero_projeto = tb.numero_projeto) where tb.horas = 0.0 and funcionario.cpf = tb.cpf_funcionario group by projeto.nome_projeto, departamento.nome_departamento, nome_completo;

# Questao 13: 

## Durante o natal deste ano a empresa irá presentear todos os funcionários e todos os dependentes (sim, a empresa vai dar um presente para cada funcionário e um presente para cada dependente de cada funcionário) e pediu para que você preparasse um relatório que listasse o nome completo das pessoas a serem presenteadas (funcionários e dependentes), o sexo e a idade em anos completos (para poder comprar um presente adequado). Esse relatório deve estar ordenado pela idade em anos completos, de forma decrescente.

select concat(funcionario.primeiro_nome, " ", funcionario.nome_meio, " ", funcionario.ultimo_nome) as nome_completo_funcionario, concat(dependente.nome_dependente, " ", funcionario.nome_meio, " ", funcionario.ultimo_nome) as nome_completo_dependente, funcionario.sexo as sexo_funcionario, dependente.sexo as sexo_dependente, concat(funcionario.idade, " anos") as idade_funcionario, concat(year(curdate())-year(dependente.data_nascimento)," anos") as idade_dependente from (funcionario, dependente) inner join dependente as d on (funcionario.cpf = d.cpf_funcionario) group by nome_completo_funcionario, nome_completo_dependente, idade_funcionario, idade_dependente order by idade_funcionario desc, idade_dependente desc;

# Questao 14:

## Prepare um relatório que exiba quantos funcionários cada departamento tem.

select concat("Departamento ", departamento.numero_departamento, " ", departamento.nome_departamento) as departamento, count(funcionario.cpf) as numero_de_funcionarios from (departamento, funcionario) inner join funcionario as f on (departamento.numero_departamento = f.numero_departamento) where funcionario.cpf = departamento.cpf_gerente group by departamento.numero_departamento;

# Questao 15:

## Como um funcionário pode estar alocado em mais de um projeto, prepare um relatório que exiba o nome completo do funcionário, o departamento desse funcionário e o nome dos projetos em que cada funcionário está alocado. Atenção: se houver algum funcionário que não está alocado em nenhum projeto, o nome completo e o departamento também devem aparecer no relatório.

select concat(f.primeiro_nome, " ", f.nome_meio, " ", f.ultimo_nome) as nome_completo_funcionario, concat("Departamento ", d.numero_departamento, " ", d.nome_departamento) as departamento, concat("Projeto N° ", p.numero_projeto, " ", p.nome_projeto) as projeto from (departamento as d) inner join projeto as p inner join trabalha_em as tb inner join funcionario as f where d.numero_departamento = f.numero_departamento and p.numero_projeto = tb.numero_projeto and tb.cpf_funcionario = f.cpf;






















