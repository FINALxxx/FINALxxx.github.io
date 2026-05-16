!!! success "这个页面仅用于测试语法"

## code_fence
```python
print("Hello, world!")
```

## code_colon
    :::python
    def fibonacci(n):
        a, b = 0, 1
        for _ in range(n):
            yield a
            a, b = b, a + b

    for num in fibonacci(10):
        print(num)

## exec_code
/// codexec

    :::python
    def fibonacci(n):
        a, b = 0, 1
        for _ in range(n):
            yield a
            a, b = b, a + b

    for num in fibonacci(10):
        print(num)

///

## hover_card
You can [hover it at the bottom]^[#test] (default),
or [at the top]^[#test-top],
or [at the right]^[#test-right],
or [at the left]^[#test-left].

/// hover-card | test

I am at the bottom.

///

/// hover-card | test-top
    position: top

I am the top.

///

/// hover-card | test-right
    position: right

I am at the right.

///

/// hover-card | test-left
    position: left

I am at the left.

///

## tab
/// tab | `pip`

    :::bash
    pip install mkdocs-shadcn
///

/// tab | uv

    :::bash
    uv add mkdocs-shadcn
///

/// tab | poetry

    :::bash
    poetry add mkdocs-shadcn
///