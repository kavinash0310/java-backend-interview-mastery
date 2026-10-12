# Online Assessment Practice

This is a small starter set for the multiple-choice areas mentioned in the interview notes. Read every option before choosing; the explanation is the part worth remembering.

## OOP: method overriding

Given <code>Shape shape = new Circle();</code>, which implementation runs when <code>shape.draw()</code> is called, assuming Circle overrides draw?

A. Shape's method, because the variable type is Shape  
B. Circle's method, because the object's runtime type is Circle  
C. Both methods, in alphabetical order  
D. Compilation fails

**Answer: B.** Overridden instance methods use runtime dispatch. The variable's declared type controls which methods are available at compile time, while the object's class selects the override at runtime.

## Exception handling: checked exceptions

A method calls <code>Files.readString(path)</code> and does not catch the failure. What must the method do?

A. Nothing; IOException is unchecked  
B. Catch IOException or declare it with throws  
C. Catch every possible Throwable  
D. Add <code>@Override</code>

**Answer: B.** IOException is checked, so the compiler requires handling or declaration. Catch the narrow failure the method can recover from; do not catch Throwable as a shortcut.

## Database normalization

An Orders table repeats the same customer address on every order row. What is a likely reason to separate customer details into a Customers table?

A. To create update anomalies and duplicate data  
B. To reduce duplicated customer data and update anomalies  
C. To avoid all joins  
D. To make every query faster automatically

**Answer: B.** Normalization stores each fact in the place it belongs. A customer address update then changes one customer row instead of many order rows. Normalization may require joins; it does not guarantee every query will be faster.

## Aptitude: percentage

A service handled 250 requests in one minute. Twenty percent returned an error. How many requests failed?

A. 25  
B. 40  
C. 50  
D. 75

**Answer: C.** Ten percent of 250 is 25, so twenty percent is 50.

## Coding questions from the same round

For the first non-repeating character and linked-list cycle questions, use the [coding interview guide](../data-structures-algorithms/coding-interview-questions.md). It includes Java solutions, complexity, and the key edge cases to explain aloud.
