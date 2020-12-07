---
layout: "post"
title: "Python C extensions"
date: "2017-03-04 22:16"
comments: true
---

This part is used for those who want to build faster functions in `c/c++`, and import the `xxx.so` or `xxx.dll` in python, so that the overall codes can run faster with the embedded `c/c++` languages supports.

### 1. C Foreign Function Interface (CFFI)

`CFFI <https://cffi.readthedocs.io/en/latest/>` provide a simple to use mechanism for interfacing with C from CPython and PyPy. It supports two model:

1. a inline ABI compatibility model, you can dynamically load and run functions from executable modules
2. an API model, can build C extension modules.

ABI Interaction
```python
from cffi import FFI
ffi=FFI()
ffi.cdef("size_t strlen(const char*);")
clib=ffi.dlopen(None)
length=clib.strlne(String to be evaluated.)
#print:23
print("{}".format(length))
```


### 2. Ctypes

the ctypes modules provides C compatible data types and functions to load DLLs so that calls can be made to C shared libraries without having to modify them.

Simple C code to add two numbers, save it as `add.c`
```c++
// sample C file to add 2 numbers -int and floats
#include<stdio.h>

int add_int(int,int);
int add_float(float,float);
int add_int(int p,int q){ return p+q;}
float add_float(float p,float q){ return p+q;}
```

Next we compile the C file to a `.so` file (or `DLL`) :

```bash
gcc -shared -Wl,-soname,adder -o adder.so -fPIC add.c
```

Now in your python code:
```python
from ctypes import *
# load the shared object file
adder=CDLL('./adder.so')
# Find sum of integers
res_int=adder.add_int(4,5)
print "Sum of 4 and 5 =" +str(res_int)
# >>> Sum of 4 and 5 = 9
# find sum of floats
a=c_float(5.5)
b=c_float(4.1)

add_float=adder.add_float
add_float.restype=c_float
print "Sum of 5.5 and 4.1 = ", str(add_float(1,b))
# >>> Sum of 5.5 and 4.1 =  9.60000038147
```

### 3. Simplified Wrapper and Interface Generator (SWIG)
In this method, the developer must develop an extra interface file which is an input to SWIG.  This is a great method when you have a C/C++ code base, and you want to interface it to many different languages.

The C code `example.c` that has avariety of functions and variables:

```c
#include<time.h>

double my_variable=3.0;
int fact(int n){
  if(n<1) return 1;
  else  return n*fact(n-1);
}
int my_mod(int x,int y){ return (x%y);}
char* get_time(){
  time_t ltime;
  time(&ltime);
  return ctime(&ltime);
}
```

The interface file `example.i` - this will remain the same irrespective of the language you want to port yout C code:

```
/* example.i */
 %module example
 %{
 /* Put header files here or function declarations like below */
 extern double My_variable;
 extern int fact(int n);
 extern int my_mod(int x, int y);
 extern char *get_time();
 %}

 extern double My_variable;
 extern int fact(int n);
 extern int my_mod(int x, int y);
 extern char *get_time();
```

And compile it:

```
unix % swig -python example.i
unix % gcc -c example.c example_wrap.c \
        -I/usr/local/include/python2.1
unix % ld -shared example.o example_wrap.o -o _example.so
```

Finally, the Python output:

```python
>>> import example
>>> example.fact(5)
120
>>> example.my_mod(7,3)
1
>>> example.get_time()
'Sun Feb 11 23:01:07 1996'
```
It's too complex.

### 4. C/Python API
Its the most widely used method - it’s simplicity and you can manipulate python objects in your C code.

All python object are represented as `PyObject` struct which is defined in `Python.h` header file. For example, if the `PyObject` is also a `PyListType` (basically a list), the we can use the `PyList_Size()` for the length of the list ( equal to `len(list)`). Most of the basic functions/operations are defined in `Python.h`

`adder.c`:

```c
// Python.h has all the required function definitions to manipulate the Python objects.
#include<Python.h>

// This is the function that is called from your python code
static PyObject* addList_add(PyObject* self,PyObject*args){
  PyObject* ListObj;

  //The input arguments come as a tuple, we parse the args to get the various variables
  //In this case it's only one list variable, which will now be reference by ListObj
  if(! PyArg_ParseTuple(atgs,"O",&ListObj)){
    return NULL;
  }

  // length of the list
  long length=PyList_Size(listObj);

  //iterate over all the elements
  int i,sum=0;
  for(i=0;i<length;i++){
    // get an element out of the list - the element is also a python objects
    PyObject* temp=PyList_GetItem(listObj,i);
    // we know that object reprsents an integer - so convert it into C long
    long elem=PyInt_AsLong(temp);
    sum+=elem;
  }

  // value returned back to python code - another python object
  // build value here convers the C long a python integer
  return Py_BuildValue("i",sum);
}

/* This table contains the relavent info mapping -
 <function-name in python module>, <actual - function>,
 <type-of-args the function expects>, <docstring associcated with the function>
*/
static PyMethodDef addList_funcs[]={
  {"add",(PyCFunction)addList_add,METH_VARAGES,addList_docs},
  {NULL,NULL,0,NULL}
};

/*
addList is the module name, add this is the initialization blockof the module.
<desired module name>,<the-info-table>,<module's-docstring>
*/
PyMODINIT_FUNC initaddList(void){
  Py_InitModule3("addList", addList_funcs,
                  "Add all the lists");
}
```

1. the `<Python.h>` file consists of all the required types (to represent Python Object types) and function definitions (to operate on the python objects).
2. Next we write the function which we plan to call from python. {module-name}_{function-name}, which in this case is `addList_add`.
3. Then fill in the information table - contains all the relevant information of the functions.
4. the module initialization block which is of the signature `PyMODINIT_FUNC init{module-name}`



Now we build the C module. Save the following code as `setup.py`
```python
# build the modules
from distutils.core import setup, Extensions
setup(name="addList",version='1.0', ext_modules=[Extension('addList',['adder.c'])])
```

and run

```python
python setup.py install
```

This should now build and install the C file into the python module we desire. After all this hard work, we'll now test if the module works.

```python
# module that talks to the C  code
import addList
l=[1,2,3,4,5]
print "Sum of List - "+str(l)+" = " + str(addList.add(l))
```
Add here is the output
```python
Sum of List - [1, 2, 3, 4, 5] = 15
```
So as you can see, we have developed our first successful C Python extension using the `<Python.h>` API.


### References:
 - [Intermediate Python](http://book.pythontips.com/en/latest/python_c_extension.html);
 - [Hitchhiker's Guide to Python](http://docs.python-guide.org/en/latest/scenarios/clibs/).
