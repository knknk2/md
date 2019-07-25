## Relationships

one-to-many, many-to-one, many-to-many and one-to-one:

1. [A bidirectional one-to-many relationship](#1)
2. [A unidirectional many-to-one relationship](#2)
3. [A unidirectional one-to-many relationship](#3)
4. [Two one-to-many relationships on the same two entities](#4)
5. [A many-to-many relationship](#5)
6. [A one-to-one relationship](#6)
7. [A unidirectional one-to-one relationship](#7)

_the `User` entity_

- `many-to-one` relationships to this entity (a `Car` can have a many-to-one relationship to a `User`). This will have a specific query in the new entity repository, so you can filter your entity on the current security user, which is a common requirement.
- `many-to-many` and `one-to-one` relationships to the `User` entity, but the other entity **must** be the owner
  of the relationship (a `Team` can have a many-to-many relationship to `User`, but only the team can add/remove users, and a user cannot add/remove a team).

When using the UAA authentication type, you can only create relationships to the User entity if the related entity is also within the UAA microservice.

## <a name="1"></a> A bidirectional one-to-many relationship

A `Owner` and a `Car`. A owner can have many cars, and a car can have only one owner.

So this is a simple one-to-many relationship (one owner has many cars) on one side, and a many-to-one relationship (many cars have one owner) on the other side:

    Owner (1) <-----> (*) Car

## <a name="2"></a> A unidirectional many-to-one relationship

In the previous example we had a bidirectional relationship: from a `Car` instance you could find its owner, and from a `Owner` instance you could get all of its cars.

A many-to-one unidirectional relationship means that the cars know their owner, but not the opposite.

    Owner (1) <----- (*) Car

You would do that relationship for two reasons:

- From a business point of view, you only use your entities in this way. So you don't want to have an API that allows developers to do something which doesn't make sense.
- You have a small performance gain when using the `Owner` entity (as it won't have to manage the collection of cars).

## <a name="3"></a> A unidirectional one-to-many relationship

A one-to-many unidirectional relationship means that the `Owner` instance can get its collection of cars, but not the opposite. It is the opposite from the previous example.

    Owner (1) -----> (*) Car

You have two solutions for this:

- Do a bidirectional mapping, and use it without modification: this is our recommended approach, as it is much simpler
- Do a bidirectional mapping, and then modify it to transform it into a unidirectional mapping:
  - Remove the "mappedBy" attribute on your `@OneToMany` annotation
  - Generate the required join table

## <a name="4"></a> Two one-to-many relationships on the same two entities

For this example, a `Person` can be the owner of many cars, and he can also be the driver of many cars:

    Person (1) <---owns-----> (*) Car
    Person (1) <---drives---> (*) Car

For this we need to use the relationship names, which we have left with their default values in the previous examples.

A `Car` can now have a driver and a owner, which are both `Person` entities.

## <a name="5"></a> A many-to-many relationship

A `Driver` can drive many cars, but a `Car` can also have many drivers.

    Driver (*) <-----> (*) Car

At the database level, this means we will have a join table between the `Driver` (not owning) and the `Car` (owning) tables.

For JPA, one of those two entities will need to manage the relationship: in our case, that would be the `Car` entity, which will be responsible to add or remove drivers.

## <a name="6"></a> A one-to-one relationship

Following our example, a one-to-one relationship would mean that a `Driver` (non-owning) can drive only one `Car` (owning), and a `Car` can only have one `Driver`.

    Driver (1) <-----> (1) Car

## <a name="7"></a> A unidirectional one-to-one relationship

A unidirectional one-to-one relationship means that the `citizen` instance can get its passport, but the `passport` instance can't get to its owner.

    Citizen (1) -----> (1) Passport

After doing this, a `Citizen` possesses a passport, but no `Citizen` instance is defined in `Passport`.

### <a name="8"> Using JPA Derived Identifiers(@MapsId) for one-to-one relationship

[JPA Derived Identifiers](https://javaee.github.io/javaee-spec/javadocs/javax/persistence/MapsId.html) can be used to have [the most efficient mapping](https://vladmihalcea.com/the-best-way-to-map-a-onetoone-relationship-with-jpa-and-hibernate/).

However, based on business requirements, there might be cases where this should be avoided because it has following constraint:
**Once the id(primary key) is set at owning side, it is not changeable using JPA/Hibernate. You should not change it anyway.**

**Here are a few suggestions regarding usage:**

Use `@MapsId` when:

- Dependent - if the owning side (child entity) seems tightly dependent on the non-owning (parent entity).
- Association value is never meant to be changed - if you are never going to change the id(primary key) of the child entity once it is set.

  For eg,

  ```
  class User{}
  class Profile{ @OneToOne @MapsId private User user; } // profile is only meant for that user
  class Preferences{ @OneToOne @MapsId private User user; } // preference is only meant for that user
  ```

  Once a profile or a preference is created for a user, it will never change to refer to another user.

Do not use `@MapsId` when:

- Not dependent - If the owning side (child entity) seems not dependent on the non-owning (parent entity)
- Association value is meant to be changed - if you think that the child entity is going to refer to another parent entity in future.

  For eg,

  ```
  class Car{ @OneToOne @JoinColumn(name="id") Driver currentDriver} // car can be drived by another driver in future
  class Driver{@OneToOne @JoinColumn(name="id") Car drivingCar} // driver drives another car in future
  ```

  Both car and driver association value may change in future.
