talon
=====

Mailgun library to extract message quotations and signatures.

If you ever tried to parse message quotations or signatures you know that absense of any formatting standards in this area
could make this task a nightmare. Hopefully this library will make your life much eathier. The name of the project is
inspired by TALON - multipurpose robot  designed to perform missions ranging from reconnaissance to combat and operate in
a number of hostile environments. That's what a good quotations and signature parser should be like :smile:

Usage
-----

Here's how you initialize the library and extract a reply from a text message:

```python
import talon
from talon import quotations

talon.init()

text =  """Reply

-----Original Message-----

Quote"""

reply = quotations.extract_from(text, 'text/plain')
reply = quotations.extract_from_plain(text)
# reply == "Reply"
```

To extract a reply from html:

```python
html = """Reply
<blockquote>

  <div>
    On 11-Apr-2011, at 6:54 PM, Bob &lt;bob@example.com&gt; wrote:
  </div>

  <div>
    Quote
  </div>

</blockquote>"""

reply = quotations.extract_from(html, 'text/html')
reply = quotations.extract_from_html(html)
# reply == "<html><body><p>Reply</p></body></html>"
```

Often the best way is the easiest one. Here's how you can extract signature from email message without any
machine learning fancy stuff:

```python
from talon.signature.bruteforce import extract_signature


message = """Wow. Awesome!
--
Bob Smith"""

text, signature = extract_signature(message)
# text == "Wow. Awesome!"
# signature == "--\nBob Smith"
```

Quick and works like a charm 90% of the time. For other 10% you can use the power of machine learning algorithms:

```python
from talon import signature


message = """Thanks Sasha, I can't go any higher and is why I limited it to the
homepage.

John Doe
via mobile"""

text, signature = signature.extract(message, sender='john.doe@example.com')
# text == "Thanks Sasha, I can't go any higher and is why I limited it to the\nhomepage."
# signature == "John Doe\nvia mobile"
```

For machine learning talon currently uses [PyML](http://pyml.sourceforge.net/) library to build SVM classifiers. The core of machine learning algorithm lays in ``talon.signature.learning package``. It defines a set of features to apply to a message (``featurespace.py``), how data sets are built (``dataset.py``), classifier's interface (``classifier.py``).

The data used for training is taken from our personal email conversations and from [ENRON](https://www.cs.cmu.edu/~enron/) dataset. As a result of applying our set of features to the dataset we provide files ``classifier`` and ``train.data`` that don't have any personal information but could be used to load trained classifier. Those files should be regenerated every time the feature/data set is changed.

Research
--------

The library is inspired by the following research papers and projects:

* http://www.cs.cmu.edu/~vitor/papers/sigFilePaper_finalversion.pdf
* http://www.cs.cornell.edu/people/tj/publications/joachims_01a.pdf
