<h1>tornado-generic-handlers</h1>
<p>This package contains Django's generic class based views adapted to be used with Tornado along with SQLAlchemy and WTForms. 
Note that implementation might differ a bit in some of the cases.<br/> The features included:
<ul>
  <li>generic handlers</li>
  <li>pagination</li>
  <li>project-wide context variables</li>
</ul>
</p>
<h2>Installation</h2>
```
pip install torgen
```
<h2>Configuration</h2>
```python
```
<h2>Usage</h2>
```python
from torgen.base import TemplateHandler
from torgen.list import ListHandler
from torgen.detail import DetailHandler

class HomeHandler(TemplateHandler):
    template_name = 'home.html'
    
class BlogHandler(ListHandler):
    template_name = 'blog.html'
    paginate_by = 10
    context_object_name = 'post_list'
    model = Post
    
class PostHandler(DetailHandler):
    template_name = 'post.html'
    model = Post
    context_object_name = 'post'
    
class LoginHandler(FormHandler):
    template_name = 'login.html'
    form_class = LoginForm
    
    def form_valid(self, form):
        self.set_secure_cookie('user', form.data['username'])
        return super(LoginHandler, self).form_valid(form)
    
    def get_success_url(self):
        return self.reverse_url('home')
```
