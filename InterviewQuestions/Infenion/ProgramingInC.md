
## Write a function to find the second largest number in an array.

```c
#include <stdio.h>
#include <limits.h>

int second_largest(const int *arr, size_t n) {
    if (n < 2) return INT_MIN; // Not enough elements

    int max1 = INT_MIN, max2 = INT_MIN;

    for (size_t i = 0; i < n; i++) {
        if (arr[i] > max1) {
            max2 = max1;
            max1 = arr[i];
        } else if (arr[i] > max2 && arr[i] != max1) {
            max2 = arr[i];
        }
    }

    return max2;
}

int main(void) {
    int arr[] = {12, 35, 1, 10, 34, 1};
    size_t n = sizeof(arr) / sizeof(arr[0]);

    int result = second_largest(arr, n);
    if (result == INT_MIN)
       printf("No second largest element\n");
    else
        printf("Second largest: %d\n", result);

    return 0;
}
```

## Implement a custom strlen() function using pointers.

```c
#include <stdio.h>

size_t my_strlen(const char *str) {
    const char *p = str;  // pointer to traverse
    while (*p++ != '\0') {}
    return (size_t)(p - str - 1);  // length = difference in addresses
}

int main(void) {
    const char *text = "EmbeddedC";
    printf("Length of \"%s\" = %zu\n", text, my_strlen(text));
    return 0;
}
```

## How would you reverse a string using only pointers?

```c
#include <stdio.h>

/* Reverses the string in place using only pointers.
 * Returns the same pointer for convenience.
 */
char *str_reverse(char *s) {
    if (s == NULL) return NULL;       // handle NULL input

    char *left = s;
    char *right = s;

    // Move right to the last character (not the null terminator)
    while (*right != '\0') {
        right++;
    }
    right--;                          // step back to last actual char

    // Swap towards the center
    while (left < right) {
        char tmp = *left;
        *left = *right;
        *right = tmp;
        left++;
        right--;
    }

    return s;
}

int main(void) {
    char buf1[] = "EmbeddedC";
    char buf2[] = "";                 // empty
    char buf3[] = "A";                // single char

    printf("orig: \"%s\"  rev: \"%s\"\n", buf1, str_reverse(buf1));
    printf("orig: \"%s\"  rev: \"%s\"\n", buf2, str_reverse(buf2));
    printf("orig: \"%s\"  rev: \"%s\"\n", buf3, str_reverse(buf3));
    return 0;
}
```

## Write a function to count the number of set bits in an integer.

```c
#include <stdio.h>

unsigned int count_set_bits(unsigned int n) {
    unsigned int count = 0;
    while (n) {
        n &= (n - 1); // clear the lowest set bit
        count++;
    }
    return count;
}

int main(void) {
    unsigned int num = 5; // Example: 0101 (binary)
    printf("Number of set bits in %u = %u\n", num, count_set_bits(num));
    return 0;
}
```

## How do you reverse the bits of a number?

```c
#include <stdint.h>
#include <stdio.h>

/* Reverse bits of a 32-bit unsigned integer */
uint32_t reverse_bits32_loop(uint32_t x) {
    uint32_t y = 0;
    for (int i = 0; i < 32; ++i) {
        y = (y << 1) | (x & 1U);
        x >>= 1;
    }
    return y;
}

int main(void)
{
    uint32_t num = reverse_bits32_loop(0x5a5a5a00);
    printf("Reverse: %x\n", num);
    return 0;
}
```

## Clear/reset bits range

```c
#include <stdio.h>
#include <stdint.h>

uint32_t clear_bits_range(uint32_t num, unsigned l, unsigned r) {
    unsigned bits2clear = r - l + 1;
    uint32_t mask = ((1U << bits2clear) - 1U) << l;
    return num & ~mask;
}

int main(void) {
    uint32_t num = 0xFFFF; // 1111111111111111
    unsigned l = 4, r = 7; // clear bits 4..7
    uint32_t result = clear_bits_range(num, l, r);
    printf("Original: 0x%X\n", num);
    printf("After clearing bits %u..%u: 0x%X\n", l, r, result);
    return 0;
}
```

## Write a custom implementation of malloc().

```c
#include <stdio.h>
#include <stdint.h>

#define HEAP_SIZE 1024  // 1 KB fixed heap

static uint8_t heap[HEAP_SIZE];
static size_t  heap_index = 0;

// Simple malloc using bump allocation
void *simple_malloc(size_t size) {
    if (size == 0) return NULL;

    // Align to 4 bytes for safety
    size = (size + 3) & ~((size_t)3);

    if (heap_index + size > HEAP_SIZE) {
        // Out of memory
        return NULL;
    }

    void *ptr = &heap[heap_index];
    heap_index += size;
    return ptr;
}

// Reset heap (simulate free all)
void simple_free_all(void) {
    heap_index = 0;
}

// Demo
int main(void) {
    printf("Heap size: %d bytes\n", HEAP_SIZE);

    int *a = (int *)simple_malloc(sizeof(int) * 10);
    char *b = (char *)simple_malloc(50);
    float *c = (float *)simple_malloc(20 * sizeof(float));

    if (a && b && c) {
        printf("Allocated arrays:\n");
        printf("a at %p\n", (void *)a);
        printf("b at %p\n", (void *)b);
        printf("c at %p\n", (void *)c);
    } else {
        printf("Allocation failed!\n");
    }

    // Reset heap
    simple_free_all();
    printf("Heap reset. Next allocation starts from beginning.\n");

    return 0;
}
```

