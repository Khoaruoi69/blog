---
layout: post
title: Difference between @NotNull, @NotEmpty and @NotBlank
categories: Java-8
---


#### compairation:

- **@NotNull:** *The CharSequence, Collection, Map or Array object **is not null**, but can be empty*

- **@NotEmpty:** *The CharSequence, Collection, Map, or Array object* is not null **not size > 0**

- **@NotBlank:** *The string is not null* **and the trimmed length is greater than zero.**

To help you understand, let's look into the hơ these constraints are defined and carried out ( I'm using version 4.1):

1. The **@NotNull** constraint is defined as:

+ @Constraint(validatedBy = {NotNullValidator.class}) 


This class has an *isValid* method defined as:

```java
public boolean isValid(Object object, ConstraintValidatorContext constraintValidatorContext) {
 return object != null;  
}
```

2. The **NotEmpty** constraint is defined as:

+ @NotNull

+ @Size(min=1)

So this constraint uses the **@NotNull** constraint above, and **size** whose definition differs based on the object but should be self explanitory.

3. Finally, the **NotBlank** constraint is defined as:

+ @NotNull

+ @Constraint(validatedBy = {NotBlankValidator.class})    

So this constraint also uses the **@NotNull** constraint, but also constrains with the NotBlankValidator class. This class has an isValid method defined as:

```java
if ( charSequence == null ) {  //curious 
  return true;   
}   
return charSequence.toString().trim().length() > 0;  
```
Interestingly, this method returns true if the string is null, but false if and only if the length of the trimmed string is 0. It's ok that it returns true if it's null because, as I mentioned, the **@NotEmpty** definition also requires **@NotNul()**

**Example:**

1. String name = null

+ @NotNull: false

+ @NotEmpty: false

+ @NotBlank: false

2. String name = "";

+ @NotNull: true

+ @NotEmpty: false

+ @NotBlank: false

3. String name = " ";

+ @NotNull: true

+ @NotEmpty: true

+ @NotBlank: false

4. String name = "Great answer!";

+ @NotNull: true
 
+ @NotEmpty: true
 
+ @NotBlank: true
 