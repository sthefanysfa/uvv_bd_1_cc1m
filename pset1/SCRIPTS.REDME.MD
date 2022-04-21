* ## COMO EU FIZ OS SCRIPTS DO MARIADB E DO POSTGRESQL?? 

* ## NO MARIADB:

##### 1-ENTREIO NO TERMINAL E ACESSANDO O MARIADB COM mysql -u root -p E COLOCANDO A SENHA
##### 2-LOGO DEPOIS CRIEI MEU USUÁRIO USANDO create user 'sthefany@localhost' indentified by 'senha'
##### 3-DEI OS PRIVILEGIOS AO USUARIO USANDO grant privileges on *.* to sthefany@localhost
##### 4-ENTREI NO MEU USUARIO COM mysql -u sthefany -p E COLOCQUEI A SENHA
##### 5-E FINALIZANDO CRIEI O BANCO DE DADOS QUE ESTA NO SCRIP DENTRO DO SUBDIRETORIO "SCRIPT MARIADB"

* ## NO POSTGRES:

##### 1-ENTREI NO TERMINAL COM psql -U postgres -W
##### 2-CRIEI MEU USUARIO COM create user sthefany with password 'senha'
##### 3-FIZ O alter user sthefany createdb
##### 4-ENTREI COM psql -U sthefany postgres -W
##### 5-CRIEI O BANCO DE DADOS COM AS INSTRUÇÕES DP PSET:
        CREATE database uvv 
        WITH
        owner = STHEFANY
        encoding = 'UTF8'
        template = template0
        LC_COLLATE = 'pt_BR.UTF-8'
        LC_CTYPE = 'pt_BR.UTF-8'
        ALLOW_CONNECTIONS = TRUE;
##### 6-ENTREI NO BANCO DE DADOS COM \c
##### 7- CRIEI O SCHEMA ELMASRI COM create schema if not exist elmasri authorization sthefany;
##### 8-DEI OS PRIVILEGIOS AO USUÁRIO COM grant all privileges on all tables in schema elmasri to sthefany;
##### 9-DEI O set search_path to elmasri, "$user", public;
##### 10-E CRIEI AS TABELAS DE ACORDO COM O SCRIPT DE POSTGRES DENTRO DO SUBDIRETÓRIO "SCRIPT POSTGRES"
