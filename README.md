# CS 104 Midterm Review

Sometimes, the best way to understand a concept
is seeing minimalistic snippets of code. Feel free
to clone, pull or contribute to this.

Except when not found, all definitions shown below 
are taken from the 
[official lecture notes](http://www-bcf.usc.edu/%7Edkempe/teaching/DataStructures.pdf)
or the lecture handouts available in the [course website](http://bits.usc.edu/cs104/schedule.html). For such exceptions, a source is quoted and
credited.

The snippets of code were written by me, a student. Although
they were compiled and tested without errors, they are not official
from the staff. They are meant to be simplified versions devoided
from distractions to better illustrate a concept.

Also, notice that there are C++ files included in this repo
containing the examples shown below. You are welcome to browse, 
compile and execute them.

## Recursion

*Lecture notes p. 27*

The adjective recursive means “defined in terms of itself”. 
Here is an example with the fibonacci sequence.

```c++
#include <iostream>

using namespace std;

int fib(int n) {
    
    if(n == 1 || n == 0) return n;
    
    return fib(n - 1) + fib(n - 2);
}

int main(void)
{
    // print the first 15 number of the fibonacci series
    for(int i = 0; i < 15; i++) {
        cout << fib(i) << endl;
    }
}
```

## Overloading

*Lecture notes p. 73*

Function overloading simply means having multiple functions 
of the same name, but with different types of parameters.

Operators are symbols such as ==, !=, = (assignment), 
<=, +, -, ++, * (multiplication, and dereferencing), 
[], <<, >>, and many others. 

Using operators is really just shorthand for calling a function. 

When you write the following code:
    if ( a1 == a2 ) cout << "They’re equal!";

it actually gets translated to
    if (operator==(a1, a2)) cout << "They’re equal!";

In this example, we use overload + to add a string and a char.

```c++
#include <iostream>
#include <sstream>

using namespace std;

string operator+(string s, char c) {

    // we use string stream
    stringstream ss;

    // from char to string stream
    ss << c;

    // here we save the char to string
    string c_as_string;
    ss >> c_as_string;

    // we append the former char and return
    s.append(c_as_string);
    return s;
}

int main(void)
{
    // overloading
    string coo = "coo";
    char l = 'l';

    // calls overload and prints "cool"
    cout << coo + l << endl;
}
```

## Inheritance

*Lecture notes p. 90*

**public:** everyone (other classes, this class) can access the field.

**private:** only objects of the same class can access the field. 
One object of class A may access the private fields of 
another object of class A.

**protected:** only objects of the same or inheriting classes can access the field.

```c++
#include <iostream>

using namespace std;

class Parent {
public:
    
    void playFunction() {
        
        // from here I have access to everything of parent
        cout << _public + _protected + _private << endl;
    }
    
    string _public = "public ";

protected:
    string _protected = "protected ";
    
private:
    string _private = "private ";
};

class Child : private Parent {

public:
    // from here I only have access to _public and _private
    void play() {
        cout << _public + _protected << endl;
        
        // but i do not have access to _private
        // cout << _private; // compiler error
    }
    
};

int main(void)
{
    Parent parent;
    parent.playFunction();
    
    cout << parent._public << endl;
    
    // but i cannot invoke parent._protected or parent._private
    
    Child child;
    child.play();
}
```

## Friend functions

*Lecture notes p. 78*

What we’d really like to do is say that these variables are 
still private, but we’ll make an exception just for this one 
particular function operator\*. 
C++ has a mechanism for doing this: 
you do this by declaring a function a friend of a class

```c++
#include <iostream>

using namespace std;

class Book {
public:
    Book(string name): _name(name) {}
    ~Book() {}

    void getName() { cout << _name << endl; }

    // friend declaration
    friend void appendName(Book &b);

private:
    string _name;

};

/***
* notice that it is able to access the private data
**/
void appendName(Book &b) {
    b._name = b._name + " written by Alex ";
}

int main(void)
{
    Book b("Moby Dick");
    b.getName(); // prints Moby Dick

    appendName(b);
    b.getName(); // prints Moby Dick written by Alex
}
```



## Deep copy

*No official definition was found on the 104 class notes
or handouts. The following definition is taken from 
[freedosaurus.com](http://www.fredosaurus.com/notes-cpp/oop-condestructors/shallowdeepcopy.html)*

A shallow copy of an object copies all of the member field values. This works well if the fields are values, but may not be what you want for fields that point to dynamically allocated memory. The pointer will be copied. but the memory it points to will not be copied -- the field in both the original object and the copy will then point to the same dynamically allocated memory, which is not usually what you want. The default copy constructor and assignment operator make shallow copies.

A deep copy copies all fields, and makes copies of dynamically allocated memory pointed to by the fields. To make a deep copy, you must write a copy constructor and overload the assignment operator, otherwise the copy will point to the original, with disasterous consequences.

### The Problem

```c++
#include <iostream>

using namespace std;

class Student {
public:
    Student(string name):_name(name){}
    string _name;
};


int main(void)
{
    Student* a = new Student("alex");
    
    // this is a problem because both students
    // are pointing to the same object
    Student* b = a;
    
    // so if I mutate b, you mutate a as well
    b->_name = "sergio";
    
    // will print sergio
    cout << "a: " << a->_name << endl;
    
}
```

### Solution

The solution is deep copy. However, it is something you
have to write yourself. The rule is to include these three
things.

1. Constructor
2. Destructor
3. Operator

Here is a simple example. A word of warning: the operator
has not been implemented.

```c++
#include <iostream>

using namespace std;

class Student {
    public:
        Student(int id, string name):
            _id(id), _name(name) {}
        int _id;
        string _name;

    Student* deepCopy() {
        return new Student(_id, _name);
    }
    Student& operator=(const Student &rhs) {
        if( this == &rhs ) return *this;

        this->_id = rhs._id;
        this->_name = rhs._name;

        return *this;
    }
};

int main(void) {

    // object
    Student* student = new Student(1, "alex");
    Student* cp = student->deepCopy();

    cp->_name = "sergio";

    cout << "st: " << student->_name << endl;
    cout << "cp: " << cp->_name << endl;

}
```

## Abstract Class

*Lecture notes p. 94*


Of course, if a class has one or more pure virtual functions in it, the class cannot be instantiated. Such a class is called an abstract class. No object can be created from an abstract class.

```c++
#include <iostream>

using namespace std;

class Abstract {
public:
    // pure virtual
    virtual void print() = 0;
};

class Child : Abstract {
public:

    // compiler enforces you to implement this
    void print() { cout << "Implemented" << endl; }
};

int main(void)
{
    Child child;
    child.print();

    // not allowed because it is an abstract class
    // Abstract a;
}
```


## Polymorphism

*Lecture notes p. 96*

Determining which version of a class member function to call at runtime — is called polymorphism, which literally means “many forms”: the object stored in a variable could be one of many forms, and the execution will do “the right thing” for the current object.

Here is a simple example.

```c++
#include <iostream>

using namespace std;

class Shape {
public:
    Shape()     {}
    virtual ~Shape()    {}
    virtual void print(){ p( "print shape" );}

protected:
    void p(string s) {cout << s << endl;}

};


class Rectangle : public Shape {
public:
    Rectangle() {}
    ~Rectangle(){}

    void print() { p("print rectangle"); }

};

int main(void)
{

    Rectangle* s = new Rectangle();
//    s->print(); // print rectangle called

    Shape* t = new Shape();
//    t->print(); // print shape

    // pointer to iterate
    Shape* u;
    u = s;
    u->print(); // print rectangle

    u = t;
    u->print(); // print shape


    delete s;
    delete t;
}
```

## Sorting Algorithms

### Bubble Sort

*From [Lecture slides, p 6](https://piazza-resources.s3.amazonaws.com/jc0zlm0ibz12df/jdz47r0f1qr2y0/L14_sorting.pdf?AWSAccessKeyId=AKIAIEDNRLJ4AZKBW6HA&Expires=1519941734&Signature=EE3ytUXJDww3Z1vbmkgS4OZi6%2Bk%3D)*

Main Idea: Bubble up the largest value to the greatest index on list

- Comparing neighbors, moving larger item up and smaller item down until largest item is at greatest index. Repeat on list of remaining elements.
- Outer loop counts each pass and keeps largest unsorted index
- Inner loop starts at the lowest index bubbling up largest element to the greatest unsorted index

```c++
#include <iostream>

using namespace std;


void printVector(vector<int> v) {
    for(int i = 0; i < v.size(); i++) {
        cout << v[i] << " ";
    }
    cout << endl;
}

void bsort(vector<int> mylist) {
    
    for(int i = (int) mylist.size() - 1; i > 0; i--) {
        
        for(int j = 0; j < i; j++) {
            
            // compares with neighbor
            if( mylist[j] > mylist[j + 1] ) {
                
                // swap j and j + 1
                int temp = mylist[j];
                mylist[j] = mylist[j + 1];
                mylist[j + 1] = temp;
            }
            printVector(mylist);
        }
    }
}

int main(void)
{
    vector<int> v;
    v.insert(v.end(), {7, 3, 8, 6, 5, 1});
    
    cout << "Printing vector: " << endl;
    printVector(v);
    
    cout << "Bubble sorting: " << endl;
    bsort(v);
}
int main(void)
{
    // create and populate vector
    vector<int> v;
    int array[] = {7, 3, 8, 6, 5, 1};
    v.insert(v.end(), array, array+6);

    // printing vector
    cout << "Printing vector: " << endl;
    printVector(v);

    cout << "Bubble sorting: " << endl;
    bsort(v);
}
```
