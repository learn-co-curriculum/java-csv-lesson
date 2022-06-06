# CSV

## Learning Goals

- Understand the CSV format
- Work with the CSV format in Java

## CSV Format

A "CSV" is a "Comma Separated Values" file, where each line is a grouping of
properties and each entry on each line is separated by a comma to indicate the
end of one property and the start of another one.

Let's consider a `Person` class:

```java
public class Person {
    private String firstName;
    private String lastName;
    private int birthYear;
    private int birthMonth;
    private int birthDay;

    public Person(String firstName, String lastName, int birthYear, int birthMonth, int birthDay) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.birthYear = birthYear;
        this.birthMonth = birthMonth;
        this.birthDay = birthDay;
    }

    public String toString() {
        StringBuffer personString = new StringBuffer();
        personString.append("firstName = " + firstName);
        personString.append("\n");
        personString.append("lastName = " + lastName);
        personString.append("\n");
        personString.append("Birth date = ");
        personString.append(birthMonth + "/");
        personString.append(birthDay+ "/");
        personString.append(birthYear);
        personString.append("\n");

        return personString.toString();
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

    public int getBirthYear() {
        return birthYear;
    }

    public void setBirthYear(int birthYear) {
        this.birthYear = birthYear;
    }

    public int getBirthMonth() {
        return birthMonth;
    }

    public void setBirthMonth(int birthMonth) {
        this.birthMonth = birthMonth;
    }

    public int getBirthDay() {
        return birthDay;
    }

    public void setBirthDay(int birthDay) {
        this.birthDay = birthDay;
    }
}
```

Beyond the private fields and the associated getter and setter methods you're
used, we've added a couple of aspects to this basic class:

- A custom constructor that allows us to pass all the values to initialize an
  instance
- A `toString()` method that returns a `String` representation of the values of
  the object. The `toString()` method is called by default when a non-string
  object is passed to the `System.out.println()` method

> Note: in the `toString()` method, we use the `StringBuffer` class to create a
> string incrementally by appending values to it. The `StringBuffer` class is a
> better class than the `String` class to build long strings because it does not
> re-create a new object every time a value is appended, like the `String` class
> does.

So we can initialize an instance of `Person` and print its value to the console
with the following code:

```java
    public static void main(String[] args) throws IOException {
        Person testPerson = new Person("Soraya", "Rabots", 1990, 12, 19);
        System.out.println(testPerson);
    }
```

This produces the following output:

```java
firstName = Soraya
lastName = Rabots
Birth date = 12/19/1990
```

We want to be able to save the values of each field in this class in a file, and
we will structure this file so that each field is separated with a comma.
Consider the `testPerson` object we created above, it could be represented as
follows:

```
Soraya,Rabots,1990,12,19
```

We can re-use our `writeToFile()` method from before, but we need to feed it a
`String` that matches the format above. Let's create that method and call is
`formatAsCSV()` in the `Person` class:

```java
    public String formatAsCSV() {
        StringBuffer personString = new StringBuffer();
        personString.append(firstName);
        personString.append(",");
        personString.append(lastName);
        personString.append(",");
        personString.append(birthYear);
        personString.append(",");
        personString.append(birthMonth);
        personString.append(",");
        personString.append(birthDay);

        return personString.toString();
    }
```

We can then use the `formatAsCSV()` method in our runner class:

```java
    public static void main(String[] args) throws IOException {
        Person testPerson = new Person("Soraya", "Rabots", 1990, 12, 19);
        System.out.println(testPerson);
        String personCSV = testPerson.formatAsCSV();
        writeToFile("person.csv", personCSV);
    }
```

Which writes a file named `person.csv` with the following content:

```
Soraya,Rabots,1990,12,19
```

Now we need to write code that can read this file and create a Person object
from it. For this, let's create a new constructor that can create a `Person`
object from the string that is saved to the file. We will read that string from
the file and pass it to the constructor to build a new instance.

Let's look at the constructor first:

```java
    public Person(String personCSV) {
        String[] personValues = personCSV.split(",");
        this.firstName = personValues[0];
        this.lastName = personValues[1];
        this.birthYear = Integer.valueOf(personValues[2]);
        this.birthMonth = Integer.valueOf(personValues[3]);
        this.birthDay = Integer.valueOf(personValues[4]);
        System.out.println("Initialized person object from CSV string");
        System.out.println(this);
    }
```

We use the `split()` method from the `String` class, which returns a `String`
array of each value in the input string, based on the "separator" that is passed
into the method. We can then use this array to set each property of the `Person`
class.

So now we can start our read from our file we saved earlier when we start our
program:

```java
    public static void main(String[] args) throws IOException {

        String personCSV = readFromFile("person.csv", false);
        Person testPerson = new Person(personCSV);

        // use the person object here...
    }
```
