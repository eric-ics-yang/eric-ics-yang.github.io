### Debugging

Some tips from [debugging], Chapter 5 of "The Practice of Programming".

  [debugging]:https://www.cs.princeton.edu/~bwk/tpop.webpage/debugging.html
  
#### Good Clues, Easy Bugs
Debugging involves backwards reasoning, Something impossible occurred, and the only solid information is that it really did occur. 
So we must think backwards from the result to discover the reasons. 

Once we have a full explanation, we'll know what to fix and, along the way, likely discover a few other things we hadn't expected. 

- First：
```
Look for familiar patterns.
    ...
Examine the most recent change.
    ...
Don't make the same mistake twice.
    ...
Debug it now, not later.
```

**Being in too much of a hurry can hurt.**

Don't ignore a crash when it happens; track it down right away, since it may not happen again until it's too late. 

- Second:
```
Get a stack trace.
    ...
Read before typing.
    ...
Explain your code to someone else.
```
**Another effective technique is to explain your code to someone else.** 

This will often cause you to explain the bug to yourself. 
Sometimes it takes no more than a few sentences, followed by an embarrassed ``Never mind, I see what's wrong. Sorry to bother you.'' 

- No Clues, Hard Bugs

``I haven't got a clue. What on earth is going on?'' 
```
Make the bug reproducible.
Divide and conquer.
Study the numerology of failures.
Display output to localize your search.
Write self-checking code.
Write a log file.(remember to flush io.)
Draw a picture.
Use tools.
Keep records.
```
**Use tools** 
For example, a file comparison program like diff compares the outputs from successful and failed debugging runs so you can focus on what has changed. 
If your debugging output is long, use grep to search it or an editor to examine it. 
Resist the temptation to send debugging output to a printer: computers scan voluminous output better than people do. 
Use shell scripts and other tools to automate the processing of the output from debugging runs. 
**keep records**
If the search for a bug goes on for any length of time, you will begin to lose track of what you tried and what you learned. 
If you record your tests and results, you are less likely to overlook something or to think that you have checked some possibility when you haven't. 
The act of writing will help you remember the problem the next time something similar comes up, and will also serve when you're explaining it to someone else. 


 
