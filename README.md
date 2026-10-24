# Lab 10 - Dynamic Memory in Classes

## About This Project

This is my Lab 10 project for C++ Programming Fundamentals II.

In this lab, I worked on a Deck class for a Blackjack game. The main goal was to learn how dynamic memory works in C++ and how to copy objects correctly.

## What the Program Does

- Creates a deck of cards
- Shuffles the deck
- Deals five cards
- Makes a copy of the deck
- Shuffles the copied deck without changing the original

## What I Learned

- Using `new[]` and `delete[]`
- Constructors and destructors
- Copy constructors
- Assignment operators
- Deep copying and shallow copying
- Basic debugging with GDB

## How to Run

```bash
cmake -S . -B build
cmake --build build
./build/arcade
```

## How to Test

```bash
cd build
ctest --output-on-failure
```

## Files

- `deck.cpp` — Deck functions
- `deck.h` — Deck class
- `blackjack.cpp` — Blackjack demonstration
- `GDB_LOG.md` — Debugging notes
- `DIAGRAMS.md` — Memory diagrams and flowcharts
