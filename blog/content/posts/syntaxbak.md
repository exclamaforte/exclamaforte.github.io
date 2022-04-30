---
title: "Dueling Ontologies"
date: 2022-04-29T20:59:16-07:00
draft: true
categories: ["software", "work", "psychology"]
tags: ["ontologies"]
ShowShareButtons: true
---

# hello world
this is some text
## title 1
### title 2
**bold text**
*italic*
> blockquote
1. arst
2. tsra

- first
- second

`code 1`
```c++
template<long FROM, long TO>
class Range {
public:
    // member typedefs provided through inheriting from std::iterator
    class iterator: public std::iterator<
                        std::input_iterator_tag,   // iterator_category
                        long,                      // value_type
                        long,                      // difference_type
                        const long*,               // pointer
                        long                       // reference
                                      >{
        long num = FROM;
    public:
        explicit iterator(long _num = 0) : num(_num) {}
        iterator& operator++() {num = TO >= FROM ? num + 1: num - 1; return *this;}
        iterator operator++(int) {iterator retval = *this; ++(*this); return retval;}
        bool operator==(iterator other) const {return num == other.num;}
        bool operator!=(iterator other) const {return !(*this == other);}
        reference operator*() const {return num;}
    };
    iterator begin() {return iterator(FROM);}
    iterator end() {return iterator(TO >= FROM? TO+1 : TO-1);}
};
```

```code 2
code 3```
---
[arsttsra](www.example.com)
- [x] task 1
- [ ] task 2

:joy:


