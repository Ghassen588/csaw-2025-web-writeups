**Challenge name**: Smolder Alexandria

**Vulnerability**: Blind OS command injection where stdout is suppressed but stderr is returned. Output can be forced into error messages (stderr) by using failing commands whose error text contains desired data.

Solution:
## Step 1: Find the Real Injection Point

First, use your browser's Network Tools while searching on the main page. This will show you that the real requests are sent to a hidden URL. The actual injection point is the q parameter at this endpoint: .../vault/search?q=[payload].

## Step 2: Discover the stderr Information Leak

Next, test the endpoint to understand how it handles output. Send two payloads:

A valid command: $(env)

An invalid command: $(/a/b/c)

The valid command will return an empty result. The invalid command will return a large log dump containing the error. This proves that normal output (stdout) is hidden, but error output (stderr) is visible. Your entire strategy must be to force command results into error messages.

## Step 3: List the Files in the Directory

To see the filenames, you need to cause an error for each file. Use the rmdir command, as it fails when used on a file, and its error message contains the filename.

Payload: .../vault/search?q=$(rmdir $(find .))

This will reveal all the files in the current directory by listing them in a series of error messages.

## Step 4: Identify the Flag File

Analyze the error messages from the previous step. You will see an error specifically for flag.txt, which will look like: rmdir: failed to remove './flag.txt': Permission denied. This confirms your target.

## Step 5: Read and Exfiltrate the Flag

Finally, force the content of flag.txt to appear in an error message.

Payload: .../vault/search?q=$(head $(head ./flag.txt))

This command works in two stages. The inner head reads the flag (hidden), and the outer head tries to open the flag's content as a file. This fails, and the "No such file or directory" error it produces will contain the flag itself, revealing it through the visible stderr channel.
