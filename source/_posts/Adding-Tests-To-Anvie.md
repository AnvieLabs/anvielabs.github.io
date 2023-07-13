---
title: Adding Tests To Anvie
date: 2023-07-13 21:55:19
tags:
- anvie
- utils
- tests
- design-decision
- developent
- system
categories:
- utils
---

| Date            | Author           |
|-----------------|------------------|
| July, 13th 2023 | Siddharth Mishra |

I created a very simple unit testing mechanism to create tests in `AnvieUtils` and it's dependent libraries. `AnvieUtils` is a utility module
for Anvie that, for now, just provides generic containers. Later on I wish to create a custom allocator inside this in order to provide faster memory operation and a more data oriented approach. `AnvieUtils` is supposed to be one single module to fill all basic utility requirements for any dependent library, so, it's supposed to be present in all other modules in Anvie. Therefore, it makes sense to have tests declared here.

If you want to create a new unit test suite, you'll first want to create tests for all units

```c
#include <Anvie/Containers/Vector.h>
#include <Anvie/Test/UnitTest.h>

  /-- declared static to prevent name conflicts with tests in other units 
  |
  v
static Bool CreateDestroy() {
    AnvVector* vec = AnvVector_Create(sizeof(Uint64), NULL, NULL);
    
    // length is nonzero just after creation
    RETURN_VALUE_IF_FAIL(vec->length, NULL, "LENGTH OF VECTOR JUST AFTER CREATTION MUST BE NONZERO\n");
    
    // element size must be nonzero just after creation
    RETURN_VALUE_IF_FAIL(vec->element_size, NULL, "ELEMENT SIZE OF VECTOR JUST AFTER CREATTION MUST BE NONZERO\n");
    
    return True;
}

static Bool InsertDelete() {
    /* do your tests */
    return False;
}

// UNIT : AnvVector
BEGIN_TESTS(AnvVector) <-- note test name
    TEST(CreateDestroy),
    TEST(InsertDelete)
END_TESTS()
```

and then to run a unit test, you just need to import it in main test runner like this :

```c
#include <Anvie/Test/UnitTest.h>

IMPORT_UNIT_TEST(AnvVector)

BEGIN_UNIT_TESTS()
    UNIT_TEST(AnvVector) <-- note test name
END_UNIT_TESTS()
```

This will run the `AnvVector` unit test automatically! You just need to integrate this unit test file as an executable in your build system.

Here's the sample output for above test :
```shell
##################### BUILD PROJECT #######################
➜  Build make -j4
[ 20%] Building C object Source/Containers/CMakeFiles/anvutils_containers.dir/Vector.c.o
[ 60%] Building C object Source/Tests/CMakeFiles/anvutils_tests.dir/Vector.c.o
[ 60%] Building C object Source/Tests/CMakeFiles/anvutils_tests.dir/UnitTest.c.o
[ 80%] Linking C executable ../../bin/anvutils_tests
[ 80%] Built target anvutils_tests
[100%] Linking C static library ../../lib/libanvutils_containers.a
[100%] Built target anvutils_containers

####################### RUN TESTS #########################
➜  Build bin/anvutils_tests 
================================================================================
UNIT : "AnvVector"
================================================================================
TEST : "InsertDelete" : PASS
TEST : "CreateDestroy" : PASS
================================================================================
FAILED TESTS COUNT : "0"
================================================================================
```

Now, you can just run this test as a part of your build process or explicitly in your workflows file in order to inform you when a bad commit goes upstream. While working with Rizin, I realized how important tests are when you're having multiple contributions at the same time.

I'll give you a special example of a situation that I encountered while working, that forced me to write tests before working any further.
I was working on module that'll help Anvie parse and edit different binary file formats. Different structured file formats have different headers. I was working with [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) file's File Header. The elf file header looks something like this :

```c
typedef struct elf64_hdr {
  unsigned char    e_ident[EI_NIDENT];    /* ELF "magic number" */
  Elf64_Half e_type;
  Elf64_Half e_machine;
  Elf64_Word e_version;
  Elf64_Addr e_entry;        /* Entry point virtual address */
  Elf64_Off e_phoff;        /* Program header table file offset */
  Elf64_Off e_shoff;        /* Section header table file offset */
  Elf64_Word e_flags;
  Elf64_Half e_ehsize;
  Elf64_Half e_phentsize;
  Elf64_Half e_phnum;
  Elf64_Half e_shentsize;
  Elf64_Half e_shnum;
  Elf64_Half e_shstrndx;
} Elf64_Ehdr;
```

Now that `e_ident` part annoys me somethines. It also contains useful data but in an array format. I had to convert it to a struct with same size (`EI_NIDENT`). After editing the struct for both 32 and 64 bit header formats, I needed to be sure whether the size of header before and after edit are same. Now, when you're working with a library, you just can't directly run your program in a `main.c` somewhere in your project. This situation really demands for a test.

While this is just an example where you need tests, the problem was big (in quantity) because I removed `#define`s and added enums and enums also need to be of particular size in order to keep the header struct of specific size. So all for those structs, where enums were added, I needed to check size.

To be honest, I don't have much experience with how to write tests and this is a first baby step. It's also true that I don't have time to only learn just about tests and I also don't want to. I must learn as I work. That's how it's always been and it's been profitable for me and I hope it'll be that way in future.

All required defines are present in a single header file for now.

#ifndef ANVIE_UTILS_UNIT_TEST_H
```c
#define ANVIE_UTILS_UNIT_TEST_H

#include <Anvie/Types.h>
#include <Anvie/HelperDefines.h>
#include <string.h>

/**
 * All unit tests look like this
 * */
typedef Bool (*AnvUnitTestCallbackFn)();

/**
 * Test structure to contain test related data.
 * */
typedef struct anvie_unit_test_t {
    String s_name;
    AnvUnitTestCallbackFn pfn_test;
} AnvUnitTest;

// begin tests recording
#define BEGIN_TESTS(name)                              \
    Size unit_##name##_tests() {                       \
    static String unit_tests_names = #name;            \
    static const AnvUnitTest tests[] = {

// end tests recording
#define END_TESTS() \
    };                                                              \
    static Size unit_tests_count = sizeof(tests)/sizeof(tests[0]);  \
    LINE80();                                                       \
    println("UNIT : \"%s\"",unit_tests_names);                      \
    LINE80();                                                       \
                                                                    \
    Size iter = unit_tests_count;                                   \
    Size failed_count = 0;                                          \
    while(iter--) {                                                 \
        print("TEST : \"%s\" : ", tests[iter].s_name);              \
        if(!tests[iter].pfn_test()) {                               \
            println("FAIL");                                        \
            failed_count++;                                         \
        } else {                                                    \
            println("PASS");                                        \
        }                                                           \
    }                                                               \
    return failed_count;                                            \
    }

// record a test function
#define TEST(test) {.s_name = #test, .pfn_test = test}

#define BEGIN_UNIT_TESTS()                      \
    int main() {                                \
    Size failed = 0;

#define END_UNIT_TESTS()                        \
    }

#define UNIT_TEST(name)                                 \
    failed = unit_##name##_tests();                     \
    LINE80();                                           \
    println("FAILED TESTS COUNT : \"%zu\"", failed);    \
    LINE80();

define IMPORT_UNIT_TEST(name) Size unit_##name##_tests();

##endif // ANVIE_UTILS_UNIT_TEST_H
```
