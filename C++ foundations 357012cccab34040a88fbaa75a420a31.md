# C++ foundations

Created: December 25, 2021 1:41 PM

[References](https://www.notion.so/References-c00389657cbc4f7898235c5ab4c4f2be)

# `Nanodegree`

- compiled language close to hardware
- Order of major release : C++11> C++17 > C++14 > C++20
- strongly statically typed

### Vector

- write functions by passing variables by reference and even in range based loops use variables by reference so that the original object/structure is modified and no space is wasted in creating a copy.
**Trick**: pass variables by reference and keep the formal parameter as const so that the original is not modified.

```cpp
int AdditionFunction(const vector<int> &v) //v is const and referenced i.e v will only have value of original vector and cannot be updated
{
    int sum = 0;
    for(const int &i : v) // i is const and referenced i.e i will only be used to read from v and cannot be updated.
        sum += i;
    return sum;
}

int main() 
{
    vector<int> v {1, 2, 3};
    
    // Uncomment the following line to call your function:
    cout << AdditionFunction(v) << "\n";
}
```

- In vectors : v.begin() and v.end() return an **iterator** which is nothing but a pointer. So to use this iterator declare it using **auto**.

```cpp
int AdditionFunction(const vector<int> &v)
{
    int sum = 0;
    for(auto i = v.begin(); i != v.end(); ++i) // i is iterator, v.end() points to position after the last element
        sum += *i; // dereference the iterator
    return sum;
}

int main() 
{
    vector<int> v {1, 2, 3};
    
    // Uncomment the following line to call your function:
    cout << AdditionFunction(v) << "\n";
}
```

### Reading from file

- Read from file using **fstream**

```cpp
#include <fstream>
#include <iostream>
#include <string>

int main() {
    std::ifstream my_file;
    my_file.open("files/1.board");
    if (my_file) {
        std::cout << "The file stream has been created!" << "\n";
        std::string line;
        while (getline(my_file, line)) {
            std::cout << line << "\n";
        }
    }
}
```

- Steps to read from a file :

```cpp
1. #include <fstream>
2. Create a std::ifstream object using the path to your file.
3. Evaluate the std::ifstream object as a bool to ensure that the stream creation did not fail.
3. Use a while loop with getline to write file lines to a string.
4. getline returns false when there is no line to return otherwise true.
```

- A stream is a sequence of bytes and if these bytes are to be interpreted as string then create a string stream.

```cpp
#include <iostream>
#include <sstream>
#include <string>

using std::istringstream;
using std::string;
using std::cout;

int main() 
{
    string a("1 2 3");

    istringstream my_stream(a);

    int n;
    
    // Testing to see if the stream was successful and printing results.
    while (my_stream) {
        my_stream >> n;
        if (my_stream) { //CHECKS IF LAST EXTRACTION OPERATION WAS SUCCESSFULL OR NOT
            cout << "That stream was successful: " << n << "\n";
        }
        else {
            cout << "That stream was NOT successful!" << "\n";            
        }
    }
}
```

### Important Terms

**Const, constexpr**
The major difference between `const` and `constexpr`, though, is that `constexpr` must be evaluated at compile time. When in doubt, use `const`, especially to guard against accidentally modifying a variable.

**Reference**
Used in pass-by-reference for functions, and in a range-based`for` loop example that used references to modify a vector

```cpp
#include <iostream>
using std::cout;

int main() 
{
    int i = 1;
    
    // Declare a reference to i.
    int& j = i;
    cout << "The value of j is: " << j << "\n";
    
    // Change the value of i.
    i = 5;
    cout << "The value of i is changed to: " << i << "\n";
    cout << "The value of j is now: " << j << "\n";
    
    // Change the value of the reference.
    // Since reference is just another name for the variable,
    // th
    j = 7;
    cout << "The value of j is now: " << j << "\n";
    cout << "The value of i is changed to: " << i << "\n";
}
```

**Pointers:** Acc to Bjarne, pointer is machine address with type associated with it at compile time. Type is associated for pointer arithmetic ease.

- & —> this operator is overloaded to be used to access memory addresses and to pass references into function. If it appears on the left side of an equation (e.g. when declaring a variable), it means that the variable is declared as a reference. If the `&` appears on the right side of an equation, or before a previously defined variable, it is used to return a memory address.

```cpp
int j = 5;
int &i = j; //i is reference variable to j

int x = 6;
int *y = &x; // x's memory address stored in y
```

- **Passing pointers inside function —>** 
Another form of pass by reference, If the pointer is pointing to a large object, it can be much more efficient to pass the pointer to a function than to pass a copy of the object as with pass-by-value.

```cpp
#include <iostream>
using std::cout;

void AddOne(int* j)
{
    // Dereference the pointer and increment the int being pointed to.
    (*j)++;
}

int main() 
{
    int i = 1;
    // Declare a pointer to i:
    int* pi = &i;
    AddOne(pi);
    cout << "The value of i is now: " << i << "\n";
}
```

- **Passing variable as reference in function and returning pointer to reference —>**
In the example below, a reference is passed into a function and a pointer is returned. This is safe since the pointer being returned points to a reference - a variable that exists outside of the function and will not go out of scope in the function.

```cpp
#include <iostream>
using std::cout;

int* AddOne(int& j) 
{
    // Increment the referenced int and return the address of j.
    j++;
    return &j;
}

int main() 
{
    int i = 1;    
    // Declare a pointer and initialize to the value returned by AddOne:
    int* my_pointer = AddOne(i);
    cout << "The value of i is now: " << i << "\n";
    cout << "The value of the int pointed to by my_pointer is: " << *my_pointer << "\n";
}
```

- **Care to be taken while passing pointers in function —>**

Demonstration of : **you must be careful that the object being pointed to doesn't go out of scope when the function finishes executing. If the object goes out of scope, the memory address being pointed to might then be used for something else.**

```cpp
#include <iostream>
using std::cout;

void AddOne(int* (&j)) //pass pi as reference so j is another name for pi

{
    // Dereference the pointer and increment the int being pointed to.
    cout<< "*j = " << *j <<"\n";
    int x = 23;
    j = &x;
    cout<< "*j = " << *j <<"\n";
    cout<< "The value of j is: " << j <<"\n";
}

int main() 
{
    int i = 1;
    cout << "The value of i is: " << i << "\n";
    
    // Declare a pointer to i:
    int* pi = &i;
    AddOne(pi);
    cout << "The value of i is now: " << i << "\n";
    cout << "The value of *pi is now: " << *pi << "\n";
    cout << "The value of pi is: " << pi <<"\n";
}

OUTPUT:
The value of i is: 1
*j = 1
*j = 23
The value of j is: 0x7ffd3f00cb44
The value of i is now: 1
The value of *pi is now: 32765 //GARBAGE
The value of pi is: 0x7ffd3f00cb44

STEPS:
1. Declare pointer pi to i
2. Pass pi pointer as reference and accept as j
3. Alter j to point to some other int which goes out of scope after duration of function
4. Now pi/j are still pointing to the same address but after the function gets over x is out of scope
   so now pi is pointing to address that holds some garbage value.

```

```cpp
#include <iostream>
using std::cout;

int* AddOne(int* j) 
{
    int x = 23;
    j = &x; //now j pointing 
    cout<< "*j = " << *j <<"\n";
    return j;
}

int main() 
{
    int i = 1;
    cout << "The value of i is: " << i << "\n";
    int* pi = &i;
    // Declare a pointer and initialize to the value
    // returned by AddOne:
    int* my_pointer = AddOne(pi);
    cout << "The value of i is now: " << i << "\n";
    cout << "The value of the int pointed to by my_pointer is: " << *my_pointer << "\n";
}

OUTPUT:
The value of i is: 1
*j = 23
The value of i is now: 1
The value of the int pointed to by my_pointer is: 32767 //GARBAGE

STEPS:
1. Declare pointer pi to i
2. Pass pi into function that accepts pi as j and returns the pointer j
3. inside function, modify j to point to some local variable x and return j
4. In main function, now my_pointer holds address that j did but it holds garbage value as the local
   variable is now out of scope.
```

[Difference in Reference and Pointers](https://www.notion.so/735ebfd6fac94ba8b5ac00eed3336594)

## Classes and Objects

### header files

Header files, or `.h` files, allow related function, method, and class declarations to be collected in one place. 
>> The corresponding definitions can then be placed in `.cpp` files. The compiler considers a header declaration a "promise" that the definition will be found later in the code, so if the compiler reaches a function that hasn't been defined yet, it can continue on compiling until the definition is found. 
>> **This allows functions to be defined (and declared) in arbitrary order**.

- The function declarations in the header file don't need variable names, just variable types. You can put names in the declaration, however, and doing this often makes the code easier to read.
- The `#include` statement for the header used quotes `" "` around the file name, and not angle brackets `<>`. We have stored the header in the same directory as the `.cpp` file, and the quotes tell the preprocessor to look for the file in the same directory as the current file - not in the usual set of directories where libraries are typically stored.
- Finally, there is a preprocessor directive:
#ifndef HEADER_EXAMPLE_H 
#define HEADER_EXAMPLE_H
At the top of the header, along with an `#endif` at the end. This is called an "include guard". Since the header will be included into another file, and `#include`just pastes contents into a file, the include guard prevents the same file from being pasted multiple times into another file. This might happen if multiple files include the same header, and then are all included into the same main.cpp
- `#include`  libs in header file that are required in the header file itself like vector is req in below example.

```cpp
// This file will be saved as "header_practice.h"
#ifndef HEADER_PRACTICE_H
#define HEADER_PRACTICE_H
#include <vector>   //INCLUDE LIBS in HEADER THAT ARE REQUIRED AHEAD EG. VECTOR USED IN PARAM LIST

int IncrementAndComputeVectorSum(std::vector<int>);
void AddOneToEach(std::vector<int>&);

#endif
```

### Code structure

- Separate code in a way that all declarations are in header .h file and all definitions are in .cpp file.
- Write main.cpp that includes header whose functions are being called directly in main.cpp.

## Build Systems

### Steps in compilation using g++

1. The preprocessor runs and executes any statement beginning with a hash symbol: `#`, such as `#include` statements. This ensures all code is in the correct location and ready to compile.
2. Each file in the source code is compiled into an "object file" (a `.o` file).
3. The object files are "linked" together to make a single executable like a.out

```cpp
g++ -c *.cpp // creates object files
g++ *.o // creates executables
./a.out
```

Recompile only those cpps that have been changed but keeping track of that is hard hence CMake or Make is used.