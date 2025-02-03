# PL/SQL - Procedural Language/Structured Query Language

é uma extensão procedural da linguagem SQL. Permite o dev criar blocos de código que contêm: lógica de controle, loops, condições, tratamento de exceções e outras construções de programação para manipular dados em BD.

- Estrutura do bloco (No caso o código):
    - Declaração: onde variáveis e constantes são definidas.
    ```SQL
    declare
    ```
    - Executável: contém as instruções SQL e de controle.
     ```SQL
    begin

    end;
    ```
    - Exceção: lida com erros que ocorrem durante a execução.
    ```SQL
    declare
    begin
    exception
        when others then
    end;
    ```


## Stored Procedure (Função, Método ou Procedimento)
É um procedimentos ou funções armazenadas no banco, que podem ser chamadas **manualmente** para realizar tarefas específicas.

## Trigger
O objeto trigger nada mais é do que um bloco de código PL/SQL armaze- nado no banco de dados e que é disparado **automaticamente** mediante uma ação. Este disparo pode ocorrer de duas formas, por intermédio de alterações feitas em registros de uma determinada tabela, neste caso se tratando do dis- paro de um trigger de banco de dados ou por ações em nível de sistema por um trigger de sistema

```SQL
create trigger tipo_tabela
  before delete or insert or update of sal on emp
begin
--codigo
end;
```

Cursores: Para manipulação de conjuntos de resultados, PL/SQL suporta cursores, que facilitam o processamento linha por linha de resultados de consultas.

Controle de Fluxo: Comandos como IF, LOOP, WHILE, entre outros, permitem adicionar lógica de controle aos scripts SQL.