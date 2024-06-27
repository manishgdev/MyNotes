### Spring and Spring Boot
## Spring 
Spring is a comprehensive framework that provides various modules for building different types of applications. It's like having a giant toolbox with every tool you could possibly need to build anything you want.

This versatility comes at a cost. Setting up a Spring application requires a lot of configuration, and developers need to manually configure various components of the framework to get an application up and running.

### Spring Boot
Fortunately, Spring Boot makes working with Spring much simpler. Spring Boot is like a more opinionated version of Spring. It comes with many pre-configured settings and dependencies that are commonly used in Spring applications. This makes it really easy to get started quickly, without having to worry about setting up everything from scratch. Plus, Spring Boot comes with an embedded web server, so you can easily create and deploy web applications without needing an external server.

### Spring's Inversion of Control Container
Inversion of control is often called dependency injection (DI), though this is not strictly correct. Dependency injection and accompanying frameworks are one way of achieving inversion of control, and Spring developers will often state that dependencies are "injected" into their applications at runtime. We're making this distinction clear because many software languages and frameworks implement IoC, but don't necessarily call it "dependency injection." However, within the Spring community, you'll often hear the terms used interchangeably.

### REST
REST: Representational State Transfer. In a RESTful system, data objects are called Resource Representations. The purpose of a RESTful API (Application Programming Interface) is to manage the state of these Resources.

Said another way, you can think of “state” being “value” and “Resource Representation” being an “object” or "thing". Therefore, REST is just a way to manage the values of things. Those things might be accessed via an API, and are often stored in a persistent data store, such as a database.

### CRUD
CRUD stands for “Create, Read, Update, and Delete”. These are the four basic operations that can be performed on objects in a data store.

The components of the Request and Response are:

**Request**
<ul>
<li>Method (also called Verb)</li>
<li>URI (also called Endpoint)</li>
<li>Body</li>
</ul>

**Response**
<ul>
<li>Status Code</li>
<li>Body</li>
</ul>

Method Types
<ul>
<li>For CREATE: use HTTP method POST.</li>
<li>For READ: use HTTP method GET.</li>
<li>For UPDATE: use HTTP method PUT.</li>
<li>For DELETE: use HTTP method DELETE.</li>
</ul>

### Spring Annotations and Component Scan
One of the main things Spring does is to configure and instantiate objects. These objects are called Spring Beans, and are usually created by Spring (as opposed to using the Java new keyword). We can direct Spring to create Beans in several ways.

### Controller-Repository Architecture
A common architectural framework that divides these layers, typically by function or value, such as business, data, and presentation layers, is called Layered Architecture. In this regard, we can think of our Repository and Controller as two layers in a Layered Architecture. The Controller is in a layer near the Client (as it receives and responds to web requests) while the Repository is in a layer near the data store (as it reads from and writes to the data store). There may be intermediate layers as well, as dictated by business needs. We don't need any additional layers, at least not yet!

The Repository is the interface between the application and the database, and provides a common abstraction for any database, making it easier to switch to a different database when needed

### Idempotence and HTTP
An **idempotent** operation is defined as one which, if performed more than once, results in the same outcome. In a REST API, an idempotent operation is one that even if it were to be performed several times, the resulting data on the server would be the same as if it had been performed only once.

For each method, the **HTTP** standard specifies whether it is idempotent or not. GET, PUT, and DELETE are idempotent, whereas POST and PATCH are not.

```
return  ResponseEntity
        .created(uriOfCashCard)
        .build();

```
is equivalent to 

```
return ResponseEntity
        .status(HttpStatus.CREATED)
        .header(HttpHeaders.LOCATION, uriOfCashCard.toASCIIString())
        .build();
```

### Pagination and Sorting
We have a specialized version of the `CrudRepository`, called the `PagingAndSortingRepository`

#### Spring Data Pagination API
Thankfully, Spring Data provides the PageRequest and Sort classes for pagination. Let’s look at a query to get page 2 with page size 10, sorting by amount in descending order (largest amounts first):

```
Page<CashCard> page2 = cashCardRepository.findAll(
    PageRequest.of(
        1,  // page index for the second page - indexing starts at 0
        10, // page size (the last page might have fewer items)
        Sort.by(new Sort.Order(Sort.Direction.DESC, "amount"))));

```
<ul>
<li> Pagination: Spring can parse out the page and size parameters if you pass a Pageable object to a PagingAndSortingRepository find…()method.</li>
<li>Sorting: Spring can parse out the sort parameter, consisting of the field name and the direction separated by a comma – but be careful, no space before or after the comma is allowed! Again, this data is part of the Pageable object.</li>
</ul>

```
@GetMapping
private ResponseEntity<List<CashCard>> findAll(Pageable pageable) {
   Page<CashCard> page = cashCardRepository.findAll(
           PageRequest.of(
                   pageable.getPageNumber(),
                   pageable.getPageSize(),
                   pageable.getSortOr(Sort.by(Sort.Direction.DESC, "amount"))));
   return ResponseEntity.ok(page.getContent());
}
```

Let’s dive into a bit more detail:

<ul>
<li>First let’s parse the needed values out of the query string:
<ul>
<li>We use Pageable, which allows Spring to parse out the page number and size query string parameters.</li>
<li>Note: If the caller doesn’t provide the parameters, Spring provides defaults: page=0, size=20.</li>
</ul>
<li>We use getSortOr() so that even if the caller doesn’t supply the sort parameter, there is a default. Unlike the page and size parameters, for which it makes sense for Spring to supply a default, it wouldn’t make sense for Spring to arbitrarily pick a sort field and direction.</li>
<li>We use the page.getContent() method to return the Cash Cards contained in the Page object to the caller.</li>
</ul>

JSON would like below
```
{
  "content": [
    {
      "id": 1,
      "amount": 10.0
    },
    {
      "id": 2,
      "amount": 0.19
    }
  ],
  "pageable": {
    "sort": {
      "empty": false,
      "sorted": true,
      "unsorted": false
    },
    "offset": 3,
    "pageNumber": 1,
    "pageSize": 3,
    "paged": true,
    "unpaged": false
  },
  "last": true,
  "totalElements": 5,
  "totalPages": 2,
  "first": false,
  "size": 3,
  "number": 1,
  "sort": {
    "empty": false,
    "sorted": true,
    "unsorted": false
  },
  "numberOfElements": 2,
  "empty": false
}
```


### Response Code
- 200 - OK
- 201 - Created
- 400 - Bad Gateway/Bad Request
- 404 - Not Found
- 405 - Method Not Allowed
- 417 - Data not valid / validation failed

## Spring Security


### Authentication
A user of an API can actually be a person or another program, so often we use the term **Principal** as a synonym for “user”. Authentication is the act of a Principal proving its identity to the system. One way to do this is to provide credentials (e.g. a username and password using Basic Authentication). We say that once the proper credentials have been presented, the Principal is authenticated, or in other words, the user has successfully logged in.

HTTP is a stateless protocol, so each request must contain data that proves it’s from an authenticated Principal. Although it’s possible to present the credentials on every request, doing so is inefficient because it requires more processing on the server. Instead, an Authentication Session (or Auth Session, or just Session) is created when a user gets authenticated. Sessions can be implemented in many ways. We’ll use a common mechanism: A Session Token (a string of random characters) that is generated, and placed in a Cookie. A Cookie is a set of data stored in a web client (such as a browser), and associated with a specific URI.

A couple of nice things about Cookies:

- Cookies are automatically sent to the server with every request (no extra code needs to be written for this to happen). As long as the server checks that the Token in the Cookie is valid, unauthenticated requests can be rejected.
- Cookies can persist for a certain amount of time even if the web page is closed and later re-visited. This ability typically improves the user experience of the web site.
Spring Security and Authentication
Spring Security implements authentication in the Filter Chain. The Filter Chain is a component of Java web architecture which allows programmers to define a sequence of methods that get called prior to the Controller. Each filter in the chain decides whether to allow request processing to continue, or not. Spring Security inserts a filter which checks the user’s authentication and returns with a 401 UNAUTHORIZED response if the request is not authenticated.

#### Authorization
Authorization happens after authentication, and allows different users of the same system to have different permissions.

Spring Security provides Authorization via **Role-Based Access Control (RBAC)**. This means that a Principal has a number of Roles. Each resource (or operation) specifies which Roles a Principal must have in order to perform actions with proper authorization. For example, a user with an Administrator Role is likely to be authorized to perform more actions than a user with a Card Owner Role. You can configure role-based authorization at both a global level and a per-method basis.

#### Same Origin Policy
The web is a dangerous place, where bad actors are constantly trying to exploit security vulnerabilities. The most basic mechanism of protection relies on HTTP clients and servers implementing the **Same Origin Policy (SOP)**. This policy states that only scripts which are contained in a web page are allowed to send requests to the origin (URI) of the web page.

SOP is critical to the security of web sites because without the policy, anyone could write a web page containing a script which sends requests to any other site. For example, let’s look at a typical banking web site. If a user is logged into their bank account and visits a malicious web page (in a different browser tab or window), the malicious requests could be sent (with the Auth Cookies) to the banking site. This could result in unwanted actions–like a withdrawal from the user’s bank account!

#### Cross-Origin Resource Sharing
Sometimes a system consists of services running on several machines with different URIs (i.e. Microservices). Cross-Origin Resource Sharing (CORS) is a way that browsers and servers can cooperate to relax the SOP. A server can explicitly allow a list of “allowed origins” of requests coming from an origin outside the server’s.

Spring Security provides the @CrossOrigin annotation, allowing you to specify a list of allowed sites. Be careful! If you use the annotation without any arguments, it will allow all origins, so bear this in mind!

#### Common Web Exploits
Along with exploiting known security vulnerabilities, malicious actors on the web are also constantly discovering new vulnerabilities. Thankfully, Spring Security provides a powerful tool set to guard against common security exploits. Let’s discuss two common exploits, how they work, and how Spring Security helps to mitigate them.

- **Cross-Site Request Forgery** 
One type of vulnerability is a Cross-Site Request Forgery (CSRF) which is often pronounced “Sea-Surf”, and also known as Session Riding. Session Riding is actually enabled by Cookies. CSRF attacks happen when a malicious piece of code sends a request to a server where a user is authenticated. When the server receives the Authentication Cookie, it has no way of knowing if the victim sent the harmful request unintentionally.

  To protect against CSRF attacks, you can use a CSRF Token. A CSRF Token is different from an Auth Token because a unique token is generated on each request. This makes it harder for an outside actor to insert itself into the “conversation” between the client and the server.

  Thankfully, Spring Security has built-in support for CSRF tokens which is enabled by default. You’ll learn more about this in the upcoming lab.

- **Cross-Site Scripting**
  Perhaps even more dangerous than CSRF vulnerability is Cross-Site Scripting (XSS). This occurs when an attacker is somehow able to “trick” the victim application into executing arbitrary code. There are many ways to do this. A simple example is saving a string in a database containing a `<script>` tag, and then waiting until the string is rendered on a web page, resulting in the script being executed.

  XSS is potentially more dangerous than CSRF. In CSRF, only actions that a user is authorized to do can be executed. However in XSS, arbitrary malicious code executes on the client or on the server. Additionally, XSS attacks don’t depend on Authentication. Rather, XSS attacks depend on security “holes” caused by poor programming practices.

  The main way to guard against XSS attacks is to properly process all data from external sources (like web forms and URI query strings). In the case of our `<script>` tag example, attacks can be mitigated by properly escaping the special HTML characters when the string is rendered.


### Annotations
- `@DirtiesContext`

- `@Configuration`

- `@Bean`

- `@Service`

- `@RestController`

- `@GetMapping`

If packages are outside the Application root package 
- `@ComponentScan`
- `@EnableJpaRepositories`
- `@EntityScan`

### Validation Annotations
- `@NotEmpty`
- `@Pattern`
- `@Size`
- `@Email`
- `@Validated`

- `@Valid` jakarta

### Lombok
- `@Getter`
- `@Setter`
- `@ToString`
- `@AllArgsConstructor`
- `@NoArgsConstructor`
- `@Data`

### Configurations
- `@Value`

### Spring Cloud Config
- `@EnableConfigServer`

We Can store the configs using 3 ways
1. Classpath
mention below in Config server's property and place the configs for individual microservices under `src/main/resources` folder
```yml
spring:
  profiles:
    active: native
  cloud:
    config:
      server:
        native:
          search-locations: "classpath:/config"
```

In client we can fetch the configs from the Configs server as below
```yml
spring:
  config:
    import: "configserver:http://localhost:8071"
```

`import: "optional:configserver:http://localhost:8071"` the optional keyword will make the Client app start even if the host for configserver is not found

2. Local File System
In the Config server
```yml
spring:
  cloud:
    config:
      server:
        native:
          search-locations: "file:///D:/Code/SpringBootProjects/2024/Microservices/section6/v2-spring-cloud-config/config"
```

3. Github
```yml
spring:
  profiles:
    active: git
  cloud:
    config:
      server:
        git:
          uri: "https://github.com/manishgdev/spring-cloud-config.git"
          default-label: main
          timeout: 5
          clone-on-start: true
          force-pull: true
```

### JPA Annotations
- `@MappedSuperClass`
- `@Column`
- `@Entity`
- `@Table`
- `@ID`
- `@Column`
- `@CreatedDate`
- `@CreatedBy`
- `@LastModifiedDate`
- `@LastModifiedBy`
- `@GeneratedValue`
- `@EntityListeners`
- `@EnableJpaAuditing`
- `@Transactional`
- `@Modifying` - used in conjunction with @Transactional, the annotation lets the Spring know that the method is going to update state of table so do it in transaction

- derived name method to define method names mapped with some condition in repository class

- springdoc-openapi

### DTO (Data Transfer Object) Pattern
An object that carries data between processes in order to reduce the number of method calls.  
https://martinfowler.com/eaaCatalog/dataTransferObject.html


** Controller - Service Layer - Dto - Entity - DtoEntityMapper **

### Accessing Configs
- @Value
- Environment class autowired
- @ConfigurationProperties and link with config in the application config file
- Profiles
```yml
spring:  
  config:
    import:
      - "application_qa.yml"
      - "application_prod.yml"
  profiles:
    active:
      - "qa"
```
- Spring Cloud Config
```yml
spring:
  application:
    name: "configserver"
  profiles:
    active: git
  cloud:
    config:
      server:
        git:
          uri: "https://github.com/manishgdev/spring-cloud-config.git"
          default-label: main
          timeout: 5
          clone-on-start: true
          force-pull: true
```

#### Updating Configs at runtime
1. Using Spring Actuator
```yml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```
above will expose all the actuator end-points and can be seen by hitting <BaseURL>/actuator, there will be one `/actuator/refresh` end-point which can be used to refresh the configs of the micro-service at runtime

2. Spring Cloud Bus
- Uses RabbitMQ \
`docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management`
- Enable bus refresh in Actuator
```yml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```
- Add RabbitMQ details
```yml
spring:
  rabbitmq:
    host: "localhost"
    port: 5672
    username: "guest"
    password: "guest"
```
- Change config and hit `/actuator/busrefresh` on any one of the micro-service, it will trigger the event on rabbitmq and the all the other microservices will get refreshed

3. Spring Cloug Config Monitor
`/monitor` path is exposed by the library, whenever a change happens in github, it will trigger the `/monitor` end-point with the help of webhooks and other microservices will update the configs automatically\

Using hookdeck
```
C:\Users\User>hookdeck login
Press Enter to open the browser (^C to quit)
> The Hookdeck CLI is configured for Manish Singh (manish4learn@gmail.com) on project Default Project in organization singman


C:\Users\User>hookdeck listen 8071 source_github
? What path should the events be forwarded to (ie: /webhooks)? /monitor
? What's your connection label (ie: My API)? localhost

Dashboard
👉 Inspect and replay events: https://dashboard.hookdeck.com?team_id=tm_JlrFWjeoplY7

source_github Source
🔌 Event URL: https://hkdk.events/0go4gfaaeyj83j

Connections
localhost forwarding to /monitor

> Ready! (^C to quit)
2024-06-17 21:16:18 [200] POST http://localhost:8071/monitor | https://dashboard.hookdeck.com/cli/events/evt_PdHuMD1Vpy9tY6aqWP
```

### Spring Security
- Create SecurityConfig
- Add FilterChain
```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    return http.build();
}
```
- Build User Details Configuration to fetch User's credentials
```java
@Bean
UserDetailsService testOnlyUsers(PasswordEncoder passwordEncoder) {
    User.UserBuilder users = User.builder();
    UserDetails sarah = users
            .username("sarah1")
            .password(passwordEncoder.encode("abc123"))
            .roles() // No roles for now
            .build();
    return new InMemoryUserDetailsManager(sarah);
}
```
- Enable Roble Based Authentication (RBAC)
```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
            .authorizeHttpRequests(request -> request
                    .requestMatchers("/cashcards/**")
                    .hasRole("CARD-OWNER")) // enable RBAC: Replace the .authenticated() call with the hasRole(...)
                                            // call.
            .httpBasic(Customizer.withDefaults())
            .csrf(csrf -> csrf.disable());
    return http.build();
}
```


### PUT, PATCH, POST

#### PATCH (Update)
update only some fields of the existing record” - in other words, a partial update.

#### PUT (Create & Update)
- create or replace the complete record
- when the resource URI is known at creation time (as is the case in our example Invoice API), you can use PUT.

For the Invoice API, we could write a Create endpoint that accepts requests such as PUT `/invoice/1234-567`. The corresponding Read call would use the exact same URI: GET `/invoice/1234-567`.

- PUT creates or replaces (updates) a resource at a specific request URI. For the /invoice example above, the Create endpoint would be PUT /invoice/1234-567, and the URI of the created resource would be the same as the URI sent in the PUT request.

#### POST (Create)
- If you need the server to return the URI of the created resource (or the data you use to construct the URI), then you must use POST.

To create a Cash Card, we provide the POST /cashcards endpoint. The actual URI for the created Cash Card depends on the generated ID, and is provided by the server, for example, /cashcards/101 if the ID of the created card is 101.

- POST creates a sub-resource (child resource) under (after), or within the request URI. This is what the Cash Card API does: The client calls the Create endpoint at POST /cashcards, but the actual URI of the created resource contains a generated ID at the end: /cashcards/101

Response Code	Use Case
- 201 - CREATED
  - Used in PUT, POST
- 204 - NO CONTENT	
  - The record exists, and
  - The Principal is authorized, and
  - The record was successfully deleted.
  - Used in PUT, DELETE
- 404 - NOT FOUND	
  - The record does not exist (a non-existent ID was sent).
- 404 - NOT FOUND	
  - The record does exist but the Principal is not the owner.

### Right sizing the microservices
- Domain Driven sizing
- Event storming sizing - Events, Commands & Reactions

### Generating Docker Images :
- Dockerfile  - docker build -t manishgdev/accounts:s4 .
- Buildpacks  -  PS D:\Code\SpringBootProjects\2024\Microservices\section4\loans> mvn spring-boot:build-image

- Google Jib
   <plugin>
      <groupId>com.google.cloud.tools</groupId>
      <artifactId>jib-maven-plugin</artifactId>
      <version>3.3.2</version>
      <configuration>
        <to>
          <image>myimage</image>
        </to>
      </configuration>
    </plugin>

    `mvn compile jib:dockerBuild`

```
docker build . -t manishgdev/account:s4
docker images
  REPOSITORY           TAG       IMAGE ID       CREATED         SIZE
  manishgdev/account   s4        1b30aa32dd7a   3 minutes ago   464MB
  redis                latest    09f7281d20fa   2 weeks ago     117MB
  redis                4.0       191c4017dcdd   4 years ago     89.3MB
>docker inspect 1b30aa32dd7a
>docker run -p 8080:8080 manishgdev/account:s4
run in detached mode
>docker run -d -p 8080:8080 manishgdev/account:s4
627fed614c3286d62c65780c4951d195027defcf1292d54bdf4c7a52baf053af

>docker ps
  CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS          PORTS                    NAMES
  627fed614c32   manishgdev/account:s4   "java -jar accounts-…"   25 seconds ago   Up 24 seconds   0.0.0.0:8080->8080/tcp   adoring_galois

>docker compose up -d
[+] Running 4/4
 ✔ Network accounts_manishntw  Created                                                                             0.0s
 ✔ Container accounts-ms       Started                                                                             0.3s
 ✔ Container cards-ms          Started                                                                             0.3s
 ✔ Container loans-ms          Started                                                                             0.3s

>docker compose down
[+] Running 4/4
 ✔ Container loans-ms          Removed                                                                             0.7s
 ✔ Container accounts-ms       Removed                                                                             0.9s
 ✔ Container cards-ms          Removed                                                                             1.3s
 ✔ Network accounts_manishntw  Removed                                                                             0.7s
```

- MySQL Container
```
docker run -p 3306:3306 --name accountsdb -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=accountsdb -d mysql
```

If we don't want the containers to be deleted we can use `docker compose stop`

### Docker desktop not showing running container
- because they are running in different context
- to show list of all the contexts
```
> docker context list
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                             KUBERNETES ENDPOINT   ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   npipe:////./pipe/docker_engine
desktop-linux       moby                Docker Desktop                            npipe:////./pipe/dockerDesktopLinuxEngine

```
- switch to `desktop-linux` using below command
```
docker context use desktop-linux
```


## Service Discovery
### Eureka Server
Dependencies
- Eureka Server
- Spring Cloud Config
- Actuator

Convert Spring Boot Project to Eureka Server
- Add `@EnableEurekaServer` at top of main class
- add configs in applicationconfig.yml
```yml
server:
  port: 8070

eureka:
  instance:
    hostname: localhost
  client:
    fetchRegistry: false
    registerWithEureka: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

Configuring Eureka Client
```yml
management:
  endpoints:
    web:
      exposure:
        include: "*"
  endpoint:
    shutdown:
      enabled: true
  info:
    env:
      enabled: true

endpoints:
  shutdown:
    enabled: true

eureka:
  instance:
    preferIpAddress: true
  client:
    fetchRegistry: true
    registerWithEureka: true
    serviceUrl:
      defaultZone: http://localhost:8070/eureka/

info:
  app:
    name: "accounts"
    description: "Eazy Bank Accounts Application"
    version: "1.0.0"
```

#### Communication between microservices
- can be establised using `Open Feign`
- add `spring-cloud-starter-openfeign` dependency
- add `@EnableFeignClients` annotation on top of main class
- create Clients inside the project and annotate them with `@FeignClient` with the name which is used to register with eureka server  

