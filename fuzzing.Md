# Web Fuzzing

**Web fuzzing is a critical technique that every penetration tester should master. Unlike traditional methods that rely on predictable inputs, 
fuzzing systematically explores the vast input space to uncover hidden vulnerabilities, often revealing weaknesses that would otherwise remain unnoticed.**

## Fuzzing vs. Brute-forcing
Fuzzing casts a wider net. It involves feeding the web application with unexpected inputs, including malformed data, invalid characters, and nonsensical combinations. The goal is to see how the application reacts to these strange inputs and uncover potential vulnerabilities in handling unexpected data. Fuzzing tools often leverage wordlists containing common patterns, mutations of existing parameters, or even random character sequences to generate a diverse set of payloads.

Brute-forcing, on the other hand, is a more targeted approach. It focuses on systematically trying out many possibilities for a specific value, such as a password or an ID number. Brute-forcing tools typically rely on predefined lists or dictionaries (like password dictionaries) to guess the correct value through trial and error.

Here's an analogy to illustrate the difference: Imagine you're trying to open a locked door. Fuzzing would be like throwing everything you can find at the door - keys, screwdrivers, even a rubber duck - to see if anything unlocks it. Brute-forcing would be like trying every combination on a key ring until you find the one that opens the door.

# FFUF

FFUF (Fuzz Faster U Fool) is a fast web fuzzer written in Go. It excels at quickly enumerating directories, files, and parameters within web
applications. Its flexibility, speed, and ease of use make it a favorite among security professionals and enthusiasts.

## wfuzz/wenum

wenum is a actively maintained fork of wfuzz, a highly versatile and powerful command-line fuzzing tool known for its flexibility and customization options. 
It's particularly well-suited for parameter fuzzing, allowing you to test a wide range of input values against web applications and uncover potential vulnerabilities in how they process those parameters.





