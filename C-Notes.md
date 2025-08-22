  

---

# C Notes – Week 1 Prep (Memory & Hacking Focus)

## 1️⃣ Format Specifiers

|   |   |   |
|---|---|---|
|Specifier|Type|Notes / Use Case|
|%d|int|Signed decimal integer|
|%u|unsigned int|Unsigned decimal integer|
|%x|unsigned int|Hexadecimal (lowercase)|
|%X|unsigned int|Hexadecimal (uppercase)|
|%ld|long int|Long decimal integer|
|%lu|unsigned long|Unsigned long decimal|
|%lx|unsigned long|Unsigned long hexadecimal|
|%f|float|Floating point|
|%lf|double|Double precision floating point|
|%c|char|Single character|
|%s|char *|Null-terminated string|
|%p|void *|Pointer address in hex (useful for stack/heap)|
|%%|—|Literal % sign|

---

## 2️⃣ Variables & Types

- Basic types: int, float, double, char  
      
    
- sizeof(type) → size in bytes (important for reading/writing memory)  
      
    

int health = 100;

float speed = 3.5;

  

Hackers need type sizes when scanning or modifying memory.

---

## 3️⃣ Pointers

- Store memory addresses  
      
    
- Dereference with * to read/write value  
      
    

int health = 100;

int *ptr = &health; // & gives the address

*ptr = 200;         // change the value via pointer

  

Game values live somewhere in memory—knowing the address and type is critical.

---

## 4️⃣ Arrays & Memory

- Contiguous blocks of memory  
      
    

int arr[5] = {1,2,3,4,5};

int *p = arr; // points to first element

  

Hackers often scan memory linearly, so understanding arrays is key.

---

## 5️⃣ Heap & Stack

- Stack: local variables, auto-cleaned  
      
    
- Heap: dynamically allocated, persists until free()  
      
    

int *heap_num = malloc(sizeof(int));

*heap_num = 42;

free(heap_num);

  

Many in-game values are on the heap, so understanding allocation matters.

---

## 6️⃣ Structs

- Group related data (e.g., game entities)  
      
    

struct Player {

    int health;

    float x, y;

};

  

struct Player p1 = {100, 10.0, 20.0};

struct Player *ptr = &p1;

ptr->health = 200; // arrow -> access member via pointer

  

Useful for interpreting memory layouts in games.

---

## 7️⃣ Reading/Writing Memory

#include <stdio.h>

#include <stdint.h>

  

int main() {

    int value = 123;

    int *ptr = &value;

    printf("Address: %p, Value: %d\n", (void*)ptr, *ptr);

    *ptr = 456;

    printf("New Value: %d\n", value);

}

  

In Rust or Go, you do similar operations using unsafe / FFI.

---

## 8️⃣ Casting & Alignment

- Cast pointers to read memory as different types:  
      
    

void *addr = some_address;

int *iptr = (int*)addr;

float *fptr = (float*)addr;

  

- Alignment matters: reading a float at an int boundary can crash the program.  
      
    

---

## ✅ Key Takeaways

- Know your types and sizes (sizeof())  
      
    
- Understand pointers and dereferencing  
      
    
- Learn heap vs stack → most game values are on the heap  
      
    
- Understand struct layouts for memory interpretation  
      
    
- Casting & alignment are critical (friends or enemies!)  
      
    

---