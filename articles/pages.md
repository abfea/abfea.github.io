# Test

## math

$$ x^n + y^n = z^n $$

$$
    M = \left( \begin{array}{ccc}
    x_{11} & x_{12} & \ldots \\
    x_{21} & x_{22} & \ldots \\
    \vdots & \vdots & \ldots \\
    \end{array} \right)
$$

inline dispaly $$e = mc^2$$

## colorscheme

```c
#include<stdio.h>

int main(void)
{
  printf("Hello, world!");
  return 0;
}
```

```cpp
#include <concepts>
#include <iostream>

template<typename T, typename ... U>
concept IsAnyOf = (std::same_as<T, U> || ...);

template<typename T>
concept IsPrintable = std::integral<T> || std::floating_point<T> ||
    IsAnyOf<std::remove_cvref_t<std::remove_pointer_t<std::decay_t<T>>>, char, wchar_t>;

void println(IsPrintable auto const ... arguments)
{
    (std::wcout << ... << arguments) << '\n';
}

int main() { println("Example: ", 3.14, " : ", 42, " : [", 'a', L'-', L"Z]"); }
```

```python
def fib_recursive(n):
    assert n >= 0, "n must be larger than 0"
    if n <= 1:
        return n
    return fib_recursive(n-1) + fib_recursive(n-2)

if __name__ == '__main__':
    [print(fib_recursive(i),end=',') for i in range(1,15)]
```

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```
