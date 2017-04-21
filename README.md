# rustVsCpp
A comparison of Rust and Cpp reference/pointer types.  I'm an experienced C++ programmer but Rust N00b, so pull requests are welcome.

# rust `&T`
This is basically equivalent to a C++ `const T*` type, a pointer that may be reseated (made to point somewhere else), if the variable storing it is mutable.  However, it may not modify the object on the other end of the pointer.  
## Example
```
//RUST, types annotated for clarity
let a :i32  = 3; 
let b : i32 = 5;
let c : &i32 = &a; //c points to a
*c = 4; //ILLEGAL, cannot change the referrant
c = &b; //ILLEGAL, c is not mutable
let mut d : &i32 = &a;  //d points to A 
d = &b; //OK, d is mutable, so it can point somewhere else
*d = 4; //ILLEGAL, cannot change the referrant

//C++ equivalent
int a = 3, b = 5;
const int* const c = &a;
*c = 4; //ILLEGAL, cannot change the referrant
c = &b; //ILLEGAL, c is const as well, so it can't be reseated
const int* d = &a; 
d = &b; //fine, d can be reseated
```
# rust `&mut T`
This is eqiuvalent to a C++ `T*`.  You may modify the referrant.  You may reseat the pointer if the variable holding it is mutable.  Rust ensure that you can only have one mutable reference at a time.

## Example
```
//RUST, types annotated for clarity
let mut a : i32 = 3;
let mut b : i32 = 5;
let c : i32 = 7;
let d : &mut i32 = &mut a; //d points to a now
*d = 10; //OK, change a
d = &mut b; //ILLEGAL, d is not mutable, so it can't be reseated
let mut e : &mut i32;
e = &a; //illegal ince d already has a mutable reference to a
e = &b; //fine, no one has a reference to b;
e = &c; //Illegal, c is not mutable


