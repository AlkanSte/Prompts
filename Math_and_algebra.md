This was a fun challenge initially, bc Claude 3.7 really struggles with simple digits math. 

The classical errors are:

5.4-5.11 or 9.11-9.3
The first part btw, is because of confusion of feet and inches and digits

The LLM brain was hard to comprehend bc it always for the first example gave someting like 0.21.
My thoughts is that it is connected to digit tokenization, (thanks to previous LLaMa advances in it for math reasoning).
You can read about it in their papers from 2023 I think.

So my thoughts is that the model treats digit fraaction numbers the same as integers, adding fp precision at the end.
You can also see an example of this if you tokenize any number like 4.123 and see.

So I have produced 3 versions of prompts, each universal and smaller than 70 tokens (by OAI Tokenizer).

## V1

Solve following math problem by provinding algebraic reasoning step by step.
Math problem:\[  \]

### Comments
That was a nice idea but lackluster result, essentially wrote this prompt in blind without testing it. Yes it solved easier examples but was too basic.

## V2

Solve following math problem by provinding algebraic reasoning step by step, verifying your solution through multiplying the terms so that there will be no decimal places and all terms would be integers (ignoring the floating point rounding)
Math problem:\[  \]

### Comments
This was better, but I still have not tested it aside from ChatGPT. Essentially I saw that the model can easily solve basic examples by getting rid of fractions, so i pushed on it.

## V3

Solve the following math problem by:
1. Reasoning 1 operation at a time.
2. You are prohibited to operate on decimal fractions. Use frac{} and trust it.
3. Output the answer in decimal form if applicable.
Math problem: Math problem: $ ... $

### Comments
Here you can see that I finally got to api calling and testing. We got new tokens for 'math', strong instructions, and 'beautify' point #3 in the end.
Unfortunately it faild to solve harder examples like \[ 2 * (9.11 - 9.7) + 2 * pi = x \]

## V4
Solve the following math problem by:
0. Declare PEMDAS order for it.
1. Reasoning 1 operation at a time including conversions.
2. You are prohibited to operate on decimal fractions. Use frac{} and trust it (without frac{x}{1}).
3. Output the answer in decimal form if applicable.
Math problem: $ ... $

### Comments
This is the result im ok with. It declared PEMDAS order specifically for harder prompts like \[ (9.11 - 9.7) - 2 * pi / e / 2^3 / 5 \]
It asks to declare conversions (in which it stumbled before)
It prohibits to operate on decimal fractions but in a loose way, allowing for it in some examples like working with irrational constants operations.
#3 is just a 'beautify' part

Overall I like that I went from having problems with 5.4-5.11 to comples problems that require explicit pemdas declaration prior to solving and 'digit masking' (like when the answer to the subqueries would produce the adversarial digit operation like 5.4-5.11)
