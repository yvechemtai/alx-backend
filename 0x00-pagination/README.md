# 0x00. Pagination

## Back-end

**By:** Emmanuel Turlay, Staff Software Engineer at Cruise

**Weight:** 1

**Project Period:** Dec 21, 2023, 6:00 AM - Jan 2, 2024, 6:00 AM

**Checker Release:** Dec 24, 2023, 6:00 AM

**Auto Review:** Will be launched at the deadline

## Resources

Read or watch:

- [REST API Design: Pagination](#)
- [HATEOAS](#)

## Learning Objectives

At the end of this project, you are expected to be able to explain to anyone, without the help of Google:

- How to paginate a dataset with simple page and page_size parameters
- How to paginate a dataset with hypermedia metadata
- How to paginate in a deletion-resilient manner

## Requirements

- All files interpreted/compiled on Ubuntu 18.04 LTS using python3 (version 3.7)
- All files should end with a new line
- The first line of all files should be exactly `#!/usr/bin/env python3`
- A README.md file, at the root of the folder of the project, is mandatory
- Code should use the pycodestyle style (version 2.5.\*)
- The length of files will be tested using wc
- All modules should have documentation (`python3 -c 'print(__import__("my_module").__doc__)'`)
- All functions should have documentation (`python3 -c 'print(__import__("my_module").my_function.__doc__)'`)
- Documentation should be a real sentence explaining the purpose of the module, class, or method
- All functions and coroutines must be type-annotated
- Setup: [Popular_Baby_Names.csv](#) - Use this data file for your project

## Tasks

### 0. Simple Helper Function

**Write a function named `index_range` that takes two integer arguments `page` and `page_size`.**

The function should return a tuple of size two containing a start index and an end index corresponding to the range of indexes to return in a list for those particular pagination parameters.

```python
bob@dylan:~$ cat 0-main.py
#!/usr/bin/env python3
"""
Main file
"""

index_range = __import__('0-simple_helper_function').index_range

res = index_range(1, 7)
print(type(res))
print(res)

res = index_range(page=3, page_size=15)
print(type(res))
print(res)
```

### 1. Simple Pagination

**Copy `index_range` from the previous task and the following class into your code:**

```python
import csv
import math
from typing import List

class Server:
    """Server class to paginate a database of popular baby names.
    """
    DATA_FILE = "Popular_Baby_Names.csv"

    def __init__(self):
        self.__dataset = None

    def dataset(self) -> List[List]:
        """Cached dataset
        """
        if self.__dataset is None:
            with open(self.DATA_FILE) as f:
                reader = csv.reader(f)
                dataset = [row for row in reader]
            self.__dataset = dataset[1:]

        return self.__dataset

    def get_page(self, page: int = 1, page_size: int = 10) -> List[List]:
        pass
```

Implement a method named `get_page` that takes two integer arguments `page` with default value 1 and `page_size` with default value 10.

Use assert to verify that both arguments are integers greater than 0.

Use `index_range` to find the correct indexes to paginate the dataset correctly and return the appropriate page of the dataset (i.e., the correct list of rows).

If the input arguments are out of range for the dataset, an empty list should be returned.

```python
bob@dylan:~$  wc -l Popular_Baby_Names.csv
19419 Popular_Baby_Names.csv
```

### 2. Hypermedia Pagination

**Replicate code from the previous task.**

Implement a `get_hyper` method that takes the same arguments (and defaults) as `get_page` and returns a dictionary containing the following key-value pairs:

- `page_size`: the length of the returned dataset page
- `page`: the current page number
- `data`: the dataset page (equivalent to return from the previous task)
- `next_page`: number of the next page, None if no next page
- `prev_page`: number of the previous page, None if no previous page
- `total_pages`: the total number of pages in the dataset as an integer

Make sure to reuse `get_page` in your implementation.

You can use the math module if necessary.

```python
bob@dylan:~$ cat 2-main.py
#!/usr/bin/env python3
"""
Main file
"""

Server = __import__('2-hypermedia_pagination').Server

server = Server()

print(server.get_hyper(1, 2))
print("---")
print(server.get_hyper(2, 2))
print("---")
print(server.get_hyper(100, 3))
print("---")
print(server.get_hyper(3000, 100))
```

### 3. Deletion-Resilient Hypermedia Pagination

**The goal is to ensure that if between two queries, certain rows are removed from the dataset, the user does not miss items from the dataset when changing page.**

Start `3-hypermedia_del_pagination.py` with the provided code:

```python
#!/usr/bin/env python3
"""
Deletion-resilient hypermedia pagination
"""

import csv
import math
from typing import List

class Server:
    """Server class to paginate a database of popular baby names.
    """
    DATA_FILE = "Popular_Baby_Names.csv"

    def __init__(self):
        self.__dataset = None
        self.__indexed_dataset = None

    def dataset(self) -> List[List]:
        """Cached dataset
        """
        if self.__dataset is None:
            with open(self.DATA_FILE) as f:
                reader = csv.reader(f)
                dataset = [row for row in reader]
            self.__dataset = dataset[1:]

        return self.__dataset

    def indexed_dataset(self) -> Dict[int, List]:
        """Dataset indexed by sorting position, starting at 0
        """
        if self.__indexed_dataset is None:
            dataset = self.dataset()
            truncated_dataset = dataset[:1000]
            self.__indexed_dataset = {
                i: dataset[i] for i in range(len(dataset))
            }
        return self.__indexed_dataset

    def get_hyper_index(self, index: int = None, page_size: int = 10) -> Dict:
        pass
```

Implement a `get_hyper_index` method with two integer arguments: `index` with a `None` default value and `page_size` with a default value of 10.

The method should return a dictionary with the following key-value pairs:

- `index`: the current start index of the return page. That is the index of the first item on the current page.
- `next_index`: the next index to query with. That should be the index of the first

item after the last item on the current page.

- `page_size`: the current page size
- `data`: the actual page of the dataset

Requirements/Behavior:

- Use assert to verify that index is in a valid range.
- If the user queries index 0, page_size 10, they will get rows indexed 0 to 9 included.
- If they request the next index (10) with page_size 10, but rows 3, 6, and 7 were deleted, the user should still receive rows indexed 10 to 19 included.

```python
bob@dylan:~$ cat 3-main.py
#!/usr/bin/env python3
"""
Main file
"""

Server = __import__('3-hypermedia_del_pagination').Server

server = Server()

server.indexed_dataset()

try:
    server.get_hyper_index(300000, 100)
except AssertionError:
    print("AssertionError raised when out of range")

index = 3
page_size = 2

print("Nb items: {}".format(len(server._Server__indexed_dataset)))

# 1- request first index
res = server.get_hyper_index(index, page_size)
print(res)

# 2- request next index
print(server.get_hyper_index(res.get('next_index'), page_size))

# 3- remove the first index
del server._Server__indexed_dataset[res.get('index')]
print("Nb items: {}".format(len(server._Server__indexed_dataset)))

# 4- request again the initial index -> the first data retrieves is not the same as the first request
print(server.get_hyper_index(index, page_size))

# 5- request again initial next index -> same data page as the request 2-
print(server.get_hyper_index(res.get('next_index'), page_size))
```

---
