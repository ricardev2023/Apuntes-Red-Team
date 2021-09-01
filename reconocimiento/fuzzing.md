# Fuzzing \(traducir\)

[https://wfuzz.readthedocs.io/en/latest/user/basicusage.html](https://wfuzz.readthedocs.io/en/latest/user/basicusage.html)

## **WFUZZ**

### **Fuzzing Paths and Files**

Wfuzz can be used to look for hidden content, such as files and directories, within a web server, allowing to find further attack vectors. It is worth noting that, the success of this task depends highly on the dictionaries used.

However, due to the limited number of platforms, default installations, known resources such as logfiles, administrative directories, a considerable number of resources are located in predictable locations. Therefore, brute forcing these contents becomes a more feasible task.

Below is shown an example of wfuzz looking for common directories:

`$ wfuzz -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ`

### **Fuzzing Parameters In URLs**

You often want to fuzz some sort of data in the URL's query string, this can be achieved by specifying the FUZZ keyword in the URL after a question mark:

`$ wfuzz -z range,0-10 --hl 97 http://testphp.vulnweb.com/listproducts.php?cat=FUZZ`

### Fuzzing POST Requests

If you want to fuzz some form-encoded data like an HTML form will do, simply pass a -d command line argument:

`$ wfuzz -z file,wordlist/others/common_pass.txt -d "uname=FUZZ&pass=FUZZ" --hc 302 http://testphp.vulnweb.com/userinfo.php`

### **Fuzzing Cookies**

To send your own cookies to the server, for example, to associate a request to HTTP sessions, you can use the -b parameter \(repeat for various cookies\):

`$ wfuzz -z file,wordlist/general/common.txt -b cookie=value1 -b cookie2=value2 http://testphp.vulnweb.com/FUZZ`

The command above will generate HTTP requests such as the one below:

`GET /attach HTTP/1.1    
Host: testphp.vulnweb.com    
Accept: */*    
Content-Type: application/x-www-form-urlencoded    
Cookie: cookie=value1; cookie2=value2    
User-Agent: Wfuzz/2.2    
Connection: close`

Cookies can also be fuzzed:

`$ wfuzz -z file,wordlist/general/common.txt -b cookie=FUZZ http://testphp.vulnweb.com/`

### Fuzzing Custom headers

If you'd like to add HTTP headers to a request, simply use the -H parameter \(repeat for various headers\):

`$ wfuzz -z file,wordlist/general/common.txt -H "myheader: headervalue" -H "myheader2: headervalue2" http://testphp.vulnweb.com/FUZZ`

The command above will generate HTTP requests such as the one below:

`GET /agent HTTP/1.1    
Host: testphp.vulnweb.com    
Accept: */*    
Myheader2: headervalue2    
Myheader: headervalue    
Content-Type: application/x-www-form-urlencoded    
User-Agent: Wfuzz/2.2    
Connection: close`

You can modify existing headers, for example, for specifying a custom user agent, execute the following:

`$ wfuzz -z file,wordlist/general/common.txt -H "myheader: headervalue" -H "User-Agent: Googlebot-News" http://testphp.vulnweb.com/FUZZ`

The command above will generate HTTP requests such as the one below:

`GET /asp HTTP/1.1    
Host: testphp.vulnweb.com    
Accept: */*    
Myheader: headervalue    
Content-Type: application/x-www-form-urlencoded    
User-Agent: Googlebot-News    
Connection: close`

Headers can also be fuzzed:

`$ wfuzz -z file,wordlist/general/common.txt -H "User-Agent: FUZZ" http://testphp.vulnweb.com/`

### **Fuzzing HTTP Verbs**

HTTP verbs fuzzing can be specified using the -X switch:

`$ wfuzz -z list,GET-HEAD-POST-TRACE-OPTIONS -X FUZZ http://testphp.vulnweb.com/`

If you want to perform the requests using a specific verb you can also use “-X HEAD”.

### **Writing to a file**

Wfuzz supports writing the results to a file in a different format. This is performed by plugins called “printers”. The available printers can be listed executing:

For example, to write results to an output file in JSON format use the following command:

`$ wfuzz -f /tmp/outfile,json -w wordlist/general/common.txt http://testphp.vulnweb.com/FUZZ`

#### **Different output**

When using the default or raw output you can also select additional FuzzResult’s fields to show, using –efield, together with the payload description:

`$ wfuzz -z range --zD 0-1 -u http://testphp.vulnweb.com/artists.php?artist=FUZZ --efield r`

