# IBMiUnit-test-case-generation
Test case generation using IBMi
# 🚨 RPGLE Code Generation Mistakes - Comprehensive Documentation

## 📋 Purpose
This document catalogs critical syntax errors and conceptual mistakes made during RPGLE code generation to prevent future compilation failures. Each mistake is documented with the incorrect code generated and the correct compilable alternative.

---

## ❌ MISTAKE #1: Data Structure Declaration Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Standalone field declared as data structure
dcl-s errorInfo qualified;

// Wrong: Invalid *LDA declaration
dcl-s systemDataArea char(100) dtaara('*LDA');
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use dcl-ds for data structure
dcl-ds errorInfo qualified;
    // fields here
end-ds;

// Correct: Valid *LDA declaration (if needed)
dcl-ds systemDataArea dtaara('*LDA');
    // structure definition
end-ds;
```

### 📖 LESSON LEARNED:
- **Use `dcl-ds`** for data structures, not `dcl-s`
- **Data area declarations** require proper syntax
- **Qualified structures** need proper field definitions

---

## ❌ MISTAKE #2: Variable Declaration Type Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: %rem variables declared as Packed instead of Int
dcl-s remainder packed(5:0);

// Wrong: Constants declared with packed variable
dcl-c MAX_SIZE packed(3:0) inz(100);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use int for %rem results
dcl-s remainder int(10);

// Correct: Constants use const keyword
dcl-c MAX_SIZE const(100);
```

### 📖 LESSON LEARNED:
- **%rem() BIF** returns integer values, not packed decimal
- **Constants** use `const()` syntax, not data type declarations
- **Match data types** to their intended usage

---

## ❌ MISTAKE #3: Invalid BIF Usage

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: %sorta doesn't exist as a BIF
%sorta(numbers);

// Wrong: %equal doesn't work for array comparison
if %equal(array1 : array2);

// Wrong: %occurs BIF doesn't exist
closeBraces = %occurs('}' : jsonData);

// Wrong: %checkr is not a valid BIF
pos = %checkr(' ' : testName);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: SORTA is an operation code
sorta numbers;

// Correct: Manual array comparison
isEqual = *on;
for i = 1 to 3;
  if array1(i) <> array2(i);
    isEqual = *off;
    leave;
  endif;
endfor;

// Correct: Use %scan for string searching
pos = %scan('}' : jsonData);

// Correct: Use %scan for string operations
pos = %scan(' ' : testName);
```

### 📖 LESSON LEARNED:
- **SORTA** is an operation code, not a BIF
- **No %equal() BIF** exists for arrays
- **%occurs() BIF doesn't exist** - use %occur() for multiple occurrence structures
- **Use %scan()** instead of non-existent %checkr()

---

## ❌ MISTAKE #4: Multidimensional Array Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Multidimensional array syntax
dcl-s matrix packed(5:2) dim(10) dim(10);
matrix(i:j) = value;

// Wrong: Complex BIF combinations
%sorta(%subarr(numbers : 2 : 3));
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Single dimension simulating matrix
dcl-s matrix packed(5:2) dim(100);
index = (i - 1) * 10 + j;
matrix(index) = value;

// Correct: Manual subarray operations
for i = 1 to 3;
  temp(i) = numbers(i + 1);
endfor;
sorta temp;
for i = 1 to 3;
  numbers(i + 1) = temp(i);
endfor;
```

### 📖 LESSON LEARNED:
- **RPGLE doesn't support** true multidimensional arrays
- **Use calculated indices** for matrix simulation
- **Complex BIF combinations** often don't work - use manual loops

---

## ❌ MISTAKE #5: Bit Manipulation Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: BITON/BITOFF don't exist in free-format
biton PATTERN_80 bitField;
bitoff PATTERN_01 bitField;

// Wrong: Hardcoded bit manipulation
bitField = %bitand(bitField : x'A4');
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use BIFs for bit manipulation
bitField = %bitor(bitField : x'80');      // Set bits
bitField = %bitand(bitField : %bitnot(x'01')); // Clear bits

// Correct: Use %bitnot for proper clearing
bitField = %bitand(bitField : %bitnot(PATTERN_01));
```

### 📖 LESSON LEARNED:
- **BITON/BITOFF operations** don't exist in free-format
- **Use BIF combinations**: %bitor(), %bitand(), %bitnot(), %bitxor()
- **Don't hardcode** bit patterns - use proper BIF functions

---

## ❌ MISTAKE #6: Control Flow and Syntax Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Ternary operator not supported
status = (balance > 1000 ? 'GOOD' : 'POOR');
effectiveRate = %parms() >= 2 ? taxRate : defaultRate;

// Wrong: Invalid %subst usage
result = %subst(*blanks : 1 : length);

// Wrong: %varchar as opcode
%varchar(field);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use if/else structure
if balance > 1000;
  status = 'GOOD';
else;
  status = 'POOR';
endif;

if %parms() >= 2;
  effectiveRate = taxRate;
else;
  effectiveRate = defaultRate;
endif;

// Correct: Use *blanks directly
result = *blanks;

// Correct: VARCHAR is handled automatically by IBM i
// No manual conversion needed
```

### 📖 LESSON LEARNED:
- **No ternary operator** in RPGLE - use if/else/endif
- **Use *blanks directly** for string initialization
- **VARCHAR conversion** is automatic in modern RPGLE

---

## ❌ MISTAKE #7: Memory Management and Pointer Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: %dealloc doesn't exist
%dealloc(ptr);

// Wrong: Invalid assertion syntax
assertNull(%addr(ptr : *data));
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use dealloc operation code
dealloc ptr;

// Correct: Proper pointer testing
// (Note: You cannot reliably test pointer deallocation in RPGLE)

```

### 📖 LESSON LEARNED:
- **Use `dealloc` operation code**, not %dealloc() BIF
- **Pointer deallocation testing** is not reliable in RPGLE

---

## ❌ MISTAKE #8: Display File and Field Length Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Display keyword with > 52 characters
dcl-s longVariable char(100);
// Then using in display file with DDS exceeding 52 chars
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Limit display fields to 52 characters max
dcl-s displayField char(52);
// Use appropriate field lengths for display files
```

### 📖 LESSON LEARNED:
- **Display keyword** cannot exceed 52 characters
- **Match field lengths** to display file limitations
- **Consider display constraints** when declaring variables

---

## ❌ MISTAKE #9: File Operations and Structure Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: IFS operations with multiple errors
// (Specific errors not detailed but code was fundamentally flawed)

// Wrong: Invalid copybook reference
/copy QCPYSRC,HTTPAPI_PR
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Proper IFS operations require careful syntax
// Need to understand file structure before generating code

// Correct: Valid copybook reference
/copy QCPYSRC,HTTPAPI_PR  // If copybook exists
// Or use appropriate copy member syntax
```

### 📖 LESSON LEARNED:
- **IFS file operations** require understanding of file structure
- **Copybook references** must be valid and exist
- **File operations** need careful error handling

---

## ❌ MISTAKE #10: Variable Scope and Declaration Placement

### 🔴 INCORRECT CODE GENERATED:
```rpgle
dcl-proc myProcedure;
  // Some executable code
  dcl-s result varchar(32000);  // Wrong: Declaration after code
  
  for i = 1 to 10;
    dcl-s loopVar int(10);      // Wrong: Declaration in loop
  endfor;
end-proc;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
dcl-proc myProcedure;
  // Correct: All declarations at beginning
  dcl-s result varchar(32000);
  dcl-s loopVar int(10);
  dcl-s i int(10);
  
  // Executable code follows
  for i = 1 to 10;
    // Use variables declared above
  endfor;
end-proc;
```

### 📖 LESSON LEARNED:
- **All variable declarations** must be at the beginning of procedures
- **No declarations** allowed after executable statements
- **Declare all variables** at procedure level, not in nested blocks

---

## ❌ MISTAKE #11: Data Structure Template vs Instance Confusion

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Using dcl-s for template-based arrays
dcl-s cache likeds(cacheEntry_t) dim(1000);

// Wrong: Complex BIF in initialization
dcl-s timestamp timestamp inz(%timestamp());
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use dcl-ds for template-based structures
dcl-ds cache likeds(cacheEntry_t) dim(100);

// Correct: Separate declaration and assignment
dcl-s timestamp timestamp;
timestamp = %timestamp();
```

### 📖 LESSON LEARNED:
- **Use `dcl-ds`** with likeds() for template-based arrays
- **Separate declaration and assignment** for complex BIFs
- **Field sizes** in templates must match procedure expectations

---

## ❌ MISTAKE #12: Multiple Occurrence Data Structure Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Two-parameter %occur syntax
%occur(MultiOccur : 5);

// Wrong: Qualified access to occurrence fields
MultiOccur.field1 = 'Data';

// Wrong: Mixed declaration syntax
dcl-ds MultiOccur qualified template occurs(10);
dcl-ds testMultiOcc likeds(MultiOccur);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Assignment to %occur BIF
%occur(MultiOccur) = 5;

// Correct: Direct field access after setting occurrence
field1 = 'Data';

// Correct: Simple occurs declaration
dcl-ds MultiOccur occurs(10);
    field1 char(10);
    field2 packed(5:0);
end-ds;
```

### 📖 LESSON LEARNED:
- **%occur() is assignable** - use assignment syntax
- **Access fields directly** after setting occurrence
- **No qualified access** needed for occurrence fields
- **Don't mix** qualified template with occurs

---

## ❌ MISTAKE #13: Test Framework Flow Control

### 🔴 INCORRECT CODE GENERATED:
```rpgle
IBMiUnit_setupSuite('Test Suite');
IBMiUnit_addTestCase(%pAddr(Test_Proc1): 'Test_Proc1');
IBMiUnit_teardownSuite();  // Wrong: Executes immediately

dcl-proc Test_Proc1;
    // test code
end-proc;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
IBMiUnit_setupSuite('Test Suite');
IBMiUnit_addTestCase(%pAddr(Test_Proc1): 'Test_Proc1');
return;  // Correct: Prevents premature teardown
IBMiUnit_teardownSuite();

dcl-proc Test_Proc1;
    // test code
end-proc;
```

### 📖 LESSON LEARNED:
- **Add `return;`** before teardown in test runners
- **Prevents mainline** from executing teardown prematurely
- **Proper flow control** is essential for test frameworks

---

## ❌ MISTAKE #14: String and Numeric Operation Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: MOD operator usage
result = value mod 10;

// Wrong: String concatenation with +
longString = 'First part' +
             'Second part';

// Wrong: Invalid assertion syntax
assertNotNull(variable);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use %rem() BIF for modulo
result = %rem(value : 10);

// Correct: Keep strings intact or use proper continuation
longString = 'First part Second part';

// Correct: Use %addr() for varchar variables
assertNotNull(%addr(variable));
```

### 📖 LESSON LEARNED:
- **Use %rem() BIF** instead of mod operator
- **Avoid splitting strings** with + operator
- **Use %addr()** for assertNotNull with varchar variables

---

## ❌ MISTAKE #15: PSDS and System Data Area Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: PSDS without template keyword
dcl-ds PSDS;

// Wrong: Invalid *LDA syntax
dcl-s systemDataArea char(100) dtaara('*LDA');
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: PSDS with template keyword
dcl-ds PSDS template;
    // PSDS fields
end-ds;

// Correct: Proper *LDA declaration
dcl-ds systemDataArea dtaara('*LDA');
    // LDA structure
end-ds;
```

### 📖 LESSON LEARNED:
- **PSDS requires** the template keyword
- **System data areas** need proper structure definitions
- **Don't mix** standalone variables with data area syntax

---

## ❌ MISTAKE #16: Procedure Interface and Prototype Mismatches

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Mismatched prototype and interface
dcl-pr myProcedure varchar(100);
  inputParam char(50) const;
end-pr;

dcl-proc myProcedure;
  dcl-pi *n char(100);  // Wrong: Different return type
    inputParam varchar(50) const;  // Wrong: Different parameter type
  end-pi;
end-proc;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Matching prototype and interface
dcl-pr myProcedure varchar(100);
  inputParam char(50) const;
end-pr;

dcl-proc myProcedure;
  dcl-pi *n varchar(100);
    inputParam char(50) const;
  end-pi;
end-proc;
```

### 📖 LESSON LEARNED:
- **Prototype and interface** must match exactly
- **Parameter types** must be identical
- **Return types** must be identical
- **Parameter names** can differ but types cannot

---

## ❌ MISTAKE #17: Incorrect Date/Time BIF Usage

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid date format string
currentDate = %char(%date() : '*YMD-');

// Wrong: Invalid %diff parameters
daysDiff = %diff(%date() : startDate);

// Wrong: Invalid timestamp initialization
dcl-s myTimestamp timestamp inz(%timestamp('2024-01-01-12.00.00'));
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Valid date format
currentDate = %char(%date() : '*YMD');

// Correct: %diff with time unit
daysDiff = %diff(%date() : startDate : *days);

// Correct: Proper timestamp format
dcl-s myTimestamp timestamp;
myTimestamp = %timestamp('2024-01-01-12.00.00.000000');
```

### 📖 LESSON LEARNED:
- **Date format strings** must match IBM i standards
- **%diff() requires** three parameters including time unit
- **Timestamp format** must include microseconds
- **Separate declaration** from complex BIF initialization

---

## ❌ MISTAKE #18: Incorrect %SUBARR and Array BIF Usage

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: %subarr with assignment
%subarr(targetArray : 1 : 5) = %subarr(sourceArray : 1 : 5);

// Wrong: %lookup with wrong parameters
pos = %lookup('VALUE' : myArray : 1 : %elem(myArray));

// Wrong: %tlookup doesn't exist
pos = %tlookup('VALUE' : myArray);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Manual array copying
for i = 1 to 5;
  targetArray(i) = sourceArray(i);
endfor;

// Correct: %lookup without range parameters
pos = %lookup('VALUE' : myArray);

// Correct: Use %lookup for table lookup
pos = %lookup('VALUE' : myArray);
```

### 📖 LESSON LEARNED:
- **%subarr cannot be assigned** - use manual loops
- **%lookup parameters** are element and array only
- **No %tlookup BIF** exists - use %lookup
- **Range parameters** not supported in %lookup

---

## ❌ MISTAKE #19: File I/O Operation Code Confusion

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Using BIF syntax for file operations
%read(myFile);
%write(myFile);
%chain(key : myFile);

// Wrong: Mixed file operation syntax
read myFile %eof();
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use operation codes for file I/O
read myFile;
write myFile;
chain key myFile;

// Correct: Check %eof after operation
read myFile;
if %eof(myFile);
  // Handle end of file
endif;
```

### 📖 LESSON LEARNED:
- **File I/O uses operation codes**, not BIFs
- **%eof() is a BIF** used after file operations
- **Chain operation** uses key then file syntax
- **Separate file operations** from condition checking

---

## ❌ MISTAKE #20: Invalid SQL Embedded Syntax

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: SQL variable without colon
exec sql
  select count(*) into recordCount
  from myTable;

// Wrong: Invalid SQL continuation
exec sql select name,
         address
  into :empName, :empAddr
  from employee;

// Wrong: Missing null indicators
exec sql
  select name into :empName
  from employee
  where id = :empId;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: SQL variable with colon prefix
exec sql
  select count(*) into :recordCount
  from myTable;

// Correct: Proper SQL statement formatting
exec sql
  select name, address
  into :empName, :empAddr
  from employee;

// Correct: Include null indicators
dcl-s empNameInd int(5);
exec sql
  select name into :empName :empNameInd
  from employee
  where id = :empId;
```

### 📖 LESSON LEARNED:
- **Host variables** must have colon prefix in SQL
- **SQL statements** should be properly formatted
- **Always include null indicators** for nullable fields
- **Check SQLSTATE** after SQL operations

---

## ❌ MISTAKE #21: Monitor Block and Error Handling Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Missing endmon
monitor;
  riskyOperation();
on-error;
  // Handle error

// Wrong: Invalid error handling syntax
try;
  riskyOperation();
catch;
  // Handle error
endtry;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Complete monitor block
monitor;
  riskyOperation();
on-error;
  // Handle error
endmon;

// Correct: RPGLE uses monitor/on-error, not try/catch
monitor;
  riskyOperation();
on-error;
  // Handle error
endmon;
```

### 📖 LESSON LEARNED:
- **Monitor blocks** must have endmon
- **RPGLE uses monitor/on-error**, not try/catch
- **Always close** monitor blocks properly
- **Error handling** is not optional for risky operations

---

## ❌ MISTAKE #22: Incorrect Global Variable and Copy Member Usage

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Global variable without proper declaration
//copy QCPYSRC,GLOBALS
globalVar = 'Test';  // Used without proper declaration in copy member

// Wrong: Copy member with executable code
/copy MYLIB/QCPYSRC,BADCOPY
// Copy member contains executable statements
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Proper copy member usage
/copy QCPYSRC,GLOBALS
// Ensure copy member only contains declarations

// Correct: Global variable properly declared in copy member
// Copy member should only contain:
// dcl-s globalVar varchar(100) export;
globalVar = 'Test';
```

### 📖 LESSON LEARNED:
- **Copy members** should contain only declarations
- **Global variables** need export keyword in copy members
- **No executable code** in copy members
- **Validate copy member** contents before inclusion

---

## ❌ MISTAKE #23: Incorrect Use of Named Constants and Figurative Constants

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Named constant with variable syntax
dcl-s MAX_RECORDS const int(10) inz(1000);

// Wrong: Invalid figurative constant usage
field = *null;  // For non-pointer fields
field = *blanks(10);  // Invalid parameter

// Wrong: Incorrect *HIVAL/*LOVAL usage
if date > *hival;  // Invalid comparison
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Named constant syntax
dcl-c MAX_RECORDS const(1000);

// Correct: Proper figurative constant usage
field = *blanks;  // No parameters needed
ptr = *null;      // Only for pointer fields

// Correct: Proper *HIVAL/*LOVAL usage
if date = *hival;  // Used for initialization/comparison
```

### 📖 LESSON LEARNED:
- **Named constants** use dcl-c with const() syntax
- **Figurative constants** don't take parameters
- ***null** only for pointer fields
- ***hival/*loval** for boundary conditions

---

## ❌ MISTAKE #24: Template and Based Variable Confusion

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Template used as regular variable
dcl-ds myTemplate template;
  field1 char(10);
end-ds;

myTemplate.field1 = 'Test';  // Cannot use template directly

// Wrong: Based variable without pointer
dcl-s basedVar char(100) based;
basedVar = 'Test';  // No pointer specified
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Template with instance
dcl-ds myTemplate template;
  field1 char(10);
end-ds;

dcl-ds myInstance likeds(myTemplate);
myInstance.field1 = 'Test';

// Correct: Based variable with pointer
dcl-s myPtr pointer;
dcl-s basedVar char(100) based(myPtr);
myPtr = %addr(someVariable);
basedVar = 'Test';
```

### 📖 LESSON LEARNED:
- **Templates** cannot be used directly - need instances
- **Based variables** require a pointer specification
- **Use likeds()** to create instances from templates
- **Initialize pointers** before using based variables

---

## ❌ MISTAKE #25: Incorrect Loop Control and Iterator Usage

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: FOR loop with string iterator
for name = 'A' to 'Z';
  // Process names
endfor;

// Wrong: Invalid DOW condition
dow %found() and not %eof();  // %found() without context
  // Process records
enddo;

// Wrong: ITER/LEAVE outside loop
if condition;
  iter;  // Not inside a loop
endif;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: FOR loop with numeric iterator
for i = 1 to 26;
  name = %char(%int('A') + i - 1);
  // Process names
endfor;

// Correct: DOW with proper context
read myFile;
dow not %eof(myFile);
  // Process records
  read myFile;
enddo;

// Correct: ITER/LEAVE inside loop
for i = 1 to 100;
  if condition;
    iter;  // Continue to next iteration
  endif;
  if otherCondition;
    leave;  // Exit loop
  endif;
endfor;
```

### 📖 LESSON LEARNED:
- **FOR loops** require numeric iterators
- **%found()** needs context (which file/operation)
- **ITER/LEAVE** only valid inside loops
- **DOW conditions** should be clear and testable

---



## 🎯 CRITICAL SYNTAX RULES FOR FUTURE REFERENCE

### ✅ VARIABLE DECLARATIONS:
```rpgle
// All declarations at beginning of procedure
dcl-proc myProcedure;
  dcl-s variable1 char(10);
  dcl-s variable2 int(10);
  dcl-ds structure1 qualified;
    field1 char(5);
  end-ds;
  
  // Executable code after all declarations
end-proc;
```

### ✅ BIF USAGE PATTERNS:
```rpgle
// Correct BIF usage
result = %scan('text' : string);
result = %rem(number : divisor);
sorta array;  // Operation code, not BIF
%occur(dataStructure) = occurrence;  // Assignment to BIF
```

### ✅ CONTROL FLOW:
```rpgle
// No ternary operator - use if/else
if condition;
  variable = value1;
else;
  variable = value2;
endif;
```

### ✅ MEMORY AND POINTERS:
```rpgle
// Use operation codes for memory management
alloc size ptr;
dealloc ptr;  // Not %dealloc()
```

---

## 🚨 COMPILATION ERROR PREVENTION CHECKLIST

Before generating RPGLE code, verify:

- [ ] **All variables declared** at beginning of procedures
- [ ] **Use correct BIF syntax** - check IBM documentation
- [ ] **No ternary operators** - use if/else/endif
- [ ] **No multidimensional arrays** - use calculated indices
- [ ] **Proper data structure types** - dcl-ds vs dcl-s
- [ ] **Correct memory management** - dealloc vs %dealloc()
- [ ] **Valid BIF names** - %scan not %checkr, etc.
- [ ] **Proper occurrence handling** - assignment not parameters
- [ ] **Field length constraints** - display files, etc.
- [ ] **Test framework flow** - return before teardown
- [ ] **Prototype/interface matching** - exact parameter/return types
- [ ] **Date/time BIF parameters** - include required time units
- [ ] **File I/O operation codes** - not BIF syntax
- [ ] **SQL host variables** - colon prefix and null indicators
- [ ] **Monitor blocks** - complete with endmon
- [ ] **Copy members** - declarations only, no executable code
- [ ] **Template vs instance** - don't use templates directly
- [ ] **Loop iterators** - numeric only for FOR loops

---

## 📚 SUCCESS PATTERNS

### ✅ DATA STRUCTURE DECLARATIONS:
```rpgle
// Template definition
dcl-ds template_t template;
    field1 char(10);
    field2 packed(5:0);
end-ds;

// Instance with template
dcl-ds instance likeds(template_t);

// Multiple occurrence
dcl-ds occurs_ds occurs(10);
    field1 char(10);
end-ds;
```

### ✅ ERROR HANDLING:
```rpgle
// Always include proper error handling
monitor;
    // Risky operation
on-error;
    // Handle error
endmon;
```

### ✅ STRING OPERATIONS:
```rpgle
// Use appropriate BIFs
pos = %scan(searchString : targetString);
len = %len(%trim(string));
upper = %upper(string);
```

### ✅ PROCEDURE DEFINITIONS:
```rpgle
// Matching prototype and interface
dcl-pr myProcedure varchar(100);
  param1 char(50) const;
  param2 int(10) value;
end-pr;

dcl-proc myProcedure;
  dcl-pi *n varchar(100);
    param1 char(50) const;
    param2 int(10) value;
  end-pi;
  
  // All variable declarations here
  dcl-s result varchar(100);
  
  // Executable code here
  return result;
end-proc;
```

---
# Additional RPGLE Code Generation Errors - Documentation

## ❌ MISTAKE #26: Parameter Passing by Reference Validation

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Parameter not valid for reference passing
dcl-pr myProcedure;
  invalidParam char(10) const;  // Cannot pass const by reference
end-pr;

// Call with incompatible parameter
myProcedure(someVariable);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use VALUE for const parameters or remove const for reference
dcl-pr myProcedure;
  validParam char(10) value;  // Pass by value
end-pr;

// Or for reference passing
dcl-pr myProcedure;
  validParam char(10);  // Pass by reference (no const)
end-pr;
```

### 📖 LESSON LEARNED:
- **CONST parameters** cannot be passed by reference
- **Use VALUE keyword** for const parameters
- **Reference parameters** cannot have const keyword

---

## ❌ MISTAKE #27: SQL SET OPTION Usage in Procedures

### 🔴 INCORRECT CODE GENERATED:
```rpgle
dcl-proc Test_SQLOption_CloseCursor;
  // Wrong: SET OPTION in procedure
  exec sql set option closqlcsr=*endmod;
  assertNumericEquals(0 : sqlcode);
end-proc;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: SET OPTION at program level, not in procedures
// Remove the entire test case as SET OPTION is not allowed in procedures
// Handle CLOSQLCSR at compilation level instead
```

### 📖 LESSON LEARNED:
- **SET OPTION statements** not allowed within procedures
- **SQL options** should be handled at program compilation level
- **Remove problematic SQL option tests** entirely

---

## ❌ MISTAKE #28: SQL Host Structure Array Issues

### 🔴 INCORRECT CODE GENERATED:
```rpgle
dcl-proc Test_SQLCursor_FetchMultipleRows;
  dcl-ds fetchArray likeds(myFileTemplate) dim(100);
  exec sql fetch testCursor3 for 100 rows into :fetchArray;
end-proc;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
dcl-proc Test_SQLCursor_FetchMultipleRows;
  dcl-ds fetchArray likeds(myFileTemplate) dim(100) template;
  dcl-ds workArray likeds(fetchArray) dim(100);
  dcl-s tempField1 char(10);
  dcl-s tempField2 packed(7:2);
  
  exec sql declare testCursor3 cursor for select field1, field2 from myfile;
  exec sql open testCursor3;
  
  // Fetch one row at a time
  for i = 1 to %elem(workArray);
    exec sql fetch testCursor3 into :tempField1, :tempField2;
    if sqlcode <> 0;
      leave;
    endif;
    workArray(i).field1 = tempField1;
    workArray(i).field2 = tempField2;
  endfor;
  
  exec sql close testCursor3;
end-proc;
```

### 📖 LESSON LEARNED:
- **SQL host structure arrays** have specific usability requirements
- **Fetch multiple rows** often requires individual field handling
- **Use template and work arrays** for complex SQL operations

---

## ❌ MISTAKE #29: SQL GET DIAGNOSTICS Syntax

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid GET DIAGNOSTICS syntax
exec sql get diagnostics :sqlState = RETURNED_SQLSTATE;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use built-in SQLSTATE field
sqlState = SQLSTATE;
```

### 📖 LESSON LEARNED:
- **SQLSTATE is built-in** SQL communication area field
- **No GET DIAGNOSTICS needed** for basic SQL state
- **Use direct assignment** from SQLSTATE variable

---

## ❌ MISTAKE #30: SQL FETCH Syntax Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid FETCH syntax
exec sql fetch testCursor3 for 10 rows into :fetchArray;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Proper FETCH syntax
exec sql fetch from testCursor3 for 10 rows into :fetchArray;
```

### 📖 LESSON LEARNED:
- **FETCH requires FROM keyword** when using cursor names
- **Multi-row FETCH** needs proper syntax structure
- **Verify SQL syntax** against IBM i SQL standards

---

## ❌ MISTAKE #31: Invalid %move Operation Code

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: %move doesn't exist as BIF
%move(TEST_DATE : dateds);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use MOVE operation code or assignment
move TEST_DATE dateds;
// Or use direct assignment
dateds = TEST_DATE;
```

### 📖 LESSON LEARNED:
- **%move() BIF doesn't exist**
- **Use MOVE operation code** for legacy behavior
- **Use direct assignment** in free-format RPG

---

## ❌ MISTAKE #32: Wrong Assertion Types for Numeric Results

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Using char assertion for numeric result
pos = %scan('text' : string);
assertCharNotEquals('0' : pos);  // %scan returns numeric
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use numeric assertion for %scan
pos = %scan('text' : string);
assertNumericNotEquals(0 : pos);  // %scan returns 0 when not found
```

### 📖 LESSON LEARNED:
- **%scan() returns numeric** position or 0
- **Use assertNumericNotEquals** for numeric comparisons
- **%scan returns 0** when string not found (not when found)

---

## ❌ MISTAKE #33: PSDS Template and Qualified Conflict

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: PSDS with template and qualified together
dcl-ds sysds psds qualified template;
  procName char(10) pos(1);
  sysDate char(8) pos(191);
end-ds;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: PSDS with qualified only
dcl-ds sysds psds qualified;
  procName char(10) pos(1);
  sysDate char(8) pos(191);
end-ds;

// Or template only (separate from PSDS)
dcl-ds psdsTemplate qualified template;
  procName char(10) pos(1);
  sysDate char(8) pos(191);
end-ds;
```

### 📖 LESSON LEARNED:
- **TEMPLATE and QUALIFIED** cannot be used together
- **PSDS with QUALIFIED** is valid
- **Separate template definitions** from PSDS declarations

---

## ❌ MISTAKE #34: Date Format Keywords in Declarations

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: datfmt keyword in dcl-s
dcl-s eurDate date datfmt(*eur);
dcl-s isoDate date datfmt(*iso);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Standard date declarations
dcl-s eurDate date;
dcl-s isoDate date;

// Use format conversion in output
eurDateStr = %char(eurDate : *eur);
isoDateStr = %char(isoDate : *iso);
```

### 📖 LESSON LEARNED:
- **Remove datfmt keyword** from declarations
- **Use %char() with format** for output conversion
- **RPG handles internal date storage** uniformly

---

## ❌ MISTAKE #35: Invalid System Date Initialization

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: *sys not valid in free-format
dcl-s currentDate date inz(*sys);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use %date() BIF
dcl-s currentDate date;
currentDate = %date();
```

### 📖 LESSON LEARNED:
- **Cannot use *sys directly** in free-format
- **Use %date() BIF** for current system date
- **Separate declaration from assignment** for BIFs

---

## ❌ MISTAKE #36: Invalid Integer Length Specifications

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid integer lengths
dcl-s testVar int(15);
dcl-s testVar int(7);
dcl-s testVar int(8);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Valid integer lengths only
dcl-s testVar int(10);  // 15 -> 10
dcl-s testVar int(10);  // 7 -> 10
dcl-s testVar int(10);  // 8 -> 10
```

### 📖 LESSON LEARNED:
- **Integer lengths** can only be 3, 5, 10, or 20 digits
- **Most common choice** is int(10) for general use
- **Invalid lengths** cause compilation errors

---

## ❌ MISTAKE #37: Export Keyword in Prototypes

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Export in prototype
dcl-pr square int(10) export;
  pnum int(5) value;
end-pr;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Export only in procedure implementation
dcl-pr square int(10);
  pnum int(5) value;
end-pr;

dcl-proc square export;
  dcl-pi *n int(10);
    pnum int(5) value;
  end-pi;
  // procedure body
end-proc;
```

### 📖 LESSON LEARNED:
- **Export keyword** only in procedure implementation
- **Prototypes don't use export** keyword
- **Implementation includes export** if needed

---

## ❌ MISTAKE #38: Invalid Unsigned Integer Syntax

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid unsigned integer syntax
dcl-s testVar unsigned int(8);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use standard integer syntax
dcl-s testVar int(10);
```

### 📖 LESSON LEARNED:
- **No unsigned int syntax** in free-format RPG
- **Use standard int()** declarations
- **RPG handles unsigned operations** through BIFs

---

## ❌ MISTAKE #39: Invalid String Operations

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid string concatenation and operations
longString = 'First part' + 'Second part';
testStr = 'TEST123' + *blanks;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Proper string handling
longString = 'First part Second part';
testStr = 'TEST123' + %trimr(*blanks);
```

### 📖 LESSON LEARNED:
- **No + operator** for string concatenation in RPG
- **Use %trimr()** for proper blank handling
- **Keep strings intact** or use proper string BIFs

---

## ❌ MISTAKE #40: Data Area Declaration Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Standalone variable for data area
dcl-s testDataArea char(100) dtaara('*LDA');

// Wrong: PSDS with template keyword
dcl-ds testProgramStatus psds qualified template;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Data structure for data area
dcl-ds testDataArea dtaara(*lda) qualified;
end-ds;

// Correct: PSDS without template, or template without PSDS
dcl-ds programStatusTemplate qualified template;
  // fields here
end-ds;
```

### 📖 LESSON LEARNED:
- **Data areas** require data structure declarations
- **Use dcl-ds** not dcl-s for data areas
- **PSDS and template** cannot be used together

---

## ❌ MISTAKE #41: Incorrect Operation Code Syntax

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Incorrect SCAN and CHECK syntax
scan 'brown' testSearchField testPosition;
check 'aeiou' testSearchField testPosition;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use BIF syntax in free-format
testPosition = %scan('brown' : testSearchField);
testPosition = %check('aeiou' : testSearchField);
```

### 📖 LESSON LEARNED:
- **Use BIFs** instead of operation codes in free-format
- **%scan() and %check()** are the correct BIF forms
- **Operation code syntax** not valid in free-format

---

## ❌ MISTAKE #42: Multidimensional Array Simulation

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Multidimensional array syntax
dcl-s matrix packed(5:2) dim(MATRIX_SIZE:MATRIX_SIZE);
matrix(i:j) = value;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Single dimension with calculated index
dcl-s matrix packed(5:2) dim(100);  // 10x10 = 100 elements
index = (i - 1) * MATRIX_SIZE + j;
matrix(index) = value;
```

### 📖 LESSON LEARNED:
- **No multidimensional arrays** in RPG
- **Use calculated indices** for matrix operations
- **Formula: (row-1) * width + column** for 2D simulation

---

## ❌ MISTAKE #43: Invalid VARCHAR Operation

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: %varchar as operation code
%varchar(field);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: VARCHAR conversion is automatic
// No manual conversion needed in modern RPG
field = someValue;  // Automatic conversion
```

### 📖 LESSON LEARNED:
- **No %varchar() BIF** exists
- **VARCHAR conversion** is automatic in modern RPG
- **Remove manual conversion** attempts

---

## ❌ MISTAKE #44: Multiple Occurrence Issues

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: likeds with occurs not inherited
dcl-ds MultiOccur occurs(10) template;
  field1 char(10);
end-ds;

dcl-ds multiRec likeds(MultiOccur);  // Missing occurs
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Declare occurs explicitly
dcl-ds MultiOccur occurs(10) template;
  field1 char(10);
end-ds;

dcl-ds multiRec likeds(MultiOccur) occurs(10);
```

### 📖 LESSON LEARNED:
- **Occurs not inherited** from template
- **Must declare occurs** explicitly on likeds
- **Template occurs** doesn't carry over to instances

---

## ❌ MISTAKE #45: Date/Time BIF Initialization Errors

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: BIF in initialization
dcl-s baseDate date inz(%date('2024-01-01'));
dcl-s testTime time inz(%time('12:30:45'));
dcl-s yearStr char(4) inz(%char(%subdt(testDate : *years) : 4));
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use date/time literals
dcl-s baseDate date;
dcl-s testTime time;
dcl-s yearStr char(4);

// Initialize in procedure body
baseDate = d'2024-01-01';
testTime = t'12.30.45';  // Use dots, not colons
yearStr = %char(%subdt(testDate : *years));  // No second parameter
```

### 📖 LESSON LEARNED:
- **Cannot use BIFs** in inz() initialization
- **Use date/time literals**: d'YYYY-MM-DD', t'HH.MM.SS'
- **%char() takes one parameter** only
- **Time literals use dots** not colons

---

## ❌ MISTAKE #46: Invalid Packed Decimal Precision

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid packed decimal precision
dcl-s taxRate packed(3:4) const options(*nopass);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Sufficient precision for decimal places
dcl-s taxRate packed(7:4) const options(*nopass);
```

### 📖 LESSON LEARNED:
- **Packed decimal precision** must be sufficient for decimal places
- **Total digits** must be greater than decimal places
- **Use packed(7:4)** for 4 decimal places

---

## ❌ MISTAKE #47: Invalid SORTA with %SUBARR

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid SORTA with %SUBARR combination
%sorta(testSalaries : %subarr(testSalaries : 1 : testEmployeeCount));
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use SORTA with %SUBARR properly
sorta %subarr(testSalaries : 1 : testEmployeeCount);
```

### 📖 LESSON LEARNED:
- **SORTA is operation code** not BIF
- **Use SORTA with %SUBARR** directly
- **No second parameter** for SORTA

---

## ❌ MISTAKE #48: Template Name Conflicts

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Template name same as instance name
dcl-ds myTemplate template;
  field1 char(10);
end-ds;

dcl-ds myTemplate likeds(myTemplate);  // Name conflict
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Different names for template and instance
dcl-ds myTemplate_t template;
  field1 char(10);
end-ds;

dcl-ds myInstance likeds(myTemplate_t);
```

### 📖 LESSON LEARNED:
- **Template and instance** cannot have same name
- **Use naming convention** like _t for templates
- **Avoid name conflicts** between template and instance

---

## ❌ MISTAKE #49: Invalid File Operation Syntax

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Mixed file operation syntax
open(e) myFile;
close(e) myFile;
read(e) myFile;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Consistent file operation syntax
open myFile;
close myFile;
read myFile;

// Or with error handling
open(e) myFile;
if not %error();
  // File opened successfully
endif;
```

### 📖 LESSON LEARNED:
- **Consistent syntax** for file operations
- **Error handling** with (e) extender
- **Check %error()** after operations with (e)

---

## ❌ MISTAKE #50: Invalid Parameter Omission

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid parameter omission
myProcedure(param1 : : param3);  // Missing second parameter
myProcedure(param1 : ;);  // Invalid syntax
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use %omit() for optional parameters
myProcedure(param1 : %omit() : param3);
```

### 📖 LESSON LEARNED:
- **Use %omit()** for omitting parameters
- **Cannot leave parameters blank** with just colons
- **Optional parameters** need proper omission syntax

---

## ❌ MISTAKE #51: Overlay Keyword in Free-Format

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: OVERLAY not supported in **FREE
dcl-proc Test_Overlay_Field1;
  dcl-s masterField char(50);
  dcl-s field1 char(10) overlay(masterField : 1);  // Invalid in **FREE
end-proc;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use data structure for overlay functionality
dcl-ds masterField;
  field1 char(10);
  field2 char(15);
  field3 char(25);
end-ds;

dcl-proc Test_Overlay_Field1;
  masterField = TEST_OVERLAY_DATA;
  assertCharEquals(EXPECTED_FIELD1 : field1);
end-proc;
```

### 📖 LESSON LEARNED:
- **OVERLAY keyword** not supported in **FREE format
- **Use data structure subfields** for overlay functionality
- **Global data structure** provides overlay-like behavior

---

## ❌ MISTAKE #52: Invalid %occur BIF Syntax

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Invalid %occur syntax
%occur(legacyDS : occurrence) = 1;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Proper %occur assignment
%occur(legacyDS) = 1;
```

### 📖 LESSON LEARNED:
- **%occur() takes one parameter** only
- **Assignment to %occur()** sets occurrence number
- **No second parameter** for occurrence value

---

## ❌ MISTAKE #53: Based Array Element Size

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Element size too small for operations
dcl-s dynamicArray char(1) dim(32767) based(memoryPtr);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Appropriate element size
dcl-s dynamicArray char(10) dim(32767) based(memoryPtr);
```

### 📖 LESSON LEARNED:
- **Array element size** must accommodate data being stored
- **Match element size** to operation requirements
- **Consider data length** when declaring based arrays

---

## ❌ MISTAKE #54: Cache Array Declaration Type

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: dcl-s for data structure array
dcl-s cache likeds(cacheEntry_t) dim(1000);
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: dcl-ds for data structure array
dcl-ds cache likeds(cacheEntry_t) dim(100);
```

### 📖 LESSON LEARNED:
- **Data structure arrays** require dcl-ds
- **Never use dcl-s** for structure arrays
- **dcl-s is for scalar** variables only

---

## ❌ MISTAKE #55: Variable Declaration in Loops

### 🔴 INCORRECT CODE GENERATED:
```rpgle
// Wrong: Variable declaration inside loop
for i = 1 to count;
  dcl-s len int(10) = %len(%trim(strings(i)));
  if len > 0;
    // logic here
  endif;
endfor;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: All declarations at procedure level
dcl-s len int(10);

for i = 1 to count;
  len = %len(%trim(strings(i)));
  if len > 0;
    // logic here
  endif;
endfor;
```

### 📖 LESSON LEARNED:
- **No variable declarations** inside loops
- **All declarations** at procedure beginning
- **Separate declaration** from assignment

---

## 🎯 ADDITIONAL CRITICAL SYNTAX RULES

### ✅ PARAMETER PASSING:
```rpgle
// Correct parameter specifications
dcl-pr myProc varchar(100);
  param1 char(50) const value;    // const requires value
  param2 char(50);                // reference passing
  param3 char(50) options(*nopass); // optional parameter
end-pr;
```

### ✅ SQL OPERATIONS:
```rpgle
// Correct SQL syntax
exec sql
  select field1, field2
  into :hostVar1, :hostVar2
  from myTable
  where key = :keyValue;

// Use SQLSTATE for error checking
if SQLSTATE <> '00000';
  // Handle SQL error
endif;
```

### ✅ DATE/TIME OPERATIONS:
```rpgle
// Correct date/time handling
dcl-s myDate date;
dcl-s myTime time;

myDate = d'2024-01-01';
myTime = t'12.30.45';

// Correct difference calculation
daysDiff = %diff(endDate : startDate : *days);
```

### ✅ ARRAY OPERATIONS:
```rpgle
// Correct array declarations and operations
dcl-ds myArray likeds(template_t) dim(100);
dcl-s simpleArray char(10) dim(50);

// Correct sorting
sorta simpleArray;
sorta %subarr(simpleArray : 1 : actualElements);
```
### ✅ DATA STRUCTURE DECLARATIONS:
```rpgle
// Template definition
dcl-ds template_t template;
    field1 char(10);
    field2 packed(5:0);
end-ds;

// Instance with template
dcl-ds instance likeds(template_t);

// Multiple occurrence
dcl-ds occurs_ds occurs(10);
    field1 char(10);
end-ds;
```

### ✅ ERROR HANDLING:
```rpgle
// Always include proper error handling
monitor;
    // Risky operation
on-error;
    // Handle error
endmon;
```

### ✅ STRING OPERATIONS:
```rpgle
// Use appropriate BIFs
pos = %scan(searchString : targetString);
len = %len(%trim(string));
upper = %upper(string);
```

### ✅ PROCEDURE DEFINITIONS:
```rpgle
// Matching prototype and interface
dcl-pr myProcedure varchar(100);
  param1 char(50) const;
  param2 int(10) value;
end-pr;

dcl-proc myProcedure;
  dcl-pi *n varchar(100);
    param1 char(50) const;
    param2 int(10) value;
  end-pi;
  
  // All variable declarations here
  dcl-s result varchar(100);
  
  // Executable code here
  return result;
end-proc;
```
---

## 🚨 UPDATED COMPILATION ERROR PREVENTION CHECKLIST

Additional items to verify:

- [ ] **Parameter passing** - const requires value keyword
- [ ] **SQL operations** - proper host variable syntax with colons
- [ ] **Date/time literals** - use d'YYYY-MM-DD' and t'HH.MM.SS'
- [ ] **BIF initialization** - cannot use BIFs in inz() clause
- [ ] **Integer lengths** - only 3, 5, 10, 20 allowed
- [ ] **Export keyword** - only in procedure implementation
- [ ] **Data area declarations** - use dcl-ds not dcl-s
- [ ] **Template vs instance** - cannot have same names
- [ ] **File operations** - consistent syntax and error handling
- [ ] **Parameter omission** - use %omit() not blank parameters
- [ ] **Overlay functionality** - use data structures in **FREE
- [ ] **Variable scope** - no declarations inside loops or blocks
- [ ] **Array element sizes** - match to data requirements
- [ ] **SQL error handling** - use SQLSTATE for checking

This completes the documentation of the additional errors found during your RPGLE test generation process.


## 📝 ADDITIONAL SESSION-LEVEL INSIGHTS

Based on the feedback provided, additional common mistakes include:

1. **Field Size Mismatches**: Declaring fields with inappropriate sizes for their usage context
2. **Template vs Instance Confusion**: Not understanding when to use templates vs direct instances
3. **BIF Parameter Confusion**: Using wrong parameter patterns for built-in functions
4. **Legacy vs Modern Syntax**: Mixing fixed-format concepts with free-format syntax
5. **Copy Member Dependencies**: Generating code that references non-existent copy members
6. **Test Case Structure**: Not understanding proper test framework initialization and teardown
7. **Procedure Interface Mismatches**: Prototype and interface parameters not matching exactly
8. **Date/Time Operation Errors**: Missing required parameters in date/time
