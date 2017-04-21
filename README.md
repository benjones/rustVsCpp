# rustVsCpp
A comparison of Rust and Cpp reference/pointer types.  I'm an experienced C++ programmer but Rust N00b, so pull requests are welcome.

# rust `&T`
This is basically equivalent to a C++ `const T*` type, a pointer that may be reseated (made to point somewhere else), if the variable storing it is mutable.  However, it may not modify the object on the other end of the pointer.  
## Example
```
//RUST, types annotated for clarity
let a :i32  = 3; 
let b : i32 = 5;
let c : &i32 = a; //c points to a
*c = 4; //ILLEGAL, cannot change the referrant
c = &b; //ILLEGAL, c is not mutable
let mut d : &a;  //d points to A 
d = &b; //OK, d is mutable, so it can point somewhere else

//C++ equivalent
int a = 3, b = 5;
const int* const c = &a;
*c = 4; //ILLEGAL, cannot change the referrant
c = &b; //ILLEGAL, c is const as well, so it can't be reseated
const int* d = &a; 
d = &b; //fine, d can be reseated
