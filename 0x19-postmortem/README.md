<h1>POSTMORTEM</h1>
<p>
<h3>Issue Summary</h3>
<hr>
On 13/10/2023 at 2100hrs WAT, 100% of the webserver was down for a total of 15 minutes. Service was restored at 2115hrs WAT. The website architecture consists of two servers.The usage of these servers is regulated by a loadbalancer, one server was down hence the burden of service lay on the second server. The root cause was a syntax error in the <code>/etc/nginx/sites-available/default</code> file of nginx running on one server. The synstax error was in the form of a missing semicolon in the added line, resulting in nginx failing to start.


<h3>Timeline</h3> 
<hr>
2100hrs WAT: Issue was detected. 2105hrs WAT: While using curl to check the response headers, the custom header indicating the name of the server that sent the response showed that only one server was responding to the requests 2108hrs WAT: Issue was handled by Silvia, the server administrator who logged into the server that was not responding. 2109hrs WAT: She ran the command sudo service nginx status to bring up a log of the current status of nginx. 2111hrs WAT: It was discovered that nginx was not running and the cause of failure was outlined in the following snippet:
<ul>
 <li><code>nginx[31101]: nginx: [emerg] invalid number of arguments in "rewrite" directive in /etc/nginx/sites-available/default</code></li>
 <li><code>nginx[31101]: nginx: configuration file /etc/nginx/nginx.conf test failed</code> 2113hrs WAT: She then opened the file stated in the first line of the snippet and added a semicolon to the rewrite directive. 2114hrs WAT: The file was saved and restarted Nginx. 2115hrs WAT: Service was restored.</li>
 </ul>
<h3>Root Cause and Resolution</h3><hr>
The root cause was the syntax error in the rewrite directive added to the /etc/nginx/sites-available/default. This was resolved by first checking the status of the nginx service. This outlined the cause of error as a syntax error in the file /etc/nginx/sites-available/default. The file was opened and a semicolon was added at the end of the rewrite directive. Afterwards, the file was saved and nginx was restarted. This restored the service.

<h3>Corrective Measures</h3><hr>
<ul>
<li>Ensure that status of a service whose configuration file has been altered is checked after restart/reload.</li>

<li>Double check that the correct syntax is being used in the configuration files before closing the file and saving it.</li>
</ul>