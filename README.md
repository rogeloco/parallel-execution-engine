# parallel-execution-engine

A pure java library which can be used by clients to execute independent methods in parallel.
Uses Java 8 Completable Futures, MethodHandles and Generics.

* Java 8 is a pre-requisite. Uses Apache Velocity template for annotation based code generation.
* Clients can use `@Parallelizable` to specify the methods to be parallelized.
* Clients can create Signatures just as if calling the method itself.
* Clients can use the library to parallelize methods with different signatures - different return types, method arguments, collections, custom classes etc.
* Clients can also parallelize methods in different classes.
* It can be used in Spring or other DI based projects as well.
* Clients can leverage java concurrency to parallelize method calls without worrying about the implementation.

Example illustrating usage of the API:

Consider a class `StudentService.java` which provides some methods to query Student data from a database.

```java
List<Integer> getStudentMarks(Long studentId);

List<Student> getStudentsByFirstNames(List<String> firstNames);

String getRandomLastName();

Long findStudentIdByName(String firstName, String lastName);

Student findStudent(String email, Integer age, Boolean isDayScholar);

void printMapValues(Map<String, Integer> bookSeries);
```

Another class `SchoolService.java` which returns a list of School names via the method

```java
List<String> getSchoolNames();
```

Assuming each method takes 1 second to execute, a client which wants to call all the methods from StudentService and SchoolService 
will need 7 seconds to get the output of all the sequential service calls. 

Using the parallel-execution-engine library, a client can call all the methods simultaneously and get the output in 1 second.

Creating signatures is as simple as :

```java
studentServiceSignatures.add(Signature.build(StudentService_.getStudentMarks(1L)));	studentServiceSignatures.add(Signature.build(StudentService_.getStudentsByFirstNames(Arrays.asList("John","Alice"))));
studentServiceSignatures.add(Signature.build(StudentService_.getRandomLastName()));
studentServiceSignatures.add(Signature.build(StudentService_.findStudentIdByName("Kate", "Williams")));
studentServiceSignatures.add(Signature.build(StudentService_.findStudent("bob@gmail.com", 14, false)));
studentServiceSignatures.add(Signature.build(StudentService_.printMapValues(bookSeries)));
schoolServiceSignatures.add(Signature.build(SchoolService_.getSchoolNames()));
```

And then submit them to the executor to get the combined results:

```java
executionMap.put(studentService, studentServiceSignatures);
executionMap.put(schoolService, schoolServiceSignatures);
List<T> result = ParallelProcessor.genericParallelExecutor(executionMap);
```

Check out the tests in `ParallelExecutorTest.java`.

How to Build
------------
Run `mvn clean install` from the root folder. 

Run the main method in `Client.java`
