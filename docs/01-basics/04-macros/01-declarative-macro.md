# Declarative Macros

```rust
macro_rules! fibonacci {
    ($x: expr) => {
        if($x<2){
            $x
        }
        else{
            fibonacci!($x-1) + fibonacci!($x-2);
        }
    };
}
fn main() {
    fibonacci!(1);
}
```
