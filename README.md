# Mapstruct no spring-boot
O MapStruct é uma biblioteca Java de código aberto que é usada para mapear objetos de um tipo para outro de forma eficiente e fácil. Ele é frequentemente utilizado em aplicativos Spring para simplificar a conversão de objetos de transferência de dados (DTOs) para entidades de domínio e vice-versa.

1- Primeiro devemos adicionar no pom.xml a dependência

```
<dependency>
			<groupId>org.mapstruct</groupId>
			<artifactId>mapstruct</artifactId>
			<version>1.5.2.Final</version>
</dependency>
```
2- Eu adicionei um plugin também, ja que o spring não estava encontrando o tipo de Bean do mapstruct.
```
<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>11</source>
					<target>11</target>
					<annotationProcessorPaths>
						<path>
							<groupId>org.mapstruct</groupId>
							<artifactId>mapstruct-processor</artifactId>
							<version>1.5.2.Final</version>
						</path>
						<path>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
							<version>1.18.26</version>
						</path>
						<path>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok-mapstruct-binding</artifactId>
							<version>0.2.0</version>
						</path>
					</annotationProcessorPaths>
				</configuration>
			</plugin>
  ```
  
3- Criar uma interface e marcando com @Mapper, e tambem criei um AbstractMapper onde demos informar o Model e o Resource para ser mapeado.

```
import org.mapstruct.Mapper;

@Mapper(componentModel = "spring")
public interface UserAssemblerMapper extends AbstractMapper<UserModel, UserRequest> {
}
```

4 - Agora o AbstractMapper

```
public interface AbstractMapper<ENTITY, RESOURCE> {

    ENTITY toEntity(RESOURCE resource);

    RESOURCE toResource(ENTITY entity);

    List<ENTITY> toEntities(List<RESOURCE> resources);

    List<RESOURCE> toResources(List<ENTITY> entities);
}
```

Conseguimos utilizar esses metodos para mapear do Model para o Resource e lista de Model para lista de Resouce e vice versa, um pequeno exemplo utilizando o mapper para mapear do Resouce para o Model na linha 85:

```
@Service
@RequiredArgsConstructor(onConstructor = @__(@Autowired))
public class UserService {

    private final UserRepository repository;
    private final PasswordEncoder encoder;
    private final UserAssemblerMapper mapper;
    private final UserValidate validate;

    public UserResponse createUser(UserRequest request){

        validate.validateRequest(request);

        request.setPassword(encoder.encode(request.getPassword()));
        repository.save(mapper.toEntity(request));

        UserResponse response = new UserResponse();
         response.setMessage("Usuario " + request.getUserName() + " cadastrado com sucesso!");
         return response;
    }
 ```

