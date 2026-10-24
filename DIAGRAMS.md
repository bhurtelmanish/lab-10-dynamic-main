# Lab 10: memory diagrams and flowcharts

## Shallow copy (unsafe)

```text
original.m_cards ----+----> [Card 0] [Card 1] ... [Card 51] (heap)
                     |
backup.m_cards ------+

Both objects own the same pointer. Destroying both causes double deletion.
```

## Deep copy (safe)

```text
original.m_cards --------> [Card 0] [Card 1] ... [Card 51] (heap A)
backup.m_cards ----------> [Card 0] [Card 1] ... [Card 51] (heap B)

Each Deck owns a separate array; changing one does not change the other.
```

## Shuffle (Fisher–Yates)

```mermaid
flowchart TD
 A[Start] --> B[Set i to m_size minus 1]
 B --> C{Is i greater than 0?}
 C -- Yes --> D[Choose random j from 0 through i]
 D --> E[Swap cards at i and j]
 E --> F[Decrease i]
 F --> C
 C -- No --> G[Set m_next to 0]
 G --> H[End]
```

## Copy constructor

```mermaid
flowchart TD
 A[Start with other Deck] --> B[Allocate new Card array of other.m_size]
 B --> C[Copy m_size and m_next]
 C --> D[Set i to 0]
 D --> E{Is i less than m_size?}
 E -- Yes --> F[Copy card at index i]
 F --> G[Increase i]
 G --> E
 E -- No --> H[End: separate owned array]
```

## Copy assignment

```mermaid
flowchart TD
 A[Start assignment] --> B{Is this the same object as other?}
 B -- Yes --> Z[Return this object]
 B -- No --> C[Allocate temporary new Card array]
 C --> D[Copy all cards from other]
 D --> E[Delete old owned array]
 E --> F[Store new pointer, size, and next index]
 F --> Z
```
