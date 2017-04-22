# Rust vs Cpp pointers/references
A comparison of Rust and Cpp reference/pointer types.  I'm an experienced C++ programmer but Rust N00b, so pull requests are welcome.

# rust unsafe pointers: `* mut T` and `* const T`

These are the same as a `T*` and `const T*` from C++.  They work about the same as in C++.  They might be null and have no checked ownership semantics.  They are (or at least should be) rare in most user code.

# rust `&T`
This is basically equivalent to a C++ `const T*` type, a pointer that may be reseated (made to point somewhere else), if the variable storing it is mutable.  However, it may not modify the object on the other end of the pointer.   It must always point to a valid T, and cannot point to null.  You can think of them as compile-time checked versions of `non_null<const T>` smart pointers.
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
const int a = 3;
const int b = 5;
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
//d = &mut b; //ILLEGAL, d is not mutable, so it can't be reseated
let mut e : &mut i32;
//e = &a; //illegal ince d already has a mutable reference to a
e = &b; //fine, no one has a reference to b;
//e = &c; //Illegal, c is not mutable

//C++
int a = 3, b = 5;
const int c = 7;
int* const d = &a; //d can't change, but it points to somethign that can
*d = 10; //OK, d didn't change
//d = &b; //ILLEGAL, changes d itself
int* e; // e can change, as can the thing it points to
//e = &a, legal in c++ since there's no borrow checker
e = &b;  //OK, e points to b;
e = &c; //ILLEGAL, c is const



```

# Automatic Deferencing
If you try to index a reference, or call a method on a reference, Rust will automatically derefence as many times a necessary.  
```
 let v = vec![1,2,3];
 let rv = &v;
 let rrv = &rv;
 let rrrv = &rrv;
 let one = rrv[0];  //C++ would reauire (***rrrv )[0]
 let three = rrrv.len(); //C++ would require (***rrrv).len();
 ```
 If you're assigning to a reference, you have to deref yourself.  
 ```
 let mut a = 5;
 let rra = &mut &mut a;
 //rra = 6; //ILLEGAL, must derefence manually
 **rra = 6;
 ```
 
 # Box, Rc, and Arc
 `Box<T>` is equiavalent to `std::unique_ptr<T>`, and `Rc<T>` is equivalent to a *single threaded* version of `std::shared_ptr<const T>`.  They don't use an atomic reference counter, so they are unsafe to share between threads (and the compiler won't let you).  `Arc<T>` uses an atomic reference count so it can be shared between threads.  If you need a mutable shared reference, check out Cell and RefCell.
 ```
 let mut b = Box::new(5);
*b = 6;
use std::rc::Rc;
let mut rc = Rc::new(10);
//*rc = 11; //ILLEGAL, the int is const, even though rc itself is mutable.  
let other = rc.clone(); //other is a Rc<int> pointing to the same int as rc
let weak = Rc::downgrade(&rc); //weak is like a std::weak_pointer<const T>, and won't increment the reference count.  
                               //Useful for breaking ownership cycles
 ```
 

