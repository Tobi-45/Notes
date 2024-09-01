# Web Fuzzing

**Web fuzzing is a critical technique that every penetration tester should master. Unlike traditional methods that rely on predictable inputs, 
fuzzing systematically explores the vast input space to uncover hidden vulnerabilities, often revealing weaknesses that would otherwise remain unnoticed.**

## Fuzzing vs. Brute-forcing
Fuzzing casts a wider net. It involves feeding the web application with unexpected inputs, including malformed data, invalid characters, and nonsensical combinations. The goal is to see how the application reacts to these strange inputs and uncover potential vulnerabilities in handling unexpected data. Fuzzing tools often leverage wordlists containing common patterns, mutations of existing parameters, or even random character sequences to generate a diverse set of payloads.

Brute-forcing, on the other hand, is a more targeted approach. It focuses on systematically trying out many possibilities for a specific value, such as a password or an ID number. Brute-forcing tools typically rely on predefined lists or dictionaries (like password dictionaries) to guess the correct value through trial and error.

Here's an analogy to illustrate the difference: Imagine you're trying to open a locked door. Fuzzing would be like throwing everything you can find at the door - keys, screwdrivers, even a rubber duck - to see if anything unlocks it. Brute-forcing would be like trying every combination on a key ring until you find the one that opens the door.

## FFUF

FFUF (Fuzz Faster U Fool) is a fast web fuzzer written in Go. It excels at quickly enumerating directories, files, and parameters within web
applications. Its flexibility, speed, and ease of use make it a favorite among security professionals and enthusiasts.

## wfuzz/wenum

wenum is a actively maintained fork of wfuzz, a highly versatile and powerful command-line fuzzing tool known for its flexibility and customization options. 
It's particularly well-suited for parameter fuzzing, allowing you to test a wide range of input values against web applications and uncover potential vulnerabilities in how they process those parameters.

# ffuf commands

Directory Fuzzing:

`ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http: /IP:PORT/FUZZ`

File Fuzzing:

`ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http: /IP:PORT/w2ksvrus/FUZZ.html -e .php,.html,.txt,.bak,.js -v`

# Recursive Fuzzing

It  starts as a normal fuzzing, but once it finds any directory it creates a sub task and starts fuzzing for that directory, untill a requirment is meet or till there are no further dir left.

## Commands 

`ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://IP:PORT/FUZZ -e .html -recursion`

`-ic` option proves invaluable. By enabling this option, ffuf intelligently ignores commented lines during fuzzing, preventing them from being treated as valid inputs.

While recursive fuzzing is a powerful technique, it can also be resource-intensive, especially on large web applications. 
Excessive requests can overwhelm the target server, potentially causing performance issues or triggering security mechanisms

To mitigate these risks, ffuf provides options for fine-tuning the recursive fuzzing process:

`-recursion-depth`: This flag allows you to set a maximum depth for recursive exploration. For example, -recursion-depth 2 limits fuzzing to two levels deep (the starting directory and its immediate subdirectories).

`-rate`: You can control the rate at which ffuf sends requests per second, preventing the server from being overloaded.

`-timeout`: This option sets the timeout for individual requests, helping to prevent the fuzzer from hanging on unresponsive targets.

Example `ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500`

# Parameter (get, post) and Value Fuzzing

This technique focuses on manipulating the parameters and their values within web requests to uncover vulnerabilities in how the application processes input.

Parameters are the messengers of the web, carrying vital information between your browser and the server that hosts the web application. They're like variables
in programming, holding specific values that influence how the application behaves.















