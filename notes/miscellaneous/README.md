# Miscellaneous


### Bootstrapping

- The term "bootstrapping" usually applies to a situation where a system depends on itself to start.
- Example
  -  How do you start an OS initialization process if you don't have the OS running yet?
  -  How do you compile a C compiler written in C?
- In that case, bootstrapping refers to a way of breaking the circular dependency, usually with the help of an external entity.
- Example
  - You can use another C compiler to compile (bootstrap) your own compiler, and then you can use it to recompile itself
  - You use a separate piece of code that sets up the initial process without depending on any functions provided by the OS 
