### Aplicativo da Web Spring Boot CRUD com JDBC - Thymeleaf - Oracle

# 1. Visão Geral

Neste tutorial, vamos aprender a desenvolver um aplicativo da web Spring Boot que gerencia dados em um banco de dados Oracle. 
### 1.1. Aqui estão as principais tecnologias que serão usadas:

- Spring JDBC para acessar um banco de dados relacional (Oracle), via JdbcTemplate. Podemos usar Spring JDBC para operações CRUD simples em uma única tabela, em vez do Hibernate. Você verá como o Spring simplifica a programação com JDBC.
- Spring MVC para a camada do controlador, como o padrão para um aplicativo da web Spring.
Thymeleaf para a camada de visualização em vez do JSP clássico, pois o Thymeleaf é mais moderno, mais simples e fácil de usar do que JSP e JSTL.

Você verá como o Spring Boot conecta todas essas peças e simplifica muito o processo de codificação, e Spring JDBC é uma boa escolha para necessidades simples.

# 2. Tabela de conteúdo

### 2.2. Este aplicativo permite ao usuário gerenciar as vendas diárias de mercearia.

Tabela de conteúdo:
1. Crie o banco de dados Oracle
2. Crie o projeto Spring Boot no Eclipse
3. Especifique as propriedades de conexão do banco de dados
4. Classe de modelo de domínio de código
5. Classe de código DAO
6. Classe de controlador MVC do código Spring
7. Classe principal do aplicativo Code Spring Boot
8. Implementar Recurso de Lista de Vendas
9. Implementar o recurso de criação de venda
10. Implementar Editar / Atualizar Recurso de Venda
11. Implementar o recurso Excluir Venda
12. Empacote e execute nosso aplicativo da Web Spring Boot

# 3. Criar a tabela do banco de dados Oracle
Você pode executar a seguinte instrução na ferramenta SQL Developer para criar esta tabela:

```
CREATE TABLE "SALES" (
    "ID" NUMBER NOT NULL ENABLE,
    "ITEM" VARCHAR2(50 BYTE) NOT NULL ENABLE,
    "QUANTITY" NUMBER(*,0) NOT NULL ENABLE,
    "AMOUNT" FLOAT(126) NOT NULL ENABLE,
    CONSTRAINT "SALES_PK" PRIMARY KEY ("ID")
)
```

Como o Oracle não oferece suporte ao atributo de incremento automático para uma chave primária, precisamos criar uma sequência e um gatilho para que os valores da coluna ID possam ser gerados automaticamente.
Execute esta instrução para criar uma sequência:

```
CREATE SEQUENCE "SALE_SEQUENCE" MINVALUE 1 MAXVALUE 100000 INCREMENT BY 1 START WITH 1;
```

E execute o seguinte script Oracle SQL para criar um trigger:

```
CREATE TRIGGER "SYSTEM"."SALE_PRIMARY_KEY_TRG"
   before insert on "SYSTEM"."SALESABC"
   for each row
begin 
   if inserting then
      if :NEW."ID" is null then
         select SALE_SEQUENCE_ABC.nextval into :NEW."ID" from dual;
      end if;
   end if;
end;
```

Você pode usar o Oracle SQL Developer para criar a sequência e disparar com mais facilidade.

# 4. Implement List Sales Feature

O recurso lista todos os itens vendidos na página inicial. Portanto, escreva o código para o método list() da classe SalesDAO da seguinte maneira:

```
public List<Sale> list() {
    String sql = "SELECT * FROM SALES";
 
    List<Sale> listSale = jdbcTemplate.query(sql,
            BeanPropertyRowMapper.newInstance(Sale.class));
 
    return listSale;
}
```

O legal aqui é que o BeanPropertyRowMapper faz o mapeamento de valores de JDBC ResultSet para objetos Java. Você precisa ter certeza de que os nomes dos campos na classe Sale são iguais aos nomes das colunas na tabela.
Em seguida, codifique um método manipulador na classe AppController da seguinte maneira:

```
@RequestMapping("/")
public String viewHomePage(Model model) {
    List<Sale> listSale = dao.list();
    model.addAttribute("listSale", listSale);
    return "index";
}
```