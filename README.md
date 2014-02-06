<h1>tornado-generic-handlers</h1>
<p>This package contains Django's generic class based views adapted to be used with Tornado along with SQLAlchemy and WTForms. <br/></p>
<h2>Installation</h2>
```
pip install torgen
```
<h2>Usage</h2>
```python
from torgen.base import TemplateHandler

class HomeHandler(TemplateHandler):
    template_name = 'home.html'
```
