**Challenge name**: Orion Override

**Vulnerability**: HTTP Parameter Pollution (HPP) — duplicate admin query parameters are handled differently by layers: a front filter reads the first occurrence while the backend uses the last.

**Solution**:
## Step 1: Log In to the Application
Navigate to https://orion-override.ctf.csaw.io/. Find the credentials user:password hidden in an HTML comment in the page source and log in. You will be redirected to the user dashboard.

## Step 2: Analyze the Dashboard URL
Observe that your URL is .../dashboard?admin=false and the admin functions are disabled. Confirm that changing the parameter to ?admin=true does not work, which indicates a security filter is blocking the attempt.

## Step 3: Execute the HTTP Parameter Pollution Attack
To bypass the filter, send both a harmless and a malicious parameter. Modify the URL in your browser's address bar to the following payload and press Enter:

https://orion-override.ctf.csaw.io/dashboard?admin=false&admin=true

## Step 4: Capture the Flag
The page will reload with the "Abort Override Functions" enabled. Click any of the newly enabled buttons to get the flag.
