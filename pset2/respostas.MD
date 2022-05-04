# Questão 1 : 
## Prepare um relatório que mostre a média salarial dos funcionários de cada departamento.
### com o comando : "select avg(salario) from funcionario;" conseguimos ver a media salarial dos funcionários que é de: 35125.00

# Questão 2 :
## prepare um relatório que mostre a média salarial dos homens e das mulheres.
### com o comando : "select (sum(salario)/count(*)) as media from funcionario where sexo = 'F';" conseguimos ver a media salarial dos funcionarios mulheres da empresa onde a media salarial das mulheres é: 31.000,00.
### agora para saber a média salarial dos homens basta apenas trocar a variável de valor 'F' para 'M'. O comando ficará : "select (sum(salario)/count(*)) as media from funcionario where sexo = 'M';" e a média será : 37.600,00.


# Questões 3 :
## prepare um relatório que liste o nome dos departamentos e, para cada departamento, inclua as seguintes informações de seus funcionários: o nome completo, a data de nascimento, a idade em anos completos e o salário.
### 
 
