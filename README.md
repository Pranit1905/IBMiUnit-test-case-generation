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

// Wrong: Invalid return in mainline
return;
```

### ✅ CORRECT COMPILABLE CODE:
```rpgle
// Correct: Use dealloc operation code
dealloc ptr;

// Correct: Proper pointer testing
// (Note: You cannot reliably test pointer deallocation in RPGLE)

// Correct: Programs end naturally in free-format
// No return needed in mainline
```

### 📖 LESSON LEARNED:
- **Use `dealloc` operation code**, not %dealloc() BIF
- **Pointer deallocation testing** is not reliable in RPGLE
- **Free-format programs** end naturally without return

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

---

## 📝 ADDITIONAL SESSION-LEVEL INSIGHTS

Based on the feedback provided, additional common mistakes include:

1. **Field Size Mismatches**: Declaring fields with inappropriate sizes for their usage context
2. **Template vs Instance Confusion**: Not understanding when to use templates vs direct instances
3. **BIF Parameter Confusion**: Using wrong parameter patterns for built-in functions
4. **Legacy vs Modern Syntax**: Mixing fixed-format concepts with free-format syntax
5. **Copy Member Dependencies**: Generating code that references non-existent copy members
6. **Test Case Structure**: Not understanding proper test framework initialization and teardown

---

## 🏁 CONCLUSION

This documentation serves as a comprehensive reference for avoiding common RPGLE code generation mistakes. Each pattern documented here represents actual compilation failures that have been corrected through iterative feedback and testing.

**Key Success Principle**: Always verify generated RPGLE code against IBM documentation and test with actual compilation before deployment.
