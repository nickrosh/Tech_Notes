A struct is a way to define a type that consists of one or more other types pasted together. This is how C allows custom data structures.. Her is a typical struct definition:

```C
struct string {
	int length;
	char* data
};

int main() {
	struct string s;
	s.length = 4;
	s.data = "yolo";
}
```

We can also create pointers to structs. This allows us to pass large structs around without copying the entire data structure. When doing so, you can access member variables with a special (->) operator. When a struct is [dynamically allocated](Dynamic%20Memory%20Allocation.md) you will need to use a pointer to manage and interact with it.

```C
struct string *sp

sp->data = "new string";
```


#### Typedefs

Typedefs allow us to define types with a different name - which can come in handy when dealing with structs and pointers. This also means we don't have to write `struct` every time we want to initialize a struct.

```C
typedef struct Point {
	int x;
	int y;
} Point;

Point point_new(int x, int y) {
	Point a;
	a.x = x;
	a.y = y;
	return a;
}
```

This is much cleaner than having to write the full struct out over and over again, and looks like a proper type definition.