# Understand Javascript's Floating-Point Numbers

Most programming languages have several types of numeric data, but JavaScript gets away with just one. You can see this reflected in the behavior of the typeof operator, which classifies integers and floating-point numbers alike simply as numbers:

    typeof 17; // "number"
    typeof 98.6; // "number"
    typeof -2.1; // "number"

In fact, all numbers in JavaScript are double-precision floating-point numbers, that is, the 64-bit encoding of numbers specified by the IEEE 754 standard—commonly known as “doubles.” If this fact leaves you wondering what happened to the integers, keep in mind that doubles can represent integers perfectly with up to 53 bits of
precision. All of the integers from `–9,007,199,254,740,992 (–2^53)` to
`9,007,199,254,740,992 (2^53)` are valid doubles. So it’s perfectly possible to do integer arithmetic in JavaScript, despite the lack of a distinct integer type.

Most arithmetic operators work with integers, real numbers, or a combination of the two:
    
    0.1 * 1.9 // 0.19
    -99 + 100; // 1
    21 - 12.3; // 8.7
    2.5 / 5; // 0.5
    21 % 8; // 5
    
The bitwise arithmetic operators, however, are special. Rather than operating on their arguments directly as floating-point numbers, they implicitly convert them to 32-bit integers. (To be precise, they are treated as 32-bit, big-endian, two’s complement integers.) For example, take the bitwise OR expression:

    8 | 1; // 9
    
This simple-looking expression actually requires several steps to evaluate. As always, the JavaScript numbers 8 and 1 are doubles. But they can also be represented as 32-bit integers, that is, sequences of thirty-two 1’s and 0’s. As a 32-bit integer, the number 8 looks like this:

    00000000000000000000000000001000
    
You can see this for yourself by using the toString method of numbers:   

    (8).toString(2); // "1000"
    
The argument to toString specifies the radix, in this case indicating a base 2 (i.e., binary) representation. The result drops the extra 0 bits on the left since they don’t affect the value.

The integer 1 is represented in 32 bits as:
    
    00000000000000000000000000000001
    
The bitwise `OR` expression combines the two bit sequences by keeping any 1 bits found in either input, resulting in the bit pattern:

    00000000000000000000000000001001
    
This sequence represents the integer 9. You can verify this by usingthe standard library function parseInt, again with a radix of 2:

    parseInt("1001", 2); // 9       
    
(The leading 0 bits are unnecessary since, again, they don’t affect the result.)

All of the bitwise operators work the same way, converting their inputs to integers and performing their operations on the integer bit patterns before converting the results back to standard Java-Script floating-point numbers. In general, these conversions require extra work in Java Script engines: Since numbers are stored as floating-point, they have to be converted to integers and then back to floating-point again. However, optimizing compilers can sometimes infer when arithmetic expressions and even variables work exclusively with integers, and avoid the extra conversions by storing the data internally as integers.

A final note of caution about floating-point numbers: If they don’t make you at least a little nervous, they probably should. Floating-point numbers look deceptively familiar, but they are notoriously inaccurate. Even some of the simplest-looking arithmetic can produce inaccurate results:    

    0.1 + 0.2; // 0.30000000000000004
    
While 64 bits of precision is reasonably large, doubles can still only represent a finite set of numbers, rather than the infinite set of real numbers. Floating-point arithmetic can only produce approximate results, rounding to the nearest representable real number. When
you perform a sequence of calculations, these rounding errors can accumulate, leading to less and less accurate results. Rounding also causes surprising deviations from the kind of properties we usually expect of arithmetic. For example, real numbers are *associative*, meaning that for any real numbers x, y, and z, it’s always the case that `(x + y) + z = x + (y + z).`

But this is not always true of floating-point numbers:

    (0.1 + 0.2) + 0.3; // 0.6000000000000001
    0.1 + (0.2 + 0.3); // 0.6
    
Floating-point numbers offer a trade-off between accuracy and performance. When accuracy matters, it’s critical to be aware of their limitations. One useful workaround is to work with integer values wherever possible, since they can be represented without rounding.

When doing calculations with money, programmers often scale numbers up to work with the currency’s smallest denomination so that they can compute with whole numbers. For example, if the above calculation
were measured in dollars, we could work with whole numbers of cents instead:

    (10 + 20) + 30; // 60
    10 + (20 + 30); // 60
    
With integers, you still have to take care that all calculations fit within the range between –253 and 253, but you don’t have to worry about rounding errors.

# Conclusion:

- JavaScript numbers are double-precision floating-point numbers.
- Integers in JavaScript are just a subset of doubles rather than a separate datatype.
- Bitwise operators treat numbers as if they were 32-bit signed integers.
- Be aware of limitations of precisions in floating-point arithmetic.
