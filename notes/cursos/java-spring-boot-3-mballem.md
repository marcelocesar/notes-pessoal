# REST API com Spring Boot 3, Spring Security, Spring Data JPA, JWT, Swagger, Testes end2end, DTO

https://indra.udemy.com/course/api-rest-e-spring-boot-aprenda-do-zero-e-na-pratica

## 1 - Introdução

Ferramentas
- MySQL
- Postman
- IDE Intellij

##  2 - Configuração inicial do projeto

Criar o projeto utilizando o spring initializr ou pelo intellij

- Spring Initializr
https://start.spring.io/

- Timezone
Criar uma class SpringTimezoneConfig dentro da pasta config. Utilizar a anotação '@configuration'. Depois criar um método 'timezoneConfig', com anotação '@PostConstruct'.

```Java
package com.mballem.demoparkapi.config;

import jakarta.annotation.PostConstruct;
import org.springframework.context.annotation.Configuration;

import java.util.TimeZone;

@Configuration
public class SpringTimezoneConfig {

    @PostConstruct
    public void timezoneConfig() {
        TimeZone.setDefault(TimeZone.getTimeZone("America/Sao_Paulo"));
    }
}
```

- Locale PT-BR
Utilizar o arquivo de configuração do spring que fica dentro da pasta resource.

```md
# LOCALE
spring.mvc.locale-resolver=fixed
spring.mvc.locale=pt_BR
```

- Banco de dados 
O curso utiliza MySQL, mas pode ser Postgres. Crie um banco de dados ex: 'demo_park' e adiciona o charset UTF-8. Todas as tabelas serão criadas utilizando JPA.


```md
# MySQL Database Connection Properties
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver 
spring.datasource.url=jdbc:mysql://localhost:3306/demo_park?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=America/Sao_Paulo
spring.datasource.username=root
spring.datasource.password=root
````

```md
# JPA
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=false
spring.jpa.hibernate.ddl-auto=update
```

spring.jpa.show-sql: exibe o sql no console;

spring.jpa.properties.hibernate.format_sql: exibe os comandos sql numa linha, caso falso, caso true, exibe o comando completo;

spring.jpa.hibernate.ddl-auto: atualiza o banco, sempre que houver atualização no mapeamento de entidades;


## 3 - Nivelamento REST
-

## 4 - Recursos de Usuário (Mapeamento de Entity, Repository, Service e Controller)


### Entity Usuário

No mapeamento da entidade usuário foi utilizada a implementação 'jakarta.persistence.*'. Mas existem outras que fazer o mesmo papeu.

```Java
package com.mballem.demoparkapi.entity;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.io.Serializable;
import java.time.LocalDateTime;
import java.util.Objects;

@Getter @Setter @NoArgsConstructor
@Entity
@Table(name = "usuarios")
public class Usuario implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Long id;
    @Column(name = "username", nullable = false, unique = true, length = 100)
    private String username;
    @Column(name = "password", nullable = false, length = 200)
    private String password;
    @Enumerated(EnumType.STRING)
    @Column(name = "role", nullable = false, length = 25)
    private Role role = Role.ROLE_CLIENTE;

    @Column(name = "data_criacao")
    private LocalDateTime dataCriacao;
    @Column(name = "data_modificacao")
    private LocalDateTime dataModificacao;
    @Column(name = "criado_por")
    private String criadoPor;
    @Column(name = "modificado_por")
    private String modificadoPor;

    public enum Role {
        ROLE_ADMIN, ROLE_CLIENTE
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Usuario usuario = (Usuario) o;
        return Objects.equals(id, usuario.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }

    @Override
    public String toString() {
        return "Usuario{" +
                "id=" + id +
                '}';
    }
}
```

Obs:

**Anotações do Lombok**:
- *@Getter:* cria automanticamente os gets das propriedades.
- *@Setter:* cria automanticamente os sets das propriedades.
- *@NoArgsConstructor:* cria um método construtor sem argumentos.


**Anotaçoes do jarkata.persistence**
- @Entity: Essa anotação é usada para informar que a classe é um entidade de persistencia, ajuda o JPA a fazer a ligação com o BD.
- @Table(name = "usuarios"): Essa anotação é usada para especificar o nome da tabela no banco de dados
- @Id: Essa anotação marca o campo como a chave primária da entidade.
- @GeneratedValue(strategy = GenerationType.IDENTITY): Essa anotação é usada para definir como o valor da chave primária (marcada com @Id) será gerado. O parâmetro strategy define a estratégia de geração do valor. O GenerationType.IDENTITY indica que o banco de dados será responsável por gerar o valor da chave primária usando um campo auto-incremento.
- @Column(name = "id"): Essa anotação é usada para mapear um campo da classe Java para uma coluna específica no BD.
- @Enumerated(EnumType.STRING): Essa anotação é usada para mapear um campo que é um enum em Java para uma coluna no banco de dados. O parâmetro EnumType.STRING indica que o valor do enum será armazenado como uma string (nome do enum) no banco de dados. Outra opção seria EnumType.ORDINAL, que armazena o índice ordinal do enum (números).
- @Column(name = "username", nullable = false, unique = true, length = 100): A anotação @Column é usada para personalizar o mapeamento de um campo de entidade para uma coluna no banco de dados:
    1. name = "username"
    Isso define o nome da coluna no banco de dados para a qual o campo será mapeado. No caso, o campo username da entidade será mapeado para a coluna username no banco de dados.

    2. nullable = false
    Esse parâmetro define se a coluna pode ou não aceitar valores null. Quando nullable = false, a coluna não pode ter valores nulos; ou seja, o campo username sempre precisará ter um valor válido quando persistido no banco de dados.

    3. unique = true
    Define que o valor da coluna precisa ser único no banco de dados. Isso significa que dois registros diferentes não podem ter o mesmo valor para o campo username. O banco de dados criará uma restrição de unicidade para essa coluna.

    4. length = 100
    Especifica o tamanho máximo da coluna para armazenar dados do tipo String. Neste caso, o campo username terá um tamanho máximo de 100 caracteres. Se uma string com mais de 100


### Repository Usuário - (Camada Repository)

Em projetos Spring, uma classe Repository é usada para fornecer uma abstração de acesso aos dados e encapsular a lógica de interação com o banco de dados. No exemplo, a interface UsuarioRepository está estendendo a interface JpaRepository, o que faz parte do Spring Data JPA, um módulo do Spring que facilita a implementação de repositórios baseados em JPA.

```Java
package com.mballem.demoparkapi.repository;

import com.mballem.demoparkapi.entity.Usuario;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
}
```

**Interface Repository**
A interface UsuarioRepository não precisa implementar nenhum método diretamente. Ao estender JpaRepository, ela herda uma série de métodos prontos para realizar operações básicas de CRUD (Create, Read, Update, Delete) e outras operações de banco de dados. O Spring Data JPA gera automaticamente as implementações desses métodos.

#### Uso de JpaRepository
A interface JpaRepository<Usuario, Long> é um repositório específico para a entidade Usuario. Vamos entender os parâmetros:

- Usuario: Representa a entidade que este repositório vai gerenciar, ou seja, a tabela Usuario no banco de dados.
- Long: Tipo da chave primária (o identificador) da entidade Usuario. No caso, o campo id é do tipo Long.

Métodos Herdados de JpaRepository
Ao estender JpaRepository, a interface UsuarioRepository já possui diversos métodos implementados que você pode usar diretamente, sem precisar codificar:

- save(S entity): Salva ou atualiza a entidade no banco de dados.
- findById(ID id): Busca uma entidade pelo seu ID.
- findAll(): Retorna todas as entidades do tipo Usuario no banco de dados.
- deleteById(ID id): Exclui uma entidade pelo seu ID.
- count(): Retorna o número de entidades.

Personalização do Repositório
Embora você tenha métodos prontos com JpaRepository, você pode criar seus próprios métodos de consulta, apenas definindo o nome do método de acordo com o padrão de nomeação do Spring Data JPA. Por exemplo, se você quiser buscar um usuário pelo nome de usuário (username), pode adicionar o seguinte método na interface:

```Java
package com.mballem.demoparkapi.repository;

import com.mballem.demoparkapi.entity.Usuario;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    // Método personalizado para encontrar um usuário pelo username
    Usuario findByUsername(String username);
}
```


### Service Usuário - (Camada Service)

A classe UsuarioService é uma classe de serviço no Spring, responsável por encapsular a lógica de negócios relacionada à entidade Usuario. Ela usa o repositório UsuarioRepository para realizar operações de acesso aos dados, e também define regras de negócio, como validação de senhas. 

```Java
package com.mballem.demoparkapi.service;

import com.mballem.demoparkapi.entity.Usuario;
import com.mballem.demoparkapi.exception.EntityNotFoundException;
import com.mballem.demoparkapi.exception.PasswordInvalidException;
import com.mballem.demoparkapi.exception.UsernameUniqueViolationException;
import com.mballem.demoparkapi.repository.UsuarioRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@RequiredArgsConstructor
@Service
public class UsuarioService {

    private final UsuarioRepository usuarioRepository;

    @Transactional
    public Usuario salvar(Usuario usuario) {
        try {
            return usuarioRepository.save(usuario);
        } catch (org.springframework.dao.DataIntegrityViolationException ex) {
            throw new UsernameUniqueViolationException(String.format("Username '%s' já cadastrado", usuario.getUsername()));
        }
    }

    @Transactional(readOnly = true)
    public Usuario buscarPorId(Long id) {
        return usuarioRepository.findById(id).orElseThrow(
                () -> new EntityNotFoundException(String.format("Usuário id=%s não encontrado", id))
        );
    }

    @Transactional
    public Usuario editarSenha(Long id, String senhaAtual, String novaSenha, String confirmaSenha) {
        if (!novaSenha.equals(confirmaSenha)) {
            throw new PasswordInvalidException("Nova senha não confere com confirmação de senha.");
        }

        Usuario user = buscarPorId(id);
        if (!user.getPassword().equals(senhaAtual)) {
            throw new PasswordInvalidException("Sua senha não confere.");
        }

        user.setPassword(novaSenha);
        return user;
    }

    @Transactional(readOnly = true)
    public List<Usuario> buscarTodos() {
        return usuarioRepository.findAll();
    }
}


```

Obs:

**Anotações de Spring**
- @Service: Indica que essa classe é um serviço no Spring. O Spring gerencia a instância dessa classe e a torna disponível para injeção em outras partes da aplicação. Serviços geralmente contêm lógica de negócios.

- @RequiredArgsConstructor: Gera um construtor com base nos campos final da classe, ou seja, injeta automaticamente o UsuarioRepository por meio do construtor.

- @Transactional: Indica que os métodos anotados devem ser executados dentro de uma transação. Isso garante que as operações de banco de dados sejam realizadas de forma segura e atômica, ou seja, se algo der errado, a transação pode ser revertida.

- @Transactional(readOnly = true): Define que a operação não irá modificar os dados no banco, otimizando as consultas, já que nenhuma alteração será feita.

**Dependência: UsuarioRepository**
A classe usa o UsuarioRepository para realizar as operações de banco de dados. Como o campo usuarioRepository é marcado como final e a classe tem a anotação @RequiredArgsConstructor, o Spring injeta automaticamente essa dependência no construtor.


### Controller Usuário - (Camada Controller)

Um Controller é uma classe que lida com requisições HTTP. Ele serve como o ponto de entrada da sua aplicação para as interações do cliente, permitindo a comunicação entre o frontend e o backend.

```Java
package com.mballem.demoparkapi.web.controller;

import com.mballem.demoparkapi.entity.Usuario;
import com.mballem.demoparkapi.service.UsuarioService;
import com.mballem.demoparkapi.web.dto.UsuarioCreateDto;
import com.mballem.demoparkapi.web.dto.UsuarioResponseDto;
import com.mballem.demoparkapi.web.dto.UsuarioSenhaDto;
import com.mballem.demoparkapi.web.dto.mapper.UsuarioMapper;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RequiredArgsConstructor
@RestController
@RequestMapping("api/v1/usuarios")
public class UsuarioController {

    private final UsuarioService usuarioService;

    @PostMapping
    public ResponseEntity<UsuarioResponseDto> create(@Valid @RequestBody UsuarioCreateDto createDto) {
        Usuario user = usuarioService.salvar(UsuarioMapper.toUsuario(createDto));
        return ResponseEntity.status(HttpStatus.CREATED).body(UsuarioMapper.toDto(user));
    }

    @GetMapping("/{id}")
    public ResponseEntity<UsuarioResponseDto> getById(@PathVariable Long id) {
        Usuario user = usuarioService.buscarPorId(id);
        return ResponseEntity.ok(UsuarioMapper.toDto(user));
    }

    @PatchMapping("/{id}")
    public ResponseEntity<Void> updatePassword(@PathVariable Long id, @Valid @RequestBody UsuarioSenhaDto dto) {
        Usuario user = usuarioService.editarSenha(id, dto.getSenhaAtual(), dto.getNovaSenha(), dto.getConfirmaSenha());
        return ResponseEntity.noContent().build();
    }

    @GetMapping
    public ResponseEntity<List<UsuarioResponseDto>> getAll() {
        List<Usuario> users = usuarioService.buscarTodos();
        return ResponseEntity.ok(UsuarioMapper.toListDto(users));
    }
}

```

Obs:

**Anotações da Classe**
- @RestController: Indica que esta classe é um controlador REST, ou seja, processa requisições HTTP e retorna dados diretamente (geralmente em JSON ou XML).
- @RequestMapping("api/v1/usuarios"): Define o caminho base para todas as rotas da classe. Todas as requisições começando com "api/v1/usuarios" serão tratadas por essa classe.
- @RequiredArgsConstructor: Gera um construtor para as dependências final, injetando automaticamente o UsuarioService.

**Métodos do Controller**

create(@Valid @RequestBody UsuarioCreateDto createDto)
- @PostMapping: Mapeia requisições HTTP POST para criar um novo usuário.
Recebe um DTO de criação de usuário (UsuarioCreateDto), valida os dados com @Valid, e converte para a entidade Usuario usando o UsuarioMapper.
O serviço UsuarioService salva o usuário e retorna uma resposta HTTP com status 201 (CREATED) e o DTO de resposta (UsuarioResponseDto).

getById(@PathVariable Long id)
- @GetMapping("/{id}"): Mapeia requisições HTTP GET para buscar um usuário por ID.
O id é capturado da URL (@PathVariable).
O serviço UsuarioService busca o usuário e retorna seu DTO de resposta.

updatePassword(@PathVariable Long id, @Valid @RequestBody UsuarioSenhaDto dto)
- @PatchMapping("/{id}"): Mapeia requisições HTTP PATCH para atualizar parcialmente o recurso (neste caso, a senha).
Recebe um DTO contendo as senhas e chama o serviço editarSenha para aplicar a alteração.
Retorna uma resposta sem conteúdo (HTTP 204 No Content) se a operação for bem-sucedida.

getAll()
- @GetMapping: Mapeia requisições HTTP GET para listar todos os usuários.
Usa o serviço para buscar a lista de usuários e retorna os dados em forma de lista de DTOs.



## 5 - Refectore DTO (Data Transfer Object)

Um DTO (Data Transfer Object) é um objeto usado para transportar dados entre diferentes partes de uma aplicação, como entre o backend e o frontend ou entre serviços. Ele contém apenas atributos simples (sem lógica de negócios) e serve para desacoplar as camadas de apresentação, negócios e persistência, evitando o envio de informações desnecessárias ou sensíveis. 

**Biblitoecas de suporte a DTO**

MapStruct: O MapStruct é uma biblioteca de mapeamento de objetos que pode gerar automaticamente código para mapear entidades para DTOs e vice-versa. Ele usa anotações para configurar o mapeamento e gera o código necessário durante o processo de compilação. Isso elimina a necessidade de escrever manualmente o código de mapeamento entre as classes.
https://mapstruct.org/

ModelMapper: O ModelMapper é outra biblioteca de mapeamento de objetos que permite a configuração de regras de mapeamento entre classes. Ele também pode ser usado para mapear automaticamente entidades para DTOs e vice-versa, eliminando a necessidade de escrever código de mapeamento manualmente.
https://modelmapper.org/

Adicionando o ModelMapper no pom.xml

```xml
<dependency>
  <groupId>org.modelmapper</groupId>
  <artifactId>modelmapper</artifactId>
  <version>3.0.0</version>
</dependency>
```

Exemplo de como utilizar o ModelMapper com DTO. Na classe UsuarioController o método create recebe um UsuarioCreateDto, faz um mapper do DTO para Usario, depois retorna um UsuarioResponseDto para o cliente.

**UsuarioController**
```Java
    @PostMapping
    public ResponseEntity<UsuarioResponseDto> create(@Valid @RequestBody UsuarioCreateDto createDto) {
        Usuario user = usuarioService.salvar(UsuarioMapper.toUsuario(createDto));
        return ResponseEntity.status(HttpStatus.CREATED).body(UsuarioMapper.toDto(user));
    }
```

**UsuarioCreateDto**
```Java
package com.mballem.demoparkapi.web.dto;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;
import lombok.*;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class UsuarioCreateDto {

    @NotBlank
    @Email(message = "formato do e-mail está invalido", regexp = "^[a-z0-9.+-]+@[a-z0-9.-]+\\.[a-z]{2,}$")
    private String username;
    @NotBlank
    @Size(min = 6, max = 6)
    private String password;
}

```

A anotação ```@Valid``` no argumento do metodo create no UsuarioController, faz a validação do campos com as anotações: @NotBlank, @Email, @Size, etc. Visualizar item 6.

**UsuarioResponseDto**
```Java
package com.mballem.demoparkapi.web.dto;

import lombok.*;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class UsuarioResponseDto {

    private Long id;
    private String username;
    private String role;
}

```

Classe UsuarioMapper que faz o parse do Usuario para DTO ou vice versa.

**UsuarioMapper**
```Java
package com.mballem.demoparkapi.web.dto.mapper;

import com.mballem.demoparkapi.entity.Usuario;
import com.mballem.demoparkapi.web.dto.UsuarioCreateDto;
import com.mballem.demoparkapi.web.dto.UsuarioResponseDto;
import org.modelmapper.ModelMapper;
import org.modelmapper.PropertyMap;

import java.util.List;
import java.util.stream.Collectors;

public class UsuarioMapper {

    public static Usuario toUsuario(UsuarioCreateDto createDto) {
        return new ModelMapper().map(createDto, Usuario.class);
    }

    public static UsuarioResponseDto toDto(Usuario usuario) {
        String role = usuario.getRole().name().substring("ROLE_".length());
        PropertyMap<Usuario, UsuarioResponseDto> props = new PropertyMap<Usuario, UsuarioResponseDto>() {
            @Override
            protected void configure() {
                map().setRole(role);
            }
        };
        ModelMapper mapper = new ModelMapper();
        mapper.addMappings(props);
        return mapper.map(usuario, UsuarioResponseDto.class);
    }

    public static List<UsuarioResponseDto> toListDto(List<Usuario> usuarios) {
        return usuarios.stream().map(user -> toDto(user)).collect(Collectors.toList());
    }
}
```
## 6 - Validação com Bean Validation

O Spring suporta a especificação de validação Jakarta Bean Validation, que permite adicionar anotações de validação nos campos dos objetos de entrada das APIs. Você pode usar anotações como @Valid, @NotNull, @Size, @NotBlank, entre outras, para definir as restrições de validação. Essas anotações podem ser adicionadas aos parâmetros dos métodos dos controladores ou aos campos dos objetos de entrada dos modelos.


```Java
@PostMapping("/users")
public ResponseEntity<User> create(@Valid @RequestBody User user) {    
    // ...
}
```

O Spring também fornece uma API de validação nativa que pode ser utilizada para realizar validações customizadas. Com a Spring Validator você pode criar classes de validação personalizadas que implementam a interface org.springframework.validation.Validator e definir as regras de validação no método validate(). Em seguida, você pode usar o Validator nos seus controllers para validar os objetos de entrada.
https://beanvalidation.org/3.0/


```Java
@Component
public class UserValidator implements Validator {    
    @Override    
    public boolean supports(Class<?> clazz) {        
        return User.class.isAssignableFrom(clazz);    
    }
 
    @Override    
    public void validate(Object target, Errors errors) {        
        User user = (User) target;        
        if (user.getName() == null || user.getName().isEmpty()) {            
            errors.rejectValue("name", "NotEmpty", "Name cannot be empty");        
        }    
    }
}
 
@RestControllerpublic class User {    
    @PostMapping("/api/users")   
    public ResponseEntity<User> create(@Validated @RequestBody User user, Errors errors) {        
        if (errors.hasErrors()) {            
            // Handle validation errors        
        }        
        // ...    
    }
}
```

Adiciona a dependencia no pom.xml

```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
```

```Java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class UsuarioCreateDto {

    @NotBlank
    @Email(message = "formato do e-mail está invalido", regexp = "^[a-z0-9.+-]+@[a-z0-9.-]+\\.[a-z]{2,}$")
    private String username;
    @NotBlank
    @Size(min = 6, max = 6)
    private String password;
}

```

A anotação ```@Valid``` no argumento do metodo create no UsuarioController, faz a validação do campos com as anotações: @NotBlank, @Email, @Size, etc. 
https://beanvalidation.org/3.0/
Na documentação tem mais opçao para validação dos campos.

## 7 - Tratamento de exceção com RestControllerAdvice

## 8 - Documentação comSpring docs, OpenAPI e Swagger

Acessar a documentação:
https://springdoc.org/

Adicionar a dependência ao projeto no pom.xml.

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.6.0</version>
</dependency>
```

Adicionar as configurações no arquivo ```application.properties```.

```md
# Springdoc OpenApi 3.1 & Swagger 3
springdoc.swagger-ui.path=/docs-park.html
springdoc.api-docs.path=/docs-park
springdoc.packagesToScan=com.mballem.demoparkapi.web.controller
```

Criar a classe de configuração ```SpringDocOpenApiConfig``` e adicionar as propriedades da API doc.

```Java
package com.mballem.demoparkapi.config;

import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Contact;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringDocOpenApiConfig {

    @Bean
    public OpenAPI openAPI() {
        return new OpenAPI()
                .info(
                        new Info()
                                .title("REST API - Spring Park")
                                .description("API para gestão de estacionamento de veículos")
                                .version("v1")
                                .license(new License().name("Apache 2.0").url("https://www.apache.org/licenses/LICENSE-2.0"))
                                .contact(new Contact().name("Marcio Ballem").email("marcio@spring-park.com"))
                );
    }
}

```
