# SDS
### What is binary-safe string?
What do people mean when they say **“binary-safe strings”**? In C, strings are traditionally represented as a pointer to bytes, i.e. char*, where the array of bytes is terminated by a “null byte” (i.e. '\0', i.e. 0). This representation has the disadvantage that your string of bytes cannot itself contain a null byte, and so this structure cannot represent arbitrary strings of bytes. That is, C-strings are not “binary-safe”.

Binary-safe strings in C are typically implemented with an explicit known length. Something like:

```
struct bytestring {
  size_t len;
  unsigned char * bytes;
};
```
(Reference: [https://jameshfisher.com/2016/12/06/binary-safe-strings/](https://jameshfisher.com/2016/12/06/binary-safe-strings/))

### Flexible Array Member
Flexible Array Member(FAM) is a feature introduced in the C99 standard of the C programming language.

- For the structures in C programming language from C99 standard onwards, we can declare an array without a dimension and whose size is flexible in nature.
- Such an array inside the structure should preferably be declared as the last member of structure and its size is variable(can be changed be at runtime).
- The structure must contain at least one more named member in addition to the flexible array member.

(Reference: [https://www.geeksforgeeks.org/flexible-array-members-structure-c/](https://www.geeksforgeeks.org/flexible-array-members-structure-c/))

### SDS Types
+ sdshdr5 (Never Used)
+ sdshdr8
+ sdshdr16
+ sdshdr32
+ sdshdr64

### Definition (sds.h)
```C
#define SDS_TYPE_5  0
#define SDS_TYPE_8  1
#define SDS_TYPE_16 2
#define SDS_TYPE_32 3
#define SDS_TYPE_64 4

/* Note: sdshdr5 is never used, we just access the flags byte directly.
 * However is here to document the layout of type 5 SDS strings. */
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

#### sdshdr5 (Never Used)
```
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
```
**flags:** 3 bits from left stands for **type**, remaining 5 bits stands for **length**.  
**buf:** The actual string.

#### sdshdr8, sdshdr16, sdshdr32, sdshdr64
**len:** The length of buf. This makes obtaining the length of a Redis string an O(1) operation.  
**alloc:** The memory allocated for this SDS. It may not be fully used. The available size for using is `alloc - len`.  
**flags:** 3 bits for indicating the type of SDS.  
**buf:** The actual string.



