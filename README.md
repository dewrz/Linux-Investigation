# Linux-Investigation

This is from a security challenge from one of the cyber ranges that I use regulalry for education purposes. They want us to help remediate some issues that a web server is experiencing.<br>
<br>
<b>Step 1:</b><br>
<br>
First they want us to visit the website. Upon doing so, a PDF file was downloaded to the VM automatically. After that happens, I immediately take a look at the source code and at the bottom there is an iframe code snippet pointing to a php file, which is certainly suspicious.<br>
<br>
<a href="https://imgur.com/pxu1R75"><img src="https://i.imgur.com/pxu1R75.png" title="source: imgur.com" /></a><br>
<br>
<a href="https://imgur.com/NOA3cP5"><img src="https://i.imgur.com/NOA3cP5.png" title="source: imgur.com" /></a><br>
<br>
<br>
<b>Step 2:</b><br>
Next they want me to analyze the file. So I SSH into the server and use finger to check and see if anyone else is logged on, which they aren't. So we will take the file over to VirusTotal to scan it. The module asks how many JavaScript blocks the pdf containts(3) and for the SHA-256 hash of the file.<br>
<br>
<a href="https://imgur.com/xA6G5u3"><img src="https://i.imgur.com/xA6G5u3.png" title="source: imgur.com" /></a><br>
<br>
<b>Step 3:</b><br>
<br>
Now they would like us to remove the malware. So we'll locate the pdf file and then remove it.<br>
<br>
<a href="https://imgur.com/xBd8YaR"><img src="https://i.imgur.com/xBd8YaR.png" title="source: imgur.com" /></a><br>
<br>
<b>Step 4:</b><br>
<br>
The malicious pdf keeps returning, so we will have to find the root cause. We will download a known good backup from Github and compare that with the current website files on the server. After inspecting the good backup from the current files, we see there are two php files on the server, which are possibly web shells. I also inspected the code of the index.html and the cloned version does not include the iframe.<br>
<br>
<a href="https://imgur.com/DrQG2DC"><img src="https://i.imgur.com/DrQG2DC.png" title="source: imgur.com" /></a><br>
<br>
Next we will edit the code to remove the iframe.<br>
<br>
<a href="https://imgur.com/6w8RhaD"><img src="https://i.imgur.com/6w8RhaD.png" title="source: imgur.com" /></a><br>
<br>
Now we will inspect both the .wget.php and the jquery-1.11.3.min.php files and remove them if they are malicious.<br>
<br>
<a href="https://imgur.com/xN7Ocog"><img src="https://i.imgur.com/xN7Ocog.png" title="source: imgur.com" /></a><br>
<br>
<a href="https://imgur.com/rmC7jci"><img src="https://i.imgur.com/rmC7jci.png" title="source: imgur.com" /></a><br>
<br>
They both appear to be malicious, so we removed them from the system.<br>
<br>
<b>Step 5:</b><br>
<br>
So now we have to figure out how they got into the system. So we will comb through the logs looking for failed authentications. After looking through the logs it looks like they gained entry through the "student" login via ssh.<br>
<br>
<a href="https://imgur.com/DPiQ1Im"><img src="https://i.imgur.com/DPiQ1Im.png" title="source: imgur.com" /></a><br>
<br>
We will also check the Apache logs to see if the shell communicated outside of the network. It certianly did, so we will swing back around one more time to make sure all of the files are cleared from the system<br>
<a href="https://imgur.com/QrF0M5k"><img src="https://i.imgur.com/QrF0M5k.png" title="source: imgur.com" /></a><br>
<br>
To finish up, we will check CRON jobs and we see that there was a job created to create daily a generic password for the student username. We will delete that job, change the student password and install SSHGuard to harden the system.<br>
<br>
And there you have it, the system has been remediated and hardened.



