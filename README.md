# sUNC Introduction

## What is "sUNC"?

**sUNC** stands for **senS' Unified Naming Convention**. It's a tool designed to check if an executor can properly run essential global functions, following the existing [Unified Naming Convention](https://github.com/unified-naming-convention/NamingStandard/tree/main).  
Unlike the original UNC, which is now outdated and prone to spoofing, sUNC ensures that functions actually work as intended, by testing them as if they were to be used in a real scenario.     
However, we are not, in any way calling UNC "bad", we are simply saying people shouldn't be using UNC to deeply test their environment, as that is not its purpose.

## How would I know what fails?

We are not gatekeeping function tests, despite the script being obfuscated.  
You are welcome to ask the owner (@sens6222 on Discord) if you are struggling with passing certain tests. We keep it this way for now, due to  executors already faking their environments to suit the [original UNC](https://github.com/unified-naming-convention/NamingStandard/tree/main).

Note that it's also possible to see the detailed reason of a function's failure at the top of the `Developer Console` in game.

## Will sUNC be discontinued?

Not in the near future, we hope. However, if sUNC would be discontinued, we will open source the project after 1-2 months.

-----

# Credits

- [Original UNC Documentation](https://github.com/unified-naming-convention/NamingStandard/tree/main)