# Chapter 3

## 03. Security and validation

## Implementing User and Rule entities

### DSCatalog Conceptual Model

![uml](https://user-images.githubusercontent.com/22635013/150198718-0428dd88-afa1-4d25-ae9f-b7d9eb3aaeab.jpg)

First, we will complete the bidirectional relationship between the ***Product.java*** and ***Category.java*** class. The ***Product.java*** class already has a mapping with multiple categories as we can see below

*Excerpt inside the class **Product.java** mapping to multiple **categories***

```java
@ManyToMany
@JoinTable(name = "tb_product_category",
    joinColumns = @JoinColumn(name = "product_id"),
    inverseJoinColumns = @JoinColumn(name = "category_id"))	
Set<Category> categories = new HashSet<>();
```

*Excerpt inside the class **Product.java**. We only need the *getCategories()**

```java
public Set<Category> getCategories() {
    return categories;
}
```
<hr>

As mentioned earlier, there is a bidirectional relationship. The *Product.java* knows its categories and the *Category.java* knows its products, as shown in the diagram above *DSCatalog Conceptual Model*, however, this bidirectional declaration has not yet been implemented in the *Category.java*.java class. This is the step we will do next in the *Category.java* class

Declaration of the product list associated with a category

Association of the relationship between the *Product* **products** in *Category.java* class

*Excerpt within the ***Category.java*** class, to associate the ***Product.java*** with the products*

```java
@ManyToMany(mappedBy = "categories")
private Set<Product> products = new HashSet<>();
```
The mapping definition <code>mappedBy = "categories"</code> will do the mapping based on what has already been mapped on the ***Product*** side. In the case in question with the code below definition with *categories*

```java
Set<Category> categories = new HashSet<>()
```

*Excerpt inside the class ***Category.java***. We only need the ***getProducts()****

```java
public Set<Product> getProducts() {
    return products;
}
```
<hr>

### User and Role implementation

Let's start the implementation of our model, creating the *User* and *Role* entities

The *Role* class implementation will have 2 user profiles:


- **OPERATOR**
- and **ADMIN**


We create the Role and User classes following the *DSCatalog Conceptual Model*

Taking into account the diagram, the *User.java* class has a directed association with the *Role.java** class. It is not a multiple association as is the case between the *Product* and *Category* class. In this case, the *User* must know their roles

*Class **Role.java** implementation*

```java
package com.devsuperior.dscatalog.entities;

import java.io.Serializable;
import java.util.Objects;

public class Role implements Serializable {
	private static final long serialVersionUID = 1L;
	
	private Long id;
	private String authority;
	
	public Role() {
	}

	public Role(Long id, String authority) {
		this.id = id;
		this.authority = authority;
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getAuthority() {
		return authority;
	}

	public void setAuthority(String authority) {
		this.authority = authority;
	}

	@Override
	public int hashCode() {
		return Objects.hash(id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Role other = (Role) obj;
		return Objects.equals(id, other.id);
	}
	
}
```

*Class **User.java** implementation*

```java
package com.devsuperior.dscatalog.entities;

import java.io.Serializable;
import java.util.HashSet;
import java.util.Objects;
import java.util.Set;

public class User implements Serializable {
	private static final long serialVersionUID = 1L;
	
	private Long id;
	private String firstName;
	private String lastName;
	private String email;
	private String password;
	
	private Set<Role> roles = new HashSet<>();
	
	public User() {	
	}

	public User(Long id, String firstName, String lastName, String email, String password) {
		this.id = id;
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
		this.password = password;
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}
	
	public Set<Role> getRoles() {
		return roles;
	}

	@Override
	public int hashCode() {
		return Objects.hash(id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		User other = (User) obj;
		return Objects.equals(id, other.id);
	}
	
}
```

<hr>

## 03-05 Object-Relational Mappings, Database Seed

Let's make the object-relational mapping, seed of the database in order to work in the H2-Console

*Class **Role.java** Object-Relational Mappings, Database Seed implementation*

```java
@Entity
@Table(name = "tb_role")
public class Role implements Serializable {
	private static final long serialVersionUID = 1L;
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String authority;
```

*Class **User.java** Object-Relational Mappings, Database Seed implementation*

```java
@Entity
@Table(name = "tb_user")
public class User implements Serializable {
	private static final long serialVersionUID = 1L;
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String firstName;
	private String lastName;
	private String email;
	private String password;
	
	private Set<Role> roles = new HashSet<>();
```


We have to map the multiplicity between **User** and **Role**. A user can have several roles, however the same profile, or the same role can be of several users at the same time. It's a many-to-many relationship, so we know that we have to have a third table of this association, just like the **Product** and **Category**

*Class **User.java** @ManyToMany Association*

```java
@ManyToMany
@JoinTable(name = "tb_user_role",
    joinColumns = @JoinColumn(name = "user_id"),
    inverseJoinColumns = @JoinColumn(name = "role_id"))	
private Set<Role> roles = new HashSet<>();
```

At this point we can test running ***SpringToolSuite*** on http://localhost:8080/h2-console and check if the *user*, *role* and *user_role* tables are being created

Add Seed for users and their profiles to populate the tables test running ***SpringToolSuite*** to see if tables are being populated

<hr>

# 03-06 Users CRUD PART 1

*Excerpt ***UserRepository*** creation*

```java
package com.devsuperior.dscatalog.repositories;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.devsuperior.dscatalog.entities.User;

@Repository
public interface  UserRepository extends JpaRepository<User, Long>{

}
```

We create a List of Set Roles, in order to receive the profiles or roles of the users

*UserDTO excerpt creation, without passing the password*

```java
package com.devsuperior.dscatalog.dto;

import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;

import com.devsuperior.dscatalog.entities.User;

public class UserDTO implements Serializable {
	private static final long serialVersionUID = 1L;

	private Long id;
	private String firstName;
	private String lastName;
	private String email;
	
	private Set<RoleDTO> roles = new HashSet<>();
	
	public UserDTO() {
	}

	public UserDTO(Long id, String firstName, String lastName, String email) {
		this.id = id;
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}

	public UserDTO(User entity) {
		id = entity.getId();
		firstName = entity.getFirstName();
		lastName = entity.getLastName();
		email = entity.getEmail();
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public Set<RoleDTO> getRoles() {
		return roles;
	}	
}
```

*At this point we also have to create the RoleDTO*

```java
package com.devsuperior.dscatalog.dto;

import java.io.Serializable;

import com.devsuperior.dscatalog.entities.Role;

public class RoleDTO implements Serializable {
	private static final long serialVersionUID = 1L;
	
	private Long id;
	private String authority;
	
	public RoleDTO() {
	}

	public RoleDTO(Long id, String authority) {
		this.id = id;
		this.authority = authority;
	}

	public RoleDTO(Role entity) {
		id = entity.getId();
		authority = entity.getAuthority();
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getAuthority() {
		return authority;
	}

	public void setAuthority(String authority) {
		this.authority = authority;
	}	
	
}
```

With this ready we will implement the Service and the User Controller

To simplify the creation of the ***UserService***, we copied the ***ProductService*** class and renamed it to ***UserService***

Then inside the class ***UserService***, we replace everything that says ***Product*** to ***User***

There is one aspect that we have to consider, and this is about the ***findById*** method, in the code below, in which a builder was called passing the list of products categories

```java
@Transactional(readOnly = true)
public UserDTO findById(Long id) {
    Optional<User> obj = repository.findById(id);
    User entity = obj.orElseThrow(() -> new ResourceNotFoundException("Entity not found"));
    return new UserDTO(entity, entity.getCategories());
}
```


In the ***ProductDTO*** class we had the possibility to create a ***ProductDTO*** constructor leaving an empty list

****ProductDTO*** excerpt in ***ProductDTO*** class*

```java
public ProductDTO(Product entity) {
    this.id = entity.getId();
    this.name = entity.getName();
    this.description = entity.getDescription();
    this.price = entity.getPrice();
    this.imgUrl = entity.getImgUrl();
    this.date = entity.getDate();
}
```

Or, passing another constructor indicating that we want to populate the list associated with ProductDTO <code>private List<CategoryDTO> categories = new ArrayList<>();</code>

```java
	private List<CategoryDTO> categories = new ArrayList<>();
	
	(...)
	
	public ProductDTO(Product entity, Set<Category> categories) {
		this(entity);
		categories.forEach(cat -> this.categories.add(new CategoryDTO(cat)));
	}
```
In the case of user and *user* profiles, the number of user profiles is limited, and it will be necessary to load them all for authentication and authorization.

For our implementation we will add in the *User* class the parameter <code>@ManyToMany(fetch = FetchType.EAGER)</code>

With this, whenever a search is made in the database, the roles or profiles of that user will be hanging with it. This is a Spring Security requirement

Based on that, we will let *UserDTO* load user profiles

In the <code>public UserDTO(User entity)</code> constructor of the *UserDTO* class, the User entity, once the <code>EAGER</code> has been configured in the *User*, we know that the *Role* profile objects will be hanging on it, so we can also load the collection of *RoleDTO*

Let's use the <code>RoleDTO(Role entity)</code> constructor of the *RoleDTO* class to populate the *RoleDTO* list inside the *UserDTO*

*Excerpt from the ***RoleDTO*** class with the RoleDTO(Role entity) method*

```java
public RoleDTO(Long id, String authority) {
    this.id = id;
    this.authority = authority;
}

public RoleDTO(Role entity) {
    id = entity.getId();
    authority = entity.getAuthority();
}
```
We use a schema similar to what was done in ProductDTO

*We create a foreach inside the UserDTO(User entity) and for each user we load we search the list of roles and add it to our list of roles*

```java
package com.devsuperior.dscatalog.dto;

import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;

import com.devsuperior.dscatalog.entities.User;

public class UserDTO implements Serializable {
	private static final long serialVersionUID = 1L;
	
	private Long id;
	private String firstName;
	private String lastName;
	private String email;
	
	private Set<RoleDTO> roles = new HashSet<>();
	
	public UserDTO() {
	}

	public UserDTO(Long id, String firstName, String lastName, String email) {
		this.id = id;
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}

	public UserDTO(User entity) {
		id = entity.getId();
		firstName = entity.getFirstName();
		lastName = entity.getLastName();
		email = entity.getEmail();
		entity.getRoles().forEach(role -> this.roles.add(new RoleDTO(role)));
	}
```

The <code>UserDTO findById(Long id)</code> method of the UserService class will only return the <code>return new UserDTO(entity)</code>


```java
@Transactional(readOnly = true)
public UserDTO findById(Long id) {
    Optional<User> obj = repository.findById(id);
    User entity = obj.orElseThrow(() -> new ResourceNotFoundException("Entity not found"));
    return new UserDTO(entity);
}
```


Method <code>UserDTO insert(UserDTO dto)</code>, we instantiate an empty user, copy the data to the entity, write and return the saved DTO as seen below


```java
@Transactional
public UserDTO insert(UserDTO dto) {
    User entity = new User();
    copyDtoToEntity(dto, entity);
    entity = repository.save(entity);
    return new UserDTO(entity);
}
```

Method <code>UserDTO update(Long id, UserDTO dto)</code>, it's the same thing, we get the *id* we want to *update*, we instantiate it with <code>getOne(id)</code> since it doesn't go to the database, copy the data to the new entity, save it, and return the updated object, as seen in the code below

```java
@Transactional
public UserDTO update(Long id, UserDTO dto) {
    try {
        User entity = repository.getOne(id);
        copyDtoToEntity(dto, entity);
        entity = repository.save(entity);
        return new UserDTO(entity);
    }
    catch (EntityNotFoundException e) {
        throw new ResourceNotFoundException("Id not found " + id);
    }		
}
```
The <code>delete(Long id)</code> method to delete take care of exceptions

```java
public void delete(Long id) {
    try {
        repository.deleteById(id);
    }
    catch (EmptyResultDataAccessException e) {
        throw new ResourceNotFoundException("Id not found " + id);
    }
    catch (DataIntegrityViolationException e) {
        throw new DatabaseException("Integrity violation");
    }
}
```

Let's implement the *copyDtoToEntity(UserDTO dto, User entity)* method without considering the *password* for the moment

We have to copy the data from a *UserDTO* to a *User entity*

In this specific case, we only have to consider the following fields (however, the password will not be considered, for now):


- id
- firstName
- lastName
- email
- password


The *id* is a field that does not update

*Excerpt from the ***UserService*** class, implementing ***copyDtoToEntity(UserDTO dto, User entity)****

```java
private void copyDtoToEntity(UserDTO dto, User entity) {

    entity.setFirstName(dto.getFirstName());
    entity.setLastName(dto.getLastName());
    entity.setEmail(dto.getEmail());
    
    entity.getRoles().clear();
    for (RoleDTO roleDto : dto.getRoles()) {
        Role role = roleRepository.getOne(roleDto.getId());
        entity.getRoles().add(role);			
    }
}
```

Taking into account the code above, to instantiate the *roles*, we follow the same reasoning, in which we clear the collection with <code>clear()</code> in <code>entity.getRoles().clear();</code>

We need to create the *RoleRepository.java* first, since we are going to use in *copyDtoToEntity(UserDTO dto, User entity)* method



```java
package com.devsuperior.dscatalog.repositories;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.devsuperior.dscatalog.entities.Role;

@Repository
public interface  RoleRepository extends JpaRepository<Role, Long>{

}
```

Then we inject the *RoleRepository* dependency in the *UserService* class

*Inject the ***RoleRepository*** dependency in the ***UserService*** class*

```java
@Autowired
private RoleRepository roleRepository;
```
*Excerpt from the ***UserService*** class, implementing ***copyDtoToEntity(UserDTO dto, User entity)****

```java
private void copyDtoToEntity(UserDTO dto, User entity) {

    entity.setFirstName(dto.getFirstName());
    entity.setLastName(dto.getLastName());
    entity.setEmail(dto.getEmail());
    
    entity.getRoles().clear();
    for (RoleDTO roleDto : dto.getRoles()) {
        Role role = roleRepository.getOne(roleDto.getId());
        entity.getRoles().add(role);			
    }
}
```

For each <code>RoleDTO</code> in <code>dto.getRoles()</code>

Now, following our previous reasoning, *roleRepository* will do a *getOne()* on roleDto.getId() and instantiate a temporary *role* with *getOne()* and finally it is inserted in our list of *roles*

<code>Role role = roleRepository.getOne(roleDto.getId());</code>

*Excerpt of the ***UserService*** class after this implementation*

```java
package com.devsuperior.dscatalog.services;

import java.util.Optional;

import javax.persistence.EntityNotFoundException;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataIntegrityViolationException;
import org.springframework.dao.EmptyResultDataAccessException;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.devsuperior.dscatalog.dto.RoleDTO;
import com.devsuperior.dscatalog.dto.UserDTO;
import com.devsuperior.dscatalog.entities.Role;
import com.devsuperior.dscatalog.entities.User;
import com.devsuperior.dscatalog.repositories.RoleRepository;
import com.devsuperior.dscatalog.repositories.UserRepository;
import com.devsuperior.dscatalog.services.exceptions.DatabaseException;
import com.devsuperior.dscatalog.services.exceptions.ResourceNotFoundException;

@Service
public class UserService {

	@Autowired
	private UserRepository repository;
	
	@Autowired
	private RoleRepository roleRepository;
	
	@Transactional(readOnly = true)
	public Page<UserDTO> findAllPaged(Pageable pageable) {
		Page<User> list = repository.findAll(pageable);
		return list.map(x -> new UserDTO(x));
	}

	@Transactional(readOnly = true)
	public UserDTO findById(Long id) {
		Optional<User> obj = repository.findById(id);
		User entity = obj.orElseThrow(() -> new ResourceNotFoundException("Entity not found"));
		return new UserDTO(entity);
	}

	@Transactional
	public UserDTO insert(UserDTO dto) {
		User entity = new User();
		copyDtoToEntity(dto, entity);
		entity = repository.save(entity);
		return new UserDTO(entity);
	}

	@Transactional
	public UserDTO update(Long id, UserDTO dto) {
		try {
			User entity = repository.getOne(id);
			copyDtoToEntity(dto, entity);
			entity = repository.save(entity);
			return new UserDTO(entity);
		}
		catch (EntityNotFoundException e) {
			throw new ResourceNotFoundException("Id not found " + id);
		}		
	}

	public void delete(Long id) {
		try {
			repository.deleteById(id);
		}
		catch (EmptyResultDataAccessException e) {
			throw new ResourceNotFoundException("Id not found " + id);
		}
		catch (DataIntegrityViolationException e) {
			throw new DatabaseException("Integrity violation");
		}
	}
	
	private void copyDtoToEntity(UserDTO dto, User entity) {

		entity.setFirstName(dto.getFirstName());
		entity.setLastName(dto.getLastName());
		entity.setEmail(dto.getEmail());
		
		entity.getRoles().clear();
		for (RoleDTO roleDto : dto.getRoles()) {
			Role role = roleRepository.getOne(roleDto.getId());
			entity.getRoles().add(role);			
		}
	}	
}
```
# 03-07 Users CRUD PART 2

We will handle the user's password, in which it only has to travel on the insertion of the new user and not on the other endpoints

Let's create a special dto to insert the password, in which it will load all the data of the common dto, but it will also load the password


To simplify our task, let's use inheritance, creating the *UserInsertDTO* class. This class will inherit from the *UserDTO* class, and it will have everything that the *UserDTO* class has plus the password

```java
package com.devsuperior.dscatalog.dto;

public class UserInsertDTO extends UserDTO {
	private static final long serialVersionUID = 1L;
	
	private String password;
	
	UserInsertDTO() {
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

}
```

Then, in the *UserService*, we will have to change the *UserDTO insert(UserDTO dto)* method, using the *UserInsertDTO* as an argument

*We changed the <code>UserDTO insert(UserDTO dto)</code> method to use the <code>UserDTO insert(UserInsertDTO dto)</code>*

```java
@Transactional
public UserDTO insert(UserInsertDTO dto) {
    User entity = new User();
    copyDtoToEntity(dto, entity);
    entity = repository.save(entity);
    return new UserDTO(entity);
}
```
At this point, to insert a user, in addition to copying the standard data, we will have to do the same with the password

However, the password is not the one that will be introduced by the user, since it will have to be encrypted using the bcrypt algorithm

We have to add the following dependency in the *pom.xml* file:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
<i>Excerpt from our <strong>pom.xml</strong>file after being edited</i>

```xml
(...)

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>

		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>

	</dependencies>

(...)
```

We will have to create a *BEAN* component of *BCRYPT* in order to be used inside the *UserService*

For this we will create a class that we will call *AppConfig*. It will be a configuration class and it will be inside a new package that we will call *config*

This class will save the application settings as a whole

Within a project *SpringBoot* we can have configuration classes that can specify a specific component or maintain some kind of configuration. This class type is specified with the <code>@Configuration</code> annotation

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
public class AppConfig {

	@Bean
	public BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
}
```
<p>In the code above, the <code>@Bean</code> notation, above the <code>BCryptPasswordEncoder passwordEncoder()</code> method indicates that the new <code>BCryptPasswordEncoder()</code> instance will be managed by <strong>SpringBoot</strong >, allowing the injection of that instance in other classes, namely in the <strong>UserService</strong> class</p>

<i>Excerpt in which an instance of <strong>BCryptPasswordEncoder</strong> is injected into the <strong>UserService</strong> class</i>

```java
@Service
public class UserService {

	@Autowired
	private BCryptPasswordEncoder passwordEncoder;
```

We can now use the *passwordEncoder* service to transform the user's password into a HASH code for the password that will be registered in the database

*Using ***passwordEncoder*** in the ***insert(UserInsertDTO dto)*** method*

```java
@Transactional
public UserDTO insert(UserInsertDTO dto) {
	User entity = new User();
	copyDtoToEntity(dto, entity);
	entity.setPassword(passwordEncoder.encode(dto.getPassword()));
	entity = repository.save(entity);
	return new UserDTO(entity);
}
```

At this point, if we run our application, we will verify that because we have included the *security* dependency in the *pom.xml* file, it will make the application protected, and the will not release the *endpoints* for us

Likewise, if we test *Postman* to access the *Category* by *id*, we will get a 401 error, due to lack of login

In order to be able to test the endpoints before doing the authentication and authorization process with the token, we will provisionally release the endpoints here, even with the *Spring Security* library included

Let's use the code below to provisionally release the *endpoints*

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	public void configure(WebSecurity web) throws Exception {
		web.ignoring().antMatchers("/**");
	}
}
```
We're going to put this code in another class that we're going to create, and we're going to call it *SecurityConfig*

*Excerpt from ***SecurityConfig*** class to ignore all ***endpoints****

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	public void configure(WebSecurity web) throws Exception {
		web.ignoring().antMatchers("/**");
	}
}
```

If we run the application again and then test *Postman* we can verify that all *CRUD* actions will work

<hr>

# 03-08 Users CRUD PART 3

<p>Let's start our controller to test the user <strong>CRUD</strong></p>

<p>Before that, let's rename the package <strong>resources</strong> to <strong>controllers</strong> and the files <strong>CategoryResource</strong> to <strong>CategoryController</strong> and <strong>ProductResource</strong> to <strong>ProductController</strong></p>

<p>After that, let's copy and paste the <strong>ProductController</strong> in the same package and rename it to <strong>UserController</strong></p>

<p>Inside the file <strong>UserController</strong> let's change the <code>@RequestMapping(value = "/products")</code> to <code>@RequestMapping(value = "/users")</code> and all references from <strong>Product</strong> to <strong>User</strong></p>

<i>Current excerpt from the <code>ResponseEntity<UserDTO> insert(@RequestBody UserDTO dto)</code> method</i>

```java
@PostMapping
public ResponseEntity<UserDTO> insert(@RequestBody UserDTO dto) {
	dto = service.insert(dto);
	URI uri = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}")
			.buildAndExpand(dto.getId()).toUri();
	return ResponseEntity.created(uri).body(dto);
}
```
<p>The <strong>insert</strong> method of the code above should change as well. The <strong>UserDTO</strong> will pass to <strong>UserInsertDTO</strong>. </p>


<p>The return of <code>dto=service.insert(dto);</code></p>

<p>According to what we can see in the <strong>UserService</strong> class, the <strong>insert</strong> method receives <strong>UserInsertDTO</strong> but returns the <strong>UserDTO</strong>></p>


<i>Excerpt from the <strong>insert</strong> method in the <strong>UserService</strong> class</i>

```java
@Transactional
public UserDTO insert(UserInsertDTO dto) {
	User entity = new User();
	copyDtoToEntity(dto, entity);
	entity.setPassword(passwordEncoder.encode(dto.getPassword()));
	entity = repository.save(entity);
	return new UserDTO(entity);
}
```
<i>Excerpt of the change we made to the <code>ResponseEntity<UserDTO> insert(@RequestBody UserDTO dto)</code> method</i>

```java
@PostMapping
public ResponseEntity<UserDTO> insert(@RequestBody UserInsertDTO dto) {
	UserDTO userDTo = service.insert(dto);
	URI uri = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}")
			.buildAndExpand(userDTo.getId()).toUri();
	return ResponseEntity.created(uri).body(userDTo);
}
```
<p>All other methods, namely <strong>update</strong> and <strong>delete</strong> do not need any changes</p>

<p>At the moment, if we test in <strong>Postman</strong> all the <strong>endpoints</strong> related to the <strong>User</strong>, we will verify that they all work. We can even check at <a href="http://localhost:8080/h2-console">http://localhost:8080/h2-console</a> that an encrypted password is being generated for the new user created in <strong>Postman</strong> using the <strong>insert</strong> method</p>

<pLet's start our field validation process. In the <strong>User</strong> class, if we define the <strong>email</strong> as <strong>unique</strong> see the code below
In <strong>Postman</strong>, if we try to update a user or insert a new user with an email that already exists in the database we will get a <strong>500 internal server error</strong></p>

```java
@Column(unique = true)
private String email;
```
<p>The <strong>SpringToolSuite</strong> application will throw the <strong>JdbcSQLIntegrityConstraintViolationException</strong> error because we try to insert a repeated email, and we configure the <strong>Database</strong> not to let the email repeat</p>

<p>We will handle this exception later when we do the validation of the fields</p>

<p>Our user <strong>CRUD</strong> is working, although exception handling is missing</p>

### 03-09 Introduction to Bean Validation

### 03-10 Testing Basic Annotations

<p>In order to test our validations, we have to have the <strong>spring-boot-starter-validation</strong> dependency inserted in our <strong>pom.xml</strong> file</p>

<p>In the <strong>UserDTO</strong> and <strong>ProductDTO</strong> classes we will create validation for the fields, in a hard-code way but later we will extract this validation</p>

<i>Excerpt class <strong>UserDTO</strong></i>

```java
package com.devsuperior.dscatalog.dto;

import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotBlank;

import com.devsuperior.dscatalog.entities.User;

public class UserDTO implements Serializable {
	private static final long serialVersionUID = 1L;
	
	private Long id;
	
	@NotBlank(message = "Campo obrigatório")
	private String firstName;
	private String lastName;
	
	@Email(message = "Por favor introduza um email válido")
	private String email;
```
<i>Excerpt class <strong>ProductDTO</strong></i>

```java
package com.devsuperior.dscatalog.dto;

import java.io.Serializable;
import java.time.Instant;
import java.util.ArrayList;
import java.util.List;
import java.util.Set;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.PastOrPresent;
import javax.validation.constraints.Positive;
import javax.validation.constraints.Size;

import com.devsuperior.dscatalog.entities.Category;
import com.devsuperior.dscatalog.entities.Product;

public class ProductDTO implements Serializable {
	private static final long serialVersionUID = 1L;

	private Long id;
	
	@Size(min = 5, max = 60, message = "O valor introduzido tem que ter entre 5 e 60 caracteres")
	@NotBlank(message = "Campo obrigatório")
	private String name;
	
	@NotBlank(message = "Campo obrigatório")
	private String description;
	
	@Positive(message = "Preço deve ser um valor positivo")
	private Double price;
	private String imgUrl;
	
	@PastOrPresent(message = "A data do produto não pode ser futura")
	private Instant date;
```

<i><strong>ProductController</strong> class excerpt with <code>@Valid</code> attribute</i>


```java
@PostMapping
public ResponseEntity<ProductDTO> insert(@Valid @RequestBody ProductDTO dto) {
	dto = service.insert(dto);
	URI uri = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}")
			.buildAndExpand(dto.getId()).toUri();
	return ResponseEntity.created(uri).body(dto);
}

@PutMapping(value = "/{id}")
public ResponseEntity<ProductDTO> update(@PathVariable Long id, @Valid @RequestBody ProductDTO dto) {
	dto = service.update(id, dto);
	return ResponseEntity.ok().body(dto);
}
```
<i><strong>UserController</strong> class excerpt with <code>@Valid</code> attribute</i>

```java
@PostMapping
	public ResponseEntity<UserDTO> insert(@Valid @RequestBody UserInsertDTO dto) {
		UserDTO userDTo = service.insert(dto);
		URI uri = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}")
				.buildAndExpand(userDTo.getId()).toUri();
		return ResponseEntity.created(uri).body(userDTo);
	}

	@PutMapping(value = "/{id}")
	public ResponseEntity<UserDTO> update(@PathVariable Long id, @Valid @RequestBody UserDTO dto) {
		dto = service.update(id, dto);
		return ResponseEntity.ok().body(dto);
	}
```
<p>If we run the application in <strong>SpringToolSuite</strong> and test in <strong>Postman</strong>, if we test to introduce a new product with less than 5 characters in the name, we will get <strong>error 400</strong > but without receiving any messages</p>

<p>If we check the <strong>SpringToolSuite</strong> message, the application returned a <strong>warn</strong> with the information <strong>MethodArgumentNotValidException</strong>. The exception thrown, should be handled in order to return a <strong>json</strong> with the custom messages, however, the framework no longer accepts data that does not correspond to the imposed rules</p>


### 03-11 Handling MethodArgumentNotValidException exception

<p>In the <strong>ResourceExceptionHandler</strong> class inside the <strong>exceptions</strong> package we have the <strong>ResourceNotFoundException</strong> which is able to catch exceptions and return a custom response, similarly with the exception <strong>DatabaseException</strong>. Let's do the same with the <strong>MethodArgumentNotValidException</strong></p>

<p>Let's reuse the <strong>DatabaseException</strong> exception handling code and copy, paste and rename it to </strong>MethodArgumentNotValidException</strong>. We also changed the method name to <strong>validation</strong></p>

<p>In case this exception happens, the response to be returned will not be <strong>BAD_REQUEST</strong>, we will return the HTTP code <strong>422</strong>, designated as <strong>UNPROCESSABLE_ENTITY</strong></p>

<p>The <strong>UNPROCESSABLE_ENTITY</strong> serves to warn that an entity has not been processed</p>

<p>At this moment, if we run the application, and test in <strong>Postman</strong> the insertion of a new product with less than 5 characters, <strong>Postman</strong> will already return the error <strong>422 Unprocessable Entity</strong>, in the error description already put <strong>error: "Validation exception"</strong> and in <strong>"message":</strong> as shown in the code below:</p>


```json
{
    "timestamp": "2022-01-27T05:18:22.184901600Z",
    "status": 422,
    "error": "Validation exception",
    "message": "Validation failed for argument [0] in public org.springframework.http.ResponseEntity<com.devsuperior.dscatalog.dto.ProductDTO> com.devsuperior.dscatalog.controller.ProductController.insert(com.devsuperior.dscatalog.dto.ProductDTO) with 2 errors: [Field error in object 'productDTO' on field 'price': rejected value [0.0]; codes [Positive.productDTO.price,Positive.price,Positive.java.lang.Double,Positive]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [productDTO.price,price]; arguments []; default message [price]]; default message [Preço deve ser um valor positivo]] [Field error in object 'productDTO' on field 'name': rejected value [PS5]; codes [Size.productDTO.name,Size.name,Size.java.lang.String,Size]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [productDTO.name,name]; arguments []; default message [name],60,5]; default message [O valor introduzido tem que ter entre 5 e 60 caracteres]] ",
    "path": "/products"
}
```
<p>Let's improve the message returned by the exception, so that the frontend can take advantage of the message returned</p>

### 03-12 Custom response to validation error

<p>Let's make an error object that will do everything that <strong>StandardError</strong> already had and let's add another list of <strong>FieldMessage</strong> with the creation of a new class that we will call <strong>ValidationError</strong> which will extend from <strong>StandardError</strong> having one more item to add, which is the error list</p>

<p>First we have to create a helper class to put the field and the error message</p>

<i>Helper class <strong>FieldMessage</strong></i>

```java
package com.devsuperior.dscatalog.resources.exceptions;

import java.io.Serializable;

public class FieldMessage implements Serializable {
	private static final long serialVersionUID = 1L;

	private String fieldName;
	private String message;
	
	public FieldMessage() {
	}

	public FieldMessage(String fieldName, String message) {
		super();
		this.fieldName = fieldName;
		this.message = message;
	}

	public String getFieldName() {
		return fieldName;
	}

	public void setFieldName(String fieldName) {
		this.fieldName = fieldName;
	}

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}
	
	
}
```
<p>We create the class with an error list <strong>FieldMessage</strong> that will be used to add the errors and messages</p>


```java
package com.devsuperior.dscatalog.resources.exceptions;

import java.util.ArrayList;
import java.util.List;

public class ValidationError extends StandardError {
	private static final long serialVersionUID = 1L;

	private List<FieldMessage> errors = new ArrayList<>();

	public List<FieldMessage> getErrors() {
		return errors;
	}
	
	public void addError(String fieldName, String message) {
		errors.add(new FieldMessage(fieldName, message));
	}
	
}
```
<p>Finally we update the <strong>validation</strong> method in the <strong>ResourceExceptionHandler</strong> class</p>


```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<ValidationError> validation(MethodArgumentNotValidException e, HttpServletRequest request) {
	HttpStatus status = HttpStatus.UNPROCESSABLE_ENTITY;
	ValidationError err = new ValidationError();
	err.setTimestamp(Instant.now());
	err.setStatus(status.value());
	err.setError("Validation exception");
	err.setMessage(e.getMessage());
	err.setPath(request.getRequestURI());
	
	for (FieldError f : e.getBindingResult().getFieldErrors()) {
		err.addError(f.getField(), f.getDefaultMessage());
	}
	
	return ResponseEntity.status(status).body(err);
}
```
<hr/>

## 03-13 Implementing a custom ConstraintValidator

<p>The validations we've done so far are syntax validations, that is, in terms of attribute values</p>

<p>Not all validations are in the sense of parsing syntax or attribute value. There are validations that depend on and other things, including accessing the database to verify something. For example, when we are going to enter a user, the field <code>private String email;</code> cannot be repeated. In validation, we will have to do this treatment. If the user tries to register an email that already exists for another user, we will not allow it to be registered, we will have to generate another element in our list, with the warning that the email already exists. This validation is more complex, it implies access to the database</p>

<p>Let's take advantage of the entire <strong>beans validation</strong> library, its entire lifecycle, and insert some validation into it that can make a more complex rule and even access the database</p>

### Custom ConstraintValidator

<p>Let's use <strong>Custom ConstraintValidator</strong>, which is more specialized java code to create an annotation. All the basis for creating an annotation is a very specialized, difficult subject, and it is not our objective in the course. Let's just be a user of this template to create an annotation of a custom validation. The code below is a Boilerplate or standard code for all the validation we want to create</p>

<p>In our implementation, we will have to create two types. An interface with the definition of an annotation <code>@interface</code> and let's create a class that will be an implementation of the <strong>ConstraintValidator</strong></p>

<i>Excerpt <strong>Boilerplate</strong> for creating validations. Just copy</i>

### Annotation

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import javax.validation.Constraint;
import javax.validation.Payload;

@Constraint(validatedBy = UserInsertValidator.class)
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)

public @interface UserInsertValid {
	String message() default "Validation error";

	Class<?>[] groups() default {};

	Class<? extends Payload>[] payload() default {};
}
```

<p>In our project, inside the services package, once the validation is a business rule once we are doing a validation, and besides that this customized validation will access the database, it will use the repository. As it is a business rule and for accessing the repository, we will consider that this validation is in the service layer. Let's create a subpackage named <strong>validation</strong>, and inside it we'll create our annotation class named <strong>UserInsertValid</strong>. Then we copy all the code from above our class <code>@interface UserInsertValid</code> and paste it in our class, including the imports</p>

### Validator

```java
import java.util.ArrayList;
import java.util.List;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import org.springframework.beans.factory.annotation.Autowired;

import com.devsuperior.dscatalog.dto.UserInsertDTO;
import com.devsuperior.dscatalog.entities.User;
import com.devsuperior.dscatalog.repositories.UserRepository;
import com.devsuperior.dscatalog.resources.exceptions.FieldMessage;

public class UserInsertValidator implements ConstraintValidator<UserInsertValid, UserInsertDTO> {
	
	@Override
	public void initialize(UserInsertValid ann) {
	}

	@Override
	public boolean isValid(UserInsertDTO dto, ConstraintValidatorContext context) {
		
		List<FieldMessage> list = new ArrayList<>();
		
		// Coloque aqui seus testes de validação, acrescentando objetos FieldMessage à lista
		
		for (FieldMessage e : list) {
			context.disableDefaultConstraintViolation();
			context.buildConstraintViolationWithTemplate(e.getMessage()).addPropertyNode(e.getFieldName())
					.addConstraintViolation();
		}
		return list.isEmpty();
	}
}
```
<p>This annotation will make use of a class named <strong>UserInsertValidator</strong> that we will create in the same package in order to implement our validation logic. For our new class, we also already have a default code that we are going to use. Let's copy the <strong>Validator</strong> code from above, we must copy the  <strong>UserInsertValidator</strong> and paste it inside the <strong>UserInsertValidator</strong> class, replacing all the code in the class, leaving only the <code>package com.devsuperior.dscatalog.services.validation;</code></p>

<p>The <strong>UserInsertValidator</strong> class implements the <strong>ConstraintValidator</strong>, it is an interface of the <strong> beans validation</strong> as we can see through the <code>import javax.validation.ConstraintValidator;</code> and is a generics. Within generics we have to parameterize the type of our notation, which in this case is the <strong>UserInsertValid</strong> and the type of the class that will receive the notation, which in this case is the <strong>UserInsertDTO</strong>. So, we have to go to the <strong>UserInsertDTO</strong> and put the <strong>UserInsertValid</strong> notation that we created</p>

<p>The notation <i><code>@UserInsertValid</code></i> in the <strong>UserInsertDTO</strong> class will do a behind-the-scenes processing to check in the database if the email I'm inserting is already exists. By doing so, we are taking advantage of the entire validation lifecycle that we are using</p>

<p>The <code>isValid(UserInsertDTO dto, ConstraintValidatorContext context)</code> method in the <strong>UserInsertValidator</strong> class tests whether the <strong>UserInsertDTO</strong> object will be valid or not, as we can see from the its boolean return, if the object returns true it means that it is valid and found no errors, and if it returns false it means that there was at least one error</p>

<p>Our implementation will be based on a list of type <code>fieldMessage</code>. We start the tests we need on our <code>UserInsertDTO</code> object. We test if something was true or if there was an error and in that case we go to the list and add that error. At the end the method does a <code>return list.isEmpty();</code>, which means that if the list at the end returns empty, this happened because none of the tests entered, or that none of the tests failed. In this case, a true or false value will be returned depending on the empty list</p>

<p>In our case we will have to test if the email already exists in the database. To do this test, let's start with the <code>UserRepository</code></p>

<p>However, we still don't have within the <code>UserRepository</code>, a method that searches for the user by their email, so we will have to implement this</p>


<i>Excerpt with <code>findByEmail(String email)</code> implementation in <strong>UserRepository</strong></i>

```java
package com.devsuperior.dscatalog.repositories;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.devsuperior.dscatalog.entities.User;

@Repository
public interface  UserRepository extends JpaRepository<User, Long>{

	User findByEmail(String email);
}
```
<p>From now on we can search through the <strong>userRepository</strong> for an email and compare it with the email passed in the <strong>UserInsertDTO dto</strong>. The <strong>userRepository</strong> by default if it doesn't find the email will return null. If a value other than null is found in the condition, it means that an email was found equal to the one in the <strong>dto</strong> and in this case I have to generate a validation error, so I will add the error to the <code>List <FieldMessage> list = new ArrayList<>();</code></p>



```java
package com.devsuperior.dscatalog.services.validation;

import java.util.ArrayList;
import java.util.List;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import org.springframework.beans.factory.annotation.Autowired;

import com.devsuperior.dscatalog.dto.UserInsertDTO;
import com.devsuperior.dscatalog.entities.User;
import com.devsuperior.dscatalog.repositories.UserRepository;
import com.devsuperior.dscatalog.resources.exceptions.FieldMessage;

public class UserInsertValidator implements ConstraintValidator<UserInsertValid, UserInsertDTO> {
	
	@Autowired
	public UserRepository userRepository;
	
	@Override
	public void initialize(UserInsertValid ann) {
	}

	@Override
	public boolean isValid(UserInsertDTO dto, ConstraintValidatorContext context) {
		
		List<FieldMessage> list = new ArrayList<>();
		
		// Coloque aqui seus testes de validação, acrescentando objetos FieldMessage à lista
		
		User user = userRepository.findByEmail(dto.getEmail());
		if (user != null) {
			list.add(new FieldMessage("email", "Email já existe"));
		}
		
		for (FieldMessage e : list) {
			context.disableDefaultConstraintViolation();
			context.buildConstraintViolationWithTemplate(e.getMessage()).addPropertyNode(e.getFieldName())
					.addConstraintViolation();
		}
		return list.isEmpty();
	}
}
```
<p>At that moment, if we run our application and test it in <strong>Postman</strong>, when we insert a new user, if we do it with an existing email, the program will return an error with the warning that the <strong>O email already exists</strong></p>


<hr/>

### 03-14 UserUpdateValidator PART 1

<p>We made the <code>UserInsertValidator</code>, but we still have a problem that has to be resolved. If we go to <strong>Postman</strong>, and we try to update a user with an existing email, we will get a <strong>500</strong> error. We have to handle this error. </p>

<p>We should return an error <strong>422</strong> with a message handled to warn that this email is already taken by another user. </p>

<p>However, if I try to update the same user's email with their email, I can update. What I can't do is create a new user and give him an email that is already assigned to another user or even we can't update a user with an email that has also been assigned to another user</p>

<p>We have already created our notation to insert, we have to create another one to update</p>

<p>In the <code>public @interface UserUpdateValid</code> class we will configure your validator with the <code>UserUpdateValidator</code></p>

<i>Implementation of the <code>UserUpdateValid</code></i> class

```java
package com.devsuperior.dscatalog.services.validation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import javax.validation.Constraint;
import javax.validation.Payload;

@Constraint(validatedBy = UserUpdateValidator.class)
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)

public @interface UserUpdateValid {
	String message() default "Validation error";

	Class<?>[] groups() default {};

	Class<? extends Payload>[] payload() default {};
}

```
<p>In the <code>UserUpdateValid</code> class, as we can see in the code above, the validator is now the <code>UserUpdateValidator</code></p>

<p>In the <code>UserUpdateValidator</code> class the class will implement the <code>ConstraintValidator</code> but now it is in the <code>UserUpdateValid</code> notation. Who will be the <strong>DTO</strong> is a question that we have to consider in an important detail. If we simply put the <code>UserDTO</code> and in the <code>UserDTO</code> class we put the notation <code>@UserUpdateValid</code>, we are going to have a problem, since we made the <code> UserInsertDTO</code> inherit from <code>UserDTO</code>, that would mean that <code>UserInsertDTO</code> would also inherit update logic, and that would be wrong. The <code>UserInsertDTO</code> cannot inherit validation logic from update, only from insert. We will have to implement our implementation in another way, which is a bit controversial, which involves creating another <strong>DTO</strong>. Let's create the <code>UserUpdateDTO</code></p>

<i>Excerpt from the <code>UserUpdateValidator</code> class</i>

```java
package com.devsuperior.dscatalog.services.validation;

import java.util.ArrayList;
import java.util.List;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import org.springframework.beans.factory.annotation.Autowired;

import com.devsuperior.dscatalog.dto.UserInsertDTO;
import com.devsuperior.dscatalog.entities.User;
import com.devsuperior.dscatalog.repositories.UserRepository;
import com.devsuperior.dscatalog.resources.exceptions.FieldMessage;

public class UserUpdateValidator implements ConstraintValidator<UserUpdateValid, UserInsertDTO> {
	(...)
```

<p>To implement the <code>UserUpdateDTO</code> class we will clone the <code>UserInsertDTO</code> and rename it to <code>UserUpdateDTO</code>. This class will inherit from <code>UserDTO</code> and will have nothing else.</p>

<i><code>UserUpdateDTO</code> class implementation</i>

```java
package com.devsuperior.dscatalog.dto;

import com.devsuperior.dscatalog.services.validation.UserUpdateValid;

@UserUpdateValid
public class UserUpdateDTO extends UserDTO {
	private static final long serialVersionUID = 1L;
	
	

}
```

Class <code>UserDTO</code> will no longer have the notation <code>@UserUpdateValid</code>, however the notation <code>@UserUpdateValid</code> will have to be in the class <code>UserUpdateDTO</code> as we can see from the code above.


After dividing the <code>UserInsertDTO</code> and the <code>UserUpdateDTO</code> we have to refactor the <code>update(...)</code> method of the <code>UserController</code> to receive the <code>UserUpdateDTO</code>

<i>Excert class <code>UserController</code> with the <strong>update</strong> method refactored</i>

```java
@PutMapping(value = "/{id}")
	public ResponseEntity<UserDTO> update(@PathVariable Long id, @Valid @RequestBody UserUpdateDTO dto) {
		UserDTO userDto = service.update(id, dto);
		return ResponseEntity.ok().body(userDto);
	}
```

<p>Let's take the opportunity to update the <code>update</code> method of the <code>UserService</code></p>



<i>Excerpt from the <strong>UserService</strong> class, with refactoring in the <strong>update</strong> method</i>

```java
@Transactional
public UserDTO update(Long id, UserUpdateDTO dto) {
	try {
		User entity = repository.getOne(id);
		copyDtoToEntity(dto, entity);
		entity = repository.save(entity);
		return new UserDTO(entity);
	}
	catch (EntityNotFoundException e) {
		throw new ResourceNotFoundException("Id not found " + id);
	}		
}
```
<hr>

### 03-15 UserUpdateValidator PART 2

<p>Now that we've done completely separate insert and update validations, let's go back to the <strong>UserUpdateValidator</strong>
and let's change <strong>UserInsertDTO</strong> to <strong>UserUpdateDTO</strong>, we also change the <strong>initialize(UserInsertValid ann)</strong> to <strong>initialize(UserUpdateValid ann)</strong></p>

<i>Excerto da classe <strong>UserUpdateValidator</strong> com o método <strong>initialize</strong></i>

```java
@Override
public void initialize(UserUpdateValid ann) {
}
```

<p>The <strong>update</strong> has a peculiarity, if we analyze the endpoint of the <strong>Update user</strong> in the <strong>Postman</strong>, it has a <strong>id</strong> in the url , that is, <strong>http://localhost:8080/users/id</strong>, the <strong>id</strong> of the user we want to update will be passed. This is different from insert, where only the insert body is passed. For this, we have to access the code that came in the request inside the validator, in the request url</p>

<p>To access the <strong>id</strong> of the request we will have to inject the <strong>HttpServletRequest</strong> into the <strong>UserUpdateValidator</strong></p>

```java
@Autowired
private HttpServletRequest servletRequest;
```
<p>This <strong>HttpServletRequest</strong> stores the request information, and it is from there that I will be able to get this <strong>id</strong> that I passed in the <strong>update</strong> request</p>

<p>To get this code <strong>id</strong> I'll have to write the code below inside the <strong>isValid</strong> method</p>

```java
@SuppressWarnings("unchecked")
var uriVars = (Map<String, String>) servletRequest.getAttribute(HandlerMapping.URI_TEMPLATE_VARIABLES_ATTRIBUTE);

long userId = Long.parseLong(uriVars.get("id"));
```
<p>Agora tenho que verificar se o email que pretendo atualizar no <strong>DTO</strong> se ele já existe para outro utilizador que não seja esse que eu estou a atualizar passado no <strong>id</strong></p>


<p>So the logic will be similar to <strong>insert</strong>, but we will have to add one more condition. Now we'll have to test if the user is different from null, and the user's <strong>id</strong> is different from the database user's user <strong>id</strong>, so in that case I'm trying update a user with the email that already exists</p>




<p>With this, the logic for updating the user is done. If we run the application and test in <strong>Postman</strong> the update of a user with the existing email to another user, the application will return a <strong>422 error</strong> with the message <strong>Email already exists</strong></p>

<i><strong>UserUpdateValidator</strong> class</i>

```java
package com.devsuperior.dscatalog.services.validation;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import javax.servlet.http.HttpServletRequest;
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.servlet.HandlerMapping;

import com.devsuperior.dscatalog.dto.UserUpdateDTO;
import com.devsuperior.dscatalog.entities.User;
import com.devsuperior.dscatalog.repositories.UserRepository;
import com.devsuperior.dscatalog.resources.exceptions.FieldMessage;

public class UserUpdateValidator implements ConstraintValidator<UserUpdateValid, UserUpdateDTO> {
	
	@Autowired
	private HttpServletRequest servletRequest;
	
	@Autowired
	public UserRepository userRepository;
	
	@Override
	public void initialize(UserUpdateValid ann) {
	}

	@Override
	public boolean isValid(UserUpdateDTO dto, ConstraintValidatorContext context) {
		
		@SuppressWarnings("unchecked")
		var uriVars = (Map<String, String>) servletRequest.getAttribute(HandlerMapping.URI_TEMPLATE_VARIABLES_ATTRIBUTE);
		
		long userId = Long.parseLong(uriVars.get("id"));
		
		List<FieldMessage> list = new ArrayList<>();
		
		// Coloque aqui seus testes de validação, acrescentando objetos FieldMessage à lista
		
		User user = userRepository.findByEmail(dto.getEmail());
		if (user != null && userId != user.getId()) {
			list.add(new FieldMessage("email", "Email já existe"));
		}
		
		for (FieldMessage e : list) {
			context.disableDefaultConstraintViolation();
			context.buildConstraintViolationWithTemplate(e.getMessage()).addPropertyNode(e.getFieldName())
					.addConstraintViolation();
		}
		return list.isEmpty();
	}
}
```
<hr>


# 03-16 JWT Token, Authentication and Authorization

https://www.youtube.com/watch?v=n1z9lx4ymPM

# 03-17 OAuth2

![oauth_2](https://user-images.githubusercontent.com/22635013/142412334-dd929967-c72e-4071-9bad-c8d447d2e00c.jpg)

<p>In OAuth2 you will have three main actors in the process of accessing a protected project. You will have an Authorization Server and a Resource Server and you will also have the client that will be accessing these guys</p>

<p>The Authorization Server does not necessarily have to be the same application. It could be a different application, and it could even be installed in a different location, but it could be the same application</p>

<p>In our case the <strong>dscatalog</strong> will be at the same time the <strong>Authorization Server</strong> and <strong>Resource Server</strong></p>

<p>The <strong>Authorization Server</strong> is the one that will receive application credentials and user credentials, authenticate that user, and return a signed token. The authentication process consists of analyzing the application's credentials and analyzing the user's credentials and verifying that these credentials are valid. In the end, it will recognize these credentials as a system user and if it is recognized, it will return the token, if it does not recognize it, it will return a bad credentials and the error code 401</p>

<p>This process is actually authentication, why do we call it <strong>Authorization</strong>?! This is a somewhat controversial question, a possible interpretation is that someone wanted to find a name to define this server, either Authorization, Authentication, or both at the same time?! Someone chose <strong>Authorization</strong> because the concept of <strong>Authorization</strong> is one that consists of checking if you are authorized to access a resource. Even though you are authenticated, depending on your user profile, you may not be authorized to access a resource. So, starting from the generic concept of <strong>Authorization</strong> which is deciding whether or not you are authorized to access a certain resource, and considering that the token is a resource, we can interpret that an <strong>Authorization is happening </strong> here. You provided your credentials, the server authenticated you, and then it authorized you to receive a token</p>

<p>Starting from the point that we have a guy responsible for receiving credentials and generating the token. Once we have the token, we may want to access a resource, I may want to access a specific CRUD, this photo, this file that I want to download, then I will request the URI informing my token and then the Resource Server will say whether or not I can have that feature. If I try to access a resource even though I have a valid token, even though I am authenticated, if I try to access the resource and that resource is not authorized for me, I will get a response with <strong>error 403</ strong> which is the authorization or access denied error</p>

<p>The Oauth2 protocol is even used for you to share authentication between completely different systems. We see this many times on the web, for example you will access dropbox there, I want to access a file from dropbox, and as long as you are authorized to access that resource, of course you will be able to access the resource and then in this case <strong>Facebook </strong> would be the <strong>Authorization server</strong> and the <srtong>Dropbox</strong> would be the <strong>Resource server</strong></p>

<p>If I want an application of mine with social login integrating with github, facebook or gmail, I will login to gmail, facebook or github and then after the login is authorized I will receive a token that will give me access to your system. In this case the <strong>Authorization server</strong> would be github, facebook or gmail and the <strong>Resource server</strong> would be your system. So, they can be different systems, remembering once again that our <strong>dscatalog</strong> system will be simultaneously <strong>Authorization server</strong> and <strong>Resource server</strong></p>

<p>Let's now implement these two processes in our <strong>dscatalog</strong></p>

<hr>

# 03-18 How the login process works

<p>Let's make the first part of our system which is the <strong>Authorization server</strong>, the one that will receive the credentials and will return the token


![client_app](https://user-images.githubusercontent.com/22635013/142031459-02a4e24b-4fad-44f3-a102-1785b69adbd1.jpg)

When receiving the token, I will have to pass the <strong>Application credentials</strong> and the <strong>User credentials</strong> 

#### App credentials

So the application must also have a username and password, which here in <strong>Oauth2</strong> is <strong>client_id</strong> and <strong>client_secret</strong> 

We will see in practice that this is passed as the request header, and this header calls <code>Authorization:"Basic" + Base64.encode(client_id + ":" + client_secret)</code>

#### User credentials

User credentials will be passed in the request body, in the request parameter format <strong>urlencoded</strong> with <strong>username</strong> to <strong>password</strong> and in the case of <strong>Oauth2</strong> let's pass a parameter designated as <strong>grant_type</strong>. <strong>Oauth2</strong> allows you to perform several types of authentication, one of them is passing the username and password, then this type is called <strong>password</strong>, but there are other types

#### JWT Token

Let's use <strong>JWT</strong> as our token format. <strong>Oauth2</strong> accepts other formats but we're going to use this <strong>JWT</strong> because it's a very good standard and it's grown a lot, and it's very secure and so it's been widely adopted

It is worth remembering that the <strong>token</strong> has a signature that only the system knows, this token will also have the claims that are the data it carries and this token will have an expiration time. After it expires it is no longer valid

<hr>

# 03-19 Spring Security Checklist

### Spring Security

Spring Security is a subproject of SrpingBoot that is related to security. It is related to authentication, authorization...

#### Interfaces that must be implemented


<ul>
	<li>UserDetails</li>
	<li>UserDetailsService</li>
</ul>



Even working with Oauth2 and JWT you will have to have the basic infrastructure of Spring Security, to know how to access the database, access your user and check the credentials

It will be necessary to implement some interfaces, namely the <strong>UserDetails</strong> and <strong>UserDetailsService</strong>

![interfaces_2](https://user-images.githubusercontent.com/22635013/152502410-5ad50150-a7a4-470e-b58b-b5d9108d1adc.png)

The <strong>UserDetails</strong> interface will give user information. There will be a <strong>getAuthorities()</strong> method to return a collection of the profiles a user has. The profile type inside <strong>Spring Security</strong> is called <strong>GrantedAuthority</strong>, but for us it will just be a String, for example (admin, operator, client), we are going to set these strings

The <strong>UserDetails</strong> will also have the <strong>getUsername()</strong> that will return a String, in this case we will adopt the email itself as the user's username, which can also be the user's name if we prefer, as long as it's a String. We also have some methods to be able to control the user if we need to, such as <strong>isAccountNonExpired()</strong> which serves to test if the account is not expired, <strong>isAccountNonLocked()</strong>, <strong>isCredentialsNonExpired()</strong>, or the <strong>isEnabled()</strong> method to tell if the user is enabled or not

The <strong>UserDetailsService</strong> is an interface that has the <strong>loadUserByUsername()</strong> method, that is, let's give the user's name, and it will return the <strong>UserDetails</strong> object for us. We will have to implement this method, given the user's email it will return the <strong>UserDetails</strong> object. Lastly this method can throw a <strong>UsernameNotFoundException</strong>

### Class for web security configuration


- WebSecurityConfigurerAdapter

In addition to having to configure or implement the <strong>UserDetails</strong> and <strong>UserDetailsService</strong> interfaces, we will have to configure a web security class that extends from the <strong>WebSecurityConfigurerAdapter</strong>

### Bean to perform authentication

- AuthenticationManager

We will also have to make a Bean to perform authentication, of the type <strong>AuthenticationManager</strong>


Briefly, this is what we have to implement to integrate <strong>Spring Security</strong>

<hr>

# 03-20 Spring OAuth and JWT Checklist

<strong>Spring Cloud OAuth2</strong> is a library that we will implement the <strong>OAuth2</strong> pattern in our application. We will even use the <strong>JWT</srtong> token

# Spring Cloud OAuth2

<p align="center">
  <img width="" height="" src="https://user-images.githubusercontent.com/22635013/152515983-0a3ce909-51eb-490d-ad08-b48d6b31ba3b.png">
</p>

<strong>OAuth2</strong> provides for the three clients, the client application that will access the backend, and then we will have the <strong>Authorization server</strong> and the <strong>Resource server</strong> and this two guys can be in the same application or different applications

### Configuration class for Authorization Server

<ul>
	<li>AuthorizationServerConfigurerAdapter</li>
</ul>

To implement the <strong>Authorization server</strong> and the <strong>Resource server</strong> we will have to place the dependency <strong>Spring Cloud OAuth2</strong> in the application and then if you want to implement your system as an <strong>Authorization server</strong>, you will have to implement the <strong>AuthorizationServerConfigurerAdapter</strong>, which is a class that already comes here in this library


## Configuration class for Resource server

- ResourceServerConfigurerAdapter

If you want to implement the <strong>Resource server</strong> which is the guy who decides whether or not you can access the resources, depending on the token, then you will have to implement the <strong>ResourceServerConfigurerAdapter</strong>

## Beans to implement the JWT pattern

- JwtAccessTokenConverter
- JwtTokenStore	

If you also want your <strong>OAuth2</strong> to use the <strong>JWT</strong> pattern for the token, you will have to provide two beans, they are the <strong>JwtAccessTokenConverter</strong> and the <strong>JwtTokenStore</strong>

<hr>

# 03-21 Adjusting the pom.xml file

Before we start our <strong>Spring Security</strong> implementation, we have to update our <strong>pom.xml</strong> file as per the file below.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.4.4</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.devsuperior</groupId>
	<artifactId>dscatalog</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>dscatalog</name>
	<description>DSCatalog Bootcamp DevSuperior</description>

	<properties>
		<java.version>11</java.version>
		<spring-cloud.version>Hoxton.SR8</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>

		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>

		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.security.oauth.boot</groupId>
			<artifactId>spring-security-oauth2-autoconfigure</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
</project>
```
The <strong>pom.xml</strong> must be with the version of springboot 2.4.4, version of java 11, in the dependencies we must have the spring-boot-starter-web, the spring-boot-starter-data-jpa, h2, postgresql, we will also have spring-boot-starter-validation, the tests part with spring-boot-starter-test and junit-vintage-engine and then we will also have spring-security-test, and spring-security-oauth2-autoconfigure. For this to work there is a detail at the beginning of the properties we have to have <spring-cloud.version>Hoxton.SR8</spring-cloud.version> and at the end of the file we have to put all the dependencyManagement code

<hr>

# 03-22 Implementing Spring Security Checklist

Let's start the implementation of the <strong>Authorization server</strong>

Let's make a request where I offer the credentials, and the system returns the token <strong>JWT</strong> already with the <strong>Oauth2</strong>

Let's start implementing based on the <strong>checklist</strong> from topic <strong>03-19 Spring Security Checklist</strong>

Let's start by implementing the <strong>UserDetails</strong> interface

As we already have an entity that is already a user and already has the email, I can implement the interface in the <strong>user</strong> class and we implement the methods of this interface

<i>Excerpt of the <strong>User</strong> class implementing <strong>UserDetails</strong></i>

```java
import org.springframework.security.core.userdetails.UserDetails;

@Entity
@Table(name = "tb_user")
public class User implements UserDetails, Serializable {
	
	(...)

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public String getUsername() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public boolean isAccountNonExpired() {
		// TODO Auto-generated method stub
		return false;
	}

	@Override
	public boolean isAccountNonLocked() {
		// TODO Auto-generated method stub
		return false;
	}

	@Override
	public boolean isCredentialsNonExpired() {
		// TODO Auto-generated method stub
		return false;
	}

	@Override
	public boolean isEnabled() {
		// TODO Auto-generated method stub
		return false;
	}
```

We have to implement the <strong>getAuthorities()</strong>, <strong>getUsername()</strong> methods, and the 4 user-related tests <strong>isAccountNonExpired()</strong>, <strong>isAccountNonLocked()</strong>, <strong>isCredentialsNonExpired()</strong>, <strong>isEnabled()</strong>


The <strong>getUsername()</strong> method, which returns a String, will actually return the guy's email. Not forgetting that email is a user attribute

```java
	@Override
	public String getUsername() {
		return email;
	}
```

The <strong>getAuthorities()</strong> is to return a List, or a Collection of type <strong>GrantedAuthority</strong>, then our user has an association with the <strong>roles</strong>

I will loop through this collection of roles and convert each element that is of type <strong>role</strong> to an element of type <strong>GrantedAuthority</strong>


```java
(...)
private Set<Role> roles = new HashSet<>();
(...)
@Override
public Collection<? extends GrantedAuthority> getAuthorities() {
	return roles.stream().map(role -> new SimpleGrantedAuthority(role.getAuthority()))
			.collect(Collectors.toList());
}
```
At the moment we are not worried about implementing the other methods so we will just put the <strong>return true</strong>

With this we finish our implementation of <strong>UserDetails</strong>

<i></i>

```java
package com.devsuperior.dscatalog.entities;

import java.io.Serializable;
import java.util.Collection;
import java.util.HashSet;
import java.util.Objects;
import java.util.Set;
import java.util.stream.Collectors;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.JoinTable;
import javax.persistence.ManyToMany;
import javax.persistence.Table;

import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

@Entity
@Table(name = "tb_user")
public class User implements UserDetails, Serializable {
	private static final long serialVersionUID = 1L;
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	
	private String firstName;
	private String lastName;
	
	@Column(unique = true)
	private String email;
	private String password;
	
	@ManyToMany(fetch = FetchType.EAGER)
	@JoinTable(name = "tb_user_role",
		joinColumns = @JoinColumn(name = "user_id"),
		inverseJoinColumns = @JoinColumn(name = "role_id"))	
	private Set<Role> roles = new HashSet<>();
	
	public User() {	
	}

	public User(Long id, String firstName, String lastName, String email, String password) {
		this.id = id;
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
		this.password = password;
	}

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}
	
	public Set<Role> getRoles() {
		return roles;
	}

	@Override
	public int hashCode() {
		return Objects.hash(id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		User other = (User) obj;
		return Objects.equals(id, other.id);
	}

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		return roles.stream().map(role -> new SimpleGrantedAuthority(role.getAuthority()))
				.collect(Collectors.toList());
	}

	@Override
	public String getUsername() {
		return email;
	}

	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	@Override
	public boolean isAccountNonLocked() {
		return true;
	}

	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}

	@Override
	public boolean isEnabled() {
		return true;
	}
	
}
```

Continuing with our project we have to implement the <strong>UserDetailsService</strong>, which will be a service to retrieve the user by email

Since we already have the <strong>UserService</strong>, let's implement it here, and add the missing <strong>loadUserByUsername()</strong> method


<i>Excerpt from <strong>UserService</strong> class implementing <strong>UserDetailsService</strong></strong></i>

```java
package com.devsuperior.dscatalog.services;
(...)
import org.springframework.security.core.userdetails.UserDetailsService;
(...)

@Service
public class UserService implements UserDetailsService {

(...)

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		// TODO Auto-generated method stub
		return null;
	}	
```

We will have to implement the <strong>loadUserByUsername(String username)</strong> method, which will actually be the email, it will return the <strong>UserDetails</strong>, which will actually be the <strong>User </strong> because it is implementing the <strong>UserDetails</strong> interface


Bear in mind that in the <strong>UserRepository</strong> class we already have the <code>User findByEmail(String email)</code> method, let's use this method. If the email, however, does not exist, I will have to throw an exception <strong>UsernameNotFoundException</strong>, but if I find the email I will return the user

<i>Excerpt from the <atrong>UserService</strong> class extending from the <strong>UserDetailsService</strong> class implementing the <code>loadUserByUsername(String username)</code> method</i>

```java
@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
	
	User user = repository.findByEmail(username);
	if(user == null) {
		throw new UsernameNotFoundException("Email not found");
	}		
	return user;
}
```

Let's implement a trick here, using a <strong>Logger</strong> object

The <strong>Logger</strong> will print messages in the console, obeying that pattern there of what is warn and what is error

For this I will instantiate a <strong>Logger</strong> in the class itself, with the <strong>LoggerFactory</strong> pointing to our <strong>UserService</strong>


```java
package com.devsuperior.dscatalog.services;

import java.util.Optional;

import javax.persistence.EntityNotFoundException;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

(...)

@Service
public class UserService implements UserDetailsService {

	private static Logger logger = LoggerFactory.getLogger(UserService.class);

(...) 
```
To help keep track of the check to see if <strong>Spring Security</strong> is actually working, let's test

If the user is null, it means that the test is true, that is, the user was not found, in this case we throw a <code>logger.error</code> with the message in the console <code>"User not found " + username</code>

 In the opposite case, if the user is found, we launch a <code>logger.info</code> with the message <code>"User found " + username</code>

 This is just to help in the tests, because when we look at the console, we can have more clues of an error that may happen

 ```java
 @Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
	
	User user = repository.findByEmail(username);
	if(user == null) {
		logger.error("User not found: " + username);
		throw new UsernameNotFoundException("Email not found");
	}
	logger.info("User found: " + username);
	return user;
}

```
We have just configured our second <strong>Spring Security</strong> item, the <strong>UserDetailsService</strong>


We have just configured our second <strong>Spring Security</strong> item, the <strong>UserDetailsService</strong>

Let's now implement the Class for web security configuration, the <strong>WebSecurityConfigurerAdapter</strong>

In fact, this class has already been implemented, as we can see below. We will just need to elaborate this configuration better

<i><strong>SecurityConfig</strong> class that extends from the <strong>WebSecurityConfigurerAdapter</strong> class</i>

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	public void configure(WebSecurity web) throws Exception {
		web.ignoring().antMatchers("/**");
	}
}
```


Let's rename this <strong>SecurityConfig</strong> class that extends from the <strong>WebSecurityConfigurerAdapter</strong> class</strong> to <strong>WebSecurityConfigurerAdapter</strong>

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	public void configure(WebSecurity web) throws Exception {
		web.ignoring().antMatchers("/**");
	}
}

```

We have already overwritten a method called <code>configure</code> receiving the <code>WebSecurity</code> to free up all application paths for us

At this point, using <strong>OAuth2</strong> we will have to modify

```code
web.ignoring().antMatchers("/**");
```
to

```code
web.ignoring ().antMatchers("/actuator/**");
```

The <strong>actuator</strong> is a library of the <strong>Spring cloud OAuth2</strong> uses to pass it here in requests. Actually, we're releasing everything again, but going through this library that <strong>Spring cloude OAuth2</strong> uses

Another method we're going to have to override is an overload of this <strong>configure</strong> but with another parameter

To import the method, follow the example below:

<p align="center">
<image src="https://user-images.githubusercontent.com/22635013/152972530-5af35bc3-3bd7-4677-be7a-58be08f0eda9.png">
</p>

We select <strong>configure(AuthenticationManagerBuilder)</strong>

<p align="center"> 
<image src="https://user-images.githubusercontent.com/22635013/152970007-e8916263-bbbf-4759-ba32-e3cf8d5c6f8e.JPG">
</p>


```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
	// TODO Auto-generated method stub
	super.configure(auth);
}
```
We will have to configure the algorithm that we are using to encrypt, which is the <strong>BCryptPasswordEncoder</strong> and I will also have to configure who my <strong>UserDetailsService</strong> is, which is the interface that we have just to implement

In order to configure this, we will have to inject the <strong>BCryptPasswordEncoder</strong> and the <strong>UserDetailsService</strong>

```java
package com.devsuperior.dscatalog.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
	
	@Autowired
	private BCryptPasswordEncoder passwordEncoder;
	
	@Autowired
	private UserDetailsService userdetailsService;
	
	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		// TODO Auto-generated method stub
		super.configure(auth);
	}

	@Override
	public void configure(WebSecurity web) throws Exception {
		web.ignoring().antMatchers("/actuator/**");
	}
}
```
We will have to configure the <strong>BCryptPasswordEncoder</strong> and the <strong>UserDetailsService</strong> in the <strong>configure(AuthenticationManagerBuilder auth)</strong> method

```java
@Autowired
private BCryptPasswordEncoder passwordEncoder;

@Autowired
private UserDetailsService userdetailsService;

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
	auth.userDetailsService(userdetailsService).passwordEncoder(passwordEncoder);
}
```


From now on, <strong>Spring Security OAuth2</strong>, when authenticating, will know how to search for the user by email, through <strong>userDetailService</strong> and how to analyze the encrypted password, via <strong>passwordEncoder</strong>

Only one detail is missing. We have to configure a <strong>Bean</strong> for authentication, the <strong>AuthenticationManager</strong>

The <strong>AuthenticationManager</strong> comes as a method of the <strong>WebSecurityConfigurerAdapter</strong> class. Actually this <strong>Bean</strong>, will be an override of this class


To import the method, follow the example below:

<p align="center">
<image src="https://user-images.githubusercontent.com/22635013/152972530-5af35bc3-3bd7-4677-be7a-58be08f0eda9.png">
</p>

We select the <strong>authenticationManager</strong>:

<p align="center">
	<img src="https://user-images.githubusercontent.com/22635013/152974359-cc8e2a10-9338-4459-8804-600b2cfa17eb.JPG">
</p>

Once imported, we added the @Bean notation, and its implementation is the one here

```java
@Override
@Bean
protected AuthenticationManager authenticationManager() throws Exception {
	return super.authenticationManager();
}
```

We had to do this explicitly here so that <strong>AuthenticationManager</strong> would also be a component of the system here. When I implement the <strong>Authorization Server</strong> we will need this object here

With this here, we've just implemented the <strong>Spring Security</strong> checklist

Next we will implement the <strong>Authorization Server</strong> with the <strong>AuthorizationServerConfigurerAdapter</strong> class

<hr>

# 03-23 Implementing OAuth2 authorization server

Let's implement the <strong>Authorization server</strong>

According to the <a href="https://github.com/leninepestana/dscatalog-validation-security/tree/master/backend#spring-cloud-oauth2">03-20 Spring OAuth and JWT Checklist</a> 
it is indicated that the <strong>JWT</strong> Beans should be implemented:

#### Beans to implement the JWT pattern

- JwtAccessTokenConverter
- JwtTokenStore

So we will have to implement the two Beans from above

We already have this implementation code prepared, we can get it from <a href="https://github.com/leninepestana/dscatalog-validation-security#beans-for-jwt-token">Beans for JWT token</a>


We can also copy the code below, with CTRL-C and do CTRL+V inside the <strong>AppConfig</strong> class, right after the <strong>BCryptPasswordEncoder</strong> method

```java
@Bean
public JwtAccessTokenConverter accessTokenConverter() {
	JwtAccessTokenConverter tokenConverter = new JwtAccessTokenConverter();
	tokenConverter.setSigningKey("MY-JWT-SECRET");
	return tokenConverter;
}

@Bean
public JwtTokenStore tokenStore() {
	return new JwtTokenStore(accessTokenConverter());
}
```

These 2 Beans are objects that will be able to access the <strong>JWT</strong>, that is, read the token, decode the token, create a token by encoding it

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.provider.token.store.JwtAccessTokenConverter;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

@Configuration
public class AppConfig {

	@Bean
	public BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
	
	@Bean
	public JwtAccessTokenConverter accessTokenConverter() {
		JwtAccessTokenConverter tokenConverter = new JwtAccessTokenConverter();
		tokenConverter.setSigningKey("MY-JWT-SECRET");
		return tokenConverter;
	}

	@Bean
	public JwtTokenStore tokenStore() {
		return new JwtTokenStore(accessTokenConverter());
	}
}
```

We can even notice that in the configuration of the <strong>JwtAccessTokenConverter</strong> we register our signature <code>tokenConverter.setSigningKey("MY-JWT-SECRET")</code>, and here we put the secret key directly in the code
and finally we return

We also need another Bean of type <strong>JwtTokenStore</strong> that serves to access the token, passing as an argument the <strong>accessTokenConverter()</strong>

```java
@Bean
public JwtTokenStore tokenStore() {
	return new JwtTokenStore(accessTokenConverter());
}
```


We will inject these two tokens into our class which will be the <strong>Authorization server</strong> which is the main actor of our <strong>OAuth2</strong>

We have to build a class to implement the <strong>Authorization server</strong>, and it must have access to <strong>JwtAccessTokenConverter</strong> and <strong>JwtTokenStore</strong>



<p align="center">
	<img src="https://user-images.githubusercontent.com/22635013/153176910-9bb20a42-9c59-486a-bef3-2c53fbcb8633.png">
</p>



## Implementing the Authorization server

### Spring Cloud OAuth2

Configuration class for <strong>Authorization server</strong>

<ul>
	<li>AuthorizationServerConfigurerAdapter</li>
</ul>

The <strong>AuthorizationServerConfigurerAdapter</strong> class will be created inside the <code>com.devsuperior.dscatalog.config</code> package and we will call this class <strong>AuthorizationServerConfig</strong>

<p align="center">
	<img src="https://user-images.githubusercontent.com/22635013/153178509-213adb7f-db8d-4687-a932-c51c5bd4eb48.JPG">
</p>

<i>The <strong>AuthorizationServerConfig</strong> class will extend the class <strong>AuthorizationServerConfigurerAdapter</strong></i>

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

}
```
We put the notation <code>@Configuration</code> to say that it is a configuration class and we also put the main and most important notation which is the <code>@EnableAuthorizationServer</code>

The notation <code>@EnableAuthorizationServer</code> is going to do a processing behind the scenes to say that this class here is going to represent the <strong>Authorization Server</strong> of <strong>OAuth2</strong>

To implement this configuration we will have to override 3 configurer methods of the <strong>AuthorizationServerConfigurerAdapter</strong> class

<p align="center">
	<img src="https://user-images.githubusercontent.com/22635013/153182466-942e8faf-9c47-4260-b90f-fda1dc142d45.png">
</p>

```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerEndpointsConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerSecurityConfigurer;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

	@Override
	public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
		// TODO Auto-generated method stub
		super.configure(security);
	}

	@Override
	public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
		// TODO Auto-generated method stub
		super.configure(clients);
	}

	@Override
	public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
		// TODO Auto-generated method stub
		super.configure(endpoints);
	}

}
```
We will inject the objects that we will need:

Let's inject the objects we will need:
<ul>
<li>BCryptPasswordEncoder</li>
<li>JwtAccessTokenConverter</li>
<li>JwtTokenStore</li>
</ul>

and 

finally we will also have to inject an object that we built in the <strong>WebSecurityConfig</strong> class:
<ul>
<li>AuthenticationManager</li>
</ul>

```java
package com.devsuperior.dscatalog.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerEndpointsConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerSecurityConfigurer;
import org.springframework.security.oauth2.provider.token.store.JwtAccessTokenConverter;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

	@Autowired
	private BCryptPasswordEncoder passwordEncoder;
	
	@Autowired
	private JwtAccessTokenConverter accessTokenConverter;
	
	@Autowired
	private JwtTokenStore tokenStore;
	
	@Autowired
	private AuthenticationManager authenticationManager;
	
	@Override
	public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
		// TODO Auto-generated method stub
		super.configure(security);
	}

	@Override
	public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
		// TODO Auto-generated method stub
		super.configure(clients);
	}

	@Override
	public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
		// TODO Auto-generated method stub
		super.configure(endpoints);
	}

}
```
Let's configure the 3 methods we have to override

#### 💢 On the *AuthorizationServerSecurityConfigurer* object we must be careful to type the name of the methods since it is String and the compiler does not help with errors

```java
@Override
public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
	security.tokenKeyAccess("permitAll()").checkTokenAccess("isAuthenticated()");
}
```

#### 💢  Configure receiving *ClientDetailsServiceConfigurer* object

It is in this object that we will define how our authentication will be and what the customer data will be
When authenticating, we have to indicate the user's credentials and the application's credentials.
In this method we will configure the application credentials and other details

- <code> inMemory()</code> indicates that this process will be in memory
- <code>withClient("dscatalog")</code> will define the clientId, that is, the name of the application
- <code>secret(passwordEncoder.encode("dscatalog123"))</code> will set the application password encrypted with **BCrypt**
- <code>scopes("read", "write")</code> the access type, in our case, will be read and write
- <code>authorizedGrantTypes("password")</code> type of access, or login
- <code>accessTokenValiditySeconds(86400)</code> token expiration time

```java
@Override
public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
	clients.inMemory()
	.withClient("dscatalog")
	.secret(passwordEncoder.encode("dscatalog123"))
	.scopes("read", "write")
	.authorizedGrantTypes("password")
	.accessTokenValiditySeconds(86400);
}
```
Finally

#### 💢  Configure the *AuthorizationServerSecurityConfigurer*

This is where I'll say who I'm going to authorize, and what the token format will be

```java
@Override
public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
	endpoints.authenticationManager(authenticationManager)
	.tokenStore(tokenStore)
	.accessTokenConverter(accessTokenConverter);
}
```

### Testing our application in *Postman*

We run the Spring **Tool Suite application** and if there are no errors we can switch to *Postman*

We will have to inform in the *Postman* the following

**App Creedentials**
- **client_id**: myappname123
- **client_secret**: myappsecret123

**Request Header**
- Authorization: "Basic" + Base64.encode(client_id + ":" + client_secret)

**User Credentials**
- **username**: alex@gmail.com
- **password**: 123456
- **grant_type**: password

Body:x-www-form-urlencoded

In *Postman*, we create a new folder with the name of **Auth**, inside this folder we will configure the *login*


*We create an* **Auth** *folder and add a request named* **Login** then we configure the accesses

<p>
<img align="center" src="https://user-images.githubusercontent.com/22635013/153293192-bcbdc212-73e6-4a2b-956c-f35368f82df6.png">
</p>

In the image above, the {{host}} is the equivalent of having http://localhost:8080

The <code>/oauth/token</code> endpoint is given by the *Spring Framework*, it is not necessary to configure an endpoint

Also, still in the image above, we pass the parameters of the application, which must take into account the type of authorization, the user and the password

The username and password we configure in the *AuthorizationServerConfig* class and must be configured in the *Postman* according to our configuration in the application

```java
@Override
public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
	clients.inMemory()
	.withClient("dscatalog")
	.secret(passwordEncoder.encode("dscatalog123"))
	.scopes("read", "write")
	.authorizedGrantTypes("password")
	.accessTokenValiditySeconds(86400);
}
```

<p>
<img align="center" src="https://user-images.githubusercontent.com/22635013/153294551-82573b50-972e-4817-aeb5-7aa193d4d00e.png">
</p>

According to the image above, in the **Body**, we configure the *User credentials*, that is, the **username**, the **password** and the **grant_type** fields with the configured values , let's get the script that we inserted in the **database**

```sql
INSERT INTO tb_user (first_name, last_name, email, password) VALUES ('Alex', 'Brown', 'alex@gmail.com', '$2a$10$eACCYoNOHEqXve8aIWT8Nu3PkMXWBaOxJ9aORUYzfMQCbVBIhZ8tG');
INSERT INTO tb_user (first_name, last_name, email, password) VALUES ('Maria', 'Green', 'maria@gmail.com', '$2a$10$eACCYoNOHEqXve8aIWT8Nu3PkMXWBaOxJ9aORUYzfMQCbVBIhZ8tG');
```
https://bcrypt-generator.com/

<p>
<img align="center" src="https://user-images.githubusercontent.com/22635013/153373057-ac0e2929-d5e1-4a79-a12d-c3c6a4b7f67d.png">
</p>


At the end of the *Login* configuration, we save the configuration, and according to the image below, a token is generated


<p>
<img align="center" src="https://user-images.githubusercontent.com/22635013/153374428-28f0311a-b3a3-4965-91db-e58bb9290e21.png">
</p>


If we copy the generated token and go to https://jwt.io/ and paste it into *debugger*, we will see the information contained in the token, separated by *HEADER*, *PAYLOAD* and *SIGNATURE*

<p>

```code
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2NDQ1NjkxODMsInVzZXJfbmFtZSI6ImFsZXhAZ21haWwuY29tIiwiYXV0aG9yaXRpZXMiOlsiUk9MRV9PUEVSQVRPUiJdLCJqdGkiOiI3ZjE0MmY0OC00MDUyLTRlMmItOTdmMC1lNDM3OTQ3NzA4YjAiLCJjbGllbnRfaWQiOiJkc2NhdGFsb2ciLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXX0._jUDkxk5IiFXRFzhL8l6eFW52vG-r8q1vJ8fdJwHngk",
    "token_type": "bearer",
    "expires_in": 86399,
    "scope": "read write",
    "jti": "7f142f48-4052-4e2b-97f0-e437947708b0"
}
```
</p>

In the **UserService** class we instantiate a **Loger** object and print in the console if there was an error or if the user was found

```java
@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
	
	User user = repository.findByEmail(username);
	if(user == null) {
		logger.error("User not found: " + username);
		throw new UsernameNotFoundException("Email not found");
	}
	logger.info("User found: " + username);
	return user;
}	
```

Let's test this **debugger** by running the application again in the *Spring Tool Suite* and in the *Postman* we'll do **send** again to send the *Login*

![user_found](https://user-images.githubusercontent.com/22635013/153382401-c2b18e16-054a-4479-ac51-3ddaeccee754.png)

In this case, since the email is valid, the console returned the value <code>User found: alex@gmail.com</code>, but if the email was not valid the console would return the value <code>User not found: alex123@gmail.com</code> for example

*Postman* would give *400 Bad Request error* if user email does not exist in *Database*

Generates *Error 401* if we try to access a protected resource and the token is invalid

Generates *Error 403* when we try to access a resource with a valid token but the profile does not allow access to that resource

<hr>

## 03-24 Externalizing configuration, environment variables

We have to externalize the hardcode codes that we configured earlier

 The *client_id*, the *client_secret*, the *validity_seconds*, which are in the *AuthorizationServerConfig* class

We will also have to externalize the MY-JWT-SECRET that is in the *AppConfig* class



*Code snippet from the **application.properties** file in which we will place the environment variables*

```xml
spring.profiles.active=test

spring.jpa.open-in-view=false
```

We copy the code below and replace it in the *application.properties* file

Basic environment variables after adding security

```xml
spring.profiles.active=${APP_PROFILE:test}

spring.jpa.open-in-view=false

security.oauth2.client.client-id=${CLIENT_ID:dscatalog}
security.oauth2.client.client-secret=${CLIENT_SECRET:dscatalog123}

jwt.secret=${JWT_SECRET:MY-JWT-SECRET}
jwt.duration=${JWT_DURATION:86400}
```
In the code above, we just put a reference to an environment variable

We changed the code <code>spring.profiles.active=test</code> to <code>spring.profiles.active=${APP_PROFILE:test}</code>, with that we named the environment variable APP_PROFILE

The environment variable, in this context, will store a value in the system of the environment where the application is running

With that, we can say that we can go to our windows pc and configure an environment variable for our application. I can even put an environment variable inside Heroku, or even in the Docker container, with a value I want

In this case, the value of the variable will be what is configured here, but if the environment variable is not defined, by default I will get the value *test*, <code>APP_PROFILE:test</code>

I will do the same with the other variables, if they are not defined, they will get the default value
- ${CLIENT_ID:dscatalog}
- ${CLIENT_SECRET:dscatalog123}
- ${JWT_SECRET:MY-JWT-SECRET}
- ${JWT_DURATION:86400}

Now inside our code, we have to get these values

In the *AppConfig* class we have to change the code below

```java
@Bean
public JwtAccessTokenConverter accessTokenConverter() {
	JwtAccessTokenConverter tokenConverter = new JwtAccessTokenConverter();
	tokenConverter.setSigningKey("MY-JWT-SECRET");
	return tokenConverter;
}
```
we change the code to

Instead of putting ***MY-JWT-SECRET*** directly into the code, we create an internal variable in the *AppConfig* class, and use a trick to read the value defined in the configuration file, which in this case was given the value of *jwt.secret*

The final code from *AppConfig* will look like the example below

```java
package com.devsuperior.dscatalog.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.provider.token.store.JwtAccessTokenConverter;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

@Configuration
public class AppConfig {

	@Value("${jwt.secret}")
	private String jwtSecret;
	
	@Bean
	public BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
	
	@Bean
	public JwtAccessTokenConverter accessTokenConverter() {
		JwtAccessTokenConverter tokenConverter = new JwtAccessTokenConverter();
		tokenConverter.setSigningKey(jwtSecret);
		return tokenConverter;
	}

	@Bean
	public JwtTokenStore tokenStore() {
		return new JwtTokenStore(accessTokenConverter());
	}
}
```

We will do the same procedure in the *AuthorizationServerConfig* class

*Current code in the **AuthorizationServerConfig** class before we change anything*

```java
package com.devsuperior.dscatalog.config;

(...)

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

	@Autowired
	private BCryptPasswordEncoder passwordEncoder;
	
	@Autowired
	private JwtAccessTokenConverter accessTokenConverter;
	
	@Autowired
	private JwtTokenStore tokenStore;
	
	@Autowired
	private AuthenticationManager authenticationManager;
	
	@Override
	public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
		security.tokenKeyAccess("permitAll()").checkTokenAccess("isAuthenticated()");
	}

	@Override
	public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
		clients.inMemory()
		.withClient("dscatalog")
		.secret(passwordEncoder.encode("dscatalog123"))
		.scopes("read", "write")
		.authorizedGrantTypes("password")
		.accessTokenValiditySeconds(86400);
	}

	@Override
	public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
		endpoints.authenticationManager(authenticationManager)
		.tokenStore(tokenStore)
		.accessTokenConverter(accessTokenConverter);
	}

}
```
Let's create the variables
- clientId
- clientSecret
- client_passwor
- jwtDurattion

*Final code in the **AuthorizationServerConfig** class after we cretate the the varibles*

```java
package com.devsuperior.dscatalog.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerEndpointsConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerSecurityConfigurer;
import org.springframework.security.oauth2.provider.token.store.JwtAccessTokenConverter;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {
	
	@Value("${security.oauth2.client.client-id}")
	private String clientId;
	
	@Value("${security.oauth2.client.client-secret}")
	private String clientSecret;
	
	@Value("${jwt.duration}")
	private Integer jwtDuration;
	
	@Autowired
	private BCryptPasswordEncoder passwordEncoder;
	
	@Autowired
	private JwtAccessTokenConverter accessTokenConverter;
	
	@Autowired
	private JwtTokenStore tokenStore;
	
	@Autowired
	private AuthenticationManager authenticationManager;
	
	@Override
	public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
		security.tokenKeyAccess("permitAll()").checkTokenAccess("isAuthenticated()");
	}

	@Override
	public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
		clients.inMemory()
		.withClient(clientId)
		.secret(passwordEncoder.encode(clientSecret))
		.scopes("read", "write")
		.authorizedGrantTypes("password")
		.accessTokenValiditySeconds(jwtDuration);
	}

	@Override
	public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
		endpoints.authenticationManager(authenticationManager)
		.tokenStore(tokenStore)
		.accessTokenConverter(accessTokenConverter);
	}

}
```
At the end of our implementation we will test, and see if it worked
In ***Spring Tool Suite*** let's restart the application

Again, in ***Postman*** we will run the login request

<p>
<img align="center" src="https://user-images.githubusercontent.com/22635013/153374428-28f0311a-b3a3-4965-91db-e58bb9290e21.png">
</p>

According to the image above, the request worked, with the difference that we are now externalizing our configuration

<hr>

## 03-25 Adding information to the token

Let's learn how to add information to the token

For example, assuming it is important for the application, to know in the token itself, the *id* of the user, or the *firstName* of the user

By default, we check that **PAYLOAD** already has the *user_name*, and the *ROLE_OPERATOR*

Let's learn to put additional data, taking advantage of the framework

To do this we have to implement a component called *TokenEnhancer*

The *TokenEnhancer* is not necessarily a service, it's more of a utility, therefore, and that's why we're going to implement it in a package that we'll call *components*

We are going to create a class inside the **components** package and we will call it *JwtTokenEnhancer* and this class will implement the *TokenEnhancer*

We will have to implement the **@override** method *OAuth2AccessToken enhance(...)* that will enter the life cycle of the token, and when generating the token, it will add the objects that I pass


```java
package com.devsuperior.dscatalog.components;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.oauth2.common.DefaultOAuth2AccessToken;
import org.springframework.security.oauth2.common.OAuth2AccessToken;
import org.springframework.security.oauth2.provider.OAuth2Authentication;
import org.springframework.security.oauth2.provider.token.TokenEnhancer;
import org.springframework.stereotype.Component;

import com.devsuperior.dscatalog.entities.User;
import com.devsuperior.dscatalog.repositories.UserRepository;

@Component
public class JwtTokenEnhancer implements TokenEnhancer {

	@Autowired
	private UserRepository userRepository;
	
	@Override
	public OAuth2AccessToken enhance(OAuth2AccessToken accessToken, OAuth2Authentication authentication) {
		
		User user = userRepository.findByEmail(authentication.getName());
		
		Map<String, Object> map = new HashMap<>();
		map.put("userFirstName", user.getFirstName());
		map.put("userId", user.getId());
		
		DefaultOAuth2AccessToken token = (DefaultOAuth2AccessToken) accessToken;
		token.setAdditionalInformation(map);
		
		return accessToken;
	}


}

```

To access the *id* of the user or the name of the user, I will have to access the user, and for that I will have to access the ***UserRepository***

The email, which in our case is like the username, will be instantiated inside the *authentication* object

- <code>User user = userRepository.findByEmail(authentication.getName());</code>

I can then add to the token whatever I want. To add objects to the token, I'll have to put these objects in the form of a map here in java, in the form of a pair, key and value. Let's instantiate a *Map*

The key type will be *String* and the value type will be *Object* because it can be any object type

- <code>Map<String, Object> map = new HashMap<>();</code>

In our case we will want to insert the first name of the user in the token, which will be the *user* that I instantiated from the **userRepository**

- <code>map.put("userFirstName", user.getFirstName());</code>

We will also want to enter the *userId*

- <code>map.put("userId", user.getId());</code>

To insert the values into the token, I will have to access the ***accessToken*** object, but the method to insert the token is not of the same type as the ***OAuth2AccessToken***, it is of a more specific type

I'll have to do a *downcast* and it will look like this
- <code>DefaultOAuth2AccessToken token = (DefaultOAuth2AccessToken) accessToken;</code>

After doing the *downcast* I can add the information contained in the map
- <code>token.setAdditionalInformation(map);</code>

Finally I return the *accessToken*, but we could also return the *token*, since it is a more specific type
- <code>return accessToken;</code>
- or <code>return token;</code>

### 💢  For all this information to be updated when generating the token, we will have to update our *AuthorizationServerConfig*


We will have to inject the *JwtTokenEnhancer* in the *AuthorizationServerConfig* class, creating it just before the first method

We will have to change the configuration that is receiving the *endpoints*, and we will instantiate an object of type *TokenEnhancerChain*

- <code>TokenEnhancerChain chain = new TokenEnhancerChain();</code>

The *TokenEnhancerChain* will be the *tokenEhhancer* and the *accessTokenConverter*, a list with these two
- <code>chain.setTokenEnhancers(Arrays.asList(accessTokenConverter, tokenEhhancer));</code>


At the end of the **configure(AuthorizationServerEndpointsConfigurer endpoints)** method, we add the *.token Enhancer(chain)*, making the method look like this

```java
@Override
public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
	
	TokenEnhancerChain chain = new TokenEnhancerChain();
	chain.setTokenEnhancers(Arrays.asList(accessTokenConverter, tokenEhhancer));
	
	endpoints.authenticationManager(authenticationManager)
	.tokenStore(tokenStore)
	.accessTokenConverter(accessTokenConverter)
	.tokenEnhancer(chain);
}
```

*Class **AuthorizationServerConfig** after configuring **JwtTokenEnhancer***

```java
package com.devsuperior.dscatalog.config;

import java.util.Arrays;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerEndpointsConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerSecurityConfigurer;
import org.springframework.security.oauth2.provider.token.TokenEnhancerChain;
import org.springframework.security.oauth2.provider.token.store.JwtAccessTokenConverter;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

import com.devsuperior.dscatalog.components.JwtTokenEnhancer;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {
	
	@Value("${security.oauth2.client.client-id}")
	private String clientId;
	
	@Value("${security.oauth2.client.client-secret}")
	private String clientSecret;
	
	@Value("${jwt.duration}")
	private Integer jwtDuration;
	
	@Autowired
	private BCryptPasswordEncoder passwordEncoder;
	
	@Autowired
	private JwtAccessTokenConverter accessTokenConverter;
	
	@Autowired
	private JwtTokenStore tokenStore;
	
	@Autowired
	private AuthenticationManager authenticationManager;
	
	@Autowired
	private JwtTokenEnhancer tokenEhhancer;
	
	@Override
	public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
		security.tokenKeyAccess("permitAll()").checkTokenAccess("isAuthenticated()");
	}

	@Override
	public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
		clients.inMemory()
		.withClient(clientId)
		.secret(passwordEncoder.encode(clientSecret))
		.scopes("read", "write")
		.authorizedGrantTypes("password")
		.accessTokenValiditySeconds(jwtDuration);
	}

	@Override
	public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
		
		TokenEnhancerChain chain = new TokenEnhancerChain();
		chain.setTokenEnhancers(Arrays.asList(accessTokenConverter, tokenEhhancer));
		
		endpoints.authenticationManager(authenticationManager)
		.tokenStore(tokenStore)
		.accessTokenConverter(accessTokenConverter)
		.tokenEnhancer(chain);
	}

}
```

Now let's restart *Spring Tool Suite* and in *Postman* we send again the authentication request


![userFirstName_userId](https://user-images.githubusercontent.com/22635013/153570644-d23c8f49-2e02-4926-8476-c0ffe5e65068.png)

As we can see in the image above, *Postman* returned the normal data, and also added the firstName and userId

![userFirstName_userId_Maria](https://user-images.githubusercontent.com/22635013/153571629-0ece565b-293d-4f3e-b302-f826ddc5bbd5.png)

If we log in with Maria, we will see that the data will change

<hr>

# 03-26 Implementing OAuth2 resource server

Now that we've implemented the ***Authorization server***,
  which is the guy who receives the credentials and delivers a token, let's implement the ***Resource server*** now, who is the guy who receives the requests for a resource + the token he will know if he can deliver that resource

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153573737-47e7b725-20ec-4861-b31b-6ad23d73e160.png">
</p>

## Configuration class for Resource Server

- ***ResourceServerConfigurerAdapter***

As we said before, we will have to implement a class that implements the ***ResourceServerConfigurerAdapter*** class

In our project, in the config package, we will create a new class with the name of ResourceServerConfig that will extend the class ***ResourceServerConfigurerAdapter***

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153583082-23eca92e-2b3f-4848-a7c3-50f658548635.JPG">
</p>


```java
package com.devsuperior.dscatalog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.config.annotation.web.configuration.ResourceServerConfigurerAdapter;

@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {

}
```

We put the notations

- @Configuration
- <code>@EnableResourceServer</code> this is the most important one, because it will process under the hood for this class to implement the *OAuth 2* ***Resource server*** functionality

We'll have to do a two-method *@Override*

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153661436-8fc7429d-4a33-448b-9039-2c7512d9b1c6.png">
</p>



```java
@Override
public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
	// TODO Auto-generated method stub
	super.configure(resources);
}
```
💢 To configure the *ResourceServerConfigurerAdapter* class I will need the **@Bean TokenStore**

```java
@Autowired
private JwtTokenStore tokenStore;
```
We will configure the tokenStore in the <code>configure(ResourceServerSecurityConfigurer resources)</code> method

```java
@Override
public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
	resources.tokenStore(tokenStore);
}
```
With the implementation above, the part of working with the token is configured

With this implementation, the *Resource server* can now decode the token and analyze whether the token matches the secret, whether it is expired or not, and whether the token is valid.

💢 Another thing we have to configure is the routes, with *HttpSecurity*

```java
@Override
public void configure(HttpSecurity http) throws Exception {
	// TODO Auto-generated method stub
	super.configure(http);
}
```

In the design of our page, the part of browsing the products, viewing the paginated products, accessing the product details, all this is released. It is the standard of online stores, where the product catalog is released for you to browse

But there is a downside, the part of the page where we have the creation of products, creation of categories and creation of users, this part will be protected and will require a login

The **CRUD** of *profiles* and *categories* will be released for people with an ***OPERATOR*** profile, but the **CRUD** of users will only be possible to edit with the ***ADMIN*** profile

Let's make some routes to restrict these accesses

💢 We will define static variables for public routes so that all users can log in

```java
private static final String[] PUBLIC = {"/oauth/token"};
```
💢 We also have to define product and category routes, but only the endpoints that I will call with the GET verb

We also have to define product and category routes, but only the endpoints that I will call with the ***GET*** verb. Browsing the ***Products*** catalog implies a ***GET*** request, however, when I enter the ***Products CRUD*** to edit the Products, then I will have to ***POST***, ***PUT*** and ***DELETE***

Let's create another vector and call it *OPERATOR_OR_ADMIN*

We are going to create another vector and we are going to call it *OPERATOR_OR_ADMIN*, and we are going to put all the routes that will be released for the **OPERATOR** and the **ADMIN**

```java
private static final String[] OPERATOR_OR_ADMIN = {"/products/**", "/categories/**"};
```

In this case, 

- Access to all pages starting with <code>/products/**</code>

- Access to all pages starting with <code>/categories/**</code> 

and the ** indicates that they are all routes to from *products* and from *categories*

In the end we will do a trick to release only the GET routes of the vector *OPERATOR_OR_ADMIN*
 
💢 Let's define **ADMIN** routes in *ADMIN* vector


```java
private static final String[] ADMIN = {"/users/**"};
```
*Final implementation of the ***ResourceServerConfig class****

```java
package com.devsuperior.dscatalog.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;
import org.springframework.security.oauth2.config.annotation.web.configuration.ResourceServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configurers.ResourceServerSecurityConfigurer;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {

	@Autowired
	private JwtTokenStore tokenStore;
	
	private static final String[] PUBLIC = {"/oauth/token"};
	
	private static final String[] OPERATOR_OR_ADMIN = {"/products/**", "/categories/**"};
	
	private static final String[] ADMIN = {"/users/**"};
	
	@Override
	public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
		resources.tokenStore(tokenStore);
	}

	@Override
	public void configure(HttpSecurity http) throws Exception {
		
		http.authorizeRequests()
		.antMatchers(PUBLIC).permitAll()
		.antMatchers(HttpMethod.GET, OPERATOR_OR_ADMIN).permitAll()
		.antMatchers(OPERATOR_OR_ADMIN).hasAnyRole("OPERATOR", "ADMIN")
		.antMatchers(ADMIN).hasAnyRole("ADMIN")
		.anyRequest().authenticated();
	}
}

```
To set authorizations, we use <code>.antMatchers()</code>

Whoever is free to access my public routes should define it as <code>.antMatchers(PUBLIC).permitAll()</code>

We take the routes from *OPERATOR_OR_ADMIN*, and we are going to define the accesses for all, but only in the *GET* method, in the end we say that users of this vector will have permission

```java
.antMatchers(HttpMethod.GET, OPERATOR_OR_ADMIN).permitAll()
```

Here we take the *OPERATOR_OR_ADMIN* vector but we don't specify any method and with the <code>.hasAnyRole("OPERATOR", "ADMIN")</code> we specify that users who have the role of **OERATOR** or **ADMIN** will have access




Profiles are defined in our Database

*Code excerpt from the ***data.sql*** file to insert the roles in the Database*

```sql
INSERT INTO tb_role (authority) VALUES ('ROLE_OPERATOR');
INSERT INTO tb_role (authority) VALUES ('ROLE_ADMIN');
```
To register in the Database, the Framework forces us to put the prefix *ROLE_*  but to register the authorizations, just put the word **OPERATOR**, or **ADMIN**

The routes defined here can be accessed by anyone who has one of these OPERATOR or ADMIN roles

```java
.antMatchers(OPERATOR_OR_ADMIN).hasAnyRole("OPERATOR", "ADMIN")
```

We need to put the **ADMIN** authorization in users <code>.antMatchers(ADMIN).hasAnyRole("ADMIN")</code>, in which we define that only whoever is in the *ADMIN* vector can access

```java
.antMatchers(ADMIN).hasAnyRole("ADMIN")
```

In the end we require that any other route that has not been previously specified must be logged in, regardless of user profile

```java
.anyRequest().authenticated();
```

Let's test our application leaving the **Spring Tool Suite**

According to our Database, Maria is both **OPERATOR** and **ADMIN** and Alex is only **OPERATOR**

```sql
INSERT INTO tb_user (first_name, last_name, email, password) VALUES ('Alex', 'Brown', 'alex@gmail.com', '$2a$10$eACCYoNOHEqXve8aIWT8Nu3PkMXWBaOxJ9aORUYzfMQCbVBIhZ8tG');
INSERT INTO tb_user (first_name, last_name, email, password) VALUES ('Maria', 'Green', 'maria@gmail.com', '$2a$10$eACCYoNOHEqXve8aIWT8Nu3PkMXWBaOxJ9aORUYzfMQCbVBIhZ8tG');

INSERT INTO tb_role (authority) VALUES ('ROLE_OPERATOR');
INSERT INTO tb_role (authority) VALUES ('ROLE_ADMIN');

INSERT INTO tb_user_role (user_id, role_id) VALUES (1, 1);
INSERT INTO tb_user_role (user_id, role_id) VALUES (2, 1);
INSERT INTO tb_user_role (user_id, role_id) VALUES (2, 2);
```

### Access tests in Postman

I will try to access ***Categories paged*** without passing any token in my request


<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153722463-a1c494eb-d5f4-4218-a522-94f64c01ec52.png">
</p>

As we can see, it was possible to access the contents of ***Categories paged*** even without any token

I'm going to try now to register a ***New category***, in this case I'll have to be barred, because I defined an authorization in which I'm only releasing the routes of *products* and *categories* for the **GET** method, so if I try to include a new category, which goes through the **POST** method, will have to stop

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153723539-51ce138e-75ec-4fa3-9b33-838fa7f0d607.png">
</p>

As we see in the image above, we received the *Unauthorized error 401*, as we are not allowed to enter a ***New category***

The same applies to the *product*. If we try to access the ***Products paged*** we will be able to view the paged products but when sending a ***New product*** we will receive the *Unauthorized error 401*

In *User*, if we try to access ***Users paged*** or any other option, we won't be able to, since I didn't even release the **GET** for *User*

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153724229-84d18d68-7740-4183-8e5b-f902a66b4865.png">
</p>

I'm going to login with Alex in Postman and I'm going to access the endpoints with Alex's profile

The login will be done manually, later on I will configure *Postman* to automatically login the user profiles

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153724453-3f1adab2-c174-49a9-bbee-35735051d420.png">
</p>


<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153724879-f850ff0b-7b8a-45fd-8c31-a4cb0f2b0508.JPG">
</p>


*Passing **Bearer** token to **Categories paged***

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153724951-ecb9e88e-1794-4bd6-833f-7db60510291e.JPG">
</p>

After logging in, we copy the toten with **CTRL+C** and I go to ***Categories paged*** for example and I will try to access the categories, passing the **Bearer Token**, in order to test if it accepts access to ***Categories paged*** with the token, despite know that ***Categories paged*** is released to everyone in the **GET** method

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153725140-fdd79efa-d94d-4add-ae85-b98a5e1a2fc3.JPG">
</p>

As we see in the image above, it is possible to consult the ***Categories paged*** with Alex's login

Next we are going to test with Alex's credentials to see if he can send a ***New category***, he is *OPERATOR*, so he should be able to, naturally passing Alex's token to **Bearer Token** first

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153725375-c95c36e0-8d62-4914-a388-a2ad4b663442.JPG">
</p>

As we can see from the above image, it is possible to Alex send a ***New category*** as  expected

Let's test with the *User*. Alex has the role of *OPERATOR*, so he should not have access permission on the *users* page. In the same way, we go to *User*, select the Bearer Token option and put Alex's token and send the request to a ***Users paged query***

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153742705-c544c277-2782-4307-883f-e0549f883c75.png">
</p>

As expected, according to the image above, the endpoind recognizes that Alex is logged in, the token is valid, but he is not authorized to access these contents, hence the endpoint returned a *403 error*

The **OPERATOR** profile is working as we want it to work

Let's now test with the **ADMIN** profile, using Maria's login, which is simultaneously **OPERATOR** and **ADMIN**

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153743376-1a345cb0-41ef-4200-8556-128e28a3b312.png">
</p>

We access the **Login** and in the **username** we change to **maria@gmail.com** we press *Send* and **copy the token** as in the image above

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153743836-6c3a1c21-3985-4de5-9d17-db535ae0903e.png">
</p>

Let's try registering a ***New category*** with Maria's **Bearer Token**, delete Alex's **Bearer Token** and paste Maria's

As expected, it was possible to register the ***New category***


Let's test now if Maria can get the users page, which is only accessible to ADMIN

Let's test now if Maria can access the **users page**, which is only accessible to **ADMIN**, we access ***Users paged*** and in **Bearer token** we pass Maria's token, then press the **Send** button

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153744432-6aead5e5-54d9-4e9e-a73e-c911815347d2.png">
</p>

According to the image above, Maria, who is **ADMIN**, can browse the users' pages, so our ***Resource server*** is playing its role, since it is receiving the token and is releasing the resources according to the authorization rules that have been implemented

<hr>

# 03-27 Special configuration for bank H2

At this moment if we try to access the database H2 console at http://localhost:8080/h2-console we will receive the error below


<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153744867-6940df79-e8b8-4261-8fb5-9019cdb832dd.JPG">
</p>

It is necessary to configure the **H2 console** as part of the ***PUBLIC*** routes as well

We will also create a security, so that only the *test* profile has access to the *H2 console*, for that we will have to inject a **Spring** object according to the code below

```java
@Autowired
private Environment env;
```
The **Environment** is the execution environment of our application, and it is from this object that we can access several interesting variables

From the **env** we can access the profiles that are currently running and from there test which specific profile is running. In our case, let's test if a *test* profile is running, and if so, let's release the **H2 console**

```java
@Override
public void configure(HttpSecurity http) throws Exception {
	
	// H2
	if (Arrays.asList(env.getActiveProfiles()).contains("test")) {
		http.headers().frameOptions().disable();
	}
	
	http.authorizeRequests()
	.antMatchers(PUBLIC).permitAll()
	.antMatchers(HttpMethod.GET, OPERATOR_OR_ADMIN).permitAll()
	.antMatchers(OPERATOR_OR_ADMIN).hasAnyRole("OPERATOR", "ADMIN")
	.antMatchers(ADMIN).hasAnyRole("ADMIN")
	.anyRequest().authenticated();
}
```

We necessarily have to add the route of the **H2 console** as ***PUBLIC***

```java
private static final String[] PUBLIC = {"/oauth/token", "/h2-console/**"};
```
*Complete and changed code of the ***ResourceServerConfig*** class*

```java
package com.devsuperior.dscatalog.config;

import java.util.Arrays;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;
import org.springframework.security.oauth2.config.annotation.web.configuration.ResourceServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configurers.ResourceServerSecurityConfigurer;
import org.springframework.security.oauth2.provider.token.store.JwtTokenStore;

@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {

	@Autowired
	private Environment env;
	
	@Autowired
	private JwtTokenStore tokenStore;
	
	private static final String[] PUBLIC = {"/oauth/token", "/h2-console/**"};
	
	private static final String[] OPERATOR_OR_ADMIN = {"/products/**", "/categories/**"};
	
	private static final String[] ADMIN = {"/users/**"};
	
	@Override
	public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
		resources.tokenStore(tokenStore);
	}

	@Override
	public void configure(HttpSecurity http) throws Exception {
		
		// H2
		if (Arrays.asList(env.getActiveProfiles()).contains("test")) {
			http.headers().frameOptions().disable();
		}
		
		http.authorizeRequests()
		.antMatchers(PUBLIC).permitAll()
		.antMatchers(HttpMethod.GET, OPERATOR_OR_ADMIN).permitAll()
		.antMatchers(OPERATOR_OR_ADMIN).hasAnyRole("OPERATOR", "ADMIN")
		.antMatchers(ADMIN).hasAnyRole("ADMIN")
		.anyRequest().authenticated();
	}
}

```


If we restart the project again and try to access the *H2 console*, we will be able to as we can see in the image below

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153745900-759697d0-1abf-430d-b030-45237288317c.JPG">
</p>

<hr>

# 03-28 Making Postman top

Let's configure *Postman* to make it easier to work with.

Let's increment our environment, putting more variables
In our *Postman*, at the moment we only create the **{host}** variable <code>httt://localhost:8080</code>, we are going to edit this environment and we are going to put more variables

Variables:

- host: http://localhost:8080
- client-id: dscatalog
- client-secret: dscatalog123
- username: alex@gmail.com
- password: 123456
- token:

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153749343-472b9419-a1f1-4a5e-9cd7-c6dc421cf56d.png">
</p>

We have to edit and add the variables to ***bds-employees*** so that it looks like the image above

Next, let's go to the **Login** request, and in the *Authorization* tab, I won't put the configuration manually anymore. I'll put the *Username* and *Password* with the name of the variable as you can see in the image below

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153749648-e6019131-8323-414f-9e52-e22c4d25f398.png">
</p>


<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153750114-04039ea0-241d-4126-a850-4fc4f455ab21.png">
</p>

In the *Body* tab, we select the variables for the *Username* and *Password* according to the image above

At the end, when we press the *Send* button, we can see that it is possible to log into our application with our configuration

This way *Postman* is also more secure, our *Collection* no longer carries sensitive data, will only load a reference, the environment will have the sensitive data

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153751846-c76d6978-05cb-4536-8610-70246504dd61.png">
</p>

In the *Categories paged* on the *Authorization* tab we put the **Type** as the *Bearer token*, and instead of putting the direct token, I will pass a reference with the token variable as in the image above, then by pressing the Send button, we can see that it works

We must do the same for the other endpoints, *Category id*, *New category*, *Update category*, and *Delete Category*

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153753004-e9c6e57c-6bf1-4098-a2cd-40d5f5483c34.png">
</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153753593-cdecac60-72f6-483b-ae93-1d37941edc51.png">
</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153753817-ccbe626c-06fc-4161-8f90-a72d4cbe6f5e.png">
</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153754157-aef25f91-5494-4b68-bba1-2fe6c07e16bf.png">
</p>

In addition to the Category endpoints, we must do the same for the User endpoints, exactly the same way as we have done so far

Finally, in order to be top, in the Login request, when I make the request and return the token, I will put a script to get the token that was generated, and throw it inside the token variable, automatically. This way you will no longer need to copy the token and paste

To do this, go to the Login request and go to the tests tab and paste the code below

```javascript
if (responseCode.code >= 200 && responseCode.code < 300) {
    var json = JSON.parse(responseBody);
    postman.setEnvironmentVariable('token', json.access_token);
}
```
Basically what this code does is, if the response code is around 200, that is, greater than 200 and less than 300, it means that the login was successful. Then I will get the response body which is the responseBody and access it as a json, that is, in String format, so that we can access the access_token and I will assign its value in the token variable

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153754956-38e51dd0-8b91-4384-9f93-1d8c765106c5.png">
</p>

If we run our login again, remembering that our login has the variables. If we check our environment variables, we can see that the token now appears there as a variable as well. Now all endpoints will work with the credentials we have configured

<p align="center">
<img src="https://user-images.githubusercontent.com/22635013/153755252-a1db8d80-e3d0-458c-a359-403d56ec590d.png">
</p>

<hr>

# 03-29 Spoiler - authentication and authorization next topics

<hr>

# 03-30 Introducing the solved challenge

# 05-22 Preparing DSCatalog from the End of Chapter 3

Create a PostgreSQL database named ***dscatalog*** on pgAdmin and paste the code below

```sql
create table tb_category (id  bigserial not null, created_at TIMESTAMP WITHOUT TIME ZONE, name varchar(255), updated_at TIMESTAMP WITHOUT TIME ZONE, primary key (id));
create table tb_product (id  bigserial not null, date TIMESTAMP WITHOUT TIME ZONE, description TEXT, img_url varchar(255), name varchar(255), price float8, primary key (id));
create table tb_product_category (product_id int8 not null, category_id int8 not null, primary key (product_id, category_id));
create table tb_role (id  bigserial not null, authority varchar(255), primary key (id));
create table tb_user (id  bigserial not null, email varchar(255), first_name varchar(255), last_name varchar(255), password varchar(255), primary key (id));
create table tb_user_role (user_id int8 not null, role_id int8 not null, primary key (user_id, role_id));
alter table tb_user add constraint UK_4vih17mube9j7cqyjlfbcrk4m unique (email);
alter table tb_product_category add constraint FK5r4sbavb4nkd9xpl0f095qs2a foreign key (category_id) references tb_category;
alter table tb_product_category add constraint FKgbof0jclmaf8wn2alsoexxq3u foreign key (product_id) references tb_product;
alter table tb_user_role add constraint FKea2ootw6b6bb0xt3ptl28bymv foreign key (role_id) references tb_role;
alter table tb_user_role add constraint FK7vn3h53d0tqdimm8cp45gc0kl foreign key (user_id) references tb_user;
```
Next, open the ***data.sql*** from the ***dscatalog*** project to copy the code below, on the ***dscatalog*** database from PostgreSQL

```sql
INSERT INTO tb_user (first_name, last_name, email, password) VALUES ('Alex', 'Brown', 'alex@gmail.com', '$2a$10$eACCYoNOHEqXve8aIWT8Nu3PkMXWBaOxJ9aORUYzfMQCbVBIhZ8tG');
INSERT INTO tb_user (first_name, last_name, email, password) VALUES ('Maria', 'Green', 'maria@gmail.com', '$2a$10$eACCYoNOHEqXve8aIWT8Nu3PkMXWBaOxJ9aORUYzfMQCbVBIhZ8tG');

INSERT INTO tb_role (authority) VALUES ('ROLE_OPERATOR');
INSERT INTO tb_role (authority) VALUES ('ROLE_ADMIN');

INSERT INTO tb_user_role (user_id, role_id) VALUES (1, 1);
INSERT INTO tb_user_role (user_id, role_id) VALUES (2, 1);
INSERT INTO tb_user_role (user_id, role_id) VALUES (2, 2);


INSERT INTO tb_category (name, created_At) VALUES ('Livros', NOW());
INSERT INTO tb_category (name, created_At) VALUES ('Eletrônicos', NOW());
INSERT INTO tb_category (name, created_At) VALUES ('Computadores', NOW());

INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('The Lord of the Rings', 90.5, TIMESTAMP WITH TIME ZONE '2020-07-13T20:50:07.12345Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/1-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('Smart TV', 2190.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/2-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('Macbook Pro', 1250.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/3-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer', 1200.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/4-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('Rails for Dummies', 100.99, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/5-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Ex', 1350.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/6-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer X', 1350.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/7-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Alfa', 1850.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/8-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Tera', 1950.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/9-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Y', 1700.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/10-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Nitro', 1450.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/11-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Card', 1850.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/12-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Plus', 1350.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/13-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Hera', 2250.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/14-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Weed', 2200.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/15-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Max', 2340.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/16-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Turbo', 1280.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/17-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Hot', 1450.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/18-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Ez', 1750.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/19-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Tr', 1650.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/20-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Tx', 1680.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/21-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Er', 1850.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/22-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Min', 2250.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/23-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Boo', 2350.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/24-big.jpg');
INSERT INTO tb_product (name, price, date, description, img_url) VALUES ('PC Gamer Foo', 4170.0, TIMESTAMP WITH TIME ZONE '2020-07-14T10:00:00Z', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.', 'https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/25-big.jpg');

INSERT INTO tb_product_category (product_id, category_id) VALUES (1, 2);
INSERT INTO tb_product_category (product_id, category_id) VALUES (2, 1);
INSERT INTO tb_product_category (product_id, category_id) VALUES (2, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (3, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (4, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (5, 2);
INSERT INTO tb_product_category (product_id, category_id) VALUES (6, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (7, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (8, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (9, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (10, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (11, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (12, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (13, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (14, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (15, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (16, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (17, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (18, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (19, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (20, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (21, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (22, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (23, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (24, 3);
INSERT INTO tb_product_category (product_id, category_id) VALUES (25, 3);
```

# 05-23 Query methods

# 05-24 Problem N+1 queries with Spring Data JPA

For this topic I create a new repository named
***class_nplus1***

- https://github.com/leninepestana/class_nplus1


# 05-25 Disclaimer - notices about DSCatalog

# 05-26 Filter products, INNER JOIN, IN

Filter the product catalog by product and category

I can also not inform the product or inform the category and do a complete search

***ProductController*** class

```java
package com.devsuperior.dscatalog.controller;

import java.net.URI;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.devsuperior.dscatalog.dto.ProductDTO;
import com.devsuperior.dscatalog.services.ProductService;

@RestController
@RequestMapping(value = "/products")
public class ProductController {

	@Autowired
	private ProductService service;
	
	@GetMapping
	public ResponseEntity<Page<ProductDTO>> findAll(
			@RequestParam(value = "categoryId", defaultValue = "0") Long categoryId,
			@RequestParam(value = "name", defaultValue = "") String name,
			Pageable pageable) {
		Page<ProductDTO> list = service.findAllPaged(categoryId, pageable);		
		return ResponseEntity.ok().body(list);
	}

	(...)
} 
```

***ProductService*** class

```java
package com.devsuperior.dscatalog.services;

import java.util.Optional;

import javax.persistence.EntityNotFoundException;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataIntegrityViolationException;
import org.springframework.dao.EmptyResultDataAccessException;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.devsuperior.dscatalog.dto.CategoryDTO;
import com.devsuperior.dscatalog.dto.ProductDTO;
import com.devsuperior.dscatalog.entities.Category;
import com.devsuperior.dscatalog.entities.Product;
import com.devsuperior.dscatalog.repositories.CategoryRepository;
import com.devsuperior.dscatalog.repositories.ProductRepository;
import com.devsuperior.dscatalog.services.exceptions.DatabaseException;
import com.devsuperior.dscatalog.services.exceptions.ResourceNotFoundException;

@Service
public class ProductService {

	@Autowired
	private ProductRepository repository;
	
	@Autowired
	private CategoryRepository categoryRepository;
	
	@Transactional(readOnly = true)
	public Page<ProductDTO> findAllPaged(Long categoryId, Pageable pageable) {
		Category category = (categoryId == 0) ? null :  categoryRepository.getOne(categoryId);
		Page<Product> list = repository.find(category, pageable);
		return list.map(x -> new ProductDTO(x));
	}

	(...)	
}
```
***ProductRepository*** interface

```java
package com.devsuperior.dscatalog.repositories;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import com.devsuperior.dscatalog.entities.Category;
import com.devsuperior.dscatalog.entities.Product;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

	@Query("SELECT obj FROM Product obj INNER JOIN obj.categories cats WHERE "
			+ ":category IN cats")
	Page<Product> find(Category category, Pageable pageable);
}
```
When testing in Postman with this implementation, an empty list is returned instead of all categories

![products_paged_all](https://user-images.githubusercontent.com/22635013/162674722-bc845215-496d-4a27-95db-4c40506fd26b.PNG)

```code
{
    "content": [],
    "pageable": {
        "sort": {
            "sorted": true,
            "unsorted": false,
            "empty": false
        },
        "offset": 0,
        "pageSize": 12,
        "pageNumber": 0,
        "paged": true,
        "unpaged": false
    },
    "last": true,
    "totalElements": 0,
    "totalPages": 0,
    "size": 12,
    "number": 0,
    "sort": {
        "sorted": true,
        "unsorted": false,
        "empty": false
    },
    "first": true,
    "numberOfElements": 0,
    "empty": true
}
```

When the ***categoryId*** is not informed and it is null, and all categories are searched instead of none. To ensure this. If the category is not passed, it is to show everything, so in the code we have to put that the restriction is true and it is to show all categories


```java
@Query("SELECT obj FROM Product obj INNER JOIN obj.categories cats WHERE "
			+ ":category IN cats")
```

In the query below, I will get the products and categories where the ***category*** belongs to this product list. 

If the ***category*** is null, it means that It's true that and I'm not restricting nothing, so that what I want is to get all the products regardless of the condition of the category that was not passed, see the code below

```java
@Query("SELECT obj FROM Product obj INNER JOIN obj.categories cats WHERE "
			+ "(:category IS NULL OR :category IN cats)")
	Page<Product> find(Category category, Pageable pageable);
```

If the ***category*** is null, the **OR** condition will no longer search for any ***category***
associated with the product, and the condition will be true

Testing in **Postman** with ***categoryId=3***

![categoryId3](https://user-images.githubusercontent.com/22635013/162691830-4d99f84a-49d1-4e52-ba7b-5e94d9627480.PNG)

```code
{
    "content": [
        (..)
    ],
    "pageable": {
        "sort": {
            "sorted": true,
            "unsorted": false,
            "empty": false
        },
        "offset": 0,
        "pageNumber": 0,
        "pageSize": 12,
        "paged": true,
        "unpaged": false
    },
    "last": false,
    "totalPages": 2,
    "totalElements": 23,
    "size": 12,
    "number": 0,
    "sort": {
        "sorted": true,
        "unsorted": false,
        "empty": false
    },
    "first": true,
    "numberOfElements": 12,
    "empty": false
}
```

```SQL
SELECT * FROM TB_PRODUCT_CATEGORY 
WHERE CATEGORY_ID = 3
```
![category_id](https://user-images.githubusercontent.com/22635013/162698347-8551263b-b7f7-4f96-8a78-77dadd04dd63.PNG)

The top result returned a total of 23 records

Testing in **Postman** with ***categoryId=1***

![categoryId1](https://user-images.githubusercontent.com/22635013/162698966-ec0e1cc4-1633-4786-bc8d-073fe704d9e6.PNG)



```code
{
    "content": [
        {
            "id": 2,
            "name": "Smart TV",
            "description": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt 
			ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut 
			aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore 
			eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt 
			mollit anim id est laborum.",
            "price": 2190.0,
            "imgUrl": "https://raw.githubusercontent.com/devsuperior/dscatalog-resources/master/backend/img/2-big.jpg",
            "date": "2020-07-14T10:00:00Z",
            "categories": []
        }
    ],
    "pageable": {
        "sort": {
            "sorted": true,
            "unsorted": false,
            "empty": false
        },
        "offset": 0,
        "pageNumber": 0,
        "pageSize": 12,
        "paged": true,
        "unpaged": false
    },
    "last": true,
    "totalPages": 1,
    "totalElements": 1,
    "size": 12,
    "number": 0,
    "sort": {
        "sorted": true,
        "unsorted": false,
        "empty": false
    },
    "first": true,
    "numberOfElements": 1,
    "empty": false
}
```






![tb_product_category](https://user-images.githubusercontent.com/22635013/162685201-0309d68c-1491-4b62-b6e1-ecc9fbf7bf1b.PNG)



<a href="https://github.com/leninepestana/bds03">https://github.com/leninepestana/bds03</a>

<hr>

Lenine Ferrer de Pestana <br />
Email: leninepestana@gmail.com