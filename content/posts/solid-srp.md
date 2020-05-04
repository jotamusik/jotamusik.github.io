---
title: "Single Responsability Principle"
categories: [ "Principios SOLID" ]
tags: [ "Buenas Prácticas", "Java" ]
date: 2020-05-04
draft: false
---

Últimamente me he interesado más por __Arquitectura Hexagonal__, __Domain-Driven-Design__ y __CQRS__, así que he aprovechado que 
tengo la posibilidad de tener una suscripción a CodelyTv y he empezado a formarme en estos temas.

Tal como CodelyTv propone estos temas como una serie de cursos a seguir, yo hare una serie de post en los que vaya 
explicando estos conceptos. 

¡Empecemos! SOLID hace referencia a 5 principios del diseño de software siendo cada letra, la sigla de un principio.

### Single Responsability Principle

> Una clase debería cambiar sólo por una razón

Este principio va de la mano del uso correcto de la orientación a objetos. La POO (Programación Orientada a Objetos) dicta 
que una clase sólo debe tener un fin, por consiguiente, este principio refuerza esa idea para que la tengamos aún más presente
en el momento en el que diseñamos software. 

Como guía para saber si cumplimos o no este principio, deberemos comprobar que nuestras clases tienen un único punto de 
entrada, es decir, un único método público. Sobre esto hay que aclarar que no hay que aplicarlo a todo ya que los Value 
Objects por ejemplo necesitan de varios métodos públicos para poder definir su comportamiento.

Ahora veamos un ejemplo. Imaginémonos que tenemos un servicio, ___UserService___ de manera que tiene dos metodos, uno 
___CreateUser___ y otro que es ___UpdateUser___. Veamos cómo sería en código:


``` java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public void createUser(String username) {
        userRepository.insertUser(username);
    }

    public void updateUser(String username, String newUsername) {
        userRepository.updateUser(username, newUsername);
    }
}

public class UserRepository {
    public void insertUser(String username) {
        // Insertamos Usuario en BBDD
    }

    public void updateUser(String username, String newUsername) {
        // Insertamos Usuario en BBDD
    }
}
```

Como vemos, ambas clases tienen varios metodos publicos. En este caso ninguna de las clases resulta ser un Value Object 
por lo que realmente, no cumplen con este principio. Veamos como queda el codigo tras refactorizar y separar las responsabilidades.


``` java
public class CreateUserService {
    private final CreateUserRepository createUserRepository;

    public CreateUserService(CreateUserRepository createUserRepository) {
        this.createUserRepository = createUserRepository;
    }

    public void invoke(String username) {
        createUserRepository.invoke(username);
    }
}

public class CreateUserRepository {
    public void invoke(String username) {
        // Insertamos Usuario en BBDD
    }
}

public class UpdateUserService {
    private final UpdateUserRepository updateUserRepository;

    public UpdateUserService(UpdateUserRepository updateUserRepository) {
        this.updateUserRepository = updateUserRepository;
    }
    public void invoke(String username, String newUsername) {
        updateUserRepository.invoke(username, newUsername);
    }

}

public class UpdateUserRepository {
    public void invoke(String username, String newUsername) {
        // Insertamos Usuario en BBDD
    }
}
```

Tal como lo tenemos ahora, en caso de necesitar cambiar el flujo de crear usuario, sólo tendremos que tocar la clase dedicada
a ello mientras que en la anterior solución, realmente podríamos estar afectando a otros flujos ya que estos no estarían 
aislados.

Es super importante comprender este principio y realmente no resulta fácil aplicarlo a pesar de lo sencillo que es 
el concepto sobre el que trabaja. Sin embargo, es super fácil ver lo que nos aporta a largo plazo en cuanto a mantenibilidad.
 