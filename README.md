# Spring Security

Este é um projeto de demonstração utilizando o Spring Security para autenticação e autorização em uma aplicação Spring Boot.

## Requisitos

- Java 8 ou superior
- Maven

## Configuração

No arquivo `application.properties`, é possível definir as configurações de segurança da aplicação, incluindo nome de usuário, senha e papéis (roles) dos usuários. Por padrão, está configurado para um único usuário:

```properties
spring.application.name=spring-security

# IDEAL PARA APENAS 1 USUÁRIO
#spring.security.user.name=adriano
#spring.security.user.password=user123
#spring.security.user.roles=USERS
```


## Dependências Maven

O arquivo `pom.xml` contém as dependências Maven necessárias para o projeto, incluindo o Spring Boot Starter Security, Spring Boot Starter Web, Spring Boot Starter Test, entre outras.

## Configuração de Segurança

A classe `WebSecurityConfig` é responsável por configurar as regras de segurança da aplicação. Ela estende `WebSecurityConfigurerAdapter` e define as autorizações para diferentes URLs com base nos papéis dos usuários. Aqui está um trecho da configuração:

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
            .antMatchers("/").permitAll()
            .antMatchers(HttpMethod.POST, "/login").permitAll()
            .antMatchers("/managers").hasAnyRole("MANAGERS")
            .antMatchers("/users").hasAnyRole("USERS","MANAGERS")
            .anyRequest().authenticated().and().httpBasic();
}
```

## Controladores e Entidades

Existem controladores REST, como `WelcomeController`, que define endpoints como `/` (página inicial), `/users` e `/managers`. As entidades, como `User`, representam usuários da aplicação. Esses controladores e entidades são usados em conjunto com a segurança fornecida pelo Spring Security.

## Serviço de Dados de Segurança

A classe `SecurityDataBaseService` implementa `UserDetailsService` e é responsável por carregar os detalhes do usuário com base no nome de usuário fornecido durante o processo de autenticação. Aqui está um trecho do serviço:

```java
@Override
public UserDetails loadUserByUsername(String username) {
    User userEntity = userRepository.findByUsername(username);
    if (userEntity == null) {
        throw new UsernameNotFoundException(username);
    }

    Set<GrantedAuthority> authorities = new HashSet<GrantedAuthority>();
    userEntity.getRoles().forEach(role -> {
        authorities.add(new SimpleGrantedAuthority("ROLE_"+ role));
    });

    UserDetails user = new org.springframework.security.core.userdetails.User(
            userEntity.getUsername(),
            userEntity.getPassword(),
            authorities
    );

    return user;
}
```

## Execução

1. Clone este repositório:

    ```bash
    git clone https://github.com/seu-usuario/spring-security.git
    ```

2. Navegue até o diretório do projeto:

    ```bash
    cd spring-security
    ```

3. Compile o projeto:

    ```bash
    mvn clean package
    ```

4. Execute a aplicação:

    ```bash
    java -jar target/spring-security-0.0.1-SNAPSHOT.jar
    ```

## Notas

- Este projeto utiliza o Spring Boot Starter Security para configurar a segurança da aplicação.
- Certifique-se de revisar e personalizar as configurações de segurança conforme necessário para atender aos requisitos do seu projeto.