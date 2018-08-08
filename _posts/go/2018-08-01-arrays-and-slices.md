### Arrays

The slice type is an abstraction built on top of Go's array type, and so to understand slices we must first understand arrays.

An array type definition specifies a length and an element type. For example, the type [4]int represents an array of four integers. 

An array's size is fixed; its length is part of its type ([4]int and [5]int are distinct, incompatible types). 

Arrays can be indexed in the usual way, so the expression s[n] accesses the nth element, starting from zero.
```
var a [4]int
a[0] = 1
i := a[0]
// i == 1
```
Arrays do not need to be initialized explicitly; the zero value of an array is a ready-to-use array whose elements are themselves zeroed:
```
// a[2] == 0, the zero value of the int type
The in-memory representation of [4]int is just four integer values laid out sequentially:
```

Go's arrays are values. 

An array variable denotes the entire array; it is not a pointer to the first array element (as would be the case in C). 

This means that when you assign or pass around an array value you will make a copy of its contents. (To avoid the copy you could pass a pointer to the array, but then that's a pointer to an array, not an array.) 

One way to think about arrays is as a sort of struct but with indexed rather than named fields: a fixed-size composite value.

An array literal can be specified like so:
```
b := [2]string{"Penn", "Teller"}
Or, you can have the compiler count the array elements for you:

b := [...]string{"Penn", "Teller"}
In both cases, the type of b is [2]string.
```

### Slices

Arrays have their place, but they're a bit inflexible, so you don't see them too often in Go code. 

Slices, though, are everywhere. They build on arrays to provide great power and convenience.

The type specification for a slice is []T, where T is the type of the elements of the slice. 

Unlike an array type, a slice type has no specified length.

A slice literal is declared just like an array literal, except you leave out the element count:
```
letters := []string{"a", "b", "c", "d"}
```
A slice can be created with the built-in function called make, which has the signature,

func make([]T, len, cap) []T

where T stands for the element type of the slice to be created. The make function takes a type, a length, and an optional capacity. 
When called, make allocates an array and returns a slice that refers to that array.
```
var s []byte
s = make([]byte, 5, 5)
// s == []byte{0, 0, 0, 0, 0}
```
When the capacity argument is omitted, it defaults to the specified length. Here's a more succinct version of the same code:
```
s := make([]byte, 5)
```
The length and capacity of a slice can be inspected using the built-in len and cap functions.
```
len(s) == 5
cap(s) == 5
```
The next two sections discuss the relationship between length and capacity.

The zero value of a slice is nil. The len and cap functions will both return 0 for a nil slice.

A slice can also be formed by "slicing" an existing slice or array. 

Slicing is done by specifying a half-open range with two indices separated by a colon. 

For example, the expression b[1:4] creates a slice including elements 1 through 3 of b (the indices of the resulting slice will be 0 through 2).
```
b := []byte{'g', 'o', 'l', 'a', 'n', 'g'}
// b[1:4] == []byte{'o', 'l', 'a'}, sharing the same storage as b
```
The start and end indices of a slice expression are optional; they default to zero and the slice's length respectively:

```
// b[:2] == []byte{'g', 'o'}
// b[2:] == []byte{'l', 'a', 'n', 'g'}
// b[:] == b
```

This is also the syntax to create a slice given an array:
```
x := [3]string{"Лайка", "Белка", "Стрелка"}
s := x[:] // a slice referencing the storage of x
```

#### A possible "gotcha"
As mentioned earlier, re-slicing a slice doesn't make a copy of the underlying array. The full array will be kept in memory until it is no longer referenced. Occasionally this can cause the program to hold all the data in memory when only a small piece of it is needed.

For example, this FindDigits function loads a file into memory and searches it for the first group of consecutive numeric digits, returning them as a new slice.
```
var digitRegexp = regexp.MustCompile("[0-9]+")

func FindDigits(filename string) []byte {
    b, _ := ioutil.ReadFile(filename)
    return digitRegexp.Find(b)
}
```
This code behaves as advertised, but the returned []byte points into an array containing the entire file. Since the slice references the original array, as long as the slice is kept around the garbage collector can't release the array; the few useful bytes of the file keep the entire contents in memory.

To fix this problem one can copy the interesting data to a new slice before returning it:
```
func CopyDigits(filename string) []byte {
    b, _ := ioutil.ReadFile(filename)
    b = digitRegexp.Find(b)
    c := make([]byte, len(b))
    copy(c, b)
    return c
}
```
A more concise version of this function could be constructed by using append. This is left as an exercise for the reader.


ref:[slices]

  [slices]:https://blog.golang.org/go-slices-usage-and-internals
