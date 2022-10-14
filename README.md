# CSV

## Learning Goals

- Understand the CSV format.
- Work with the CSV format in Java.

## What is a CSV?

Now that we can read and write text files, let's look at a different kind of
file: a CSV. **Comma Separated Values (CSV) files** take on a format where each
line is a grouping of properties and each entry on each line is separated by a
comma to indicate the end of one property and the start of another.

To explain a CSV file format better, let us revisit our students from the
`Student` class we looked at earlier!

```text
firstName,lastName,letterGrade
Suzie,Bingham,A
Dustin,Henderson,B
Mike,Wheeler,C
```

Notice in the above, every line is comma-separated and every row represents
a different grouping. For example, we can read this as "Suzie Bingham has an A;
Dustin Henderson has a B; and Mike Wheeler has a C."

## Data Persistence

The process of saving your program's data, so it can be used later is called
**data persistence**. There are many ways to implement data persistence - in this
lesson, we will focus on file-based data persistence using CSV files.

Let's consider a `Student` class:

```java
public class Student {
  private String firstName;
  private String lastName;
  private char grade;

  public Student(String firstName, String lastName, char grade) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.grade = grade;
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

  public char getGrade() {
    return grade;
  }

  public void setGrade(char grade) {
    this.grade = grade;
  }

  @Override
  public String toString() {
    return String.format("%s %s has the letter grade %s",
            firstName,
            lastName,
            grade);
  }
}
```

If we want to initialize an instance of a `Student`, then we can do so like this:

```java
public static void main(String[] args) {
    Student suzie = new Student("Suzie", "Bingham", 'A');
    System.out.println(suzie);
}
```

This would print:

```text
Suzie Bingham has the letter grade A
```

Now what if we want to be able to save the values of each field in the `Student`
class in a file? Or use this data again to create a `Student` object?

We can do so using CSV files! Let's see how to write and read from a CSV.

## Writing to a CSV File

Consider the `Student` object we created above, it could be represented as
follows:

```text
Suzie,Bingham,A
```

Let's add a method to the `Student` class called `formatAsCSV()` to write the
`Student` object in this comma-separated format!

```java
public String formatAsCSV() {
    return String.format("%s,%s,%s", firstName, lastName, grade);
}
```

Now let's see if we can write this out to a CSV file! We'll make use of a lot of
the same code we used when we were writing out to a text file:

```java
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;

public class FileIOMain {
    public static void main(String[] args) {
        Student suzie = new Student("Suzie", "Bingham", 'A');
        File file = new File("student.csv");

        try (FileWriter fileWriter = new FileWriter(file)) {
            fileWriter.write(suzie.formatAsCSV());
        } catch (IOException ioException) {
            ioException.printStackTrace();
        }
    }
}
```

Notice the minimal differences from the `FileIOMain` class we used previously.
We are now writing to a file with the relative path of `student.csv`. If we
run this program, we'll now see the `student.csv` file!

![create-csv](https://curriculum-content.s3.amazonaws.com/java-mod-3/csv/create-csv-file.png)

## Reading from a CSV File

Now we need to write the code that can read this file and create a `Student`
instance from it. For this, let's create a new constructor that can create a
`Student` object from the content in a CSV file.

```java
public Student(String studentCSV) {
    String[] studentProperties = studentCSV.split(",");
    this.firstName = studentProperties[0];
    this.lastName = studentProperties[1];
    this.grade = studentProperties[2].charAt(0);
}
```

Before we go any further, let's discuss what is happening in this constructor:

- Since the content from the CSV is formatted with a comma as a delimiter, or
  separator, we know we can split the `studentCSV` using the `,` as a delimiter.
  - For example, if we have `Suzie,Bingham,A` as the value of `studentCSV`, if
    we split it on the `,`, the `String` array would have the elements "Suzie",
    "Bingham", and "A" in it.
- We'll get the first name and last name by using the square brackets to access
  the first two elements (indexes 0 and 1) from the `studentProperties` array.
- Since the letter grade is a character, and we are only expecting one
  character, we can just simply call the `charAt()` method and provide it the
  parameter of `0` since it is assumed to be the only character.

Now we'll write some code to read the CSV. This, again, will reuse most of the
code we wrote to read a text file. To make our program a little more flexible,
we'll also have the user pass in the file as a command-line argument:

```java
import java.io.File;
import java.io.IOException;
import java.util.Scanner;

public class FileIOMain {
    public static void main(String[] args) {
        if (args.length != 1) {
            System.out.println("Please specify one file path as a command-line argument.");
        } else {
            File file = new File(args[0]);
            try (Scanner reader = new Scanner(file)) {
                while (reader.hasNextLine()) {
                    Student student = new Student(reader.nextLine());
                    System.out.println(student);
                }
            } catch (IOException ioException) {
                ioException.printStackTrace();
            }
        }
    }
}
```

In the `while` loop, notice that instead of printing the content in the file,
we pass in the content to the `Student` constructor that we just created. This
will then parse the file appropriately to create a `Student` object. We'll then
print out `student` to ensure that everything was created correctly. Let's pass
in the `student.csv` file now through IntelliJ and run the code! The following
is the output:

```text
Suzie Bingham has the letter grade A
```
