# Specifiers:-

## auto
Attempting to use a typeless and unitilaised variable declared with ```auto``` will result in a compile error:

```
std:vector<int> numbers;

// Compile Error
auto x;
numbers.push_back(x);

// Undefined Beaviour
int x;
numbers.push_back(x);
```

auto is often used as a convience to avoid typing out the mother of all template type declarations:

```
std::vector<std::pair<data, metaData>> dataList;

typedef std::vector<std::pair<data, metaData>>::iterator dataListIt;
dataListIt It = dataList.begin();

auto It = dataList.begin();
```

Admittadley ```std::vector<std::pair<data, metaData>>``` is not a massively unwieldly type, but it does get worse, and ```auto``` to declare the iterator is still more convienent than writing the whole type out by hand. Another more subtle advantage of auto is that it prevents us from making silly mistakes like declaring an incorrect type, which the type we want converts to. Such as declaring an ```int``` and assigning it to the return value of a function that returns some convuloted alias of ```long```.

## Trailing Return Type Syntax

This syntax allows us to include function arguments when declaring the function return type (NOTE: ```auto``` does not actually deduce anything here, it's just to indicate the trailing return type syntax): 

```
template<typename Term1, typename Term2>
auto Sum(const Term1& t1, const Term2& t2)
  -> decltype(t1 + t2)
{
    return t1 + t2;
}
```

## Type Deduction

```auto``` uses the same type dedcution system as templates with one minor execption:

```
auto x = { 1, 2, 3 };
```

Here ```auto``` will actually deduce to ```std::initializer_list<int>``` with 3 elements. Unfortunately 

```
auto x = { 1 };
```

will also deduce to ```std::initializer_list<int>```, this has been changed to deduce to ```int``` as of C++17.

Template type deduction doesn't take `const`ness or references into account which means here:

```
std::string strArr[10];
for(auto str : strArr)
{
}
```

```auto``` deduces to ```std::string```, this is typically solved by explicitly marking ```auto``` as a reference. However the array may be some complex type that returns r-values that will bind to and dangle from either ```T&``` or ```auto&```. C++14 solves this by introducing the ```decltype(auto)``` idiom, essentially it allows you to use the ```auto``` speicifer with ```decltype``` type deduction rules:
```
std::string strArr[10];
for(decltype(auto) str : strArr)
{
}
```

Here ```decltype(auto) == std::string&```.
