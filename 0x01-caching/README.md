# Caching Project

## Overview

This project focuses on implementing various caching algorithms in Python. The goal is to create classes that inherit from a base caching class and demonstrate different caching strategies like Basic, FIFO, LIFO, LRU, MRU, and LFU.

## Project Structure

- `base_caching.py`: Contains the base caching class `BaseCaching` with common functionalities.

- `0-basic_cache.py`: Implementation of the BasicCache class, a simple caching system without any limits.

- `1-fifo_cache.py`: Implementation of the FIFOCache class, following the First-In-First-Out caching algorithm.

- `2-lifo_cache.py`: Implementation of the LIFOCache class, following the Last-In-First-Out caching algorithm.

- `3-lru_cache.py`: Implementation of the LRUCache class, following the Least Recently Used caching algorithm.

- `4-mru_cache.py`: Implementation of the MRUCache class, following the Most Recently Used caching algorithm.

- `100-lfu_cache.py`: Implementation of the LFUCache class, following the Least Frequently Used caching algorithm.

## How to Use

### Prerequisites

- Python 3.7 or later
- pycodestyle 2.5

### Setting Up

1. Clone the repository:

   ```bash
   git clone https://github.com/mugambi12/alx-backend.git
   ```

2. Navigate to the project directory:

   ```bash
   cd alx-backend/0x01-caching
   ```

### Running the Examples

1. Execute the desired main file (e.g., `./0-main.py` for BasicCache):

   ```bash
   ./0-main.py
   ```

2. Follow the output to observe the cache behavior based on the implemented algorithm.

## Contributing

Feel free to contribute by adding more caching algorithms or improving existing ones. Follow the coding style guidelines and ensure that your code is well-documented.

## License

This project is licensed under the [MIT License](LICENSE).

---
